# Flutter Web 开发

## 概述

Flutter Web 允许将 Flutter 应用编译为 Web 应用，支持 Chrome、Firefox、Safari、Edge 等现代浏览器。

## 快速开始

### 创建和运行

```bash
# 创建支持 Web 的项目
flutter create --platforms=web my_web_app

# 运行 Web 应用
flutter run -d chrome

# 指定端口运行
flutter run -d web-server --web-port=8080

# 构建生产版本
flutter build web
```

### 渲染模式

```dart
// Flutter Web 支持两种渲染模式

// 1. HTML (CanvasKit 关闭)
// 使用 HTML 元素 + CSS 渲染
// 优点：首次加载快，SEO 友好
// 缺点：复杂 UI 性能较差

// 2. CanvasKit (默认)
// 使用 WebGL + WebAssembly 渲染
// 优点：与移动端渲染一致，性能好
// 缺点：首次加载较大

// 在 web/index.html 中配置
<script>
  window.flutterConfiguration = {
    canvasKitMaximumSurfaces: 3,
  };
</script>
```

## Web 特定功能

### URL 策略

```dart
import 'package:flutter_web_plugins/url_strategy.dart';

void main() {
  // 使用路径 URL (默认)
  usePathUrlStrategy();
  // URL 格式: https://example.com/page/detail

  // 使用 hash URL
  useHashUrlStrategy();
  // URL 格式: https://example.com/#/page/detail

  runApp(const MyApp());
}
```

### 浏览器检测

```dart
import 'package:flutter/foundation.dart';

class WebUtils {
  static bool get isWeb => kIsWeb;

  static String get userAgent {
    if (!kIsWeb) return '';
    return html.window.navigator.userAgent;
  }

  static bool get isMobileWeb {
    if (!kIsWeb) return false;
    final ua = userAgent.toLowerCase();
    return ua.contains('mobile') || ua.contains('android') || ua.contains('iphone');
  }

  static bool get isChrome {
    if (!kIsWeb) return false;
    return userAgent.contains('Chrome');
  }

  static bool get isSafari {
    if (!kIsWeb) return false;
    return userAgent.contains('Safari') && !userAgent.contains('Chrome');
  }
}
```

### 窗口控制

```dart
import 'dart:html' as html;

class WindowService {
  // 获取窗口尺寸
  static Size getWindowSize() {
    return Size(
      html.window.innerWidth?.toDouble() ?? 0,
      html.window.innerHeight?.toDouble() ?? 0,
    );
  }

  // 打开新窗口
  static void openNewTab(String url) {
    html.window.open(url, '_blank');
  }

  // 设置页面标题
  static void setTitle(String title) {
    html.document.title = title;
  }

  // 复制到剪贴板
  static Future<void> copyToClipboard(String text) async {
    await html.window.navigator.clipboard?.writeText(text);
  }

  // 刷新页面
  static void reload() {
    html.window.location.reload();
  }
}
```

### 本地存储

```dart
import 'dart:html' as html;
import 'dart:convert';

class WebStorage {
  // localStorage (持久化)
  static void setItem(String key, dynamic value) {
    html.window.localStorage[key] = jsonEncode(value);
  }

  static T? getItem<T>(String key, T Function(Map<String, dynamic>) fromJson) {
    final value = html.window.localStorage[key];
    if (value == null) return null;
    try {
      return fromJson(jsonDecode(value));
    } catch (_) {
      return null;
    }
  }

  static void removeItem(String key) {
    html.window.localStorage.remove(key);
  }

  static void clear() {
    html.window.localStorage.clear();
  }

  // sessionStorage (会话级)
  static void setSessionItem(String key, dynamic value) {
    html.window.sessionStorage[key] = jsonEncode(value);
  }

  static String? getSessionItem(String key) {
    return html.window.sessionStorage[key];
  }
}
```

### Cookie 操作

```dart
import 'dart:html' as html;

class CookieService {
  static void setCookie(String key, String value, {int? days}) {
    String cookie = '$key=$value; path=/';
    if (days != null) {
      final date = DateTime.now().add(Duration(days: days));
      cookie += '; expires=${date.toUtc().toIso8601String()}';
    }
    html.document.cookie = cookie;
  }

  static String? getCookie(String key) {
    final cookies = html.document.cookie?.split(';') ?? [];
    for (var cookie in cookies) {
      final parts = cookie.trim().split('=');
      if (parts[0] == key) {
        return parts.length > 1 ? parts[1] : null;
      }
    }
    return null;
  }

  static void deleteCookie(String key) {
    html.document.cookie = '$key=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/';
  }
}
```

### 键盘快捷键

```dart
import 'package:flutter/services.dart';

class KeyboardHandler {
  static void setupShortcuts() {
    // 禁用默认浏览器快捷键
    html.document.onKeyDown.listen((event) {
      // 禁用 Ctrl+S
      if (event.ctrlKey && event.key == 's') {
        event.preventDefault();
      }
      // 禁用 F5 刷新（如果需要）
      if (event.key == 'F5') {
        event.preventDefault();
      }
    });
  }
}

// 在 Widget 中使用
class ShortcutWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Shortcuts(
      shortcuts: {
        LogicalKeySet(LogicalKeyboardKey.control, LogicalKeyboardKey.keyS): SaveIntent(),
        LogicalKeySet(LogicalKeyboardKey.control, LogicalKeyboardKey.keyN): NewIntent(),
      },
      child: Actions(
        actions: {
          SaveIntent: CallbackAction(onInvoke: (_) => saveDocument()),
          NewIntent: CallbackAction(onInvoke: (_) => createNew()),
        },
        child: Focus(
          autofocus: true,
          child: Container(child: Text('按 Ctrl+S 保存')),
        ),
      ),
    );
  }
}
```

### 文件下载

```dart
import 'dart:html' as html;
import 'dart:typed_data';

class FileDownloadService {
  // 下载文本文件
  static void downloadText(String content, String filename) {
    final blob = html.Blob([content], 'text/plain');
    final url = html.Url.createObjectUrlFromBlob(blob);
    final anchor = html.AnchorElement(href: url)
      ..setAttribute('download', filename)
      ..click();
    html.Url.revokeObjectUrl(url);
  }

  // 下载二进制文件
  static void downloadBytes(Uint8List bytes, String filename, {String mimeType = 'application/octet-stream'}) {
    final blob = html.Blob([bytes], mimeType);
    final url = html.Url.createObjectUrlFromBlob(blob);
    final anchor = html.AnchorElement(href: url)
      ..setAttribute('download', filename)
      ..click();
    html.Url.revokeObjectUrl(url);
  }

  // 下载网络文件
  static void downloadFromUrl(String url, String filename) {
    final anchor = html.AnchorElement(href: url)
      ..setAttribute('download', filename)
      ..click();
  }
}
```

### 文件上传

```dart
import 'dart:html' as html;

class FileUploadService {
  static Future<List<html.File>> pickFiles({
    String accept = '*/*',
    bool multiple = false,
  }) async {
    final input = html.FileUploadInputElement()
      ..accept = accept
      ..multiple = multiple;

    input.click();

    await input.onChange.first;
    return input.files?.toList() ?? [];
  }

  static Future<Uint8List> readFileBytes(html.File file) async {
    final reader = html.FileReader();
    reader.readAsArrayBuffer(file);
    await reader.onLoad.first;
    return reader.result as Uint8List;
  }

  static Future<String> readFileText(html.File file) async {
    final reader = html.FileReader();
    reader.readAsText(file);
    await reader.onLoad.first;
    return reader.result as String;
  }
}

// 使用示例
Future<void> uploadFile() async {
  final files = await FileUploadService.pickFiles(
    accept: '.pdf,.doc,.docx',
    multiple: true,
  );

  for (final file in files) {
    final bytes = await FileUploadService.readFileBytes(file);
    print('文件: ${file.name}, 大小: ${bytes.length} bytes');
  }
}
```

## 性能优化

### 按需加载

```yaml
# pubspec.yaml
flutter:
  uses-material-design: true
  # 延迟加载资源
```

```dart
// 使用 deferred import
import 'package:heavy_package/page.dart' deferred as heavy;

class DeferredLoader {
  static Future<void> loadHeavyPage() async {
    await heavy.loadLibrary();
    // 然后使用
    // Navigator.push(context, MaterialPageRoute(builder: (_) => heavy.HeavyPage()));
  }
}
```

### 图片优化

```dart
// Web 图片优化
Image.network(
  'https://example.com/image.jpg',
  // 使用 cacheWidth 限制解码尺寸
  cacheWidth: 800,
  // 使用 filterQuality
  filterQuality: FilterQuality.medium,
  // 加载占位
  loadingBuilder: (context, child, loadingProgress) {
    if (loadingProgress == null) return child;
    return CircularProgressIndicator();
  },
);
```

### CanvasKit 优化

```html
<!-- web/index.html -->
<script>
  window.flutterConfiguration = {
    // 降低 CanvasKit 复杂度
    canvasKitMaximumSurfaces: 3,
    // 延迟加载 CanvasKit
    canvasKitVariant: 'auto',
  };
</script>
```

## SEO 优化

### Meta 标签

```html
<!-- web/index.html -->
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="Flutter Web 应用描述">
  <meta name="keywords" content="Flutter, Web, App">
  <meta name="author" content="Your Name">

  <!-- Open Graph -->
  <meta property="og:title" content="应用名称">
  <meta property="og:description" content="应用描述">
  <meta property="og:image" content="https://example.com/og-image.png">
  <meta property="og:url" content="https://example.com">

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="应用名称">
  <meta name="twitter:description" content="应用描述">
</head>
```

### 动态 Meta 标签

```dart
import 'dart:html' as html;

class MetaService {
  static void updateMetaTag(String name, String content) {
    final meta = html.document.querySelector('meta[name="$name"]');
    if (meta != null) {
      meta.setAttribute('content', content);
    }
  }

  static void updateTitle(String title) {
    html.document.title = title;
    // 更新 og:title
    final ogTitle = html.document.querySelector('meta[property="og:title"]');
    ogTitle?.setAttribute('content', title);
  }

  static void updateDescription(String description) {
    updateMetaTag('description', description);
    final ogDesc = html.document.querySelector('meta[property="og:description"]');
    ogDesc?.setAttribute('content', description);
  }
}

// 在路由变化时更新
class PageTracker extends StatelessWidget {
  final String title;
  final String description;
  final Widget child;

  const PageTracker({
    required this.title,
    required this.description,
    required this.child,
  });

  @override
  Widget build(BuildContext context) {
    // 页面加载时更新 meta
    WidgetsBinding.instance.addPostFrameCallback((_) {
      MetaService.updateTitle(title);
      MetaService.updateDescription(description);
    });

    return child;
  }
}
```

## 限制与注意事项

### 不支持的功能

```dart
// ❌ dart:io 在 Web 上不可用
import 'dart:io'; // 编译错误

// ❌ 部分插件不支持 Web
// 使用前检查平台支持

// ✅ 使用条件导入
import 'file_service_stub.dart'
    if (dart.library.io) 'file_service_io.dart'
    if (dart.library.html) 'file_service_web.dart';
```

### 响应式设计

```dart
// Web 需要特别处理响应式布局
class WebResponsive extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        // Web 上使用更大的断点
        if (constraints.maxWidth > 1200) {
          return DesktopLayout();
        } else if (constraints.maxWidth > 800) {
          return TabletLayout();
        }
        return MobileLayout();
      },
    );
  }
}
```

### 鼠标交互

```dart
// Web 支持鼠标悬停效果
Widget build(BuildContext context) {
  return MouseRegion(
    cursor: SystemMouseCursors.click, // 鼠标样式
    onEnter: (_) => setState(() => isHovered = true),
    onExit: (_) => setState(() => isHovered = false),
    child: AnimatedContainer(
      duration: Duration(milliseconds: 200),
      color: isHovered ? Colors.blue.withOpacity(0.1) : Colors.transparent,
      child: Text('悬停查看效果'),
    ),
  );
}
```

## 部署

### 构建

```bash
# 构建 Web 应用
flutter build web --release

# 指定渲染模式
flutter build web --web-renderer html
flutter build web --web-renderer canvaskit
flutter build web --web-renderer auto
```

### 部署到静态服务器

```bash
# 构建后文件位于 build/web/
# 可部署到任何静态服务器

# Nginx 配置示例
server {
  listen 80;
  server_name example.com;
  root /var/www/flutter_app;

  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

### 部署到 Firebase

```bash
# 安装 Firebase CLI
npm install -g firebase-tools

# 登录
firebase login

# 初始化
firebase init hosting

# 部署
firebase deploy
```

---

*最后更新: 2026-02-19*

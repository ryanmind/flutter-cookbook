# Flutter 调试技巧

## 概述

掌握调试技巧是高效开发的关键，本文介绍 Flutter 开发中常用的调试方法和工具。

## 开发者工具

### DevTools

Flutter DevTools 是官方提供的强大调试工具套件。

```bash
# 启动 DevTools
flutter pub global activate devtools
flutter pub global run devtools

# 或在运行时按 v 启动
flutter run
# 按 v 打开 DevTools
```

#### DevTools 功能

- **Widget Inspector** - 检查 Widget 树
- **Performance** - 性能分析
- **CPU Profiler** - CPU 使用分析
- **Memory** - 内存分析
- **Network** - 网络请求监控
- **Logging** - 日志查看
- **Debugger** - 断点调试

### Widget Inspector

```dart
// 在代码中标记 Widget 以便快速定位
Widget build(BuildContext context) {
  return Container(
    // Widget Inspector 中显示
    key: const ValueKey('user_card'),
    child: Text('用户卡片'),
  );
}
```

在 DevTools 中：
1. 点击 "Select Widget" 按钮
2. 在设备上点击目标 Widget
3. 查看其属性、父组件、渲染信息

## 日志调试

### print vs debugPrint

```dart
// print - 基本输出
print('普通日志');

// debugPrint - 避免日志丢失，适合大量输出
debugPrint('安全日志输出');

// debugPrintStack - 打印当前堆栈
debugPrintStack();

// 选择性输出
debugPrint('仅在 debug 模式输出'); // 自动在 release 模式移除
```

### 日志级别

```dart
import 'dart:developer' as developer;

void logWithLevel(String message, {String level = 'INFO'}) {
  developer.log(
    message,
    time: DateTime.now(),
    level: switch (level) {
      'DEBUG' => 500,
      'INFO' => 800,
      'WARNING' => 900,
      'ERROR' => 1000,
      _ => 800,
    },
    name: 'MyApp',
  );
}

// 使用
logWithLevel('开始加载数据', level: 'DEBUG');
logWithLevel('加载失败', level: 'ERROR');
```

### 条件日志

```dart
// 仅在 debug 模式执行
void debugLog(String message) {
  assert(() {
    print(message);
    return true;
  }());
}

// 使用
debugLog('这条日志只在 debug 模式显示');
```

## 断点调试

### 基本断点

在 IDE 中点击行号左侧添加断点，程序会在断点处暂停。

```dart
void calculateTotal(List<int> items) {
  int total = 0;
  for (var item in items) {  // ← 在这里设置断点
    total += item;
  }
  print('Total: $total');
}
```

### 条件断点

右键断点设置条件，仅在条件满足时触发。

```dart
for (int i = 0; i < 100; i++) {
  process(items[i]); // 条件断点: i == 50
}
```

### 表达式求值

在断点暂停时，可以在 Debug Console 中执行任意 Dart 表达式：

```dart
// 在断点处可以执行
items.length
items.first.name
calculateSomething(items)
```

## 断言

### assert 语法

```dart
void setAge(int age) {
  // assert 只在 debug 模式生效
  assert(age >= 0, '年龄不能为负数');
  assert(age <= 150, '年龄不能超过150');

  _age = age;
}
```

### 复杂断言

```dart
void processData(List<int> data) {
  assert(() {
    // 复杂验证逻辑
    if (data.isEmpty) {
      print('警告: 数据为空');
      return false;
    }
    return true;
  }());

  // 处理数据
}
```

## 性能调试

### Performance Overlay

```dart
// 在 MaterialApp 中启用
MaterialApp(
  showPerformanceOverlay: true, // 显示性能面板
  home: MyHomePage(),
);

// 或通过代码切换
void togglePerformanceOverlay() {
  debugProfileBuildsEnabled = !debugProfileBuildsEnabled;
}
```

### 检查帧率

```dart
import 'dart:ui';

void checkFrameRate() {
  final startTime = DateTime.now();
  int frameCount = 0;

  PlatformDispatcher.instance.onBeginFrame = (time) {
    frameCount++;
    final elapsed = DateTime.now().difference(startTime);
    if (elapsed.inSeconds >= 1) {
      print('FPS: $frameCount');
      frameCount = 0;
    }
  };
}
```

### 慢速动画

```dart
// 减慢动画速度，便于观察
timeDilation = 5.0; // 动画速度变为 1/5

// 恢复正常速度
timeDilation = 1.0;
```

## Widget 调试

### debugPrint 开关

```dart
// 禁用特定调试输出
debugPrintGestureArenaDiagnostics = false; // 手势竞技场
debugPrintBeginFrameBanner = false; // 帧开始标记
debugPrintEndFrameBanner = false; // 帧结束标记
debugPrintScheduleBuildForStacks = false; // 构建调度堆栈
```

### Widget 树打印

```dart
// 打印 Widget 树
debugPrint('Widget 树: ${context.widget}');

// 打印 RenderObject 树
debugPrint('Render 树: ${context.findRenderObject()}');
```

### 可视化调试

```dart
// 显示 Widget 边界
debugPaintSizeEnabled = true;

// 显示基线
debugPaintBaselinesEnabled = true;

// 显示点击区域
debugPaintPointersEnabled = true;

// 关闭
debugPaintSizeEnabled = false;
```

## 内存调试

### 内存快照

在 DevTools Memory 面板中：
1. 点击 "GC" 清理垃圾
2. 点击 "Snapshot" 捕获内存快照
3. 分析对象数量和大小

### 内存泄漏检测

```dart
// 检查对象是否被正确释放
class MyWidget extends StatefulWidget {
  @override
  State<MyWidget> createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> {
  final String _id = UniqueKey().toString();

  @override
  void initState() {
    super.initState();
    print('$_id: State 创建');
  }

  @override
  void dispose() {
    print('$_id: State 销毁'); // 确保被调用
    super.dispose();
  }
}
```

## 网络调试

### Dio 拦截器

```dart
import 'package:dio/dio.dart';

final dio = Dio();

dio.interceptors.add(
  LogInterceptor(
    request: true,
    requestHeader: true,
    requestBody: true,
    responseHeader: true,
    responseBody: true,
    error: true,
  ),
);
```

### Charles / Proxyman 代理

```dart
// 配置代理（仅 debug）
void setupProxy() {
  if (kDebugMode) {
    (dio.httpClientAdapter as DefaultHttpClientAdapter).onHttpClientCreate =
        (client) {
      client.findProxy = (uri) => 'PROXY localhost:8888';
      client.badCertificateCallback = (cert, host, port) => true;
    };
  }
}
```

## 常见调试场景

### 1. 布局溢出

```dart
// Flutter 会显示溢出警告（黄黑条纹）
// 点击溢出区域，Widget Inspector 定位问题

// 常见解决方案
Column(
  children: [
    // 使用 Expanded 填充
    Expanded(child: ListView(...)),
    // 或使用 Flexible
    Flexible(child: Container(...)),
  ],
);
```

### 2. 空指针异常

```dart
// 使用 null safety 避免
String? name;
print(name ?? '未知'); // 提供默认值

// 或使用 if-null
if (name != null) {
  print(name.length);
}
```

### 3. 状态不更新

```dart
// 检查是否调用了 setState
void updateData() {
  _data = newData;
  setState(() {}); // 必须调用
}

// 或使用 ValueNotifier
final counter = ValueNotifier<int>(0);
counter.value++; // 自动通知监听者
```

### 4. 异步调试

```dart
// 使用 FutureBuilder
FutureBuilder<User>(
  future: fetchUser(),
  builder: (context, snapshot) {
    if (snapshot.hasError) {
      return Text('错误: ${snapshot.error}'); // 显示错误信息
    }
    // ...
  },
);
```

## 命令行调试

```bash
# 运行应用
flutter run

# 常用命令
r - 热重载
R - 热重启
h - 帮助
d - Detach
c - 清屏
q - 退出
v - 打开 DevTools
w - 打印 Widget 树
t - 打印渲染树
p - 切换性能覆盖层
o - 切换平台 (Android/iOS)
P - 切换性能覆盖层
```

## 最佳实践

### 1. 日志分级

```dart
enum LogLevel { debug, info, warning, error }

class Logger {
  static LogLevel level = kReleaseMode ? LogLevel.warning : LogLevel.debug;

  static void debug(String message) {
    if (level.index <= LogLevel.debug.index) {
      print('[DEBUG] $message');
    }
  }

  static void error(String message, [Object? error]) {
    print('[ERROR] $message ${error ?? ''}');
  }
}
```

### 2. 开发环境隔离

```dart
void main() {
  if (kDebugMode) {
    // 开发环境配置
    Logger.level = LogLevel.debug;
  } else if (kProfileMode) {
    // Profile 模式
    Logger.level = LogLevel.info;
  } else {
    // 生产环境
    Logger.level = LogLevel.warning;
  }

  runApp(const MyApp());
}
```

### 3. 使用错误边界

```dart
// 捕获 Widget 树错误
ErrorWidget.builder = (FlutterErrorDetails details) {
  if (kReleaseMode) {
    return const CustomErrorScreen();
  }
  return ErrorWidget(details.exception);
};
```

---

*最后更新: 2026-02-19*

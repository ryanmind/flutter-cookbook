# Flutter MediaQuery 组件用法详解

## 简介

MediaQuery 是 Flutter 中用于获取设备屏幕信息和系统设置的核心组件。它提供了屏幕尺寸、像素密度、文本缩放因子、平台亮度等重要信息，是响应式布局的基础。

## 1. 初级用法

### 1.1 基本概念

MediaQuery 的作用：
- 获取屏幕尺寸（宽度、高度）
- 获取像素密度
- 获取系统主题模式（深色/浅色）
- 获取文本缩放因子
- 获取安全区域信息
- 获取屏幕方向

### 1.2 基本语法

```dart
// 获取 MediaQueryData
final mediaQuery = MediaQuery.of(context);

// 获取屏幕宽度
final width = MediaQuery.of(context).size.width;

// 获取屏幕高度
final height = MediaQuery.of(context).size.height;
```

### 1.3 MediaQueryData 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `size` | `Size` | 屏幕尺寸 |
| `devicePixelRatio` | `double` | 像素密度 |
| `textScaler` | `TextScaler` | 文本缩放策略 |
| `platformBrightness` | `Brightness` | 系统亮度模式 |
| `padding` | `EdgeInsets` | 安全区域内边距 |
| `viewPadding` | `EdgeInsets` | 视图内边距 |
| `viewInsets` | `EdgeInsets` | 键盘等遮挡区域 |
| `orientation` | `Orientation` | 屏幕方向 |
| `alwaysUse24HourFormat` | `bool` | 是否使用24小时制 |
| `accessibleNavigation` | `bool` | 是否启用辅助导航 |
| `invertColors` | `bool` | 是否反转颜色 |
| `disableAnimations` | `bool` | 是否禁用动画 |
| `boldText` | `bool` | 是否启用粗体文本 |

### 1.4 基础示例

#### 获取屏幕尺寸

```dart
class ScreenSizeExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final size = MediaQuery.of(context).size;
    
    return Column(
      children: [
        Text('宽度: ${size.width}'),
        Text('高度: ${size.height}'),
        Text('像素密度: ${MediaQuery.of(context).devicePixelRatio}'),
      ],
    );
  }
}
```

#### 响应式布局

```dart
Widget build(BuildContext context) {
  final width = MediaQuery.of(context).size.width;
  
  if (width > 600) {
    return TabletLayout();
  } else {
    return MobileLayout();
  }
}
```

## 2. 中级用法

### 2.1 安全区域处理

```dart
class SafeAreaExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final padding = MediaQuery.of(context).padding;
    
    return Container(
      padding: EdgeInsets.only(
        top: padding.top,      // 刘海/状态栏
        bottom: padding.bottom, // 底部指示器
        left: padding.left,    // 横屏时的安全区域
        right: padding.right,
      ),
      color: Colors.blue,
      child: Text('内容在安全区域内'),
    );
  }
}

// 更简单的方式：使用 SafeArea 组件
class SafeAreaWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return SafeArea(
      child: Container(
        color: Colors.blue,
        child: Text('自动处理安全区域'),
      ),
    );
  }
}
```

### 2.2 键盘高度处理

```dart
class KeyboardExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final viewInsets = MediaQuery.of(context).viewInsets;
    final keyboardHeight = viewInsets.bottom;
    
    return Column(
      children: [
        Expanded(child: ListView(...)),
        // 键盘弹出时自动上移
        Padding(
          padding: EdgeInsets.only(bottom: keyboardHeight),
          child: TextField(...),
        ),
      ],
    );
  }
}
```

### 2.3 深色模式适配

```dart
class ThemeExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final isDark = MediaQuery.of(context).platformBrightness == Brightness.dark;
    
    return Container(
      color: isDark ? Colors.black : Colors.white,
      child: Text(
        '适配深色模式',
        style: TextStyle(
          color: isDark ? Colors.white : Colors.black,
        ),
      ),
    );
  }
}

// 推荐：使用 Theme
class ThemeWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final isDark = Theme.of(context).brightness == Brightness.dark;
    
    return Container(
      color: isDark ? Colors.grey[900] : Colors.white,
      child: Text('使用 Theme'),
    );
  }
}
```

### 2.4 文本缩放处理

```dart
class TextScaleExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final textScaler = MediaQuery.of(context).textScaler;
    final scale = textScaler.scale(1.0);
    
    return Column(
      children: [
        Text('文本会跟随系统缩放: $scale'),
        // 忽略系统缩放
        MediaQuery(
          data: MediaQuery.of(context).copyWith(
            textScaler: TextScaler.noScaling,
          ),
          child: Text('固定大小文本'),
        ),
      ],
    );
  }
}
```

### 2.5 屏幕方向判断

```dart
class OrientationExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final orientation = MediaQuery.of(context).orientation;
    
    if (orientation == Orientation.portrait) {
      return Column(
        children: [
          Container(height: 100, color: Colors.red),
          Container(height: 100, color: Colors.blue),
        ],
      );
    } else {
      return Row(
        children: [
          Container(width: 100, color: Colors.red),
          Container(width: 100, color: Colors.blue),
        ],
      );
    }
  }
}

// 使用 OrientationBuilder 更高效
class OrientationBuilderExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return OrientationBuilder(
      builder: (context, orientation) {
        return orientation == Orientation.portrait
            ? Column(children: [...])
            : Row(children: [...]);
      },
    );
  }
}
```

### 2.6 常见布局场景

#### 响应式网格

```dart
class ResponsiveGrid extends StatelessWidget {
  final List<Widget> children;
  
  const ResponsiveGrid({required this.children});
  
  @override
  Widget build(BuildContext context) {
    final width = MediaQuery.of(context).size.width;
    final crossAxisCount = width > 1200 ? 4 : width > 800 ? 3 : 2;
    
    return GridView.count(
      crossAxisCount: crossAxisCount,
      children: children,
    );
  }
}
```

#### 底部输入框

```dart
class BottomInputExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Expanded(child: ListView(...)),
          Container(
            padding: EdgeInsets.only(
              bottom: MediaQuery.of(context).viewInsets.bottom,
            ),
            child: TextField(
              decoration: InputDecoration(hintText: '输入消息'),
            ),
          ),
        ],
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 MediaQuery.removePadding

移除特定方向的 padding：

```dart
// 移除顶部安全区域
MediaQuery.removePadding(
  context: context,
  removeTop: true,
  child: ListView(
    children: [
      Container(height: 200, color: Colors.blue),
    ],
  ),
)
```

### 3.2 MediaQuery.removeViewInsets

移除 viewInsets（键盘高度）：

```dart
MediaQuery.removeViewInsets(
  context: context,
  removeBottom: true,
  child: Container(...),
)
```

### 3.3 自定义 MediaQueryData

使用 MediaQuery 覆盖子组件的 MediaQueryData：

```dart
MediaQuery(
  data: MediaQuery.of(context).copyWith(
    textScaler: TextScaler.noScaling,  // 禁用文本缩放
  ),
  child: Text('不受系统文本缩放影响'),
)

// 禁用动画
MediaQuery(
  data: MediaQuery.of(context).copyWith(
    disableAnimations: true,
  ),
  child: MyAnimatedWidget(),
)
```

### 3.4 封装响应式工具

```dart
class ScreenHelper {
  static bool isMobile(BuildContext context) {
    return MediaQuery.of(context).size.width < 600;
  }
  
  static bool isTablet(BuildContext context) {
    final width = MediaQuery.of(context).size.width;
    return width >= 600 && width < 1200;
  }
  
  static bool isDesktop(BuildContext context) {
    return MediaQuery.of(context).size.width >= 1200;
  }
  
  static double width(BuildContext context) {
    return MediaQuery.of(context).size.width;
  }
  
  static double height(BuildContext context) {
    return MediaQuery.of(context).size.height;
  }
  
  static double wp(BuildContext context, double percent) {
    return width(context) * percent / 100;
  }
  
  static double hp(BuildContext context, double percent) {
    return height(context) * percent / 100;
  }
}

// 使用
if (ScreenHelper.isMobile(context)) {
  return MobileLayout();
} else if (ScreenHelper.isTablet(context)) {
  return TabletLayout();
} else {
  return DesktopLayout();
}
```

### 3.5 响应式字体大小

```dart
class ResponsiveText extends StatelessWidget {
  final String text;
  final double fontSize;
  
  const ResponsiveText({
    required this.text,
    this.fontSize = 16,
  });
  
  @override
  Widget build(BuildContext context) {
    final width = MediaQuery.of(context).size.width;
    final scaleFactor = width / 375; // 以 375 为基准
    
    return Text(
      text,
      style: TextStyle(
        fontSize: fontSize * scaleFactor,
      ),
    );
  }
}
```

### 3.6 监听变化

```dart
class MediaQueryListener extends StatefulWidget {
  @override
  State<MediaQueryListener> createState() => _MediaQueryListenerState();
}

class _MediaQueryListenerState extends State<MediaQueryListener> {
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    // MediaQuery 变化时会触发
    final size = MediaQuery.of(context).size;
    print('屏幕尺寸变化: $size');
  }
  
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}
```

## 4. 常见问题与解决方案

### 问题1：MediaQuery.of 报错

**原因**：context 上没有 MediaQuery 祖先组件。

```dart
// 问题：在 MaterialApp 外部使用
void main() {
  final size = MediaQuery.of(context).size;  // 报错
  runApp(MyApp());
}

// 解决：在 MaterialApp 内部使用
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Builder(
        builder: (context) {
          final size = MediaQuery.of(context).size;  // 正确
          return Container();
        },
      ),
    );
  }
}
```

### 问题2：键盘遮挡输入框

```dart
// 问题：键盘遮挡
Scaffold(
  body: Column(
    children: [
      Expanded(child: ListView()),
      TextField(),  // 被键盘遮挡
    ],
  ),
)

// 解决：使用 viewInsets
Scaffold(
  body: Column(
    children: [
      Expanded(child: ListView()),
      Container(
        padding: EdgeInsets.only(
          bottom: MediaQuery.of(context).viewInsets.bottom,
        ),
        child: TextField(),
      ),
    ],
  ),
)

// 更好：使用 resizeToAvoidBottomInset
Scaffold(
  resizeToAvoidBottomInset: true,  // 默认为 true
  body: Column(
    children: [
      Expanded(child: ListView()),
      TextField(),
    ],
  ),
)
```

### 问题3：频繁重建

```dart
// 问题：MediaQuery.of 导致频繁重建
Widget build(BuildContext context) {
  final size = MediaQuery.of(context).size;  // 每次变化都重建
  return Container(width: size.width);
}

// 解决：使用 LayoutBuilder 或特定属性
Widget build(BuildContext context) {
  return LayoutBuilder(
    builder: (context, constraints) {
      return Container(width: constraints.maxWidth);
    },
  );
}
```

## 5. MediaQuery vs 其他组件对比

| 组件/方法 | 用途 | 特点 |
|------|------|------|
| **MediaQuery** | 获取媒体信息 | 最全面，全局数据 |
| **LayoutBuilder** | 获取父容器约束 | 更精确，局部布局 |
| **OrientationBuilder** | 获取方向 | 专门用于方向 |
| **SafeArea** | 安全区域处理 | 简单封装 |

### 选择建议

```dart
// 需要屏幕尺寸 → MediaQuery
final size = MediaQuery.of(context).size;

// 需要父容器约束 → LayoutBuilder
LayoutBuilder(
  builder: (context, constraints) {
    return Container(width: constraints.maxWidth);
  },
)

// 只需要方向 → OrientationBuilder
OrientationBuilder(
  builder: (context, orientation) {
    return orientation == Orientation.portrait
        ? Column(...)
        : Row(...);
  },
)

// 需要安全区域 → SafeArea
SafeArea(child: ...)
```

## 6. 最佳实践

### 6.1 断点定义

```dart
abstract class Breakpoints {
  static const double mobile = 600;
  static const double tablet = 900;
  static const double desktop = 1200;
}

// 使用
final width = MediaQuery.of(context).size.width;
if (width < Breakpoints.mobile) {
  return MobileLayout();
}
```

### 6.2 避免频繁调用

```dart
// 不推荐：多次调用
Widget build(BuildContext context) {
  return Column(
    children: [
      Text('${MediaQuery.of(context).size.width}'),
      Text('${MediaQuery.of(context).size.height}'),
      Text('${MediaQuery.of(context).padding.top}'),
    ],
  );
}

// 推荐：一次获取
Widget build(BuildContext context) {
  final mediaQuery = MediaQuery.of(context);
  final size = mediaQuery.size;
  final padding = mediaQuery.padding;
  
  return Column(
    children: [
      Text('${size.width}'),
      Text('${size.height}'),
      Text('${padding.top}'),
    ],
  );
}
```

### 6.3 响应式组件封装

```dart
class ResponsiveBuilder extends StatelessWidget {
  final Widget mobile;
  final Widget? tablet;
  final Widget? desktop;
  
  const ResponsiveBuilder({
    required this.mobile,
    this.tablet,
    this.desktop,
  });
  
  @override
  Widget build(BuildContext context) {
    final width = MediaQuery.of(context).size.width;
    
    if (width >= 1200 && desktop != null) {
      return desktop!;
    } else if (width >= 600 && tablet != null) {
      return tablet!;
    } else {
      return mobile;
    }
  }
}

// 使用
ResponsiveBuilder(
  mobile: MobileLayout(),
  tablet: TabletLayout(),
  desktop: DesktopLayout(),
)
```

## 总结

MediaQuery 是 Flutter 响应式布局的核心：

**核心要点**：
1. 提供屏幕尺寸、安全区域、键盘高度等信息
2. 支持深色模式、文本缩放等系统设置
3. 使用 `MediaQuery.of(context)` 获取数据

**最佳实践**：
1. 定义统一的断点常量
2. 避免频繁调用，一次获取
3. 简单场景使用封装组件

**常见场景**：
- 响应式布局
- 键盘遮挡处理
- 安全区域适配
- 深色模式适配

---

*最后更新: 2026-02-19*

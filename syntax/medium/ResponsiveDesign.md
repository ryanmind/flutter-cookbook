# Flutter 响应式设计与多端适配

## 概述

Flutter 支持移动端、Web、桌面端多平台，响应式设计确保应用在不同屏幕尺寸上提供良好体验。

## 屏幕尺寸分类

### Material Design 断点

```dart
// Material Design 3 断点标准
enum ScreenType {
  compact,   // < 600dp (手机)
  medium,    // 600-839dp (平板竖屏)
  expanded,  // >= 840dp (平板横屏、桌面)
}

ScreenType getScreenType(double width) {
  if (width < 600) return ScreenType.compact;
  if (width < 840) return ScreenType.medium;
  return ScreenType.expanded;
}
```

### 自定义断点

```dart
class Breakpoints {
  static const double mobile = 600;
  static const double tablet = 900;
  static const double desktop = 1200;
  static const double wide = 1600;
}

enum DeviceType { mobile, tablet, desktop, wide }

DeviceType getDeviceType(double width) {
  if (width < Breakpoints.mobile) return DeviceType.mobile;
  if (width < Breakpoints.tablet) return DeviceType.tablet;
  if (width < Breakpoints.desktop) return DeviceType.desktop;
  return DeviceType.wide;
}
```

## MediaQuery

### 获取屏幕信息

```dart
Widget build(BuildContext context) {
  final mediaQuery = MediaQuery.of(context);

  // 屏幕尺寸
  final size = mediaQuery.size;
  final width = size.width;
  final height = size.height;

  // 设备像素比
  final dpr = mediaQuery.devicePixelRatio;

  // 状态栏高度
  final statusBarHeight = mediaQuery.padding.top;

  // 底部安全区域
  final bottomPadding = mediaQuery.padding.bottom;

  // 屏幕方向
  final orientation = mediaQuery.orientation;

  // 文本缩放
  final textScaler = mediaQuery.textScaler;

  return Container();
}
```

### 响应式 Widget

```dart
class ResponsiveBuilder extends StatelessWidget {
  final Widget Function(BuildContext context, BoxConstraints constraints) builder;

  const ResponsiveBuilder({super.key, required this.builder});

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        return builder(context, constraints);
      },
    );
  }
}
```

## LayoutBuilder

### 根据约束调整布局

```dart
class ResponsiveGrid extends StatelessWidget {
  final List<Widget> children;

  const ResponsiveGrid({super.key, required this.children});

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        // 根据宽度决定列数
        int crossAxisCount = 2;
        if (constraints.maxWidth > 600) crossAxisCount = 3;
        if (constraints.maxWidth > 900) crossAxisCount = 4;
        if (constraints.maxWidth > 1200) crossAxisCount = 5;

        return GridView.count(
          crossAxisCount: crossAxisCount,
          children: children,
        );
      },
    );
  }
}
```

### 响应式卡片布局

```dart
class ResponsiveCardList extends StatelessWidget {
  final List<CardItem> items;

  const ResponsiveCardList({super.key, required this.items});

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final width = constraints.maxWidth;

        // 手机：单列
        // 平板：双列
        // 桌面：三列
        if (width < 600) {
          return ListView.builder(
            itemCount: items.length,
            itemBuilder: (context, index) => CardWidget(item: items[index]),
          );
        }

        final columns = width < 900 ? 2 : 3;
        return GridView.builder(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: columns,
            crossAxisSpacing: 16,
            mainAxisSpacing: 16,
            childAspectRatio: 1.5,
          ),
          itemCount: items.length,
          itemBuilder: (context, index) => CardWidget(item: items[index]),
        );
      },
    );
  }
}
```

## 自定义响应式框架

### 响应式布局类

```dart
class Responsive {
  final double width;
  final double height;

  Responsive(this.width, this.height);

  factory Responsive.of(BuildContext context) {
    final size = MediaQuery.of(context).size;
    return Responsive(size.width, size.height);
  }

  bool get isMobile => width < 600;
  bool get isTablet => width >= 600 && width < 1200;
  bool get isDesktop => width >= 1200;

  T value<T>({required T mobile, T? tablet, T? desktop}) {
    if (isDesktop) return desktop ?? tablet ?? mobile;
    if (isTablet) return tablet ?? mobile;
    return mobile;
  }
}
```

### 响应式 Widget

```dart
class ResponsiveLayout extends StatelessWidget {
  final Widget mobile;
  final Widget? tablet;
  final Widget? desktop;

  const ResponsiveLayout({
    super.key,
    required this.mobile,
    this.tablet,
    this.desktop,
  });

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final responsive = Responsive(constraints.maxWidth, constraints.maxHeight);

        if (responsive.isDesktop && desktop != null) {
          return desktop!;
        }
        if (responsive.isTablet && tablet != null) {
          return tablet!;
        }
        return mobile;
      },
    );
  }
}
```

### 使用示例

```dart
class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return ResponsiveLayout(
      mobile: MobileHomeLayout(),
      tablet: TabletHomeLayout(),
      desktop: DesktopHomeLayout(),
    );
  }
}

class MobileHomeLayout extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('首页')),
      body: ListView(...),
      bottomNavigationBar: BottomNavigationBar(...),
    );
  }
}

class DesktopHomeLayout extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Row(
        children: [
          NavigationRail(...),  // 侧边导航
          Expanded(child: ListView(...)),
        ],
      ),
    );
  }
}
```

## 平台感知

### 检测平台

```dart
import 'dart:io' show Platform;
import 'package:flutter/foundation.dart' show kIsWeb;

class PlatformInfo {
  static bool get isWeb => kIsWeb;
  static bool get isAndroid => !kIsWeb && Platform.isAndroid;
  static bool get isIOS => !kIsWeb && Platform.isIOS;
  static bool get isMacOS => !kIsWeb && Platform.isMacOS;
  static bool get isWindows => !kIsWeb && Platform.isWindows;
  static bool get isLinux => !kIsWeb && Platform.isLinux;
  static bool get isDesktop => isMacOS || isWindows || isLinux;
  static bool get isMobile => isAndroid || isIOS;
}
```

### 平台特定样式

```dart
Widget buildAdaptiveButton({
  required String label,
  required VoidCallback onPressed,
}) {
  if (PlatformInfo.isIOS || PlatformInfo.isMacOS) {
    // Cupertino 风格
    return CupertinoButton(
      onPressed: onPressed,
      child: Text(label),
    );
  }
  // Material 风格
  return ElevatedButton(
    onPressed: onPressed,
    child: Text(label),
  );
}
```

## 方向适配

### 监听方向变化

```dart
class OrientationAwareWidget extends StatelessWidget {
  const OrientationAwareWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final orientation = MediaQuery.of(context).orientation;

    return orientation == Orientation.portrait
        ? _buildPortraitLayout()
        : _buildLandscapeLayout();
  }

  Widget _buildPortraitLayout() {
    return Column(
      children: [
        Expanded(child: ContentArea()),
        SizedBox(
          height: 200,
          child: BottomPanel(),
        ),
      ],
    );
  }

  Widget _buildLandscapeLayout() {
    return Row(
      children: [
        Expanded(child: ContentArea()),
        SizedBox(
          width: 300,
          child: SidePanel(),
        ),
      ],
    );
  }
}
```

### OrientationBuilder

```dart
class ResponsiveOrientation extends StatelessWidget {
  const ResponsiveOrientation({super.key});

  @override
  Widget build(BuildContext context) {
    return OrientationBuilder(
      builder: (context, orientation) {
        return orientation == Orientation.portrait
            ? PortraitView()
            : LandscapeView();
      },
    );
  }
}
```

## 自适应导航

### 响应式导航组件

```dart
class AdaptiveNavigation extends StatelessWidget {
  final int selectedIndex;
  final ValueChanged<int> onDestinationSelected;
  final List<NavigationDestination> destinations;

  const AdaptiveNavigation({
    super.key,
    required this.selectedIndex,
    required this.onDestinationSelected,
    required this.destinations,
  });

  @override
  Widget build(BuildContext context) {
    final responsive = Responsive.of(context);

    if (responsive.isDesktop) {
      return NavigationRail(
        selectedIndex: selectedIndex,
        onDestinationSelected: onDestinationSelected,
        labelType: NavigationRailLabelType.all,
        destinations: destinations
            .map((d) => NavigationRailDestination(
                  icon: d.icon,
                  label: Text(d.label),
                ))
            .toList(),
      );
    }

    return NavigationBar(
      selectedIndex: selectedIndex,
      onDestinationSelected: onDestinationSelected,
      destinations: destinations,
    );
  }
}
```

### 完整页面示例

```dart
class AdaptiveScaffold extends StatelessWidget {
  final String title;
  final Widget body;
  final int selectedIndex;
  final ValueChanged<int> onNavigationChanged;
  final List<NavigationItem> navigationItems;

  const AdaptiveScaffold({
    super.key,
    required this.title,
    required this.body,
    required this.selectedIndex,
    required this.onNavigationChanged,
    required this.navigationItems,
  });

  @override
  Widget build(BuildContext context) {
    return ResponsiveLayout(
      mobile: _buildMobile(context),
      tablet: _buildTablet(context),
      desktop: _buildDesktop(context),
    );
  }

  Widget _buildMobile(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(title)),
      body: body,
      bottomNavigationBar: NavigationBar(
        selectedIndex: selectedIndex,
        onDestinationSelected: onNavigationChanged,
        destinations: navigationItems
            .map((item) => NavigationDestination(
                  icon: item.icon,
                  label: item.label,
                ))
            .toList(),
      ),
    );
  }

  Widget _buildDesktop(BuildContext context) {
    return Scaffold(
      body: Row(
        children: [
          NavigationRail(
            selectedIndex: selectedIndex,
            onDestinationSelected: onNavigationChanged,
            extended: true,
            destinations: navigationItems
                .map((item) => NavigationRailDestination(
                      icon: item.icon,
                      label: Text(item.label),
                    ))
                .toList(),
          ),
          const VerticalDivider(thickness: 1, width: 1),
          Expanded(
            child: Column(
              children: [
                AppBar(title: Text(title)),
                Expanded(child: body),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

## 字体缩放

### 响应式字体

```dart
class ResponsiveText extends StatelessWidget {
  final String text;
  final TextStyle? style;
  final TextAlign? textAlign;

  const ResponsiveText({
    super.key,
    required this.text,
    this.style,
    this.textAlign,
  });

  @override
  Widget build(BuildContext context) {
    final responsive = Responsive.of(context);
    final baseStyle = style ?? Theme.of(context).textTheme.bodyLarge;

    // 根据屏幕宽度调整字体大小
    final fontSize = baseStyle?.fontSize ?? 16;
    final scaleFactor = responsive.value(
      mobile: 1.0,
      tablet: 1.1,
      desktop: 1.2,
    );

    return Text(
      text,
      style: baseStyle?.copyWith(fontSize: fontSize * scaleFactor),
      textAlign: textAlign,
    );
  }
}
```

## 最佳实践

### 1. 使用约束而非固定尺寸

```dart
// ❌ 固定尺寸
Container(
  width: 300,
  child: Content(),
)

// ✅ 响应式尺寸
Container(
  width: MediaQuery.of(context).size.width * 0.8,
  constraints: const BoxConstraints(maxWidth: 600),
  child: Content(),
)
```

### 2. 优先使用 Flex 布局

```dart
// ✅ 使用 Expanded/Flexible
Row(
  children: [
    Expanded(
      flex: 2,
      child: MainContent(),
    ),
    Expanded(
      flex: 1,
      child: SidePanel(),
    ),
  ],
)
```

### 3. 测试多种尺寸

```dart
// 在 MaterialApp 中设置多种尺寸测试
MaterialApp(
  builder: (context, child) {
    return MediaQuery(
      data: MediaQuery.of(context).copyWith(
        size: const Size(1200, 800), // 模拟桌面尺寸
      ),
      child: child!,
    );
  },
);
```

### 4. 使用 flutter_adaptive_scaffold 包

```yaml
dependencies:
  flutter_adaptive_scaffold: ^0.1.0
```

```dart
import 'package:flutter_adaptive_scaffold/flutter_adaptive_scaffold.dart';

AdaptiveScaffold(
  destinations: const [
    NavigationDestination(icon: Icon(Icons.home), label: '首页'),
    NavigationDestination(icon: Icon(Icons.settings), label: '设置'),
  ],
  body: (context) => const HomePage(),
)
```

---

*最后更新: 2026-02-19*

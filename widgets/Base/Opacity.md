# Flutter Opacity 组件用法详解

## 简介

Opacity 是 Flutter 中用于控制子组件透明度的组件。它可以调整子组件的整体不透明度，实现淡入淡出等视觉效果。

## 1. 初级用法

### 1.1 基本概念

Opacity 的作用：
- 设置子组件的透明度
- 值范围 0.0（完全透明）到 1.0（完全不透明）
- 影响子组件的所有内容

### 1.2 基本语法

```dart
Opacity(
  opacity: 0.5,
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `opacity` | `double` | 不透明度，0.0-1.0 |
| `child` | `Widget` | 子组件 |
| `alwaysIncludeSemantics` | `bool` | 是否始终包含语义 |

### 1.4 基础示例

#### 半透明效果

```dart
Opacity(
  opacity: 0.5,
  child: Container(
    width: 100,
    height: 100,
    color: Colors.red,
    child: Center(child: Text('半透明')),
  ),
)
```

#### 完全透明

```dart
Opacity(
  opacity: 0.0,
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
    child: Text('看不见'),
  ),
)
```

## 2. 中级用法

### 2.1 常见透明度值

```dart
Opacity(opacity: 1.0, ...)   // 完全不透明（默认）
Opacity(opacity: 0.75, ...)  // 75% 不透明
Opacity(opacity: 0.5, ...)   // 50% 不透明
Opacity(opacity: 0.25, ...)  // 25% 不透明
Opacity(opacity: 0.0, ...)   // 完全透明
```

### 2.2 常见布局场景

#### 禁用状态

```dart
class DisabledButton extends StatelessWidget {
  final bool enabled;
  final String text;
  final VoidCallback? onPressed;

  const DisabledButton({
    required this.enabled,
    required this.text,
    this.onPressed,
  });

  @override
  Widget build(BuildContext context) {
    return Opacity(
      opacity: enabled ? 1.0 : 0.5,
      child: ElevatedButton(
        onPressed: enabled ? onPressed : null,
        child: Text(text),
      ),
    );
  }
}
```

#### 加载遮罩

```dart
class LoadingOverlay extends StatelessWidget {
  final bool isLoading;
  final Widget child;

  const LoadingOverlay({
    required this.isLoading,
    required this.child,
  });

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        child,
        if (isLoading)
          Opacity(
            opacity: 0.5,
            child: ModalBarrier(
              color: Colors.grey,
              dismissible: false,
            ),
          ),
        if (isLoading)
          Center(child: CircularProgressIndicator()),
      ],
    );
  }
}
```

#### 骨架屏

```dart
class SkeletonItem extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Opacity(
      opacity: 0.5,
      child: Shimmer.fromColors(
        baseColor: Colors.grey[300]!,
        highlightColor: Colors.grey[100]!,
        child: Container(
          height: 80,
          color: Colors.grey[300],
        ),
      ),
    );
  }
}
```

### 2.3 与 Container 的 color 属性区别

```dart
// Opacity：整体透明，包括子组件
Opacity(
  opacity: 0.5,
  child: Container(
    color: Colors.blue,
    child: Text('文字也会变淡'),  // 文字也变淡
  ),
)

// Container color：只有背景透明
Container(
  color: Colors.blue.withValues(alpha: 0.5),
  child: Text('文字不会变淡'),  // 文字不变
)
```

### 2.4 叠加效果

```dart
Stack(
  children: [
    Container(width: 100, height: 100, color: Colors.red),
    Positioned(
      left: 30,
      top: 30,
      child: Opacity(
        opacity: 0.7,
        child: Container(width: 100, height: 100, color: Colors.blue),
      ),
    ),
  ],
)
```

## 3. 高级用法

### 3.1 AnimatedOpacity 淡入淡出

```dart
class FadeWidget extends StatefulWidget {
  @override
  State<FadeWidget> createState() => _FadeWidgetState();
}

class _FadeWidgetState extends State<FadeWidget> {
  bool _visible = true;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedOpacity(
          opacity: _visible ? 1.0 : 0.0,
          duration: Duration(milliseconds: 500),
          child: Container(
            width: 100,
            height: 100,
            color: Colors.blue,
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _visible = !_visible),
          child: Text('切换'),
        ),
      ],
    );
  }
}
```

### 3.2 渐变透明效果

```dart
ShaderMask(
  shaderCallback: (bounds) {
    return LinearGradient(
      begin: Alignment.topCenter,
      end: Alignment.bottomCenter,
      colors: [Colors.black, Colors.transparent],
      stops: [0.5, 1.0],
    ).createShader(bounds);
  },
  blendMode: BlendMode.dstIn,
  child: Image.network('https://picsum.photos/200'),
)
```

### 3.3 悬停效果

```dart
class HoverCard extends StatefulWidget {
  @override
  State<HoverCard> createState() => _HoverCardState();
}

class _HoverCardState extends State<HoverCard> {
  bool _hovering = false;

  @override
  Widget build(BuildContext context) {
    return MouseRegion(
      onEnter: (_) => setState(() => _hovering = true),
      onExit: (_) => setState(() => _hovering = false),
      child: AnimatedOpacity(
        opacity: _hovering ? 0.8 : 1.0,
        duration: Duration(milliseconds: 200),
        child: Card(
          child: Padding(
            padding: EdgeInsets.all(16),
            child: Text('悬停试试'),
          ),
        ),
      ),
    );
  }
}
```

### 3.4 淡入动画

```dart
class FadeInOnLoad extends StatefulWidget {
  final Widget child;
  final Duration duration;

  const FadeInOnLoad({
    required this.child,
    this.duration = const Duration(milliseconds: 500),
  });

  @override
  State<FadeInOnLoad> createState() => _FadeInOnLoadState();
}

class _FadeInOnLoadState extends State<FadeInOnLoad>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: widget.duration,
      vsync: this,
    );
    _animation = CurvedAnimation(
      parent: _controller,
      curve: Curves.easeIn,
    );
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return FadeTransition(
      opacity: _animation,
      child: widget.child,
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：透明度为 0 仍可点击

```dart
// 问题：opacity 为 0 时组件仍可点击
Opacity(
  opacity: 0.0,
  child: ElevatedButton(onPressed: () {}, child: Text('点击')),
)

// 解决：使用 IgnorePointer 或条件渲染
Opacity(
  opacity: 0.0,
  child: IgnorePointer(
    child: ElevatedButton(onPressed: () {}, child: Text('点击')),
  ),
)

// 或直接不渲染
if (visible) ElevatedButton(onPressed: () {}, child: Text('点击'))
```

### 问题2：性能问题

```dart
// 问题：Opacity 有性能开销
// 每帧都需要计算透明度

// 解决：简单场景使用 Visibility 或条件渲染
Visibility(
  visible: visible,
  child: Container(...),
)

// 或
if (visible) Container(...)
```

### 问题3：alpha 与 opacity 区别

```dart
// Opacity：组件级别的透明度
Opacity(opacity: 0.5, child: ...)

// Color.withValues：颜色级别的透明度
Container(color: Colors.blue.withValues(alpha: 0.5))

// 区别：Opacity 影响整个子组件，包括文字、图片等
```

## 5. Opacity vs 其他透明方案对比

| 方案 | 用途 | 特点 |
|------|------|------|
| **Opacity** | 整体透明 | 影响所有子内容 |
| **Color.withValues** | 颜色透明 | 只影响特定颜色 |
| **AnimatedOpacity** | 动画透明 | 自动过渡动画 |
| **Visibility** | 显示/隐藏 | 无动画，性能好 |

### 选择建议

```dart
// 需要整体透明 → Opacity
Opacity(opacity: 0.5, child: ...)

// 只需背景透明 → Color.withValues
Container(color: Colors.blue.withValues(alpha: 0.5))

// 需要淡入淡出 → AnimatedOpacity
AnimatedOpacity(opacity: visible ? 1.0 : 0.0, ...)

// 只是显示隐藏 → Visibility
Visibility(visible: visible, child: ...)
```

## 6. 最佳实践

### 6.1 预定义透明度常量

```dart
abstract class AppOpacity {
  static const double full = 1.0;
  static const double high = 0.87;
  static const double medium = 0.6;
  static const double low = 0.38;
  static const double disabled = 0.5;
  static const double none = 0.0;
}

// 使用
Opacity(opacity: AppOpacity.disabled, child: ...)
```

### 6.2 封装淡入组件

```dart
class FadeIn extends StatefulWidget {
  final Widget child;
  final Duration duration;

  const FadeIn({
    required this.child,
    this.duration = const Duration(milliseconds: 300),
  });

  @override
  State<FadeIn> createState() => _FadeInState();
}

class _FadeInState extends State<FadeIn> with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: widget.duration,
      vsync: this,
    );
    _animation = CurvedAnimation(parent: _controller, curve: Curves.easeIn);
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return FadeTransition(opacity: _animation, child: widget.child);
  }
}
```

### 6.3 性能考虑

```dart
// 性能排序（从好到差）
// 1. 不渲染（条件判断）
// 2. Visibility
// 3. Opacity(0) + IgnorePointer
// 4. Opacity(0)

// 对于频繁切换的场景，优先使用 Visibility
```

## 总结

Opacity 是 Flutter 中控制组件透明度的基础组件：

**核心要点**：
1. opacity 范围 0.0-1.0
2. 影响整个子组件
3. 透明度为 0 仍占用布局空间

**最佳实践**：
1. 动画场景用 AnimatedOpacity
2. 简单显示隐藏用 Visibility
3. 预定义透明度常量

**常见场景**：
- 禁用状态
- 加载遮罩
- 淡入淡出
- 悬停效果

---

*最后更新: 2026-02-19*

# Flutter AnimatedContainer 组件用法详解

## 简介

AnimatedContainer 是 Flutter 中用于实现容器属性动画的组件。它可以自动将容器的属性变化转换为平滑的动画效果。

## 1. 初级用法

### 1.1 基本概念

AnimatedContainer 的核心作用：
- 自动动画化容器属性变化
- 无需 AnimationController
- 支持 width、height、color、decoration 等属性

### 1.2 基本语法

```dart
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  width: _width,
  height: _height,
  color: _color,
  child: Text('Animated'),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `duration` | `Duration` | 动画时长 |
| `curve` | `Curve` | 动画曲线，默认 Curves.linear |
| `width` | `double?` | 宽度 |
| `height` | `double?` | 高度 |
| `color` | `Color?` | 背景颜色 |
| `decoration` | `Decoration?` | 装饰 |
| `padding` | `EdgeInsetsGeometry?` | 内边距 |
| `alignment` | `AlignmentGeometry?` | 对齐方式 |
| `transform` | `Matrix4?` | 变换矩阵 |
| `onEnd` | `VoidCallback?` | 动画结束回调 |

### 1.4 基础示例

#### 尺寸动画

```dart
class SizeAnimationExample extends StatefulWidget {
  @override
  State<SizeAnimationExample> createState() => _SizeAnimationExampleState();
}

class _SizeAnimationExampleState extends State<SizeAnimationExample> {
  double _width = 100;
  double _height = 100;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedContainer(
          duration: Duration(milliseconds: 500),
          curve: Curves.easeInOut,
          width: _width,
          height: _height,
          color: Colors.blue,
        ),
        ElevatedButton(
          onPressed: () => setState(() {
            _width = _width == 100 ? 200 : 100;
            _height = _height == 100 ? 150 : 100;
          }),
          child: Text('Toggle Size'),
        ),
      ],
    );
  }
}
```

#### 颜色动画

```dart
class ColorAnimationExample extends StatefulWidget {
  @override
  State<ColorAnimationExample> createState() => _ColorAnimationExampleState();
}

class _ColorAnimationExampleState extends State<ColorAnimationExample> {
  Color _color = Colors.blue;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => setState(() {
        _color = _color == Colors.blue ? Colors.red : Colors.blue;
      }),
      child: AnimatedContainer(
        duration: Duration(milliseconds: 500),
        width: 200,
        height: 100,
        color: _color,
        child: Center(child: Text('点击切换颜色', style: TextStyle(color: Colors.white))),
      ),
    );
  }
}
```

## 2. 中级用法

### 2.1 多属性动画

```dart
AnimatedContainer(
  duration: Duration(milliseconds: 500),
  curve: Curves.easeInOut,
  width: _isExpanded ? 300 : 100,
  height: _isExpanded ? 200 : 100,
  padding: _isExpanded ? EdgeInsets.all(24) : EdgeInsets.all(8),
  decoration: BoxDecoration(
    color: _isExpanded ? Colors.blue : Colors.grey,
    borderRadius: BorderRadius.circular(_isExpanded ? 24 : 8),
  ),
  child: Text('Multiple properties'),
)
```

### 2.2 圆角动画

```dart
class BorderRadiusAnimation extends StatefulWidget {
  @override
  State<BorderRadiusAnimation> createState() => _BorderRadiusAnimationState();
}

class _BorderRadiusAnimationState extends State<BorderRadiusAnimation> {
  bool _isCircle = false;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => setState(() => _isCircle = !_isCircle),
      child: AnimatedContainer(
        duration: Duration(milliseconds: 300),
        width: 100,
        height: 100,
        decoration: BoxDecoration(
          color: Colors.purple,
          borderRadius: BorderRadius.circular(_isCircle ? 50 : 8),
        ),
      ),
    );
  }
}
```

### 2.3 内边距动画

```dart
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  padding: EdgeInsets.all(_isHovered ? 20 : 10),
  decoration: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(8),
  ),
  child: Text('Padding Animation'),
)
```

### 2.4 阴影动画

```dart
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  decoration: BoxDecoration(
    color: Colors.white,
    borderRadius: BorderRadius.circular(8),
    boxShadow: [
      BoxShadow(
        color: Colors.black26,
        blurRadius: _isHovered ? 20 : 8,
        offset: Offset(0, _isHovered ? 10 : 4),
      ),
    ],
  ),
  child: Card(child: Text('Shadow Animation')),
)
```

### 2.5 渐变动画

```dart
AnimatedContainer(
  duration: Duration(milliseconds: 500),
  decoration: BoxDecoration(
    gradient: LinearGradient(
      colors: _isLight 
          ? [Colors.blue, Colors.green]
          : [Colors.purple, Colors.pink],
      begin: Alignment.topLeft,
      end: Alignment.bottomRight,
    ),
  ),
  child: Text('Gradient Animation'),
)
```

## 3. 高级用法

### 3.1 悬停效果卡片

```dart
class HoverCard extends StatefulWidget {
  final Widget child;
  final VoidCallback? onTap;

  const HoverCard({required this.child, this.onTap, Key? key}) : super(key: key);

  @override
  State<HoverCard> createState() => _HoverCardState();
}

class _HoverCardState extends State<HoverCard> {
  bool _isHovered = false;

  @override
  Widget build(BuildContext context) {
    return MouseRegion(
      onEnter: (_) => setState(() => _isHovered = true),
      onExit: (_) => setState(() => _isHovered = false),
      child: GestureDetector(
        onTap: widget.onTap,
        child: AnimatedContainer(
          duration: Duration(milliseconds: 200),
          curve: Curves.easeOut,
          transform: Matrix4.identity()
            ..translate(0.0, _isHovered ? -5.0 : 0.0),
          decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.circular(12),
            boxShadow: [
              BoxShadow(
                color: Colors.black12,
                blurRadius: _isHovered ? 20 : 8,
                offset: Offset(0, _isHovered ? 10 : 4),
              ),
            ],
          ),
          child: widget.child,
        ),
      ),
    );
  }
}
```

### 3.2 展开/折叠面板

```dart
class ExpandablePanel extends StatefulWidget {
  final String title;
  final Widget content;

  const ExpandablePanel({
    required this.title,
    required this.content,
    Key? key,
  }) : super(key: key);

  @override
  State<ExpandablePanel> createState() => _ExpandablePanelState();
}

class _ExpandablePanelState extends State<ExpandablePanel> {
  bool _isExpanded = false;

  @override
  Widget build(BuildContext context) {
    return Container(
      decoration: BoxDecoration(
        border: Border.all(color: Colors.grey[300]!),
        borderRadius: BorderRadius.circular(8),
      ),
      child: Column(
        children: [
          GestureDetector(
            onTap: () => setState(() => _isExpanded = !_isExpanded),
            child: Container(
              padding: EdgeInsets.all(16),
              child: Row(
                children: [
                  Text(widget.title, style: TextStyle(fontWeight: FontWeight.bold)),
                  Spacer(),
                  AnimatedRotation(
                    duration: Duration(milliseconds: 200),
                    turns: _isExpanded ? 0.5 : 0,
                    child: Icon(Icons.expand_more),
                  ),
                ],
              ),
            ),
          ),
          ClipRect(
            child: AnimatedContainer(
              duration: Duration(milliseconds: 300),
              curve: Curves.easeInOut,
              height: _isExpanded ? null : 0,
              child: widget.content,
            ),
          ),
        ],
      ),
    );
  }
}
```

### 3.3 加载按钮

```dart
class LoadingButton extends StatefulWidget {
  final String text;
  final Future<void> Function() onPressed;

  const LoadingButton({
    required this.text,
    required this.onPressed,
    Key? key,
  }) : super(key: key);

  @override
  State<LoadingButton> createState() => _LoadingButtonState();
}

class _LoadingButtonState extends State<LoadingButton> {
  bool _isLoading = false;

  Future<void> _handlePress() async {
    if (_isLoading) return;
    setState(() => _isLoading = true);
    try {
      await widget.onPressed();
    } finally {
      if (mounted) {
        setState(() => _isLoading = false);
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: _handlePress,
      child: AnimatedContainer(
        duration: Duration(milliseconds: 200),
        width: _isLoading ? 48 : 200,
        height: 48,
        decoration: BoxDecoration(
          color: Colors.blue,
          borderRadius: BorderRadius.circular(_isLoading ? 24 : 8),
        ),
        child: Center(
          child: _isLoading
              ? SizedBox(
                  width: 24,
                  height: 24,
                  child: CircularProgressIndicator(
                    strokeWidth: 2,
                    color: Colors.white,
                  ),
                )
              : Text(
                  widget.text,
                  style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold),
                ),
        ),
      ),
    );
  }
}
```

### 3.4 动画结束回调

```dart
class AnimatedContainerWithCallback extends StatefulWidget {
  @override
  State<AnimatedContainerWithCallback> createState() => _AnimatedContainerWithCallbackState();
}

class _AnimatedContainerWithCallbackState extends State<AnimatedContainerWithCallback> {
  double _width = 100;
  int _animationCount = 0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedContainer(
          duration: Duration(milliseconds: 500),
          width: _width,
          height: 100,
          color: Colors.blue,
          onEnd: () {
            setState(() => _animationCount++);
            print('Animation ended! Count: $_animationCount');
          },
        ),
        Text('Animation count: $_animationCount'),
        ElevatedButton(
          onPressed: () => setState(() {
            _width = _width == 100 ? 200 : 100;
          }),
          child: Text('Animate'),
        ),
      ],
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：动画不流畅

```dart
// 使用合适的曲线
AnimatedContainer(
  curve: Curves.easeInOutCubic,  // 更流畅的曲线
  duration: Duration(milliseconds: 300),
  // ...
)
```

### 问题2：decoration 和 color 冲突

```dart
// ❌ 错误：同时使用 decoration 和 color
AnimatedContainer(
  color: Colors.blue,
  decoration: BoxDecoration(color: Colors.red),  // 冲突
)

// ✅ 正确：使用 decoration 的 color
AnimatedContainer(
  decoration: BoxDecoration(color: Colors.blue),
)
```

### 问题3：子组件不随容器变化

```dart
// 使用 alignment 控制子组件对齐
AnimatedContainer(
  width: _width,
  alignment: Alignment.center,
  child: Text('Always centered'),
)
```

## 5. AnimatedContainer vs 其他动画方式

| 方式 | 用途 | 特点 |
|------|------|------|
| **AnimatedContainer** | 容器属性动画 | 简单，无需 Controller |
| **AnimationController** | 复杂动画 | 灵活，完全控制 |
| **TweenAnimationBuilder** | 单值动画 | 自定义动画值 |
| **AnimatedBuilder** | 自定义动画 | 配合 Controller |

### 选择建议

```dart
// 简单属性变化 → AnimatedContainer
AnimatedContainer(duration: Duration(milliseconds: 300), ...)

// 复杂动画序列 → AnimationController
AnimationController(duration: Duration(seconds: 1), ...)

// 单值自定义动画 → TweenAnimationBuilder
TweenAnimationBuilder<double>(duration: ..., tween: ..., builder: ...)
```

## 6. 最佳实践

### 6.1 统一动画时长

```dart
abstract class AppDurations {
  static const Duration fast = Duration(milliseconds: 150);
  static const Duration normal = Duration(milliseconds: 300);
  static const Duration slow = Duration(milliseconds: 500);
}

AnimatedContainer(
  duration: AppDurations.normal,
  // ...
)
```

### 6.2 统一曲线

```dart
abstract class AppCurves {
  static const Curve standard = Curves.easeInOut;
  static const Curve bounce = Curves.elasticOut;
  static const Curve sharp = Curves.easeOutCubic;
}
```

### 6.3 性能优化

```dart
// 避免在 build 中创建对象
AnimatedContainer(
  duration: const Duration(milliseconds: 300),  // const
  curve: Curves.easeInOut,
)
```

## 总结

AnimatedContainer 是实现容器属性动画的便捷工具：

**核心要点**：
1. 自动动画化属性变化
2. 支持 width、height、color、decoration 等
3. duration 和 curve 控制动画效果

**最佳实践**：
1. 统一动画时长和曲线
2. 使用 decoration 替代 color + decoration
3. 合理使用 onEnd 回调

**常见场景**：
- 尺寸变化
- 颜色过渡
- 悬停效果
- 展开/折叠

---

*最后更新: 2026-02-19*

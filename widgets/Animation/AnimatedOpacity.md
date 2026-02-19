# Flutter AnimatedOpacity 组件用法详解

## 简介

AnimatedOpacity 是 Flutter 中用于实现透明度动画的组件。它可以自动将透明度变化转换为平滑的淡入淡出效果。

## 1. 初级用法

### 1.1 基本概念

AnimatedOpacity 的核心作用：
- 动画化透明度变化
- 无需 AnimationController
- 适合淡入淡出效果

### 1.2 基本语法

```dart
AnimatedOpacity(
  opacity: _visible ? 1.0 : 0.0,
  duration: Duration(milliseconds: 300),
  child: Text('Fade'),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `opacity` | `double` | 透明度（0.0 - 1.0） |
| `duration` | `Duration` | 动画时长 |
| `curve` | `Curve` | 动画曲线 |
| `onEnd` | `VoidCallback?` | 动画结束回调 |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 淡入淡出

```dart
class FadeExample extends StatefulWidget {
  @override
  State<FadeExample> createState() => _FadeExampleState();
}

class _FadeExampleState extends State<FadeExample> {
  bool _visible = true;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedOpacity(
          opacity: _visible ? 1.0 : 0.0,
          duration: Duration(milliseconds: 500),
          child: Container(
            width: 200,
            height: 100,
            color: Colors.blue,
            child: Center(child: Text('Fade me')),
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _visible = !_visible),
          child: Text(_visible ? 'Hide' : 'Show'),
        ),
      ],
    );
  }
}
```

#### 渐变透明度

```dart
class GradientOpacityExample extends StatefulWidget {
  @override
  State<GradientOpacityExample> createState() => _GradientOpacityExampleState();
}

class _GradientOpacityExampleState extends State<GradientOpacityExample> {
  double _opacity = 1.0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedOpacity(
          opacity: _opacity,
          duration: Duration(milliseconds: 300),
          child: Container(
            width: 150,
            height: 150,
            color: Colors.purple,
          ),
        ),
        Slider(
          value: _opacity,
          onChanged: (value) => setState(() => _opacity = value),
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 闪烁效果

```dart
class BlinkingWidget extends StatefulWidget {
  final Widget child;
  final Duration duration;

  const BlinkingWidget({
    required this.child,
    this.duration = const Duration(milliseconds: 500),
    Key? key,
  }) : super(key: key);

  @override
  State<BlinkingWidget> createState() => _BlinkingWidgetState();
}

class _BlinkingWidgetState extends State<BlinkingWidget> {
  bool _visible = true;

  @override
  void initState() {
    super.initState();
    _startBlinking();
  }

  void _startBlinking() {
    Future.delayed(widget.duration, () {
      if (mounted) {
        setState(() => _visible = !_visible);
        _startBlinking();
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedOpacity(
      opacity: _visible ? 1.0 : 0.0,
      duration: widget.duration,
      child: widget.child,
    );
  }
}
```

### 2.2 延迟显示

```dart
class DelayedAppearance extends StatefulWidget {
  final Widget child;
  final Duration delay;

  const DelayedAppearance({
    required this.child,
    required this.delay,
    Key? key,
  }) : super(key: key);

  @override
  State<DelayedAppearance> createState() => _DelayedAppearanceState();
}

class _DelayedAppearanceState extends State<DelayedAppearance> {
  bool _visible = false;

  @override
  void initState() {
    super.initState();
    Future.delayed(widget.delay, () {
      if (mounted) setState(() => _visible = true);
    });
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedOpacity(
      opacity: _visible ? 1.0 : 0.0,
      duration: Duration(milliseconds: 500),
      child: widget.child,
    );
  }
}
```

### 2.3 列表项动画

```dart
class AnimatedListItem extends StatefulWidget {
  final Widget child;
  final int index;

  const AnimatedListItem({
    required this.child,
    required this.index,
    Key? key,
  }) : super(key: key);

  @override
  State<AnimatedListItem> createState() => _AnimatedListItemState();
}

class _AnimatedListItemState extends State<AnimatedListItem> {
  bool _visible = false;

  @override
  void initState() {
    super.initState();
    Future.delayed(Duration(milliseconds: 100 * widget.index), () {
      if (mounted) setState(() => _visible = true);
    });
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedOpacity(
      opacity: _visible ? 1.0 : 0.0,
      duration: Duration(milliseconds: 300),
      child: widget.child,
    );
  }
}
```

### 2.4 切换内容

```dart
class FadeSwitcher extends StatefulWidget {
  @override
  State<FadeSwitcher> createState() => _FadeSwitcherState();
}

class _FadeSwitcherState extends State<FadeSwitcher> {
  bool _showFirst = true;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => setState(() => _showFirst = !_showFirst),
      child: AnimatedOpacity(
        opacity: 1.0,
        duration: Duration(milliseconds: 300),
        child: _showFirst
            ? Container(
                width: 100,
                height: 100,
                color: Colors.blue,
                child: Center(child: Text('First')),
              )
            : Container(
                width: 100,
                height: 100,
                color: Colors.red,
                child: Center(child: Text('Second')),
              ),
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 骨架屏效果

```dart
class SkeletonLoading extends StatefulWidget {
  @override
  State<SkeletonLoading> createState() => _SkeletonLoadingState();
}

class _SkeletonLoadingState extends State<SkeletonLoading> {
  double _opacity = 0.3;

  @override
  void initState() {
    super.initState();
    _animate();
  }

  void _animate() {
    Future.delayed(Duration(milliseconds: 500), () {
      if (mounted) {
        setState(() => _opacity = _opacity == 0.3 ? 0.6 : 0.3);
        _animate();
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedOpacity(
      opacity: _opacity,
      duration: Duration(milliseconds: 500),
      child: Column(
        children: [
          Container(
            width: double.infinity,
            height: 20,
            color: Colors.grey[300],
          ),
          SizedBox(height: 8),
          Container(
            width: 200,
            height: 20,
            color: Colors.grey[300],
          ),
        ],
      ),
    );
  }
}
```

### 3.2 加载状态

```dart
class LoadingOverlay extends StatelessWidget {
  final bool isLoading;
  final Widget child;

  const LoadingOverlay({
    required this.isLoading,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        child,
        AnimatedOpacity(
          opacity: isLoading ? 1.0 : 0.0,
          duration: Duration(milliseconds: 200),
          child: IgnorePointer(
            ignoring: !isLoading,
            child: Container(
              color: Colors.black54,
              child: Center(child: CircularProgressIndicator()),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.3 图片淡入

```dart
class FadeInImage extends StatefulWidget {
  final String url;

  const FadeInImage({required this.url, Key? key}) : super(key: key);

  @override
  State<FadeInImage> createState() => _FadeInImageState();
}

class _FadeInImageState extends State<FadeInImage> {
  bool _loaded = false;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Container(
          width: 200,
          height: 200,
          color: Colors.grey[200],
          child: Center(child: CircularProgressIndicator()),
        ),
        AnimatedOpacity(
          opacity: _loaded ? 1.0 : 0.0,
          duration: Duration(milliseconds: 300),
          child: Image.network(
            widget.url,
            width: 200,
            height: 200,
            fit: BoxFit.cover,
            frameBuilder: (context, child, frame, wasSynchronouslyLoaded) {
              if (frame != null && !_loaded) {
                WidgetsBinding.instance.addPostFrameCallback((_) {
                  setState(() => _loaded = true);
                });
              }
              return child;
            },
          ),
        ),
      ],
    );
  }
}
```

### 3.4 提示信息

```dart
class ToastMessage extends StatefulWidget {
  final String message;
  final Duration duration;

  const ToastMessage({
    required this.message,
    this.duration = const Duration(seconds: 2),
    Key? key,
  }) : super(key: key);

  @override
  State<ToastMessage> createState() => _ToastMessageState();
}

class _ToastMessageState extends State<ToastMessage> {
  double _opacity = 0.0;

  @override
  void initState() {
    super.initState();
    _show();
  }

  void _show() async {
    await Future.delayed(Duration(milliseconds: 50));
    if (mounted) setState(() => _opacity = 1.0);
    await Future.delayed(widget.duration);
    if (mounted) setState(() => _opacity = 0.0);
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedOpacity(
      opacity: _opacity,
      duration: Duration(milliseconds: 300),
      onEnd: () {
        if (_opacity == 0.0) {
          // 可以在这里移除 widget
        }
      },
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
        decoration: BoxDecoration(
          color: Colors.black87,
          borderRadius: BorderRadius.circular(8),
        ),
        child: Text(
          widget.message,
          style: TextStyle(color: Colors.white),
        ),
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：隐藏时仍占空间

```dart
// AnimatedOpacity 隐藏时仍占布局空间
// 使用 IgnorePointer 禁用交互

AnimatedOpacity(
  opacity: _visible ? 1.0 : 0.0,
  duration: Duration(milliseconds: 300),
  child: IgnorePointer(
    ignoring: !_visible,
    child: YourWidget(),
  ),
)
```

### 问题2：需要移除组件

```dart
// 使用条件渲染配合 AnimatedOpacity
if (_visible)
  AnimatedOpacity(
    opacity: _opacity,
    duration: Duration(milliseconds: 300),
    child: YourWidget(),
  )
else
  SizedBox.shrink()
```

### 问题3：动画曲线选择

```dart
// 淡入使用 easeOut，淡出使用 easeIn
AnimatedOpacity(
  opacity: _visible ? 1.0 : 0.0,
  curve: _visible ? Curves.easeOut : Curves.easeIn,
  duration: Duration(milliseconds: 300),
  child: YourWidget(),
)
```

## 5. AnimatedOpacity vs 其他方式

| 方式 | 用途 | 特点 |
|------|------|------|
| **AnimatedOpacity** | 透明度动画 | 简单，无需 Controller |
| **Opacity** | 静态透明度 | 无动画 |
| **FadeTransition** | 透明度动画 | 配合 AnimationController |
| **AnimatedCrossFade** | 切换动画 | 两个组件间切换 |

### 选择建议

```dart
// 简单淡入淡出 → AnimatedOpacity
AnimatedOpacity(opacity: _visible ? 1.0 : 0.0, ...)

// 两个组件切换 → AnimatedCrossFade
AnimatedCrossFade(firstChild: A, secondChild: B, ...)

// 复杂动画 → FadeTransition
FadeTransition(opacity: _animation, ...)
```

## 6. 最佳实践

### 6.1 统一动画时长

```dart
const kDefaultFadeDuration = Duration(milliseconds: 300);

AnimatedOpacity(
  opacity: _opacity,
  duration: kDefaultFadeDuration,
  child: YourWidget(),
)
```

### 6.2 配合 IgnorePointer

```dart
AnimatedOpacity(
  opacity: _visible ? 1.0 : 0.0,
  duration: Duration(milliseconds: 200),
  child: IgnorePointer(
    ignoring: !_visible,
    child: YourWidget(),
  ),
)
```

### 6.3 列表项延迟显示

```dart
ListView.builder(
  itemBuilder: (context, index) {
    return AnimatedOpacity(
      opacity: _itemsVisible ? 1.0 : 0.0,
      duration: Duration(milliseconds: 300),
      curve: Interval(
        (index * 0.1).clamp(0.0, 1.0),
        ((index + 1) * 0.1).clamp(0.0, 1.0),
      ),
      child: ListItem(index: index),
    );
  },
)
```

## 总结

AnimatedOpacity 是实现淡入淡出效果的便捷工具：

**核心要点**：
1. opacity 值范围 0.0 - 1.0
2. 自动动画化透明度变化
3. 隐藏时仍占用布局空间

**最佳实践**：
1. 配合 IgnorePointer 禁用隐藏时的交互
2. 使用条件渲染完全移除组件
3. 统一动画时长和曲线

**常见场景**：
- 淡入淡出
- 加载状态
- 提示信息
- 骨架屏

---

*最后更新: 2026-02-19*

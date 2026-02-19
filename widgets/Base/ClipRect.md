# Flutter ClipRect 组件用法详解

## 简介

ClipRect 是 Flutter 中用于将子组件裁剪为矩形的裁剪组件。它使用矩形边界裁剪子组件，是最基础的裁剪组件。

## 1. 初级用法

### 1.1 基本概念

ClipRect 的作用：
- 将子组件裁剪为矩形
- 默认裁剪到子组件的边界
- 使用 clipper 可自定义裁剪区域

### 1.2 基本语法

```dart
ClipRect(
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
| `child` | `Widget?` | 子组件 |
| `clipper` | `CustomClipper<Rect>?` | 自定义裁剪器 |
| `clipBehavior` | `Clip` | 裁剪行为，默认 Clip.hardEdge |

### 1.4 基础示例

#### 基本裁剪

```dart
ClipRect(
  child: Align(
    alignment: Alignment.center,
    heightFactor: 0.5,
    child: Container(
      width: 200,
      height: 200,
      color: Colors.blue,
    ),
  ),
)
```

#### 溢出裁剪

```dart
SizedBox(
  width: 100,
  height: 100,
  child: ClipRect(
    child: OverflowBox(
      alignment: Alignment.center,
      minWidth: 200,
      minHeight: 200,
      child: Container(
        width: 200,
        height: 200,
        color: Colors.red,
      ),
    ),
  ),
)
```

## 2. 中级用法

### 2.1 自定义裁剪区域

```dart
class RectClipper extends CustomClipper<Rect> {
  @override
  Rect getClip(Size size) {
    return Rect.fromLTWH(0, 0, size.width * 0.5, size.height);
  }

  @override
  bool shouldReclip(RectClipper oldClipper) => false;
}

// 使用：只显示左半部分
ClipRect(
  clipper: RectClipper(),
  child: Container(
    width: 200,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 2.2 常见布局场景

#### 限制溢出内容

```dart
Container(
  width: 200,
  height: 100,
  color: Colors.grey[200],
  child: ClipRect(
    child: Container(
      width: 300,  // 超出父容器
      height: 100,
      color: Colors.blue,
      child: Center(child: Text('超出部分被裁剪')),
    ),
  ),
)
```

#### 动画展开/收起

```dart
class ExpandableContent extends StatefulWidget {
  final Widget child;

  const ExpandableContent({required this.child});

  @override
  State<ExpandableContent> createState() => _ExpandableContentState();
}

class _ExpandableContentState extends State<ExpandableContent>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  bool _expanded = false;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 300),
      vsync: this,
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  void _toggle() {
    setState(() {
      _expanded = !_expanded;
      if (_expanded) {
        _controller.forward();
      } else {
        _controller.reverse();
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextButton(
          onPressed: _toggle,
          child: Text(_expanded ? '收起' : '展开'),
        ),
        AnimatedBuilder(
          animation: _controller,
          builder: (context, child) {
            return ClipRect(
              child: Align(
                heightFactor: _controller.value,
                child: widget.child,
              ),
            );
          },
        ),
      ],
    );
  }
}
```

#### 自定义裁剪动画

```dart
class AnimatedClipRect extends StatefulWidget {
  final Widget child;
  final bool isOpen;

  const AnimatedClipRect({
    required this.child,
    required this.isOpen,
  });

  @override
  State<AnimatedClipRect> createState() => _AnimatedClipRectState();
}

class _AnimatedClipRectState extends State<AnimatedClipRect>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 300),
      vsync: this,
    );
    _animation = CurvedAnimation(
      parent: _controller,
      curve: Curves.easeInOut,
    );
    if (widget.isOpen) {
      _controller.value = 1.0;
    }
  }

  @override
  void didUpdateWidget(AnimatedClipRect oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (widget.isOpen != oldWidget.isOpen) {
      if (widget.isOpen) {
        _controller.forward();
      } else {
        _controller.reverse();
      }
    }
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _animation,
      builder: (context, child) {
        return ClipRect(
          child: Align(
            heightFactor: _animation.value,
            child: child,
          ),
        );
      },
      child: widget.child,
    );
  }
}
```

### 2.3 配合 Align 使用

```dart
// heightFactor 实现高度裁剪
ClipRect(
  child: Align(
    alignment: Alignment.topCenter,
    heightFactor: 0.5,  // 只显示上半部分
    child: Container(
      height: 200,
      color: Colors.blue,
    ),
  ),
)

// widthFactor 实现宽度裁剪
ClipRect(
  child: Align(
    alignment: Alignment.centerLeft,
    widthFactor: 0.5,  // 只显示左半部分
    child: Container(
      width: 200,
      color: Colors.green,
    ),
  ),
)
```

### 2.4 进度条实现

```dart
class ProgressBar extends StatelessWidget {
  final double progress;
  final Color color;
  final Color backgroundColor;
  final double height;

  const ProgressBar({
    required this.progress,
    this.color = Colors.blue,
    this.backgroundColor = Colors.grey,
    this.height = 8,
  });

  @override
  Widget build(BuildContext context) {
    return Container(
      height: height,
      decoration: BoxDecoration(
        color: backgroundColor,
        borderRadius: BorderRadius.circular(height / 2),
      ),
      child: ClipRRect(
        borderRadius: BorderRadius.circular(height / 2),
        child: Stack(
          children: [
            ClipRect(
              child: Align(
                widthFactor: progress.clamp(0.0, 1.0),
                child: Container(
                  height: height,
                  color: color,
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 动态裁剪区域

```dart
class DynamicClipRect extends StatefulWidget {
  @override
  State<DynamicClipRect> createState() => _DynamicClipRectState();
}

class _DynamicClipRectState extends State<DynamicClipRect> {
  double _clipWidth = 1.0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Slider(
          value: _clipWidth,
          onChanged: (value) => setState(() => _clipWidth = value),
        ),
        ClipRect(
          clipper: WidthClipper(_clipWidth),
          child: Container(
            width: 200,
            height: 100,
            color: Colors.blue,
            child: Center(child: Text('裁剪内容')),
          ),
        ),
      ],
    );
  }
}

class WidthClipper extends CustomClipper<Rect> {
  final double widthFactor;

  WidthClipper(this.widthFactor);

  @override
  Rect getClip(Size size) {
    return Rect.fromLTWH(0, 0, size.width * widthFactor, size.height);
  }

  @override
  bool shouldReclip(WidthClipper oldClipper) {
    return widthFactor != oldClipper.widthFactor;
  }
}
```

### 3.2 图片局部显示

```dart
class ImageCropper extends StatelessWidget {
  final String imageUrl;
  final double topFactor;
  final double leftFactor;
  final double widthFactor;
  final double heightFactor;

  const ImageCropper({
    required this.imageUrl,
    this.topFactor = 0,
    this.leftFactor = 0,
    this.widthFactor = 1.0,
    this.heightFactor = 1.0,
  });

  @override
  Widget build(BuildContext context) {
    return ClipRect(
      clipper: ImageClipper(
        topFactor: topFactor,
        leftFactor: leftFactor,
        widthFactor: widthFactor,
        heightFactor: heightFactor,
      ),
      child: Image.network(imageUrl),
    );
  }
}

class ImageClipper extends CustomClipper<Rect> {
  final double topFactor;
  final double leftFactor;
  final double widthFactor;
  final double heightFactor;

  ImageClipper({
    required this.topFactor,
    required this.leftFactor,
    required this.widthFactor,
    required this.heightFactor,
  });

  @override
  Rect getClip(Size size) {
    return Rect.fromLTWH(
      size.width * leftFactor,
      size.height * topFactor,
      size.width * widthFactor,
      size.height * heightFactor,
    );
  }

  @override
  bool shouldReclip(ImageClipper oldClipper) {
    return topFactor != oldClipper.topFactor ||
        leftFactor != oldClipper.leftFactor ||
        widthFactor != oldClipper.widthFactor ||
        heightFactor != oldClipper.heightFactor;
  }
}
```

### 3.3 文字渐隐效果

```dart
class FadeText extends StatelessWidget {
  final String text;
  final double fadeHeight;

  const FadeText({
    required this.text,
    this.fadeHeight = 40,
  });

  @override
  Widget build(BuildContext context) {
    return ShaderMask(
      shaderCallback: (bounds) {
        return LinearGradient(
          begin: Alignment.topCenter,
          end: Alignment.bottomCenter,
          colors: [Colors.black, Colors.black, Colors.transparent],
          stops: [0, 1 - fadeHeight / bounds.height, 1],
        ).createShader(bounds);
      },
      blendMode: BlendMode.dstIn,
      child: Text(text, maxLines: 5),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：没有效果

```dart
// 问题：ClipRect 默认裁剪到子组件边界，看起来没有效果
ClipRect(
  child: Container(width: 100, height: 100, color: Colors.blue),
)

// 解决：子组件超出边界时才有意义
SizedBox(
  width: 50,
  height: 50,
  child: ClipRect(
    child: Container(width: 100, height: 100, color: Colors.blue),
  ),
)
```

### 问题2：与其他裁剪组件的选择

```dart
// 圆角 → ClipRRect
ClipRRect(
  borderRadius: BorderRadius.circular(8),
  child: Container(...),
)

// 圆形 → ClipOval
ClipOval(child: Container(...))

// 矩形 → ClipRect
ClipRect(child: Container(...))
```

## 5. ClipRect vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **ClipRect** | 矩形裁剪 | 最基础，可自定义区域 |
| **ClipRRect** | 圆角矩形裁剪 | 常用于卡片 |
| **ClipOval** | 椭圆裁剪 | 圆形头像 |
| **ClipPath** | 自定义路径裁剪 | 任意形状 |

### 选择建议

```dart
// 需要矩形裁剪 → ClipRect
ClipRect(child: ...)

// 需要圆角 → ClipRRect
ClipRRect(borderRadius: BorderRadius.circular(8), child: ...)

// 需要圆形 → ClipOval
ClipOval(child: ...)

// 需要自定义形状 → ClipPath
ClipPath(clipper: MyClipper(), child: ...)
```

## 6. 最佳实践

### 6.1 封装可复用裁剪组件

```dart
class ClipFactor extends StatelessWidget {
  final Widget child;
  final double? widthFactor;
  final double? heightFactor;
  final Alignment alignment;

  const ClipFactor({
    required this.child,
    this.widthFactor,
    this.heightFactor,
    this.alignment = Alignment.center,
  });

  @override
  Widget build(BuildContext context) {
    return ClipRect(
      child: Align(
        alignment: alignment,
        widthFactor: widthFactor,
        heightFactor: heightFactor,
        child: child,
      ),
    );
  }
}
```

### 6.2 性能考虑

```dart
// 只在需要时使用裁剪
// 裁剪有性能开销

// 不需要时避免使用
Container(
  width: 100,
  height: 100,
  color: Colors.blue,
)

// 需要时再使用
SizedBox(
  width: 100,
  height: 100,
  child: ClipRect(
    child: Container(width: 200, height: 200, color: Colors.blue),
  ),
)
```

## 总结

ClipRect 是 Flutter 中最基础的裁剪组件：

**核心要点**：
1. 矩形边界裁剪
2. 配合 Align 实现比例裁剪
3. 可自定义裁剪区域

**最佳实践**：
1. 子组件超出边界时使用
2. 配合 heightFactor/widthFactor 实现动画
3. 需要圆角用 ClipRRect

**常见场景**：
- 内容溢出裁剪
- 展开收起动画
- 进度条
- 图片裁剪

---

*最后更新: 2026-02-19*

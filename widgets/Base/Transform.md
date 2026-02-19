# Flutter Transform 组件用法详解

## 简介

Transform 是 Flutter 中用于对子组件进行变换的组件。它支持平移、旋转、缩放、倾斜等矩阵变换操作。

## 1. 初级用法

### 1.1 基本概念

Transform 的作用：
- 平移（translate）
- 旋转（rotate）
- 缩放（scale）
- 倾斜（skew）
- 自定义矩阵变换

### 1.2 基本语法

```dart
Transform(
  transform: Matrix4.rotationZ(0.1),
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
| `transform` | `Matrix4` | 变换矩阵 |
| `origin` | `Offset?` | 变换原点 |
| `alignment` | `AlignmentGeometry?` | 对齐方式 |
| `transformHitTests` | `bool` | 是否变换点击测试 |
| `filterQuality` | `FilterQuality?` | 滤镜质量 |
| `child` | `Widget?` | 子组件 |

### 1.4 常用构造函数

| 构造函数 | 说明 |
|------|------|
| `Transform()` | 自定义矩阵 |
| `Transform.rotate()` | 旋转 |
| `Transform.scale()` | 缩放 |
| `Transform.translate()` | 平移 |
| `Transform.flip()` | 翻转 |

### 1.5 基础示例

#### 旋转

```dart
Transform.rotate(
  angle: 0.5,  // 弧度
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
)
```

#### 缩放

```dart
Transform.scale(
  scale: 1.5,
  child: Container(
    width: 100,
    height: 100,
    color: Colors.green,
  ),
)
```

#### 平移

```dart
Transform.translate(
  offset: Offset(50, 50),
  child: Container(
    width: 100,
    height: 100,
    color: Colors.red,
  ),
)
```

## 2. 中级用法

### 2.1 旋转原点

```dart
Transform.rotate(
  angle: 0.5,
  origin: Offset(50, 50),  // 旋转中心点
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 2.2 对齐方式

```dart
Transform.rotate(
  angle: 0.3,
  alignment: Alignment.bottomRight,  // 以右下角为中心旋转
  child: Container(
    width: 100,
    height: 100,
    color: Colors.orange,
  ),
)
```

### 2.3 组合变换

```dart
Transform(
  transform: Matrix4.identity()
    ..translate(50.0, 50.0)  // 平移
    ..rotateZ(0.3)           // 旋转
    ..scale(1.2),            // 缩放
  child: Container(
    width: 100,
    height: 100,
    color: Colors.purple,
  ),
)
```

### 2.4 常见布局场景

#### 卡片倾斜效果

```dart
Transform(
  transform: Matrix4.identity()
    ..setEntry(3, 2, 0.001)  // 透视效果
    ..rotateY(0.1),
  alignment: Alignment.center,
  child: Card(
    child: Container(
      width: 200,
      height: 150,
      child: Center(child: Text('倾斜卡片')),
    ),
  ),
)
```

#### 翻转效果

```dart
Transform.flip(
  flipX: true,  // 水平翻转
  child: Icon(Icons.arrow_forward),
)

Transform.flip(
  flipY: true,  // 垂直翻转
  child: Icon(Icons.arrow_upward),
)
```

#### 3D 旋转

```dart
Transform(
  transform: Matrix4.identity()
    ..setEntry(3, 2, 0.001)  // 透视
    ..rotateX(0.3),
  alignment: Alignment.center,
  child: Container(
    width: 150,
    height: 100,
    color: Colors.blue,
    child: Center(child: Text('3D旋转')),
  ),
)
```

## 3. 高级用法

### 3.1 AnimatedTransform（自定义动画）

```dart
class AnimatedTransformExample extends StatefulWidget {
  @override
  State<AnimatedTransformExample> createState() => _AnimatedTransformExampleState();
}

class _AnimatedTransformExampleState extends State<AnimatedTransformExample>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 2),
      vsync: this,
    )..repeat();
    _animation = CurvedAnimation(parent: _controller, curve: Curves.easeInOut);
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
        return Transform.rotate(
          angle: _animation.value * 2 * pi,
          child: Container(
            width: 100,
            height: 100,
            color: Colors.blue,
          ),
        );
      },
    );
  }
}
```

### 3.2 手势控制变换

```dart
class GestureTransform extends StatefulWidget {
  @override
  State<GestureTransform> createState() => _GestureTransformState();
}

class _GestureTransformState extends State<GestureTransform> {
  double _rotation = 0;
  double _scale = 1.0;
  Offset _position = Offset.zero;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanUpdate: (details) {
        setState(() {
          _position += details.delta;
        });
      },
      onScaleUpdate: (details) {
        setState(() {
          _rotation = details.rotation;
          _scale = details.scale;
        });
      },
      child: Transform(
        transform: Matrix4.identity()
          ..translate(_position.dx, _position.dy)
          ..rotateZ(_rotation)
          ..scale(_scale),
        alignment: Alignment.center,
        child: Container(
          width: 150,
          height: 150,
          color: Colors.blue,
          child: Center(child: Text('拖动/缩放/旋转')),
        ),
      ),
    );
  }
}
```

### 3.3 倾斜效果

```dart
Transform(
  transform: Matrix4.skewX(0.3),  // X 轴倾斜
  child: Container(
    width: 100,
    height: 100,
    color: Colors.green,
  ),
)

Transform(
  transform: Matrix4.skewY(0.3),  // Y 轴倾斜
  child: Container(
    width: 100,
    height: 100,
    color: Colors.red,
  ),
)

Transform(
  transform: Matrix4.skew(0.3, 0.2),  // 双向倾斜
  child: Container(
    width: 100,
    height: 100,
    color: Colors.orange,
  ),
)
```

### 3.4 组合动画

```dart
class ComplexTransformAnimation extends StatefulWidget {
  @override
  State<ComplexTransformAnimation> createState() => _ComplexTransformAnimationState();
}

class _ComplexTransformAnimationState extends State<ComplexTransformAnimation>
    with TickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _rotation;
  late Animation<double> _scale;
  late Animation<Offset> _translation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 3),
      vsync: this,
    )..repeat(reverse: true);

    _rotation = Tween<double>(begin: 0, end: 2 * pi).animate(
      CurvedAnimation(parent: _controller, curve: Interval(0.0, 0.5)),
    );

    _scale = Tween<double>(begin: 1.0, end: 1.5).animate(
      CurvedAnimation(parent: _controller, curve: Interval(0.3, 0.8)),
    );

    _translation = Tween<Offset>(
      begin: Offset.zero,
      end: Offset(50, 50),
    ).animate(
      CurvedAnimation(parent: _controller, curve: Interval(0.5, 1.0)),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _controller,
      builder: (context, child) {
        return Transform(
          transform: Matrix4.identity()
            ..translate(_translation.value.dx, _translation.value.dy)
            ..rotateZ(_rotation.value)
            ..scale(_scale.value),
          alignment: Alignment.center,
          child: Container(
            width: 100,
            height: 100,
            color: Colors.blue,
          ),
        );
      },
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：变换不影响布局

```dart
// Transform 只影响绘制，不影响布局
// 其他组件仍按原始位置计算

Container(
  width: 100,
  height: 100,
  color: Colors.grey[200],
  child: Transform.translate(
    offset: Offset(50, 50),
    child: Container(
      width: 50,
      height: 50,
      color: Colors.red,
      // 这个容器仍然占用 50x50 的布局空间
    ),
  ),
)
```

### 问题2：点击区域不变

```dart
// 问题：变换后点击区域仍按原位置
Transform.translate(
  offset: Offset(100, 100),
  child: ElevatedButton(
    onPressed: () {},
    child: Text('按钮'),
    // 点击测试区域仍在原位置
  ),
)

// 解决：使用 transformHitTests
Transform.translate(
  offset: Offset(100, 100),
  transformHitTests: true,  // 变换点击测试
  child: ElevatedButton(
    onPressed: () {},
    child: Text('按钮'),
  ),
)
```

### 问题3：旋转单位混淆

```dart
// Transform.rotate 使用弧度，不是角度

// 角度转弧度
import 'dart:math';
final radians = degrees * pi / 180;

// 示例：旋转 45 度
Transform.rotate(
  angle: 45 * pi / 180,  // 或 pi / 4
  child: Container(...),
)
```

## 5. Transform vs 其他变换方式对比

| 方式 | 用途 | 特点 |
|------|------|------|
| **Transform** | 矩阵变换 | 灵活，不影响布局 |
| **RotatedBox** | 旋转 | 影响布局 |
| **FittedBox** | 自适应缩放 | 基于约束 |
| **ScaleTransition** | 动画缩放 | 配合动画 |

### 选择建议

```dart
// 不影响布局的变换 → Transform
Transform.rotate(angle: 0.3, child: ...)

// 需要影响布局 → RotatedBox
RotatedBox(quarterTurns: 1, child: ...)

// 自适应缩放 → FittedBox
FittedBox(child: ...)

// 动画场景 → 使用 AnimationController + Transform
AnimatedBuilder(
  animation: animation,
  builder: (context, child) => Transform.rotate(angle: animation.value, child: child),
)
```

## 6. 最佳实践

### 6.1 封装常用变换

```dart
class ShakeTransform extends StatelessWidget {
  final Widget child;
  final double intensity;

  const ShakeTransform({
    required this.child,
    this.intensity = 5,
  });

  @override
  Widget build(BuildContext context) {
    return Transform.translate(
      offset: Offset(
        Random().nextDouble() * intensity * 2 - intensity,
        0,
      ),
      child: child,
    );
  }
}
```

### 6.2 3D 变换辅助

```dart
class Transform3D extends StatelessWidget {
  final Widget child;
  final double rotateX;
  final double rotateY;
  final double rotateZ;
  final double perspective;

  const Transform3D({
    required this.child,
    this.rotateX = 0,
    this.rotateY = 0,
    this.rotateZ = 0,
    this.perspective = 0.001,
  });

  @override
  Widget build(BuildContext context) {
    return Transform(
      transform: Matrix4.identity()
        ..setEntry(3, 2, perspective)
        ..rotateX(rotateX)
        ..rotateY(rotateY)
        ..rotateZ(rotateZ),
      alignment: Alignment.center,
      child: child,
    );
  }
}
```

### 6.3 性能优化

```dart
// 复杂变换使用 RepaintBoundary 隔离重绘
RepaintBoundary(
  child: Transform(
    transform: complexMatrix,
    child: ComplexWidget(),
  ),
)
```

## 总结

Transform 是 Flutter 中实现组件变换的核心组件：

**核心要点**：
1. Matrix4 定义变换矩阵
2. 变换不影响布局
3. 可设置变换原点和对齐方式

**最佳实践**：
1. 使用便捷构造函数简化代码
2. 需要变换点击测试时设置 transformHitTests
3. 动画场景配合 AnimatedBuilder

**常见场景**：
- 旋转动画
- 缩放效果
- 卡片倾斜
- 3D 变换

---

*最后更新: 2026-02-19*

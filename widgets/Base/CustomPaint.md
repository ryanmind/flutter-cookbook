# Flutter CustomPaint 组件用法详解

## 简介

CustomPaint 是 Flutter 中用于自定义绘制的组件。它允许你通过 CustomPainter 在画布上绘制任意图形，是实现复杂自定义 UI 的核心组件。

## 1. 初级用法

### 1.1 基本概念

CustomPaint 的作用：
- 提供画布（Canvas）进行自定义绘制
- 通过 CustomPainter 定义绘制逻辑
- 可绘制路径、图形、文字、图片等

### 1.2 基本语法

```dart
CustomPaint(
  painter: MyPainter(),
  size: Size(200, 200),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `painter` | `CustomPainter?` | 背景绘制器 |
| `foregroundPainter` | `CustomPainter?` | 前景绘制器 |
| `size` | `Size` | 画布尺寸 |
| `child` | `Widget?` | 子组件 |

### 1.4 CustomPainter 核心方法

| 方法 | 说明 |
|------|------|
| `paint(Canvas, Size)` | 绘制逻辑 |
| `shouldRepaint(covariant CustomPainter)` | 是否需要重绘 |

### 1.5 基础示例

#### 绘制圆形

```dart
class CirclePainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.blue
      ..style = PaintingStyle.fill;
    
    canvas.drawCircle(
      Offset(size.width / 2, size.height / 2),
      size.width / 2,
      paint,
    );
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}

// 使用
CustomPaint(
  painter: CirclePainter(),
  size: Size(100, 100),
)
```

#### 绘制矩形

```dart
class RectPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.red
      ..style = PaintingStyle.fill;
    
    canvas.drawRect(
      Rect.fromLTWH(0, 0, size.width, size.height),
      paint,
    );
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

## 2. 中级用法

### 2.1 Paint 画笔配置

```dart
final paint = Paint()
  ..color = Colors.blue                    // 颜色
  ..style = PaintingStyle.fill             // 填充或描边
  ..strokeWidth = 2.0                      // 描边宽度
  ..strokeCap = StrokeCap.round            // 线帽样式
  ..strokeJoin = StrokeJoin.round          // 连接样式
  ..shader = LinearGradient(...).createShader(rect)  // 渐变
  ..maskFilter = MaskFilter.blur(BlurStyle.normal, 5)  // 模糊
  ..colorFilter = ColorFilter.mode(Colors.red, BlendMode.srcIn)  // 颜色滤镜
  ..isAntiAlias = true;                    // 抗锯齿
```

### 2.2 常见绘制操作

#### 绘制线条

```dart
class LinePainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.black
      ..strokeWidth = 2
      ..style = PaintingStyle.stroke;
    
    canvas.drawLine(
      Offset(0, 0),
      Offset(size.width, size.height),
      paint,
    );
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

#### 绘制路径

```dart
class PathPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.green
      ..style = PaintingStyle.stroke
      ..strokeWidth = 3;
    
    final path = Path()
      ..moveTo(0, size.height / 2)
      ..lineTo(size.width / 4, 0)
      ..lineTo(size.width * 3 / 4, size.height)
      ..lineTo(size.width, size.height / 2);
    
    canvas.drawPath(path, paint);
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

#### 绘制文字

```dart
class TextPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final textPainter = TextPainter(
      text: TextSpan(
        text: 'Hello',
        style: TextStyle(
          color: Colors.black,
          fontSize: 24,
        ),
      ),
      textDirection: TextDirection.ltr,
    )..layout();
    
    textPainter.paint(
      canvas,
      Offset(
        (size.width - textPainter.width) / 2,
        (size.height - textPainter.height) / 2,
      ),
    );
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

### 2.3 常见布局场景

#### 进度环

```dart
class ProgressRingPainter extends CustomPainter {
  final double progress;
  final Color color;
  final double strokeWidth;

  ProgressRingPainter({
    required this.progress,
    this.color = Colors.blue,
    this.strokeWidth = 8,
  });

  @override
  void paint(Canvas canvas, Size size) {
    final center = Offset(size.width / 2, size.height / 2);
    final radius = (size.width - strokeWidth) / 2;
    
    // 背景圆
    final bgPaint = Paint()
      ..color = Colors.grey[200]!
      ..style = PaintingStyle.stroke
      ..strokeWidth = strokeWidth;
    
    canvas.drawCircle(center, radius, bgPaint);
    
    // 进度圆
    final progressPaint = Paint()
      ..color = color
      ..style = PaintingStyle.stroke
      ..strokeWidth = strokeWidth
      ..strokeCap = StrokeCap.round;
    
    canvas.drawArc(
      Rect.fromCircle(center: center, radius: radius),
      -pi / 2,
      2 * pi * progress,
      false,
      progressPaint,
    );
  }

  @override
  bool shouldRepaint(ProgressRingPainter old) => progress != old.progress;
}

// 使用
CustomPaint(
  painter: ProgressRingPainter(progress: 0.7),
  size: Size(100, 100),
)
```

#### 图表

```dart
class BarChartPainter extends CustomPainter {
  final List<double> values;
  final Color color;

  BarChartPainter({required this.values, this.color = Colors.blue});

  @override
  void paint(Canvas canvas, Size size) {
    final barWidth = size.width / values.length;
    final maxValue = values.reduce((a, b) => a > b ? a : b);
    
    for (var i = 0; i < values.length; i++) {
      final barHeight = (values[i] / maxValue) * size.height;
      final rect = Rect.fromLTWH(
        i * barWidth + 4,
        size.height - barHeight,
        barWidth - 8,
        barHeight,
      );
      
      canvas.drawRect(rect, Paint()..color = color);
    }
  }

  @override
  bool shouldRepaint(BarChartPainter old) => values != old.values;
}
```

## 3. 高级用法

### 3.1 动画绘制

```dart
class AnimatedPainter extends StatefulWidget {
  @override
  State<AnimatedPainter> createState() => _AnimatedPainterState();
}

class _AnimatedPainterState extends State<AnimatedPainter>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 2),
      vsync: this,
    )..repeat();
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
        return CustomPaint(
          painter: SpinnerPainter(_controller.value),
          size: Size(50, 50),
        );
      },
    );
  }
}

class SpinnerPainter extends CustomPainter {
  final double progress;

  SpinnerPainter(this.progress);

  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.blue
      ..style = PaintingStyle.stroke
      ..strokeWidth = 4
      ..strokeCap = StrokeCap.round;
    
    canvas.drawArc(
      Rect.fromLTWH(2, 2, size.width - 4, size.height - 4),
      progress * 2 * pi,
      1.5 * pi,
      false,
      paint,
    );
  }

  @override
  bool shouldRepaint(SpinnerPainter old) => progress != old.progress;
}
```

### 3.2 触摸绘制

```dart
class DrawingCanvas extends StatefulWidget {
  @override
  State<DrawingCanvas> createState() => _DrawingCanvasState();
}

class _DrawingCanvasState extends State<DrawingCanvas> {
  final List<Offset?> _points = [];

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanStart: (details) {
        setState(() {
          _points.add(details.localPosition);
        });
      },
      onPanUpdate: (details) {
        setState(() {
          _points.add(details.localPosition);
        });
      },
      onPanEnd: (details) {
        setState(() {
          _points.add(null);  // 断点
        });
      },
      child: CustomPaint(
        painter: DrawingPainter(_points),
        size: Size.infinite,
      ),
    );
  }
}

class DrawingPainter extends CustomPainter {
  final List<Offset?> points;

  DrawingPainter(this.points);

  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.black
      ..strokeWidth = 3
      ..strokeCap = StrokeCap.round;
    
    for (var i = 0; i < points.length - 1; i++) {
      if (points[i] != null && points[i + 1] != null) {
        canvas.drawLine(points[i]!, points[i + 1]!, paint);
      }
    }
  }

  @override
  bool shouldRepaint(DrawingPainter old) => points != old.points;
}
```

### 3.3 渐变效果

```dart
class GradientPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final rect = Rect.fromLTWH(0, 0, size.width, size.height);
    final gradient = LinearGradient(
      colors: [Colors.red, Colors.yellow, Colors.green],
      begin: Alignment.topLeft,
      end: Alignment.bottomRight,
    );
    
    final paint = Paint()
      ..shader = gradient.createShader(rect);
    
    canvas.drawRect(rect, paint);
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

### 3.4 复杂图形

```dart
class StarPainter extends CustomPainter {
  final Color color;
  final int points;

  StarPainter({this.color = Colors.yellow, this.points = 5});

  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = color
      ..style = PaintingStyle.fill;
    
    final path = Path();
    final center = Offset(size.width / 2, size.height / 2);
    final outerRadius = size.width / 2;
    final innerRadius = outerRadius * 0.4;
    
    for (var i = 0; i < points * 2; i++) {
      final radius = i.isEven ? outerRadius : innerRadius;
      final angle = (i * pi / points) - pi / 2;
      final x = center.dx + radius * cos(angle);
      final y = center.dy + radius * sin(angle);
      
      if (i == 0) {
        path.moveTo(x, y);
      } else {
        path.lineTo(x, y);
      }
    }
    path.close();
    
    canvas.drawPath(path, paint);
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

## 4. 常见问题与解决方案

### 问题1：绘制模糊

```dart
// 问题：绘制的图形边缘模糊
// 解决：确保尺寸正确，使用抗锯齿

final paint = Paint()
  ..isAntiAlias = true;  // 开启抗锯齿
```

### 问题2：坐标系统

```dart
// Canvas 坐标系：
// (0,0) 在左上角
// x 向右增加
// y 向下增加

// 绘制中心点内容
canvas.drawCircle(
  Offset(size.width / 2, size.height / 2),  // 中心点
  50,
  paint,
);
```

### 问题3：性能优化

```dart
// 问题：频繁重绘导致卡顿
// 解决：优化 shouldRepaint

@override
bool shouldRepaint(covariant MyPainter old) {
  // 只有真正需要时才重绘
  return old.value != value;
}
```

## 5. CustomPaint vs 其他绘制方式对比

| 方式 | 用途 | 特点 |
|------|------|------|
| **CustomPaint** | 自定义绘制 | 最灵活，性能好 |
| **Container** | 简单装饰 | 声明式，易用 |
| **DecoratedBox** | 装饰效果 | 比Container更底层 |
| **SvgPicture** | SVG图片 | 矢量图渲染 |

## 6. 最佳实践

### 6.1 封装自定义绘制组件

```dart
class ProgressCircle extends StatelessWidget {
  final double progress;
  final double size;
  final double strokeWidth;
  final Color color;

  const ProgressCircle({
    required this.progress,
    this.size = 100,
    this.strokeWidth = 8,
    this.color = Colors.blue,
  });

  @override
  Widget build(BuildContext context) {
    return CustomPaint(
      painter: ProgressRingPainter(
        progress: progress,
        color: color,
        strokeWidth: strokeWidth,
      ),
      size: Size(size, size),
    );
  }
}
```

### 6.2 使用 const 优化

```dart
class MyPainter extends CustomPainter {
  final double value;
  
  const MyPainter(this.value);
  
  @override
  bool shouldRepaint(MyPainter old) => value != old.value;
}
```

### 6.3 分离绘制逻辑

```dart
// 将绘制逻辑抽取到独立类
class ShapeDrawer {
  static void drawCircle(Canvas canvas, Offset center, double radius, Paint paint) {
    canvas.drawCircle(center, radius, paint);
  }
  
  static void drawRect(Canvas canvas, Rect rect, Paint paint) {
    canvas.drawRect(rect, paint);
  }
}
```

## 总结

CustomPaint 是 Flutter 自定义绘制的核心组件：

**核心要点**：
1. CustomPainter 定义绘制逻辑
2. Paint 配置画笔属性
3. Canvas 提供绘制方法

**最佳实践**：
1. 合理使用 shouldRepaint
2. 封装可复用组件
3. 使用抗锯齿提升质量

**常见场景**：
- 进度环
- 图表
- 签名板
- 自定义图形

---

*最后更新: 2026-02-19*

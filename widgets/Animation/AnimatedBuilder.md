# Flutter AnimatedBuilder 组件用法详解

## 简介

AnimatedBuilder 是 Flutter 中用于构建动画的通用组件。它监听 Animation 对象的变化，在每一帧重建子组件，是实现复杂动画的核心工具。

## 1. 初级用法

### 1.1 基本概念

AnimatedBuilder 的核心作用：
- 监听 Animation 变化
- 每帧调用 builder 重建
- 分离动画逻辑和 UI 构建

### 1.2 基本语法

```dart
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    return Transform.rotate(
      angle: _controller.value * 2 * pi,
      child: child,
    );
  },
  child: Container(width: 100, height: 100, color: Colors.blue),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `animation` | `Listenable` | 监听的动画对象 |
| `builder` | `Widget Function` | 构建函数 |
| `child` | `Widget?` | 静态子组件（优化用） |

### 1.4 基础示例

#### 旋转动画

```dart
class RotationAnimation extends StatefulWidget {
  @override
  State<RotationAnimation> createState() => _RotationAnimationState();
}

class _RotationAnimationState extends State<RotationAnimation>
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
        return Transform.rotate(
          angle: _controller.value * 2 * pi,
          child: child,
        );
      },
      child: Container(
        width: 100,
        height: 100,
        color: Colors.blue,
        child: Center(child: Text('Rotate')),
      ),
    );
  }
}
```

## 2. 中级用法

### 2.1 使用 Tween

```dart
class TweenAnimation extends StatefulWidget {
  @override
  State<TweenAnimation> createState() => _TweenAnimationState();
}

class _TweenAnimationState extends State<TweenAnimation>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    );
    _animation = Tween<double>(begin: 0, end: 200).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeInOut),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedBuilder(
          animation: _animation,
          builder: (context, child) {
            return Container(
              width: _animation.value,
              height: 100,
              color: Colors.green,
            );
          },
        ),
        ElevatedButton(
          onPressed: () {
            if (_controller.status == AnimationStatus.completed) {
              _controller.reverse();
            } else {
              _controller.forward();
            }
          },
          child: Text('Animate'),
        ),
      ],
    );
  }
}
```

### 2.2 多动画监听

```dart
class MultiAnimation extends StatefulWidget {
  @override
  State<MultiAnimation> createState() => _MultiAnimationState();
}

class _MultiAnimationState extends State<MultiAnimation>
    with TickerProviderStateMixin {
  late AnimationController _controller1;
  late AnimationController _controller2;
  late Animation<double> _widthAnimation;
  late Animation<double> _heightAnimation;

  @override
  void initState() {
    super.initState();
    _controller1 = AnimationController(
      duration: Duration(milliseconds: 500),
      vsync: this,
    );
    _controller2 = AnimationController(
      duration: Duration(milliseconds: 800),
      vsync: this,
    );
    
    _widthAnimation = Tween<double>(begin: 100, end: 200).animate(_controller1);
    _heightAnimation = Tween<double>(begin: 100, end: 150).animate(_controller2);
  }

  @override
  void dispose() {
    _controller1.dispose();
    _controller2.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: Listenable.merge([_controller1, _controller2]),
      builder: (context, child) {
        return Container(
          width: _widthAnimation.value,
          height: _heightAnimation.value,
          color: Colors.purple,
        );
      },
    );
  }
}
```

### 2.3 颜色动画

```dart
late Animation<Color?> _colorAnimation;

@override
void initState() {
  super.initState();
  _controller = AnimationController(
    duration: Duration(seconds: 1),
    vsync: this,
  );
  _colorAnimation = ColorTween(
    begin: Colors.blue,
    end: Colors.red,
  ).animate(_controller);
}

AnimatedBuilder(
  animation: _colorAnimation,
  builder: (context, child) {
    return Container(
      width: 100,
      height: 100,
      color: _colorAnimation.value,
    );
  },
)
```

### 2.4 滑动动画

```dart
class SlideAnimation extends StatefulWidget {
  @override
  State<SlideAnimation> createState() => _SlideAnimationState();
}

class _SlideAnimationState extends State<SlideAnimation>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<Offset> _offsetAnimation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 500),
      vsync: this,
    );
    _offsetAnimation = Tween<Offset>(
      begin: Offset(-1, 0),
      end: Offset.zero,
    ).animate(CurvedAnimation(
      parent: _controller,
      curve: Curves.easeOutCubic,
    ));
    
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _offsetAnimation,
      builder: (context, child) {
        return SlideTransition(
          position: _offsetAnimation,
          child: child,
        );
      },
      child: Container(
        width: 200,
        height: 100,
        color: Colors.blue,
        child: Center(child: Text('Slide In')),
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 自定义动画组件

```dart
class ShakingWidget extends StatefulWidget {
  final Widget child;
  final bool shake;

  const ShakingWidget({
    required this.child,
    this.shake = false,
    Key? key,
  }) : super(key: key);

  @override
  State<ShakingWidget> createState() => _ShakingWidgetState();
}

class _ShakingWidgetState extends State<ShakingWidget>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 100),
      vsync: this,
    );
    _animation = Tween<double>(begin: -5, end: 5).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeInOut),
    );
  }

  @override
  void didUpdateWidget(ShakingWidget oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (widget.shake && !oldWidget.shake) {
      _startShaking();
    }
  }

  void _startShaking() async {
    for (int i = 0; i < 5; i++) {
      await _controller.forward();
      await _controller.reverse();
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
        return Transform.translate(
          offset: Offset(_animation.value, 0),
          child: child,
        );
      },
      child: widget.child,
    );
  }
}
```

### 3.2 波浪动画

```dart
class WaveAnimation extends StatefulWidget {
  @override
  State<WaveAnimation> createState() => _WaveAnimationState();
}

class _WaveAnimationState extends State<WaveAnimation>
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
          size: Size(double.infinity, 200),
          painter: _WavePainter(_controller.value),
        );
      },
    );
  }
}

class _WavePainter extends CustomPainter {
  final double animation;

  _WavePainter(this.animation);

  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.blue
      ..style = PaintingStyle.fill;

    final path = Path();
    path.moveTo(0, size.height);

    for (double x = 0; x <= size.width; x++) {
      final y = size.height * 0.6 + 
                20 * sin((x / size.width * 4 * pi) + (animation * 2 * pi));
      path.lineTo(x, y);
    }

    path.lineTo(size.width, size.height);
    path.close();

    canvas.drawPath(path, paint);
  }

  @override
  bool shouldRepaint(_WavePainter oldDelegate) => animation != oldDelegate.animation;
}
```

### 3.3 进度圆环

```dart
class ProgressRing extends StatelessWidget {
  final Animation<double> animation;
  final double size;
  final double strokeWidth;
  final Color color;

  const ProgressRing({
    required this.animation,
    this.size = 100,
    this.strokeWidth = 8,
    this.color = Colors.blue,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: animation,
      builder: (context, child) {
        return SizedBox(
          width: size,
          height: size,
          child: CustomPaint(
            painter: _RingPainter(
              progress: animation.value,
              strokeWidth: strokeWidth,
              color: color,
            ),
          ),
        );
      },
    );
  }
}

class _RingPainter extends CustomPainter {
  final double progress;
  final double strokeWidth;
  final Color color;

  _RingPainter({
    required this.progress,
    required this.strokeWidth,
    required this.color,
  });

  @override
  void paint(Canvas canvas, Size size) {
    final center = Offset(size.width / 2, size.height / 2);
    final radius = (size.width - strokeWidth) / 2;

    // 背景圆
    canvas.drawCircle(
      center,
      radius,
      Paint()
        ..color = Colors.grey[200]!
        ..strokeWidth = strokeWidth
        ..style = PaintingStyle.stroke,
    );

    // 进度圆
    canvas.drawArc(
      Rect.fromCircle(center: center, radius: radius),
      -pi / 2,
      2 * pi * progress,
      false,
      Paint()
        ..color = color
        ..strokeWidth = strokeWidth
        ..style = PaintingStyle.stroke
        ..strokeCap = StrokeCap.round,
    );
  }

  @override
  bool shouldRepaint(_RingPainter oldDelegate) => progress != oldDelegate.progress;
}
```

### 3.4 粒子动画

```dart
class ParticleAnimation extends StatefulWidget {
  @override
  State<ParticleAnimation> createState() => _ParticleAnimationState();
}

class _ParticleAnimationState extends State<ParticleAnimation>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  final List<_Particle> _particles = [];

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 2),
      vsync: this,
    )..repeat();
    
    // 创建粒子
    for (int i = 0; i < 50; i++) {
      _particles.add(_Particle());
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
      animation: _controller,
      builder: (context, child) {
        return CustomPaint(
          size: Size(300, 300),
          painter: _ParticlePainter(_particles, _controller.value),
        );
      },
    );
  }
}

class _Particle {
  final double x = Random().nextDouble();
  final double y = Random().nextDouble();
  final double size = Random().nextDouble() * 5 + 2;
  final double speed = Random().nextDouble() * 0.5 + 0.5;
}

class _ParticlePainter extends CustomPainter {
  final List<_Particle> particles;
  final double animation;

  _ParticlePainter(this.particles, this.animation);

  @override
  void paint(Canvas canvas, Size size) {
    for (var particle in particles) {
      final y = (particle.y + animation * particle.speed) % 1.0;
      canvas.drawCircle(
        Offset(particle.x * size.width, y * size.height),
        particle.size,
        Paint()..color = Colors.blue.withValues(alpha: 0.7),
      );
    }
  }

  @override
  bool shouldRepaint(_ParticlePainter oldDelegate) => true;
}
```

## 4. 常见问题与解决方案

### 问题1：性能问题

```dart
// 使用 child 参数避免不必要的重建
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    return Transform.rotate(
      angle: _controller.value * 2 * pi,
      child: child,  // 静态内容不会重建
    );
  },
  child: Container(...),  // 只构建一次
)
```

### 问题2：动画完成回调

```dart
_controller.addStatusListener((status) {
  if (status == AnimationStatus.completed) {
    // 动画完成
  } else if (status == AnimationStatus.dismissed) {
    // 动画回到起点
  }
});
```

### 问题3：多个动画同步

```dart
// 使用 Interval 控制动画时序
final animation1 = Tween<double>(begin: 0, end: 1).animate(
  CurvedAnimation(
    parent: _controller,
    curve: Interval(0.0, 0.5),  // 前半段
  ),
);

final animation2 = Tween<double>(begin: 0, end: 1).animate(
  CurvedAnimation(
    parent: _controller,
    curve: Interval(0.5, 1.0),  // 后半段
  ),
);
```

## 5. AnimatedBuilder vs 其他动画方式

| 方式 | 用途 | 特点 |
|------|------|------|
| **AnimatedBuilder** | 自定义动画 | 最灵活 |
| **AnimatedWidget** | 封装动画 | 可复用 |
| **TweenAnimationBuilder** | 单次动画 | 简单 |
| **AnimatedContainer** | 属性动画 | 便捷 |

### 选择建议

```dart
// 复杂自定义动画 → AnimatedBuilder
AnimatedBuilder(animation: _controller, builder: ...)

// 简单单值动画 → TweenAnimationBuilder
TweenAnimationBuilder(tween: ..., builder: ...)

// 容器属性动画 → AnimatedContainer
AnimatedContainer(width: _width, ...)
```

## 6. 最佳实践

### 6.1 使用 child 优化

```dart
// ✅ 好：静态内容使用 child
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) => Transform.scale(
    scale: _controller.value,
    child: child,
  ),
  child: const Text('Static Content'),
)

// ❌ 差：所有内容都在 builder 中
AnimatedBuilder(
  animation: _controller,
  builder: (context, _) => Transform.scale(
    scale: _controller.value,
    child: const Text('Static Content'),  // 每帧都创建
  ),
)
```

### 6.2 封装动画 Widget

```dart
class FadeInWidget extends StatefulWidget {
  final Widget child;
  final Duration duration;

  const FadeInWidget({
    required this.child,
    this.duration = const Duration(milliseconds: 300),
    Key? key,
  }) : super(key: key);

  @override
  State<FadeInWidget> createState() => _FadeInWidgetState();
}

class _FadeInWidgetState extends State<FadeInWidget>
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
    return AnimatedBuilder(
      animation: _animation,
      builder: (context, child) => Opacity(
        opacity: _animation.value,
        child: child,
      ),
      child: widget.child,
    );
  }
}
```

### 6.3 使用 RepaintBoundary

```dart
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    return RepaintBoundary(
      child: Transform.rotate(
        angle: _controller.value * 2 * pi,
        child: child,
      ),
    );
  },
  child: ComplexWidget(),
)
```

## 总结

AnimatedBuilder 是实现自定义动画的核心工具：

**核心要点**：
1. 监听 Animation 对象变化
2. 每帧调用 builder 重建
3. 使用 child 参数优化性能

**最佳实践**：
1. 静态内容使用 child
2. 封装可复用动画组件
3. 使用 RepaintBoundary 隔离重绘

**常见场景**：
- 自定义过渡动画
- 复杂动画效果
- 粒子/波浪动画
- 进度指示器

---

*最后更新: 2026-02-19*

# Flutter 动画系统 - 初学者指南

## 目录
1. [动画基础概念](#动画基础概念)
2. [隐式动画组件](#隐式动画组件)
3. [显式动画](#显式动画)
4. [Hero 动画](#hero-动画)
5. [自定义动画](#自定义动画)
6. [动画曲线](#动画曲线)

## 动画基础概念

### 动画三要素

```dart
// 1. Animation - 动画值（0.0 到 1.0）
Animation<double> animation;

// 2. AnimationController - 控制动画
AnimationController controller = AnimationController(
  duration: Duration(seconds: 1),
  vsync: this,
);

// 3. CurvedAnimation - 动画曲线
CurvedAnimation curve = CurvedAnimation(
  parent: controller,
  curve: Curves.easeInOut,
);
```

### TickerProviderStateMixin

```dart
class _MyWidgetState extends State<MyWidget> with TickerProviderStateMixin {
  late AnimationController _controller;
  
  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,  // 需要 TickerProviderStateMixin
    );
  }
  
  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
}
```

## 隐式动画组件

隐式动画组件会自动处理动画，无需手动管理 AnimationController。

### AnimatedContainer

```dart
class AnimatedContainerDemo extends StatefulWidget {
  @override
  _AnimatedContainerDemoState createState() => _AnimatedContainerDemoState();
}

class _AnimatedContainerDemoState extends State<AnimatedContainerDemo> {
  bool _expanded = false;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: GestureDetector(
          onTap: () => setState(() => _expanded = !_expanded),
          child: AnimatedContainer(
            duration: Duration(milliseconds: 300),
            curve: Curves.easeInOut,
            width: _expanded ? 200 : 100,
            height: _expanded ? 200 : 100,
            decoration: BoxDecoration(
              color: _expanded ? Colors.blue : Colors.red,
              borderRadius: BorderRadius.circular(_expanded ? 100 : 8),
            ),
            child: Center(
              child: Text(_expanded ? 'Expanded' : 'Tap'),
            ),
          ),
        ),
      ),
    );
  }
}
```

### AnimatedOpacity

```dart
class FadeDemo extends StatefulWidget {
  @override
  _FadeDemoState createState() => _FadeDemoState();
}

class _FadeDemoState extends State<FadeDemo> {
  double _opacity = 1.0;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            AnimatedOpacity(
              opacity: _opacity,
              duration: Duration(milliseconds: 500),
              child: FlutterLogo(size: 100),
            ),
            ElevatedButton(
              onPressed: () => setState(() => _opacity = _opacity == 0 ? 1 : 0),
              child: Text('Toggle'),
            ),
          ],
        ),
      ),
    );
  }
}
```

### AnimatedAlign

```dart
class AlignDemo extends StatefulWidget {
  @override
  _AlignDemoState createState() => _AlignDemoState();
}

class _AlignDemoState extends State<AlignDemo> {
  Alignment _alignment = Alignment.topLeft;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GestureDetector(
        onTap: () => setState(() {
          _alignment = _alignment == Alignment.topLeft
              ? Alignment.bottomRight
              : Alignment.topLeft;
        }),
        child: Container(
          color: Colors.grey[200],
          child: AnimatedAlign(
            alignment: _alignment,
            duration: Duration(milliseconds: 500),
            curve: Curves.easeInOut,
            child: Container(
              width: 80,
              height: 80,
              color: Colors.blue,
            ),
          ),
        ),
      ),
    );
  }
}
```

### AnimatedCrossFade

```dart
class CrossFadeDemo extends StatefulWidget {
  @override
  _CrossFadeDemoState createState() => _CrossFadeDemoState();
}

class _CrossFadeDemoState extends State<CrossFadeDemo> {
  bool _showFirst = true;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            AnimatedCrossFade(
              firstChild: Container(
                width: 200,
                height: 100,
                color: Colors.blue,
                child: Center(child: Text('First')),
              ),
              secondChild: Container(
                width: 200,
                height: 150,
                color: Colors.green,
                child: Center(child: Text('Second')),
              ),
              crossFadeState: _showFirst
                  ? CrossFadeState.showFirst
                  : CrossFadeState.showSecond,
              duration: Duration(milliseconds: 300),
            ),
            ElevatedButton(
              onPressed: () => setState(() => _showFirst = !_showFirst),
              child: Text('Switch'),
            ),
          ],
        ),
      ),
    );
  }
}
```

### AnimatedSwitcher

```dart
class SwitcherDemo extends StatefulWidget {
  @override
  _SwitcherDemoState createState() => _SwitcherDemoState();
}

class _SwitcherDemoState extends State<SwitcherDemo> {
  int _count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            AnimatedSwitcher(
              duration: Duration(milliseconds: 300),
              transitionBuilder: (child, animation) {
                return FadeTransition(
                  opacity: animation,
                  child: SlideTransition(
                    position: Tween<Offset>(
                      begin: Offset(0.0, 0.5),
                      end: Offset.zero,
                    ).animate(animation),
                    child: child,
                  ),
                );
              },
              child: Text(
                '$_count',
                key: ValueKey(_count),
                style: Theme.of(context).textTheme.headlineMedium,
              ),
            ),
            ElevatedButton(
              onPressed: () => setState(() => _count++),
              child: Text('Increment'),
            ),
          ],
        ),
      ),
    );
  }
}
```

### TweenAnimationBuilder

```dart
class TweenBuilderDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: TweenAnimationBuilder<double>(
        tween: Tween(begin: 0, end: 1),
        duration: Duration(seconds: 2),
        builder: (context, value, child) {
          return Opacity(
            opacity: value,
            child: Transform.scale(
              scale: value,
              child: child,
            ),
          );
        },
        child: FlutterLogo(size: 100),
      ),
    );
  }
}
```

## 显式动画

### AnimationController

```dart
class ExplicitAnimationDemo extends StatefulWidget {
  @override
  _ExplicitAnimationDemoState createState() => _ExplicitAnimationDemoState();
}

class _ExplicitAnimationDemoState extends State<ExplicitAnimationDemo>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;
  
  @override
  void initState() {
    super.initState();
    
    _controller = AnimationController(
      duration: Duration(seconds: 2),
      vsync: this,
    );
    
    _animation = CurvedAnimation(
      parent: _controller,
      curve: Curves.easeInOut,
    );
  }
  
  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            FadeTransition(
              opacity: _animation,
              child: FlutterLogo(size: 100),
            ),
            ElevatedButton(
              onPressed: () {
                _controller.forward();
              },
              child: Text('Play'),
            ),
            ElevatedButton(
              onPressed: () {
                _controller.reverse();
              },
              child: Text('Reverse'),
            ),
            ElevatedButton(
              onPressed: () {
                _controller.repeat();
              },
              child: Text('Repeat'),
            ),
            ElevatedButton(
              onPressed: () {
                _controller.stop();
              },
              child: Text('Stop'),
            ),
          ],
        ),
      ),
    );
  }
}
```

### 多动画组合

```dart
class MultiAnimationDemo extends StatefulWidget {
  @override
  _MultiAnimationDemoState createState() => _MultiAnimationDemoState();
}

class _MultiAnimationDemoState extends State<MultiAnimationDemo>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _widthAnimation;
  late Animation<double> _heightAnimation;
  late Animation<Color?> _colorAnimation;
  
  @override
  void initState() {
    super.initState();
    
    _controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    );
    
    _widthAnimation = Tween<double>(begin: 100, end: 200).animate(
      CurvedAnimation(parent: _controller, curve: Interval(0.0, 0.5)),
    );
    
    _heightAnimation = Tween<double>(begin: 100, end: 200).animate(
      CurvedAnimation(parent: _controller, curve: Interval(0.5, 1.0)),
    );
    
    _colorAnimation = ColorTween(begin: Colors.blue, end: Colors.red).animate(
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
    return Scaffold(
      body: Center(
        child: AnimatedBuilder(
          animation: _controller,
          builder: (context, child) {
            return Container(
              width: _widthAnimation.value,
              height: _heightAnimation.value,
              color: _colorAnimation.value,
            );
          },
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          _controller.forward(from: 0);
        },
        child: Icon(Icons.play_arrow),
      ),
    );
  }
}
```

### AnimatedBuilder

```dart
class AnimatedBuilderDemo extends StatefulWidget {
  @override
  _AnimatedBuilderDemoState createState() => _AnimatedBuilderDemoState();
}

class _AnimatedBuilderDemoState extends State<AnimatedBuilderDemo>
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
    return Scaffold(
      body: Center(
        child: AnimatedBuilder(
          animation: _controller,
          builder: (context, child) {
            return Transform.rotate(
              angle: _controller.value * 2 * pi,
              child: child,
            );
          },
          child: FlutterLogo(size: 100),
        ),
      ),
    );
  }
}
```

## Hero 动画

### 基本 Hero 动画

```dart
// 页面 1
class ListPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: ListView.builder(
        itemCount: 20,
        itemBuilder: (context, index) {
          return GestureDetector(
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (_) => DetailPage(index: index),
                ),
              );
            },
            child: Hero(
              tag: 'item-$index',
              child: Container(
                height: 100,
                color: Colors.primaries[index % Colors.primaries.length],
                child: Center(child: Text('Item $index')),
              ),
            ),
          );
        },
      ),
    );
  }
}

// 页面 2
class DetailPage extends StatelessWidget {
  final int index;
  
  const DetailPage({required this.index});
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Hero(
          tag: 'item-$index',
          child: Container(
            width: double.infinity,
            height: 300,
            color: Colors.primaries[index % Colors.primaries.length],
            child: Center(
              child: Text(
                'Detail $index',
                style: TextStyle(fontSize: 32, color: Colors.white),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

### 自定义 Hero 动画

```dart
Hero(
  tag: 'custom-hero',
  flightShuttleBuilder: (flightContext, animation, flightDirection,
      fromHeroContext, toHeroContext) {
    return AnimatedBuilder(
      animation: animation,
      builder: (context, child) {
        return Transform.scale(
          scale: 1.0 + animation.value * 0.2,
          child: Opacity(
            opacity: 1.0 - animation.value * 0.3,
            child: child,
          ),
        );
      },
      child: toHeroContext.widget,
    );
  },
  child: Container(...),
)
```

## 自定义动画

### 自定义 StatefulWidget 动画

```dart
class PulsingCircle extends StatefulWidget {
  final Widget child;
  final Duration duration;
  
  const PulsingCircle({
    required this.child,
    this.duration = const Duration(milliseconds: 1000),
  });
  
  @override
  _PulsingCircleState createState() => _PulsingCircleState();
}

class _PulsingCircleState extends State<PulsingCircle>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;
  
  @override
  void initState() {
    super.initState();
    
    _controller = AnimationController(
      duration: widget.duration,
      vsync: this,
    )..repeat(reverse: true);
    
    _animation = Tween<double>(begin: 0.95, end: 1.05).animate(
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
    return AnimatedBuilder(
      animation: _animation,
      builder: (context, child) {
        return Transform.scale(
          scale: _animation.value,
          child: child,
        );
      },
      child: widget.child,
    );
  }
}
```

### 使用 CustomPainter 动画

```dart
class AnimatedProgress extends StatefulWidget {
  final double progress;
  
  const AnimatedProgress({required this.progress});
  
  @override
  _AnimatedProgressState createState() => _AnimatedProgressState();
}

class _AnimatedProgressState extends State<AnimatedProgress>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;
  
  @override
  void initState() {
    super.initState();
    
    _controller = AnimationController(
      duration: Duration(milliseconds: 500),
      vsync: this,
    );
    
    _animation = Tween<double>(begin: 0, end: widget.progress).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeOut),
    );
    
    _controller.forward();
  }
  
  @override
  void didUpdateWidget(AnimatedProgress oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (oldWidget.progress != widget.progress) {
      _animation = Tween<double>(
        begin: _animation.value,
        end: widget.progress,
      ).animate(
        CurvedAnimation(parent: _controller, curve: Curves.easeOut),
      );
      _controller.forward(from: 0);
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
        return CustomPaint(
          size: Size(100, 100),
          painter: ProgressPainter(progress: _animation.value),
        );
      },
    );
  }
}

class ProgressPainter extends CustomPainter {
  final double progress;
  
  ProgressPainter({required this.progress});
  
  @override
  void paint(Canvas canvas, Size size) {
    final center = Offset(size.width / 2, size.height / 2);
    final radius = size.width / 2 - 5;
    
    // 背景圆
    final bgPaint = Paint()
      ..color = Colors.grey[300]!
      ..style = PaintingStyle.stroke
      ..strokeWidth = 10;
    canvas.drawCircle(center, radius, bgPaint);
    
    // 进度圆弧
    final progressPaint = Paint()
      ..color = Colors.blue
      ..style = PaintingStyle.stroke
      ..strokeWidth = 10
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
  bool shouldRepaint(ProgressPainter oldDelegate) {
    return oldDelegate.progress != progress;
  }
}
```

## 动画曲线

### 常用曲线

```dart
// 线性
Curves.linear

// 缓入缓出
Curves.ease
Curves.easeIn
Curves.easeOut
Curves.easeInOut

// 弹性
Curves.elasticIn
Curves.elasticOut
Curves.elasticInOut

// 弹跳
Curves.bounceIn
Curves.bounceOut
Curves.bounceInOut

// 快速减速
Curves.fastOutSlowIn

// 快速加速
Curves.easeInExpo

// 自定义三次贝塞尔曲线
Curves.fastLinearToSlowEaseIn
Curves.slowMiddle
```

### 自定义曲线

```dart
class CustomCurve extends Curve {
  @override
  double transform(double t) {
    // t 从 0 到 1
    // 返回变换后的值
    return t * t;  // 二次缓动
  }
}

// 使用
CurvedAnimation(
  parent: controller,
  curve: CustomCurve(),
)

// 使用 Cubic 曲线
CurvedAnimation(
  parent: controller,
  curve: Cubic(0.42, 0.0, 0.58, 1.0),  // 自定义贝塞尔
)
```

---

*最后更新: 2026-02-19*

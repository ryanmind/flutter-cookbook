# Animation vs CABasicAnimation 对比学习

本文档记录 Flutter Animation 系统与 UIKit CABasicAnimation 的对比学习要点。

---

## 1. Animation 核心概念

### Animation 是什么

Flutter Animation 系统是一个基于监听器的动画框架，提供了显式动画和隐式动画两种方式。

- 基于 `AnimationController` 控制动画
- 使用 `Tween` 定义值范围
- 支持 `Curve` 曲线
- 支持 `Ticker` 驱动

### Flutter Animation vs Core Animation 概念对比

| Flutter Animation | UIKit Core Animation | 说明 |
|-------------------|---------------------|------|
| `AnimationController` | `CABasicAnimation` | 动画控制器 |
| `Tween` | `fromValue` / `toValue` | 值范围 |
| `CurvedAnimation` | `timingFunction` | 动画曲线 |
| `Ticker` | `CADisplayLink` | 帧驱动 |
| `AnimatedBuilder` | 无直接对应 | 动画构建器 |
| `addListener` | `CAMediaiaTiming` | 值监听 |
| `forward()` | `add(_:forKey:)` | 开始动画 |
| `repeat()` | `repeatCount` | 重复动画 |

---

## 2. 源码分析

### AnimationController

```dart
class AnimationController extends Animation<double> with AnimationEagerListenerMixin {
  AnimationController({
    double value = 0.0,              // 初始值
    this.duration,                   // 持续时间
    this.reverseDuration,            // 反向持续时间
    this.debugLabel,                 // 调试标签
    double? lowerBound = 0.0,        // 下限
    double? upperBound = 1.0,        // 上限
    required TickerProvider vsync,   // Ticker 提供者
    this.animationBehavior = AnimationBehavior.normal,
  });
  
  // 控制方法
  TickerFuture forward({double? from});   // 正向播放
  TickerFuture reverse({double? from});   // 反向播放
  TickerFuture repeat({double? min, double? max, bool reverse = false});  // 重复
  void stop({bool canceled = false});     // 停止
  void reset();                            // 重置
  void dispose();                          // 释放
  
  // 值
  double get value;           // 当前值
  double get velocity;        // 当前速度
}
```

### Tween

```dart
class Tween<T> extends Animatable<T> {
  Tween({
    this.begin,    // 起始值
    this.end,      // 结束值
  });
  
  T lerp(double t);  // 线性插值
  T transform(double t);  // 转换
  
  // 链式组合
  Animatable<T> chain(Animatable<double> parent);
}

// 常用 Tween
IntTween          // 整数
DoubleTween       // 浮点数
ColorTween        // 颜色
SizeTween         // 尺寸
RectTween         // 矩形
BorderRadiusTween // 圆角
TextStyleTween    // 文字样式
```

### Curves

```dart
// 内置曲线
Curves.linear           // 线性
Curves.easeIn           // 渐入
Curves.easeOut          // 渐出
Curves.easeInOut        // 渐入渐出
Curves.easeInCubic      // 三次渐入
Curves.easeOutCubic     // 三次渐出
Curves.elasticOut       // 弹性
Curves.bounceOut        // 弹跳
Curves.fastOutSlowIn    // Material 标准
Curves.decelerate       // 减速
```

---

## 3. 与 CABasicAnimation 详细对比

### 3.1 基础动画

```objc
// Objective-C - CABasicAnimation
CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"position.x"];
animation.fromValue = @100;
animation.toValue = @300;
animation.duration = 1.0;
animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
animation.fillMode = kCAFillModeForwards;
animation.removedOnCompletion = NO;
[layer addAnimation:animation forKey:@"move"];
```

```dart
// Flutter - AnimationController + Tween
class _MyWidgetState extends State<MyWidget> with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    );
    
    _animation = Tween<double>(begin: 100, end: 300).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeInOut),
    );
    
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
}
```

### 3.2 动画曲线

```objc
// Objective-C - CAMediaTimingFunction
// 预设曲线
[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionLinear];
[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseIn];
[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];

// 自定义贝塞尔曲线
[CAMediaTimingFunction functionWithControlPoints:0.4 :0.0 :0.2 :1.0];
```

```dart
// Flutter - Curves
Curves.linear;
Curves.easeIn;
Curves.easeOut;
Curves.easeInOut;

// 自定义贝塞尔曲线
Curves.cubic(0.4, 0.0, 0.2, 1.0);
```

### 3.3 颜色动画

```objc
// Objective-C - CABasicAnimation
CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"backgroundColor"];
animation.fromValue = (__bridge id)[UIColor redColor].CGColor;
animation.toValue = (__bridge id)[UIColor blueColor].CGColor;
animation.duration = 1.0;
[layer addAnimation:animation forKey:@"color"];
```

```dart
// Flutter - ColorTween
Animation<Color?> _colorAnimation = ColorTween(
  begin: Colors.red,
  end: Colors.blue,
).animate(_controller);
```

### 3.4 重复动画

```objc
// Objective-C
animation.repeatCount = HUGE_VALF;  // 无限重复
animation.autoreverses = YES;       // 自动反转
```

```dart
// Flutter
_controller.repeat(reverse: true);  // 重复并反转
```

### 3.5 动画监听

```objc
// Objective-C - CAAnimationDelegate
animation.delegate = self;

- (void)animationDidStart:(CAAnimation *)anim {
    NSLog(@"Animation started");
}

- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag {
    NSLog(@"Animation stopped");
}
```

```dart
// Flutter - addListener + addStatusListener
_controller.addListener(() {
  print('Current value: ${_controller.value}');
});

_controller.addStatusListener((status) {
  switch (status) {
    case AnimationStatus.forward:
      print('Forward animation started');
      break;
    case AnimationStatus.completed:
      print('Animation completed');
      break;
    case AnimationStatus.reverse:
      print('Reverse animation started');
      break;
    case AnimationStatus.dismissed:
      print('Animation dismissed');
      break;
  }
});
```

### 3.6 使用 AnimatedBuilder

```dart
// Flutter - AnimatedBuilder 分离动画和 UI
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    return Transform.translate(
      offset: Offset(_animation.value, 0),
      child: child,
    );
  },
  child: Container(width: 100, height: 100, color: Colors.blue),
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 心跳动画
@interface HeartbeatView : UIView
@property (nonatomic, strong) UIView *heartView;
@end

@implementation HeartbeatView

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        self.heartView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
        self.heartView.center = self.center;
        self.heartView.backgroundColor = [UIColor redColor];
        self.heartView.layer.cornerRadius = 50;
        [self addSubview:self.heartView];
        
        [self startHeartbeat];
    }
    return self;
}

- (void)startHeartbeat {
    CAKeyframeAnimation *animation = [CAKeyframeAnimation animationWithKeyPath:@"transform.scale"];
    animation.values = @[@1.0, @1.2, @1.0];
    animation.keyTimes = @[@0.0, @0.5, @1.0];
    animation.duration = 0.8;
    animation.repeatCount = HUGE_VALF;
    [self.heartView.layer addAnimation:animation forKey:@"heartbeat"];
}
@end
```

### Flutter 实现

```dart
// 心跳动画 - 更灵活
class HeartbeatWidget extends StatefulWidget {
  @override
  _HeartbeatWidgetState createState() => _HeartbeatWidgetState();
}

class _HeartbeatWidgetState extends State<HeartbeatWidget> with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 800),
      vsync: this,
    )..repeat(reverse: true);
    
    _animation = TweenSequence<double>([
      TweenSequenceItem(tween: Tween(begin: 1.0, end: 1.2), weight: 0.5),
      TweenSequenceItem(tween: Tween(begin: 1.2, end: 1.0), weight: 0.5),
    ]).animate(CurvedAnimation(parent: _controller, curve: Curves.easeInOut));
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Center(
      child: AnimatedBuilder(
        animation: _animation,
        builder: (context, child) {
          return Transform.scale(
            scale: _animation.value,
            child: Container(
              width: 100,
              height: 100,
              decoration: BoxDecoration(
                color: Colors.red,
                shape: BoxShape.circle,
              ),
            ),
          );
        },
      ),
    );
  }
}
```

---

## 5. 显式动画 vs 隐式动画

### Flutter 隐式动画（推荐简单场景）

```dart
// AnimatedContainer - 自动处理动画
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  curve: Curves.easeInOut,
  width: isExpanded ? 200 : 100,
  height: isExpanded ? 200 : 100,
  color: isExpanded ? Colors.blue : Colors.red,
  child: Center(child: Text('Implicit')),
)

// AnimatedOpacity
AnimatedOpacity(
  opacity: isVisible ? 1.0 : 0.0,
  duration: Duration(milliseconds: 300),
  child: Text('Fade'),
)

// AnimatedPositioned
AnimatedPositioned(
  duration: Duration(milliseconds: 300),
  left: isRight ? 200 : 0,
  top: 100,
  child: Container(width: 50, height: 50, color: Colors.blue),
)
```

### Flutter 显式动画（复杂场景）

```dart
// AnimationController - 完全控制
AnimationController _controller;
Animation<double> _animation;

// 需要手动管理生命周期
@override
void initState() {
  super.initState();
  _controller = AnimationController(vsync: this, duration: ...);
  _animation = Tween(...).animate(_controller);
}

@override
void dispose() {
  _controller.dispose();
  super.dispose();
}
```

---

## 6. 常见陷阱

### 陷阱1：忘记 dispose

```dart
// ❌ 错误：忘记释放 Controller
class _MyState extends State<MyWidget> with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  
  @override
  void initState() {
    super.initState();
    _controller = AnimationController(vsync: this, duration: Duration(seconds: 1));
    _controller.forward();
  }
  // 忘记 dispose！
}

// ✅ 正确：释放资源
@override
void dispose() {
  _controller.dispose();
  super.dispose();
}
```

### 陷阱2：在 build 中创建 Animation

```dart
// ❌ 错误：每次 build 都创建新动画
@override
Widget build(BuildContext context) {
  final controller = AnimationController(vsync: this, duration: Duration(seconds: 1));
  // 每次重建都会创建新 Controller
}

// ✅ 正确：在 initState 中创建
@override
void initState() {
  super.initState();
  _controller = AnimationController(vsync: this, duration: Duration(seconds: 1));
}
```

### 陷阱3：vsync 缺失

```dart
// ❌ 错误：没有 vsync
AnimationController(duration: Duration(seconds: 1));  // 报错！

// ✅ 正确：提供 vsync
class _MyState extends State<MyWidget> with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  
  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      vsync: this,  // 必需
      duration: Duration(seconds: 1),
    );
  }
}
```

### 陷阱4：动画完成后的值

```objc
// Objective-C - fillMode
animation.fillMode = kCAFillModeForwards;
animation.removedOnCompletion = NO;
```

```dart
// Flutter - 动画完成后值保持
// AnimationController 完成后 value 保持在上限
// 无需额外设置
```

---

## 7. 学习技巧

### 技巧1：多个动画组合

```dart
// 使用 Interval 控制多个动画的时间
final _widthAnimation = Tween<double>(begin: 100, end: 200).animate(
  CurvedAnimation(
    parent: _controller,
    curve: Interval(0.0, 0.5, curve: Curves.easeInOut),  // 前半段
  ),
);

final _heightAnimation = Tween<double>(begin: 100, end: 200).animate(
  CurvedAnimation(
    parent: _controller,
    curve: Interval(0.5, 1.0, curve: Curves.easeInOut),  // 后半段
  ),
);
```

### 技巧2：TweenSequence 组合多个 Tween

```dart
final _animation = TweenSequence<double>([
  TweenSequenceItem(tween: Tween(begin: 0, end: 100), weight: 0.5),
  TweenSequenceItem(tween: Tween(begin: 100, end: 50), weight: 0.3),
  TweenSequenceItem(tween: Tween(begin: 50, end: 200), weight: 0.2),
]).animate(_controller);
```

### 技巧3：使用 AnimatedWidget 简化

```dart
class _AnimatedLogo extends AnimatedWidget {
  const _AnimatedLogo(Animation<double> animation) : super(listenable: animation);

  @override
  Widget build(BuildContext context) {
    final animation = listenable as Animation<double>;
    return Transform.scale(
      scale: animation.value,
      child: FlutterLogo(),
    );
  }
}

// 使用
_AnimatedLogo(_animation)
```

### 技巧4：性能优化

```dart
// AnimatedBuilder 的 child 参数不会被重建
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    return Opacity(
      opacity: _controller.value,
      child: child,  // 这个 child 只构建一次
    );
  },
  child: HeavyWidget(),  // 不会被重复构建
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
CABasicAnimation           → AnimationController + Tween
CAKeyframeAnimation        → TweenSequence
CAMediaTimingFunction      → Curves
CADisplayLink              → Ticker
CAAnimationDelegate        → addStatusListener
fromValue/toValue          → Tween(begin/end)
duration                   → duration
repeatCount                → repeat()
autoreverses               → reverse: true
fillMode                   → 自动处理
CALayer 动画               → Widget 动画
```

### 关键差异

| 方面 | UIKit Core Animation | Flutter Animation |
|------|---------------------|-------------------|
| **目标** | Layer 属性 | Widget 属性 |
| **驱动** | CADisplayLink | Ticker |
| **值范围** | fromValue/toValue | Tween |
| **曲线** | timingFunction | Curves |
| **监听** | Delegate | Listener |
| **隐式动画** | CATransaction | AnimatedXxx |

### 最佳实践

1. **简单场景用隐式动画** - AnimatedContainer、AnimatedOpacity 等
2. **复杂场景用显式动画** - AnimationController + Tween
3. **记得 dispose** - 释放 Controller 资源
4. **使用 AnimatedBuilder** - 分离动画逻辑和 UI

---

*最后更新: 2026-02-24*

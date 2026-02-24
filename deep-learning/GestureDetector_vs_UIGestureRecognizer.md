# GestureDetector vs UIGestureRecognizer 对比学习

本文档记录 Flutter GestureDetector 组件与 UIKit UIGestureRecognizer 的对比学习要点。

---

## 1. GestureDetector 核心概念

### GestureDetector 是什么

GestureDetector 是 Flutter 中用于检测手势的组件，可以识别点击、双击、长按、滑动、缩放等多种手势。

- 无视觉表现，纯手势检测
- 包装子组件，监听其上的手势
- 支持多种手势类型
- 支持手势竞争和自定义行为

### GestureDetector vs UIGestureRecognizer 概念对比

| Flutter GestureDetector | UIKit UIGestureRecognizer | 说明 |
|-------------------------|---------------------------|------|
| `onTap` | `UITapGestureRecognizer` | 单击 |
| `onDoubleTap` | `UITapGestureRecognizer(numberOfTapsRequired: 2)` | 双击 |
| `onLongPress` | `UILongPressGestureRecognizer` | 长按 |
| `onVerticalDragStart/Update/End` | `UIPanGestureRecognizer` | 垂直拖动 |
| `onHorizontalDragStart/Update/End` | `UIPanGestureRecognizer` | 水平拖动 |
| `onScaleStart/Update/End` | `UIPinchGestureRecognizer` + `UIRotationGestureRecognizer` | 缩放旋转 |
| `behavior` | 无直接对应 | 点击区域行为 |

---

## 2. 源码分析

### GestureDetector 类定义

```dart
class GestureDetector extends StatelessWidget {
  const GestureDetector({
    super.key,
    this.child,
    this.onTapDown,          // 手指按下
    this.onTapUp,            // 手指抬起
    this.onTap,              // 点击完成
    this.onTapCancel,        // 点击取消
    this.onSecondaryTap,     // 右键/辅助点击
    this.onDoubleTap,        // 双击
    this.onLongPress,        // 长按开始
    this.onLongPressStart,   // 长按开始（带位置）
    this.onLongPressMoveUpdate,  // 长按移动
    this.onLongPressEnd,     // 长按结束
    this.onVerticalDragStart,    // 垂直拖动开始
    this.onVerticalDragUpdate,   // 垂直拖动更新
    this.onVerticalDragEnd,      // 垂直拖动结束
    this.onHorizontalDragStart,  // 水平拖动开始
    this.onHorizontalDragUpdate, // 水平拖动更新
    this.onHorizontalDragEnd,    // 水平拖动结束
    this.onForcePressStart,  // 3D Touch 开始
    this.onForcePressPeak,   // 3D Touch 峰值
    this.onForcePressEnd,    // 3D Touch 结束
    this.onPanStart,         // 通用拖动开始
    this.onPanUpdate,        // 通用拖动更新
    this.onPanEnd,           // 通用拖动结束
    this.onScaleStart,       // 缩放开始
    this.onScaleUpdate,      // 缩放更新
    this.onScaleEnd,         // 缩放结束
    this.behavior,           // 点击区域行为
    this.excludeFromSemantics = false,
    this.dragStartBehavior = DragStartBehavior.start,
  });
}
```

### 手势竞争机制

```dart
// Flutter 使用 GestureArena 处理手势竞争
// 当多个手势识别器同时存在时，会进行竞争

// 例如：同时设置 onTap 和 onDoubleTap
GestureDetector(
  onTap: () => print('Tap'),
  onDoubleTap: () => print('Double Tap'),
  // 双击时，会等待确认不是单击后才触发
)
```

---

## 3. 与 UIGestureRecognizer 详细对比

### 3.1 点击手势

```objc
// Objective-C - UITapGestureRecognizer
UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleTap:)];
tap.numberOfTapsRequired = 1;
[view addGestureRecognizer:tap];

- (void)handleTap:(UITapGestureRecognizer *)recognizer {
    CGPoint location = [recognizer locationInView:recognizer.view];
    NSLog(@"Tapped at: %@", NSStringFromCGPoint(location));
}
```

```dart
// Flutter - onTap
GestureDetector(
  onTap: () {
    print('Tapped');
  },
  onTapDown: (details) {
    print('Tapped down at: ${details.globalPosition}');
  },
  onTapUp: (details) {
    print('Tapped up at: ${details.globalPosition}');
  },
  child: Container(
    width: 200,
    height: 100,
    color: Colors.blue,
    child: Center(child: Text('Tap me')),
  ),
)
```

### 3.2 双击手势

```objc
// Objective-C
UITapGestureRecognizer *doubleTap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleDoubleTap:)];
doubleTap.numberOfTapsRequired = 2;
[view addGestureRecognizer:doubleTap];

// 防止单击和双击冲突
UITapGestureRecognizer *singleTap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleSingleTap:)];
singleTap.numberOfTapsRequired = 1;
[singleTap requireGestureRecognizerToFail:doubleTap];  // 等待双击失败
[view addGestureRecognizer:singleTap];
```

```dart
// Flutter - 自动处理冲突
GestureDetector(
  onTap: () => print('Single tap'),
  onDoubleTap: () => print('Double tap'),
  // Flutter 自动等待判断是单击还是双击
  child: Container(...),
)
```

### 3.3 长按手势

```objc
// Objective-C - UILongPressGestureRecognizer
UILongPressGestureRecognizer *longPress = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(handleLongPress:)];
longPress.minimumPressDuration = 0.5;  // 0.5 秒
longPress.minimumPressDuration = 0.5;
[view addGestureRecognizer:longPress];

- (void)handleLongPress:(UILongPressGestureRecognizer *)recognizer {
    if (recognizer.state == UIGestureRecognizerStateBegan) {
        NSLog(@"Long press began");
    } else if (recognizer.state == UIGestureRecognizerStateEnded) {
        NSLog(@"Long press ended");
    }
}
```

```dart
// Flutter - onLongPress
GestureDetector(
  onLongPress: () {
    print('Long press started');
  },
  onLongPressStart: (details) {
    print('Long press started at: ${details.globalPosition}');
  },
  onLongPressMoveUpdate: (details) {
    print('Long press moved: ${details.globalPosition}');
  },
  onLongPressEnd: (details) {
    print('Long press ended at: ${details.globalPosition}');
  },
  child: Container(...),
)
```

### 3.4 拖动手势

```objc
// Objective-C - UIPanGestureRecognizer
UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(handlePan:)];
[view addGestureRecognizer:pan];

- (void)handlePan:(UIPanGestureRecognizer *)recognizer {
    CGPoint translation = [recognizer translationInView:recognizer.view];
    
    if (recognizer.state == UIGestureRecognizerStateBegan) {
        NSLog(@"Pan began");
    } else if (recognizer.state == UIGestureRecognizerStateChanged) {
        NSLog(@"Pan: %@", NSStringFromCGPoint(translation));
        // 移动视图
        recognizer.view.center = CGPointMake(recognizer.view.center.x + translation.x,
                                              recognizer.view.center.y + translation.y);
        [recognizer setTranslation:CGPointZero inView:recognizer.view];
    } else if (recognizer.state == UIGestureRecognizerStateEnded) {
        CGPoint velocity = [recognizer velocityInView:recognizer.view];
        NSLog(@"Pan ended with velocity: %@", NSStringFromCGPoint(velocity));
    }
}
```

```dart
// Flutter - onPan
class DraggableWidget extends StatefulWidget {
  @override
  _DraggableWidgetState createState() => _DraggableWidgetState();
}

class _DraggableWidgetState extends State<DraggableWidget> {
  Offset _position = Offset.zero;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Positioned(
          left: _position.dx,
          top: _position.dy,
          child: GestureDetector(
            onPanStart: (details) {
              print('Pan started');
            },
            onPanUpdate: (details) {
              setState(() {
                _position += details.delta;
              });
            },
            onPanEnd: (details) {
              print('Pan ended with velocity: ${details.velocity}');
            },
            child: Container(
              width: 100,
              height: 100,
              color: Colors.blue,
              child: Center(child: Text('Drag me')),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.5 缩放手势

```objc
// Objective-C - UIPinchGestureRecognizer
UIPinchGestureRecognizer *pinch = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(handlePinch:)];
[view addGestureRecognizer:pinch];

- (void)handlePinch:(UIPinchGestureRecognizer *)recognizer {
    CGFloat scale = recognizer.scale;
    NSLog(@"Scale: %f", scale);
    // 应用缩放
    recognizer.view.transform = CGAffineTransformScale(recognizer.view.transform, scale, scale);
    recognizer.scale = 1;  // 重置
}
```

```dart
// Flutter - onScale
class ScalableWidget extends StatefulWidget {
  @override
  _ScalableWidgetState createState() => _ScalableWidgetState();
}

class _ScalableWidgetState extends State<ScalableWidget> {
  double _scale = 1.0;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onScaleStart: (details) {
        print('Scale started');
      },
      onScaleUpdate: (details) {
        setState(() {
          _scale = details.scale;
        });
      },
      onScaleEnd: (details) {
        print('Scale ended');
      },
      child: Transform.scale(
        scale: _scale,
        child: Container(
          width: 200,
          height: 200,
          color: Colors.blue,
          child: Center(child: Text('Pinch to scale')),
        ),
      ),
    );
  }
}
```

### 3.6 手势冲突处理

```objc
// Objective-C - requireGestureRecognizerToFail:
UITapGestureRecognizer *singleTap = ...;
UITapGestureRecognizer *doubleTap = ...;
[singleTap requireGestureRecognizerToFail:doubleTap];  // 单击等待双击失败
```

```dart
// Flutter - 自动处理竞争
// GestureDetector 内部自动处理大多数冲突

// 如果需要更精细控制，使用 RawGestureDetector
RawGestureDetector(
  gestures: {
    TapGestureRecognizer: GestureRecognizerFactoryWithHandlers<TapGestureRecognizer>(
      () => TapGestureRecognizer(),
      (TapGestureRecognizer instance) {
        instance.onTap = () => print('Tap');
      },
    ),
  },
  child: Container(...),
)
```

---

## 4. behavior 属性详解

### HitTestBehavior 枚举

```dart
// 1. deferToChild（默认）- 只有子组件响应触摸的区域才响应
GestureDetector(
  behavior: HitTestBehavior.deferToChild,
  onTap: () => print('Tap'),
  child: Text('Only text area responds'),  // 只有文字区域可点击
)

// 2. opaque - 整个区域响应触摸（包括透明区域）
GestureDetector(
  behavior: HitTestBehavior.opaque,
  onTap: () => print('Tap'),
  child: Container(
    width: 200,
    height: 100,
    child: Text('Entire area responds'),  // 整个 200x100 区域可点击
  ),
)

// 3. translucent - 整个区域响应，且触摸事件会继续传递
GestureDetector(
  behavior: HitTestBehavior.translucent,
  onTap: () => print('Outer tap'),
  child: GestureDetector(
    onTap: () => print('Inner tap'),  // 两个都会触发
    child: Container(...),
  ),
)
```

### behavior 对比图

```
deferToChild:
┌────────────────────┐
│                    │
│   ┌──────────┐     │
│   │  Text    │ ← 只有这里响应
│   └──────────┘     │
│                    │
└────────────────────┘

opaque:
┌────────────────────┐
│                    │
│   ┌──────────┐     │
│   │  Text    │     │
│   └──────────┘     │
│                    │
└────────────────────┘
← 整个区域响应

translucent:
┌────────────────────┐
│  GestureDetector1  │ ← 响应
│   ┌──────────┐     │
│   │GestureD2 │ ← 响应
│   │  Text    │     │
│   └──────────┘     │
│                    │
└────────────────────┘
```

---

## 5. 完整示例对比

### UIKit 实现

```objc
// 创建一个可拖动、可缩放的视图
@interface TransformableViewController : UIViewController <UIGestureRecognizerDelegate>
@property (nonatomic, strong) UIView *transformableView;
@end

@implementation TransformableViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.transformableView = [[UIView alloc] initWithFrame:CGRectMake(100, 100, 150, 150)];
    self.transformableView.backgroundColor = [UIColor blueColor];
    [self.view addSubview:self.transformableView];
    
    // 拖动
    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(handlePan:)];
    pan.delegate = self;
    [self.transformableView addGestureRecognizer:pan];
    
    // 缩放
    UIPinchGestureRecognizer *pinch = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(handlePinch:)];
    pinch.delegate = self;
    [self.transformableView addGestureRecognizer:pinch];
    
    // 旋转
    UIRotationGestureRecognizer *rotation = [[UIRotationGestureRecognizer alloc] initWithTarget:self action:@selector(handleRotation:)];
    rotation.delegate = self;
    [self.transformableView addGestureRecognizer:rotation];
    
    // 双击重置
    UITapGestureRecognizer *doubleTap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleDoubleTap)];
    doubleTap.numberOfTapsRequired = 2;
    [self.transformableView addGestureRecognizer:doubleTap];
}

- (void)handlePan:(UIPanGestureRecognizer *)recognizer {
    CGPoint translation = [recognizer translationInView:self.view];
    recognizer.view.center = CGPointMake(recognizer.view.center.x + translation.x,
                                          recognizer.view.center.y + translation.y);
    [recognizer setTranslation:CGPointZero inView:self.view];
}

- (void)handlePinch:(UIPinchGestureRecognizer *)recognizer {
    recognizer.view.transform = CGAffineTransformScale(recognizer.view.transform, recognizer.scale, recognizer.scale);
    recognizer.scale = 1;
}

- (void)handleRotation:(UIRotationGestureRecognizer *)recognizer {
    recognizer.view.transform = CGAffineTransformRotate(recognizer.view.transform, recognizer.rotation);
    recognizer.rotation = 0;
}

- (void)handleDoubleTap {
    [UIView animateWithDuration:0.3 animations:^{
        self.transformableView.transform = CGAffineTransformIdentity;
        self.transformableView.center = CGPointMake(self.view.center.x, self.view.center.y);
    }];
}

- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer {
    return YES;  // 允许同时识别多个手势
}

@end
```

### Flutter 实现

```dart
// 创建同样的可变换视图 - 更简洁
class TransformableWidget extends StatefulWidget {
  @override
  _TransformableWidgetState createState() => _TransformableWidgetState();
}

class _TransformableWidgetState extends State<TransformableWidget> {
  Offset _position = Offset(100, 100);
  double _scale = 1.0;
  double _rotation = 0.0;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Positioned(
          left: _position.dx,
          top: _position.dy,
          child: GestureDetector(
            onScaleStart: (details) {
              // 记录初始状态
            },
            onScaleUpdate: (details) {
              setState(() {
                _position += details.focalPointDelta;
                _scale *= details.scale;
                _rotation += details.rotation;
              });
            },
            onDoubleTap: () {
              setState(() {
                _position = Offset(100, 100);
                _scale = 1.0;
                _rotation = 0.0;
              });
            },
            child: Transform(
              transform: Matrix4.identity()
                ..scale(_scale)
                ..rotateZ(_rotation),
              alignment: Alignment.center,
              child: Container(
                width: 150,
                height: 150,
                color: Colors.blue,
                child: Center(
                  child: Text('Drag, Pinch, Rotate'),
                ),
              ),
            ),
          ),
        ),
      ],
    );
  }
}
```

---

## 6. 常见陷阱

### 陷阱1：behavior 默认值

```dart
// ❌ 问题：Container 内的空白区域不响应点击
GestureDetector(
  onTap: () => print('Tap'),
  child: Container(
    width: 200,
    height: 100,
    child: Text('Only text responds'),  // 只有文字区域可点击
  ),
)

// ✅ 解决方案：设置 behavior
GestureDetector(
  behavior: HitTestBehavior.opaque,  // 整个区域响应
  onTap: () => print('Tap'),
  child: Container(
    width: 200,
    height: 100,
    child: Text('Entire area responds'),
  ),
)
```

### 陷阱2：onTap 和 onDoubleTap 冲突

```dart
// 问题：双击时会延迟单击响应
GestureDetector(
  onTap: () => print('Tap'),        // 会延迟约 300ms
  onDoubleTap: () => print('Double'),
  child: Container(...),
)

// 如果不需要双击，移除 onDoubleTap
GestureDetector(
  onTap: () => print('Tap'),  // 立即响应
  child: Container(...),
)
```

### 陷阱3：嵌套 GestureDetector

```dart
// ❌ 问题：内层 GestureDetector 阻止外层
GestureDetector(
  onTap: () => print('Outer'),
  child: GestureDetector(
    onTap: () => print('Inner'),  // 只有 Inner 触发
    child: Container(...),
  ),
)

// ✅ 如果需要都触发，使用 translucent
GestureDetector(
  behavior: HitTestBehavior.translucent,
  onTap: () => print('Outer'),
  child: GestureDetector(
    onTap: () => print('Inner'),
    child: Container(...),
  ),
)
```

### 陷阱4：滚动组件中的手势

```dart
// 问题：GestureDetector 在 ListView 中可能不响应
ListView(
  children: [
    GestureDetector(
      onHorizontalDragUpdate: (details) {},  // 与 ListView 滚动冲突
      child: Container(...),
    ),
  ],
)

// 解决方案：使用特定方向的手势
GestureDetector(
  onVerticalDragUpdate: (details) {},  // 垂直拖动
  // 或排除滚动
)
```

---

## 7. 学习技巧

### 技巧1：使用 Listener 获取原始触摸事件

```dart
// Listener 可以获取最原始的触摸事件
Listener(
  onPointerDown: (event) {
    print('Pointer down at: ${event.position}');
  },
  onPointerMove: (event) {
    print('Pointer move: ${event.delta}');
  },
  onPointerUp: (event) {
    print('Pointer up');
  },
  child: Container(...),
)
```

### 技巧2：手势回调的顺序

```dart
// 点击事件的顺序
GestureDetector(
  onTapDown: (details) => print('1. onTapDown'),
  onTapUp: (details) => print('2. onTapUp'),
  onTap: () => print('3. onTap'),
  onTapCancel: () => print('or onTapCancel'),
  child: Container(...),
)
```

### 技巧3：使用 InkResponse 实现水波纹

```dart
// 带水波纹效果的点击
InkResponse(
  onTap: () => print('Tap'),
  splashColor: Colors.blue.withOpacity(0.3),
  child: Container(
    padding: EdgeInsets.all(16),
    child: Text('Tap me'),
  ),
)

// 或使用 InkWell（矩形水波纹）
InkWell(
  onTap: () => print('Tap'),
  child: Container(...),
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UITapGestureRecognizer      → onTap / onDoubleTap
UILongPressGestureRecognizer → onLongPress
UIPanGestureRecognizer     → onPanStart/Update/End
UIPinchGestureRecognizer    → onScaleStart/Update/End
UIRotationGestureRecognizer → onScaleStart/Update/End (包含旋转)
addGestureRecognizer:       → GestureDetector 包装
requireGestureRecognizerToFail: → 自动处理 / RawGestureDetector
```

### 关键差异

| 方面 | UIKit UIGestureRecognizer | Flutter GestureDetector |
|------|---------------------------|-------------------------|
| **使用方式** | 添加到视图 | 包装子组件 |
| **回调定义** | Target-Action | 闭包 |
| **手势冲突** | requireGestureRecognizerToFail | 自动处理 |
| **点击区域** | 视图 bounds | behavior 控制 |
| **水波纹** | 需要自定义 | InkWell / InkResponse |

### 最佳实践

1. **设置 behavior** - 需要整个区域响应时设置 opaque
2. **避免冲突** - onTap 和 onDoubleTap 同时存在会有延迟
3. **使用 InkWell** - 需要水波纹效果时
4. **Listener 原始事件** - 需要底层触摸事件时

---

*最后更新: 2026-02-24*

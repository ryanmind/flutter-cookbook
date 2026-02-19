# Flutter GestureDetector 组件用法详解

## 简介

GestureDetector 是 Flutter 中用于手势检测的基础组件。它可以包裹任何组件并响应各种手势事件，是实现用户交互的核心工具。

## 1. 初级用法

### 1.1 基本概念

GestureDetector 的作用：
- 检测并响应用户手势
- 支持点击、双击、长按、滑动等手势
- 不影响子组件的视觉效果（透明）

### 1.2 基本语法

```dart
GestureDetector(
  onTap: () {
    print('点击了');
  },
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
    child: Center(child: Text('点击我')),
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `onTap` | `GestureTapCallback?` | 点击回调 |
| `onDoubleTap` | `GestureTapCallback?` | 双击回调 |
| `onLongPress` | `GestureLongPressCallback?` | 长按回调 |
| `onTapDown` | `GestureTapDownCallback?` | 按下回调 |
| `onTapUp` | `GestureTapUpCallback?` | 抬起回调 |
| `onTapCancel` | `GestureTapCancelCallback?` | 取消点击 |
| `onVerticalDragStart` | `GestureDragStartCallback?` | 垂直拖动开始 |
| `onVerticalDragUpdate` | `GestureDragUpdateCallback?` | 垂直拖动更新 |
| `onVerticalDragEnd` | `GestureDragEndCallback?` | 垂直拖动结束 |
| `onHorizontalDragStart` | `GestureDragStartCallback?` | 水平拖动开始 |
| `onHorizontalDragUpdate` | `GestureDragUpdateCallback?` | 水平拖动更新 |
| `onHorizontalDragEnd` | `GestureDragEndCallback?` | 水平拖动结束 |
| `onPanStart` | `GestureDragStartCallback?` | 自由拖动开始 |
| `onPanUpdate` | `GestureDragUpdateCallback?` | 自由拖动更新 |
| `onPanEnd` | `GestureDragEndCallback?` | 自由拖动结束 |
| `onScaleStart` | `GestureScaleStartCallback?` | 缩放开始 |
| `onScaleUpdate` | `GestureScaleUpdateCallback?` | 缩放更新 |
| `onScaleEnd` | `GestureScaleEndCallback?` | 缩放结束 |
| `child` | `Widget?` | 子组件 |
| `behavior` | `HitTestBehavior` | 点击测试行为 |

### 1.4 基础示例

#### 点击事件

```dart
GestureDetector(
  onTap: () {
    print('点击');
  },
  child: Container(
    padding: EdgeInsets.all(16),
    color: Colors.blue,
    child: Text('点击我'),
  ),
)
```

#### 长按事件

```dart
GestureDetector(
  onLongPress: () {
    print('长按');
  },
  child: Container(
    padding: EdgeInsets.all(16),
    color: Colors.green,
    child: Text('长按我'),
  ),
)
```

#### 双击事件

```dart
GestureDetector(
  onDoubleTap: () {
    print('双击');
  },
  child: Container(
    padding: EdgeInsets.all(16),
    color: Colors.orange,
    child: Text('双击我'),
  ),
)
```

## 2. 中级用法

### 2.1 点击测试行为 (behavior)

| 值 | 说明 |
|------|------|
| `deferToChild` | 只响应子组件区域（默认） |
| `opaque` | 响应整个区域，不透明 |
| `translucent` | 响应整个区域，透明 |

```dart
// 问题：Container 无颜色时点击区域小
GestureDetector(
  onTap: () => print('点击'),
  child: Container(
    child: Text('可能点不到'),  // 只有文字区域可点击
  ),
)

// 解决：设置 behavior
GestureDetector(
  behavior: HitTestBehavior.opaque,  // 整个区域可点击
  onTap: () => print('点击'),
  child: Container(
    padding: EdgeInsets.all(20),
    child: Text('整个区域可点击'),
  ),
)
```

### 2.2 拖动手势

#### 垂直拖动

```dart
class VerticalDragExample extends StatefulWidget {
  @override
  State<VerticalDragExample> createState() => _VerticalDragExampleState();
}

class _VerticalDragExampleState extends State<VerticalDragExample> {
  double _top = 100;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Positioned(
          top: _top,
          child: GestureDetector(
            onVerticalDragUpdate: (details) {
              setState(() {
                _top += details.delta.dy;
              });
            },
            child: Container(
              width: 100,
              height: 100,
              color: Colors.blue,
              child: Center(child: Text('上下拖动')),
            ),
          ),
        ),
      ],
    );
  }
}
```

#### 自由拖动 (Pan)

```dart
class PanExample extends StatefulWidget {
  @override
  State<PanExample> createState() => _PanExampleState();
}

class _PanExampleState extends State<PanExample> {
  double _left = 100;
  double _top = 100;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Positioned(
          left: _left,
          top: _top,
          child: GestureDetector(
            onPanUpdate: (details) {
              setState(() {
                _left += details.delta.dx;
                _top += details.delta.dy;
              });
            },
            child: Container(
              width: 80,
              height: 80,
              color: Colors.red,
              child: Center(child: Text('拖动')),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 2.3 缩放手势

```dart
class ScaleExample extends StatefulWidget {
  @override
  State<ScaleExample> createState() => _ScaleExampleState();
}

class _ScaleExampleState extends State<ScaleExample> {
  double _scale = 1.0;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onScaleUpdate: (details) {
        setState(() {
          _scale = details.scale;
        });
      },
      child: Transform.scale(
        scale: _scale,
        child: Container(
          width: 150,
          height: 150,
          color: Colors.purple,
          child: Center(child: Text('双指缩放')),
        ),
      ),
    );
  }
}
```

### 2.4 点击生命周期

```dart
GestureDetector(
  onTapDown: (details) {
    print('按下: ${details.globalPosition}');
  },
  onTapUp: (details) {
    print('抬起: ${details.globalPosition}');
  },
  onTap: () {
    print('点击完成');
  },
  onTapCancel: () {
    print('点击取消（如滑出区域）');
  },
  child: Container(
    padding: EdgeInsets.all(20),
    color: Colors.blue,
    child: Text('查看点击生命周期'),
  ),
)
```

### 2.5 常见场景

#### 自定义按钮

```dart
class CustomButton extends StatelessWidget {
  final String text;
  final VoidCallback? onTap;
  final Color color;

  const CustomButton({
    required this.text,
    this.onTap,
    this.color = Colors.blue,
  });

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onTap,
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
        decoration: BoxDecoration(
          color: color,
          borderRadius: BorderRadius.circular(8),
        ),
        child: Text(
          text,
          style: TextStyle(color: Colors.white),
        ),
      ),
    );
  }
}
```

#### 点击涟漪效果

```dart
// 需要配合 Material 使用
GestureDetector(
  onTap: () {},
  child: Material(
    color: Colors.transparent,
    child: InkWell(
      onTap: () => print('点击'),
      splashColor: Colors.blue.withOpacity(0.3),
      child: Container(
        padding: EdgeInsets.all(16),
        child: Text('涟漪效果'),
      ),
    ),
  ),
)
```

## 3. 高级用法

### 3.1 手势冲突处理

当同时监听多种手势时，可能发生冲突：

```dart
// 问题：onTap 和 onDoubleTap 冲突
GestureDetector(
  onTap: () => print('点击'),
  onDoubleTap: () => print('双击'),  // 会延迟检测
  child: Container(...),
)

// 解决：使用时间差判断
class DoubleTapHandler extends StatefulWidget {
  @override
  State<DoubleTapHandler> createState() => _DoubleTapHandlerState();
}

class _DoubleTapHandlerState extends State<DoubleTapHandler> {
  DateTime? _lastTap;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        final now = DateTime.now();
        if (_lastTap != null && now.difference(_lastTap!) < Duration(milliseconds: 300)) {
          print('双击');
          _lastTap = null;
        } else {
          _lastTap = now;
          Future.delayed(Duration(milliseconds: 300), () {
            if (_lastTap != null) {
              print('单击');
              _lastTap = null;
            }
          });
        }
      },
      child: Container(
        padding: EdgeInsets.all(20),
        color: Colors.blue,
        child: Text('点击或双击'),
      ),
    );
  }
}
```

### 3.2 拖动和滑动冲突

```dart
// 问题：垂直拖动和水平拖动冲突
// 解决：使用 onPan 代替，或使用手势识别器

class DirectionalDragExample extends StatefulWidget {
  @override
  State<DirectionalDragExample> createState() => _DirectionalDragExampleState();
}

class _DirectionalDragExampleState extends State<DirectionalDragExample> {
  Offset _position = Offset.zero;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanUpdate: (details) {
        setState(() {
          _position += details.delta;
        });
      },
      child: Transform.translate(
        offset: _position,
        child: Container(
          width: 100,
          height: 100,
          color: Colors.green,
          child: Center(child: Text('自由拖动')),
        ),
      ),
    );
  }
}
```

### 3.3 手势识别器 (GestureRecognizer)

使用 RawGestureDetector 进行更精细的控制：

```dart
class CustomGestureExample extends StatefulWidget {
  @override
  State<CustomGestureExample> createState() => _CustomGestureExampleState();
}

class _CustomGestureExampleState extends State<CustomGestureExample> {
  late TapGestureRecognizer _tapRecognizer;

  @override
  void initState() {
    super.initState();
    _tapRecognizer = TapGestureRecognizer()
      ..onTapDown = (details) => print('按下')
      ..onTapUp = (details) => print('抬起')
      ..onTap = () => print('点击');
  }

  @override
  void dispose() {
    _tapRecognizer.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return RawGestureDetector(
      gestures: {
        TapGestureRecognizer: GestureRecognizerFactoryWithHandlers<TapGestureRecognizer>(
          () => _tapRecognizer,
          (instance) {
            instance
              ..onTapDown = (details) => print('按下')
              ..onTap = () => print('点击');
          },
        ),
      },
      child: Container(
        width: 100,
        height: 100,
        color: Colors.blue,
        child: Center(child: Text('自定义手势')),
      ),
    );
  }
}
```

### 3.4 滑动方向检测

```dart
class SwipeDetector extends StatefulWidget {
  final Widget child;
  final VoidCallback? onSwipeLeft;
  final VoidCallback? onSwipeRight;
  final VoidCallback? onSwipeUp;
  final VoidCallback? onSwipeDown;

  const SwipeDetector({
    required this.child,
    this.onSwipeLeft,
    this.onSwipeRight,
    this.onSwipeUp,
    this.onSwipeDown,
  });

  @override
  State<SwipeDetector> createState() => _SwipeDetectorState();
}

class _SwipeDetectorState extends State<SwipeDetector> {
  Offset _startPosition = Offset.zero;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanStart: (details) {
        _startPosition = details.globalPosition;
      },
      onPanEnd: (details) {
        final endPosition = details.primaryVelocity ?? 0;
        final dx = endPosition;
        
        if (dx.abs() > 100) {
          if (dx > 0) {
            widget.onSwipeRight?.call();
          } else {
            widget.onSwipeLeft?.call();
          }
        }
      },
      child: widget.child,
    );
  }
}
```

### 3.5 多点触控

```dart
class MultiTouchExample extends StatefulWidget {
  @override
  State<MultiTouchExample> createState() => _MultiTouchExampleState();
}

class _MultiTouchExampleState extends State<MultiTouchExample> {
  int _touchCount = 0;

  @override
  Widget build(BuildContext context) {
    return Listener(
      onPointerDown: (_) => setState(() => _touchCount++),
      onPointerUp: (_) => setState(() => _touchCount--),
      onPointerCancel: (_) => setState(() => _touchCount--),
      child: Container(
        width: 200,
        height: 200,
        color: Colors.blue,
        child: Center(
          child: Text(
            '触控点数: $_touchCount',
            style: TextStyle(color: Colors.white, fontSize: 18),
          ),
        ),
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：点击区域过小

```dart
// 问题：文字区域太小难点击
GestureDetector(
  onTap: () {},
  child: Text('点击'),
)

// 解决：扩大点击区域
GestureDetector(
  behavior: HitTestBehavior.opaque,
  onTap: () {},
  child: Container(
    padding: EdgeInsets.all(20),  // 扩大区域
    child: Text('点击'),
  ),
)
```

### 问题2：GestureDetector 和 ListView 冲突

```dart
// 问题：拖动手势与 ListView 滚动冲突
GestureDetector(
  onVerticalDragUpdate: (details) {},
  child: ListView(...),  // 滚动被拦截
)

// 解决：使用不同的手势或调整层级
ListView(
  children: [
    GestureDetector(
      onHorizontalDragUpdate: (details) {},  // 水平拖动
      child: Container(...),
    ),
  ],
)
```

### 问题3：手势穿透

```dart
// 问题：嵌套的 GestureDetector 都响应
GestureDetector(
  onTap: () => print('外层'),
  child: GestureDetector(
    onTap: () => print('内层'),  // 两个都响应
    child: Container(...),
  ),
)

// 解决：使用 ignorePointer 或 absorbPointer
GestureDetector(
  onTap: () => print('外层'),
  child: AbsorbPointer(  // 吸收点击事件
    child: GestureDetector(
      onTap: () => print('内层'),
      child: Container(...),
    ),
  ),
)
```

## 5. GestureDetector vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **GestureDetector** | 通用手势检测 | 功能全面，透明 |
| **InkWell** | Material 点击 | 有涟漪效果 |
| **InkResponse** | Material 点击 | 可配置涟漪 |
| **TextButton** | 按钮组件 | 自带点击和样式 |
| **Listener** | 底层指针事件 | 更低级别，无手势识别 |

### 选择建议

```dart
// 需要涟漪效果 → 使用 InkWell
InkWell(
  onTap: () {},
  child: Container(...),
)

// 需要复杂手势 → 使用 GestureDetector
GestureDetector(
  onScaleUpdate: (details) {},
  child: Container(...),
)

// 标准按钮 → 使用 TextButton/ElevatedButton
ElevatedButton(
  onPressed: () {},
  child: Text('按钮'),
)
```

## 6. 最佳实践

### 6.1 可点击组件封装

```dart
class Tappable extends StatelessWidget {
  final Widget child;
  final VoidCallback? onTap;
  final VoidCallback? onLongPress;
  final BorderRadius? borderRadius;

  const Tappable({
    required this.child,
    this.onTap,
    this.onLongPress,
    this.borderRadius,
  });

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onTap,
      onLongPress: onLongPress,
      child: Material(
        color: Colors.transparent,
        child: InkWell(
          onTap: onTap,
          onLongPress: onLongPress,
          borderRadius: borderRadius ?? BorderRadius.circular(8),
          child: child,
        ),
      ),
    );
  }
}
```

### 6.2 防抖动点击

```dart
class DebouncedGestureDetector extends StatefulWidget {
  final Widget child;
  final VoidCallback? onTap;
  final Duration debounceTime;

  const DebouncedGestureDetector({
    required this.child,
    this.onTap,
    this.debounceTime = const Duration(milliseconds: 500),
  });

  @override
  State<DebouncedGestureDetector> createState() => _DebouncedGestureDetectorState();
}

class _DebouncedGestureDetectorState extends State<DebouncedGestureDetector> {
  DateTime? _lastTap;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        final now = DateTime.now();
        if (_lastTap == null || now.difference(_lastTap!) > widget.debounceTime) {
          _lastTap = now;
          widget.onTap?.call();
        }
      },
      child: widget.child,
    );
  }
}
```

### 6.3 统一处理手势

```dart
mixin GestureDetectorMixin<T extends StatefulWidget> on State<T> {
  void handleTap() {}
  void handleDoubleTap() {}
  void handleLongPress() {}
}

class MyWidget extends StatefulWidget {
  @override
  State<MyWidget> createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> with GestureDetectorMixin {
  @override
  void handleTap() {
    print('点击');
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: handleTap,
      onDoubleTap: handleDoubleTap,
      onLongPress: handleLongPress,
      child: Container(...),
    );
  }
}
```

## 总结

GestureDetector 是 Flutter 手势处理的核心组件：

**核心要点**：
1. 支持 tap、double tap、long press、drag、scale 等手势
2. behavior 控制点击测试行为
3. 手势冲突需要特殊处理

**最佳实践**：
1. 合理设置 behavior 扩大点击区域
2. 使用 InkWell 获得涟漪效果
3. 复杂手势使用 RawGestureDetector

**常见场景**：
- 自定义按钮
- 拖动组件
- 缩放手势
- 滑动检测

---

*最后更新: 2026-02-19*

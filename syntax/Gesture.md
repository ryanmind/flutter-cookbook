# Flutter 手势处理 - 初学者指南

## 目录
1. [GestureDetector](#gesturedetector)
2. [InkWell 和InkResponse](#inkwell-和inkresponse)
3. [GestureRecognizer](#gesturerecognizer)
4. [自定义手势](#自定义手势)
5. [常见手势场景](#常见手势场景)

## GestureDetector

### 基本点击手势

```dart
GestureDetector(
  onTap: () {
    print('单击');
  },
  onDoubleTap: () {
    print('双击');
  },
  onLongPress: () {
    print('长按');
  },
  onSecondaryTap: () {
    print('右键点击（桌面端）');
  },
  child: Container(
    width: 200,
    height: 100,
    color: Colors.blue,
    child: Center(child: Text('点击我')),
  ),
)
```

### 拖动手势

```dart
class DraggableBox extends StatefulWidget {
  @override
  _DraggableBoxState createState() => _DraggableBoxState();
}

class _DraggableBoxState extends State<DraggableBox> {
  double _x = 0;
  double _y = 0;
  
  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Positioned(
          left: _x,
          top: _y,
          child: GestureDetector(
            onPanStart: (details) {
              print('开始拖动: ${details.globalPosition}');
            },
            onPanUpdate: (details) {
              setState(() {
                _x += details.delta.dx;
                _y += details.delta.dy;
              });
            },
            onPanEnd: (details) {
              print('结束拖动');
            },
            child: Container(
              width: 100,
              height: 100,
              color: Colors.red,
              child: Center(child: Text('拖动我')),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 缩放手势

```dart
class ScaleBox extends StatefulWidget {
  @override
  _ScaleBoxState createState() => _ScaleBoxState();
}

class _ScaleBoxState extends State<ScaleBox> {
  double _scale = 1.0;
  
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onScaleStart: (details) {
        print('开始缩放');
      },
      onScaleUpdate: (details) {
        setState(() {
          _scale = details.scale.clamp(0.5, 3.0);
        });
      },
      onScaleEnd: (details) {
        print('结束缩放');
      },
      child: Center(
        child: Transform.scale(
          scale: _scale,
          child: Container(
            width: 200,
            height: 200,
            color: Colors.blue,
            child: Center(child: Text('双指缩放')),
          ),
        ),
      ),
    );
  }
}
```

### 所有手势回调

```dart
GestureDetector(
  // 点击
  onTapDown: (details) {},      // 手指按下
  onTapUp: (details) {},        // 手指抬起
  onTap: () {},                 // 点击完成
  onTapCancel: () {},           // 点击取消
  onDoubleTap: () {},           // 双击
  onDoubleTapCancel: () {},     // 双击取消
  onSecondaryTap: () {},        // 右键点击
  onSecondaryTapDown: (details) {},
  onSecondaryTapUp: (details) {},
  onSecondaryTapCancel: () {},
  onTertiaryTapDown: (details) {},  // 中键
  onTertiaryTapUp: (details) {},
  onTertiaryTapCancel: () {},
  
  // 长按
  onLongPressDown: (details) {},    // 长按开始
  onLongPressCancel: () {},         // 长按取消
  onLongPress: () {},               // 长按触发
  onLongPressStart: (details) {},   // 长按开始位置
  onLongPressMoveUpdate: (details) {},  // 长按移动
  onLongPressUp: () {},             // 长按抬起
  onLongPressEnd: (details) {},     // 长按结束
  
  // 垂直拖动
  onVerticalDragDown: (details) {},
  onVerticalDragStart: (details) {},
  onVerticalDragUpdate: (details) {},
  onVerticalDragEnd: (details) {},
  onVerticalDragCancel: () {},
  
  // 水平拖动
  onHorizontalDragDown: (details) {},
  onHorizontalDragStart: (details) {},
  onHorizontalDragUpdate: (details) {},
  onHorizontalDragEnd: (details) {},
  onHorizontalDragCancel: () {},
  
  // 任意方向拖动
  onPanDown: (details) {},
  onPanStart: (details) {},
  onPanUpdate: (details) {},
  onPanEnd: (details) {},
  onPanCancel: () {},
  
  // 缩放
  onScaleStart: (details) {},
  onScaleUpdate: (details) {},
  onScaleEnd: (details) {},
  
  // 强制触摸（3D Touch）
  onForcePressStart: (details) {},
  onForcePressPeak: (details) {},
  onForcePressUpdate: (details) {},
  onForcePressEnd: (details) {},
  
  child: Container(...),
)
```

## InkWell 和InkResponse

### InkWell

```dart
InkWell(
  onTap: () {
    print('点击');
  },
  onDoubleTap: () {
    print('双击');
  },
  onLongPress: () {
    print('长按');
  },
  borderRadius: BorderRadius.circular(8),
  child: Container(
    padding: EdgeInsets.all(16),
    child: Text('InkWell 按钮'),
  ),
)
```

### InkResponse

```dart
InkResponse(
  onTap: () {
    print('点击');
  },
  radius: 50,  // 水波纹半径
  containedInkWell: true,  // 限制在边界内
  highlightShape: BoxShape.rectangle,
  customBorder: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(10),
  ),
  child: Container(
    padding: EdgeInsets.all(16),
    child: Text('InkResponse'),
  ),
)
```

### Ink效果容器

```dart
// 使用Ink 或 Material 提供Ink 效果的背景
Material(
  color: Colors.blue,
  borderRadius: BorderRadius.circular(8),
  child: InkWell(
    onTap: () {},
    borderRadius: BorderRadius.circular(8),
    child: Container(
      padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
      child: Text(
        '按钮',
        style: TextStyle(color: Colors.white),
      ),
    ),
  ),
)
```

## GestureRecognizer

### 使用 RawGestureDetector

```dart
class CustomGestureDemo extends StatefulWidget {
  @override
  _CustomGestureDemoState createState() => _CustomGestureDemoState();
}

class _CustomGestureDemoState extends State<CustomGestureDemo> {
  late TapGestureRecognizer _tapRecognizer;
  String _message = '点击测试区域';
  
  @override
  void initState() {
    super.initState();
    _tapRecognizer = TapGestureRecognizer()
      ..onTapDown = (details) {
        setState(() => _message = 'TapDown: ${details.globalPosition}');
      }
      ..onTapUp = (details) {
        setState(() => _message = 'TapUp: ${details.globalPosition}');
      }
      ..onTap = () {
        setState(() => _message = 'Tap!');
      }
      ..onTapCancel = () {
        setState(() => _message = 'Tap Cancelled');
      };
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
            // 配置 recognizer
          },
        ),
      },
      child: Container(
        width: 200,
        height: 200,
        color: Colors.grey[300],
        child: Center(child: Text(_message)),
      ),
    );
  }
}
```

### 内置 GestureRecognizer

```dart
// 点击识别器
TapGestureRecognizer()

// 双击识别器
DoubleTapGestureRecognizer()

// 长按识别器
LongPressGestureRecognizer()

// 拖动识别器
HorizontalDragGestureRecognizer()
VerticalDragGestureRecognizer()
PanGestureRecognizer()

// 缩放识别器
ScaleGestureRecognizer()

// 多点触控识别器
MultiTapGestureRecognizer()
```

## 自定义手势

### 创建自定义手势识别器

```dart
class TripleTapGestureRecognizer extends TapGestureRecognizer {
  int _tapCount = 0;
  Timer? _timer;
  
  @override
  void handleTapDown({required PointerDownEvent down}) {
    _tapCount++;
    
    if (_tapCount == 3) {
      // 三击触发
      resolve(GestureDisposition.accepted);
      onTripleTap?.call();
      _reset();
    } else {
      // 设置超时
      _timer?.cancel();
      _timer = Timer(Duration(milliseconds: 500), _reset);
    }
    
    super.handleTapDown(down: down);
  }
  
  void _reset() {
    _tapCount = 0;
    _timer?.cancel();
  }
  
  GestureTapCallback? onTripleTap;
}
```

### 组合多个手势

```dart
class MultiGestureWidget extends StatefulWidget {
  @override
  _MultiGestureWidgetState createState() => _MultiGestureWidgetState();
}

class _MultiGestureWidgetState extends State<MultiGestureWidget> {
  String _status = '等待手势';
  
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => setState(() => _status = '单击'),
      onDoubleTap: () => setState(() => _status = '双击'),
      onLongPress: () => setState(() => _status = '长按'),
      onPanUpdate: (details) {
        setState(() => _status = '拖动: ${details.globalPosition}');
      },
      onScaleUpdate: (details) {
        setState(() => _status = '缩放: ${details.scale.toStringAsFixed(2)}');
      },
      child: Container(
        width: double.infinity,
        height: 300,
        color: Colors.grey[200],
        child: Center(child: Text(_status)),
      ),
    );
  }
}
```

## 常见手势场景

### 可滑动删除项

```dart
Dismissible(
  key: Key('item-1'),
  direction: DismissDirection.endToStart,
  background: Container(
    color: Colors.red,
    alignment: Alignment.centerRight,
    padding: EdgeInsets.only(right: 20),
    child: Icon(Icons.delete, color: Colors.white),
  ),
  onDismissed: (direction) {
    // 处理删除逻辑
  },
  child: ListTile(
    title: Text('滑动删除'),
  ),
)
```

### 下拉刷新

```dart
RefreshIndicator(
  onRefresh: () async {
    await Future.delayed(Duration(seconds: 1));
    // 刷新数据
  },
  child: ListView.builder(
    itemCount: 20,
    itemBuilder: (context, index) {
      return ListTile(title: Text('Item $index'));
    },
  ),
)
```

### 拖拽排序

```dart
class ReorderableListDemo extends StatelessWidget {
  final List<String> items = ['Item 1', 'Item 2', 'Item 3', 'Item 4'];
  
  @override
  Widget build(BuildContext context) {
    return ReorderableListView(
      onReorder: (oldIndex, newIndex) {
        // 处理重新排序
        if (newIndex > oldIndex) newIndex--;
        final item = items.removeAt(oldIndex);
        items.insert(newIndex, item);
      },
      children: items.map((item) {
        return ListTile(
          key: Key(item),
          title: Text(item),
          trailing: Icon(Icons.drag_handle),
        );
      }).toList(),
    );
  }
}
```

### 手势冲突处理

```dart
// 当有多个手势监听器时，使用 Listener 处理原始触摸事件
Listener(
  onPointerDown: (event) {
    print('Pointer down: ${event.position}');
  },
  onPointerMove: (event) {
    print('Pointer move: ${event.delta}');
  },
  onPointerUp: (event) {
    print('Pointer up');
  },
  child: GestureDetector(
    onTap: () {
      print('Tap');
    },
    child: Container(
      width: 200,
      height: 200,
      color: Colors.blue,
      child: Center(child: Text('触摸区域')),
    ),
  ),
)
```

### 绘制手势

```dart
class DrawingCanvas extends StatefulWidget {
  @override
  _DrawingCanvasState createState() => _DrawingCanvasState();
}

class _DrawingCanvasState extends State<DrawingCanvas> {
  final List<Offset> _points = [];
  
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
          _points.add(Offset.infinite);  // 标记断点
        });
      },
      child: CustomPaint(
        size: Size.infinite,
        painter: DrawingPainter(_points),
      ),
    );
  }
}

class DrawingPainter extends CustomPainter {
  final List<Offset> points;
  
  DrawingPainter(this.points);
  
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.black
      ..strokeWidth = 3
      ..strokeCap = StrokeCap.round;
    
    for (int i = 0; i < points.length - 1; i++) {
      if (points[i] != Offset.infinite && points[i + 1] != Offset.infinite) {
        canvas.drawLine(points[i], points[i + 1], paint);
      }
    }
  }
  
  @override
  bool shouldRepaint(DrawingPainter oldDelegate) {
    return oldDelegate.points != points;
  }
}
```

---

*最后更新: 2026-02-19*

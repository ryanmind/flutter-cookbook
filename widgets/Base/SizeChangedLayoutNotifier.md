# Flutter SizeChangedLayoutNotifier 组件用法详解

## 简介

SizeChangedLayoutNotifier 是 Flutter 中用于监听子组件尺寸变化的组件。当子组件的尺寸发生变化时，它会通知监听器。

## 1. 初级用法

### 1.1 基本概念

SizeChangedLayoutNotifier 的核心作用：
- 监听子组件尺寸变化
- 通过 Notification 机制通知变化
- 用于响应式布局调整

### 1.2 基本语法

```dart
NotificationListener<SizeChangedLayoutNotification>(
  onNotification: (notification) {
    print('Size changed!');
    return true;
  },
  child: SizeChangedLayoutNotifier(
    child: Container(...),
  ),
)
```

### 1.3 主要特点

- 轻量级尺寸监听
- 基于 Flutter Notification 机制
- 性能优化，只在尺寸变化时通知

### 1.4 基础示例

```dart
class SizeChangedExample extends StatefulWidget {
  @override
  State<SizeChangedExample> createState() => _SizeChangedExampleState();
}

class _SizeChangedExampleState extends State<SizeChangedExample> {
  double _width = 100;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        NotificationListener<SizeChangedLayoutNotification>(
          onNotification: (notification) {
            print('Container size changed!');
            return true;
          },
          child: SizeChangedLayoutNotifier(
            child: AnimatedContainer(
              duration: Duration(milliseconds: 300),
              width: _width,
              height: 100,
              color: Colors.blue,
            ),
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _width = _width == 100 ? 200 : 100),
          child: Text('Toggle Size'),
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 获取新尺寸

```dart
class SizeTrackingWidget extends StatefulWidget {
  @override
  State<SizeTrackingWidget> createState() => _SizeTrackingWidgetState();
}

class _SizeTrackingWidgetState extends State<SizeTrackingWidget> {
  Size? _size;
  final _key = GlobalKey();

  @override
  Widget build(BuildContext context) {
    return NotificationListener<SizeChangedLayoutNotification>(
      onNotification: (notification) {
        WidgetsBinding.instance.addPostFrameCallback((_) {
          final RenderBox? renderBox =
              _key.currentContext?.findRenderObject() as RenderBox?;
          if (renderBox != null) {
            setState(() {
              _size = renderBox.size;
            });
          }
        });
        return true;
      },
      child: SizeChangedLayoutNotifier(
        child: Container(
          key: _key,
          width: 100,
          height: 100,
          color: Colors.blue,
          child: Text('Size: ${_size?.width.toStringAsFixed(0) ?? "?"} x ${_size?.height.toStringAsFixed(0) ?? "?"}'),
        ),
      ),
    );
  }
}
```

### 2.2 响应式布局

```dart
class ResponsiveLayout extends StatefulWidget {
  @override
  State<ResponsiveLayout> createState() => _ResponsiveLayoutState();
}

class _ResponsiveLayoutState extends State<ResponsiveLayout> {
  bool _isWide = false;

  @override
  Widget build(BuildContext context) {
    return NotificationListener<SizeChangedLayoutNotification>(
      onNotification: (notification) {
        WidgetsBinding.instance.addPostFrameCallback((_) {
          final width = MediaQuery.of(context).size.width;
          setState(() {
            _isWide = width > 600;
          });
        });
        return true;
      },
      child: SizeChangedLayoutNotifier(
        child: LayoutBuilder(
          builder: (context, constraints) {
            return Container(
              color: _isWide ? Colors.blue : Colors.red,
              child: Center(
                child: Text(_isWide ? 'Wide Layout' : 'Narrow Layout'),
              ),
            );
          },
        ),
      ),
    );
  }
}
```

### 2.3 列表项尺寸监听

```dart
class ListItemWithSizeNotification extends StatelessWidget {
  final String title;
  final VoidCallback onSizeChanged;

  const ListItemWithSizeNotification({
    required this.title,
    required this.onSizeChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return NotificationListener<SizeChangedLayoutNotification>(
      onNotification: (notification) {
        onSizeChanged();
        return true;
      },
      child: SizeChangedLayoutNotifier(
        child: ListTile(
          title: Text(title),
        ),
      ),
    );
  }
}
```

### 2.4 配合 CustomPaint

```dart
class CustomPaintSizeNotifier extends StatefulWidget {
  @override
  State<CustomPaintSizeNotifier> createState() => _CustomPaintSizeNotifierState();
}

class _CustomPaintSizeNotifierState extends State<CustomPaintSizeNotifier> {
  Size _size = Size.zero;

  @override
  Widget build(BuildContext context) {
    return NotificationListener<SizeChangedLayoutNotification>(
      onNotification: (notification) {
        WidgetsBinding.instance.addPostFrameCallback((_) {
          final RenderBox? box = context.findRenderObject() as RenderBox?;
          if (box != null) {
            setState(() => _size = box.size);
          }
        });
        return true;
      },
      child: SizeChangedLayoutNotifier(
        child: CustomPaint(
          size: _size,
          painter: MyPainter(_size),
        ),
      ),
    );
  }
}

class MyPainter extends CustomPainter {
  final Size size;
  MyPainter(this.size);

  @override
  void paint(Canvas canvas, Size size) {
    // 根据尺寸绘制
  }

  @override
  bool shouldRepaint(MyPainter oldDelegate) => size != oldDelegate.size;
}
```

## 3. 高级用法

### 3.1 封装尺寸监听组件

```dart
typedef SizeCallback = void Function(Size size);

class SizeObserver extends SingleChildRenderObjectWidget {
  final SizeCallback onSizeChanged;

  const SizeObserver({
    required this.onSizeChanged,
    required Widget child,
    Key? key,
  }) : super(key: key, child: child);

  @override
  RenderObject createRenderObject(BuildContext context) {
    return _SizeObserverRenderObject(onSizeChanged);
  }

  @override
  void updateRenderObject(BuildContext context, _SizeObserverRenderObject renderObject) {
    renderObject.onSizeChanged = onSizeChanged;
  }
}

class _SizeObserverRenderObject extends RenderProxyBox {
  SizeCallback onSizeChanged;
  Size? _oldSize;

  _SizeObserverRenderObject(this.onSizeChanged);

  @override
  void performLayout() {
    super.performLayout();
    if (size != _oldSize) {
      _oldSize = size;
      WidgetsBinding.instance.addPostFrameCallback((_) {
        onSizeChanged(size);
      });
    }
  }
}
```

### 3.2 滚动位置联动

```dart
class ScrollLinkedWidget extends StatefulWidget {
  @override
  State<ScrollLinkedWidget> createState() => _ScrollLinkedWidgetState();
}

class _ScrollLinkedWidgetState extends State<ScrollLinkedWidget> {
  final _scrollController = ScrollController();
  double _headerHeight = 100;

  @override
  void dispose() {
    _scrollController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return NotificationListener<SizeChangedLayoutNotification>(
      onNotification: (notification) {
        // Header 尺寸变化时更新滚动位置
        return true;
      },
      child: SizeChangedLayoutNotifier(
        child: CustomScrollView(
          controller: _scrollController,
          slivers: [
            SliverPersistentHeader(
              delegate: _SliverHeaderDelegate(_headerHeight),
            ),
            SliverList(
              delegate: SliverChildBuilderDelegate(
                (context, index) => ListTile(title: Text('Item $index')),
                childCount: 50,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

### 3.3 动画尺寸监听

```dart
class AnimatedSizeObserver extends StatefulWidget {
  @override
  State<AnimatedSizeObserver> createState() => _AnimatedSizeObserverState();
}

class _AnimatedSizeObserverState extends State<AnimatedSizeObserver>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  Size? _currentSize;

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

  @override
  Widget build(BuildContext context) {
    return NotificationListener<SizeChangedLayoutNotification>(
      onNotification: (notification) {
        _controller.forward(from: 0);
        return true;
      },
      child: SizeChangedLayoutNotifier(
        child: ScaleTransition(
          scale: Tween<double>(begin: 0.95, end: 1.0).animate(
            CurvedAnimation(parent: _controller, curve: Curves.easeOut),
          ),
          child: Container(
            width: 200,
            height: 100,
            color: Colors.blue,
          ),
        ),
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：通知不触发

```dart
// 确保使用正确的通知类型
NotificationListener<SizeChangedLayoutNotification>(
  onNotification: (notification) {
    print('Size changed');  // 确保这里执行
    return true;
  },
  child: SizeChangedLayoutNotifier(
    child: YourWidget(),
  ),
)
```

### 问题2：获取不到尺寸

```dart
// 使用 addPostFrameCallback 确保布局完成
onNotification: (notification) {
  WidgetsBinding.instance.addPostFrameCallback((_) {
    final RenderBox? box = context.findRenderObject() as RenderBox?;
    if (box != null) {
      print('Size: ${box.size}');
    }
  });
  return true;
}
```

### 问题3：频繁通知

```dart
// 使用防抖动
class DebouncedSizeNotifier {
  Timer? _timer;
  final Duration delay;
  final void Function(Size) onSizeChanged;

  DebouncedSizeNotifier({
    this.delay = const Duration(milliseconds: 100),
    required this.onSizeChanged,
  });

  void notify(Size size) {
    _timer?.cancel();
    _timer = Timer(delay, () => onSizeChanged(size));
  }

  void dispose() {
    _timer?.cancel();
  }
}
```

## 5. SizeChangedLayoutNotifier vs 其他方式

| 方式 | 用途 | 特点 |
|------|------|------|
| **SizeChangedLayoutNotifier** | 尺寸变化监听 | 轻量，通知机制 |
| **LayoutBuilder** | 构建时获取约束 | 每次构建调用 |
| **GlobalKey** | 手动获取尺寸 | 需要手动调用 |
| **WidgetsBinding** | 布局回调 | 更底层 |

### 选择建议

```dart
// 只需监听变化 → SizeChangedLayoutNotifier
NotificationListener<SizeChangedLayoutNotification>(
  onNotification: (n) { /* 处理 */ return true; },
  child: SizeChangedLayoutNotifier(child: widget),
)

// 需要约束信息 → LayoutBuilder
LayoutBuilder(
  builder: (context, constraints) => Widget(),
)

// 手动获取 → GlobalKey
final key = GlobalKey();
final size = (key.currentContext?.findRenderObject() as RenderBox?)?.size;
```

## 6. 最佳实践

### 6.1 封装复用

```dart
class SizeAwareWidget extends StatelessWidget {
  final Widget child;
  final ValueChanged<Size>? onSizeChanged;

  const SizeAwareWidget({
    required this.child,
    this.onSizeChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return NotificationListener<SizeChangedLayoutNotification>(
      onNotification: (notification) {
        WidgetsBinding.instance.addPostFrameCallback((_) {
          final RenderBox? box = context.findRenderObject() as RenderBox?;
          if (box != null && onSizeChanged != null) {
            onSizeChanged!(box.size);
          }
        });
        return true;
      },
      child: SizeChangedLayoutNotifier(child: child),
    );
  }
}
```

### 6.2 性能考虑

```dart
// 避免在 onNotification 中做耗时操作
NotificationListener<SizeChangedLayoutNotification>(
  onNotification: (notification) {
    // 轻量操作
    _lastSizeChangeTime = DateTime.now();
    return true;
  },
  child: SizeChangedLayoutNotifier(child: widget),
)
```

## 总结

SizeChangedLayoutNotifier 是监听组件尺寸变化的轻量工具：

**核心要点**：
1. 配合 NotificationListener 使用
2. 只在尺寸变化时通知
3. 使用 addPostFrameCallback 获取准确尺寸

**最佳实践**：
1. 封装为可复用组件
2. 避免频繁重绘
3. 合理处理通知回调

**常见场景**：
- 响应式布局
- 滚动联动
- 动画触发

---

*最后更新: 2026-02-19*

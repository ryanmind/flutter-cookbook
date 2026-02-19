# Flutter InteractiveViewer 组件用法详解

## 简介

InteractiveViewer 是 Flutter 中用于实现交互式视图的组件。它支持缩放、平移、旋转等手势操作，常用于图片查看器、地图等场景。

## 1. 初级用法

### 1.1 基本概念

InteractiveViewer 的作用：
- 支持双指缩放
- 支持平移拖动
- 可设置边界限制
- 支持手势识别

### 1.2 基本语法

```dart
InteractiveViewer(
  child: Image.network('https://picsum.photos/800'),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `child` | `Widget` | 子组件 |
| `minScale` | `double` | 最小缩放比例，默认 0.8 |
| `maxScale` | `double` | 最大缩放比例，默认 2.5 |
| `boundaryMargin` | `EdgeInsets` | 边界边距 |
| `constrained` | `bool` | 是否约束子组件 |
| `clipBehavior` | `Clip` | 裁剪行为 |
| `panEnabled` | `bool` | 是否启用平移 |
| `scaleEnabled` | `bool` | 是否启用缩放 |
| `onInteractionStart` | `GestureScaleStartCallback?` | 开始交互回调 |
| `onInteractionUpdate` | `GestureScaleUpdateCallback?` | 更新交互回调 |
| `onInteractionEnd` | `GestureScaleEndCallback?` | 结束交互回调 |

### 1.4 基础示例

#### 可缩放图片

```dart
InteractiveViewer(
  minScale: 0.5,
  maxScale: 4.0,
  child: Image.network(
    'https://picsum.photos/800',
    fit: BoxFit.cover,
  ),
)
```

#### 限制缩放范围

```dart
InteractiveViewer(
  minScale: 1.0,
  maxScale: 5.0,
  child: Container(
    width: 300,
    height: 300,
    color: Colors.blue,
    child: Center(child: Text('只能放大')),
  ),
)
```

## 2. 中级用法

### 2.1 边界设置

```dart
InteractiveViewer(
  boundaryMargin: EdgeInsets.all(100),  // 允许拖出边界 100 像素
  child: Container(
    width: 300,
    height: 300,
    color: Colors.green,
  ),
)
```

### 2.2 禁用特定手势

```dart
// 只允许缩放，不允许平移
InteractiveViewer(
  panEnabled: false,
  child: Image.network('https://picsum.photos/400'),
)

// 只允许平移，不允许缩放
InteractiveViewer(
  scaleEnabled: false,
  child: Container(
    width: 1000,
    height: 1000,
    color: Colors.blue[100],
  ),
)
```

### 2.3 交互回调

```dart
class InteractiveExample extends StatefulWidget {
  @override
  State<InteractiveExample> createState() => _InteractiveExampleState();
}

class _InteractiveExampleState extends State<InteractiveExample> {
  double _scale = 1.0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('当前缩放: ${_scale.toStringAsFixed(2)}'),
        Expanded(
          child: InteractiveViewer(
            onInteractionUpdate: (details) {
              setState(() {
                _scale = details.scale;
              });
            },
            child: Image.network('https://picsum.photos/800'),
          ),
        ),
      ],
    );
  }
}
```

### 2.4 常见布局场景

#### 图片查看器

```dart
class ImageViewer extends StatelessWidget {
  final String imageUrl;

  const ImageViewer({required this.imageUrl});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('图片查看')),
      body: Center(
        child: InteractiveViewer(
          minScale: 0.5,
          maxScale: 4.0,
          boundaryMargin: EdgeInsets.all(double.infinity),
          child: Image.network(
            imageUrl,
            fit: BoxFit.contain,
          ),
        ),
      ),
    );
  }
}
```

#### 地图视图

```dart
class MapViewer extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return InteractiveViewer(
      constrained: false,
      boundaryMargin: EdgeInsets.all(double.infinity),
      minScale: 0.1,
      maxScale: 2.0,
      child: Container(
        width: 2000,
        height: 2000,
        decoration: BoxDecoration(
          gradient: LinearGradient(
            colors: [Colors.green[100]!, Colors.green[300]!],
          ),
        ),
        child: Stack(
          children: [
            Positioned(
              left: 500,
              top: 500,
              child: Icon(Icons.location_on, size: 40, color: Colors.red),
            ),
            Positioned(
              left: 800,
              top: 1200,
              child: Icon(Icons.location_on, size: 40, color: Colors.blue),
            ),
          ],
        ),
      ),
    );
  }
}
```

#### 流程图

```dart
class FlowChartViewer extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return InteractiveViewer(
      constrained: false,
      boundaryMargin: EdgeInsets.all(100),
      child: Container(
        width: 1500,
        height: 1000,
        child: CustomPaint(
          painter: FlowChartPainter(),
        ),
      ),
    );
  }
}

class FlowChartPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.blue
      ..style = PaintingStyle.stroke
      ..strokeWidth = 2;

    // 绘制节点和连线
    canvas.drawRect(Rect.fromLTWH(100, 100, 100, 60), paint);
    canvas.drawRect(Rect.fromLTWH(300, 100, 100, 60), paint);
    canvas.drawLine(Offset(200, 130), Offset(300, 130), paint);
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

## 3. 高级用法

### 3.1 控制器控制

```dart
class ControlledViewer extends StatefulWidget {
  @override
  State<ControlledViewer> createState() => _ControlledViewerState();
}

class _ControlledViewerState extends State<ControlledViewer> {
  final TransformationController _controller = TransformationController();

  void _reset() {
    _controller.value = Matrix4.identity();
  }

  void _zoomIn() {
    _controller.value = Matrix4.identity()..scale(2.0);
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
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: [
            ElevatedButton(onPressed: _reset, child: Text('重置')),
            ElevatedButton(onPressed: _zoomIn, child: Text('放大')),
          ],
        ),
        Expanded(
          child: InteractiveViewer(
            transformationController: _controller,
            child: Image.network('https://picsum.photos/800'),
          ),
        ),
      ],
    );
  }
}
```

### 3.2 动画缩放

```dart
class AnimatedZoomViewer extends StatefulWidget {
  @override
  State<AnimatedZoomViewer> createState() => _AnimatedZoomViewerState();
}

class _AnimatedZoomViewerState extends State<AnimatedZoomViewer>
    with SingleTickerProviderStateMixin {
  final TransformationController _controller = TransformationController();
  late AnimationController _animationController;
  late Animation<Matrix4> _animation;

  @override
  void initState() {
    super.initState();
    _animationController = AnimationController(
      duration: Duration(milliseconds: 300),
      vsync: this,
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    _animationController.dispose();
    super.dispose();
  }

  void _animateZoom(double scale) {
    final target = Matrix4.identity()..scale(scale);
    _animation = Matrix4Tween(
      begin: _controller.value,
      end: target,
    ).animate(CurvedAnimation(
      parent: _animationController,
      curve: Curves.easeInOut,
    ));
    
    _animationController.forward(from: 0);
    
    _animationController.addListener(() {
      _controller.value = _animation.value;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: [
            ElevatedButton(
              onPressed: () => _animateZoom(1.0),
              child: Text('1x'),
            ),
            ElevatedButton(
              onPressed: () => _animateZoom(2.0),
              child: Text('2x'),
            ),
            ElevatedButton(
              onPressed: () => _animateZoom(3.0),
              child: Text('3x'),
            ),
          ],
        ),
        Expanded(
          child: InteractiveViewer(
            transformationController: _controller,
            child: Image.network('https://picsum.photos/800'),
          ),
        ),
      ],
    );
  }
}
```

### 3.3 双击缩放

```dart
class DoubleTapZoom extends StatefulWidget {
  @override
  State<DoubleTapZoom> createState() => _DoubleTapZoomState();
}

class _DoubleTapZoomState extends State<DoubleTapZoom> {
  final TransformationController _controller = TransformationController();
  bool _isZoomed = false;

  void _handleDoubleTap() {
    final targetScale = _isZoomed ? 1.0 : 2.0;
    _controller.value = Matrix4.identity()..scale(targetScale);
    setState(() {
      _isZoomed = !_isZoomed;
    });
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onDoubleTap: _handleDoubleTap,
      child: InteractiveViewer(
        transformationController: _controller,
        child: Image.network('https://picsum.photos/800'),
      ),
    );
  }
}
```

### 3.4 无限画布

```dart
InteractiveViewer(
  constrained: false,
  boundaryMargin: EdgeInsets.all(double.infinity),
  child: Container(
    width: 10000,
    height: 10000,
    color: Colors.grey[100],
    child: GridPaper(
      color: Colors.grey[300]!,
      child: Center(
        child: Text('无限画布'),
      ),
    ),
  ),
)
```

## 4. 常见问题与解决方案

### 问题1：子组件无法滚动

```dart
// 问题：InteractiveViewer 内部有 ListView 滚动冲突
InteractiveViewer(
  child: ListView(...),  // 冲突
)

// 解决：使用 constrained: false 或调整手势优先级
InteractiveViewer(
  constrained: false,
  child: Container(
    width: 2000,
    child: ListView(
      physics: NeverScrollableScrollPhysics(),
      children: [...],
    ),
  ),
)
```

### 问题2：缩放后模糊

```dart
// 问题：图片缩放后模糊
// 解决：使用高分辨率图片

InteractiveViewer(
  child: Image.network(
    'high-resolution-url',  // 高分辨率
    filterQuality: FilterQuality.high,
  ),
)
```

### 问题3：边界限制不生效

```dart
// 问题：设置了边界但可以无限拖动
// 解决：确保 constrained 为 true

InteractiveViewer(
  constrained: true,  // 启用约束
  boundaryMargin: EdgeInsets.all(50),
  child: ...,
)
```

## 5. InteractiveViewer vs 其他缩放方案对比

| 方案 | 用途 | 特点 |
|------|------|------|
| **InteractiveViewer** | 交互视图 | 功能全面 |
| **Transform.scale** | 固定缩放 | 手动控制 |
| **PhotoView** | 图片查看 | 专用于图片 |
| **GestureDetector** | 自定义手势 | 完全自定义 |

### 选择建议

```dart
// 图片查看 → InteractiveViewer 或 PhotoView
InteractiveViewer(child: Image.network(...))

// 地图/画布 → InteractiveViewer
InteractiveViewer(constrained: false, ...)

// 简单缩放 → Transform.scale
Transform.scale(scale: 1.5, child: ...)
```

## 6. 最佳实践

### 6.1 封装图片查看器

```dart
class ZoomableImage extends StatelessWidget {
  final String url;
  final double minScale;
  final double maxScale;

  const ZoomableImage({
    required this.url,
    this.minScale = 0.5,
    this.maxScale = 4.0,
  });

  @override
  Widget build(BuildContext context) {
    return InteractiveViewer(
      minScale: minScale,
      maxScale: maxScale,
      boundaryMargin: EdgeInsets.all(double.infinity),
      child: Center(
        child: Image.network(
          url,
          fit: BoxFit.contain,
          filterQuality: FilterQuality.high,
        ),
      ),
    );
  }
}
```

### 6.2 状态保存

```dart
// 保存缩放状态
class ViewerState {
  final double scale;
  final Offset position;

  ViewerState({required this.scale, required this.position});

  Matrix4 toMatrix4() {
    return Matrix4.identity()
      ..translate(position.dx, position.dy)
      ..scale(scale);
  }
}
```

### 6.3 性能优化

```dart
// 对于大图片，考虑分块加载
InteractiveViewer(
  child: InteractiveViewer(
    child: Image.network(
      'url',
      cacheWidth: 1024,  // 限制缓存尺寸
    ),
  ),
)
```

## 总结

InteractiveViewer 是 Flutter 中实现交互式视图的核心组件：

**核心要点**：
1. 支持缩放、平移等手势
2. 可设置缩放范围和边界
3. 通过 TransformationController 精确控制

**最佳实践**：
1. 合理设置缩放范围
2. 使用控制器实现动画
3. 注意手势冲突处理

**常见场景**：
- 图片查看器
- 地图视图
- 流程图
- 无限画布

---

*最后更新: 2026-02-19*

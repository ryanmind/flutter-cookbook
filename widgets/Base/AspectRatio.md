# Flutter AspectRatio 组件用法详解

## 简介

AspectRatio 是 Flutter 中用于控制子组件宽高比的布局组件。它会根据给定的宽高比自动计算尺寸，确保子组件保持指定的比例。

## 1. 初级用法

### 1.1 基本概念

AspectRatio 的作用：
- 强制子组件保持指定的宽高比
- 根据父容器约束自动计算尺寸
- 常用于图片、视频、卡片等需要固定比例的场景

### 1.2 基本语法

```dart
AspectRatio(
  aspectRatio: 16 / 9,
  child: Container(color: Colors.blue),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `aspectRatio` | `double` | 宽高比（宽/高） |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 16:9 视频比例

```dart
AspectRatio(
  aspectRatio: 16 / 9,
  child: Container(
    color: Colors.black,
    child: Center(
      child: Icon(Icons.play_circle, color: Colors.white, size: 64),
    ),
  ),
)
```

#### 1:1 正方形

```dart
AspectRatio(
  aspectRatio: 1.0,
  child: Container(
    color: Colors.blue,
    child: Center(child: Text('正方形')),
  ),
)
```

#### 4:3 图片比例

```dart
AspectRatio(
  aspectRatio: 4 / 3,
  child: Image.network(
    'https://example.com/image.jpg',
    fit: BoxFit.cover,
  ),
)
```

## 2. 中级用法

### 2.1 尺寸计算逻辑

AspectRatio 的尺寸计算：

1. **有宽度约束**：高度 = 宽度 / aspectRatio
2. **有高度约束**：宽度 = 高度 * aspectRatio
3. **都有约束**：选择满足约束且不超出限制的尺寸

```dart
// 父容器宽度 300，aspectRatio = 16/9
// 计算高度 = 300 / (16/9) = 168.75
Container(
  width: 300,
  child: AspectRatio(
    aspectRatio: 16 / 9,
    child: Container(color: Colors.blue),
  ),
)
```

### 2.2 常见比例值

```dart
// 常用宽高比
AspectRatio(aspectRatio: 1.0, ...)        // 1:1 正方形
AspectRatio(aspectRatio: 16 / 9, ...)     // 16:9 视频
AspectRatio(aspectRatio: 4 / 3, ...)      // 4:3 传统屏幕
AspectRatio(aspectRatio: 3 / 4, ...)      // 3:4 竖版
AspectRatio(aspectRatio: 21 / 9, ...)     // 21:9 超宽屏
AspectRatio(aspectRatio: 0.618, ...)      // 黄金比例
```

### 2.3 常见布局场景

#### 图片卡片网格

```dart
GridView.builder(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    mainAxisSpacing: 8,
    crossAxisSpacing: 8,
  ),
  itemCount: 10,
  itemBuilder: (context, index) {
    return AspectRatio(
      aspectRatio: 1.0,
      child: ClipRRect(
        borderRadius: BorderRadius.circular(8),
        child: Image.network(
          'https://picsum.photos/200?random=$index',
          fit: BoxFit.cover,
        ),
      ),
    );
  },
)
```

#### 视频播放器

```dart
class VideoPlayerCard extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Card(
      clipBehavior: Clip.antiAlias,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          AspectRatio(
            aspectRatio: 16 / 9,
            child: Stack(
              alignment: Alignment.center,
              children: [
                Container(color: Colors.black),
                IconButton(
                  icon: Icon(Icons.play_circle_fill, size: 64, color: Colors.white),
                  onPressed: () {},
                ),
              ],
            ),
          ),
          Padding(
            padding: EdgeInsets.all(12),
            child: Text('视频标题'),
          ),
        ],
      ),
    );
  }
}
```

#### 头像列表

```dart
Row(
  children: List.generate(5, (index) {
    return Expanded(
      child: Padding(
        padding: EdgeInsets.symmetric(horizontal: 4),
        child: AspectRatio(
          aspectRatio: 1.0,
          child: CircleAvatar(
            backgroundImage: NetworkImage('https://i.pravatar.cc/100?img=$index'),
          ),
        ),
      ),
    );
  }),
)
```

### 2.4 响应式比例

```dart
LayoutBuilder(
  builder: (context, constraints) {
    // 根据屏幕宽度动态调整比例
    final ratio = constraints.maxWidth > 600 ? 2.0 : 1.0;
    return AspectRatio(
      aspectRatio: ratio,
      child: Container(color: Colors.blue),
    );
  },
)
```

## 3. 高级用法

### 3.1 嵌套使用

```dart
AspectRat io(
  aspectRatio: 16 / 9,
  child: Container(
    color: Colors.grey[200],
    child: AspectRatio(
      aspectRatio: 4 / 3,
      child: Container(color: Colors.blue),
    ),
  ),
)
```

### 3.2 配合 Card 使用

```dart
Card(
  clipBehavior: Clip.antiAlias,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(12),
  ),
  child: AspectRatio(
    aspectRatio: 16 / 9,
    child: Stack(
      fit: StackFit.expand,
      children: [
        Image.network('https://picsum.photos/400', fit: BoxFit.cover),
        Positioned(
          bottom: 0,
          left: 0,
          right: 0,
          child: Container(
            padding: EdgeInsets.all(12),
            decoration: BoxDecoration(
              gradient: LinearGradient(
                begin: Alignment.topCenter,
                end: Alignment.bottomCenter,
                colors: [Colors.transparent, Colors.black54],
              ),
            ),
            child: Text(
              '标题',
              style: TextStyle(color: Colors.white),
            ),
          ),
        ),
      ],
    ),
  ),
)
```

### 3.3 动态比例

```dart
class DynamicAspectRatio extends StatefulWidget {
  @override
  State<DynamicAspectRatio> createState() => _DynamicAspectRatioState();
}

class _DynamicAspectRatioState extends State<DynamicAspectRatio> {
  double _ratio = 1.0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AspectRatio(
          aspectRatio: _ratio,
          child: Container(color: Colors.blue),
        ),
        Slider(
          value: _ratio,
          min: 0.5,
          max: 2.0,
          onChanged: (value) => setState(() => _ratio = value),
        ),
        Text('比例: ${_ratio.toStringAsFixed(2)}'),
      ],
    );
  }
}
```

### 3.4 配合 LayoutBuilder

```dart
LayoutBuilder(
  builder: (context, constraints) {
    // 保持图片原始比例
    final imageWidth = 1920.0;
    final imageHeight = 1080.0;
    final imageRatio = imageWidth / imageHeight;
    
    return AspectRatio(
      aspectRatio: imageRatio,
      child: Image.network(
        'https://example.com/image.jpg',
        fit: BoxFit.cover,
      ),
    );
  },
)
```

## 4. 常见问题与解决方案

### 问题1：在无限约束中使用

```dart
// 问题：高度无限时无法计算
Column(
  children: [
    AspectRatio(aspectRatio: 1.0, child: Container()),  // 高度无限
  ],
)

// 解决：使用 Expanded 或 SizedBox
Column(
  children: [
    AspectRatio(
      aspectRatio: 1.0,
      child: Container(color: Colors.blue),
    ),
  ],
)
// AspectRatio 在 Column 中会尝试扩展，但需要父容器有宽度约束
```

### 问题2：比例计算错误

```dart
// 注意：aspectRatio 是 宽/高，不是 高/宽
// 16:9 意味着宽度是高度的 16/9 倍
AspectRatio(aspectRatio: 16 / 9, ...)  // 宽屏

// 如果需要竖版 9:16
AspectRatio(aspectRatio: 9 / 16, ...)  // 竖版
```

### 问题3：子组件超出比例

```dart
// AspectRatio 只约束尺寸，不裁剪子组件
AspectRatio(
  aspectRatio: 1.0,
  child: Container(
    width: 200,
    height: 200,
    color: Colors.blue,
  ),
)

// 如果需要裁剪，使用 ClipRect
ClipRect(
  child: AspectRatio(
    aspectRatio: 1.0,
    child: Container(color: Colors.blue),
  ),
)
```

## 5. AspectRatio vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **AspectRatio** | 固定宽高比 | 根据比例自动计算 |
| **SizedBox** | 固定尺寸 | 需要明确宽高 |
| **Container** | 综合容器 | 可设置宽高但对齐 |
| **FractionallySizedBox** | 按比例尺寸 | 基于父容器百分比 |

### 选择建议

```dart
// 需要保持比例 → AspectRatio
AspectRatio(aspectRatio: 16 / 9, child: Image(...))

// 固定尺寸 → SizedBox
SizedBox(width: 100, height: 100, child: ...)

// 基于父容器百分比 → FractionallySizedBox
FractionallySizedBox(widthFactor: 0.5, child: ...)
```

## 6. 最佳实践

### 6.1 定义常用比例

```dart
abstract class AspectRatios {
  static const double square = 1.0;
  static const double video = 16 / 9;
  static const double photo = 4 / 3;
  static const double portrait = 3 / 4;
  static const double story = 9 / 16;  // Instagram Story
  static const double golden = 1.618;
}

// 使用
AspectRatio(
  aspectRatio: AspectRatios.video,
  child: VideoPlayer(),
)
```

### 6.2 图片组件封装

```dart
class AspectImage extends StatelessWidget {
  final String url;
  final double aspectRatio;
  final BoxFit fit;

  const AspectImage({
    required this.url,
    this.aspectRatio = 1.0,
    this.fit = BoxFit.cover,
  });

  @override
  Widget build(BuildContext context) {
    return AspectRatio(
      aspectRatio: aspectRatio,
      child: Image.network(url, fit: fit),
    );
  }
}
```

### 6.3 视频缩略图

```dart
class VideoThumbnail extends StatelessWidget {
  final String thumbnailUrl;
  final VoidCallback? onTap;

  const VideoThumbnail({
    required this.thumbnailUrl,
    this.onTap,
  });

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onTap,
      child: AspectRatio(
        aspectRatio: 16 / 9,
        child: Stack(
          fit: StackFit.expand,
          children: [
            Image.network(thumbnailUrl, fit: BoxFit.cover),
            Center(
              child: Container(
                padding: EdgeInsets.all(12),
                decoration: BoxDecoration(
                  color: Colors.black45,
                  shape: BoxShape.circle,
                ),
                child: Icon(Icons.play_arrow, color: Colors.white),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

## 总结

AspectRatio 是 Flutter 中控制宽高比的利器：

**核心要点**：
1. aspectRatio = 宽度 / 高度
2. 根据父容器约束自动计算尺寸
3. 子组件应使用 BoxFit 适配

**最佳实践**：
1. 定义常用比例常量
2. 图片、视频场景优先使用
3. 配合 BoxFit.cover 填充内容

**常见场景**：
- 视频播放器
- 图片卡片
- 头像组件
- 缩略图列表

---

*最后更新: 2026-02-19*

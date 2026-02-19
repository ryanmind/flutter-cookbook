# Flutter Image 组件用法详解

## 简介

Image 是 Flutter 中用于显示图片的核心组件，支持多种图片来源：网络、本地文件、资产文件、内存数据等。

## 1. 初级用法

### 1.1 基本概念

Image 的核心特点：
- 支持多种图片来源
- 自动处理缓存
- 支持占位符和错误处理
- 提供丰富的填充模式

### 1.2 图片来源对比

| 构造函数 | 来源 | 使用场景 |
|---------|------|---------|
| `Image.network` | 网络 URL | 加载网络图片 |
| `Image.asset` | 项目资产 | 加载打包图片 |
| `Image.file` | 本地文件 | 加载设备图片 |
| `Image.memory` | 内存数据 | 加载二进制图片 |

### 1.3 基本语法

```dart
// 网络图片
Image.network('https://picsum.photos/200')

// 资产图片
Image.asset('images/logo.png')

// 本地文件
Image.file(File('/path/to/image.png'))

// 内存数据
Image.memory(uint8List)
```

### 1.4 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `image` | `ImageProvider` | - | 图片提供者 |
| `width` | `double?` | - | 宽度 |
| `height` | `double?` | - | 高度 |
| `fit` | `BoxFit?` | - | 填充模式 |
| `alignment` | `Alignment` | `center` | 对齐方式 |
| `color` | `Color?` | - | 混合颜色 |
| `colorBlendMode` | `BlendMode?` | - | 混合模式 |
| `filterQuality` | `FilterQuality` | `low` | 滤镜质量 |
| `repeat` | `ImageRepeat` | `noRepeat` | 重复方式 |
| `semanticLabel` | `String?` | - | 语义标签 |
| `loadingBuilder` | `Widget Function?` | - | 加载构建器 |
| `errorBuilder` | `Widget Function?` | - | 错误构建器 |
| `frameBuilder` | `Widget Function?` | - | 帧构建器 |

### 1.5 BoxFit 填充模式

| 模式 | 说明 |
|------|------|
| `fill` | 拉伸填满，可能变形 |
| `contain` | 保持比例，完整显示 |
| `cover` | 保持比例，填满容器，可能裁剪 |
| `fitWidth` | 宽度填满，高度自适应 |
| `fitHeight` | 高度填满，宽度自适应 |
| `none` | 原始大小 |
| `scaleDown` | 缩小以适应，不放大 |

```dart
Column(
  children: [
    Text('fill'),
    Image.network('https://picsum.photos/200', fit: BoxFit.fill),
    Text('contain'),
    Image.network('https://picsum.photos/200', fit: BoxFit.contain),
    Text('cover'),
    Image.network('https://picsum.photos/200', fit: BoxFit.cover),
  ],
)
```

### 1.6 基础示例

#### 设置尺寸

```dart
Image.network(
  'https://picsum.photos/200',
  width: 100,
  height: 100,
)

// 全宽图片
Image.network(
  'https://picsum.photos/400/200',
  width: double.infinity,
  height: 200,
  fit: BoxFit.cover,
)
```

#### 圆形图片

```dart
// 方法1：ClipOval
ClipOval(
  child: Image.network(
    'https://picsum.photos/100',
    width: 80,
    height: 80,
    fit: BoxFit.cover,
  ),
)

// 方法2：CircleAvatar
CircleAvatar(
  radius: 40,
  backgroundImage: NetworkImage('https://picsum.photos/100'),
)

// 方法3：Container
Container(
  width: 80,
  height: 80,
  decoration: BoxDecoration(
    shape: BoxShape.circle,
    image: DecorationImage(
      image: NetworkImage('https://picsum.photos/100'),
      fit: BoxFit.cover,
    ),
  ),
)
```

## 2. 中级用法

### 2.1 加载占位符

```dart
Image.network(
  'https://picsum.photos/400/200',
  loadingBuilder: (context, child, loadingProgress) {
    if (loadingProgress == null) return child;
    return Center(
      child: CircularProgressIndicator(
        value: loadingProgress.expectedTotalBytes != null
            ? loadingProgress.cumulativeBytesLoaded / 
              loadingProgress.expectedTotalBytes!
            : null,
      ),
    );
  },
)
```

### 2.2 错误处理

```dart
Image.network(
  'https://invalid-url.com/image.png',
  errorBuilder: (context, error, stackTrace) {
    return Container(
      color: Colors.grey[200],
      child: Icon(Icons.broken_image, size: 48, color: Colors.grey),
    );
  },
)
```

### 2.3 混合颜色

```dart
// 灰度效果
Image.network(
  'https://picsum.photos/200',
  color: Colors.grey,
  colorBlendMode: BlendMode.saturation,
)

// 遮罩效果
Image.network(
  'https://picsum.photos/200',
  color: Colors.blue.withValues(alpha: 0.3),
  colorBlendMode: BlendMode.overlay,
)

// 暗化效果
Image.network(
  'https://picsum.photos/200',
  color: Colors.black.withValues(alpha: 0.5),
  colorBlendMode: BlendMode.darken,
)
```

### 2.4 圆角图片

```dart
// 方法1：ClipRRect
ClipRRect(
  borderRadius: BorderRadius.circular(16),
  child: Image.network(
    'https://picsum.photos/200',
    fit: BoxFit.cover,
  ),
)

// 方法2：Container
Container(
  width: 200,
  height: 150,
  decoration: BoxDecoration(
    borderRadius: BorderRadius.circular(16),
    image: DecorationImage(
      image: NetworkImage('https://picsum.photos/200'),
      fit: BoxFit.cover,
    ),
  ),
)
```

### 2.5 资产图片配置

```yaml
# pubspec.yaml
flutter:
  assets:
    - images/
    - images/icons/
```

```dart
// 加载资产图片
Image.asset('images/logo.png')

// 带参数
Image.asset(
  'images/logo.png',
  width: 100,
  height: 100,
  fit: BoxFit.contain,
)

// 指定包名
Image.asset(
  'assets/icon.png',
  package: 'my_package',
)
```

### 2.6 图片重复

```dart
Container(
  width: 200,
  height: 100,
  decoration: BoxDecoration(
    image: DecorationImage(
      image: NetworkImage('https://picsum.photos/50'),
      repeat: ImageRepeat.repeat,
    ),
  ),
)
```

## 3. 高级用法

### 3.1 缓存网络图片

```dart
// 内置缓存
Image.network(
  'https://picsum.photos/200',
  cacheWidth: 200,  // 缓存宽度
  cacheHeight: 200, // 缓存高度
)

// 使用 cached_network_image 包（推荐）
// CachedNetworkImage(
//   imageUrl: 'https://picsum.photos/200',
//   placeholder: (context, url) => CircularProgressIndicator(),
//   errorWidget: (context, url, error) => Icon(Icons.error),
//   fadeOutDuration: Duration(milliseconds: 300),
//   fadeInDuration: Duration(milliseconds: 500),
// )
```

### 3.2 图片选择器

```dart
// 使用 image_picker 包
// final ImagePicker _picker = ImagePicker();
// 
// // 拍照
// final XFile? photo = await _picker.pickImage(source: ImageSource.camera);
// 
// // 从相册选择
// final XFile? image = await _picker.pickImage(source: ImageSource.gallery);
// 
// // 显示选中的图片
// if (image != null) {
//   Image.file(File(image.path));
// }
```

### 3.3 图片预览组件

```dart
class ImagePreview extends StatelessWidget {
  final String url;
  final double aspectRatio;

  const ImagePreview({
    required this.url,
    this.aspectRatio = 16 / 9,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => _showFullScreen(context),
      child: AspectRatio(
        aspectRatio: aspectRatio,
        child: Image.network(
          url,
          fit: BoxFit.cover,
          loadingBuilder: (context, child, progress) {
            if (progress == null) return child;
            return Center(child: CircularProgressIndicator());
          },
          errorBuilder: (context, error, stackTrace) {
            return Container(
              color: Colors.grey[200],
              child: Icon(Icons.broken_image, size: 48),
            );
          },
        ),
      ),
    );
  }

  void _showFullScreen(BuildContext context) {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (_) => Scaffold(
          backgroundColor: Colors.black,
          body: GestureDetector(
            onTap: () => Navigator.pop(context),
            child: Center(
              child: InteractiveViewer(
                child: Image.network(url),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

### 3.4 网格图片展示

```dart
class ImageGrid extends StatelessWidget {
  final List<String> urls;

  const ImageGrid({required this.urls, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      shrinkWrap: true,
      physics: NeverScrollableScrollPhysics(),
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        mainAxisSpacing: 4,
        crossAxisSpacing: 4,
      ),
      itemCount: urls.length,
      itemBuilder: (context, index) {
        return Image.network(
          urls[index],
          fit: BoxFit.cover,
        );
      },
    );
  }
}
```

### 3.5 图片轮播

```dart
class ImageCarousel extends StatefulWidget {
  final List<String> urls;

  const ImageCarousel({required this.urls, Key? key}) : super(key: key);

  @override
  _ImageCarouselState createState() => _ImageCarouselState();
}

class _ImageCarouselState extends State<ImageCarousel> {
  final PageController _controller = PageController();
  int _currentIndex = 0;

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Stack(
      alignment: Alignment.bottomCenter,
      children: [
        AspectRatio(
          aspectRatio: 16 / 9,
          child: PageView.builder(
            controller: _controller,
            onPageChanged: (index) {
              setState(() => _currentIndex = index);
            },
            itemCount: widget.urls.length,
            itemBuilder: (context, index) {
              return Image.network(
                widget.urls[index],
                fit: BoxFit.cover,
              );
            },
          ),
        ),
        Positioned(
          bottom: 8,
          child: Row(
            children: List.generate(
              widget.urls.length,
              (index) => Container(
                margin: EdgeInsets.symmetric(horizontal: 4),
                width: 8,
                height: 8,
                decoration: BoxDecoration(
                  shape: BoxShape.circle,
                  color: index == _currentIndex
                      ? Colors.white
                      : Colors.white.withValues(alpha: 0.5),
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

## 4. 性能优化

### 4.1 设置缓存尺寸

```dart
// 限制缓存大小，减少内存占用
Image.network(
  'https://picsum.photos/1000/1000',
  cacheWidth: 300,  // 只缓存 300 像素宽
  cacheHeight: 300,
)
```

### 4.2 使用 AssetImage

```dart
// 资产图片比网络图片更快
Image.asset('images/logo.png')
```

### 4.3 延迟加载

```dart
// 使用 ListView.builder 自动处理
ListView.builder(
  itemCount: urls.length,
  itemBuilder: (context, index) {
    return Image.network(urls[index]);
  },
)
```

### 4.4 图片压缩

```dart
// 使用 image 包压缩图片
// final image = img.decodeImage(file.readAsBytesSync())!;
// final compressed = img.copyResize(image, width: 800);
// final bytes = img.encodeJpg(compressed, quality: 85);
```

## 5. 常见问题与解决方案

### 问题1：图片模糊

```dart
// ❌ 问题：大图缩放显示模糊
Image.network(
  'https://picsum.photos/1000',
  width: 100,
)

// ✅ 解决方案：设置合适的 filterQuality
Image.network(
  'https://picsum.photos/1000',
  width: 100,
  filterQuality: FilterQuality.high,
)

// 或使用合适的缓存尺寸
Image.network(
  'https://picsum.photos/100',
  width: 100,
  cacheWidth: 100,
)
```

### 问题2：图片溢出

```dart
// ❌ 问题：图片超出容器
Container(
  width: 100,
  child: Image.network('https://picsum.photos/200'),
)

// ✅ 解决方案：使用 fit 属性
Container(
  width: 100,
  height: 100,
  child: Image.network(
    'https://picsum.photos/200',
    fit: BoxFit.cover,
  ),
)
```

### 问题3：网络图片加载慢

```dart
// 使用占位符
Image.network(
  'https://picsum.photos/400/200',
  loadingBuilder: (context, child, progress) {
    if (progress == null) return child;
    return Container(
      color: Colors.grey[200],
      child: Center(child: CircularProgressIndicator()),
    );
  },
)

// 或使用 cached_network_image 包
```

### 问题4：图片不显示

```dart
// 检查 asset 路径配置
// pubspec.yaml 必须正确声明
flutter:
  assets:
    - images/

// 使用正确的路径
Image.asset('images/logo.png')  // 注意：不带 assets/
```

## 6. 其他图片组件

### 6.1 CircleAvatar

```dart
CircleAvatar(
  radius: 40,
  backgroundImage: NetworkImage('https://picsum.photos/100'),
  child: Text('A'),  // 加载失败时显示
)

// 带边框
CircleAvatar(
  radius: 40,
  backgroundColor: Colors.blue,
  child: CircleAvatar(
    radius: 36,
    backgroundImage: NetworkImage('https://picsum.photos/100'),
  ),
)
```

### 6.2 FadeInImage

```dart
// 淡入效果
FadeInImage.assetNetwork(
  placeholder: 'images/placeholder.png',
  image: 'https://picsum.photos/400/200',
  fit: BoxFit.cover,
)

// 内存占位符
FadeInImage.memoryNetwork(
  placeholder: kTransparentImage,
  image: 'https://picsum.photos/400/200',
)
```

### 6.3 Icon

```dart
Icon(Icons.favorite, size: 24, color: Colors.red)

// 使用图片图标
ImageIcon(
  NetworkImage('https://example.com/icon.png'),
  size: 24,
  color: Colors.blue,
)
```

## 7. 最佳实践

### 7.1 统一图片组件

```dart
class AppImage extends StatelessWidget {
  final String? url;
  final String? asset;
  final double? width;
  final double? height;
  final BoxFit fit;

  const AppImage({
    this.url,
    this.asset,
    this.width,
    this.height,
    this.fit = BoxFit.cover,
    Key? key,
  }) : assert(url != null || asset != null), super(key: key);

  @override
  Widget build(BuildContext context) {
    final image = url != null
        ? Image.network(url!, fit: fit)
        : Image.asset(asset!, fit: fit);

    return image;
  }
}
```

### 7.2 响应式图片

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final size = constraints.maxWidth > 600 ? 400.0 : 200.0;
    return Image.network(
      'https://picsum.photos/$size',
      width: size,
      height: size * 0.75,
      fit: BoxFit.cover,
    );
  },
)
```

### 7.3 图片配置类

```dart
class ImageConfig {
  static const String baseUrl = 'https://example.com/images/';
  static const String placeholder = 'images/placeholder.png';
  static const String errorImage = 'images/error.png';

  static String fullUrl(String path) => baseUrl + path;
}

Image.network(
  ImageConfig.fullUrl('banner.png'),
  errorBuilder: (_, __, ___) => Image.asset(ImageConfig.errorImage),
)
```

## 总结

Image 是 Flutter 中显示图片的核心组件：

**核心要点**：
1. 四种图片来源：network、asset、file、memory
2. BoxFit 控制填充方式
3. 使用 loadingBuilder 和 errorBuilder 处理状态
4. 合理设置缓存尺寸优化性能

**常用场景**：
- 网络图片展示
- 本地资产图片
- 用户头像
- 图片网格/轮播

**最佳实践**：
1. 始终处理加载和错误状态
2. 设置合适的缓存尺寸
3. 使用 fit 属性防止溢出
4. 大图使用 cacheWidth/cacheHeight 限制内存

---

*最后更新: 2026-02-18*

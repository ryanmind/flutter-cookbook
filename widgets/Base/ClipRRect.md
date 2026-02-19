# Flutter ClipRRect 组件用法详解

## 简介

ClipRRect 是 Flutter 中用于将子组件裁剪为圆角矩形的裁剪组件。它使用圆角矩形边界裁剪子组件，是最常用的裁剪组件之一。

## 1. 初级用法

### 1.1 基本概念

ClipRRect 的作用：
- 将子组件裁剪为圆角矩形
- 可单独设置四个角的圆角
- 常用于卡片、图片、按钮等场景

### 1.2 基本语法

```dart
ClipRRect(
  borderRadius: BorderRadius.circular(12),
  child: Container(
    width: 200,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `borderRadius` | `BorderRadius?` | 圆角半径，默认无圆角 |
| `child` | `Widget?` | 子组件 |
| `clipper` | `CustomClipper<RRect>?` | 自定义裁剪器 |
| `clipBehavior` | `Clip` | 裁剪行为，默认 Clip.antiAlias |

### 1.4 基础示例

#### 统一圆角

```dart
ClipRRect(
  borderRadius: BorderRadius.circular(16),
  child: Container(
    width: 200,
    height: 100,
    color: Colors.blue,
    child: Center(child: Text('圆角矩形')),
  ),
)
```

#### 图片圆角

```dart
ClipRRect(
  borderRadius: BorderRadius.circular(12),
  child: Image.network(
    'https://picsum.photos/200',
    width: 200,
    height: 150,
    fit: BoxFit.cover,
  ),
)
```

## 2. 中级用法

### 2.1 不同圆角设置

```dart
// 只设置部分角
ClipRRect(
  borderRadius: BorderRadius.only(
    topLeft: Radius.circular(24),
    bottomRight: Radius.circular(24),
  ),
  child: Container(
    width: 200,
    height: 100,
    color: Colors.green,
  ),
)

// 四个角不同圆角
ClipRRect(
  borderRadius: BorderRadius.only(
    topLeft: Radius.circular(20),
    topRight: Radius.circular(10),
    bottomLeft: Radius.circular(5),
    bottomRight: Radius.circular(0),
  ),
  child: Container(
    width: 200,
    height: 100,
    color: Colors.orange,
  ),
)
```

### 2.2 常见布局场景

#### 卡片效果

```dart
ClipRRect(
  borderRadius: BorderRadius.circular(12),
  child: Container(
    color: Colors.white,
    child: Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Image.network(
          'https://picsum.photos/300/150',
          height: 120,
          width: double.infinity,
          fit: BoxFit.cover,
        ),
        Padding(
          padding: EdgeInsets.all(16),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              Text('卡片标题', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
              SizedBox(height: 8),
              Text('卡片描述内容'),
            ],
          ),
        ),
      ],
    ),
  ),
)
```

#### 带阴影的圆角卡片

```dart
Material(
  elevation: 4,
  borderRadius: BorderRadius.circular(12),
  child: ClipRRect(
    borderRadius: BorderRadius.circular(12),
    child: Container(
      color: Colors.white,
      padding: EdgeInsets.all(16),
      child: Text('带阴影的圆角卡片'),
    ),
  ),
)
```

#### 圆形头像组

```dart
Row(
  children: List.generate(5, (index) {
    return Padding(
      padding: EdgeInsets.only(right: 8),
      child: ClipRRect(
        borderRadius: BorderRadius.circular(8),
        child: Image.network(
          'https://i.pravatar.cc/50?img=$index',
          width: 50,
          height: 50,
          fit: BoxFit.cover,
        ),
      ),
    );
  }),
)
```

### 2.3 配合 Card 使用

```dart
// Card 默认有圆角，但内部内容不会自动裁剪
Card(
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(16),
  ),
  clipBehavior: Clip.antiAlias,
  child: Column(
    children: [
      Image.network('https://picsum.photos/300/150', height: 120, fit: BoxFit.cover),
      ListTile(title: Text('卡片标题')),
    ],
  ),
)

// 或使用 ClipRRect 包裹
Card(
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(16),
  ),
  child: ClipRRect(
    borderRadius: BorderRadius.circular(16),
    child: Column(
      children: [
        Image.network('https://picsum.photos/300/150', height: 120, fit: BoxFit.cover),
        ListTile(title: Text('卡片标题')),
      ],
    ),
  ),
)
```

### 2.4 底部弹窗

```dart
showModalBottomSheet(
  context: context,
  backgroundColor: Colors.transparent,
  builder: (context) {
    return ClipRRect(
      borderRadius: BorderRadius.vertical(top: Radius.circular(24)),
      child: Container(
        color: Colors.white,
        padding: EdgeInsets.all(16),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Container(
              width: 40,
              height: 4,
              decoration: BoxDecoration(
                color: Colors.grey[300],
                borderRadius: BorderRadius.circular(2),
              ),
            ),
            SizedBox(height: 16),
            Text('底部弹窗内容'),
          ],
        ),
      ),
    );
  },
)
```

## 3. 高级用法

### 3.1 自定义裁剪

```dart
class RoundedClipper extends CustomClipper<RRect> {
  final double radius;

  RoundedClipper(this.radius);

  @override
  RRect getClip(Size size) {
    return RRect.fromRectAndCorners(
      Rect.fromLTWH(0, 0, size.width, size.height),
      topLeft: Radius.circular(radius),
      topRight: Radius.circular(radius * 2),
      bottomLeft: Radius.circular(radius * 2),
      bottomRight: Radius.circular(radius),
    );
  }

  @override
  bool shouldReclip(RoundedClipper oldClipper) {
    return radius != oldClipper.radius;
  }
}

// 使用
ClipRRect(
  clipper: RoundedClipper(20),
  child: Container(width: 200, height: 100, color: Colors.blue),
)
```

### 3.2 动态圆角动画

```dart
class AnimatedBorderRadius extends StatefulWidget {
  @override
  State<AnimatedBorderRadius> createState() => _AnimatedBorderRadiusState();
}

class _AnimatedBorderRadiusState extends State<AnimatedBorderRadius> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => setState(() => _expanded = !_expanded),
      child: AnimatedContainer(
        duration: Duration(milliseconds: 300),
        width: _expanded ? 200 : 100,
        height: _expanded ? 200 : 100,
        decoration: BoxDecoration(
          color: Colors.blue,
          borderRadius: BorderRadius.circular(_expanded ? 100 : 16),
        ),
        child: Center(child: Text('点击变换')),
      ),
    );
  }
}
```

### 3.3 嵌套裁剪

```dart
ClipRRect(
  borderRadius: BorderRadius.circular(24),
  child: Container(
    color: Colors.grey[200],
    padding: EdgeInsets.all(16),
    child: ClipRRect(
      borderRadius: BorderRadius.circular(12),
      child: Container(
        color: Colors.blue,
        padding: EdgeInsets.all(16),
        child: Text('嵌套圆角'),
      ),
    ),
  ),
)
```

### 3.4 图片卡片列表

```dart
class ImageCardList extends StatelessWidget {
  final List<String> imageUrls;

  const ImageCardList({required this.imageUrls});

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      scrollDirection: Axis.horizontal,
      itemCount: imageUrls.length,
      itemBuilder: (context, index) {
        return Container(
          width: 200,
          margin: EdgeInsets.symmetric(horizontal: 8),
          child: ClipRRect(
            borderRadius: BorderRadius.circular(16),
            child: Stack(
              fit: StackFit.expand,
              children: [
                Image.network(
                  imageUrls[index],
                  fit: BoxFit.cover,
                ),
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
                      '图片 ${index + 1}',
                      style: TextStyle(color: Colors.white),
                    ),
                  ),
                ),
              ],
            ),
          ),
        );
      },
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：圆角不生效

```dart
// 问题：Container 的 decoration 不会自动裁剪子组件
Container(
  decoration: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(16),
  ),
  child: Image.network('url'),  // 图片没有圆角
)

// 解决：使用 ClipRRect 或 clipBehavior
Container(
  decoration: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(16),
  ),
  clipBehavior: Clip.antiAlias,  // 添加这个
  child: Image.network('url'),
)

// 或使用 ClipRRect
ClipRRect(
  borderRadius: BorderRadius.circular(16),
  child: Container(
    color: Colors.blue,
    child: Image.network('url'),
  ),
)
```

### 问题2：阴影和圆角冲突

```dart
// 问题：ClipRRect 会裁剪阴影
ClipRRect(
  borderRadius: BorderRadius.circular(16),
  child: Container(
    decoration: BoxDecoration(
      color: Colors.white,
      boxShadow: [BoxShadow(blurRadius: 10, color: Colors.black26)],
    ),
    child: Text('阴影被裁剪'),
  ),
)

// 解决：使用 Material 或分开处理
Material(
  elevation: 4,
  borderRadius: BorderRadius.circular(16),
  child: ClipRRect(
    borderRadius: BorderRadius.circular(16),
    child: Container(
      color: Colors.white,
      child: Text('正确的阴影'),
    ),
  ),
)
```

### 问题3：圆角大小选择

```dart
// 常用圆角大小参考
BorderRadius.circular(4)   // 小圆角，适合按钮、输入框
BorderRadius.circular(8)   // 中圆角，适合列表项
BorderRadius.circular(12)  // 标准圆角，适合卡片
BorderRadius.circular(16)  // 大圆角，适合弹窗
BorderRadius.circular(24)  // 超大圆角，适合底部弹窗
```

## 5. ClipRRect vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **ClipRRect** | 圆角矩形裁剪 | 最常用，性能好 |
| **ClipRect** | 矩形裁剪 | 无圆角 |
| **ClipOval** | 椭圆裁剪 | 圆形场景 |
| **ClipPath** | 自定义路径 | 任意形状 |
| **Container** | 带装饰容器 | clipBehavior 需设置 |

### 选择建议

```dart
// 需要圆角 → ClipRRect
ClipRRect(
  borderRadius: BorderRadius.circular(12),
  child: Container(...),
)

// 简单场景 → Container + clipBehavior
Container(
  decoration: BoxDecoration(
    borderRadius: BorderRadius.circular(12),
  ),
  clipBehavior: Clip.antiAlias,
  child: ...,
)

// 需要阴影 → Material + ClipRRect
Material(
  elevation: 4,
  borderRadius: BorderRadius.circular(12),
  child: ClipRRect(...),
)
```

## 6. 最佳实践

### 6.1 统一圆角常量

```dart
abstract class AppRadius {
  static const double xs = 4.0;
  static const double sm = 8.0;
  static const double md = 12.0;
  static const double lg = 16.0;
  static const double xl = 24.0;
  
  static BorderRadius get small => BorderRadius.circular(sm);
  static BorderRadius get medium => BorderRadius.circular(md);
  static BorderRadius get large => BorderRadius.circular(lg);
}

// 使用
ClipRRect(
  borderRadius: AppRadius.medium,
  child: Container(...),
)
```

### 6.2 封装圆角卡片

```dart
class RoundedCard extends StatelessWidget {
  final Widget child;
  final double radius;
  final Color? color;
  final double? elevation;

  const RoundedCard({
    required this.child,
    this.radius = 12,
    this.color,
    this.elevation,
  });

  @override
  Widget build(BuildContext context) {
    return Material(
      elevation: elevation ?? 0,
      borderRadius: BorderRadius.circular(radius),
      child: ClipRRect(
        borderRadius: BorderRadius.circular(radius),
        child: Container(
          color: color ?? Colors.white,
          child: child,
        ),
      ),
    );
  }
}
```

### 6.3 性能优化

```dart
// 静态内容使用 const BorderRadius
ClipRRect(
  borderRadius: const BorderRadius.all(Radius.circular(12)),
  child: Container(...),
)

// 动态圆角才创建新对象
ClipRRect(
  borderRadius: BorderRadius.circular(dynamicRadius),
  child: Container(...),
)
```

## 总结

ClipRRect 是 Flutter 中最常用的裁剪组件：

**核心要点**：
1. 使用 borderRadius 设置圆角
2. 支持四个角单独设置
3. 需要裁剪子组件时使用

**最佳实践**：
1. 定义统一的圆角常量
2. 需要阴影时配合 Material
3. 使用 clipBehavior 简化代码

**常见场景**：
- 圆角卡片
- 圆角图片
- 底部弹窗
- 列表项

---

*最后更新: 2026-02-19*

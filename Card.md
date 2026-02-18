# Flutter Card 组件用法详解

## 简介

Card 是 Flutter 中 Material Design 风格的卡片组件，用于将相关内容组织在一起，提供视觉上的层次感。Material 3 中，Card 有了更圆润的边角和更现代的视觉风格。

## 1. 初级用法

### 1.1 基本概念

Card 的核心特点：
- 提供容器和视觉层次
- 支持圆角、阴影、边框等装饰
- Material 3 提供多种变体
- 可响应点击事件

### 1.2 基本语法

```dart
Card(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('卡片内容'),
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `color` | `Color?` | - | 卡片背景色 |
| `shadowColor` | `Color?` | - | 阴影颜色 |
| `surfaceTintColor` | `Color?` | - | 表面着色 |
| `elevation` | `double?` | - | 阴影高度 |
| `shape` | `ShapeBorder?` | - | 形状边框 |
| `borderOnForeground` | `bool` | `true` | 边框是否在前 |
| `margin` | `EdgeInsetsGeometry?` | - | 外边距 |
| `clipBehavior` | `Clip` | `Clip.none` | 裁剪行为 |
| `child` | `Widget?` | - | 子组件 |
| `semanticContainer` | `bool` | `true` | 语义容器 |

### 1.4 基础示例

#### 简单卡片

```dart
Card(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('这是一个简单的卡片'),
  ),
)
```

#### 带标题的卡片

```dart
Card(
  child: Column(
    mainAxisSize: MainAxisSize.min,
    children: [
      ListTile(
        leading: Icon(Icons.album),
        title: Text('卡片标题'),
        subtitle: Text('卡片副标题'),
      ),
      Padding(
        padding: EdgeInsets.all(16),
        child: Text('卡片内容描述'),
      ),
      OverflowBar(
        alignment: MainAxisAlignment.end,
        children: [
          TextButton(child: Text('取消'), onPressed: () {}),
          TextButton(child: Text('确定'), onPressed: () {}),
        ],
      ),
    ],
  ),
)
```

## 2. 中级用法

### 2.1 Material 3 Card 变体

Material 3 提供了三种卡片变体：

```dart
// 1. Card - 基础卡片，有轻微阴影
Card(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('基础卡片'),
  ),
)

// 2. Card.outlined - 轮廓卡片，无阴影
Card.outlined(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('轮廓卡片'),
  ),
)

// 3. Card.filled - 填充卡片，有背景色
Card.filled(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('填充卡片'),
  ),
)
```

### 2.2 自定义样式

```dart
Card(
  color: Colors.blue[50],
  shadowColor: Colors.blue[200],
  elevation: 8,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(16),
    side: BorderSide(color: Colors.blue, width: 1),
  ),
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('自定义样式卡片'),
  ),
)
```

### 2.3 圆形图片卡片

```dart
Card(
  clipBehavior: Clip.antiAlias,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(16),
  ),
  child: Column(
    children: [
      Image.network(
        'https://picsum.photos/400/200',
        height: 150,
        width: double.infinity,
        fit: BoxFit.cover,
      ),
      Padding(
        padding: EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              '图片标题',
              style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
            ),
            SizedBox(height: 8),
            Text('图片描述内容'),
          ],
        ),
      ),
    ],
  ),
)
```

### 2.4 可点击卡片

```dart
Card(
  clipBehavior: Clip.antiAlias,
  child: InkWell(
    onTap: () {
      print('卡片被点击');
    },
    child: Padding(
      padding: EdgeInsets.all(16),
      child: Row(
        children: [
          Icon(Icons.info, size: 40),
          SizedBox(width: 16),
          Expanded(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text('点击我', style: TextStyle(fontWeight: FontWeight.bold)),
                Text('点击卡片查看详情'),
              ],
            ),
          ),
          Icon(Icons.arrow_forward_ios),
        ],
      ),
    ),
  ),
)
```

### 2.5 列表卡片

```dart
Column(
  children: [
    Card(
      child: ListTile(
        leading: CircleAvatar(child: Icon(Icons.person)),
        title: Text('用户1'),
        subtitle: Text('用户描述'),
        trailing: Icon(Icons.more_vert),
        onTap: () {},
      ),
    ),
    Card(
      child: ListTile(
        leading: CircleAvatar(child: Icon(Icons.person)),
        title: Text('用户2'),
        subtitle: Text('用户描述'),
        trailing: Icon(Icons.more_vert),
        onTap: () {},
      ),
    ),
    Card(
      child: ListTile(
        leading: CircleAvatar(child: Icon(Icons.person)),
        title: Text('用户3'),
        subtitle: Text('用户描述'),
        trailing: Icon(Icons.more_vert),
        onTap: () {},
      ),
    ),
  ],
)
```

## 3. 高级用法

### 3.1 产品卡片

```dart
class ProductCard extends StatelessWidget {
  final String title;
  final String description;
  final String price;
  final String imageUrl;

  const ProductCard({
    required this.title,
    required this.description,
    required this.price,
    required this.imageUrl,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Card(
      clipBehavior: Clip.antiAlias,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(12),
      ),
      child: InkWell(
        onTap: () {
          // 跳转到详情页
        },
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            AspectRatio(
              aspectRatio: 16 / 9,
              child: Image.network(
                imageUrl,
                fit: BoxFit.cover,
              ),
            ),
            Padding(
              padding: EdgeInsets.all(12),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    title,
                    style: TextStyle(
                      fontSize: 16,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                  SizedBox(height: 4),
                  Text(
                    description,
                    style: TextStyle(
                      color: Colors.grey[600],
                      fontSize: 14,
                    ),
                    maxLines: 2,
                    overflow: TextOverflow.ellipsis,
                  ),
                  SizedBox(height: 8),
                  Row(
                    mainAxisAlignment: MainAxisAlignment.spaceBetween,
                    children: [
                      Text(
                        '¥$price',
                        style: TextStyle(
                          fontSize: 18,
                          fontWeight: FontWeight.bold,
                          color: Colors.red,
                        ),
                      ),
                      IconButton(
                        icon: Icon(Icons.add_shopping_cart),
                        onPressed: () {},
                      ),
                    ],
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}

// 使用示例
ProductCard(
  title: '商品名称',
  description: '商品描述，这是一段很长的描述文字',
  price: '99.00',
  imageUrl: 'https://picsum.photos/400/200',
)
```

### 3.2 文章卡片

```dart
class ArticleCard extends StatelessWidget {
  final String title;
  final String summary;
  final String author;
  final String date;
  final String? imageUrl;

  const ArticleCard({
    required this.title,
    required this.summary,
    required this.author,
    required this.date,
    this.imageUrl,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
      child: Padding(
        padding: EdgeInsets.all(16),
        child: Row(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    title,
                    style: TextStyle(
                      fontSize: 16,
                      fontWeight: FontWeight.bold,
                    ),
                    maxLines: 2,
                    overflow: TextOverflow.ellipsis,
                  ),
                  SizedBox(height: 8),
                  Text(
                    summary,
                    style: TextStyle(color: Colors.grey[600]),
                    maxLines: 2,
                    overflow: TextOverflow.ellipsis,
                  ),
                  SizedBox(height: 12),
                  Row(
                    children: [
                      Text(
                        author,
                        style: TextStyle(
                          color: Colors.blue,
                          fontSize: 12,
                        ),
                      ),
                      SizedBox(width: 8),
                      Text(
                        '·',
                        style: TextStyle(color: Colors.grey),
                      ),
                      SizedBox(width: 8),
                      Text(
                        date,
                        style: TextStyle(
                          color: Colors.grey,
                          fontSize: 12,
                        ),
                      ),
                    ],
                  ),
                ],
              ),
            ),
            if (imageUrl != null) ...[
              SizedBox(width: 16),
              ClipRRect(
                borderRadius: BorderRadius.circular(8),
                child: Image.network(
                  imageUrl!,
                  width: 80,
                  height: 80,
                  fit: BoxFit.cover,
                ),
              ),
            ],
          ],
        ),
      ),
    );
  }
}
```

### 3.3 统计卡片

```dart
class StatCard extends StatelessWidget {
  final String title;
  final String value;
  final String change;
  final bool isPositive;
  final IconData icon;

  const StatCard({
    required this.title,
    required this.value,
    required this.change,
    required this.isPositive,
    required this.icon,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Padding(
        padding: EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceBetween,
              children: [
                Icon(icon, color: Colors.blue),
                Container(
                  padding: EdgeInsets.symmetric(horizontal: 8, vertical: 4),
                  decoration: BoxDecoration(
                    color: isPositive ? Colors.green[50] : Colors.red[50],
                    borderRadius: BorderRadius.circular(4),
                  ),
                  child: Text(
                    change,
                    style: TextStyle(
                      color: isPositive ? Colors.green : Colors.red,
                      fontSize: 12,
                    ),
                  ),
                ),
              ],
            ),
            SizedBox(height: 16),
            Text(
              value,
              style: TextStyle(
                fontSize: 24,
                fontWeight: FontWeight.bold,
              ),
            ),
            SizedBox(height: 4),
            Text(
              title,
              style: TextStyle(
                color: Colors.grey[600],
                fontSize: 14,
              ),
            ),
          ],
        ),
      ),
    );
  }
}

// 使用示例
Row(
  children: [
    Expanded(
      child: StatCard(
        title: '总收入',
        value: '¥12,345',
        change: '+12.5%',
        isPositive: true,
        icon: Icons.attach_money,
      ),
    ),
    SizedBox(width: 16),
    Expanded(
      child: StatCard(
        title: '订单数',
        value: '234',
        change: '+8.2%',
        isPositive: true,
        icon: Icons.shopping_bag,
      ),
    ),
  ],
)
```

### 3.4 可展开卡片

```dart
class ExpandableCard extends StatefulWidget {
  final String title;
  final String summary;
  final String content;

  const ExpandableCard({
    required this.title,
    required this.summary,
    required this.content,
    Key? key,
  }) : super(key: key);

  @override
  _ExpandableCardState createState() => _ExpandableCardState();
}

class _ExpandableCardState extends State<ExpandableCard> {
  bool _isExpanded = false;

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Column(
        children: [
          ListTile(
            title: Text(widget.title),
            subtitle: Text(widget.summary),
            trailing: IconButton(
              icon: Icon(_isExpanded ? Icons.expand_less : Icons.expand_more),
              onPressed: () {
                setState(() {
                  _isExpanded = !_isExpanded;
                });
              },
            ),
          ),
          if (_isExpanded)
            Padding(
              padding: EdgeInsets.all(16),
              child: Text(widget.content),
            ),
        ],
      ),
    );
  }
}
```

### 3.5 卡片网格

```dart
GridView.count(
  crossAxisCount: 2,
  padding: EdgeInsets.all(16),
  mainAxisSpacing: 16,
  crossAxisSpacing: 16,
  childAspectRatio: 0.75,
  children: List.generate(6, (index) {
    return Card(
      clipBehavior: Clip.antiAlias,
      child: Column(
        children: [
          Expanded(
            child: Container(
              color: Colors.primaries[index % Colors.primaries.length],
              child: Center(
                child: Icon(
                  Icons.image,
                  size: 48,
                  color: Colors.white,
                ),
              ),
            ),
          ),
          Padding(
            padding: EdgeInsets.all(8),
            child: Text('卡片 ${index + 1}'),
          ),
        ],
      ),
    );
  }),
)
```

## 4. 性能优化

### 4.1 使用 const 构造函数

```dart
// 静态内容使用 const
Card(
  child: const Padding(
    padding: EdgeInsets.all(16),
    child: Text('静态内容'),
  ),
)
```

### 4.2 避免过度使用阴影

```dart
// 高 elevation 会影响性能
Card(
  elevation: 2,  // 适度的阴影
  child: Container(),
)

// 或使用 outlined 卡片（无阴影）
Card.outlined(
  child: Container(),
)
```

### 4.3 复用卡片样式

```dart
class AppCard extends StatelessWidget {
  final Widget child;
  final VoidCallback? onTap;

  const AppCard({
    required this.child,
    this.onTap,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Card(
      clipBehavior: Clip.antiAlias,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(12),
      ),
      child: InkWell(
        onTap: onTap,
        child: child,
      ),
    );
  }
}
```

## 5. 常见问题与解决方案

### 问题1：Card 内容没有边距

```dart
// ❌ 问题：内容贴边
Card(
  child: Text('内容贴边'),
)

// ✅ 解决方案：添加 Padding
Card(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('内容有边距'),
  ),
)
```

### 问题2：图片溢出圆角

```dart
// ❌ 问题：图片超出圆角边界
Card(
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(16),
  ),
  child: Image.network('url'),  // 图片不会遵循圆角
)

// ✅ 解决方案：使用 clipBehavior
Card(
  clipBehavior: Clip.antiAlias,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(16),
  ),
  child: Image.network('url'),  // 图片会被裁剪
)
```

### 问题3：Card 点击无反馈

```dart
// ❌ 问题：Card 本身不可点击
Card(
  child: Text('无法点击'),
)

// ✅ 解决方案：使用 InkWell
Card(
  clipBehavior: Clip.antiAlias,
  child: InkWell(
    onTap: () {},
    child: Padding(
      padding: EdgeInsets.all(16),
      child: Text('可点击'),
    ),
  ),
)

// 或使用 GestureDetector（无水波纹效果）
Card(
  child: GestureDetector(
    onTap: () {},
    child: Padding(
      padding: EdgeInsets.all(16),
      child: Text('可点击'),
    ),
  ),
)
```

### 问题4：Card 嵌套问题

```dart
// ❌ 问题：Card 嵌套 Card 样式混乱
Card(
  child: Card(
    child: Text('嵌套卡片'),
  ),
)

// ✅ 解决方案：使用 Container 或其他组件替代内层
Card(
  child: Container(
    decoration: BoxDecoration(
      color: Colors.grey[100],
      borderRadius: BorderRadius.circular(8),
    ),
    child: Text('内容'),
  ),
)
```

## 6. Card vs Container 对比

| 特性 | Card | Container |
|------|------|-----------|
| 阴影 | 内置 Material 阴影 | 需要手动设置 boxShadow |
| 默认圆角 | Material 3 默认圆角 | 需要手动设置 |
| 点击效果 | 配合 InkWell | 需要手动实现 |
| 语义 | 语义容器支持 | 无 |

```dart
// Card - Material 风格
Card(
  child: Text('Card'),
)

// Container - 自定义容器
Container(
  decoration: BoxDecoration(
    color: Colors.white,
    borderRadius: BorderRadius.circular(8),
    boxShadow: [
      BoxShadow(
        color: Colors.black12,
        blurRadius: 4,
      ),
    ],
  ),
  child: Text('Container'),
)
```

## 7. 最佳实践

### 7.1 统一卡片样式

```dart
class AppTheme {
  static RoundedRectangleBorder get cardShape =>
      RoundedRectangleBorder(borderRadius: BorderRadius.circular(12));
  
  static double get cardElevation => 2;
  static EdgeInsets get cardPadding => EdgeInsets.all(16);
}

Card(
  shape: AppTheme.cardShape,
  elevation: AppTheme.cardElevation,
  child: Padding(
    padding: AppTheme.cardPadding,
    child: Text('统一风格卡片'),
  ),
)
```

### 7.2 卡片内容布局

```dart
Card(
  child: Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    mainAxisSize: MainAxisSize.min,
    children: [
      // 头部区域
      Padding(
        padding: EdgeInsets.fromLTRB(16, 16, 16, 8),
        child: Text('标题', style: TextStyle(fontWeight: FontWeight.bold)),
      ),
      // 内容区域
      Padding(
        padding: EdgeInsets.symmetric(horizontal: 16),
        child: Text('内容'),
      ),
      // 操作区域
      Padding(
        padding: EdgeInsets.all(8),
        child: Row(
          mainAxisAlignment: MainAxisAlignment.end,
          children: [
            TextButton(child: Text('取消'), onPressed: () {}),
            TextButton(child: Text('确定'), onPressed: () {}),
          ],
        ),
      ),
    ],
  ),
)
```

### 7.3 响应式卡片

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final isWide = constraints.maxWidth > 600;
    
    return Card(
      child: Padding(
        padding: EdgeInsets.all(isWide ? 24 : 16),
        child: Row(
          children: [
            if (isWide)
              Padding(
                padding: EdgeInsets.only(right: 24),
                child: Icon(Icons.info, size: 48),
              ),
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text('标题'),
                  Text('内容'),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  },
)
```

## 总结

Card 是 Flutter 中展示内容容器的核心组件：

**核心要点**：
1. Material Design 风格的卡片容器
2. Material 3 提供三种变体：默认、outlined、filled
3. 支持圆角、阴影、边框等装饰
4. 配合 InkWell 实现点击效果

**常见场景**：
- 列表项
- 产品卡片
- 文章卡片
- 统计卡片
- 信息展示

**最佳实践**：
1. 使用 clipBehavior 裁剪圆角内容
2. 统一卡片样式
3. 合理设置 elevation
4. 配合 InkWell 实现点击反馈

---

*最后更新: 2026-02-18*

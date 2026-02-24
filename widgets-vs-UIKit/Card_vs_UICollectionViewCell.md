# Card vs UICollectionViewCell 对比学习

本文档记录 Flutter Card 组件与 UIKit UICollectionViewCell 的对比学习要点。

---

## 1. Card 核心概念

### Card 是什么

Card 是 Flutter 中 Material Design 风格的卡片组件，用于展示相关内容的容器。

- Material Design 风格
- 圆角和阴影
- 支持点击交互
- 通常作为内容容器

### Card vs UICollectionViewCell 概念对比

| Flutter Card | UIKit UICollectionViewCell | 说明 |
|--------------|--------------------------|------|
| `Card` | UICollectionViewCell | 卡片容器 |
| `elevation` | `layer.shadow*` | 阴影 |
| `shape` | `layer.cornerRadius` | 形状 |
| `color` | `backgroundColor` | 背景色 |
| `margin` | 布局约束 | 外边距 |
| `clipBehavior` | `clipsToBounds` | 裁剪 |
| `onTap` (InkWell) | `didSelectItemAtIndexPath` | 点击 |

---

## 2. 源码分析

### Card 类定义

```dart
class Card extends StatelessWidget {
  const Card({
    super.key,
    this.color,                    // 背景色
    this.shadowColor,              // 阴影颜色
    this.surfaceTintColor,         // 表面色
    this.elevation,                // 阴影高度
    this.shape,                    // 形状
    this.borderOnForeground = true, // 边框在前
    this.margin,                   // 外边距
    this.clipBehavior,             // 裁剪行为
    this.child,                    // 子组件
    this.semanticContainer = true, // 语义容器
  });
}

// Material 3 的 Card 变体
Card.outlined()    // 边框卡片
Card.filled()      // 填充卡片
Card.elevated()    // 阴影卡片
```

---

## 3. 与 UICollectionViewCell 详细对比

### 3.1 基础卡片

```objc
// Objective-C - UICollectionViewCell
// 需要自定义 Cell 类
@interface CardCell : UICollectionViewCell
@property (nonatomic, strong) UILabel *titleLabel;
@property (nonatomic, strong) UILabel *subtitleLabel;
@end

@implementation CardCell

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        self.contentView.backgroundColor = [UIColor whiteColor];
        self.contentView.layer.cornerRadius = 12;
        self.contentView.layer.shadowColor = [UIColor blackColor].CGColor;
        self.contentView.layer.shadowOffset = CGSizeMake(0, 2);
        self.contentView.layer.shadowRadius = 8;
        self.contentView.layer.shadowOpacity = 0.1;
        
        _titleLabel = [[UILabel alloc] init];
        _titleLabel.font = [UIFont boldSystemFontOfSize:18];
        [self.contentView addSubview:_titleLabel];
        
        _subtitleLabel = [[UILabel alloc] init];
        _subtitleLabel.font = [UIFont systemFontOfSize:14];
        _subtitleLabel.textColor = [UIColor grayColor];
        [self.contentView addSubview:_subtitleLabel];
        
        // 约束...
    }
    return self;
}
@end
```

```dart
// Flutter - Card 直接使用
Card(
  elevation: 4,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(12),
  ),
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('Title', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
        SizedBox(height: 4),
        Text('Subtitle', style: TextStyle(fontSize: 14, color: Colors.grey)),
      ],
    ),
  ),
)
```

### 3.2 可点击卡片

```objc
// Objective-C - 通过 delegate 处理点击
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath {
    CardCell *cell = (CardCell *)[collectionView cellForItemAtIndexPath:indexPath];
    // 处理点击
    [collectionView deselectItemAtIndexPath:indexPath animated:YES];
}

// 或添加 UITapGestureRecognizer
UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(cardTapped:)];
[cell.contentView addGestureRecognizer:tap];
```

```dart
// Flutter - InkWell 或 GestureDetector
Card(
  child: InkWell(
    onTap: () {
      print('Card tapped');
    },
    borderRadius: BorderRadius.circular(12),
    child: Padding(
      padding: EdgeInsets.all(16),
      child: Text('Clickable Card'),
    ),
  ),
)
```

### 3.3 卡片阴影

```objc
// Objective-C
cell.layer.shadowColor = [UIColor blackColor].CGColor;
cell.layer.shadowOffset = CGSizeMake(0, 4);
cell.layer.shadowRadius = 8;
cell.layer.shadowOpacity = 0.15;
cell.layer.shadowPath = [UIBezierPath bezierPathWithRoundedRect:cell.bounds cornerRadius:12].CGPath;
```

```dart
// Flutter
Card(
  elevation: 8,  // 阴影高度
  shadowColor: Colors.black.withValues(alpha: 0.15),
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(12),
  ),
  child: ...,
)
```

### 3.4 Material 3 卡片变体

```dart
// Flutter - Material 3 风格
// 边框卡片
Card.outlined(
  child: Text('Outlined Card'),
)

// 填充卡片
Card.filled(
  color: Colors.blue,
  child: Text('Filled Card'),
)

// 阴影卡片
Card.elevated(
  elevation: 4,
  child: Text('Elevated Card'),
)
```

### 3.5 卡片列表

```objc
// Objective-C - UICollectionView
- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return self.items.count;
}

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    CardCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"CardCell" forIndexPath:indexPath];
    NSDictionary *item = self.items[indexPath.item];
    cell.titleLabel.text = item[@"title"];
    cell.subtitleLabel.text = item[@"subtitle"];
    return cell;
}
```

```dart
// Flutter - ListView + Card
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) {
    final item = items[index];
    return Card(
      margin: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
      child: ListTile(
        title: Text(item['title']),
        subtitle: Text(item['subtitle']),
        trailing: Icon(Icons.chevron_right),
        onTap: () {
          // 处理点击
        },
      ),
    );
  },
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 产品卡片列表
@interface ProductCardCell : UICollectionViewCell
@property (nonatomic, strong) UIImageView *imageView;
@property (nonatomic, strong) UILabel *nameLabel;
@property (nonatomic, strong) UILabel *priceLabel;
@property (nonatomic, strong) UIButton *cartButton;
@end

@implementation ProductCardCell

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        // 卡片样式
        self.contentView.backgroundColor = [UIColor whiteColor];
        self.contentView.layer.cornerRadius = 12;
        self.contentView.layer.shadowColor = [UIColor blackColor].CGColor;
        self.contentView.layer.shadowOffset = CGSizeMake(0, 2);
        self.contentView.layer.shadowRadius = 8;
        self.contentView.layer.shadowOpacity = 0.1;
        self.contentView.layer.shadowPath = [UIBezierPath bezierPathWithRoundedRect:self.contentView.bounds cornerRadius:12].CGPath;
        
        // 图片
        _imageView = [[UIImageView alloc] init];
        _imageView.contentMode = UIViewContentModeScaleAspectFill;
        _imageView.clipsToBounds = YES;
        _imageView.layer.cornerRadius = 12;
        _imageView.translatesAutoresizingMaskIntoConstraints = NO;
        [self.contentView addSubview:_imageView];
        
        // 名称
        _nameLabel = [[UILabel alloc] init];
        _nameLabel.font = [UIFont boldSystemFontOfSize:16];
        _nameLabel.translatesAutoresizingMaskIntoConstraints = NO;
        [self.contentView addSubview:_nameLabel];
        
        // 价格
        _priceLabel = [[UILabel alloc] init];
        _priceLabel.font = [UIFont systemFontOfSize:14];
        _priceLabel.textColor = [UIColor systemBlueColor];
        _priceLabel.translatesAutoresizingMaskIntoConstraints = NO;
        [self.contentView addSubview:_priceLabel];
        
        // 购物车按钮
        _cartButton = [UIButton buttonWithType:UIButtonTypeSystem];
        [_cartButton setImage:[UIImage systemImageNamed:@"cart.badge.plus"] forState:UIControlStateNormal];
        _cartButton.translatesAutoresizingMaskIntoConstraints = NO;
        [self.contentView addSubview:_cartButton];
        
        // 约束
        [NSLayoutConstraint activateConstraints:@[
            [_imageView.topAnchor constraintEqualToAnchor:self.contentView.topAnchor],
            [_imageView.leadingAnchor constraintEqualToAnchor:self.contentView.leadingAnchor],
            [_imageView.trailingAnchor constraintEqualToAnchor:self.contentView.trailingAnchor],
            [_imageView.heightAnchor constraintEqualToConstant:150],
            
            [_nameLabel.topAnchor constraintEqualToAnchor:_imageView.bottomAnchor constant:12],
            [_nameLabel.leadingAnchor constraintEqualToAnchor:self.contentView.leadingAnchor constant:12],
            [_nameLabel.trailingAnchor constraintEqualToAnchor:self.contentView.trailingAnchor constant:-12],
            
            [_priceLabel.topAnchor constraintEqualToAnchor:_nameLabel.bottomAnchor constant:4],
            [_priceLabel.leadingAnchor constraintEqualToAnchor:self.contentView.leadingAnchor constant:12],
            
            [_cartButton.centerYAnchor constraintEqualToAnchor:_priceLabel.centerYAnchor],
            [_cartButton.trailingAnchor constraintEqualToAnchor:self.contentView.trailingAnchor constant:-12],
            [_cartButton.bottomAnchor constraintEqualToAnchor:self.contentView.bottomAnchor constant:-12],
        ]];
    }
    return self;
}

- (void)configureWithTitle:(NSString *)title price:(NSString *)price imageName:(NSString *)imageName {
    self.nameLabel.text = title;
    self.priceLabel.text = price;
    self.imageView.image = [UIImage imageNamed:imageName];
}
@end
```

### Flutter 实现

```dart
// 产品卡片 - 极简
class ProductCard extends StatelessWidget {
  final String title;
  final String price;
  final String imageName;
  final VoidCallback? onCartPressed;

  const ProductCard({
    required this.title,
    required this.price,
    required this.imageName,
    this.onCartPressed,
  });

  @override
  Widget build(BuildContext context) {
    return Card(
      elevation: 4,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(12),
      ),
      clipBehavior: Clip.antiAlias,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          // 图片
          Image.asset(
            imageName,
            height: 150,
            width: double.infinity,
            fit: BoxFit.cover,
          ),
          Padding(
            padding: EdgeInsets.all(12),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                // 名称
                Text(
                  title,
                  style: TextStyle(
                    fontSize: 16,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                SizedBox(height: 4),
                // 价格和按钮
                Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    Text(
                      price,
                      style: TextStyle(
                        fontSize: 14,
                        color: Colors.blue,
                      ),
                    ),
                    IconButton(
                      icon: Icon(Icons.add_shopping_cart),
                      onPressed: onCartPressed,
                    ),
                  ],
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}

// 列表使用
GridView.count(
  crossAxisCount: 2,
  padding: EdgeInsets.all(16),
  mainAxisSpacing: 16,
  crossAxisSpacing: 16,
  childAspectRatio: 0.75,
  children: products.map((product) {
    return ProductCard(
      title: product['title'],
      price: product['price'],
      imageName: product['image'],
      onCartPressed: () => addToCart(product),
    );
  }).toList(),
)
```

---

## 5. Card vs 其他容器对比

| 组件 | 特点 | 使用场景 |
|------|------|---------|
| **Card** | Material 阴影卡片 | 独立内容块 |
| **Container** | 通用容器 | 自定义布局 |
| **Material** | Material 基础 | 墨水效果 |
| **Ink** | 墨水效果 | 点击反馈 |

### 选择建议

```dart
// ✅ 独立内容块用 Card
Card(
  child: ListTile(...),
)

// ✅ 需要完整自定义用 Container
Container(
  decoration: BoxDecoration(
    color: Colors.white,
    borderRadius: BorderRadius.circular(12),
    boxShadow: [BoxShadow(...)],
  ),
  child: ...,
)

// ✅ 需要墨水效果用 Material + InkWell
Material(
  color: Colors.white,
  borderRadius: BorderRadius.circular(12),
  child: InkWell(
    onTap: () {},
    child: ...,
  ),
)
```

---

## 6. 常见陷阱

### 陷阱1：Card 没有点击效果

```dart
// ❌ Card 本身不可点击
Card(
  child: Text('Not clickable'),
)

// ✅ 使用 InkWell 添加点击效果
Card(
  child: InkWell(
    onTap: () {},
    child: Text('Clickable'),
  ),
)
```

### 陷阱2：阴影被裁剪

```dart
// ❌ clipBehavior 会裁剪阴影
Card(
  clipBehavior: Clip.antiAlias,  // 阴影可能被裁剪
  child: ...,
)

// ✅ 如果需要圆角裁剪内容但不裁剪阴影
Card(
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(12),
  ),
  child: ClipRRect(
    borderRadius: BorderRadius.circular(12),
    child: Image.asset(...),  // 图片被裁剪
  ),
)
```

### 陷阱3：Card 内布局

```dart
// ❌ Card 内没有 padding
Card(
  child: Text('No padding'),  // 内容贴边
)

// ✅ 添加 Padding
Card(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Text('With padding'),
  ),
)

// ✅ 或使用 ListTile
Card(
  child: ListTile(
    leading: Icon(Icons.star),
    title: Text('Title'),
    subtitle: Text('Subtitle'),
  ),
)
```

### 陷阱4：Card 宽度

```dart
// Card 默认填满父容器宽度
// 如果需要固定宽度，用 SizedBox 或 ConstrainedBox 包装
SizedBox(
  width: 300,
  child: Card(child: ...),
)
```

---

## 7. 学习技巧

### 技巧1：卡片网格

```dart
GridView.count(
  crossAxisCount: 2,
  childAspectRatio: 0.8,
  children: items.map((item) {
    return Card(
      margin: EdgeInsets.all(8),
      child: ItemContent(item),
    );
  }).toList(),
)
```

### 技巧2：卡片动画

```dart
// 使用 Dismissible 实现滑动删除
Dismissible(
  key: Key(item.id),
  direction: DismissDirection.endToStart,
  onDismissed: (direction) {
    // 删除项目
  },
  background: Container(color: Colors.red),
  child: Card(
    child: ListTile(title: Text(item.title)),
  ),
)
```

### 技巧3：组合 ListTile

```dart
// Card + ListTile 是经典组合
Card(
  child: Column(
    children: [
      ListTile(
        leading: Icon(Icons.photo),
        title: Text('Photos'),
        trailing: Icon(Icons.chevron_right),
        onTap: () {},
      ),
      Divider(height: 1),
      ListTile(
        leading: Icon(Icons.video),
        title: Text('Videos'),
        trailing: Icon(Icons.chevron_right),
        onTap: () {},
      ),
    ],
  ),
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UICollectionViewCell        → Card / Container
自定义 Cell 类              → StatelessWidget
layer.cornerRadius         → shape
layer.shadow*              → elevation
UICollectionViewDelegate   → InkWell onTap
dequeueReusableCell        → 不需要（Flutter 自动处理）
registerClass:             → 不需要
```

### 关键差异

| 方面 | UIKit UICollectionViewCell | Flutter Card |
|------|--------------------------|--------------|
| **复用** | 需要 dequeue | 不需要 |
| **注册** | 需要注册 | 不需要 |
| **布局** | Auto Layout | Flex 布局 |
| **阴影** | layer.shadow | elevation |
| **点击** | delegate | InkWell |

### 最佳实践

1. **Card + ListTile** - 快速构建列表项
2. **使用 InkWell** - 添加点击效果
3. **注意 padding** - Card 内没有默认内边距
4. **Material 3 变体** - 使用 outlined/filled/elevated

---

*最后更新: 2026-02-24*

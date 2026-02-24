# GridView vs UICollectionView 对比学习

本文档记录 Flutter GridView 组件与 UIKit UICollectionView 的对比学习要点。

---

## 1. GridView 核心概念

### GridView 是什么

GridView 是 Flutter 中用于网格布局的滚动组件，类似于 UIKit 的 UICollectionView。

- 支持多种网格布局方式
- 支持滚动
- 支持子组件复用（builder 模式）
- 支持多种构造函数

### GridView vs UICollectionView 概念对比

| Flutter GridView | UIKit UICollectionView | 说明 |
|------------------|------------------------|------|
| `GridView.count()` | `UICollectionViewFlowLayout` | 固定列数网格 |
| `GridView.extent()` | `UICollectionViewFlowLayout` | 固定宽度网格 |
| `GridView.builder()` | `dequeueReusableCell` | 按需构建 |
| `gridDelegate` | `UICollectionViewLayout` | 布局代理 |
| `crossAxisCount` | `itemSize + 计算列数` | 列数 |
| `mainAxisSpacing` | `minimumLineSpacing` | 行间距 |
| `crossAxisSpacing` | `minimumInteritemSpacing` | 列间距 |
| `childAspectRatio` | `itemSize` | 宽高比 |

---

## 2. 源码分析

### GridView 构造函数

```dart
class GridView extends BoxScrollView {
  // 通用构造函数
  GridView({
    super.key,
    super.scrollDirection = Axis.vertical,
    super.reverse = false,
    super.controller,
    super.primary,
    super.physics,
    super.shrinkWrap = false,
    super.padding,
    required this.gridDelegate,  // 必需的布局代理
    super.children = const <Widget>[],
  });
  
  // 固定列数
  GridView.count({
    required int crossAxisCount,    // 列数
    double mainAxisSpacing = 0.0,   // 行间距
    double crossAxisSpacing = 0.0,  // 列间距
    double childAspectRatio = 1.0,  // 子组件宽高比
    // ...
  });
  
  // 固定最大宽度
  GridView.extent({
    required double maxCrossAxisExtent,  // 子组件最大宽度
    double mainAxisSpacing = 0.0,
    double crossAxisSpacing = 0.0,
    double childAspectRatio = 1.0,
    // ...
  });
  
  // 按需构建（推荐用于大数据）
  GridView.builder({
    required this.gridDelegate,
    required IndexedWidgetBuilder itemBuilder,
    int? itemCount,
    // ...
  });
}
```

### SliverGridDelegate

```dart
// 布局代理
abstract class SliverGridDelegate {
  SliverGridLayout getLayout(SliverConstraints constraints);
}

// 固定列数
class SliverGridDelegateWithFixedCrossAxisCount {
  final int crossAxisCount;      // 列数
  final double mainAxisSpacing;  // 主轴间距
  final double crossAxisSpacing; // 交叉轴间距
  final double childAspectRatio; // 子组件宽高比
}

// 固定最大宽度
class SliverGridDelegateWithMaxCrossAxisExtent {
  final double maxCrossAxisExtent;  // 子组件最大宽度
  final double mainAxisSpacing;
  final double crossAxisSpacing;
  final double childAspectRatio;
}
```

---

## 3. 与 UICollectionView 详细对比

### 3.1 固定列数网格

```objc
// Objective-C - UICollectionViewFlowLayout
UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc] init];
layout.itemSize = CGSizeMake(100, 100);
layout.minimumInteritemSpacing = 8;  // 列间距
layout.minimumLineSpacing = 8;       // 行间距
layout.sectionInset = UIEdgeInsetsMake(16, 16, 16, 16);

UICollectionView *collectionView = [[UICollectionView alloc] initWithFrame:frame collectionViewLayout:layout];
collectionView.dataSource = self;
[collectionView registerClass:[MyCell class] forCellWithReuseIdentifier:@"Cell"];
```

```dart
// Flutter - GridView.count
GridView.count(
  crossAxisCount: 3,          // 固定 3 列
  mainAxisSpacing: 8,         // 行间距
  crossAxisSpacing: 8,        // 列间距
  padding: EdgeInsets.all(16),
  childAspectRatio: 1.0,      // 宽高比 1:1
  children: List.generate(20, (index) {
    return Container(
      color: Colors.primaries[index % Colors.primaries.length],
      child: Center(child: Text('$index')),
    );
  }),
)
```

### 3.2 自适应宽度网格

```objc
// Objective-C - 需要自定义 FlowLayout 或使用 UICollectionViewFlowLayout
// 设置 itemSize 后系统自动计算列数
layout.itemSize = CGSizeMake(150, 150);
```

```dart
// Flutter - GridView.extent
GridView.extent(
  maxCrossAxisExtent: 150,    // 最大宽度 150
  mainAxisSpacing: 8,
  crossAxisSpacing: 8,
  childAspectRatio: 1.0,
  children: [...],
)
// 子组件宽度会根据屏幕宽度自动调整，最大不超过 150
```

### 3.3 大数据网格（builder 模式）

```objc
// Objective-C - dequeueReusableCell
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    MyCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"Cell" forIndexPath:indexPath];
    cell.label.text = self.items[indexPath.item];
    return cell;
}
```

```dart
// Flutter - GridView.builder
GridView.builder(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 3,
    mainAxisSpacing: 8,
    crossAxisSpacing: 8,
  ),
  itemCount: items.length,
  itemBuilder: (context, index) {
    return Container(
      color: Colors.primaries[index % Colors.primaries.length],
      child: Center(child: Text(items[index])),
    );
  },
)
```

### 3.4 不同尺寸子组件

```objc
// Objective-C - 需要自定义 UICollectionViewLayout
// 或使用 UICollectionViewFlowLayout 的 estimatedItemSize
layout.estimatedItemSize = CGSizeMake(100, 100);
// 然后在 Cell 中使用 Auto Layout
```

```dart
// Flutter - 使用 staggered_grid_view 包
// 或使用自定义布局

// 标准 GridView 可以通过 childAspectRatio 设置不同宽高比
GridView.count(
  crossAxisCount: 2,
  childAspectRatio: 0.75,  // 宽:高 = 0.75:1
  children: [...],
)
```

### 3.5 滚动方向

```objc
// Objective-C
layout.scrollDirection = UICollectionViewScrollDirectionVertical;
layout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
```

```dart
// Flutter
GridView.count(
  scrollDirection: Axis.vertical,    // 垂直滚动
  scrollDirection: Axis.horizontal,  // 水平滚动
  crossAxisCount: 3,
  children: [...],
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 照片网格
@interface PhotoGridViewController : UIViewController <UICollectionViewDataSource, UICollectionViewDelegate>
@property (nonatomic, strong) NSArray *photos;
@property (nonatomic, strong) UICollectionView *collectionView;
@end

@implementation PhotoGridViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc] init];
    layout.itemSize = CGSizeMake(100, 100);
    layout.minimumInteritemSpacing = 2;
    layout.minimumLineSpacing = 2;
    
    self.collectionView = [[UICollectionView alloc] initWithFrame:self.view.bounds collectionViewLayout:layout];
    self.collectionView.backgroundColor = [UIColor systemBackgroundColor];
    self.collectionView.dataSource = self;
    self.collectionView.delegate = self;
    [self.collectionView registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:@"Cell"];
    [self.view addSubview:self.collectionView];
}

- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return self.photos.count;
}

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"Cell" forIndexPath:indexPath];
    
    UIImageView *imageView = [[UIImageView alloc] initWithFrame:cell.contentView.bounds];
    imageView.contentMode = UIViewContentModeScaleAspectFill;
    imageView.clipsToBounds = YES;
    [imageView sd_setImageWithURL:[NSURL URLWithString:self.photos[indexPath.item]]];
    
    for (UIView *subview in cell.contentView.subviews) {
        [subview removeFromSuperview];
    }
    [cell.contentView addSubview:imageView];
    
    return cell;
}

- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"Selected photo: %ld", (long)indexPath.item);
}
@end
```

### Flutter 实现

```dart
// 照片网格 - 更简洁
class PhotoGridWidget extends StatelessWidget {
  final photos = List.generate(
    20,
    (i) => 'https://picsum.photos/200/200?random=$i',
  );

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      padding: EdgeInsets.all(2),
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        mainAxisSpacing: 2,
        crossAxisSpacing: 2,
      ),
      itemCount: photos.length,
      itemBuilder: (context, index) {
        return GestureDetector(
          onTap: () => print('Selected photo: $index'),
          child: Image.network(
            photos[index],
            fit: BoxFit.cover,
            loadingBuilder: (context, child, loadingProgress) {
              if (loadingProgress == null) return child;
              return Container(
                color: Colors.grey[200],
                child: Center(child: CircularProgressIndicator()),
              );
            },
          ),
        );
      },
    );
  }
}
```

---

## 5. GridView 构造函数选择

| 构造函数 | 特点 | 使用场景 |
|---------|------|---------|
| `GridView()` | 自定义 delegate | 高度自定义 |
| `GridView.count()` | 固定列数 | 最常用 |
| `GridView.extent()` | 固定最大宽度 | 响应式布局 |
| `GridView.builder()` | 按需构建 | 大数据量 |
| `GridView.custom()` | 自定义子组件代理 | 特殊需求 |

### 选择建议

```dart
// 少量固定子组件
GridView.count(
  crossAxisCount: 3,
  children: [...],
)

// 大量数据
GridView.builder(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 3,
  ),
  itemBuilder: (context, index) => Item(index),
  itemCount: 1000,
)

// 响应式宽度
GridView.extent(
  maxCrossAxisExtent: 150,
  children: [...],
)
```

---

## 6. 常见陷阱

### 陷阱1：childAspectRatio 计算错误

```dart
// childAspectRatio = 宽度 / 高度
// 如果宽高比为 1:1，childAspectRatio = 1.0
// 如果宽高比为 4:3，childAspectRatio = 4/3

GridView.count(
  crossAxisCount: 2,
  childAspectRatio: 4 / 3,  // 宽是高的 4/3 倍
  children: [...],
)

// ⚠️ 注意：这是宽高比，不是高宽比
// 如果想要高度大于宽度，childAspectRatio 应该小于 1
GridView.count(
  crossAxisCount: 2,
  childAspectRatio: 3 / 4,  // 高是宽的 4/3 倍
  children: [...],
)
```

### 陷阱2：GridView 在 Column 中

```dart
// ❌ 错误：GridView 在 Column 中需要 Expanded
Column(
  children: [
    GridView.count(crossAxisCount: 3, children: [...]),  // 报错！
  ],
)

// ✅ 正确：使用 Expanded 或 shrinkWrap
Column(
  children: [
    Expanded(
      child: GridView.count(crossAxisCount: 3, children: [...]),
    ),
  ],
)

// 或使用 shrinkWrap（性能较差）
Column(
  children: [
    GridView.count(
      shrinkWrap: true,
      physics: NeverScrollableScrollPhysics(),
      crossAxisCount: 3,
      children: [...],
    ),
  ],
)
```

### 陷阱3：间距计算

```dart
// 子组件宽度 = (总宽度 - padding - crossAxisSpacing * (crossAxisCount - 1)) / crossAxisCount
// 需要考虑 padding 和 spacing

GridView.count(
  padding: EdgeInsets.all(16),     // 边距
  crossAxisCount: 3,
  crossAxisSpacing: 8,             // 2 个间距 = 16
  // 子组件宽度 = (屏幕宽度 - 32 - 16) / 3
  children: [...],
)
```

### 陷阱4：嵌套滚动

```dart
// 外层 Column + 内层 GridView
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(child: Header()),
    SliverGrid(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
      ),
      delegate: SliverChildBuilderDelegate(
        (context, index) => Item(index),
        childCount: items.length,
      ),
    ),
  ],
)
```

---

## 7. 学习技巧

### 技巧1：响应式列数

```dart
LayoutBuilder(
  builder: (context, constraints) {
    // 根据宽度计算列数
    final crossAxisCount = (constraints.maxWidth / 150).floor();
    
    return GridView.count(
      crossAxisCount: crossAxisCount.clamp(2, 6),  // 限制 2-6 列
      children: [...],
    );
  },
)
```

### 技巧2：使用 SliverGrid

```dart
// CustomScrollView 中使用 SliverGrid
CustomScrollView(
  slivers: [
    SliverPersistentHeader(...),
    SliverGrid(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
      ),
      delegate: SliverChildBuilderDelegate(
        (context, index) => Item(index),
        childCount: items.length,
      ),
    ),
  ],
)
```

### 技巧3：固定尺寸优化

```dart
// 如果子组件尺寸固定，可以优化性能
GridView.builder(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 3,
    mainAxisExtent: 100,  // 固定高度，跳过计算
  ),
  itemBuilder: ...,
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UICollectionView             → GridView
UICollectionViewFlowLayout   → SliverGridDelegate
dequeueReusableCell         → itemBuilder
numberOfItemsInSection      → itemCount
cellForItemAtIndexPath      → itemBuilder
itemSize                    → childAspectRatio
minimumInteritemSpacing     → crossAxisSpacing
minimumLineSpacing          → mainAxisSpacing
registerClass:forCell       → 不需要
```

### 关键差异

| 方面 | UIKit UICollectionView | Flutter GridView |
|------|------------------------|------------------|
| **复用机制** | Cell 复用 | Widget 自动管理 |
| **Cell 注册** | 必须注册 | 不需要 |
| **布局** | Layout 对象 | gridDelegate |
| **列数** | 通过 itemSize 计算 | 直接指定 crossAxisCount |
| **性能优化** | estimatedItemSize | shrinkWrap / mainAxisExtent |

### 最佳实践

1. **大数据用 builder** - 按需构建，性能好
2. **固定列数用 count** - 简单直接
3. **响应式用 extent** - 自动适配
4. **复杂布局用 SliverGrid** - 配合 CustomScrollView

---

*最后更新: 2026-02-24*

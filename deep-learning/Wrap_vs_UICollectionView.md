# Wrap vs UICollectionView 对比学习

本文档记录 Flutter Wrap 组件与 UIKit UICollectionView FlowLayout 的对比学习要点。

---

## 1. Wrap 核心概念

### Wrap 是什么

Wrap 是 Flutter 中用于流式布局的组件，子组件会自动换行，类似于 UICollectionView 使用 FlowLayout。

- 子组件按主轴方向排列
- 空间不足时自动换行
- 支持水平和垂直方向
- 支持间距和对齐设置

### Wrap vs UICollectionView FlowLayout 概念对比

| Flutter Wrap | UIKit UICollectionViewFlowLayout | 说明 |
|--------------|----------------------------------|------|
| `direction` | `scrollDirection` | 主轴方向 |
| `alignment` | `justifyContent` | 主轴对齐 |
| `runAlignment` | 无直接对应 | 交叉轴对齐 |
| `crossAxisAlignment` | 无直接对应 | 子组件对齐 |
| `spacing` | `minimumInteritemSpacing` | 主轴间距 |
| `runSpacing` | `minimumLineSpacing` | 行/列间距 |
| `children` | `items` | 子组件 |

---

## 2. 源码分析

### Wrap 类定义

```dart
class Wrap extends MultiChildRenderObjectWidget {
  const Wrap({
    super.key,
    this.direction = Axis.horizontal,  // 主轴方向
    this.alignment = WrapAlignment.start,  // 主轴对齐
    this.spacing = 0.0,                // 主轴间距
    this.runAlignment = WrapAlignment.start,  // 行对齐
    this.runSpacing = 0.0,             // 行间距
    this.crossAxisAlignment = WrapCrossAlignment.start,  // 交叉轴对齐
    this.textDirection,
    this.verticalDirection = VerticalDirection.down,
    super.children = const <Widget>[],
  });
}
```

### WrapAlignment 枚举

```dart
enum WrapAlignment {
  start,        // 起点对齐
  end,          // 终点对齐
  center,       // 居中
  spaceBetween, // 两端对齐
  spaceAround,  // 环绕对齐
  spaceEvenly,  // 均匀对齐
}
```

---

## 3. 与 UICollectionView FlowLayout 详细对比

### 3.1 基础流式布局

```objc
// Objective-C - UICollectionViewFlowLayout
UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc] init];
layout.scrollDirection = UICollectionViewScrollDirectionVertical;
layout.minimumInteritemSpacing = 8;  // 水平间距
layout.minimumLineSpacing = 8;       // 垂直间距
layout.itemSize = CGSizeMake(80, 80);

UICollectionView *collectionView = [[UICollectionView alloc] initWithFrame:frame collectionViewLayout:layout];
collectionView.dataSource = self;
```

```dart
// Flutter - Wrap
Wrap(
  spacing: 8,      // 水平间距
  runSpacing: 8,   // 垂直间距（行间距）
  children: List.generate(20, (index) {
    return Container(
      width: 80,
      height: 80,
      color: Colors.primaries[index % Colors.primaries.length],
      child: Center(child: Text('$index')),
    );
  }),
)
```

### 3.2 对齐方式

```objc
// Objective-C - FlowLayout 对齐有限
// 需要子类化 UICollectionViewFlowLayout 来实现自定义对齐
```

```dart
// Flutter - 多种对齐选项
Wrap(
  alignment: WrapAlignment.start,      // 起点
  alignment: WrapAlignment.center,     // 居中
  alignment: WrapAlignment.end,        // 终点
  alignment: WrapAlignment.spaceBetween,  // 两端
  alignment: WrapAlignment.spaceAround,   // 环绕
  alignment: WrapAlignment.spaceEvenly,   // 均匀
  spacing: 8,
  children: [...],
)
```

### 3.3 行对齐

```dart
// runAlignment 控制多行在交叉轴上的对齐
Wrap(
  runAlignment: WrapAlignment.start,   // 行起点对齐
  runAlignment: WrapAlignment.center,  // 行居中
  runAlignment: WrapAlignment.end,     // 行终点对齐
  runAlignment: WrapAlignment.spaceBetween,
  children: [...],
)
```

**图示**：

```
alignment: WrapAlignment.center, runAlignment: WrapAlignment.start

┌────────────────────────────────┐
│    ┌──┐ ┌──┐ ┌──┐ ┌──┐        │
│    │1 │ │2 │ │3 │ │4 │        │
│    └──┘ └──┘ └──┘ └──┘        │
│    ┌──┐ ┌──┐ ┌──┐             │
│    │5 │ │6 │ │7 │             │
│    └──┘ └──┘ └──┘             │
└────────────────────────────────┘

alignment: WrapAlignment.start, runAlignment: WrapAlignment.center

┌────────────────────────────────┐
│┌──┐ ┌──┐ ┌──┐ ┌──┐            │
││1 │ │2 │ │3 │ │4 │            │
│└──┘ └──┘ └──┘ └──┘            │
│                                │
│┌──┐ ┌──┐ ┌──┐                 │
││5 │ │6 │ │7 │                 │
│└──┘ └──┘ └──┘                 │
└────────────────────────────────┘
```

### 3.4 交叉轴对齐

```dart
// crossAxisAlignment 控制同一行内子组件的对齐
Wrap(
  crossAxisAlignment: WrapCrossAlignment.start,  // 顶部对齐
  crossAxisAlignment: WrapCrossAlignment.center,  // 居中
  crossAxisAlignment: WrapCrossAlignment.end,   // 底部对齐
  children: [
    Container(height: 50, color: Colors.red),
    Container(height: 80, color: Colors.blue),
    Container(height: 30, color: Colors.green),
  ],
)
```

### 3.5 垂直流式布局

```objc
// Objective-C - 水平滚动的 FlowLayout
layout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
```

```dart
// Flutter - 垂直排列
Wrap(
  direction: Axis.vertical,  // 垂直方向
  spacing: 8,    // 垂直间距
  runSpacing: 8, // 列间距
  children: [...],
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 标签选择器
@interface TagsViewController : UIViewController <UICollectionViewDataSource, UICollectionViewDelegateFlowLayout>
@property (nonatomic, strong) NSArray *tags;
@property (nonatomic, strong) UICollectionView *collectionView;
@end

@implementation TagsViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.tags = @[@"Flutter", @"iOS", @"Android", @"Web", @"Desktop", 
                  @"Mobile", @"Cross-platform", @"Dart", @"Swift", @"Kotlin"];
    
    UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc] init];
    layout.estimatedItemSize = CGSizeMake(80, 36);  // 自适应尺寸
    layout.minimumInteritemSpacing = 8;
    layout.minimumLineSpacing = 8;
    layout.sectionInset = UIEdgeInsetsMake(16, 16, 16, 16);
    
    self.collectionView = [[UICollectionView alloc] initWithFrame:self.view.bounds collectionViewLayout:layout];
    self.collectionView.backgroundColor = [UIColor systemBackgroundColor];
    self.collectionView.dataSource = self;
    self.collectionView.delegate = self;
    
    [self.collectionView registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:@"Cell"];
    [self.view addSubview:self.collectionView];
}

- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return self.tags.count;
}

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"Cell" forIndexPath:indexPath];
    
    UILabel *label = [[UILabel alloc] init];
    label.text = self.tags[indexPath.item];
    label.font = [UIFont systemFontOfSize:14];
    label.textColor = [UIColor systemBlueColor];
    [label sizeToFit];
    
    // 清除旧内容
    for (UIView *subview in cell.contentView.subviews) {
        [subview removeFromSuperview];
    }
    
    [cell.contentView addSubview:label];
    label.center = cell.contentView.center;
    
    cell.contentView.layer.borderWidth = 1;
    cell.contentView.layer.borderColor = [UIColor systemBlueColor].CGColor;
    cell.contentView.layer.cornerRadius = 18;
    
    return cell;
}

- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"Selected: %@", self.tags[indexPath.item]);
}
@end
```

### Flutter 实现

```dart
// 标签选择器 - 更简洁
class TagsWidget extends StatelessWidget {
  final tags = [
    'Flutter', 'iOS', 'Android', 'Web', 'Desktop',
    'Mobile', 'Cross-platform', 'Dart', 'Swift', 'Kotlin'
  ];

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: EdgeInsets.all(16),
      child: Wrap(
        spacing: 8,
        runSpacing: 8,
        children: tags.map((tag) {
          return GestureDetector(
            onTap: () => print('Selected: $tag'),
            child: Container(
              padding: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
              decoration: BoxDecoration(
                border: Border.all(color: Colors.blue),
                borderRadius: BorderRadius.circular(18),
              ),
              child: Text(
                tag,
                style: TextStyle(color: Colors.blue, fontSize: 14),
              ),
            ),
          );
        }).toList(),
      ),
    );
  }
}

// 使用 Chip 组件更简单
Wrap(
  spacing: 8,
  children: tags.map((tag) {
    return Chip(
      label: Text(tag),
      backgroundColor: Colors.blue.withOpacity(0.1),
      side: BorderSide(color: Colors.blue),
      onDeleted: () => print('Delete: $tag'),
    );
  }).toList(),
)
```

---

## 5. Wrap vs ListView vs GridView 选择

| 组件 | 特点 | 使用场景 |
|------|------|---------|
| **Wrap** | 自动换行，无滚动 | 标签、胶囊按钮、少量内容 |
| **ListView** | 单列滚动 | 长列表 |
| **GridView** | 网格滚动 | 规则网格布局 |
| **Flow** | 自定义布局规则 | 复杂流式布局 |

### 需要滚动时

```dart
// Wrap 本身不可滚动，需要包装
SingleChildScrollView(
  child: Wrap(
    children: [...],
  ),
)

// 或使用可滚动的 GridView
GridView.count(
  crossAxisCount: 3,
  children: [...],
)
```

---

## 6. 常见陷阱

### 陷阱1：Wrap 不可滚动

```dart
// ❌ 问题：内容过多时溢出
Wrap(
  children: List.generate(100, (i) => Chip(label: Text('Tag $i'))),
)

// ✅ 解决方案：包裹滚动组件
SingleChildScrollView(
  child: Wrap(
    children: [...],
  ),
)
```

### 陷阱2：子组件尺寸过大

```dart
// ❌ 问题：子组件比容器宽
Container(
  width: 200,
  child: Wrap(
    children: [
      Container(width: 250, color: Colors.red),  // 溢出
    ],
  ),
)

// ✅ 解决方案：限制子组件尺寸
Wrap(
  children: [
    Container(
      constraints: BoxConstraints(maxWidth: 180),
      child: LongContent(),
    ),
  ],
)
```

### 陷阱3：spacing 和 runSpacing 混淆

```dart
// spacing - 主轴方向间距（水平布局时是水平间距）
// runSpacing - 行/列间距（水平布局时是垂直间距）

Wrap(
  direction: Axis.horizontal,
  spacing: 8,      // 水平间距
  runSpacing: 16,  // 垂直间距（行间距）
  children: [...],
)

Wrap(
  direction: Axis.vertical,
  spacing: 8,      // 垂直间距
  runSpacing: 16,  // 水平间距（列间距）
  children: [...],
)
```

### 陷阱4：alignment 与 crossAxisAlignment 混淆

```dart
// alignment - 整行在主轴上的对齐
// crossAxisAlignment - 同一行内子组件在交叉轴上的对齐

Wrap(
  alignment: WrapAlignment.center,           // 整行居中
  crossAxisAlignment: WrapCrossAlignment.end, // 子组件底部对齐
  children: [
    Container(height: 50, color: Colors.red),
    Container(height: 80, color: Colors.blue),
    Container(height: 30, color: Colors.green),
  ],
)
```

---

## 7. 学习技巧

### 技巧1：动态子组件

```dart
// 使用条件过滤
Wrap(
  children: items
      .where((item) => item.isActive)
      .map((item) => Chip(label: Text(item.name)))
      .toList(),
)

// 使用条件渲染
Wrap(
  children: [
    if (showAll) Chip(label: Text('All')),
    ...items.map((item) => Chip(label: Text(item.name))),
  ],
)
```

### 技巧2：与 Chip 组件配合

```dart
// Wrap + Chip 是经典组合
Wrap(
  spacing: 8,
  runSpacing: 8,
  children: [
    Chip(
      label: Text('Flutter'),
      avatar: CircleAvatar(child: Text('F')),
      onDeleted: () {},
    ),
    FilterChip(
      label: Text('iOS'),
      selected: _selected == 'iOS',
      onSelected: (selected) => setState(() => _selected = 'iOS'),
    ),
    InputChip(
      label: Text('Android'),
      onPressed: () {},
    ),
  ],
)
```

### 技巧3：响应式换行

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final itemWidth = 100.0;
    final count = (constraints.maxWidth / (itemWidth + 8)).floor();
    
    return Wrap(
      spacing: 8,
      runSpacing: 8,
      children: items.map((item) {
        return SizedBox(
          width: (constraints.maxWidth - (count - 1) * 8) / count,
          child: ItemWidget(item),
        );
      }).toList(),
    );
  },
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UICollectionViewFlowLayout  → Wrap
scrollDirection            → direction
minimumInteritemSpacing    → spacing
minimumLineSpacing         → runSpacing
UICollectionViewCell       → 直接使用 Widget
自定义对齐                  → alignment / runAlignment
滚动                        → 需要额外包装
```

### 关键差异

| 方面 | UIKit UICollectionView | Flutter Wrap |
|------|------------------------|--------------|
| **滚动** | 内置 | 需要包装 |
| **复用** | Cell 复用 | 无复用机制 |
| **子组件** | Cell | 任意 Widget |
| **对齐** | 需要子类化 | 内置支持 |
| **适用场景** | 大量数据 | 少量内容 |

### 最佳实践

1. **标签选择用 Wrap + Chip** - 经典组合
2. **少量内容用 Wrap** - 不需要滚动
3. **大量内容用 GridView** - 需要滚动和复用
4. **注意间距方向** - spacing vs runSpacing

---

*最后更新: 2026-02-24*

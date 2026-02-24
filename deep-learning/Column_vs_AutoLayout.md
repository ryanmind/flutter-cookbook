# Column vs UIStackView 对比学习

本文档记录 Flutter Column 组件与 UIKit UIStackView (vertical) 的对比学习要点。

---

## 1. Column 核心概念

### Column 是什么

Column 是 Flutter 中用于垂直排列子组件的布局组件，类似于 UIKit 中的 UIStackView（垂直方向）。

- 子组件沿垂直方向排列
- 支持主轴对齐（mainAxisAlignment）
- 支持交叉轴对齐（crossAxisAlignment）
- 支持弹性子组件（Expanded、Flexible）

### Column vs UIStackView 概念对比

| Flutter Column | UIKit UIStackView | 说明 |
|----------------|-------------------|------|
| `mainAxisAlignment` | `distribution` | 主轴对齐方式 |
| `crossAxisAlignment` | `alignment` | 交叉轴对齐 |
| `mainAxisSize` | 无直接对应 | 主轴尺寸 |
| `Expanded` | `distribution = .fill` | 填充剩余空间 |
| `SizedBox(height:)` | `spacing` | 子组件间距 |
| `verticalDirection` | 无直接对应 | 排列方向 |

---

## 2. 源码分析

### Column 继承关系

```dart
class Column extends Flex {
  const Column({
    super.key,
    super.mainAxisAlignment = MainAxisAlignment.start,
    super.mainAxisSize = MainAxisSize.max,
    super.crossAxisAlignment = CrossAxisAlignment.center,
    super.textDirection,
    super.verticalDirection = VerticalDirection.down,
    super.textBaseline,
    super.children = const <Widget>[],
  }) : super(
         direction: Axis.vertical,  // 关键：方向为垂直
       );
}
```

### Row vs Column 关键差异

```dart
// Row 和 Column 都继承自 Flex
// 唯一区别是 direction 参数

// Row: direction = Axis.horizontal
// Column: direction = Axis.vertical

// 主轴和交叉轴互换：
// Row:    主轴=水平，交叉轴=垂直
// Column: 主轴=垂直，交叉轴=水平
```

---

## 3. 与 UIStackView 详细对比

### 3.1 基础垂直布局

```objc
// Objective-C - UIStackView
UIStackView *stack = [[UIStackView alloc] init];
stack.axis = UILayoutConstraintAxisVertical;
stack.distribution = UIStackViewDistributionFill;
stack.alignment = UIStackViewAlignmentFill;
stack.spacing = 8;

[stack addArrangedSubview:view1];
[stack addArrangedSubview:view2];
[stack addArrangedSubview:view3];
```

```dart
// Flutter - Column
Column(
  mainAxisAlignment: MainAxisAlignment.start,
  crossAxisAlignment: CrossAxisAlignment.center,
  children: [
    view1,
    const SizedBox(height: 8),  // 间距
    view2,
    const SizedBox(height: 8),
    view3,
  ],
)
```

### 3.2 主轴对齐 (Main Axis Alignment)

```objc
// Objective-C - UIStackView distribution
stack.distribution = UIStackViewDistributionFill;           // 填充
stack.distribution = UIStackViewDistributionFillEqually;    // 等高
stack.distribution = UIStackViewDistributionEqualSpacing;   // 等间距
```

```dart
// Flutter - Column mainAxisAlignment
Column(
  mainAxisAlignment: MainAxisAlignment.start,        // 顶部对齐（默认）
  mainAxisAlignment: MainAxisAlignment.end,          // 底部对齐
  mainAxisAlignment: MainAxisAlignment.center,       // 居中
  mainAxisAlignment: MainAxisAlignment.spaceBetween, // 两端对齐，中间等分
  mainAxisAlignment: MainAxisAlignment.spaceAround,  // 每个子组件两侧等分
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,  // 完全等分
  children: [view1, view2, view3],
)
```

**对齐方式图解**（垂直方向）：

```
start:     ┌──┐
           │A │
           ├──┤
           │B │
           ├──┤
           │C │
           └──┘

center:    
           ┌──┐
           │A │
           ├──┤
           │B │
           ├──┤
           │C │
           └──┘

end:       
           ┌──┐
           │A │
           ├──┤
           │B │
           ├──┤
           │C │
           └──┘

spaceBetween:
┌──┐
│A │
│  │
│  │
├──┤
│B │
│  │
│  │
├──┤
│C │
└──┘
```

### 3.3 交叉轴对齐 (Cross Axis Alignment)

```objc
// Objective-C - UIStackView alignment
stack.alignment = UIStackViewAlignmentFill;      // 填充宽度
stack.alignment = UIStackViewAlignmentLeading;   // 左对齐
stack.alignment = UIStackViewAlignmentCenter;    // 居中
stack.alignment = UIStackViewAlignmentTrailing;  // 右对齐
```

```dart
// Flutter - Column crossAxisAlignment
Column(
  crossAxisAlignment: CrossAxisAlignment.start,   // 左对齐
  crossAxisAlignment: CrossAxisAlignment.end,     // 右对齐
  crossAxisAlignment: CrossAxisAlignment.center,  // 居中（默认）
  crossAxisAlignment: CrossAxisAlignment.stretch, // 拉伸填满宽度
  children: [
    Container(width: 50, height: 50, color: Colors.red),
    Container(width: 100, height: 50, color: Colors.blue),
    Container(width: 75, height: 50, color: Colors.green),
  ],
)
```

**交叉轴对齐图解**（Column 的交叉轴是水平方向）：

```
start:    ┌──┐
          │A │
          └──┘
          ┌──────┐
          │  B   │
          └──────┘
          ┌────┐
          │ C  │
          └────┘

center:      ┌──┐
             │A │
             └──┘
          ┌──────┐
          │  B   │
          └──────┘
            ┌────┐
            │ C  │
            └────┘

stretch:  ┌────────────┐
          │     A      │
          └────────────┘
          ┌────────────┐
          │     B      │
          └────────────┘
          ┌────────────┐
          │     C      │
          └────────────┘
```

### 3.4 弹性布局

```objc
// Objective-C - UIStackView distribution
// 让某个子视图填充剩余空间
UIView *spacer = [[UIView alloc] init];
[spacer setContentHuggingPriority:UILayoutPriorityDefaultLow
                          forAxis:UILayoutConstraintAxisVertical];
[stack addArrangedSubview:spacer];
```

```dart
// Flutter - Expanded
Column(
  children: [
    Container(height: 50, color: Colors.red),
    Expanded(                    // 填充剩余空间
      child: Container(color: Colors.blue),
    ),
    Container(height: 50, color: Colors.green),
  ],
)
```

### 3.5 比例布局

```objc
// Objective-C - 需要手动设置约束
[view1.heightAnchor constraintEqualToAnchor:view2.heightAnchor multiplier:2].active = YES;
```

```dart
// Flutter - Expanded with flex
Column(
  children: [
    Expanded(
      flex: 2,                  // 占 2/3
      child: Container(color: Colors.red),
    ),
    Expanded(
      flex: 1,                  // 占 1/3
      child: Container(color: Colors.blue),
    ),
  ],
)
```

### 3.6 反向排列

```objc
// Objective-C - 没有直接方式，需要手动反转数组
// 或使用 transform
stack.transform = CGAffineTransformMakeScale(1, -1);
```

```dart
// Flutter - verticalDirection
Column(
  verticalDirection: VerticalDirection.up,  // 从下往上排列
  children: [
    Text('A'),  // 显示在底部
    Text('B'),
    Text('C'),  // 显示在顶部
  ],
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 创建一个垂直布局的卡片列表
UIStackView *cardStack = [[UIStackView alloc] init];
cardStack.axis = UILayoutConstraintAxisVertical;
cardStack.distribution = UIStackViewDistributionFill;
cardStack.alignment = UIStackViewAlignmentFill;
cardStack.spacing = 16;
cardStack.translatesAutoresizingMaskIntoConstraints = NO;

for (int i = 0; i < 3; i++) {
    UIView *card = [[UIView alloc] init];
    card.backgroundColor = [UIColor whiteColor];
    card.layer.cornerRadius = 8;
    card.layer.shadowColor = [UIColor blackColor].CGColor;
    card.layer.shadowOffset = CGSizeMake(0, 2);
    card.layer.shadowRadius = 4;
    card.layer.shadowOpacity = 0.1;
    
    UILabel *titleLabel = [[UILabel alloc] init];
    titleLabel.text = [NSString stringWithFormat:@"Card %d", i + 1];
    titleLabel.font = [UIFont boldSystemFontOfSize:16];
    titleLabel.translatesAutoresizingMaskIntoConstraints = NO;
    [card addSubview:titleLabel];
    
    [titleLabel.leadingAnchor constraintEqualToAnchor:card.leadingAnchor constant:16].active = YES;
    [titleLabel.topAnchor constraintEqualToAnchor:card.topAnchor constant:16].active = YES;
    
    [card addConstraint:[NSLayoutConstraint constraintWithItem:card
                                                      attribute:NSLayoutAttributeHeight
                                                      relatedBy:NSLayoutRelationEqual
                                                         toItem:nil
                                                      attribute:NSLayoutAttributeNotAnAttribute
                                                     multiplier:1
                                                       constant:80]];
    
    [cardStack addArrangedSubview:card];
}

[self.view addSubview:cardStack];
[cardStack.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor constant:16].active = YES;
[cardStack.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor constant:-16].active = YES;
[cardStack.topAnchor constraintEqualToAnchor:self.view.safeAreaLayoutGuide.topAnchor constant:20].active = YES;
```

### Flutter 实现

```dart
// 创建同样的卡片列表 - 更简洁
Column(
  children: List.generate(3, (index) {
    return Container(
      margin: const EdgeInsets.only(bottom: 16),
      padding: const EdgeInsets.all(16),
      height: 80,
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(8),
        boxShadow: [
          BoxShadow(
            color: Colors.black.withOpacity(0.1),
            offset: const Offset(0, 2),
            blurRadius: 4,
          ),
        ],
      ),
      child: Text(
        'Card ${index + 1}',
        style: const TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
      ),
    );
  }),
)

// 或使用 ListView 更适合长列表
ListView.builder(
  padding: const EdgeInsets.all(16),
  itemCount: 3,
  itemBuilder: (context, index) {
    return Container(
      margin: const EdgeInsets.only(bottom: 16),
      height: 80,
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(8),
        boxShadow: [
          BoxShadow(
            color: Colors.black.withOpacity(0.1),
            offset: const Offset(0, 2),
            blurRadius: 4,
          ),
        ],
      ),
      child: Center(
        child: Text(
          'Card ${index + 1}',
          style: const TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
        ),
      ),
    );
  },
)
```

---

## 5. 常见陷阱

### 陷阱1：无限高度错误

```dart
// ❌ 错误：Column 在无限高度中
Column(
  children: [
    Text('Top'),
    Expanded(child: Container()),  // 在无限高度中 Expanded 会报错
  ],
)

// 场景：在另一个 Column 或 ListView 中使用 Column
ListView(
  children: [
    Column(                    // 这个 Column 高度无界
      children: [
        Expanded(child: ...),  // 报错！
      ],
    ),
  ],
)

// ✅ 正确：给 Column 限制高度
SizedBox(
  height: 300,
  child: Column(
    children: [
      Text('Top'),
      Expanded(child: Container()),
    ],
  ),
)
```

### 陷阱2：嵌套 Column 需要明确约束

```dart
// ❌ 错误：内层 Column 高度无界
Column(
  children: [
    Text('Header'),
    Column(                    // 内层 Column 高度不确定
      children: [Text('A'), Text('B')],
    ),
  ],
)

// ✅ 正确方案1：使用 mainAxisSize.min
Column(
  children: [
    Text('Header'),
    Column(
      mainAxisSize: MainAxisSize.min,  // 只占用内容高度
      children: [Text('A'), Text('B')],
    ),
  ],
)

// ✅ 正确方案2：使用 Expanded 限制
Column(
  children: [
    Text('Header'),
    Expanded(
      child: SingleChildScrollView(  // 可滚动
        child: Column(
          children: [Text('A'), Text('B')],
        ),
      ),
    ),
  ],
)
```

### 陷阱3：mainAxisSize 默认值

```dart
// Column 默认 mainAxisSize = MainAxisSize.max（占满高度）
Column(
  children: [Text('A')],  // Column 会占满整个高度
)

// 如果只想包裹内容
Column(
  mainAxisSize: MainAxisSize.min,  // 只占内容所需高度
  children: [Text('A')],
)
```

### 陷阱4：子组件超出边界

```dart
// ❌ 问题：子组件高度超过 Column 高度会导致溢出
Column(
  children: [
    Container(height: 300, color: Colors.red),
    Container(height: 300, color: Colors.blue),  // 总高度 600，可能溢出
  ],
)

// ✅ 解决方案：使用 SingleChildScrollView
SingleChildScrollView(
  child: Column(
    children: [
      Container(height: 300, color: Colors.red),
      Container(height: 300, color: Colors.blue),
    ],
  ),
)
```

### 陷阱5：IntrinsicHeight 性能问题

```dart
// ❌ 避免：IntrinsicHeight 性能开销大
IntrinsicHeight(
  child: Column(
    children: [
      Container(width: 50, height: 100, color: Colors.red),
      Container(width: 100, height: 50, color: Colors.blue),
    ],
  ),
)

// ✅ 更好：明确设置尺寸或使用其他布局方式
Column(
  crossAxisAlignment: CrossAxisAlignment.stretch,
  children: [
    SizedBox(height: 100, child: Container(color: Colors.red)),
    SizedBox(height: 50, child: Container(color: Colors.blue)),
  ],
)
```

---

## 6. 学习技巧

### 技巧1：使用 Spacer 简化

```dart
Column(
  children: [
    Header(),
    const Spacer(),  // 填充中间空间
    Footer(),
  ],
)
```

### 技巧2：理解约束传递

```
Column 收到约束（如高度 0-896）
     ↓
将约束分解给每个子组件
     ↓
Expanded/Flexible 子组件分享剩余空间
     ↓
确定每个子组件的最终尺寸
```

### 技巧3：动态子组件

```dart
// 使用条件表达式
Column(
  children: [
    if (showHeader) HeaderWidget(),
    ContentWidget(),
    if (showFooter) FooterWidget(),
  ],
)

// 使用列表展开
Column(
  children: [
    Header(),
    ...items.map((item) => ItemWidget(item)),
    Footer(),
  ],
)
```

### 技巧4：处理键盘弹出

```dart
// 使用 keyboardDismissBehavior
Column(
  keyboardDismissBehavior: ScrollViewKeyboardDismissBehavior.onDrag,
  children: [...],
)

// 或使用 SingleChildScrollView
SingleChildScrollView(
  child: Column(
    children: [
      TextField(),
      // 键盘弹出时可以滚动
    ],
  ),
)
```

---

## 7. Column vs Row 对比

| 方面 | Row | Column |
|------|-----|--------|
| **方向** | 水平 | 垂直 |
| **主轴** | 水平（X 轴） | 垂直（Y 轴） |
| **交叉轴** | 垂直（Y 轴） | 水平（X 轴） |
| **mainAxisAlignment** | 控制水平对齐 | 控制垂直对齐 |
| **crossAxisAlignment** | 控制垂直对齐 | 控制水平对齐 |
| **常见溢出** | 宽度溢出 | 高度溢出 |
| **默认约束** | 需要有限宽度 | 需要有限高度 |

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UIStackView (vertical)       → Column
axis = .vertical             → Column 默认垂直
distribution = .fill         → Expanded
distribution = .fillEqually  → Expanded(flex: 1) 每个子组件
spacing                      → SizedBox(height:) / Gap
alignment = .center          → mainAxisAlignment / crossAxisAlignment
setContentHuggingPriority    → Flexible
```

### 关键差异

| 方面 | UIKit UIStackView | Flutter Column |
|------|-------------------|----------------|
| **布局算法** | Auto Layout 约束求解 | 约束传递 + Flex 布局 |
| **间距** | `spacing` 属性 | `SizedBox` 或 `Gap` |
| **弹性子组件** | Content Hugging Priority | `Expanded` / `Flexible` |
| **对齐** | `alignment` + `distribution` | `mainAxisAlignment` + `crossAxisAlignment` |
| **无限高度** | 可正常工作 | 需要 `mainAxisSize.min` 或有限约束 |

### 最佳实践

1. **有限高度环境** - 确保 Column 在有限高度约束中，或使用 `MainAxisSize.min`
2. **Expanded 处理溢出** - 子组件可能溢出时使用 SingleChildScrollView 包裹
3. **Spacer 简化** - 用 `Spacer()` 替代 `Expanded(child: SizedBox())`
4. **动态子组件** - 使用条件表达式或 `...` 展开运算符

---

*最后更新: 2026-02-24*

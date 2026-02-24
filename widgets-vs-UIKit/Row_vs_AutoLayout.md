# Row vs UIStackView 对比学习

本文档记录 Flutter Row 组件与 UIKit UIStackView (horizontal) 的对比学习要点。

---

## 1. Row 核心概念

### Row 是什么

Row 是 Flutter 中用于水平排列子组件的布局组件，类似于 UIKit 中的 UIStackView（水平方向）。

- 子组件沿水平方向排列
- 支持主轴对齐（mainAxisAlignment）
- 支持交叉轴对齐（crossAxisAlignment）
- 支持弹性子组件（Expanded、Flexible）

### Row vs UIStackView 概念对比

| Flutter Row | UIKit UIStackView | 说明 |
|-------------|-------------------|------|
| `mainAxisAlignment` | `alignment` | 主轴对齐方式 |
| `crossAxisAlignment` | 无直接对应 | 交叉轴对齐 |
| `mainAxisSize` | 无直接对应 | 主轴尺寸 |
| `Expanded` | `distribution = .fill` | 填充剩余空间 |
| `SizedBox(width:)` | `spacing` | 子组件间距 |
| `verticalDirection` | 无直接对应 | 排列方向 |

---

## 2. 源码分析

### Row 继承关系

```dart
class Row extends Flex {
  const Row({
    super.key,
    super.mainAxisAlignment = MainAxisAlignment.start,
    super.mainAxisSize = MainAxisSize.max,
    super.crossAxisAlignment = CrossAxisAlignment.center,
    super.textDirection,
    super.verticalDirection = VerticalDirection.down,
    super.textBaseline,
    super.children = const <Widget>[],
  }) : super(
         direction: Axis.horizontal,  // 关键：方向为水平
       );
}
```

### Flex 布局算法

```dart
// Flex 是 Row 和 Column 的父类
// 布局算法：
// 1. 确定主轴方向（Row 为水平）
// 2. 计算子组件约束
// 3. 处理 Expanded/Flexible 子组件
// 4. 计算最终尺寸
```

---

## 3. 与 UIStackView 详细对比

### 3.1 基础水平布局

```objc
// Objective-C - UIStackView
UIStackView *stack = [[UIStackView alloc] init];
stack.axis = UILayoutConstraintAxisHorizontal;
stack.distribution = UIStackViewDistributionFill;
stack.alignment = UIStackViewAlignmentCenter;
stack.spacing = 8;

[stack addArrangedSubview:view1];
[stack addArrangedSubview:view2];
[stack addArrangedSubview:view3];
```

```dart
// Flutter - Row
Row(
  mainAxisAlignment: MainAxisAlignment.start,
  crossAxisAlignment: CrossAxisAlignment.center,
  children: [
    view1,
    SizedBox(width: 8),  // 间距
    view2,
    SizedBox(width: 8),
    view3,
  ],
)
```

### 3.2 主轴对齐 (Main Axis Alignment)

```objc
// Objective-C - UIStackView alignment
// UIStackView 的 alignment 主要用于交叉轴
// 主轴对齐需要通过 distribution 实现

stack.distribution = UIStackViewDistributionFill;           // 填充
stack.distribution = UIStackViewDistributionFillEqually;    // 等宽
stack.distribution = UIStackViewDistributionEqualSpacing;   // 等间距
```

```dart
// Flutter - Row mainAxisAlignment
Row(
  mainAxisAlignment: MainAxisAlignment.start,        // 左对齐（默认）
  mainAxisAlignment: MainAxisAlignment.end,          // 右对齐
  mainAxisAlignment: MainAxisAlignment.center,       // 居中
  mainAxisAlignment: MainAxisAlignment.spaceBetween, // 两端对齐，中间等分
  mainAxisAlignment: MainAxisAlignment.spaceAround,  // 每个子组件两侧等分
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,  // 完全等分
  children: [view1, view2, view3],
)
```

**对齐方式图解**：

```
start:     [■][■][■]                    
end:                     [■][■][■]      
center:          [■][■][■]              
spaceBetween: [■]      [■]      [■]     
spaceAround:   [■]    [■]    [■]        
spaceEvenly:    [■]   [■]   [■]         
```

### 3.3 交叉轴对齐 (Cross Axis Alignment)

```objc
// Objective-C - UIStackView alignment
stack.alignment = UIStackViewAlignmentFill;      // 填充
stack.alignment = UIStackViewAlignmentLeading;   // 顶部对齐
stack.alignment = UIStackViewAlignmentCenter;    // 居中
stack.alignment = UIStackViewAlignmentTrailing;  // 底部对齐
```

```dart
// Flutter - Row crossAxisAlignment
Row(
  crossAxisAlignment: CrossAxisAlignment.start,   // 顶部对齐
  crossAxisAlignment: CrossAxisAlignment.end,     // 底部对齐
  crossAxisAlignment: CrossAxisAlignment.center,  // 居中（默认）
  crossAxisAlignment: CrossAxisAlignment.stretch, // 拉伸填满
  crossAxisAlignment: CrossAxisAlignment.baseline, // 基线对齐
  textBaseline: TextBaseline.alphabetic,          // baseline 必须指定
  children: [
    Container(height: 50, child: Text('A')),
    Container(height: 80, child: Text('B')),
    Container(height: 30, child: Text('C')),
  ],
)
```

**交叉轴对齐图解**（Row 的交叉轴是垂直方向）：

```
start:    ┌──┐ ┌──┐ ┌──┐
          │A │ │B │ │C │
          └──┘ │  │ └──┘
                └──┘

center:        ┌──┐
          ┌──┐ │B │ ┌──┐
          │A │ │  │ │C │
          └──┘ └──┘ └──┘

end:                ┌──┐
          ┌──┐ ┌──┐ │C │
          │A │ │B │ └──┘
          └──┘ │  │
                └──┘

stretch:  ┌──┐ ┌──┐ ┌──┐
          │A │ │B │ │C │
          │  │ │  │ │  │
          └──┘ └──┘ └──┘
```

### 3.4 弹性布局

```objc
// Objective-C - UIStackView distribution
// 让某个子视图填充剩余空间
UIView *spacer = [[UIView alloc] init];
spacer.backgroundColor = [UIColor clearColor];
[spacer setContentHuggingPriority:UILayoutPriorityDefaultLow
                          forAxis:UILayoutConstraintAxisHorizontal];
[stack addArrangedSubview:spacer];
```

```dart
// Flutter - Expanded
Row(
  children: [
    Container(width: 50, color: Colors.red),
    Expanded(                    // 填充剩余空间
      child: Container(color: Colors.blue),
    ),
    Container(width: 50, color: Colors.green),
  ],
)
```

### 3.5 比例布局

```objc
// Objective-C - 需要手动设置约束
[view1.widthAnchor constraintEqualToAnchor:view2.widthAnchor multiplier:2].active = YES;
```

```dart
// Flutter - Expanded with flex
Row(
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

### 3.6 间距设置

```objc
// Objective-C - UIStackView spacing
stack.spacing = 8;                    // 统一间距
stack.customSpacing = 16 afterView: view1;  // iOS 11+ 自定义间距
```

```dart
// Flutter - 方式一：SizedBox
Row(
  children: [
    view1,
    const SizedBox(width: 16),  // 自定义间距
    view2,
    const SizedBox(width: 8),
    view3,
  ],
)

// 方式二：使用 package（如 gap）
Row(
  children: [
    view1,
    const Gap(16),
    view2,
    const Gap(8),
    view3,
  ],
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 创建一个水平布局的工具栏
UIStackView *toolbar = [[UIStackView alloc] init];
toolbar.axis = UILayoutConstraintAxisHorizontal;
toolbar.distribution = UIStackViewDistributionFill;
toolbar.alignment = UIStackViewAlignmentCenter;
toolbar.spacing = 8;
toolbar.translatesAutoresizingMaskIntoConstraints = NO;

UIButton *backButton = [UIButton buttonWithType:UIButtonTypeSystem];
[backButton setImage:[UIImage systemImageNamed:@"chevron.left"] forState:UIControlStateNormal];
backButton.widthAnchor.constraintEqualToConstant(44).active = YES;

UIView *spacer = [[UIView alloc] init];
spacer.backgroundColor = [UIColor clearColor];
[spacer setContentHuggingPriority:UILayoutPriorityDefaultLow
                          forAxis:UILayoutConstraintAxisHorizontal];

UIButton *actionButton = [UIButton buttonWithType:UIButtonTypeSystem];
[actionButton setImage:[UIImage systemImageNamed:@"ellipsis"] forState:UIControlStateNormal];
actionButton.widthAnchor.constraintEqualToConstant(44).active = YES;

[toolbar addArrangedSubview:backButton];
[toolbar addArrangedSubview:spacer];
[toolbar addArrangedSubview:actionButton];

[self.view addSubview:toolbar];
[toolbar.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor constant:16].active = YES;
[toolbar.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor constant:-16].active = YES;
[toolbar.topAnchor constraintEqualToAnchor:self.view.safeAreaLayoutGuide.topAnchor].active = YES;
[toolbar.heightAnchor constraintEqualToConstant:44].active = YES;
```

### Flutter 实现

```dart
// 创建同样的工具栏 - 更简洁
Row(
  children: [
    // 返回按钮
    SizedBox(
      width: 44,
      child: IconButton(
        icon: const Icon(Icons.chevron_left),
        onPressed: () {},
      ),
    ),
    // 弹性空白
    const Expanded(child: SizedBox()),
    // 操作按钮
    SizedBox(
      width: 44,
      child: IconButton(
        icon: const Icon(Icons.more_vert),
        onPressed: () {},
      ),
    ),
  ],
)

// 或使用 Toolbar 实现
Toolbar(
  leading: IconButton(
    icon: const Icon(Icons.chevron_left),
    onPressed: () {},
  ),
  trailing: IconButton(
    icon: const Icon(Icons.more_vert),
    onPressed: () {},
  ),
)
```

---

## 5. 常见陷阱

### 陷阱1：无限宽度错误

```dart
// ❌ 错误：Row 在无限宽度中需要 Expanded
Row(
  children: [
    Text('Left'),
    Text('Right'),  // 可能导致溢出
  ],
)

// ✅ 正确：使用 Expanded 或 Flexible
Row(
  children: [
    Text('Left'),
    Expanded(child: Text('Right')),  // 自动换行
  ],
)

// ✅ 或包裹在有限宽度容器中
SizedBox(
  width: 300,
  child: Row(
    children: [Text('Left'), Text('Right')],
  ),
)
```

### 陷阱2：嵌套 Row/Column 的约束问题

```dart
// ❌ 错误：嵌套的 Row 在外层 Row 中无法确定宽度
Row(
  children: [
    Row(                    // 内层 Row 宽度无法确定
      children: [Text('A'), Text('B')],
    ),
    Expanded(child: Container()),
  ],
)

// ✅ 正确：给内层 Row 明确约束
Row(
  children: [
    Flexible(               // 或 Expanded
      child: Row(
        children: [Text('A'), Text('B')],
      ),
    ),
    Expanded(child: Container()),
  ],
)
```

### 陷阱3：mainAxisSize 默认值

```dart
// Row 默认 mainAxisSize = MainAxisSize.max（占满宽度）
Row(
  children: [Text('A')],  // Row 会占满整个宽度
)

// 如果只想包裹内容
Row(
  mainAxisSize: MainAxisSize.min,  // 只占内容所需宽度
  children: [Text('A')],
)
```

### 陷阱4：baseline 对齐需要 textBaseline

```dart
// ❌ 错误：使用 baseline 但未指定 textBaseline
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,  // 报错！
  children: [
    Text('A', style: TextStyle(fontSize: 20)),
    Text('B', style: TextStyle(fontSize: 14)),
  ],
)

// ✅ 正确：必须指定 textBaseline
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [
    Text('A', style: TextStyle(fontSize: 20)),
    Text('B', style: TextStyle(fontSize: 14)),
  ],
)
```

### 陷阱5：子组件超出边界

```dart
// ❌ 问题：子组件宽度超过 Row 宽度会导致溢出
Row(
  children: [
    Container(width: 200, color: Colors.red),
    Container(width: 200, color: Colors.blue),  // 总宽度 400，可能溢出
  ],
)

// ✅ 解决方案1：使用 Expanded
Row(
  children: [
    Expanded(child: Container(height: 50, color: Colors.red)),
    Expanded(child: Container(height: 50, color: Colors.blue)),
  ],
)

// ✅ 解决方案2：使用 SingleChildScrollView
SingleChildScrollView(
  scrollDirection: Axis.horizontal,
  child: Row(
    children: [
      Container(width: 200, height: 50, color: Colors.red),
      Container(width: 200, height: 50, color: Colors.blue),
    ],
  ),
)
```

---

## 6. 学习技巧

### 技巧1：使用 LayoutBuilder 调试

```dart
LayoutBuilder(
  builder: (context, constraints) {
    debugPrint('Row constraints: $constraints');
    return Row(
      children: [...],
    );
  },
)
```

### 技巧2：理解约束传递

```
Row 收到约束（如宽度 0-414）
     ↓
将约束分解给每个子组件
     ↓
Expanded/Flexible 子组件分享剩余空间
     ↓
确定每个子组件的最终尺寸
```

### 技巧3：使用 Spacer 简化

```dart
// 代替 Expanded(child: SizedBox())
Row(
  children: [
    Text('Left'),
    const Spacer(),  // 等价于 Expanded(child: SizedBox())
    Text('Right'),
  ],
)
```

### 技巧4：提取间距为组件

```dart
// 定义间距组件
const SizedBox _hGap8 = SizedBox(width: 8);
const SizedBox _hGap16 = SizedBox(width: 16);

Row(
  children: [
    view1,
    _hGap8,
    view2,
    _hGap16,
    view3,
  ],
)
```

---

## 7. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UIStackView (horizontal)     → Row
axis = .horizontal           → Row 默认水平
distribution = .fill         → Expanded
distribution = .fillEqually  → Expanded(flex: 1) 每个子组件
spacing                      → SizedBox(width:) / Gap
alignment = .center          → mainAxisAlignment / crossAxisAlignment
setContentHuggingPriority    → Flexible
```

### 关键差异

| 方面 | UIKit UIStackView | Flutter Row |
|------|-------------------|-------------|
| **布局算法** | Auto Layout 约束求解 | 约束传递 + Flex 布局 |
| **间距** | `spacing` 属性 | `SizedBox` 或 `Gap` |
| **弹性子组件** | Content Hugging Priority | `Expanded` / `Flexible` |
| **对齐** | `alignment` + `distribution` | `mainAxisAlignment` + `crossAxisAlignment` |
| **动态子组件** | `addArrangedSubview:` | 条件表达式或列表 |

### 最佳实践

1. **有限宽度环境** - 确保 Row 在有限宽度约束中
2. **Expanded 处理溢出** - 子组件可能溢出时使用 Expanded
3. **Spacer 简化** - 用 `Spacer()` 替代 `Expanded(child: SizedBox())`
4. **间距抽取** - 统一间距常量便于维护

---

*最后更新: 2026-02-24*

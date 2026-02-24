# Flexible vs Auto Layout 对比学习

本文档记录 Flutter Flexible 组件与 UIKit Auto Layout 比例约束的对比学习要点。

---

## 1. Flexible 核心概念

### Flexible 是什么

Flexible 是 Flutter 中用于让子组件在 Flex 容器（Row、Column、Flex）中按比例分配空间的组件，但子组件可以选择小于分配的空间。

- 只能在 Flex 容器中使用
- 按 flex 比例分配剩余空间
- 子组件可以选择小于分配的空间（loose 约束）
- 是 Expanded 的父类

### Flexible vs Expanded vs Auto Layout 概念对比

| Flutter | UIKit Auto Layout | 说明 |
|---------|-------------------|------|
| `Flexible` | 中等 Content Hugging Priority | 可扩展但不必填满 |
| `Expanded` | 低 Content Hugging Priority | 必须填满分配空间 |
| `flex` | `multiplier:` | 比例分配 |
| `FlexFit.loose` | 无直接对应 | 可以小于分配空间 |
| `FlexFit.tight` | 无直接对应 | 必须填满分配空间 |

---

## 2. 源码分析

### Flexible 类定义

```dart
class Flexible extends ParentDataWidget<Flex> {
  const Flexible({
    super.key,
    this.flex = 1,              // 比例因子
    this.fit = FlexFit.loose,   // 默认 loose
    required super.child,
  });
}

// FlexFit 枚举
enum FlexFit {
  tight,   // 子组件必须填满分配空间（Expanded 使用）
  loose,   // 子组件可以小于分配空间
}
```

### Flexible vs Expanded 关系

```dart
// Expanded 是 Flexible 的子类
class Expanded extends Flexible {
  const Expanded({
    super.key,
    super.flex = 1,
    required super.child,
  }) : super(fit: FlexFit.tight);  // 关键：使用 tight
}
```

---

## 3. 与 Auto Layout 详细对比

### 3.1 Flexible 的核心特点

```dart
// Flexible 给子组件传递 loose 约束
// 子组件可以选择 0 到分配空间之间的任意尺寸

Row(
  children: [
    Flexible(
      child: Container(
        width: 50,  // 可以是 50，不强制填满
        color: Colors.red,
      ),
    ),
    Flexible(
      child: Container(
        width: 100,  // 可以是 100
        color: Colors.blue,
      ),
    ),
  ],
)
// 剩余空间被平分，但子组件可以选择自己的尺寸
```

### 3.2 与 Auto Layout 对比

```objc
// Objective-C - 类似 Flexible 的效果
// 需要设置中等优先级，让视图可以不填满

UIView *view1 = [[UIView alloc] init];
UIView *view2 = [[UIView alloc] init];

// 设置中等 Content Hugging Priority
[view1 setContentHuggingPriority:UILayoutPriorityDefaultHigh - 1
                          forAxis:UILayoutConstraintAxisHorizontal];
[view2 setContentHuggingPriority:UILayoutPriorityDefaultHigh - 1
                          forAxis:UILayoutConstraintAxisHorizontal];

// 视图可以在约束范围内选择自己的尺寸
[view1.widthAnchor constraintLessThanOrEqualToConstant:200].active = YES;
```

```dart
// Flutter - Flexible
Row(
  children: [
    Flexible(
      flex: 1,
      child: Container(
        width: 50,  // 实际宽度是 50
        color: Colors.red,
      ),
    ),
    Flexible(
      flex: 1,
      child: Container(
        width: 100,  // 实际宽度是 100
        color: Colors.blue,
      ),
    ),
  ],
)
```

### 3.3 图示对比

```
Row 宽度 300px，两个 Flexible(flex: 1)

Flexible + Flexible（子组件自定尺寸）:
┌────┬─────────┐
│ 50 │   100   │  （子组件可以选择小于 150）
└────┴─────────┘
分配: 150px   150px
实际: 50px    100px
剩余空间空着

Expanded + Expanded（子组件必须填满）:
┌──────────────┬──────────────┐
│    150px     │    150px     │  （必须填满）
└──────────────┴──────────────┘
```

---

## 4. 完整示例对比

### 使用场景：文本标签 + 值

```objc
// Objective-C - Auto Layout
UILabel *label = [[UILabel alloc] init];
label.text = @"Username:";
[label setContentHuggingPriority:UILayoutPriorityDefaultHigh
                          forAxis:UILayoutConstraintAxisHorizontal];

UILabel *value = [[UILabel alloc] init];
value.text = @"John Doe";
[value setContentHuggingPriority:UILayoutPriorityDefaultLow
                          forAxis:UILayoutConstraintAxisHorizontal];
[value setContentCompressionResistancePriority:UILayoutPriorityDefaultHigh
                                        forAxis:UILayoutConstraintAxisHorizontal];

// 布局
[stackView addArrangedSubview:label];
[stackView addArrangedSubview:value];
```

```dart
// Flutter - Flexible
Row(
  children: [
    Text('Username:'),  // 自然宽度
    Flexible(
      child: Text(
        'John Doe',
        overflow: TextOverflow.ellipsis,  // 超长时截断
      ),
    ),
  ],
)

// 或者使用 Expanded 强制填满
Row(
  children: [
    Text('Username:'),
    Expanded(
      child: Text(
        'John Doe Long Name That Might Overflow',
        overflow: TextOverflow.ellipsis,
      ),
    ),
  ],
)
```

### 多个 Flexible 比例布局

```dart
Row(
  children: [
    Flexible(
      flex: 1,
      child: Container(
        height: 100,
        color: Colors.red,
        child: Center(child: Text('1/6')),
      ),
    ),
    Flexible(
      flex: 2,
      child: Container(
        height: 100,
        color: Colors.green,
        child: Center(child: Text('2/6')),
      ),
    ),
    Flexible(
      flex: 3,
      child: Container(
        height: 100,
        color: Colors.blue,
        child: Center(child: Text('3/6')),
      ),
    ),
  ],
)
// 总 flex = 6，每个按比例分配
```

---

## 5. Flexible vs Expanded 选择指南

### 何时使用 Flexible

```dart
// ✅ 子组件需要保持自然尺寸
Row(
  children: [
    Text('Label'),
    Flexible(
      child: TextField(),  // TextField 可以选择自己的宽度
    ),
  ],
)

// ✅ 子组件可能会比分配空间小
Row(
  children: [
    Flexible(
      child: Text('Short'),  // 文本较短时不会强制拉伸
    ),
    Flexible(
      child: Text('A very long text that might overflow'),
    ),
  ],
)

// ✅ 需要子组件保持固有尺寸
Row(
  children: [
    Flexible(
      child: Image.asset('icon.png'),  // 图片保持原始比例
    ),
  ],
)
```

### 何时使用 Expanded

```dart
// ✅ 子组件必须填满空间
Row(
  children: [
    SizedBox(width: 100),
    Expanded(
      child: Container(color: Colors.blue),  // 必须填满剩余空间
    ),
  ],
)

// ✅ 背景色需要延伸
Row(
  children: [
    Expanded(
      child: Container(
        color: Colors.grey[200],
        child: Text('Background extends'),
      ),
    ),
  ],
)

// ✅ 分隔线
Row(
  children: [
    Text('Left'),
    Expanded(child: Divider()),  // 分隔线填满
    Text('Right'),
  ],
)
```

---

## 6. 常见陷阱

### 陷阱1：Flexible 在非 Flex 容器中

```dart
// ❌ 错误：只能在 Row/Column/Flex 中使用
Container(
  child: Flexible(  // 报错！
    child: Text('Error'),
  ),
)

// ✅ 正确：在 Row/Column 中
Row(
  children: [
    Flexible(child: Text('Correct')),
  ],
)
```

### 陷阱2：不理解 loose 约束

```dart
// ⚠️ 子组件宽度可能小于预期
Row(
  children: [
    Flexible(
      child: Container(
        color: Colors.red,
        child: Text('Short'),  // Container 只有文字宽度，不会填满
      ),
    ),
  ],
)

// ✅ 如果需要填满，使用 Expanded 或设置宽度
Row(
  children: [
    Expanded(
      child: Container(
        color: Colors.red,
        child: Text('Fills space'),
      ),
    ),
  ],
)
```

### 陷阱3：flex 为 0

```dart
// flex: 0 时，不参与空间分配
Row(
  children: [
    Flexible(
      flex: 0,  // 等同于普通子组件
      child: Container(width: 100, color: Colors.red),
    ),
    Flexible(
      flex: 1,
      child: Container(color: Colors.blue),  // 占据剩余全部
    ),
  ],
)
```

### 陷阱4：与固定尺寸组件混用

```dart
// ⚠️ 固定尺寸 + Flexible 可能导致意外布局
Row(
  children: [
    Container(width: 100, color: Colors.red),
    Flexible(
      child: Container(color: Colors.blue),
    ),
    Container(width: 100, color: Colors.green),
  ],
)
// Flexible 只能使用中间剩余的空间
```

---

## 7. 学习技巧

### 技巧1：理解约束传递

```dart
// Flexible 传递 loose 约束
// 子组件约束: minWidth=0, maxWidth=分配的宽度

// Expanded 传递 tight 约束
// 子组件约束: minWidth=maxWidth=分配的宽度
```

### 技巧2：调试布局

```dart
Row(
  children: [
    Flexible(
      child: LayoutBuilder(
        builder: (context, constraints) {
          debugPrint('Flexible constraints: $constraints');
          // 输出: BoxConstraints(0.0 <= w <= 150.0, ...)
          return Container(color: Colors.red);
        },
      ),
    ),
    Expanded(
      child: LayoutBuilder(
        builder: (context, constraints) {
          debugPrint('Expanded constraints: $constraints');
          // 输出: BoxConstraints(w=150.0, ...)  // tight
          return Container(color: Colors.blue);
        },
      ),
    ),
  ],
)
```

### 技巧3：组合使用

```dart
// 固定 + 弹性 + 固定的经典布局
Row(
  children: [
    SizedBox(width: 16),  // 固定边距
    Icon(Icons.star),     // 固定宽度图标
    SizedBox(width: 8),
    Flexible(             // 弹性内容
      child: Text('Title', overflow: TextOverflow.ellipsis),
    ),
    SizedBox(width: 8),
    IconButton(           // 固定宽度按钮
      icon: Icon(Icons.more_vert),
      onPressed: () {},
    ),
    SizedBox(width: 16),  // 固定边距
  ],
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
中等 Content Hugging Priority → Flexible
低 Content Hugging Priority    → Expanded
multiplier:                    → flex 参数
约束范围宽松                   → FlexFit.loose
约束范围严格                   → FlexFit.tight
```

### 关键差异

| 方面 | UIKit Auto Layout | Flutter Flexible |
|------|-------------------|------------------|
| **控制方式** | Priority | Flexible/Expanded |
| **填充模式** | Priority 调整 | fit 参数 |
| **比例分配** | multiplier | flex 参数 |
| **代码简洁** | 需要多个约束 | 单个组件包装 |

### 最佳实践

1. **子组件自定尺寸用 Flexible** - 让子组件决定自己的大小
2. **填满空间用 Expanded** - 强制子组件填满
3. **组合使用** - 固定 + Flexible + 固定
4. **注意约束类型** - loose vs tight

---

*最后更新: 2026-02-24*

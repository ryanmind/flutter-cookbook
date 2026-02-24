# Expanded vs Auto Layout 对比学习

本文档记录 Flutter Expanded 组件与 UIKit Auto Layout 填充约束的对比学习要点。

---

## 1. Expanded 核心概念

### Expanded 是什么

Expanded 是 Flutter 中用于让子组件填充父容器剩余空间的组件，类似于 Auto Layout 中设置低优先级的约束让视图填充剩余空间。

- 只能在 Flex 容器（Row、Column、Flex）中使用
- 按 flex 比例分配剩余空间
- 子组件必须能接受传入的约束

### Expanded vs Auto Layout 概念对比

| Flutter Expanded | UIKit Auto Layout | 说明 |
|------------------|-------------------|------|
| `Expanded` | 低 Content Hugging Priority | 填充剩余空间 |
| `flex` | `multiplier:` | 比例分配 |
| `Row + Expanded` | `leadingAnchor + trailingAnchor` | 水平填充 |
| `Column + Expanded` | `topAnchor + bottomAnchor` | 垂直填充 |
| `Flexible` | 中等优先级 | 可压缩可扩展 |

---

## 2. 源码分析

### Expanded 继承关系

```dart
class Expanded extends Flexible {
  const Expanded({
    super.key,
    super.flex = 1,
    required super.child,
  }) : super(fit: FlexFit.tight);  // 关键：tight 模式
}

// Flexible 是 Expanded 的父类
class Flexible extends ParentDataWidget<Flex> {
  const Flexible({
    super.key,
    this.flex = 1,
    this.fit = FlexFit.loose,  // 默认 loose 模式
    required super.child,
  });
}
```

### Expanded vs Flexible 关键差异

```dart
// Expanded - 必须填满分配的空间（tight）
Expanded(
  child: Container(),  // 必须填满分配的空间
)

// Flexible - 可以小于分配的空间（loose）
Flexible(
  child: Container(width: 50),  // 可以只占 50，剩余空间空着
)
```

### FlexFit 模式

```dart
enum FlexFit {
  tight,   // 子组件必须填满分配的空间
  loose,   // 子组件可以小于或等于分配的空间
}
```

---

## 3. 与 Auto Layout 详细对比

### 3.1 填充剩余空间

```objc
// Objective-C - Auto Layout 填充
UIView *container = [[UIView alloc] init];
UIView *left = [[UIView alloc] init];
UIView *right = [[UIView alloc] init];

// left 固定宽度
[left.widthAnchor constraintEqualToConstant:100].active = YES;

// right 填充剩余空间
[left.leadingAnchor constraintEqualToAnchor:container.leadingAnchor].active = YES;
[right.leadingAnchor constraintEqualToAnchor:left.trailingAnchor].active = YES;
[right.trailingAnchor constraintEqualToAnchor:container.trailingAnchor].active = YES;

// 通过降低 Content Hugging Priority 让 right 可扩展
[right setContentHuggingPriority:UILayoutPriorityDefaultLow
                          forAxis:UILayoutConstraintAxisHorizontal];
```

```dart
// Flutter - Expanded
Row(
  children: [
    SizedBox(width: 100, child: Container(color: Colors.red)),
    Expanded(
      child: Container(color: Colors.blue),  // 自动填充剩余宽度
    ),
  ],
)
```

### 3.2 比例分配

```objc
// Objective-C - 比例约束
[view1.widthAnchor constraintEqualToAnchor:view2.widthAnchor multiplier:2].active = YES;
// view1 : view2 = 2 : 1
```

```dart
// Flutter - flex 比例
Row(
  children: [
    Expanded(
      flex: 2,  // 占 2/3
      child: Container(color: Colors.red),
    ),
    Expanded(
      flex: 1,  // 占 1/3
      child: Container(color: Colors.blue),
    ),
  ],
)
```

### 3.3 多个 Expanded

```objc
// Objective-C - 多个视图等宽
[view1.widthAnchor constraintEqualToAnchor:view2.widthAnchor].active = YES;
[view2.widthAnchor constraintEqualToAnchor:view3.widthAnchor].active = YES;
```

```dart
// Flutter - 多个 Expanded 默认等分
Row(
  children: [
    Expanded(child: Container(color: Colors.red)),    // 各占 1/3
    Expanded(child: Container(color: Colors.blue)),
    Expanded(child: Container(color: Colors.green)),
  ],
)
```

### 3.4 固定 + 弹性组合

```objc
// Objective-C - 固定宽度 + 弹性宽度
UIView *sidebar = [[UIView alloc] init];
UIView *content = [[UIView alloc] init];

[sidebar.widthAnchor constraintEqualToConstant:200].active = YES;
[content setContentHuggingPriority:UILayoutPriorityDefaultLow
                            forAxis:UILayoutConstraintAxisHorizontal];
```

```dart
// Flutter - SizedBox + Expanded
Row(
  children: [
    SizedBox(
      width: 200,  // 固定宽度
      child: Container(color: Colors.red),
    ),
    Expanded(
      child: Container(color: Colors.blue),  // 弹性宽度
    ),
  ],
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 创建一个三列布局：固定侧边栏 + 弹性内容 + 固定侧边栏
UIView *container = [[UIView alloc] init];
container.translatesAutoresizingMaskIntoConstraints = NO;

UIView *leftSidebar = [[UIView alloc] init];
leftSidebar.backgroundColor = [UIColor redColor];
leftSidebar.translatesAutoresizingMaskIntoConstraints = NO;
[container addSubview:leftSidebar];

UIView *content = [[UIView alloc] init];
content.backgroundColor = [UIColor blueColor];
content.translatesAutoresizingMaskIntoConstraints = NO;
[container addSubview:content];

UIView *rightSidebar = [[UIView alloc] init];
rightSidebar.backgroundColor = [UIColor greenColor];
rightSidebar.translatesAutoresizingMaskIntoConstraints = NO;
[container addSubview:rightSidebar];

// 左侧边栏固定宽度
[leftSidebar.widthAnchor constraintEqualToConstant:80].active = YES;
[leftSidebar.leadingAnchor constraintEqualToAnchor:container.leadingAnchor].active = YES;
[leftSidebar.topAnchor constraintEqualToAnchor:container.topAnchor].active = YES;
[leftSidebar.bottomAnchor constraintEqualToAnchor:container.bottomAnchor].active = YES;

// 内容填充剩余空间
[content.leadingAnchor constraintEqualToAnchor:leftSidebar.trailingAnchor].active = YES;
[content.trailingAnchor constraintEqualToAnchor:rightSidebar.leadingAnchor].active = YES;
[content.topAnchor constraintEqualToAnchor:container.topAnchor].active = YES;
[content.bottomAnchor constraintEqualToAnchor:container.bottomAnchor].active = YES;
[content setContentHuggingPriority:UILayoutPriorityDefaultLow
                            forAxis:UILayoutConstraintAxisHorizontal];

// 右侧边栏固定宽度
[rightSidebar.widthAnchor constraintEqualToConstant:100].active = YES;
[rightSidebar.trailingAnchor constraintEqualToAnchor:container.trailingAnchor].active = YES;
[rightSidebar.topAnchor constraintEqualToAnchor:container.topAnchor].active = YES;
[rightSidebar.bottomAnchor constraintEqualToAnchor:container.bottomAnchor].active = YES;
```

### Flutter 实现

```dart
// 创建同样的三列布局 - 极其简洁
Row(
  children: [
    // 左侧边栏固定宽度
    SizedBox(
      width: 80,
      child: Container(color: Colors.red),
    ),
    // 内容填充剩余空间
    Expanded(
      child: Container(color: Colors.blue),
    ),
    // 右侧边栏固定宽度
    SizedBox(
      width: 100,
      child: Container(color: Colors.green),
    ),
  ],
)

// 添加比例布局示例
Row(
  children: [
    Expanded(
      flex: 1,  // 1/4 宽度
      child: Container(color: Colors.red),
    ),
    Expanded(
      flex: 2,  // 2/4 = 1/2 宽度
      child: Container(color: Colors.blue),
    ),
    Expanded(
      flex: 1,  // 1/4 宽度
      child: Container(color: Colors.green),
    ),
  ],
)
```

---

## 5. Expanded vs Flexible 详细对比

### 5.1 核心差异

```dart
// Expanded - 必须填满（tight）
Row(
  children: [
    Expanded(
      child: Container(
        width: 50,  // 无效！必须填满分配空间
        color: Colors.red,
      ),
    ),
  ],
)

// Flexible - 可以不填满（loose）
Row(
  children: [
    Flexible(
      child: Container(
        width: 50,  // 有效！可以是 50
        color: Colors.red,
      ),
    ),
  ],
)
```

### 5.2 图示对比

```
Row 宽度 300px，两个子组件

Expanded + Expanded:
┌──────────────┬──────────────┐
│   150px      │    150px     │
│  (必须填满)   │   (必须填满)  │
└──────────────┴──────────────┘

Flexible + Flexible:
┌────┬────┐                    
│ 50 │ 50 │  (可以是各自 50，剩余空间空着)
└────┴────┘                    

Expanded + Flexible:
┌──────────────┬────┐
│    250px     │ 50 │  (Expanded 填满剩余)
│  (必须填满)   │    │
└──────────────┴────┘
```

### 5.3 使用场景

```dart
// Expanded - 需要填满空间
Row(
  children: [
    Text('Label'),
    Expanded(
      child: TextField(),  // 输入框填满剩余空间
    ),
  ],
)

// Flexible - 可以不填满
Row(
  children: [
    Flexible(
      child: Text('Long text that might need to wrap'),  // 可以换行
    ),
    Icon(Icons.star),
  ],
)
```

---

## 6. 常见陷阱

### 陷阱1：在非 Flex 容器中使用

```dart
// ❌ 错误：Expanded 只能在 Row/Column/Flex 中使用
Container(
  child: Expanded(  // 报错！
    child: Text('Error'),
  ),
)

// ✅ 正确：在 Row/Column 中使用
Row(
  children: [
    Expanded(
      child: Text('Correct'),
    ),
  ],
)
```

### 陷阱2：子组件忽略约束

```dart
// ❌ 问题：子组件忽略 Expanded 的约束
Expanded(
  child: SizedBox(
    width: 50,  // Expanded 要求填满，但 SizedBox 限制为 50
    child: Container(color: Colors.red),
  ),
)
// 实际上会填满，SizedBox 在 Expanded 内会被忽略

// ✅ 理解：Expanded 会覆盖子组件的尺寸约束
Expanded(
  child: Container(
    color: Colors.red,
    width: 50,  // 这个会被忽略
  ),
)
```

### 陷阱3：嵌套 Expanded

```dart
// ❌ 错误：嵌套 Expanded 在不同方向
Row(
  children: [
    Expanded(
      child: Column(
        children: [
          Expanded(  // 这个 Expanded 控制垂直方向
            child: Container(),
          ),
        ],
      ),
    ),
  ],
)
// 外层 Expanded 控制水平，内层控制垂直，这是正确的

// 但如果方向相同会冲突
Row(
  children: [
    Expanded(
      child: Row(  // 内层 Row 又有 Expanded
        children: [
          Expanded(child: Container()),  // 与外层 Expanded 冲突！
        ],
      ),
    ),
  ],
)
```

### 陷阱4：flex 为 0

```dart
// flex: 0 时，Expanded 行为类似普通子组件
Row(
  children: [
    Expanded(
      flex: 0,  // 不参与空间分配
      child: Container(width: 100, color: Colors.red),
    ),
    Expanded(
      child: Container(color: Colors.blue),  // 填满剩余
    ),
  ],
)
// 相当于第一个子组件是固定宽度
```

### 陷阱5：无界约束环境

```dart
// ❌ 错误：在无界约束中使用
Column(
  children: [
    Row(  // Row 在 Column 中，宽度无界
      children: [
        Expanded(child: Container()),  // 正常，水平方向
      ],
    ),
  ],
)

// 但如果反过来
Row(
  children: [
    Column(  // Column 在 Row 中，高度无界
      children: [
        Expanded(child: Container()),  // 报错！垂直方向无界
      ],
    ),
  ],
)

// ✅ 解决方案：限制高度
Row(
  children: [
    SizedBox(
      height: 200,
      child: Column(
        children: [
          Expanded(child: Container()),
        ],
      ),
    ),
  ],
)
```

---

## 7. 学习技巧

### 技巧1：理解 flex 计算

```dart
// flex 计算公式
// 子组件宽度 = (剩余空间 / 总flex) * 该子组件flex

Row(
  children: [
    Expanded(flex: 1, child: A),  // 1/6 剩余空间
    Expanded(flex: 2, child: B),  // 2/6 = 1/3 剩余空间
    Expanded(flex: 3, child: C),  // 3/6 = 1/2 剩余空间
  ],
)
// 总 flex = 1 + 2 + 3 = 6
```

### 技巧2：Spacer 是 Expanded 的语法糖

```dart
// Spacer() 等价于 Expanded(child: SizedBox())
Row(
  children: [
    Text('Left'),
    const Spacer(),  // 填充中间空白
    Text('Right'),
  ],
)

// 等价于
Row(
  children: [
    Text('Left'),
    Expanded(child: const SizedBox()),
    Text('Right'),
  ],
)
```

### 技巧3：调试 Expanded 行为

```dart
// 使用 LayoutBuilder 调试
Expanded(
  child: LayoutBuilder(
    builder: (context, constraints) {
      debugPrint('Expanded constraints: $constraints');
      return Container(
        color: Colors.blue,
        child: Text('Width: ${constraints.maxWidth}'),
      );
    },
  ),
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
setContentHuggingPriority   → Expanded / Flexible
leadingAnchor+trailingAnchor → Row + Expanded
topAnchor+bottomAnchor      → Column + Expanded
multiplier:                 → flex 参数
UILayoutPriorityDefaultLow  → Expanded
UILayoutPriorityDefaultHigh → 固定尺寸子组件
```

### 关键差异

| 方面 | UIKit Auto Layout | Flutter Expanded |
|------|-------------------|------------------|
| **声明方式** | 约束 + 优先级 | 组件包装 |
| **比例分配** | multiplier | flex 参数 |
| **必填/可选** | Priority 控制 | Expanded/Flexible |
| **作用范围** | 单个约束 | 整个子组件 |
| **嵌套** | 自动处理 | 需注意方向 |

### 最佳实践

1. **填满空间用 Expanded** - 需要子组件填满分配空间
2. **可选填充用 Flexible** - 子组件可以小于分配空间
3. **比例布局用 flex** - 多个 Expanded 用 flex 分配比例
4. **简化空白用 Spacer** - 填充空白区域

---

*最后更新: 2026-02-24*

# Positioned vs Auto Layout 对比学习

本文档记录 Flutter Positioned 组件与 UIKit Auto Layout 绝对定位约束的对比学习要点。

---

## 1. Positioned 核心概念

### Positioned 是什么

Positioned 是 Flutter 中用于在 Stack 中绝对定位子组件的组件，类似于 Auto Layout 中的绝对定位约束。

- 只能在 Stack 中使用
- 通过 left、top、right、bottom 定位
- 支持宽高设置
- 支持填充整个 Stack（Positioned.fill）

### Positioned vs Auto Layout 概念对比

| Flutter Positioned | UIKit Auto Layout | 说明 |
|--------------------|-------------------|------|
| `left` | `leadingAnchor` | 左边距 |
| `top` | `topAnchor` | 上边距 |
| `right` | `trailingAnchor` | 右边距 |
| `bottom` | `bottomAnchor` | 下边距 |
| `width` | `widthAnchor` | 宽度 |
| `height` | `heightAnchor` | 高度 |
| `Positioned.fill` | 四边约束为 0 | 填充 |

---

## 2. 源码分析

### Positioned 类定义

```dart
class Positioned extends ParentDataWidget<Stack> {
  const Positioned({
    super.key,
    this.left,        // 距左边距离
    this.top,         // 距顶部距离
    this.right,       // 距右边距离
    this.bottom,      // 距底部距离
    this.width,       // 宽度
    this.height,      // 高度
    required super.child,
  });
  
  // 填充整个 Stack
  const Positioned.fill({
    super.key,
    required super.child,
  }) : left = 0.0, top = 0.0, right = 0.0, bottom = 0.0, width = null, height = null;
  
  // 从 Rect 创建
  Positioned.fromRect({
    super.key,
    required Rect rect,
    required super.child,
  }) : left = rect.left,
       top = rect.top,
       width = rect.width,
       height = rect.height,
       right = null,
       bottom = null;
  
  // 相对定位
  const Positioned.directional({
    super.key,
    required TextDirection textDirection,
    double? start,    // 文本方向相关的开始
    double? top,
    double? end,      // 文本方向相关的结束
    double? bottom,
    double? width,
    double? height,
    required super.child,
  });
}
```

---

## 3. 与 Auto Layout 详细对比

### 3.1 基础定位

```objc
// Objective-C - Auto Layout
UIView *view = [[UIView alloc] init];
view.translatesAutoresizingMaskIntoConstraints = NO;
[container addSubview:view];

[view.leadingAnchor constraintEqualToAnchor:container.leadingAnchor constant:20].active = YES;
[view.topAnchor constraintEqualToAnchor:container.topAnchor constant:20].active = YES;
[view.widthAnchor constraintEqualToConstant:100].active = YES;
[view.heightAnchor constraintEqualToConstant:100].active = YES;
```

```dart
// Flutter - Positioned
Stack(
  children: [
    Positioned(
      left: 20,
      top: 20,
      width: 100,
      height: 100,
      child: Container(color: Colors.red),
    ),
  ],
)
```

### 3.2 四边定位

```objc
// Objective-C - 距四边距离
[view.leadingAnchor constraintEqualToAnchor:container.leadingAnchor constant:10].active = YES;
[view.trailingAnchor constraintEqualToAnchor:container.trailingAnchor constant:-10].active = YES;
[view.topAnchor constraintEqualToAnchor:container.topAnchor constant:10].active = YES;
[view.bottomAnchor constraintEqualToAnchor:container.bottomAnchor constant:-10].active = YES;
```

```dart
// Flutter - 四边定位
Stack(
  children: [
    Positioned(
      left: 10,
      right: 10,
      top: 10,
      bottom: 10,
      child: Container(color: Colors.blue),
    ),
  ],
)
```

### 3.3 填充父容器

```objc
// Objective-C
[view.leadingAnchor constraintEqualToAnchor:container.leadingAnchor].active = YES;
[view.trailingAnchor constraintEqualToAnchor:container.trailingAnchor].active = YES;
[view.topAnchor constraintEqualToAnchor:container.topAnchor].active = YES;
[view.bottomAnchor constraintEqualToAnchor:container.bottomAnchor].active = YES;
```

```dart
// Flutter - Positioned.fill
Stack(
  children: [
    Positioned.fill(
      child: Container(color: Colors.green),
    ),
  ],
)

// 等价于
Positioned(
  left: 0,
  right: 0,
  top: 0,
  bottom: 0,
  child: Container(color: Colors.green),
)
```

### 3.4 右下角定位

```objc
// Objective-C
[view.trailingAnchor constraintEqualToAnchor:container.trailingAnchor constant:-20].active = YES;
[view.bottomAnchor constraintEqualToAnchor:container.bottomAnchor constant:-20].active = YES;
[view.widthAnchor constraintEqualToConstant:50].active = YES;
[view.heightAnchor constraintEqualToConstant:50].active = YES;
```

```dart
// Flutter
Stack(
  children: [
    Positioned(
      right: 20,
      bottom: 20,
      width: 50,
      height: 50,
      child: Container(color: Colors.orange),
    ),
  ],
)
```

### 3.5 居中定位

```objc
// Objective-C
[view.centerXAnchor constraintEqualToAnchor:container.centerXAnchor].active = YES;
[view.centerYAnchor constraintEqualToAnchor:container.centerYAnchor].active = YES;
```

```dart
// Flutter - Positioned 本身不支持居中
// 使用 Align 或 Center
Stack(
  children: [
    Align(
      alignment: Alignment.center,
      child: Container(width: 100, height: 100, color: Colors.purple),
    ),
  ],
)

// 或计算位置
LayoutBuilder(
  builder: (context, constraints) {
    return Stack(
      children: [
        Positioned(
          left: (constraints.maxWidth - 100) / 2,
          top: (constraints.maxHeight - 100) / 2,
          width: 100,
          height: 100,
          child: Container(color: Colors.purple),
        ),
      ],
    );
  },
)
```

### 3.6 宽度和高度约束

```objc
// Objective-C
[view.widthAnchor constraintEqualToConstant:200].active = YES;
[view.heightAnchor constraintEqualToConstant:100].active = YES;

// 或范围约束
[view.widthAnchor constraintGreaterThanOrEqualToConstant:100].active = YES;
[view.widthAnchor constraintLessThanOrEqualToConstant:200].active = YES;
```

```dart
// Flutter
Positioned(
  left: 0,
  top: 0,
  width: 200,
  height: 100,
  child: Container(color: Colors.red),
)

// 如果需要范围约束，使用 ConstrainedBox
Positioned(
  left: 0,
  top: 0,
  child: ConstrainedBox(
    constraints: BoxConstraints(
      minWidth: 100,
      maxWidth: 200,
      minHeight: 50,
      maxHeight: 100,
    ),
    child: Container(color: Colors.red),
  ),
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 创建一个带浮动按钮的图片查看器
UIView *container = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 300, 300)];
container.backgroundColor = [UIColor blackColor];

// 图片 - 填充容器
UIImageView *imageView = [[UIImageView alloc] init];
imageView.translatesAutoresizingMaskIntoConstraints = NO;
imageView.contentMode = UIViewContentModeScaleAspectFit;
imageView.image = [UIImage imageNamed:@"photo"];
[container addSubview:imageView];

[imageView.leadingAnchor constraintEqualToAnchor:container.leadingAnchor].active = YES;
[imageView.trailingAnchor constraintEqualToAnchor:container.trailingAnchor].active = YES;
[imageView.topAnchor constraintEqualToAnchor:container.topAnchor].active = YES;
[imageView.bottomAnchor constraintEqualToAnchor:container.bottomAnchor].active = YES;

// 关闭按钮 - 右上角
UIButton *closeButton = [UIButton buttonWithType:UIButtonTypeSystem];
closeButton.translatesAutoresizingMaskIntoConstraints = NO;
[closeButton setImage:[UIImage systemImageNamed:@"xmark.circle.fill"] forState:UIControlStateNormal];
closeButton.tintColor = [UIColor whiteColor];
[container addSubview:closeButton];

[closeButton.trailingAnchor constraintEqualToAnchor:container.trailingAnchor constant:-16].active = YES;
[closeButton.topAnchor constraintEqualToAnchor:container.topAnchor constant:16].active = YES;
[closeButton.widthAnchor constraintEqualToConstant:44].active = YES;
[closeButton.heightAnchor constraintEqualToConstant:44].active = YES;

// 标签 - 底部居中
UILabel *label = [[UILabel alloc] init];
label.translatesAutoresizingMaskIntoConstraints = NO;
label.text = @"Photo Title";
label.textColor = [UIColor whiteColor];
label.font = [UIFont boldSystemFontOfSize:16];
[container addSubview:label];

[label.centerXAnchor constraintEqualToAnchor:container.centerXAnchor].active = YES;
[label.bottomAnchor constraintEqualToAnchor:container.bottomAnchor constant:-16].active = YES;
```

### Flutter 实现

```dart
// 创建同样的图片查看器 - 更简洁
Container(
  width: 300,
  height: 300,
  color: Colors.black,
  child: Stack(
    children: [
      // 图片 - 填充
      Positioned.fill(
        child: Image.asset(
          'assets/photo.jpg',
          fit: BoxFit.contain,
        ),
      ),
      // 关闭按钮 - 右上角
      Positioned(
        right: 16,
        top: 16,
        child: IconButton(
          icon: Icon(Icons.cancel, color: Colors.white, size: 44),
          onPressed: () {},
        ),
      ),
      // 标签 - 底部居中
      Positioned(
        left: 0,
        right: 0,
        bottom: 16,
        child: Text(
          'Photo Title',
          textAlign: TextAlign.center,
          style: TextStyle(
            color: Colors.white,
            fontSize: 16,
            fontWeight: FontWeight.bold,
          ),
        ),
      ),
    ],
  ),
)
```

---

## 5. Positioned 与其他定位组件对比

| 组件 | 特点 | 使用场景 |
|------|------|---------|
| **Positioned** | 绝对定位 | Stack 中的精确位置 |
| **Align** | 相对定位 | 按比例定位 |
| **Center** | 居中 | 简单居中 |
| **Flexible** | 弹性定位 | Row/Column 中 |
| **Expanded** | 填充定位 | Row/Column 中填充 |

### 组合使用

```dart
// Stack 中混合使用
Stack(
  children: [
    // 背景填充
    Positioned.fill(
      child: Container(color: Colors.grey),
    ),
    // 左上角
    Positioned(
      left: 16,
      top: 16,
      child: Icon(Icons.star),
    ),
    // 居中
    Align(
      alignment: Alignment.center,
      child: Text('Center'),
    ),
    // 右下角
    Positioned(
      right: 16,
      bottom: 16,
      child: Icon(Icons.share),
    ),
  ],
)
```

---

## 6. 常见陷阱

### 陷阱1：Positioned 在非 Stack 中使用

```dart
// ❌ 错误：只能在 Stack 中使用
Container(
  child: Positioned(  // 报错！
    left: 20,
    top: 20,
    child: Text('Error'),
  ),
)

// ✅ 正确：在 Stack 中
Stack(
  children: [
    Positioned(
      left: 20,
      top: 20,
      child: Text('Correct'),
    ),
  ],
)
```

### 陷阱2：width/right 和 height/bottom 冲突

```dart
// ❌ 同时设置 width 和 left/right 会冲突
Positioned(
  left: 0,
  right: 0,
  width: 100,  // 冲突！width 会覆盖 left/right
  child: Container(),
)

// ✅ 只用一种方式
Positioned(
  left: 0,
  right: 0,  // 宽度由 left 和 right 决定
  child: Container(),
)

// 或
Positioned(
  left: 0,
  width: 100,  // 宽度固定
  child: Container(),
)
```

### 陷阱3：部分定位

```dart
// 只设置部分属性
Positioned(
  left: 20,
  top: 20,
  // 没有 right 和 bottom
  child: Container(color: Colors.red),  // 需要子组件有自己的尺寸
)

// ⚠️ 如果子组件没有尺寸会报错
Positioned(
  left: 20,
  top: 20,
  child: Container(),  // 报错！Container 没有尺寸
)
```

### 陷阱4：负值定位

```dart
// 负值可以让子组件超出 Stack 边界
Stack(
  clipBehavior: Clip.none,  // 允许溢出
  children: [
    Positioned(
      right: -20,  // 向右溢出 20
      top: 20,
      child: Container(width: 50, height: 50, color: Colors.red),
    ),
  ],
)
```

---

## 7. 学习技巧

### 技巧1：Positioned.fill 快捷

```dart
// 代替写四个 0
Positioned.fill(child: ...)

// 等价于
Positioned(left: 0, right: 0, top: 0, bottom: 0, child: ...)
```

### 技巧2：动态定位

```dart
final position = Offset(100, 200);

Positioned(
  left: position.dx,
  top: position.dy,
  child: DraggableWidget(),
)
```

### 技巧3：相对父容器比例定位

```dart
LayoutBuilder(
  builder: (context, constraints) {
    return Stack(
      children: [
        Positioned(
          left: constraints.maxWidth * 0.1,   // 10% 从左边
          top: constraints.maxHeight * 0.2,   // 20% 从顶部
          width: constraints.maxWidth * 0.8,  // 80% 宽度
          height: constraints.maxHeight * 0.6, // 60% 高度
          child: Container(color: Colors.blue),
        ),
      ],
    );
  },
)
```

### 技巧4：AnimatedPositioned

```dart
// 动画版本的 Positioned
AnimatedPositioned(
  duration: Duration(milliseconds: 300),
  curve: Curves.easeInOut,
  left: isExpanded ? 0 : 100,
  top: isExpanded ? 0 : 100,
  width: isExpanded ? 300 : 100,
  height: isExpanded ? 300 : 100,
  child: Container(color: Colors.red),
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
leadingAnchor.constant     → left
trailingAnchor.constant    → right
topAnchor.constant         → top
bottomAnchor.constant      → bottom
widthAnchor.constant       → width
heightAnchor.constant      → height
四边约束为 0                → Positioned.fill
centerXAnchor             → Align(alignment: Alignment.center)
centerYAnchor             → Align(alignment: Alignment.center)
```

### 关键差异

| 方面 | UIKit Auto Layout | Flutter Positioned |
|------|-------------------|-------------------|
| **容器要求** | 任意父视图 | 必须在 Stack 中 |
| **设置方式** | 约束 | 属性 |
| **居中** | centerX/Y | 需要 Align |
| **动画** | animateWith | AnimatedPositioned |
| **溢出** | 需要设置 clipsToBounds | clipBehavior |

### 最佳实践

1. **只能在 Stack 中** - Positioned 必须是 Stack 的子组件
2. **避免冲突** - 不要同时设置 width 和 left/right
3. **使用 fill** - 需要填充时用 Positioned.fill
4. **动画用 AnimatedPositioned** - 位置变化动画

---

*最后更新: 2026-02-24*

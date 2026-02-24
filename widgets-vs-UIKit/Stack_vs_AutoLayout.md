# Stack vs UIView 层级 对比学习

本文档记录 Flutter Stack 组件与 UIKit UIView 层级布局的对比学习要点。

---

## 1. Stack 核心概念

### Stack 是什么

Stack 是 Flutter 中用于层叠布局的组件，允许子组件相互覆盖堆叠，类似于 UIKit 中多个 UIView 的层级叠加。

- 子组件按顺序从下到上堆叠
- 支持 Positioned 定位子组件
- 支持 IndexedStack 显示单个子组件
- 支持对齐方式设置

### Stack vs UIView 层级 概念对比

| Flutter Stack | UIKit UIView 层级 | 说明 |
|---------------|-------------------|------|
| `children[0]` | `addSubview:` 第一个 | 最底层 |
| `children[n]` | `addSubview:` 最后一个 | 最顶层 |
| `Positioned` | Auto Layout 约束 | 绝对定位 |
| `alignment` | `center` 约束 | 默认对齐 |
| `fit` | 无直接对应 | 子组件尺寸适配 |
| `clipBehavior` | `clipsToBounds` | 裁剪溢出 |

---

## 2. 源码分析

### Stack 继承关系

```dart
class Stack extends MultiChildRenderObjectWidget {
  const Stack({
    super.key,
    this.alignment = AlignmentDirectional.topStart,  // 默认左上角
    this.textDirection,
    this.fit = StackFit.loose,      // 子组件自定尺寸
    this.clipBehavior = Clip.hardEdge,
    super.children = const <Widget>[],
  });
}

// Stack 的定位子组件
class Positioned extends ParentDataWidget<Stack> {
  const Positioned({
    super.key,
    this.left,        // 距左边距离
    this.top,         // 距顶部距离
    this.right,       // 距右边距离
    this.bottom,      // 距底部距离
    this.width,       // 指定宽度
    this.height,      // 指定高度
    required super.child,
  });
}
```

### Stack 布局算法

```dart
// 1. Stack 先确定自身尺寸
//    - 如果有非定位子组件，取最大尺寸
//    - 如果全是定位子组件，尝试填满父容器

// 2. 非定位子组件根据 alignment 对齐
// 3. 定位子组件根据 left/top/right/bottom/width/height 定位
```

---

## 3. 与 UIView 层级详细对比

### 3.1 基础层叠

```objc
// Objective-C - UIView 层级
UIView *container = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 200, 200)];

UIView *background = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 200, 200)];
background.backgroundColor = [UIColor blueColor];
[container addSubview:background];  // 底层

UIView *foreground = [[UIView alloc] initWithFrame:CGRectMake(50, 50, 100, 100)];
foreground.backgroundColor = [UIColor redColor];
[container addSubview:foreground];  // 顶层

// 层级顺序：background 在下，foreground 在上
```

```dart
// Flutter - Stack
Stack(
  children: [
    Container(
      width: 200,
      height: 200,
      color: Colors.blue,  // 底层
    ),
    Positioned(
      left: 50,
      top: 50,
      child: Container(
        width: 100,
        height: 100,
        color: Colors.red,  // 顶层
      ),
    ),
  ],
)
```

### 3.2 层级顺序

```objc
// Objective-C - 调整层级顺序
[container bringSubviewToFront:background];   // 移到最前
[container sendSubviewToBack:foreground];     // 移到最后
[container exchangeSubviewAtIndex:0 withSubviewAtIndex:1];  // 交换
```

```dart
// Flutter - 调整 children 顺序
Stack(
  children: [
    RedBox(),    // 底层
    BlueBox(),   // 中层
    GreenBox(),  // 顶层
  ],
)

// 调整顺序只需改变 children 列表顺序
```

### 3.3 绝对定位

```objc
// Objective-C - Auto Layout 绝对定位
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

### 3.4 填充父容器

```objc
// Objective-C - 填充父容器
UIView *view = [[UIView alloc] init];
view.translatesAutoresizingMaskIntoConstraints = NO;
[container addSubview:view];

[view.leadingAnchor constraintEqualToAnchor:container.leadingAnchor].active = YES;
[view.trailingAnchor constraintEqualToAnchor:container.trailingAnchor].active = YES;
[view.topAnchor constraintEqualToAnchor:container.topAnchor].active = YES;
[view.bottomAnchor constraintEqualToAnchor:container.bottomAnchor].active = YES;
```

```dart
// Flutter - Positioned.fill
Stack(
  children: [
    Positioned.fill(  // 填充整个 Stack
      child: Container(color: Colors.red),
    ),
  ],
)

// 或使用 SizedBox.expand
Stack(
  children: [
    SizedBox.expand(
      child: Container(color: Colors.red),
    ),
  ],
)
```

### 3.5 居中对齐

```objc
// Objective-C - Auto Layout 居中
[view.centerXAnchor constraintEqualToAnchor:container.centerXAnchor].active = YES;
[view.centerYAnchor constraintEqualToAnchor:container.centerYAnchor].active = YES;
```

```dart
// Flutter - Stack alignment
Stack(
  alignment: Alignment.center,  // 所有非定位子组件居中
  children: [
    Container(width: 200, height: 200, color: Colors.blue),
    Container(width: 100, height: 100, color: Colors.red),  // 居中
  ],
)

// 或使用 Center
Stack(
  children: [
    Container(width: 200, height: 200, color: Colors.blue),
    Center(
      child: Container(width: 100, height: 100, color: Colors.red),
    ),
  ],
)
```

### 3.6 相对定位

```objc
// Objective-C - 相对于四边定位
[view.leadingAnchor constraintEqualToAnchor:container.leadingAnchor constant:10].active = YES;
[view.trailingAnchor constraintEqualToAnchor:container.trailingAnchor constant:-10].active = YES;
[view.topAnchor constraintEqualToAnchor:container.topAnchor constant:10].active = YES;
[view.bottomAnchor constraintEqualToAnchor:container.bottomAnchor constant:-10].active = YES;
```

```dart
// Flutter - Positioned 四边定位
Stack(
  children: [
    Positioned(
      left: 10,
      right: 10,
      top: 10,
      bottom: 10,  // 距四边各 10pt
      child: Container(color: Colors.red),
    ),
  ],
)
```

### 3.7 裁剪溢出

```objc
// Objective-C - clipsToBounds
container.clipsToBounds = YES;  // 裁剪超出边界的内容
```

```dart
// Flutter - clipBehavior
Stack(
  clipBehavior: Clip.hardEdge,  // 裁剪溢出
  children: [
    Container(width: 100, height: 100, color: Colors.blue),
    Positioned(
      left: 80,
      child: Container(width: 50, height: 50, color: Colors.red),  // 部分溢出
    ),
  ],
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 创建一个带徽章的头像
UIView *avatarContainer = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 60, 60)];

// 头像
UIImageView *avatar = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"avatar"]];
avatar.frame = CGRectMake(0, 0, 50, 50);
avatar.layer.cornerRadius = 25;
avatar.clipsToBounds = YES;
[avatarContainer addSubview:avatar];

// 徽章
UIView *badge = [[UIView alloc] initWithFrame:CGRectMake(40, 0, 20, 20)];
badge.backgroundColor = [UIColor redColor];
badge.layer.cornerRadius = 10;
[avatarContainer addSubview:badge];

// 徽章数字
UILabel *badgeLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 20, 20)];
badgeLabel.text = @"3";
badgeLabel.textColor = [UIColor whiteColor];
badgeLabel.font = [UIFont boldSystemFontOfSize:12];
badgeLabel.textAlignment = NSTextAlignmentCenter;
[badge addSubview:badgeLabel];
```

### Flutter 实现

```dart
// 创建同样的带徽章头像 - 更简洁
SizedBox(
  width: 60,
  height: 60,
  child: Stack(
    clipBehavior: Clip.none,  // 允许徽章溢出
    children: [
      // 头像
      Positioned(
        left: 0,
        top: 5,
        child: CircleAvatar(
          radius: 25,
          backgroundImage: AssetImage('assets/avatar.png'),
        ),
      ),
      // 徽章
      Positioned(
        right: 0,
        top: 0,
        child: Container(
          width: 20,
          height: 20,
          decoration: BoxDecoration(
            color: Colors.red,
            shape: BoxShape.circle,
          ),
          child: const Center(
            child: Text(
              '3',
              style: TextStyle(
                color: Colors.white,
                fontSize: 12,
                fontWeight: FontWeight.bold,
              ),
            ),
          ),
        ),
      ),
    ],
  ),
)

// 更简洁的写法
Stack(
  clipBehavior: Clip.none,
  children: [
    const CircleAvatar(
      radius: 25,
      backgroundImage: AssetImage('assets/avatar.png'),
    ),
    Positioned(
      right: -5,
      top: -5,
      child: Container(
        padding: const EdgeInsets.all(4),
        decoration: const BoxDecoration(
          color: Colors.red,
          shape: BoxShape.circle,
        ),
        child: const Text('3', style: TextStyle(color: Colors.white, fontSize: 12)),
      ),
    ),
  ],
)
```

---

## 5. Stack 特有功能

### 5.1 IndexedStack

```dart
// IndexedStack 只显示指定索引的子组件
// 其他子组件仍然存在，只是隐藏
IndexedStack(
  index: currentIndex,  // 当前显示的索引
  children: [
    Page1(),
    Page2(),
    Page3(),
  ],
)

// 切换页面时保持状态
setState(() {
  currentIndex = 1;  // 切换到 Page2
});
```

### 5.2 StackFit

```dart
// StackFit.loose - 子组件可以使用小于等于 Stack 的尺寸（默认）
Stack(
  fit: StackFit.loose,
  children: [
    Container(width: 100, height: 100, color: Colors.red),  // 可以比 Stack 小
  ],
)

// StackFit.expand - 子组件填满 Stack
Stack(
  fit: StackFit.expand,
  children: [
    Container(color: Colors.red),  // 必须填满 Stack
  ],
)

// StackFit.passthrough - Stack 使用父容器的约束
Stack(
  fit: StackFit.passthrough,
  children: [
    Container(color: Colors.red),  // 使用父容器约束
  ],
)
```

### 5.3 alignment 详解

```dart
// alignment 影响非定位子组件的位置
Stack(
  alignment: Alignment.topLeft,      // 左上角
  alignment: Alignment.topRight,     // 右上角
  alignment: Alignment.bottomLeft,   // 左下角
  alignment: Alignment.bottomRight,  // 右下角
  alignment: Alignment.center,       // 居中
  alignment: Alignment(0.5, 0.5),    // 自定义位置 (-1 到 1)
  children: [
    Container(width: 200, height: 200, color: Colors.blue),
    Container(width: 50, height: 50, color: Colors.red),  // 根据 alignment 对齐
  ],
)
```

---

## 6. 常见陷阱

### 陷阱1：Stack 无约束子组件

```dart
// ❌ 错误：非定位子组件没有约束
Stack(
  children: [
    Container(color: Colors.red),  // 没有尺寸约束，报错！
  ],
)

// ✅ 正确：给子组件约束
Stack(
  children: [
    SizedBox(
      width: 200,
      height: 200,
      child: Container(color: Colors.red),
    ),
  ],
)

// ✅ 或使用 Positioned
Stack(
  children: [
    Positioned.fill(
      child: Container(color: Colors.red),
    ),
  ],
)
```

### 陷阱2：Positioned 与约束冲突

```dart
// ❌ 错误：同时设置 width 和 left/right
Positioned(
  left: 0,
  right: 0,
  width: 100,  // 冲突！width 会覆盖 left/right
  child: Container(),
)

// ✅ 正确：只用 left/right 或只用 width
Positioned(
  left: 0,
  right: 0,  // 自动计算宽度
  child: Container(),
)
```

### 陷阱3：IndexedStack 不释放资源

```dart
// IndexedStack 的所有子组件都会被创建和保留
// 即使不显示也占用内存

// ❌ 如果子组件很多，可能内存问题
IndexedStack(
  index: 0,
  children: List.generate(100, (i) => HeavyWidget(i)),
)

// ✅ 使用条件渲染替代
if (currentIndex == 0) Page1()
else if (currentIndex == 1) Page2()
else Page3()
```

### 陷阱4：Stack 尺寸确定

```dart
// Stack 如何确定自身尺寸？
// 规则：取所有非定位子组件的最大尺寸

Stack(
  children: [
    Container(width: 200, height: 200, color: Colors.blue),  // 非定位
    Positioned(
      left: 0,
      top: 0,
      child: Container(width: 300, height: 300, color: Colors.red),  // 定位
    ),
  ],
)
// Stack 尺寸为 200x200，红色容器会溢出
```

### 陷阱5：overflow 可视化

```dart
// 开发时溢出会显示警告条纹
Stack(
  children: [
    Container(width: 100, height: 100, color: Colors.blue),
    Positioned(
      left: 80,
      child: Container(width: 50, height: 50, color: Colors.red),  // 溢出 30
    ),
  ],
)
// 会看到黄色黑条纹的溢出警告

// ✅ 解决方案：设置 clipBehavior
Stack(
  clipBehavior: Clip.hardEdge,
  children: [...],
)
```

---

## 7. 学习技巧

### 技巧1：理解 Stack 的布局规则

```
1. 确定自身尺寸
   - 有非定位子组件 → 取最大尺寸
   - 全是定位子组件 → 尝试填满父容器

2. 布局非定位子组件
   - 根据 alignment 对齐

3. 布局定位子组件
   - 根据 Positioned 属性定位
```

### 技巧2：常用模式

```dart
// 模式1：背景 + 内容
Stack(
  children: [
    BackgroundImage(),  // 底层背景
    Content(),          // 内容
  ],
)

// 模式2：悬浮按钮
Stack(
  children: [
    ListView(...),
    Positioned(
      right: 16,
      bottom: 16,
      child: FloatingActionButton(...),
    ),
  ],
)

// 模式3：图片遮罩
Stack(
  children: [
    Image.asset('photo.jpg'),
    Container(color: Colors.black45),  // 半透明遮罩
    Text('Title', style: TextStyle(color: Colors.white)),
  ],
)
```

### 技巧3：调试层级

```dart
// 使用 Flutter Inspector 查看层级
// 或用不同颜色区分
Stack(
  children: [
    Container(color: Colors.red.withValues(alpha: 0.5)),
    Container(color: Colors.green.withValues(alpha: 0.5)),
    Container(color: Colors.blue.withValues(alpha: 0.5)),
  ],
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UIView 层级                  → Stack + children
addSubview:                  → children 列表添加
bringSubviewToFront:         → 调整 children 顺序
clipsToBounds                → clipBehavior
Auto Layout 定位             → Positioned
centerX/Y                    → alignment: Alignment.center
fill                         → Positioned.fill
```

### 关键差异

| 方面 | UIKit | Flutter Stack |
|------|-------|---------------|
| **层级管理** | `addSubview:` / `removeFromSuperview` | `children` 列表 |
| **定位** | Auto Layout 约束 | `Positioned` 组件 |
| **尺寸确定** | frame 或约束 | 非定位子组件最大尺寸 |
| **裁剪** | `clipsToBounds` | `clipBehavior` |
| **隐藏保留** | `hidden = YES` | `IndexedStack` |

### 最佳实践

1. **明确尺寸约束** - 非定位子组件需要有明确尺寸
2. **使用 Positioned.fill** - 填充整个 Stack
3. **注意溢出** - 设置 `clipBehavior` 或确保内容不溢出
4. **IndexedStack 谨慎用** - 大量子组件时注意内存

---

*最后更新: 2026-02-24*

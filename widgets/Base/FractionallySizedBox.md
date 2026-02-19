# Flutter FractionallySizedBox 组件用法详解

## 简介

FractionallySizedBox 是 Flutter 中用于根据父容器尺寸的百分比来设置子组件尺寸的布局组件。它允许子组件的大小相对于父容器进行缩放。

## 1. 初级用法

### 1.1 基本概念

FractionallySizedBox 的核心作用：
- 子组件大小按父容器百分比计算
- widthFactor 和 heightFactor 定义比例
- 可配合 alignment 对齐

### 1.2 基本语法

```dart
FractionallySizedBox(
  widthFactor: 0.8,   // 父容器宽度的 80%
  heightFactor: 0.5,  // 父容器高度的 50%
  child: Container(color: Colors.blue),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `widthFactor` | `double?` | 宽度因子（0-1），相对于父容器 |
| `heightFactor` | `double?` | 高度因子（0-1），相对于父容器 |
| `alignment` | `AlignmentGeometry` | 对齐方式，默认 Alignment.center |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 基本用法

```dart
Container(
  width: 300,
  height: 200,
  color: Colors.grey[200],
  child: FractionallySizedBox(
    widthFactor: 0.5,
    heightFactor: 0.5,
    child: Container(color: Colors.blue),
  ),
)
// 结果：蓝色容器 150 x 100，居中显示
```

#### 全宽按钮

```dart
FractionallySizedBox(
  widthFactor: 1.0,  // 100% 宽度
  child: ElevatedButton(
    onPressed: () {},
    child: Text('全宽按钮'),
  ),
)
```

#### 左对齐

```dart
Container(
  height: 100,
  color: Colors.grey[200],
  child: FractionallySizedBox(
    widthFactor: 0.6,
    alignment: Alignment.centerLeft,
    child: Container(color: Colors.green),
  ),
)
```

## 2. 中级用法

### 2.1 只设置宽度或高度

```dart
// 只限制宽度
FractionallySizedBox(
  widthFactor: 0.8,
  child: Container(
    height: 50,
    color: Colors.red,
  ),
)

// 只限制高度
FractionallySizedBox(
  heightFactor: 0.3,
  child: Container(
    width: 100,
    color: Colors.green,
  ),
)
```

### 2.2 响应式布局

```dart
Container(
  constraints: BoxConstraints(maxWidth: 600),
  child: FractionallySizedBox(
    widthFactor: 0.9,
    child: Card(
      child: Padding(
        padding: EdgeInsets.all(16),
        child: Text('响应式卡片'),
      ),
    ),
  ),
)
```

### 2.3 多级嵌套

```dart
Container(
  width: 300,
  height: 200,
  color: Colors.grey[200],
  child: FractionallySizedBox(
    widthFactor: 0.8,
    heightFactor: 0.8,
    child: Container(
      color: Colors.blue[200],
      child: FractionallySizedBox(
        widthFactor: 0.5,
        heightFactor: 0.5,
        child: Container(color: Colors.blue),
      ),
    ),
  ),
)
```

### 2.4 配合 Align 使用

```dart
Container(
  width: 200,
  height: 200,
  color: Colors.grey[200],
  child: Align(
    alignment: Alignment.topRight,
    child: FractionallySizedBox(
      widthFactor: 0.5,
      heightFactor: 0.5,
      child: Container(color: Colors.orange),
    ),
  ),
)
```

### 2.5 与 LayoutBuilder 配合

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final isWide = constraints.maxWidth > 600;
    return FractionallySizedBox(
      widthFactor: isWide ? 0.6 : 0.9,
      child: Container(
        height: 100,
        color: Colors.blue,
      ),
    );
  },
)
```

## 3. 高级用法

### 3.1 动态尺寸动画

```dart
class AnimatedFractionallySizedBox extends StatefulWidget {
  @override
  State<AnimatedFractionallySizedBox> createState() => _AnimatedFractionallySizedBoxState();
}

class _AnimatedFractionallySizedBoxState extends State<AnimatedFractionallySizedBox>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 2),
      vsync: this,
    )..repeat(reverse: true);
    _animation = Tween<double>(begin: 0.3, end: 0.9).animate(_controller);
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      height: 200,
      color: Colors.grey[200],
      child: FractionallySizedBox(
        widthFactor: _animation.value,
        heightFactor: 0.5,
        child: Container(color: Colors.blue),
      ),
    );
  }
}
```

### 3.2 比例卡片网格

```dart
class ProportionalGrid extends StatelessWidget {
  final int columns;
  final List<Widget> children;

  const ProportionalGrid({
    required this.columns,
    required this.children,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: List.generate((children.length / columns).ceil(), (row) {
        return Row(
          children: List.generate(columns, (col) {
            final index = row * columns + col;
            if (index >= children.length) {
              return Expanded(child: SizedBox());
            }
            return Expanded(
              child: FractionallySizedBox(
                widthFactor: 0.9,
                child: children[index],
              ),
            );
          }),
        );
      }),
    );
  }
}
```

### 3.3 相对于屏幕尺寸

```dart
// 相对于屏幕宽度
MediaQuery.removePadding(
  context: context,
  removeTop: true,
  child: FractionallySizedBox(
    widthFactor: 0.9,
    child: Card(child: Text('屏幕宽度的 90%')),
  ),
)
```

### 3.4 背景比例区域

```dart
Stack(
  children: [
    // 背景
    Container(color: Colors.grey[200]),
    // 覆盖层（覆盖 50%）
    FractionallySizedBox(
      widthFactor: 1.0,
      heightFactor: 0.5,
      alignment: Alignment.topCenter,
      child: Container(color: Colors.blue.withValues(alpha: 0.3)),
    ),
    // 内容
    Center(child: Text('内容')),
  ],
)
```

## 4. 常见问题与解决方案

### 问题1：在无约束环境中无效

```dart
// ❌ 错误：在 ListView 中使用无约束
ListView(
  children: [
    FractionallySizedBox(
      widthFactor: 0.8,
      child: Container(height: 100, color: Colors.red),
    ),
  ],
)

// ✅ 正确：添加约束
ListView(
  children: [
    SizedBox(
      height: 100,
      child: FractionallySizedBox(
        widthFactor: 0.8,
        child: Container(color: Colors.red),
      ),
    ),
  ],
)
```

### 问题2：alignment 无效

```dart
// alignment 需要在有限空间内才有效
// 如果父容器正好等于子组件大小，alignment 无意义

Container(
  width: 200,
  height: 100,
  child: FractionallySizedBox(
    widthFactor: 0.5,  // 100 宽
    heightFactor: 0.5, // 50 高
    alignment: Alignment.bottomRight,  // 有效
    child: Container(color: Colors.red),
  ),
)
```

### 问题3：factor 大于 1

```dart
// factor 可以大于 1，子组件会超出父容器
FractionallySizedBox(
  widthFactor: 1.5,  // 超出父容器
  child: Container(color: Colors.red),
)

// 需要裁剪时使用 ClipRect
ClipRect(
  child: FractionallySizedBox(
    widthFactor: 1.5,
    child: Container(color: Colors.red),
  ),
)
```

## 5. FractionallySizedBox vs 其他组件

| 组件 | 用途 | 特点 |
|------|------|------|
| **FractionallySizedBox** | 百分比尺寸 | 基于父容器 |
| **SizedBox** | 固定尺寸 | 绝对值 |
| **ConstrainedBox** | 约束尺寸 | 最小/最大值 |
| **Container** | 多功能容器 | 可设置尺寸 |

### 选择建议

```dart
// 百分比尺寸 → FractionallySizedBox
FractionallySizedBox(widthFactor: 0.8, child: ...)

// 固定尺寸 → SizedBox
SizedBox(width: 100, height: 100, child: ...)

// 约束范围 → ConstrainedBox
ConstrainedBox(
  constraints: BoxConstraints(minWidth: 100, maxWidth: 200),
  child: ...,
)

// 复杂需求 → Container
Container(width: 100, height: 100, color: Colors.red)
```

## 6. 最佳实践

### 6.1 响应式设计

```dart
// 使用 FractionallySizedBox 实现响应式布局
class ResponsiveContainer extends StatelessWidget {
  final Widget child;
  final double widthFactor;
  final double heightFactor;

  const ResponsiveContainer({
    required this.child,
    this.widthFactor = 0.9,
    this.heightFactor,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return FractionallySizedBox(
      widthFactor: widthFactor,
      heightFactor: heightFactor,
      child: child,
    );
  }
}
```

### 6.2 统一比例

```dart
abstract class AppFactors {
  static const double fullWidth = 1.0;
  static const double cardWidth = 0.9;
  static const double dialogWidth = 0.85;
  static const double buttonWidth = 0.8;
}
```

### 6.3 配合 SafeArea

```dart
SafeArea(
  child: FractionallySizedBox(
    widthFactor: 0.9,
    child: Card(child: Text('安全区域内')),
  ),
)
```

## 总结

FractionallySizedBox 是实现响应式布局的便捷工具：

**核心要点**：
1. widthFactor/heightFactor 定义相对父容器的比例
2. alignment 控制子组件在剩余空间的对齐
3. 需要在有约束的环境中才能生效

**最佳实践**：
1. 响应式 UI 使用百分比尺寸
2. 固定尺寸使用 SizedBox
3. 注意无约束环境的问题

**常见场景**：
- 全宽按钮
- 响应式卡片
- 比例布局
- 适配不同屏幕

---

*最后更新: 2026-02-19*

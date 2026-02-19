# Flutter Flow 组件用法详解

## 简介

Flow 是 Flutter 中一个高性能的布局组件，它将子组件的位置和大小计算委托给 FlowDelegate，可以实现复杂的自定义布局效果。

## 1. 初级用法

### 1.1 基本概念

Flow 的核心特点：
- 使用 FlowDelegate 控制子组件的位置和大小
- 子组件不参与常规布局约束传递
- 高性能，适合复杂动画布局
- 需要手动管理子组件尺寸

### 1.2 基本语法

```dart
Flow(
  delegate: MyFlowDelegate(),
  children: [
    Container(width: 50, height: 50, color: Colors.red),
    Container(width: 50, height: 50, color: Colors.green),
    Container(width: 50, height: 50, color: Colors.blue),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `delegate` | `FlowDelegate` | 布局代理，控制子组件位置和大小 |
| `children` | `List<Widget>` | 子组件列表 |
| `clipBehavior` | `Clip` | 裁剪行为，默认 Clip.hardEdge |

### 1.4 FlowDelegate 核心方法

| 方法 | 说明 |
|------|------|
| `paintChildren()` | 绑制子组件位置和大小 |
| `getSize()` | 返回 Flow 自身大小 |
| `shouldRelayout()` | 是否需要重新布局 |
| `shouldRepaint()` | 是否需要重绘 |

### 1.5 基础示例

```dart
class BasicFlowDelegate extends FlowDelegate {
  @override
  void paintChildren(FlowPaintingContext context) {
    double x = 0;
    for (int i = 0; i < context.childCount; i++) {
      context.paintChild(i, transform: Matrix4.translationValues(x, 0, 0));
      x += context.getChildSize(i)!.width + 10;
    }
  }

  @override
  Size getSize(BoxConstraints constraints) {
    return constraints.biggest;
  }

  @override
  bool shouldRelayout(covariant FlowDelegate oldDelegate) => false;

  @override
  bool shouldRepaint(covariant FlowDelegate oldDelegate) => false;
}

Flow(
  delegate: BasicFlowDelegate(),
  children: [
    Container(width: 80, height: 80, color: Colors.red),
    Container(width: 80, height: 80, color: Colors.green),
    Container(width: 80, height: 80, color: Colors.blue),
  ],
)
```

## 2. 中级用法

### 2.1 圆形布局

```dart
class CircleFlowDelegate extends FlowDelegate {
  @override
  void paintChildren(FlowPaintingContext context) {
    final count = context.childCount;
    final centerX = context.size.width / 2;
    final centerY = context.size.height / 2;
    final radius = 80.0;

    for (int i = 0; i < count; i++) {
      final angle = 2 * pi * i / count;
      final x = centerX + radius * cos(angle) - context.getChildSize(i)!.width / 2;
      final y = centerY + radius * sin(angle) - context.getChildSize(i)!.height / 2;
      
      context.paintChild(
        i,
        transform: Matrix4.translationValues(x, y, 0),
      );
    }
  }

  @override
  Size getSize(BoxConstraints constraints) => constraints.biggest;

  @override
  bool shouldRelayout(covariant FlowDelegate oldDelegate) => false;

  @override
  bool shouldRepaint(covariant FlowDelegate oldDelegate) => false;
}
```

### 2.2 菜单展开效果

```dart
class MenuFlowDelegate extends FlowDelegate {
  final double animation;
  final double radius;

  MenuFlowDelegate({required this.animation, this.radius = 100});

  @override
  void paintChildren(FlowPaintingContext context) {
    final count = context.childCount - 1; // 排除主按钮
    final center = Offset(context.size.width / 2, context.size.height / 2);

    // 先绘制菜单项
    for (int i = 0; i < count; i++) {
      final angle = pi + (pi / (count + 1)) * (i + 1);
      final distance = radius * animation;
      final x = center.dx + distance * cos(angle);
      final y = center.dy + distance * sin(angle);

      context.paintChild(
        i,
        transform: Matrix4.translationValues(
          x - context.getChildSize(i)!.width / 2,
          y - context.getChildSize(i)!.height / 2,
          0,
        )..scale(animation),
        opacity: animation,
      );
    }

    // 绘制主按钮（最后一个）
    final mainButtonSize = context.getChildSize(count)!;
    context.paintChild(
      count,
      transform: Matrix4.translationValues(
        center.dx - mainButtonSize.width / 2,
        center.dy - mainButtonSize.height / 2,
        0,
      ),
    );
  }

  @override
  Size getSize(BoxConstraints constraints) => constraints.biggest;

  @override
  bool shouldRepaint(MenuFlowDelegate oldDelegate) => animation != oldDelegate.animation;
}
```

### 2.3 栈式卡片布局

```dart
class CardStackFlowDelegate extends FlowDelegate {
  final double cardOffset;

  CardStackFlowDelegate({this.cardOffset = 30});

  @override
  void paintChildren(FlowPaintingContext context) {
    for (int i = context.childCount - 1; i >= 0; i--) {
      context.paintChild(
        i,
        transform: Matrix4.translationValues(
          cardOffset * i,
          cardOffset * i * 0.5,
          0,
        ),
      );
    }
  }

  @override
  Size getSize(BoxConstraints constraints) => constraints.biggest;

  @override
  bool shouldRelayout(covariant FlowDelegate oldDelegate) => cardOffset != oldDelegate.cardOffset;

  @override
  bool shouldRepaint(covariant FlowDelegate oldDelegate) => false;
}
```

### 2.4 网格布局

```dart
class GridFlowDelegate extends FlowDelegate {
  final int columns;
  final double spacing;

  GridFlowDelegate({required this.columns, this.spacing = 8});

  @override
  void paintChildren(FlowPaintingContext context) {
    for (int i = 0; i < context.childCount; i++) {
      final row = i ~/ columns;
      final col = i % columns;
      final size = context.getChildSize(i)!;

      final x = col * (size.width + spacing);
      final y = row * (size.height + spacing);

      context.paintChild(
        i,
        transform: Matrix4.translationValues(x, y, 0),
      );
    }
  }

  @override
  Size getSize(BoxConstraints constraints) {
    if (constraints.childCount == 0) return Size.zero;
    final childSize = context.getChildSize(0)!;
    final rows = (constraints.childCount / columns).ceil();
    return Size(
      constraints.maxWidth,
      rows * (childSize.height + spacing) - spacing,
    );
  }

  @override
  bool shouldRelayout(covariant FlowDelegate oldDelegate) => true;
}
```

## 3. 高级用法

### 3.1 动画圆形菜单

```dart
class AnimatedMenu extends StatefulWidget {
  @override
  State<AnimatedMenu> createState() => _AnimatedMenuState();
}

class _AnimatedMenuState extends State<AnimatedMenu>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 500),
      vsync: this,
    );
    _animation = CurvedAnimation(parent: _controller, curve: Curves.easeOut);
  }

  void _toggle() {
    if (_controller.isCompleted) {
      _controller.reverse();
    } else {
      _controller.forward();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Flow(
      delegate: AnimatedMenuFlowDelegate(animation: _animation.value),
      children: [
        // 菜单项
        for (int i = 0; i < 5; i++)
          FloatingActionButton(
            heroTag: 'menu_$i',
            mini: true,
            onPressed: () {},
            child: Icon(Icons.star),
          ),
        // 主按钮
        FloatingActionButton(
          onPressed: _toggle,
          child: AnimatedIcon(
            icon: AnimatedIcons.menu_close,
            progress: _animation,
          ),
        ),
      ],
    );
  }
}

class AnimatedMenuFlowDelegate extends FlowDelegate {
  final double animation;

  AnimatedMenuFlowDelegate({required this.animation});

  @override
  void paintChildren(FlowPaintingContext context) {
    final count = context.childCount - 1;
    final center = Offset(context.size.width / 2, context.size.height / 2);
    final radius = 120.0;

    for (int i = 0; i < count; i++) {
      final angle = -pi / 2 + (pi / (count - 1)) * i;
      final x = center.dx + radius * cos(angle) * animation - 28;
      final y = center.dy + radius * sin(angle) * animation - 28;

      context.paintChild(
        i,
        transform: Matrix4.translationValues(x, y, 0),
        opacity: animation,
      );
    }

    context.paintChild(
      count,
      transform: Matrix4.translationValues(center.dx - 28, center.dy - 28, 0)
        ..rotateZ(animation * pi / 4),
    );
  }

  @override
  Size getSize(BoxConstraints constraints) => constraints.biggest;

  @override
  bool shouldRepaint(AnimatedMenuFlowDelegate oldDelegate) =>
      animation != oldDelegate.animation;
}
```

### 3.2 手风琴效果

```dart
class AccordionFlowDelegate extends FlowDelegate {
  final double expandedIndex;

  AccordionFlowDelegate({this.expandedIndex = -1});

  @override
  void paintChildren(FlowPaintingContext context) {
    double y = 0;
    for (int i = 0; i < context.childCount; i++) {
      final size = context.getChildSize(i)!;
      final isExpanded = i == expandedIndex;
      final height = isExpanded ? size.height : 60;

      context.paintChild(
        i,
        transform: Matrix4.translationValues(0, y, 0),
      );
      y += height + 2;
    }
  }

  @override
  Size getSize(BoxConstraints constraints) => constraints.biggest;

  @override
  bool shouldRepaint(AccordionFlowDelegate oldDelegate) =>
      expandedIndex != oldDelegate.expandedIndex;
}
```

### 3.3 3D 卡片轮播

```dart
class CarouselFlowDelegate extends FlowDelegate {
  final double currentPage;

  CarouselFlowDelegate({required this.currentPage});

  @override
  void paintChildren(FlowPaintingContext context) {
    for (int i = 0; i < context.childCount; i++) {
      final size = context.getChildSize(i)!;
      final offset = i - currentPage;
      final absOffset = offset.abs();

      if (absOffset > 2) continue; // 只显示可见卡片

      final perspective = 0.003;
      final scale = 1 - absOffset * 0.2;
      final x = context.size.width / 2 - size.width / 2 + offset * 150;
      final y = context.size.height / 2 - size.height / 2;

      final matrix = Matrix4.identity()
        ..setEntry(3, 2, perspective)
        ..translate(x, y)
        ..scale(scale)
        ..rotateY(offset * 0.5);

      context.paintChild(i, transform: matrix);
    }
  }

  @override
  Size getSize(BoxConstraints constraints) => constraints.biggest;

  @override
  bool shouldRepaint(CarouselFlowDelegate oldDelegate) =>
      currentPage != oldDelegate.currentPage;
}
```

## 4. 常见问题与解决方案

### 问题1：子组件大小为 null

```dart
// getChildSize 可能返回 null
// 确保子组件有明确大小或使用 SizedBox 包裹

Flow(
  delegate: MyFlowDelegate(),
  children: [
    SizedBox(width: 50, height: 50, child: Container(color: Colors.red)),
    SizedBox(width: 50, height: 50, child: Container(color: Colors.green)),
  ],
)
```

### 问题2：裁剪不正确

```dart
// 设置 clipBehavior 控制裁剪
Flow(
  clipBehavior: Clip.hardEdge,
  delegate: MyFlowDelegate(),
  children: [...],
)
```

### 问题3：布局约束问题

```dart
// Flow 的 getSize 决定自身大小
// 需要根据子组件计算合适的大小

@override
Size getSize(BoxConstraints constraints) {
  double maxWidth = 0;
  double maxHeight = 0;
  
  for (int i = 0; i < context.childCount; i++) {
    final size = context.getChildSize(i);
    if (size != null) {
      maxWidth = max(maxWidth, size.width);
      maxHeight += size.height;
    }
  }
  
  return Size(maxWidth, maxHeight);
}
```

## 5. Flow vs 其他布局组件

| 组件 | 用途 | 特点 |
|------|------|------|
| **Flow** | 自定义布局 | 高性能，完全控制 |
| **Stack** | 层叠布局 | 简单，基于约束 |
| **Wrap** | 流式布局 | 自动换行 |
| **CustomMultiChildLayout** | 自定义布局 | 更底层 |

### 选择建议

```dart
// 简单层叠 → Stack
Stack(children: [...])

// 自动换行 → Wrap
Wrap(children: [...])

// 复杂动画布局 → Flow
Flow(delegate: MyDelegate(), children: [...])

// 精确控制布局 → CustomMultiChildLayout
CustomMultiChildLayout(delegate: MyDelegate(), children: [...])
```

## 6. 最佳实践

### 6.1 性能优化

```dart
// 避免在 paintChildren 中做耗时计算
class OptimizedFlowDelegate extends FlowDelegate {
  List<Offset>? _positions;

  @override
  void paintChildren(FlowPaintingContext context) {
    _positions ??= _calculatePositions(context);
    
    for (int i = 0; i < context.childCount; i++) {
      context.paintChild(
        i,
        transform: Matrix4.translationValues(_positions![i].dx, _positions![i].dy, 0),
      );
    }
  }

  List<Offset> _calculatePositions(FlowPaintingContext context) {
    // 计算位置
  }
}
```

### 6.2 使用 RepaintBoundary

```dart
Flow(
  delegate: MyFlowDelegate(),
  children: [
    for (int i = 0; i < 10; i++)
      RepaintBoundary(
        child: MyWidget(),
      ),
  ],
)
```

### 6.3 传递参数

```dart
class ConfigurableFlowDelegate extends FlowDelegate {
  final double spacing;
  final double itemSize;
  final Axis direction;

  ConfigurableFlowDelegate({
    this.spacing = 8,
    this.itemSize = 50,
    this.direction = Axis.horizontal,
  });

  @override
  bool shouldRepaint(ConfigurableFlowDelegate oldDelegate) =>
      spacing != oldDelegate.spacing ||
      itemSize != oldDelegate.itemSize ||
      direction != oldDelegate.direction;
}
```

## 总结

Flow 是实现高性能自定义布局的核心组件：

**核心要点**：
1. FlowDelegate 控制子组件位置和大小
2. paintChildren 使用 Matrix4 变换
3. getSize 返回 Flow 自身大小

**最佳实践**：
1. 确保子组件有明确大小
2. 使用 RepaintBoundary 优化性能
3. 合理设置 shouldRepaint

**常见场景**：
- 圆形菜单
- 卡片轮播
- 手风琴效果
- 复杂动画布局

---

*最后更新: 2026-02-19*

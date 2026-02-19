# Flutter RenderObject 深入

## 概述

RenderObject 是 Flutter 渲染树的核心，负责布局计算和绘制。理解 RenderObject 有助于创建高性能自定义组件。

## Widget、Element、RenderObject 三层架构

```
┌─────────────────────────────────────┐
│            Widget (配置)             │
│         不可变，描述 UI 配置          │
└─────────────┬───────────────────────┘
              │ createElement()
              ▼
┌─────────────────────────────────────┐
│            Element (实例)            │
│       管理 Widget 生命周期           │
└─────────────┬───────────────────────┘
              │ createRenderObject()
              ▼
┌─────────────────────────────────────┐
│         RenderObject (渲染)          │
│       负责布局计算和绘制              │
└─────────────────────────────────────┘
```

## RenderObject 基础

### RenderObject 子类

```dart
// RenderObject 主要有两个分支

// 1. RenderBox - 盒子模型（最常用）
//    遵循 Box 约束协议，支持宽高布局
class RenderBox extends RenderObject {
  // 盒子约束
  BoxConstraints get constraints;
  Size get size;

  // 布局方法
  void performLayout();
  Size computeDryLayout(BoxConstraints constraints);

  // 命中测试
  bool hitTest(BoxHitTestResult result, {required Offset position});
}

// 2. RenderSliver - 滑动模型
//    用于 ScrollView 中的滚动布局
class RenderSliver extends RenderObject {
  SliverConstraints get constraints;
  SliverGeometry get geometry;

  void performLayout();
}
```

### 创建自定义 RenderBox

```dart
// 1. 创建 RenderObject
class RenderCustomBox extends RenderBox {
  double _padding = 0;

  double get padding => _padding;
  set padding(double value) {
    if (_padding != value) {
      _padding = value;
      markNeedsLayout(); // 标记需要重新布局
    }
  }

  @override
  void performLayout() {
    // 布局子组件
    if (child != null) {
      child!.layout(
        constraints.deflate(EdgeInsets.all(_padding)),
        parentUsesSize: true,
      );
      size = constraints.constrain(Size(
        child!.size.width + _padding * 2,
        child!.size.height + _padding * 2,
      ));
    } else {
      size = constraints.constrain(Size(_padding * 2, _padding * 2));
    }
  }

  @override
  void paint(PaintingContext context, Offset offset) {
    // 绘制背景
    final paint = Paint()..color = Colors.blue.withOpacity(0.3);
    context.canvas.drawRect(offset & size, paint);

    // 绘制子组件
    if (child != null) {
      context.paintChild(child!, offset + Offset(_padding, _padding));
    }
  }

  @override
  bool hitTestChildren(BoxHitTestResult result, {required Offset position}) {
    if (child != null) {
      return result.addWithPaintOffset(
        offset: Offset(_padding, _padding),
        position: position,
        hitTest: (BoxHitTestResult result, Offset transformed) {
          return child!.hitTest(result, position: transformed);
        },
      );
    }
    return false;
  }
}
```

### 创建对应 Widget

```dart
// 2. 创建 Widget
class CustomPadding extends SingleChildRenderObjectWidget {
  final double padding;

  const CustomPadding({
    super.key,
    required this.padding,
    required super.child,
  });

  @override
  RenderObject createRenderObject(BuildContext context) {
    return RenderCustomBox()..padding = padding;
  }

  @override
  void updateRenderObject(
    BuildContext context,
    RenderCustomBox renderObject,
  ) {
    renderObject.padding = padding;
  }
}
```

## 布局协议

### BoxConstraints

```dart
// 盒子约束 - 父组件传递给子组件的布局限制
class BoxConstraints {
  final double minWidth;
  final double maxWidth;
  final double minHeight;
  final double maxHeight;

  // 常用工厂构造
  BoxConstraints.tight(Size size);           // 固定尺寸
  BoxConstraints.tightFor({double? width, double? height});
  BoxConstraints.loose(Size size);           // 最大尺寸限制
  BoxConstraints.expand({double? width, double? height});
  BoxConstraints.tightForFinite({double? width, double? height});

  // 常用方法
  BoxConstraints deflate(EdgeInsets padding); // 减去内边距
  Size constrain(Size size);                  // 约束尺寸
  double constrainWidth([double width = double.infinity]);
  double constrainHeight([double height = double.infinity]);
}

// 示例：根据约束计算尺寸
@override
void performLayout() {
  // 紧约束（如 Expanded）
  if (constraints.hasTightWidth) {
    size = Size(constraints.maxWidth, 100);
  }
  // 松约束（如未约束的 Column）
  else {
    size = Size(200.clamp(constraints.minWidth, constraints.maxWidth), 100);
  }
}
```

### 布局流程

```dart
// 父组件布局子组件
@override
void performLayout() {
  double yOffset = 0;

  // 遍历子组件
  RenderBox? child = firstChild;
  while (child != null) {
    // 1. 给子组件布局约束
    child.layout(
      BoxConstraints(maxWidth: constraints.maxWidth),
      parentUsesSize: true, // 父组件需要知道子组件尺寸
    );

    // 2. 设置子组件位置
    final childParentData = child.parentData as BoxParentData;
    childParentData.offset = Offset(0, yOffset);

    // 3. 累加高度
    yOffset += child.size.height;

    child = childAfter(child);
  }

  // 4. 设置自身尺寸
  size = Size(constraints.maxWidth, yOffset);
}
```

## 绘制

### Paint 方法

```dart
@override
void paint(PaintingContext context, Offset offset) {
  final canvas = context.canvas;

  // 绘制背景
  final bgPaint = Paint()
    ..color = Colors.white
    ..style = PaintingStyle.fill;
  canvas.drawRect(offset & size, bgPaint);

  // 绘制边框
  final borderPaint = Paint()
    ..color = Colors.blue
    ..style = PaintingStyle.stroke
    ..strokeWidth = 2;
  canvas.drawRect(offset & size, borderPaint);

  // 绘制子组件
  if (child != null) {
    final childParentData = child!.parentData as BoxParentData;
    context.paintChild(child!, offset + childParentData.offset);
  }
}
```

### Layer 和 RepaintBoundary

```dart
// 使用 RepaintBoundary 隔离重绘
Widget build(BuildContext context) {
  return Column(
    children: [
      // 频繁变化的部分
      RepaintBoundary(
        child: AnimatedWidget(),
      ),
      // 不变的部分
      StaticWidget(),
    ],
  );
}

// 在 RenderObject 中
@override
void paint(PaintingContext context, Offset offset) {
  // 创建新图层
  context.pushLayer(
    OffsetLayer(),
    ( PaintingContext context, Offset offset) {
      // 在新图层上绘制
      context.canvas.drawCircle(Offset.zero, 50, Paint());
    },
    offset,
  );
}
```

## 命中测试

### hitTest 方法

```dart
@override
bool hitTest(BoxHitTestResult result, {required Offset position}) {
  // 1. 检查是否在自身范围内
  if (!size.contains(position)) {
    return false;
  }

  // 2. 检查子组件
  if (hitTestChildren(result, position: position)) {
    return true;
  }

  // 3. 检查自身
  if (hitTestSelf(position)) {
    result.add(BoxHitTestEntry(this, position));
    return true;
  }

  return false;
}

@override
bool hitTestSelf(Offset position) {
  // 自定义命中区域
  return true; // 或自定义逻辑
}

@override
bool hitTestChildren(BoxHitTestResult result, {required Offset position}) {
  // 从后往前测试（后绘制的在上面）
  RenderBox? child = lastChild;
  while (child != null) {
    final childParentData = child.parentData as BoxParentData;
    final transformedPosition = position - childParentData.offset;

    if (child.hitTest(result, position: transformedPosition)) {
      return true;
    }

    child = childBefore(child);
  }
  return false;
}
```

## 完整示例：自定义布局

### 自定义流式布局

```dart
class RenderWrapLayout extends RenderBox
    with ContainerRenderObjectMixin<RenderBox, BoxParentData>,
        RenderBoxContainerDefaultsMixin<RenderBox, BoxParentData> {
  double _spacing = 8;
  double _runSpacing = 8;

  double get spacing => _spacing;
  set spacing(double value) {
    if (_spacing != value) {
      _spacing = value;
      markNeedsLayout();
    }
  }

  double get runSpacing => _runSpacing;
  set runSpacing(double value) {
    if (_runSpacing != value) {
      _runSpacing = value;
      markNeedsLayout();
    }
  }

  @override
  void performLayout() {
    final double maxWidth = constraints.maxWidth;

    double x = 0;
    double y = 0;
    double rowHeight = 0;
    double totalHeight = 0;

    RenderBox? child = firstChild;

    while (child != null) {
      // 布局子组件
      child.layout(BoxConstraints.loose(Size(maxWidth, constraints.maxHeight)), parentUsesSize: true);

      // 检查是否需要换行
      if (x + child.size.width > maxWidth && x > 0) {
        x = 0;
        y += rowHeight + _runSpacing;
        rowHeight = 0;
      }

      // 设置位置
      final childParentData = child.parentData as BoxParentData;
      childParentData.offset = Offset(x, y);

      // 更新位置
      x += child.size.width + _spacing;
      rowHeight = math.max(rowHeight, child.size.height);
      totalHeight = y + rowHeight;

      child = childAfter(child);
    }

    size = Size(constraints.maxWidth, totalHeight);
  }

  @override
  void paint(PaintingContext context, Offset offset) {
    defaultPaint(context, offset);
  }

  @override
  bool hitTestChildren(BoxHitTestResult result, {required Offset position}) {
    return defaultHitTestChildren(result, position: position);
  }
}

// 对应 Widget
class WrapLayout extends MultiChildRenderObjectWidget {
  final double spacing;
  final double runSpacing;

  const WrapLayout({
    super.key,
    this.spacing = 8,
    this.runSpacing = 8,
    required super.children,
  });

  @override
  RenderObject createRenderObject(BuildContext context) {
    return RenderWrapLayout()
      ..spacing = spacing
      ..runSpacing = runSpacing;
  }

  @override
  void updateRenderObject(BuildContext context, RenderWrapLayout renderObject) {
    renderObject
      ..spacing = spacing
      ..runSpacing = runSpacing;
  }
}
```

## 语义化

### 添加语义信息

```dart
@override
void describeSemanticsConfiguration(SemanticsConfiguration config) {
  super.describeSemanticsConfiguration(config);

  config
    ..label = '自定义组件'
    ..hint = '点击执行操作'
    ..isEnabled = true
    ..isButton = true;

  // 添加自定义语义动作
  config.onTap = () {
    // 处理点击
  };
}

// 语义化容器
@override
void assembleSemanticsNode(
  SemanticsNode node,
  SemanticsConfiguration config,
  Iterable<SemanticsNode> children,
) {
  super.assembleSemanticsNode(node, config, children);
  // 组装语义节点
}
```

## 性能优化

### 避免不必要的重绘

```dart
class RenderOptimizedBox extends RenderBox {
  Color _color = Colors.blue;

  Color get color => _color;
  set color(Color value) {
    if (_color != value) {
      _color = value;
      // 只需要重绘，不需要重新布局
      markNeedsPaint(); // 而非 markNeedsLayout()
    }
  }
}
```

### 使用 isRepaintBoundary

```dart
class RenderCustomWidget extends RenderBox {
  @override
  bool get isRepaintBoundary => true; // 创建独立渲染层

  // 适用于频繁重绘但布局不变的组件
}
```

## 调试

### debugPrint

```dart
@override
void performLayout() {
  debugPrint('布局约束: $constraints');
  debugPrint('父组件: $parent');
  super.performLayout();
  debugPrint('最终尺寸: $size');
}

@override
void debugFillProperties(DiagnosticPropertiesBuilder properties) {
  super.debugFillProperties(properties);
  properties.add(DoubleProperty('padding', _padding));
  properties.add(ColorProperty('color', _color));
}
```

### debugPaint

```dart
// 启用调试绘制
debugPaintSizeEnabled = true;
debugPaintBaselinesEnabled = true;

@override
void debugPaintSize(PaintingContext context, Offset offset) {
  super.debugPaintSize(context, offset);
  // 自定义调试绘制
}
```

## 何时自定义 RenderObject

| 场景 | 推荐方案 |
|------|----------|
| 简单布局组合 | 使用现有 Widget 组合 |
| 自定义布局算法 | 自定义 RenderObject |
| 高性能绘制 | 自定义 RenderObject + Canvas |
| 简单自定义绘制 | CustomPaint |
| 复杂交互组件 | 自定义 RenderObject |

---

*最后更新: 2026-02-19*

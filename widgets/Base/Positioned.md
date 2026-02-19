# Flutter Positioned 组件用法详解

## 简介

Positioned 是 Flutter 中用于在 Stack 内精确定位子组件的布局组件。通过设置上下左右的偏移值，可以将子组件放置在 Stack 的任意位置。

## 1. 初级用法

### 1.1 基本概念

Positioned 的作用：
- 在 Stack 中精确控制子组件的位置
- 可设置上下左右四个方向的偏移
- 可设置宽度和高度

### 1.2 基本语法

```dart
Stack(
  children: [
    Container(color: Colors.grey[200]),
    Positioned(
      left: 10,
      top: 20,
      child: Container(
        width: 50,
        height: 50,
        color: Colors.blue,
      ),
    ),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `left` | `double?` | 距离左边的距离 |
| `top` | `double?` | 距离上边的距离 |
| `right` | `double?` | 距离右边的距离 |
| `bottom` | `double?` | 距离底边的距离 |
| `width` | `double?` | 宽度 |
| `height` | `double?` | 高度 |
| `child` | `Widget` | 子组件 |

### 1.4 基础示例

#### 角落定位

```dart
Stack(
  children: [
    Container(width: 200, height: 200, color: Colors.grey[200]),
    // 左上角
    Positioned(
      left: 10,
      top: 10,
      child: Container(width: 40, height: 40, color: Colors.red),
    ),
    // 右下角
    Positioned(
      right: 10,
      bottom: 10,
      child: Container(width: 40, height: 40, color: Colors.blue),
    ),
  ],
)
```

#### 水平拉伸

```dart
Positioned(
  left: 0,
  right: 0,  // 同时设置 left 和 right，宽度自动填充
  top: 50,
  child: Container(
    height: 40,
    color: Colors.green,
  ),
)
```

## 2. 中级用法

### 2.1 尺寸控制

#### 同时设置 left 和 right

```dart
Positioned(
  left: 20,
  right: 20,  // 宽度 = Stack宽度 - 40
  top: 20,
  child: Container(
    height: 50,
    color: Colors.blue,
  ),
)
```

#### 同时设置 top 和 bottom

```dart
Positioned(
  top: 20,
  bottom: 20,  // 高度 = Stack高度 - 40
  left: 20,
  child: Container(
    width: 50,
    color: Colors.green,
  ),
)
```

#### 显式设置宽高

```dart
Positioned(
  left: 50,
  top: 50,
  width: 100,  // 显式宽度
  height: 80,  // 显式高度
  child: Container(color: Colors.orange),
)
```

### 2.2 常见布局场景

#### 角标

```dart
Stack(
  children: [
    Container(
      width: 80,
      height: 80,
      decoration: BoxDecoration(
        color: Colors.blue,
        borderRadius: BorderRadius.circular(8),
      ),
    ),
    Positioned(
      top: -5,
      right: -5,
      child: Container(
        padding: EdgeInsets.all(6),
        decoration: BoxDecoration(
          color: Colors.red,
          shape: BoxShape.circle,
        ),
        child: Text(
          '3',
          style: TextStyle(color: Colors.white, fontSize: 12),
        ),
      ),
    ),
  ],
)
```

#### 进度条覆盖层

```dart
Stack(
  children: [
    Container(
      height: 10,
      decoration: BoxDecoration(
        color: Colors.grey[300],
        borderRadius: BorderRadius.circular(5),
      ),
    ),
    Positioned(
      left: 0,
      top: 0,
      bottom: 0,
      width: 150,  // 进度 75%
      child: Container(
        decoration: BoxDecoration(
          color: Colors.blue,
          borderRadius: BorderRadius.circular(5),
        ),
      ),
    ),
  ],
)
```

#### 图片水印

```dart
Stack(
  children: [
    Image.asset('assets/photo.jpg'),
    Positioned(
      right: 10,
      bottom: 10,
      child: Text(
        '© 2024',
        style: TextStyle(
          color: Colors.white,
          fontSize: 12,
          shadows: [Shadow(blurRadius: 2, color: Colors.black)],
        ),
      ),
    ),
  ],
)
```

#### 浮动按钮

```dart
Stack(
  children: [
    ListView(
      children: [...],
    ),
    Positioned(
      right: 16,
      bottom: 16,
      child: FloatingActionButton(
        onPressed: () {},
        child: Icon(Icons.add),
      ),
    ),
  ],
)
```

### 2.3 相对定位

```dart
LayoutBuilder(
  builder: (context, constraints) {
    return Stack(
      children: [
        Container(color: Colors.grey[200]),
        Positioned(
          left: constraints.maxWidth * 0.3,  // 宽度的 30%
          top: constraints.maxHeight * 0.2,  // 高度的 20%
          child: Container(
            width: 50,
            height: 50,
            color: Colors.blue,
          ),
        ),
      ],
    );
  },
)
```

## 3. 高级用法

### 3.1 AnimatedPositioned

使用 `AnimatedPositioned` 实现位置动画：

```dart
class AnimatedPositionedExample extends StatefulWidget {
  @override
  State<AnimatedPositionedExample> createState() => _AnimatedPositionedExampleState();
}

class _AnimatedPositionedExampleState extends State<AnimatedPositionedExample> {
  bool _moved = false;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Container(color: Colors.grey[200]),
        AnimatedPositioned(
          left: _moved ? 150 : 20,
          top: _moved ? 150 : 20,
          duration: Duration(milliseconds: 500),
          curve: Curves.easeInOut,
          child: GestureDetector(
            onTap: () => setState(() => _moved = !_moved),
            child: Container(
              width: 80,
              height: 80,
              color: Colors.blue,
              child: Center(child: Text('点击移动')),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.2 Positioned.fill

填充整个 Stack：

```dart
Stack(
  children: [
    Container(color: Colors.blue),
    Positioned.fill(
      child: Container(
        margin: EdgeInsets.all(20),
        color: Colors.red,
      ),
    ),
  ],
)
```

等价于：
```dart
Positioned(
  left: 0,
  top: 0,
  right: 0,
  bottom: 0,
  child: Container(...),
)
```

### 3.3 Positioned.fromRect

从 Rect 对象创建：

```dart
Positioned.fromRect(
  rect: Rect.fromLTWH(20, 30, 100, 80),
  child: Container(color: Colors.green),
)
```

### 3.4 Positioned.fromRelativeRect

从相对位置创建：

```dart
Positioned.fromRelativeRect(
  rect: RelativeRect.fromLTRB(20, 30, 20, 30),
  child: Container(color: Colors.orange),
)
```

### 3.5 拖拽定位

```dart
class DraggablePositioned extends StatefulWidget {
  @override
  State<DraggablePositioned> createState() => _DraggablePositionedState();
}

class _DraggablePositionedState extends State<DraggablePositioned> {
  double _left = 50;
  double _top = 50;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Container(color: Colors.grey[200]),
        Positioned(
          left: _left,
          top: _top,
          child: GestureDetector(
            onPanUpdate: (details) {
              setState(() {
                _left += details.delta.dx;
                _top += details.delta.dy;
              });
            },
            child: Container(
              width: 80,
              height: 80,
              color: Colors.blue,
              child: Center(child: Text('拖拽我')),
            ),
          ),
        ),
      ],
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：Positioned 不在 Stack 中

**原因**：Positioned 必须作为 Stack 的直接子组件。

```dart
// 错误：Positioned 不在 Stack 中
Column(
  children: [
    Positioned(left: 10, child: Text('错误')),
  ],
)

// 解决：使用 Stack
Stack(
  children: [
    Positioned(left: 10, child: Text('正确')),
  ],
)
```

### 问题2：部分约束导致布局问题

**原因**：只设置部分属性可能导致约束冲突。

```dart
// 问题：只设置 left 和 right 但没有设置高度
Positioned(
  left: 0,
  right: 0,
  top: 0,
  child: Container(color: Colors.red),  // 高度为 0
)

// 解决：设置 height 或 bottom
Positioned(
  left: 0,
  right: 0,
  top: 0,
  height: 100,  // 显式高度
  child: Container(color: Colors.red),
)
```

### 问题3：子组件超出 Stack 边界

```dart
// 问题：子组件可能超出 Stack
Stack(
  children: [
    Container(width: 100, height: 100, color: Colors.grey),
    Positioned(
      left: 80,
      top: 80,
      child: Container(width: 50, height: 50, color: Colors.blue),  // 超出
    ),
  ],
)

// 解决：使用 clipBehavior
Stack(
  clipBehavior: Clip.hardEdge,  // 裁剪超出部分
  children: [...],
)
```

## 5. Positioned vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **Positioned** | Stack 内定位 | 精确控制位置和尺寸 |
| **Align** | 对齐子组件 | 基于对齐方式定位 |
| **Container** | 综合容器 | 可设置边距、对齐等 |
| **Padding** | 内边距 | 基于边距定位 |

### 选择建议

```dart
// 需要精确位置 → 使用 Positioned
Stack(
  children: [
    Positioned(left: 10, top: 20, child: Text('精确位置')),
  ],
)

// 需要对齐 → 使用 Align
Stack(
  children: [
    Align(alignment: Alignment.center, child: Text('居中')),
  ],
)

// 需要填充 → 使用 Positioned.fill
Stack(
  children: [
    Positioned.fill(child: Container(color: Colors.blue)),
  ],
)
```

## 6. 最佳实践

### 6.1 封装常用定位

```dart
class Badge extends StatelessWidget {
  final Widget child;
  final String text;
  final Color color;

  const Badge({
    required this.child,
    required this.text,
    this.color = Colors.red,
  });

  @override
  Widget build(BuildContext context) {
    return Stack(
      clipBehavior: Clip.none,
      children: [
        child,
        Positioned(
          top: -8,
          right: -8,
          child: Container(
            padding: EdgeInsets.all(4),
            decoration: BoxDecoration(
              color: color,
              shape: BoxShape.circle,
            ),
            child: Text(
              text,
              style: TextStyle(color: Colors.white, fontSize: 10),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 6.2 响应式定位

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final isWide = constraints.maxWidth > 400;
    return Stack(
      children: [
        Positioned(
          left: isWide ? 40 : 16,
          right: isWide ? 40 : 16,
          top: 20,
          child: Container(color: Colors.blue),
        ),
      ],
    );
  },
)
```

### 6.3 性能优化

```dart
// 避免不必要的重建
class _StaticPositioned extends StatelessWidget {
  static const _position = Positioned(
    left: 10,
    top: 10,
    child: Text('静态'),
  );

  @override
  Widget build(BuildContext context) {
    return Stack(children: [_position]);
  }
}
```

## 总结

Positioned 是 Stack 布局中精确定位的核心组件：

**核心要点**：
1. 必须作为 Stack 的直接子组件
2. left/right/top/bottom 控制位置和尺寸
3. 支持动画 AnimatedPositioned

**最佳实践**：
1. 合理设置约束避免布局问题
2. 使用 clipBehavior 控制超出边界
3. 封装常用定位组件

**常见场景**：
- 角标/徽章
- 浮动按钮
- 进度条
- 图片水印
- 拖拽组件

---

*最后更新: 2026-02-19*

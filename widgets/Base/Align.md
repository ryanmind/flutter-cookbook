# Flutter Align 组件用法详解

## 简介

Align 是 Flutter 中用于对齐子组件的基础布局组件。它可以将子组件放置在父容器的任意位置，通过坐标系统精确控制对齐方式。

## 1. 初级用法

### 1.1 基本概念

Align 组件的核心作用：
- 将子组件对齐到父容器的指定位置
- 通过 `alignment` 属性控制对齐方式
- 可选设置 `widthFactor` 和 `heightFactor` 来约束自身尺寸

### 1.2 基本语法

```dart
Align(
  alignment: Alignment.center,
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `alignment` | `AlignmentGeometry` | 对齐方式，默认 `Alignment.center` |
| `widthFactor` | `double?` | 宽度因子，相对于子组件宽度 |
| `heightFactor` | `double?` | 高度因子，相对于子组件高度 |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 居中对齐

```dart
Container(
  width: 200,
  height: 200,
  color: Colors.grey[200],
  child: Align(
    alignment: Alignment.center,
    child: Container(
      width: 80,
      height: 80,
      color: Colors.blue,
    ),
  ),
)
```

#### 角落对齐

```dart
// 右下角
Align(
  alignment: Alignment.bottomRight,
  child: Container(width: 50, height: 50, color: Colors.red),
)

// 左上角
Align(
  alignment: Alignment.topLeft,
  child: Container(width: 50, height: 50, color: Colors.green),
)
```

## 2. 中级用法

### 2.1 Alignment 对齐常量

Flutter 提供了丰富的预定义对齐常量：

```dart
// 角落对齐
Alignment.topLeft        // 左上角
Alignment.topCenter      // 顶部居中
Alignment.topRight       // 右上角
Alignment.centerLeft     // 左侧居中
Alignment.center         // 正中心
Alignment.centerRight    // 右侧居中
Alignment.bottomLeft     // 左下角
Alignment.bottomCenter   // 底部居中
Alignment.bottomRight    // 右下角
```

### 2.2 自定义对齐位置

Alignment 使用坐标系统：
- 中心点为 `(0, 0)`
- 左上角为 `(-1, -1)`
- 右下角为 `(1, 1)`

```dart
// 自定义位置（右偏 0.5，下偏 0.3）
Align(
  alignment: Alignment(0.5, 0.3),
  child: Container(
    width: 60,
    height: 60,
    color: Colors.purple,
  ),
)

// 完全自定义（超出边界也可）
Align(
  alignment: Alignment(1.5, -0.5),  // 超出右边界
  child: Container(
    width: 50,
    height: 50,
    color: Colors.orange,
  ),
)
```

### 2.3 宽高因子 (widthFactor / heightFactor)

当不设置 `widthFactor` 和 `heightFactor` 时，Align 会尽可能填满父容器。设置因子后，Align 的尺寸会根据子组件尺寸计算：

```dart
// widthFactor: 2 表示 Align 宽度是子组件的 2 倍
Align(
  widthFactor: 2,
  heightFactor: 2,
  child: Container(
    width: 80,
    height: 80,
    color: Colors.blue,
  ),
)
// 结果：Align 尺寸为 160 x 160
```

**实际应用**：让按钮有更大的点击区域

```dart
Align(
  widthFactor: 2,
  heightFactor: 2,
  child: IconButton(
    icon: Icon(Icons.add),
    onPressed: () {},
  ),
)
```

### 2.4 AlignmentDirectional（支持 RTL）

对于需要支持从右到左（RTL）语言的场景，使用 `AlignmentDirectional`：

```dart
// start 会根据语言方向自动调整
Align(
  alignment: AlignmentDirectional.centerStart,  // LTR 时靠左，RTL 时靠右
  child: Text('自动适配方向'),
)
```

常用常量：
```dart
AlignmentDirectional.topStart
AlignmentDirectional.topEnd
AlignmentDirectional.centerStart
AlignmentDirectional.centerEnd
AlignmentDirectional.bottomStart
AlignmentDirectional.bottomEnd
```

### 2.5 常见布局场景

#### 浮动按钮

```dart
Scaffold(
  body: Stack(
    children: [
      // 主内容
      ListView(children: [...]),
      // 浮动按钮
      Align(
        alignment: Alignment.bottomRight,
        child: Padding(
          padding: EdgeInsets.all(16),
          child: FloatingActionButton(
            onPressed: () {},
            child: Icon(Icons.add),
          ),
        ),
      ),
    ],
  ),
)
```

#### 卡片角标

```dart
SizedBox(
  width: 200,
  height: 120,
  child: Stack(
    children: [
      Container(
        decoration: BoxDecoration(
          color: Colors.blue,
          borderRadius: BorderRadius.circular(8),
        ),
      ),
      Align(
        alignment: Alignment.topRight,
        child: Container(
          padding: EdgeInsets.symmetric(horizontal: 8, vertical: 4),
          decoration: BoxDecoration(
            color: Colors.red,
            borderRadius: BorderRadius.only(
              topRight: Radius.circular(8),
              bottomLeft: Radius.circular(8),
            ),
          ),
          child: Text(
            'NEW',
            style: TextStyle(color: Colors.white, fontSize: 12),
          ),
        ),
      ),
    ],
  ),
)
```

## 3. 高级用法

### 3.1 动画对齐

使用 `AnimatedAlign` 实现对齐动画：

```dart
class AnimatedAlignExample extends StatefulWidget {
  @override
  State<AnimatedAlignExample> createState() => _AnimatedAlignExampleState();
}

class _AnimatedAlignExampleState extends State<AnimatedAlignExample> {
  bool _moved = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Expanded(
            child: Container(
              color: Colors.grey[200],
              child: AnimatedAlign(
                alignment: _moved 
                    ? Alignment.bottomRight 
                    : Alignment.topLeft,
                duration: Duration(milliseconds: 500),
                curve: Curves.easeInOut,
                child: Container(
                  width: 80,
                  height: 80,
                  color: Colors.blue,
                ),
              ),
            ),
          ),
          ElevatedButton(
            onPressed: () => setState(() => _moved = !_moved),
            child: Text('移动'),
          ),
        ],
      ),
    );
  }
}
```

### 3.2 响应式对齐

根据屏幕尺寸动态调整对齐：

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final isWide = constraints.maxWidth > 600;
    return Align(
      alignment: isWide 
          ? Alignment.centerRight 
          : Alignment.center,
      child: Container(
        width: 300,
        height: 200,
        color: Colors.blue,
      ),
    );
  },
)
```

### 3.3 多层嵌套对齐

```dart
Container(
  width: 300,
  height: 200,
  color: Colors.grey[200],
  child: Align(
    alignment: Alignment.center,
    child: Container(
      width: 200,
      height: 140,
      color: Colors.blue[200],
      child: Align(
        alignment: Alignment.bottomRight,
        child: Container(
          width: 80,
          height: 50,
          color: Colors.blue,
        ),
      ),
    ),
  ),
)
```

### 3.4 配合 FractionallySizedBox

```dart
Align(
  alignment: Alignment.center,
  child: FractionallySizedBox(
    widthFactor: 0.8,  // 父容器宽度的 80%
    heightFactor: 0.5,
    child: Container(color: Colors.green),
  ),
)
```

## 4. 常见问题与解决方案

### 问题1：Align 没有效果

**原因**：Align 自身没有约束，会填满父容器。如果父容器没有约束，Align 无法确定大小。

```dart
// 错误：在无约束环境中使用 Align
Column(
  children: [
    Align(
      alignment: Alignment.center,
      child: Text('可能看不到效果'),
    ),
  ],
)

// 正确：使用 widthFactor/heightFactor 或在外层约束
Column(
  children: [
    Align(
      widthFactor: 2,
      heightFactor: 2,
      child: Text('现在有效果了'),
    ),
  ],
)
```

### 问题2：子组件超出边界

**原因**：对齐位置超出 (-1, 1) 范围时，子组件会超出 Align 边界。

```dart
// 超出边界
Container(
  width: 200,
  height: 200,
  color: Colors.grey[200],
  child: Align(
    alignment: Alignment(1.5, 0),  // 超出右边界
    child: Container(
      width: 50,
      height: 50,
      color: Colors.red,
    ),
  ),
)

// 解决方案：使用 ClipRect 裁剪
ClipRect(
  child: Align(
    alignment: Alignment(1.5, 0),
    child: Container(
      width: 50,
      height: 50,
      color: Colors.red,
    ),
  ),
)
```

### 问题3：与 Center 的区别

Center 是 Align 的特例：

```dart
// 这两个是等价的
Center(child: Text('居中'))
Align(alignment: Alignment.center, child: Text('居中'))
```

Center 的特点：
- 代码更简洁
- 不能自定义对齐位置
- 同样支持 `widthFactor` 和 `heightFactor`

## 5. Align vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **Align** | 精确对齐 | 可自定义任意位置 |
| **Center** | 居中对齐 | Align 的简化版 |
| **Container** | 综合容器 | 可同时设置对齐、边距、装饰 |
| **FittedBox** | 缩放适配 | 会缩放子组件 |

### 选择建议

```dart
// 简单居中 → 使用 Center
Center(child: Text('居中'))

// 需要精确位置 → 使用 Align
Align(
  alignment: Alignment(0.5, -0.3),
  child: Text('精确位置'),
)

// 需要对齐+装饰+边距 → 使用 Container
Container(
  alignment: Alignment.center,
  padding: EdgeInsets.all(16),
  decoration: BoxDecoration(color: Colors.blue),
  child: Text('复杂需求'),
)
```

## 6. 最佳实践

### 6.1 统一管理对齐常量

```dart
abstract class AppAlignments {
  static const cardIcon = Alignment.topLeft;
  static const badge = Alignment.topRight;
  static const fab = Alignment.bottomRight;
  static const dialogButton = Alignment.bottomCenter;
}
```

### 6.2 避免过度嵌套

```dart
// 不推荐
Align(
  alignment: Alignment.center,
  child: Align(
    alignment: Alignment.center,
    child: Text('重复对齐'),
  ),
)

// 推荐
Align(
  alignment: Alignment.center,
  child: Text('简洁'),
)
```

### 6.3 性能优化

```dart
// 使用 const 构造函数
const Align(
  alignment: Alignment.center,
  child: Text('静态内容'),
)
```

## 总结

Align 是 Flutter 中精确控制子组件位置的基础工具：

**核心要点**：
1. 坐标系统：中心 (0,0)，左上 (-1,-1)，右下 (1,1)
2. `widthFactor` / `heightFactor` 控制自身尺寸
3. 支持 RTL 语言的 `AlignmentDirectional`

**最佳实践**：
1. 简单居中用 Center，精确位置用 Align
2. 合理使用宽高因子避免布局问题
3. 注意子组件可能超出边界的情况

**常见场景**：
- 浮动按钮定位
- 卡片角标
- 引导箭头
- 动画过渡效果

---

*最后更新: 2026-02-19*

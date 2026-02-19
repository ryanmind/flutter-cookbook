# Flutter Spacer 组件用法详解

## 简介

Spacer 是 Flutter 中用于在 Flex 容器（Row、Column、Flex）中创建弹性空白空间的组件。它会自动占据剩余空间，是实现灵活布局的重要工具。

## 1. 初级用法

### 1.1 基本概念

Spacer 的作用：
- 在 Row/Column/Flex 中占据剩余空间
- 通过 flex 参数控制占用比例
- 简化布局代码，避免复杂的 Expanded 使用

### 1.2 基本语法

```dart
Row(
  children: [
    Text('左边'),
    Spacer(),  // 占据剩余空间
    Text('右边'),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `flex` | `int` | 弹性系数，默认为 1 |

### 1.4 基础示例

#### 两端对齐

```dart
Row(
  children: [
    Text('左'),
    Spacer(),
    Text('右'),
  ],
)
```

#### 多个 Spacer

```dart
Row(
  children: [
    Text('1'),
    Spacer(),
    Text('2'),
    Spacer(),
    Text('3'),
  ],
)
```

## 2. 中级用法

### 2.1 flex 参数

通过 flex 控制多个 Spacer 的空间分配比例：

```dart
Row(
  children: [
    Container(width: 50, height: 50, color: Colors.red),
    Spacer(flex: 1),  // 占 1 份
    Container(width: 50, height: 50, color: Colors.green),
    Spacer(flex: 2),  // 占 2 份
    Container(width: 50, height: 50, color: Colors.blue),
  ],
)
// 结果：绿色和蓝色之间的间距是红色和绿色的 2 倍
```

### 2.2 等价写法

Spacer 是 Expanded 的简化形式：

```dart
// 使用 Spacer
Row(
  children: [
    Text('左'),
    Spacer(),
    Text('右'),
  ],
)

// 等价于使用 Expanded
Row(
  children: [
    Text('左'),
    Expanded(child: SizedBox()),
    Text('右'),
  ],
)
```

### 2.3 常见布局场景

#### 导航栏

```dart
Row(
  children: [
    IconButton(
      icon: Icon(Icons.menu),
      onPressed: () {},
    ),
    Spacer(),
    Text('标题'),
    Spacer(),
    IconButton(
      icon: Icon(Icons.search),
      onPressed: () {},
    ),
  ],
)
```

#### 底部按钮

```dart
Row(
  children: [
    ElevatedButton(
      onPressed: () {},
      child: Text('取消'),
    ),
    Spacer(),
    ElevatedButton(
      onPressed: () {},
      child: Text('确认'),
    ),
  ],
)
```

#### 卡片信息

```dart
Card(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Row(
      children: [
        Icon(Icons.person),
        SizedBox(width: 8),
        Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          mainAxisSize: MainAxisSize.min,
          children: [
            Text('张三', style: TextStyle(fontWeight: FontWeight.bold)),
            Text('Flutter 开发工程师'),
          ],
        ),
        Spacer(),
        Text('3小时前', style: TextStyle(color: Colors.grey)),
      ],
    ),
  ),
)
```

#### 列表项

```dart
ListTile(
  leading: Icon(Icons.star),
  title: Text('收藏'),
  trailing: Row(
    mainAxisSize: MainAxisSize.min,
    children: [
      Text('123'),
      Spacer(),
      Icon(Icons.arrow_forward),
    ],
  ),
)
```

### 2.4 Column 中使用

```dart
Column(
  children: [
    Container(height: 50, color: Colors.red),
    Spacer(),
    Container(height: 50, color: Colors.blue),
  ],
)
```

### 2.5 嵌套使用

```dart
Row(
  children: [
    Container(width: 60, height: 60, color: Colors.red),
    Spacer(),
    Column(
      children: [
        Container(width: 60, height: 30, color: Colors.green),
        Spacer(),
        Container(width: 60, height: 30, color: Colors.blue),
      ],
    ),
    Spacer(),
    Container(width: 60, height: 60, color: Colors.yellow),
  ],
)
```

## 3. 高级用法

### 3.1 动态 Spacer

根据条件决定是否使用 Spacer：

```dart
Row(
  children: [
    Text('左边'),
    if (needsSpace) Spacer(),
    Text('右边'),
  ],
)
```

### 3.2 响应式布局

```dart
LayoutBuilder(
  builder: (context, constraints) {
    return Row(
      children: [
        Container(width: 100, color: Colors.red),
        if (constraints.maxWidth > 400) Spacer(flex: 2),
        Spacer(flex: 1),
        Container(width: 100, color: Colors.blue),
      ],
    );
  },
)
```

### 3.3 表单布局

```dart
Column(
  children: [
    Row(
      children: [
        SizedBox(width: 80, child: Text('用户名:')),
        Expanded(child: TextField()),
      ],
    ),
    SizedBox(height: 16),
    Row(
      children: [
        SizedBox(width: 80, child: Text('密码:')),
        Expanded(child: TextField(obscureText: true)),
      ],
    ),
    Spacer(),  // 将按钮推到底部
    SizedBox(
      width: double.infinity,
      child: ElevatedButton(
        onPressed: () {},
        child: Text('登录'),
      ),
    ),
  ],
)
```

### 3.4 工具栏布局

```dart
Row(
  children: [
    IconButton(icon: Icon(Icons.undo), onPressed: () {}),
    IconButton(icon: Icon(Icons.redo), onPressed: () {}),
    Spacer(),
    IconButton(icon: Icon(Icons.cut), onPressed: () {}),
    IconButton(icon: Icon(Icons.copy), onPressed: () {}),
    IconButton(icon: Icon(Icons.paste), onPressed: () {}),
    Spacer(),
    IconButton(icon: Icon(Icons.more_vert), onPressed: () {}),
  ],
)
```

### 3.5 仪表盘布局

```dart
Column(
  children: [
    Row(
      children: [
        _buildMetricCard('用户', '1,234'),
        Spacer(),
        _buildMetricCard('订单', '567'),
        Spacer(),
        _buildMetricCard('收入', '¥89,012'),
      ],
    ),
    Spacer(flex: 2),
    _buildChart(),
    Spacer(),
    _buildSummary(),
  ],
)
```

## 4. 常见问题与解决方案

### 问题1：Spacer 不在 Flex 容器中

**原因**：Spacer 只能在 Row、Column、Flex 中使用。

```dart
// 错误：Spacer 不在 Flex 容器中
Container(
  child: Spacer(),  // 会报错
)

// 解决：使用 SizedBox 或 Padding
Container(
  child: SizedBox(height: 16),  // 或 Padding
)
```

### 问题2：Spacer 和 Expanded 同时使用

**原因**：Spacer 本质上是 Expanded，同时使用会抢占空间。

```dart
// 可能的问题：多个 Expanded 竞争
Row(
  children: [
    Expanded(child: Container(color: Colors.red)),
    Spacer(),
    Expanded(child: Container(color: Colors.blue)),
  ],
)
// 这样是可以的，但需要理解 flex 分配

// 更清晰的方式：使用 flex 控制比例
Row(
  children: [
    Expanded(flex: 2, child: Container(color: Colors.red)),
    Expanded(flex: 1, child: Container()),  // 空白区域
    Expanded(flex: 2, child: Container(color: Colors.blue)),
  ],
)
```

### 问题3：Spacer 在无限高度的 Column 中

```dart
// 问题：外层 Column 已经有 Spacer，内层 Column 再用 Spacer
Column(
  children: [
    Spacer(),  // 外层
    Container(
      height: 100,
      child: Column(
        children: [
          Spacer(),  // 内层：高度有限，可能不生效
          Text('内容'),
        ],
      ),
    ),
  ],
)

// 解决：确保容器有明确高度或使用 Expanded
```

## 5. Spacer vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **Spacer** | 弹性空白 | 简洁，只在 Flex 中有效 |
| **Expanded** | 弹性扩展 | 可包含子组件 |
| **SizedBox** | 固定空白 | 固定尺寸，不限容器 |
| **Container** | 综合容器 | 可设置尺寸、装饰等 |

### 选择建议

```dart
// 需要弹性空白 → 使用 Spacer
Row(
  children: [
    Text('左'),
    Spacer(),
    Text('右'),
  ],
)

// 需要弹性组件 → 使用 Expanded
Row(
  children: [
    Text('左'),
    Expanded(child: TextField()),
    Text('右'),
  ],
)

// 需要固定空白 → 使用 SizedBox
Row(
  children: [
    Text('左'),
    SizedBox(width: 16),
    Text('右'),
  ],
)
```

## 6. 最佳实践

### 6.1 预定义 Spacer

```dart
class AppSpacers {
  static Spacer small() => Spacer(flex: 1);
  static Spacer medium() => Spacer(flex: 2);
  static Spacer large() => Spacer(flex: 3);
}
```

### 6.2 组合使用

```dart
Row(
  children: [
    _buildLeading(),
    Spacer(),  // 自动占据剩余空间
    _buildTrailing(),
  ],
)
```

### 6.3 条件布局

```dart
Row(
  children: [
    Text('标题'),
    if (showBadge) ...[
      Spacer(),
      Badge(child: Text('新')),
    ],
    if (showAction) ...[
      Spacer(),
      IconButton(icon: Icon(Icons.more), onPressed: () {}),
    ],
  ],
)
```

## 总结

Spacer 是 Flutter Flex 布局中的实用工具：

**核心要点**：
1. 只能在 Row、Column、Flex 中使用
2. flex 参数控制空间分配比例
3. 等价于 `Expanded(child: SizedBox())`

**最佳实践**：
1. 两端对齐使用 Spacer
2. 复杂比例使用 flex 控制
3. 非 Flex 容器使用 SizedBox

**常见场景**：
- 两端对齐
- 底部按钮
- 导航栏
- 卡片信息

---

*最后更新: 2026-02-19*

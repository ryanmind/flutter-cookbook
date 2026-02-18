# Flutter Row 组件用法详解

## 初级用法

### 基本概念
Row 是 Flutter 中的水平布局组件，用于将子组件水平排列。它是 Flutter 最基础的布局组件之一。

### 基本语法
```dart
Row(
  children: [
    // 子组件列表
  ],
)
```

### 简单示例
```dart
Row(
  children: [
    Text('Hello'),
    Icon(Icons.favorite),
    Text('Flutter'),
  ],
)
```

### 主要属性（初级）
- `children` - 子组件列表
- `textDirection` - 文字方向（ltr/rtl）
- `verticalDirection` - 垂直方向（up/down）
- `textBaseline` - 文本基线对齐

### 常见问题
Row 默认会将 `mainAxisSize` 设为 `MainAxisSize.max`，即尝试占满父级提供的全部水平空间；如果子组件整体宽度仍然超过外部约束，就会报溢出错误。若希望 Row 随内容收缩，需要显式设置 `mainAxisSize: MainAxisSize.min` 或通过 `Align`、`IntrinsicWidth` 等组件包裹。

```dart
// 错误示例 - 可能导致溢出
Row(
  children: [
    Container(width: 200, height: 50, color: Colors.red),
    Container(width: 200, height: 50, color: Colors.blue),
    Container(width: 200, height: 50, color: Colors.green),
  ],
)
```

## 中级用法

### 解决溢出问题

#### 使用 Expanded
```dart
Row(
  children: [
    Expanded(
      child: Container(color: Colors.red, height: 50),
    ),
    Expanded(
      child: Container(color: Colors.blue, height: 50),
    ),
    Expanded(
      child: Container(color: Colors.green, height: 50),
    ),
  ],
)
```

#### 使用 Flexible
```dart
Row(
  children: [
    Container(width: 100, height: 50, color: Colors.red),
    Flexible(
      child: Container(color: Colors.blue, height: 50),
    ),
    Container(width: 100, height: 50, color: Colors.green),
  ],
)
```

### 对齐控制

#### MainAxisAlignment - 主轴对齐
```dart
Row(
  mainAxisAlignment: MainAxisAlignment.start,    // 起始对齐（默认，LTR 下为左对齐）
  // MainAxisAlignment.center,                     // 居中对齐
  // MainAxisAlignment.end,                       // 结束对齐（LTR 下为右对齐）
  // MainAxisAlignment.spaceAround,               // 环绕间距
  // MainAxisAlignment.spaceBetween,               // 两端对齐
  // MainAxisAlignment.spaceEvenly,               // 均匀分布
  children: [
    Container(width: 50, height: 50, color: Colors.red),
    Container(width: 50, height: 50, color: Colors.blue),
    Container(width: 50, height: 50, color: Colors.green),
  ],
)
```

#### CrossAxisAlignment - 交叉轴对齐
```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.start,      // 顶部对齐
  // CrossAxisAlignment.center,                     // 居中对齐（默认）
  // CrossAxisAlignment.end,                       // 底部对齐
  // CrossAxisAlignment.stretch,                    // 拉伸填充
  // CrossAxisAlignment.baseline,                   // 基线对齐
  children: [
    Container(width: 50, height: 30, color: Colors.red),
    Container(width: 50, height: 50, color: Colors.blue),
    Container(width: 50, height: 70, color: Colors.green),
  ],
)
```

> 使用 `CrossAxisAlignment.baseline` 时必须为 Row 设置 `textBaseline`，**且所有参与对齐的子组件必须能提供基线（如 Text、Baseline）**，否则会抛出运行时异常。不支持基线的组件（如 Container）无法参与基线对齐。

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: const [
    Text('短'),
    SizedBox(width: 12),
    Text('较高的文字', style: TextStyle(fontSize: 24)),
    SizedBox(width: 12),
    Text('高'),
  ],
)
```

#### MainAxisAlignment 配合 Spacer
```dart
Row(
  children: [
    Container(width: 50, height: 50, color: Colors.red),
    Spacer(), // 占据剩余空间
    Container(width: 50, height: 50, color: Colors.blue),
    Spacer(flex: 2), // 占据2倍空间
    Container(width: 50, height: 50, color: Colors.green),
  ],
)
```

### Flexible 和 Expanded 的区别
```dart
// Expanded 强制子组件占满所有可用空间
Row(
  children: [
    Container(width: 50, height: 50, color: Colors.red),
    Expanded(
      child: Container(
        constraints: BoxConstraints(minWidth: 100), // 约束会被忽略
        height: 50,
        color: Colors.blue,
      ),
    ),
    Container(width: 50, height: 50, color: Colors.green),
  ],
)

// Flexible 允许子组件根据自身大小占用空间
Row(
  children: [
    Container(width: 50, height: 50, color: Colors.red),
    Flexible(
      child: Container(
        constraints: BoxConstraints(minWidth: 100), // 约束生效
        height: 50,
        color: Colors.blue,
      ),
    ),
    Container(width: 50, height: 50, color: Colors.green),
  ],
)
```

### 使用 SizedBox 控制间距
```dart
Row(
  children: [
    Container(width: 50, height: 50, color: Colors.red),
    SizedBox(width: 20), // 固定间距
    Container(width: 50, height: 50, color: Colors.blue),
    SizedBox(width: 20),
    Container(width: 50, height: 50, color: Colors.green),
  ],
)
```

## 高级用法

### 自定义布局权重
```dart
Row(
  children: [
    Expanded(
      flex: 1, // 权重为1
      child: Container(color: Colors.red, height: 50),
    ),
    Expanded(
      flex: 2, // 权重为2
      child: Container(color: Colors.blue, height: 50),
    ),
    Expanded(
      flex: 1, // 权重为1
      child: Container(color: Colors.green, height: 50),
    ),
  ],
)
```

### 动态布局
```dart
class DynamicRow extends StatefulWidget {
  @override
  _DynamicRowState createState() => _DynamicRowState();
}

class _DynamicRowState extends State<DynamicRow> {
  bool _isExpanded = false;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          children: [
            Container(width: 50, height: 50, color: Colors.red),
            if (_isExpanded) ...[
              Container(width: 50, height: 50, color: Colors.blue),
              Container(width: 50, height: 50, color: Colors.green),
            ],
            IconButton(
              icon: Icon(_isExpanded ? Icons.expand_less : Icons.expand_more),
              onPressed: () {
                setState(() {
                  _isExpanded = !_isExpanded;
                });
              },
            ),
          ],
        ),
      ],
    );
  }
}
```

### 嵌套布局
```dart
Row(
  children: [
    Expanded(
      child: Column(
        children: [
          Text('标题', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
          SizedBox(height: 8),
          Row(
            children: [
              Icon(Icons.star, color: Colors.orange),
              Icon(Icons.star, color: Colors.orange),
              Icon(Icons.star, color: Colors.orange),
              Icon(Icons.star, color: Colors.orange),
              Icon(Icons.star_half, color: Colors.orange),
            ],
          ),
        ],
      ),
    ),
    ElevatedButton(
      onPressed: () {},
      child: Text('购买'),
    ),
  ],
)
```

### IntrinsicWidth 使用注意
`IntrinsicWidth` 会额外测量子组件两次以计算本征宽度，因此一方面性能成本较高，另一方面也不能再在内部使用 `Expanded`/`Flexible` 这类需要有界约束的组件，否则会在调试模式直接触发 “RenderFlex children have non-zero flex but incoming width constraints are unbounded” 的断言。绝大多数场景可以通过主轴对齐或 `Spacer` 来达到相同效果。

```dart
// 推荐：利用 MainAxisAlignment 或 Spacer 控制左右间距
Row(
  mainAxisAlignment: MainAxisAlignment.spaceBetween,
  children: const [
    Text('左侧信息'),
    Text('右侧'),
  ],
)

Row(
  children: const [
    Text('左侧信息'),
    Spacer(),
    Text('右侧'),
  ],
)

// 若确实需要 Row 根据内容收缩，再考虑 IntrinsicWidth
IntrinsicWidth(
  child: Row(
    mainAxisSize: MainAxisSize.min,
    children: const [
      Text('短文本'),
      SizedBox(width: 16),
      Text('右侧'),
    ],
  ),
)
```

### 响应式布局
```dart
class ResponsiveRow extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final screenWidth = MediaQuery.of(context).size.width;
    final isMobile = screenWidth < 600;

    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth < 600) {
          // 移动端布局
          return Column(
            children: [
              Container(color: Colors.red, height: 50),
              Container(color: Colors.blue, height: 50),
              Container(color: Colors.green, height: 50),
            ],
          );
        } else {
          // 桌面端布局
          return Row(
            children: [
              Expanded(child: Container(color: Colors.red, height: 50)),
              Expanded(child: Container(color: Colors.blue, height: 50)),
              Expanded(child: Container(color: Colors.green, height: 50)),
            ],
          );
        }
      },
    );
  }
}
```

### 高级对齐技巧
```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.start,
  children: [
    Container(
      width: 50,
      height: 100,
      color: Colors.red,
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Icon(Icons.home),
          Text('Home'),
        ],
      ),
    ),
    SizedBox(width: 20),
    Expanded(
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Text('标题', style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold)),
          SizedBox(height: 4),
          Text('描述内容', style: TextStyle(color: Colors.grey)),
          SizedBox(height: 8),
          Row(
            children: [
              Chip(label: Text('标签1')),
              SizedBox(width: 8),
              Chip(label: Text('标签2')),
            ],
          ),
        ],
      ),
    ),
  ],
)
```

### 性能优化技巧
```dart
// 使用 const 构造函数
const Row(
  children: [
    Text('Hello'),
    Text('World'),
  ],
)

// 避免在 Row 中使用过多的复杂组件
class OptimizedRow extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        _buildIcon(), // 拆分为独立方法
        SizedBox(width: 8),
        _buildText(),
      ],
    );
  }

  Widget _buildIcon() {
    return Icon(Icons.favorite);
  }

  Widget _buildText() {
    return Text('Optimized');
  }
}
```

## 最佳实践

1. **优先使用 Expanded 和 Flexible** 来解决溢出问题
2. **合理使用 Spacer 和 SizedBox** 控制间距
3. **考虑响应式设计**，在不同屏幕尺寸下提供良好体验
4. **避免过度嵌套**，保持布局简单明了
5. **使用 const 构造函数** 提升性能
6. **合理使用 LayoutBuilder** 进行响应式布局

---

*最后更新: 2025-12-07*

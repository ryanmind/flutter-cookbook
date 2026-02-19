# Flutter Wrap 组件用法详解

## 简介

Wrap 是 Flutter 中的流式布局组件，用于将子组件按顺序排列，当一行（或一列）放不下时自动换行（或换列）。它非常适合实现标签云、标签选择器、瀑布流等需要自动换行的布局场景。

## 1. 初级用法

### 1.1 基本概念

Wrap 的核心特点：
- 子组件按顺序排列
- 空间不足时自动换行/换列
- 支持水平和垂直方向
- 可控制子组件间距和对齐方式

### 1.2 基本语法

```dart
Wrap(
  children: [
    Chip(label: Text('标签1')),
    Chip(label: Text('标签2')),
    Chip(label: Text('标签3')),
    Chip(label: Text('标签4')),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `direction` | `Axis` | `Axis.horizontal` | 排列方向 |
| `alignment` | `WrapAlignment` | `WrapAlignment.start` | 主轴对齐 |
| `spacing` | `double` | `0.0` | 主轴子组件间距 |
| `runAlignment` | `WrapAlignment` | `WrapAlignment.start` | 交叉轴对齐 |
| `runSpacing` | `double` | `0.0` | 行/列间距 |
| `crossAxisAlignment` | `WrapCrossAlignment` | `WrapCrossAlignment.start` | 交叉轴子组件对齐 |
| `textDirection` | `TextDirection?` | - | 文本方向 |
| `verticalDirection` | `VerticalDirection` | `VerticalDirection.down` | 垂直方向 |
| `clipBehavior` | `Clip` | `Clip.none` | 裁剪行为 |

### 1.4 基础示例

#### 标签云

```dart
Wrap(
  spacing: 8,  // 水平间距
  runSpacing: 8,  // 行间距
  children: [
    Chip(label: Text('Flutter')),
    Chip(label: Text('Dart')),
    Chip(label: Text('Android')),
    Chip(label: Text('iOS')),
    Chip(label: Text('Web')),
    Chip(label: Text('Desktop')),
  ],
)
```

#### 自动换行的按钮组

```dart
Wrap(
  spacing: 12,
  runSpacing: 12,
  children: [
    ElevatedButton(onPressed: () {}, child: Text('按钮1')),
    ElevatedButton(onPressed: () {}, child: Text('按钮2')),
    ElevatedButton(onPressed: () {}, child: Text('按钮3')),
    ElevatedButton(onPressed: () {}, child: Text('按钮4')),
    ElevatedButton(onPressed: () {}, child: Text('按钮5')),
  ],
)
```

## 2. 中级用法

### 2.1 direction 方向

```dart
// 水平排列（默认）- 横向换行
Wrap(
  direction: Axis.horizontal,
  spacing: 8,
  runSpacing: 8,
  children: [
    Container(width: 80, height: 40, color: Colors.red),
    Container(width: 80, height: 40, color: Colors.green),
    Container(width: 80, height: 40, color: Colors.blue),
    Container(width: 80, height: 40, color: Colors.orange),
  ],
)

// 垂直排列 - 纵向换列
Wrap(
  direction: Axis.vertical,
  spacing: 8,
  runSpacing: 8,
  children: [
    Container(width: 80, height: 40, color: Colors.red),
    Container(width: 80, height: 40, color: Colors.green),
    Container(width: 80, height: 40, color: Colors.blue),
    Container(width: 80, height: 40, color: Colors.orange),
  ],
)
```

### 2.2 alignment 主轴对齐

```dart
Column(
  children: [
    Text('start（默认）'),
    Wrap(
      alignment: WrapAlignment.start,
      spacing: 8,
      children: [
        Chip(label: Text('标签1')),
        Chip(label: Text('标签2')),
        Chip(label: Text('标签3')),
      ],
    ),
    SizedBox(height: 16),
    Text('center'),
    Wrap(
      alignment: WrapAlignment.center,
      spacing: 8,
      children: [
        Chip(label: Text('标签1')),
        Chip(label: Text('标签2')),
        Chip(label: Text('标签3')),
      ],
    ),
    SizedBox(height: 16),
    Text('end'),
    Wrap(
      alignment: WrapAlignment.end,
      spacing: 8,
      children: [
        Chip(label: Text('标签1')),
        Chip(label: Text('标签2')),
        Chip(label: Text('标签3')),
      ],
    ),
    SizedBox(height: 16),
    Text('spaceBetween'),
    Wrap(
      alignment: WrapAlignment.spaceBetween,
      spacing: 8,
      children: [
        Chip(label: Text('标签1')),
        Chip(label: Text('标签2')),
        Chip(label: Text('标签3')),
      ],
    ),
    SizedBox(height: 16),
    Text('spaceAround'),
    Wrap(
      alignment: WrapAlignment.spaceAround,
      spacing: 8,
      children: [
        Chip(label: Text('标签1')),
        Chip(label: Text('标签2')),
        Chip(label: Text('标签3')),
      ],
    ),
    SizedBox(height: 16),
    Text('spaceEvenly'),
    Wrap(
      alignment: WrapAlignment.spaceEvenly,
      spacing: 8,
      children: [
        Chip(label: Text('标签1')),
        Chip(label: Text('标签2')),
        Chip(label: Text('标签3')),
      ],
    ),
  ],
)
```

### 2.3 runAlignment 行对齐

```dart
Wrap(
  runAlignment: WrapAlignment.start,  // 行顶部对齐
  // runAlignment: WrapAlignment.center,  // 行居中
  // runAlignment: WrapAlignment.end,  // 行底部对齐
  // runAlignment: WrapAlignment.spaceBetween,  // 行之间均分
  // runAlignment: WrapAlignment.spaceAround,  // 行周围均分
  // runAlignment: WrapAlignment.spaceEvenly,  // 行均匀分布
  spacing: 8,
  runSpacing: 8,
  children: [
    Container(width: 80, height: 40, color: Colors.red),
    Container(width: 80, height: 60, color: Colors.green),
    Container(width: 80, height: 40, color: Colors.blue),
    Container(width: 80, height: 80, color: Colors.orange),
    Container(width: 80, height: 40, color: Colors.purple),
  ],
)
```

### 2.4 crossAxisAlignment 交叉轴对齐

```dart
Wrap(
  crossAxisAlignment: WrapCrossAlignment.start,  // 顶部对齐
  // crossAxisAlignment: WrapCrossAlignment.center,  // 居中对齐
  // crossAxisAlignment: WrapCrossAlignment.end,  // 底部对齐
  spacing: 8,
  runSpacing: 8,
  children: [
    Container(width: 60, height: 40, color: Colors.red),
    Container(width: 60, height: 80, color: Colors.green),
    Container(width: 60, height: 60, color: Colors.blue),
    Container(width: 60, height: 40, color: Colors.orange),
  ],
)
```

### 2.5 间距控制

```dart
Wrap(
  spacing: 16,      // 水平方向子组件间距
  runSpacing: 12,   // 行与行之间的间距
  children: [
    Chip(label: Text('Flutter')),
    Chip(label: Text('Dart')),
    Chip(label: Text('React')),
    Chip(label: Text('Vue')),
    Chip(label: Text('Angular')),
    Chip(label: Text('Svelte')),
  ],
)
```

## 3. 高级用法

### 3.1 可选择标签

```dart
class SelectableTags extends StatefulWidget {
  @override
  _SelectableTagsState createState() => _SelectableTagsState();
}

class _SelectableTagsState extends State<SelectableTags> {
  final List<String> _tags = [
    'Flutter', 'Dart', 'Android', 'iOS', 'Web', 'Desktop',
    'Firebase', 'REST API', 'GraphQL', 'BLoC', 'Provider',
  ];
  final Set<String> _selectedTags = {};

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('选择技术栈:', style: TextStyle(fontSize: 16)),
        SizedBox(height: 12),
        Wrap(
          spacing: 8,
          runSpacing: 8,
          children: _tags.map((tag) {
            final isSelected = _selectedTags.contains(tag);
            return FilterChip(
              label: Text(tag),
              selected: isSelected,
              onSelected: (selected) {
                setState(() {
                  if (selected) {
                    _selectedTags.add(tag);
                  } else {
                    _selectedTags.remove(tag);
                  }
                });
              },
              selectedColor: Colors.blue[100],
              checkmarkColor: Colors.blue,
            );
          }).toList(),
        ),
        SizedBox(height: 16),
        Text('已选择: ${_selectedTags.join(", ")}'),
      ],
    );
  }
}
```

### 3.2 可删除标签

```dart
class DeletableTags extends StatefulWidget {
  @override
  _DeletableTagsState createState() => _DeletableTagsState();
}

class _DeletableTagsState extends State<DeletableTags> {
  List<String> _tags = ['Flutter', 'Dart', 'React', 'Vue'];

  @override
  Widget build(BuildContext context) {
    return Wrap(
      spacing: 8,
      runSpacing: 8,
      children: _tags.map((tag) {
        return Chip(
          label: Text(tag),
          deleteIcon: Icon(Icons.close, size: 18),
          onDeleted: () {
            setState(() {
              _tags.remove(tag);
            });
          },
        );
      }).toList(),
    );
  }
}
```

### 3.3 输入标签

```dart
class InputTags extends StatefulWidget {
  @override
  _InputTagsState createState() => _InputTagsState();
}

class _InputTagsState extends State<InputTags> {
  final List<String> _tags = [];
  final TextEditingController _controller = TextEditingController();

  void _addTag() {
    final text = _controller.text.trim();
    if (text.isNotEmpty && !_tags.contains(text)) {
      setState(() {
        _tags.add(text);
        _controller.clear();
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Row(
          children: [
            Expanded(
              child: TextField(
                controller: _controller,
                decoration: InputDecoration(
                  hintText: '输入标签',
                  border: OutlineInputBorder(),
                ),
                onSubmitted: (_) => _addTag(),
              ),
            ),
            SizedBox(width: 8),
            ElevatedButton(
              onPressed: _addTag,
              child: Text('添加'),
            ),
          ],
        ),
        SizedBox(height: 16),
        Wrap(
          spacing: 8,
          runSpacing: 8,
          children: _tags.map((tag) {
            return Chip(
              label: Text(tag),
              deleteIcon: Icon(Icons.close, size: 18),
              onDeleted: () {
                setState(() {
                  _tags.remove(tag);
                });
              },
            );
          }).toList(),
        ),
      ],
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
}
```

### 3.4 图片瀑布流（简化版）

```dart
class ImageGrid extends StatelessWidget {
  final List<String> images = List.generate(
    12,
    (index) => 'https://picsum.photos/200/${150 + (index % 3) * 50}',
  );

  @override
  Widget build(BuildContext context) {
    return Wrap(
      spacing: 8,
      runSpacing: 8,
      children: images.map((url) {
        return Container(
          width: (MediaQuery.of(context).size.width - 32) / 2 - 4,
          child: ClipRRect(
            borderRadius: BorderRadius.circular(8),
            child: Image.network(
              url,
              fit: BoxFit.cover,
            ),
          ),
        );
      }).toList(),
    );
  }
}
```

### 3.5 响应式 Wrap

```dart
class ResponsiveWrap extends StatelessWidget {
  final List<String> items = List.generate(20, (index) => '项目 ${index + 1}');

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        // 根据宽度计算每行显示的项数
        final itemWidth = 100.0;
        final spacing = 8.0;
        final itemsPerRow = ((constraints.maxWidth + spacing) / (itemWidth + spacing)).floor();

        return Wrap(
          spacing: spacing,
          runSpacing: spacing,
          alignment: WrapAlignment.center,
          children: items.map((item) {
            return Container(
              width: itemWidth,
              height: itemWidth,
              color: Colors.primaries[items.indexOf(item) % Colors.primaries.length],
              child: Center(
                child: Text(
                  item,
                  style: TextStyle(color: Colors.white),
                ),
              ),
            );
          }).toList(),
        );
      },
    );
  }
}
```

## 4. 性能优化

### 4.1 避免过多子组件

```dart
// ❌ 不推荐：大量子组件
Wrap(
  children: List.generate(1000, (index) => Chip(label: Text('标签$index'))),
)

// ✅ 推荐：使用 ListView + Wrap 组合
ListView(
  children: [
    Wrap(
      children: List.generate(100, (index) => Chip(label: Text('标签$index'))),
    ),
  ],
)

// 或者使用分页加载
class PaginatedWrap extends StatefulWidget {
  @override
  _PaginatedWrapState createState() => _PaginatedWrapState();
}

class _PaginatedWrapState extends State<PaginatedWrap> {
  int _page = 1;
  final int _pageSize = 20;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Wrap(
          children: List.generate(_page * _pageSize, (index) {
            return Chip(label: Text('标签${index + 1}'));
          }),
        ),
        if (_page * _pageSize < 100)
          TextButton(
            onPressed: () => setState(() => _page++),
            child: Text('加载更多'),
          ),
      ],
    );
  }
}
```

### 4.2 使用 const 构造函数

```dart
Wrap(
  spacing: 8,
  runSpacing: 8,
  children: const [
    Chip(label: Text('静态标签1')),
    Chip(label: Text('静态标签2')),
    Chip(label: Text('静态标签3')),
  ],
)
```

### 4.3 预计算子组件

```dart
class OptimizedWrap extends StatelessWidget {
  final List<String> tags;

  const OptimizedWrap({required this.tags, Key? key}) : super(key: key);

  // 预构建子组件列表
  late final List<Widget> _children = tags
      .map((tag) => Chip(label: Text(tag)))
      .toList();

  @override
  Widget build(BuildContext context) {
    return Wrap(
      spacing: 8,
      runSpacing: 8,
      children: _children,
    );
  }
}
```

## 5. 常见问题与解决方案

### 问题1：子组件超出 Wrap 边界

```dart
// 问题：子组件宽度大于可用空间
Wrap(
  children: [
    Container(width: 500, height: 50, color: Colors.red),  // 可能溢出
  ],
)

// 解决方案：使用 Flexible 或限制宽度
Wrap(
  children: [
    Container(
      constraints: BoxConstraints(maxWidth: 200),
      child: Container(height: 50, color: Colors.red),
    ),
  ],
)
```

### 问题2：Wrap 在 Column 中高度问题

```dart
// 问题：Wrap 在 Column 中需要确定高度
Column(
  children: [
    Wrap(...),  // 需要确定高度
  ],
)

// 解决方案1：使用 Expanded 包裹
Column(
  children: [
    Expanded(
      child: SingleChildScrollView(
        child: Wrap(...),
      ),
    ),
  ],
)

// 解决方案2：使用 shrinkWrap
Column(
  children: [
    Wrap(...),  // 如果内容有限，可以直接使用
  ],
)
```

### 问题3：间距不均匀

```dart
// 问题：spacing 和 runSpacing 混淆
Wrap(
  spacing: 16,     // 主轴间距
  runSpacing: 8,   // 行间距
  children: [...],
)
```

### 问题4：标签过多导致性能问题

```dart
// 解决方案：限制显示数量
Wrap(
  children: [
    ...tags.take(10).map((tag) => Chip(label: Text(tag))),
    if (tags.length > 10)
      ActionChip(
        label: Text('+${tags.length - 10}'),
        onPressed: () {
          // 显示全部
        },
      ),
  ],
)
```

## 6. Wrap vs 其他组件对比

| 组件 | 特点 | 适用场景 |
|------|------|---------|
| **Wrap** | 自动换行/换列 | 标签云、流式布局 |
| **Row** | 水平排列，不换行 | 固定水平布局 |
| **Column** | 垂直排列，不换行 | 固定垂直布局 |
| **Flex** | 弹性布局，不换行 | 需要灵活调整的布局 |
| **GridView** | 网格布局，滚动 | 大量数据的网格展示 |

```dart
// Wrap - 自动换行
Wrap(
  spacing: 8,
  children: [
    Chip(label: Text('标签1')),
    Chip(label: Text('标签2')),
    Chip(label: Text('标签3')),
    Chip(label: Text('标签4')),
  ],
)

// Row - 不换行，可能溢出
Row(
  children: [
    Chip(label: Text('标签1')),
    SizedBox(width: 8),
    Chip(label: Text('标签2')),
    SizedBox(width: 8),
    Chip(label: Text('标签3')),
    // ...可能溢出
  ],
)

// GridView - 固定网格
GridView.count(
  crossAxisCount: 3,
  children: [
    Container(color: Colors.red),
    Container(color: Colors.green),
    Container(color: Colors.blue),
  ],
)
```

## 7. 最佳实践

### 7.1 合理设置间距

```dart
Wrap(
  spacing: 8,      // 根据设计规范设置
  runSpacing: 8,   // 通常与 spacing 相同
  children: [...],
)
```

### 7.2 使用 Chip 相关组件

```dart
// Chip - 基础标签
Chip(label: Text('标签'))

// InputChip - 可交互标签
InputChip(
  label: Text('标签'),
  onPressed: () {},
  onDeleted: () {},
)

// FilterChip - 可选择标签
FilterChip(
  label: Text('标签'),
  selected: isSelected,
  onSelected: (selected) {},
)

// ChoiceChip - 单选标签
ChoiceChip(
  label: Text('标签'),
  selected: isSelected,
  onSelected: (selected) {},
)

// ActionChip - 动作标签
ActionChip(
  label: Text('标签'),
  onPressed: () {},
)
```

### 7.3 统一样式管理

```dart
class TagStyle {
  static const double spacing = 8.0;
  static const double runSpacing = 8.0;
  static const EdgeInsets padding = EdgeInsets.symmetric(
    horizontal: 12,
    vertical: 6,
  );
}

Wrap(
  spacing: TagStyle.spacing,
  runSpacing: TagStyle.runSpacing,
  children: tags.map((tag) => Chip(
    label: Text(tag),
    padding: TagStyle.padding,
  )).toList(),
)
```

## 总结

Wrap 是 Flutter 中实现流式布局的核心组件：

**核心要点**：
1. 自动换行/换列，适合不确定数量的子组件
2. spacing 控制主轴间距，runSpacing 控制行/列间距
3. 支持多种对齐方式

**常见场景**：
- 标签云
- 技术栈展示
- 图片网格
- 按钮组
- 筛选条件

**最佳实践**：
1. 合理设置间距
2. 使用 Chip 系列组件
3. 避免过多子组件
4. 配合滚动组件使用

---

*最后更新: 2026-02-18*

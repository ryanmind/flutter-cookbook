# Flutter Table 组件用法详解

## 简介

Table 是 Flutter 中用于创建表格布局的组件。它提供了类似 HTML 表格的布局方式，可以精确控制单元格的对齐和跨行跨列。

## 1. 初级用法

### 1.1 基本概念

Table 的核心作用：
- 创建网格表格布局
- 支持跨行跨列
- 可设置边框、对齐等样式

### 1.2 基本语法

```dart
Table(
  children: [
    TableRow(
      children: [
        TableCell(child: Text('单元格 1')),
        TableCell(child: Text('单元格 2')),
      ],
    ),
    TableRow(
      children: [
        TableCell(child: Text('单元格 3')),
        TableCell(child: Text('单元格 4')),
      ],
    ),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `children` | `List<TableRow>` | 表格行列表 |
| `columnWidths` | `Map<int, TableColumnWidth>?` | 列宽度配置 |
| `defaultColumnWidth` | `TableColumnWidth` | 默认列宽 |
| `border` | `TableBorder?` | 边框样式 |
| `defaultVerticalAlignment` | `TableCellVerticalAlignment` | 默认垂直对齐 |

### 1.4 基础示例

#### 简单表格

```dart
Table(
  border: TableBorder.all(),
  children: [
    TableRow(
      children: [
        Padding(padding: EdgeInsets.all(8), child: Text('姓名')),
        Padding(padding: EdgeInsets.all(8), child: Text('年龄')),
        Padding(padding: EdgeInsets.all(8), child: Text('城市')),
      ],
    ),
    TableRow(
      children: [
        Padding(padding: EdgeInsets.all(8), child: Text('张三')),
        Padding(padding: EdgeInsets.all(8), child: Text('25')),
        Padding(padding: EdgeInsets.all(8), child: Text('北京')),
      ],
    ),
    TableRow(
      children: [
        Padding(padding: EdgeInsets.all(8), child: Text('李四')),
        Padding(padding: EdgeInsets.all(8), child: Text('30')),
        Padding(padding: EdgeInsets.all(8), child: Text('上海')),
      ],
    ),
  ],
)
```

## 2. 中级用法

### 2.1 列宽设置

```dart
Table(
  columnWidths: const {
    0: FlexColumnWidth(1),     // 弹性列宽，比例 1
    1: FlexColumnWidth(2),     // 弹性列宽，比例 2
    2: FixedColumnWidth(100),  // 固定宽度 100
  },
  children: [
    TableRow(children: [
      TableCell(child: Text('列 1')),
      TableCell(child: Text('列 2')),
      TableCell(child: Text('列 3')),
    ]),
  ],
)
```

### 2.2 边框样式

```dart
Table(
  border: TableBorder(
    top: BorderSide(color: Colors.black, width: 2),
    bottom: BorderSide(color: Colors.black, width: 2),
    left: BorderSide(color: Colors.grey),
    right: BorderSide(color: Colors.grey),
    horizontalInside: BorderSide(color: Colors.grey[300]!),
    verticalInside: BorderSide(color: Colors.grey[300]!),
  ),
  children: [
    TableRow(children: [
      TableCell(child: Text('A')),
      TableCell(child: Text('B')),
    ]),
    TableRow(children: [
      TableCell(child: Text('C')),
      TableCell(child: Text('D')),
    ]),
  ],
)
```

### 2.3 单元格对齐

```dart
Table(
  defaultVerticalAlignment: TableCellVerticalAlignment.middle,
  children: [
    TableRow(
      children: [
        TableCell(
          verticalAlignment: TableCellVerticalAlignment.top,
          child: Container(height: 60, child: Text('顶部对齐')),
        ),
        TableCell(
          child: Container(height: 60, child: Text('默认居中')),
        ),
        TableCell(
          verticalAlignment: TableCellVerticalAlignment.bottom,
          child: Container(height: 60, child: Text('底部对齐')),
        ),
      ],
    ),
  ],
)
```

### 2.4 斑马纹表格

```dart
Table(
  children: List.generate(10, (index) {
    return TableRow(
      decoration: BoxDecoration(
        color: index % 2 == 0 ? Colors.grey[100] : Colors.white,
      ),
      children: [
        TableCell(child: Padding(
          padding: EdgeInsets.all(8),
          child: Text('行 $index'),
        )),
      ],
    );
  }),
)
```

### 2.5 表头样式

```dart
Table(
  children: [
    // 表头
    TableRow(
      decoration: BoxDecoration(color: Colors.blue),
      children: [
        TableCell(
          child: Padding(
            padding: EdgeInsets.all(12),
            child: Text('姓名', style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold)),
          ),
        ),
        TableCell(
          child: Padding(
            padding: EdgeInsets.all(12),
            child: Text('年龄', style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold)),
          ),
        ),
      ],
    ),
    // 数据行
    TableRow(children: [
      TableCell(child: Padding(padding: EdgeInsets.all(12), child: Text('张三'))),
      TableCell(child: Padding(padding: EdgeInsets.all(12), child: Text('25'))),
    ]),
  ],
)
```

## 3. 高级用法

### 3.1 跨列单元格

```dart
class SpanningTable extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Table(
      border: TableBorder.all(),
      columnWidths: const {
        0: FlexColumnWidth(1),
        1: FlexColumnWidth(1),
        2: FlexColumnWidth(1),
      },
      children: [
        TableRow(
          children: [
            TableCell(
              child: Container(
                padding: EdgeInsets.all(8),
                child: Text('普通单元格'),
              ),
            ),
            TableCell(
              child: Container(
                padding: EdgeInsets.all(8),
                child: Text('普通单元格'),
              ),
            ),
            TableCell(
              child: Container(
                padding: EdgeInsets.all(8),
                child: Text('普通单元格'),
              ),
            ),
          ],
        ),
        TableRow(
          children: [
            TableCell(
              columnSpan: 3,  // 跨 3 列
              child: Container(
                padding: EdgeInsets.all(8),
                color: Colors.grey[200],
                child: Text('跨列单元格'),
              ),
            ),
          ],
        ),
      ],
    );
  }
}
```

### 3.2 可滚动表格

```dart
class ScrollableTable extends StatelessWidget {
  final List<String> headers = ['ID', '名称', '描述', '状态', '操作'];
  final List<List<String>> data = [
    ['1', '项目 A', '这是项目 A 的描述', '进行中', '编辑'],
    ['2', '项目 B', '这是项目 B 的描述', '已完成', '编辑'],
  ];

  @override
  Widget build(BuildContext context) {
    return SingleChildScrollView(
      scrollDirection: Axis.horizontal,
      child: SizedBox(
        width: 600,  // 固定宽度实现横向滚动
        child: Table(
          border: TableBorder.all(),
          children: [
            TableRow(
              decoration: BoxDecoration(color: Colors.blue),
              children: headers.map((h) => TableCell(
                child: Padding(
                  padding: EdgeInsets.all(12),
                  child: Text(h, style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold)),
                ),
              )).toList(),
            ),
            ...data.map((row) => TableRow(
              children: row.map((cell) => TableCell(
                child: Padding(
                  padding: EdgeInsets.all(12),
                  child: Text(cell),
                ),
              )).toList(),
            )),
          ],
        ),
      ),
    );
  }
}
```

### 3.3 动态表格

```dart
class DynamicTable extends StatefulWidget {
  @override
  State<DynamicTable> createState() => _DynamicTableState();
}

class _DynamicTableState extends State<DynamicTable> {
  final List<List<String>> _data = [
    ['张三', '25'],
    ['李四', '30'],
  ];

  void _addRow() {
    setState(() {
      _data.add(['新用户', '20']);
    });
  }

  void _removeRow(int index) {
    setState(() {
      _data.removeAt(index);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Table(
          border: TableBorder.all(),
          children: [
            TableRow(
              decoration: BoxDecoration(color: Colors.blue),
              children: [
                TableCell(child: Padding(padding: EdgeInsets.all(8), child: Text('姓名', style: TextStyle(color: Colors.white)))),
                TableCell(child: Padding(padding: EdgeInsets.all(8), child: Text('年龄', style: TextStyle(color: Colors.white)))),
                TableCell(child: Padding(padding: EdgeInsets.all(8), child: Text('操作', style: TextStyle(color: Colors.white)))),
              ],
            ),
            ..._data.asMap().entries.map((entry) {
              final index = entry.key;
              final row = entry.value;
              return TableRow(
                children: [
                  TableCell(child: Padding(padding: EdgeInsets.all(8), child: Text(row[0]))),
                  TableCell(child: Padding(padding: EdgeInsets.all(8), child: Text(row[1]))),
                  TableCell(
                    child: IconButton(
                      icon: Icon(Icons.delete, color: Colors.red),
                      onPressed: () => _removeRow(index),
                    ),
                  ),
                ],
              );
            }),
          ],
        ),
        ElevatedButton(
          onPressed: _addRow,
          child: Text('添加行'),
        ),
      ],
    );
  }
}
```

### 3.4 响应式表格

```dart
class ResponsiveTable extends StatelessWidget {
  final List<Map<String, String>> data;

  const ResponsiveTable({required this.data, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final isWide = MediaQuery.of(context).size.width > 600;

    if (isWide) {
      return _buildWideTable();
    } else {
      return _buildNarrowTable();
    }
  }

  Widget _buildWideTable() {
    return Table(
      border: TableBorder.all(),
      children: data.map((item) => TableRow(
        children: [
          TableCell(child: Padding(padding: EdgeInsets.all(8), child: Text(item['name']!))),
          TableCell(child: Padding(padding: EdgeInsets.all(8), child: Text(item['email']!))),
          TableCell(child: Padding(padding: EdgeInsets.all(8), child: Text(item['phone']!))),
        ],
      )).toList(),
    );
  }

  Widget _buildNarrowTable() {
    return Column(
      children: data.map((item) => Card(
        child: Padding(
          padding: EdgeInsets.all(12),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              Text('姓名: ${item['name']}'),
              Text('邮箱: ${item['email']}'),
              Text('电话: ${item['phone']}'),
            ],
          ),
        ),
      )).toList(),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：每行列数不一致

```dart
// ❌ 错误：每行必须包含相同数量的单元格
Table(
  children: [
    TableRow(children: [Text('A'), Text('B')]),      // 2 个
    TableRow(children: [Text('C')]),                 // 1 个 - 错误
  ],
)

// ✅ 正确：使用 columnSpan
Table(
  children: [
    TableRow(children: [Text('A'), Text('B')]),
    TableRow(children: [
      TableCell(columnSpan: 2, child: Text('C')),   // 跨 2 列
    ]),
  ],
)
```

### 问题2：内容溢出

```dart
// 使用 FittedBox 或 Flexible 处理溢出
TableCell(
  child: FittedBox(
    fit: BoxFit.scaleDown,
    child: Text('很长的内容文本'),
  ),
)
```

### 问题3：固定表头

```dart
// 使用 CustomScrollView + SliverList
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(
      child: Table(
        children: [/* 表头 */],
      ),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => TableRow(/* 数据行 */),
        childCount: data.length,
      ),
    ),
  ],
)
```

## 5. Table vs 其他表格方案

| 方式 | 用途 | 特点 |
|------|------|------|
| **Table** | 静态表格 | 跨行跨列支持好 |
| **DataTable** | 数据表格 | 内置排序、选择 |
| **GridView** | 网格布局 | 滚动优化 |
| **ListView + Row** | 自定义表格 | 灵活度高 |

### 选择建议

```dart
// 简单静态表格 → Table
Table(children: [...])

// 数据展示+交互 → DataTable
DataTable(columns: [...], rows: [...])

// 大量数据+滚动 → ListView + Row
ListView.builder(itemBuilder: (context, index) => Row(...))
```

## 6. 最佳实践

### 6.1 封装表格组件

```dart
class SimpleTable extends StatelessWidget {
  final List<String> headers;
  final List<List<Widget>> rows;

  const SimpleTable({
    required this.headers,
    required this.rows,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Table(
      border: TableBorder.all(color: Colors.grey[300]!),
      children: [
        TableRow(
          decoration: BoxDecoration(color: Colors.grey[100]),
          children: headers.map((h) => TableCell(
            child: Padding(
              padding: EdgeInsets.all(12),
              child: Text(h, style: TextStyle(fontWeight: FontWeight.bold)),
            ),
          )).toList(),
        ),
        ...rows.map((row) => TableRow(
          children: row.map((cell) => TableCell(
            child: Padding(padding: EdgeInsets.all(12), child: cell),
          )).toList(),
        )),
      ],
    );
  }
}
```

### 6.2 统一样式

```dart
abstract class TableStyles {
  static TableBorder defaultBorder() => TableBorder.all(color: Colors.grey[300]!);
  
  static BoxDecoration headerDecoration() => BoxDecoration(color: Colors.blue);
  
  static EdgeInsets defaultPadding() => EdgeInsets.all(12);
}
```

## 总结

Table 是创建表格布局的经典组件：

**核心要点**：
1. TableRow 定义行，TableCell 定义单元格
2. columnWidths 设置列宽
3. columnSpan 实现跨列

**最佳实践**：
1. 封装为可复用组件
2. 使用 DataTable 替代复杂交互
3. 注意内容溢出处理

**常见场景**：
- 数据展示表格
- 设置页面
- 日历布局

---

*最后更新: 2026-02-19*

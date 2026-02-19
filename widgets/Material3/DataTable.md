# Flutter DataTable 组件用法详解

## 简介

DataTable 是数据表格组件，用于以表格形式展示数据。支持排序、选择、分页等功能，适合展示结构化数据。

## 1. 初级用法

### 1.1 基本概念

DataTable 的核心特点：
- 表头和数据行分离
- 支持排序
- 支持行选择
- 支持自定义样式

### 1.2 基本语法

```dart
DataTable(
  columns: [
    DataColumn(label: Text('姓名')),
    DataColumn(label: Text('年龄')),
    DataColumn(label: Text('城市')),
  ],
  rows: [
    DataRow(cells: [
      DataCell(Text('张三')),
      DataCell(Text('25')),
      DataCell(Text('北京')),
    ]),
    DataRow(cells: [
      DataCell(Text('李四')),
      DataCell(Text('30')),
      DataCell(Text('上海')),
    ]),
  ],
)
```

### 1.3 DataColumn 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `label` | `Widget` | 列标题 |
| `tooltip` | `String?` | 提示文字 |
| `numeric` | `bool` | 是否数字列 |
| `onSort` | `void Function(int, bool)?` | 排序回调 |

### 1.4 DataRow 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `cells` | `List<DataCell>` | 单元格列表 |
| `selected` | `bool` | 是否选中 |
| `onSelectChanged` | `ValueChanged<bool?>?` | 选择变化回调 |
| `onLongPress` | `VoidCallback?` | 长按回调 |
| `color` | `WidgetStateProperty<Color?>?` | 行颜色 |

### 1.5 DataCell 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `child` | `Widget` | 单元格内容 |
| `placeholder` | `bool` | 是否占位 |
| `showEditIcon` | `bool` | 是否显示编辑图标 |
| `onTap` | `VoidCallback?` | 点击回调 |

### 1.6 基础示例

#### 简单表格

```dart
DataTable(
  columns: [
    DataColumn(label: Text('产品')),
    DataColumn(label: Text('价格'), numeric: true),
    DataColumn(label: Text('库存'), numeric: true),
  ],
  rows: [
    DataRow(cells: [
      DataCell(Text('手机')),
      DataCell(Text('¥2999')),
      DataCell(Text('100')),
    ]),
    DataRow(cells: [
      DataCell(Text('电脑')),
      DataCell(Text('¥5999')),
      DataCell(Text('50')),
    ]),
    DataRow(cells: [
      DataCell(Text('平板')),
      DataCell(Text('¥3999')),
      DataCell(Text('80')),
    ]),
  ],
)
```

#### 可选择表格

```dart
class SelectableDataTable extends StatefulWidget {
  @override
  _SelectableDataTableState createState() => _SelectableDataTableState();
}

class _SelectableDataTableState extends State<SelectableDataTable> {
  final Map<int, bool> _selected = {};

  @override
  Widget build(BuildContext context) {
    return DataTable(
      columns: [
        DataColumn(label: Text('姓名')),
        DataColumn(label: Text('年龄')),
      ],
      rows: List.generate(5, (index) {
        return DataRow(
          selected: _selected[index] ?? false,
          onSelectChanged: (selected) {
            setState(() {
              _selected[index] = selected!;
            });
          },
          cells: [
            DataCell(Text('用户 $index')),
            DataCell(Text('${20 + index}')),
          ],
        );
      }),
    );
  }
}
```

## 2. 中级用法

### 2.1 排序功能

```dart
class SortableDataTable extends StatefulWidget {
  @override
  _SortableDataTableState createState() => _SortableDataTableState();
}

class _SortableDataTableState extends State<SortableDataTable> {
  List<Map<String, dynamic>> _data = [
    {'name': '张三', 'age': 25, 'city': '北京'},
    {'name': '李四', 'age': 30, 'city': '上海'},
    {'name': '王五', 'age': 22, 'city': '广州'},
  ];
  
  int? _sortColumnIndex;
  bool _sortAscending = true;

  void _sort(int columnIndex, bool ascending) {
    setState(() {
      _sortColumnIndex = columnIndex;
      _sortAscending = ascending;
      
      _data.sort((a, b) {
        String key = columnIndex == 0 ? 'name' : (columnIndex == 1 ? 'age' : 'city');
        var aValue = a[key];
        var bValue = b[key];
        
        if (aValue is int && bValue is int) {
          return ascending ? aValue.compareTo(bValue) : bValue.compareTo(aValue);
        }
        return ascending 
            ? aValue.toString().compareTo(bValue.toString())
            : bValue.toString().compareTo(aValue.toString());
      });
    });
  }

  @override
  Widget build(BuildContext context) {
    return DataTable(
      sortColumnIndex: _sortColumnIndex,
      sortAscending: _sortAscending,
      columns: [
        DataColumn(
          label: Text('姓名'),
          onSort: _sort,
        ),
        DataColumn(
          label: Text('年龄'),
          numeric: true,
          onSort: _sort,
        ),
        DataColumn(
          label: Text('城市'),
          onSort: _sort,
        ),
      ],
      rows: _data.map((item) {
        return DataRow(cells: [
          DataCell(Text(item['name'])),
          DataCell(Text('${item['age']}')),
          DataCell(Text(item['city'])),
        ]);
      }).toList(),
    );
  }
}
```

### 2.2 可编辑单元格

```dart
DataCell(
  Text('点击编辑'),
  showEditIcon: true,
  onTap: () {
    // 显示编辑对话框
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('编辑'),
        content: TextField(),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: Text('取消'),
          ),
          TextButton(
            onPressed: () {
              // 保存修改
              Navigator.pop(context);
            },
            child: Text('保存'),
          ),
        ],
      ),
    );
  },
)
```

### 2.3 自定义行颜色

```dart
DataRow(
  color: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) {
      return Colors.blue[50];
    }
    return null;
  }),
  cells: [
    DataCell(Text('数据')),
  ],
)
```

### 2.4 占位单元格

```dart
DataCell(
  Text('加载中...'),
  placeholder: true,  // 显示为灰色占位符样式
)
```

## 3. 高级用法

### 3.1 分页表格

```dart
class PaginatedDataTableExample extends StatefulWidget {
  @override
  _PaginatedDataTableExampleState createState() => _PaginatedDataTableExampleState();
}

class _PaginatedDataTableExampleState extends State<PaginatedDataTableExample> {
  final List<Map<String, dynamic>> _allData = List.generate(100, (i) => {
    'id': i + 1,
    'name': '用户 $i',
    'email': 'user$i@example.com',
    'status': i % 2 == 0 ? '活跃' : '禁用',
  });

  int _rowsPerPage = 10;
  int _currentPage = 0;

  List<Map<String, dynamic>> get _currentPageData {
    final start = _currentPage * _rowsPerPage;
    final end = start + _rowsPerPage;
    return _allData.sublist(start, end.clamp(0, _allData.length));
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        SingleChildScrollView(
          scrollDirection: Axis.horizontal,
          child: DataTable(
            columns: [
              DataColumn(label: Text('ID'), numeric: true),
              DataColumn(label: Text('姓名')),
              DataColumn(label: Text('邮箱')),
              DataColumn(label: Text('状态')),
            ],
            rows: _currentPageData.map((item) {
              return DataRow(cells: [
                DataCell(Text('${item['id']}')),
                DataCell(Text(item['name'])),
                DataCell(Text(item['email'])),
                DataCell(Text(item['status'])),
              ]);
            }).toList(),
          ),
        ),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Text('每页行数: '),
            DropdownButton<int>(
              value: _rowsPerPage,
              items: [5, 10, 20, 50].map((value) {
                return DropdownMenuItem(
                  value: value,
                  child: Text('$value'),
                );
              }).toList(),
              onChanged: (value) {
                setState(() {
                  _rowsPerPage = value!;
                  _currentPage = 0;
                });
              },
            ),
            Row(
              children: [
                IconButton(
                  icon: Icon(Icons.chevron_left),
                  onPressed: _currentPage > 0
                      ? () => setState(() => _currentPage--)
                      : null,
                ),
                Text('${_currentPage + 1} / ${(_allData.length / _rowsPerPage).ceil()}'),
                IconButton(
                  icon: Icon(Icons.chevron_right),
                  onPressed: (_currentPage + 1) * _rowsPerPage < _allData.length
                      ? () => setState(() => _currentPage++)
                      : null,
                ),
              ],
            ),
          ],
        ),
      ],
    );
  }
}
```

### 3.2 使用 PaginatedDataTable

```dart
class MyDataTableSource extends DataTableSource {
  final List<Map<String, dynamic>> _data;
  
  MyDataTableSource(this._data);

  @override
  DataRow? getRow(int index) {
    if (index >= _data.length) return null;
    
    final item = _data[index];
    return DataRow(cells: [
      DataCell(Text('${item['id']}')),
      DataCell(Text(item['name'])),
      DataCell(Text(item['email'])),
    ]);
  }

  @override
  bool get isRowCountApproximate => false;

  @override
  int get rowCount => _data.length;

  @override
  int get selectedRowCount => 0;
}

// 使用
PaginatedDataTable(
  header: Text('用户列表'),
  columns: [
    DataColumn(label: Text('ID'), numeric: true),
    DataColumn(label: Text('姓名')),
    DataColumn(label: Text('邮箱')),
  ],
  source: MyDataTableSource(_data),
  rowsPerPage: 10,
)
```

### 3.3 带操作的表格

```dart
DataTable(
  columns: [
    DataColumn(label: Text('名称')),
    DataColumn(label: Text('状态')),
    DataColumn(label: Text('操作')),
  ],
  rows: [
    DataRow(cells: [
      DataCell(Text('项目 A')),
      DataCell(
        Container(
          padding: EdgeInsets.symmetric(horizontal: 8, vertical: 4),
          decoration: BoxDecoration(
            color: Colors.green,
            borderRadius: BorderRadius.circular(12),
          ),
          child: Text('运行中', style: TextStyle(color: Colors.white)),
        ),
      ),
      DataCell(
        Row(
          mainAxisSize: MainAxisSize.min,
          children: [
            IconButton(
              icon: Icon(Icons.edit, size: 18),
              onPressed: () {},
            ),
            IconButton(
              icon: Icon(Icons.delete, size: 18, color: Colors.red),
              onPressed: () {},
            ),
          ],
        ),
      ),
    ]),
  ],
)
```

### 3.4 响应式表格

```dart
class ResponsiveDataTable extends StatelessWidget {
  final List<Map<String, dynamic>> data;

  const ResponsiveDataTable({
    required this.data,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth < 600) {
          // 小屏幕：卡片布局
          return ListView.builder(
            itemCount: data.length,
            itemBuilder: (context, index) {
              final item = data[index];
              return Card(
                margin: EdgeInsets.all(8),
                child: Padding(
                  padding: EdgeInsets.all(16),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text('姓名: ${item['name']}'),
                      Text('年龄: ${item['age']}'),
                      Text('城市: ${item['city']}'),
                    ],
                  ),
                ),
              );
            },
          );
        } else {
          // 大屏幕：表格布局
          return SingleChildScrollView(
            scrollDirection: Axis.horizontal,
            child: DataTable(
              columns: [
                DataColumn(label: Text('姓名')),
                DataColumn(label: Text('年龄'), numeric: true),
                DataColumn(label: Text('城市')),
              ],
              rows: data.map((item) {
                return DataRow(cells: [
                  DataCell(Text(item['name'])),
                  DataCell(Text('${item['age']}')),
                  DataCell(Text(item['city'])),
                ]);
              }).toList(),
            ),
          );
        }
      },
    );
  }
}
```

### 3.5 全选功能

```dart
class SelectAllDataTable extends StatefulWidget {
  @override
  _SelectAllDataTableState createState() => _SelectAllDataTableState();
}

class _SelectAllDataTableState extends State<SelectAllDataTable> {
  final Set<int> _selectedRows = {};
  final int _totalRows = 10;

  bool get _isAllSelected => _selectedRows.length == _totalRows;
  bool get _isAnySelected => _selectedRows.isNotEmpty;
  bool get _isIndeterminate => _isAnySelected && !_isAllSelected;

  void _toggleAll(bool? selected) {
    setState(() {
      if (selected == true) {
        _selectedRows.addAll(List.generate(_totalRows, (i) => i));
      } else {
        _selectedRows.clear();
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          children: [
            Checkbox(
              value: _isAllSelected,
              tristate: true,
              onChanged: _toggleAll,
            ),
            Text('全选'),
            if (_isAnySelected) Text(' (已选 ${_selectedRows.length} 项)'),
          ],
        ),
        DataTable(
          columns: [
            DataColumn(label: Text('姓名')),
            DataColumn(label: Text('年龄')),
          ],
          rows: List.generate(_totalRows, (index) {
            return DataRow(
              selected: _selectedRows.contains(index),
              onSelectChanged: (selected) {
                setState(() {
                  if (selected == true) {
                    _selectedRows.add(index);
                  } else {
                    _selectedRows.remove(index);
                  }
                });
              },
              cells: [
                DataCell(Text('用户 $index')),
                DataCell(Text('${20 + index}')),
              ],
            );
          }),
        ),
      ],
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：表格宽度溢出

```dart
SingleChildScrollView(
  scrollDirection: Axis.horizontal,
  child: DataTable(
    columns: [...],
    rows: [...],
  ),
)
```

### 问题2：排序图标不显示

```dart
DataColumn(
  label: Text('姓名'),
  onSort: (columnIndex, ascending) {
    // 必须设置 onSort 回调才能显示排序图标
  },
)
```

### 问题3：数字列不对齐

```dart
DataColumn(
  label: Text('价格'),
  numeric: true,  // 数字列右对齐
)
```

### 问题4：大量数据卡顿

```dart
// 使用 PaginatedDataTable 和 DataTableSource
PaginatedDataTable(
  source: MyDataTableSource(largeData),
  columns: [...],
  rowsPerPage: 20,
)
```

## 5. DataTable 属性汇总

| 属性 | 类型 | 说明 |
|------|------|------|
| `columns` | `List<DataColumn>` | 列定义 |
| `rows` | `List<DataRow>` | 行数据 |
| `sortColumnIndex` | `int?` | 排序列索引 |
| `sortAscending` | `bool` | 是否升序 |
| `showCheckboxColumn` | `bool` | 是否显示复选框列 |
| `showBottomBorder` | `bool` | 是否显示底部边框 |
| `dividerThickness` | `double` | 分隔线宽度 |
| `dataRowHeight` | `double?` | 数据行高度 |
| `headingRowHeight` | `double?` | 表头行高度 |
| `horizontalMargin` | `double?` | 水平边距 |
| `columnSpacing` | `double?` | 列间距 |
| `headingRowColor` | `WidgetStateProperty<Color?>?` | 表头行颜色 |
| `dataRowColor` | `WidgetStateProperty<Color?>?` | 数据行颜色 |

## 总结

DataTable 是 Flutter 中展示结构化数据的核心组件：

**核心要点**：
1. DataColumn 定义列，DataRow 定义行
2. 支持排序、选择功能
3. 使用 PaginatedDataTable 处理大量数据
4. 可自定义单元格内容

**常用属性**：
- `columns` - 列定义
- `rows` - 行数据
- `sortColumnIndex` - 排序列索引
- `sortAscending` - 排序方向

**最佳实践**：
1. 大数据量使用 PaginatedDataTable
2. 横向滚动处理宽表格
3. 响应式布局适配小屏幕
4. 合理使用 numeric 对齐数字

---

*最后更新: 2026-02-19*

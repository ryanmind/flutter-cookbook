# Flutter DropdownButton 组件用法详解

## 简介

DropdownButton 是传统的下拉选择组件，用于从一组选项中选择一个值。虽然 Material 3 推荐使用 Menu 组件，但 DropdownButton 仍然广泛使用。

## 1. 初级用法

### 1.1 基本概念

DropdownButton 的核心特点：
- 下拉选择单值
- 支持自定义样式
- 支持禁用选项
- 简单易用

### 1.2 基本语法

```dart
String? selectedValue;

DropdownButton<String>(
  value: selectedValue,
  items: [
    DropdownMenuItem(value: 'option1', child: Text('选项一')),
    DropdownMenuItem(value: 'option2', child: Text('选项二')),
    DropdownMenuItem(value: 'option3', child: Text('选项三')),
  ],
  onChanged: (value) {
    setState(() {
      selectedValue = value;
    });
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `value` | `T?` | - | 当前选中值 |
| `items` | `List<DropdownMenuItem<T>>?` | - | 选项列表 |
| `onChanged` | `ValueChanged<T?>?` | - | 选中回调 |
| `hint` | `Widget?` | - | 提示文字（无选中时） |
| `disabledHint` | `Widget?` | - | 禁用时的提示 |
| `onTap` | `VoidCallback?` | - | 点击回调 |
| `elevation` | `int` | `8` | 阴影高度 |
| `style` | `TextStyle?` | - | 文字样式 |
| `underline` | `Widget?` | - | 下划线 |
| `icon` | `Widget?` | - | 下拉图标 |
| `iconDisabledColor` | `Color?` | - | 禁用图标颜色 |
| `iconEnabledColor` | `Color?` | - | 启用图标颜色 |
| `iconSize` | `double` | `24.0` | 图标大小 |
| `isDense` | `bool` | `false` | 紧凑模式 |
| `isExpanded` | `bool` | `false` | 是否占满宽度 |
| `itemHeight` | `double?` | `kMinInteractiveDimension` | 选项高度 |
| `focusColor` | `Color?` | - | 聚焦颜色 |
| `focusNode` | `FocusNode?` | - | 焦点节点 |
| `autofocus` | `bool` | `false` | 自动聚焦 |
| `dropdownColor` | `Color?` | - | 下拉菜单背景色 |
| `padding` | `EdgeInsetsGeometry?` | - | 内边距 |
| `borderRadius` | `BorderRadius?` | - | 圆角 |
| `menuMaxHeight` | `double?` | - | 菜单最大高度 |
| `enableFeedback` | `bool?` | - | 触感反馈 |
| `alignment` | `AlignmentGeometry` | `AlignmentDirectional.centerStart` | 对齐方式 |

### 1.4 DropdownMenuItem 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `value` | `T` | 选项值 |
| `child` | `Widget` | 显示内容 |
| `enabled` | `bool` | 是否启用 |
| `alignment` | `AlignmentGeometry` | 对齐方式 |
| `onTap` | `VoidCallback?` | 点击回调 |

### 1.5 基础示例

#### 简单下拉选择

```dart
class SimpleDropdown extends StatefulWidget {
  @override
  _SimpleDropdownState createState() => _SimpleDropdownState();
}

class _SimpleDropdownState extends State<SimpleDropdown> {
  String? _selectedValue;

  @override
  Widget build(BuildContext context) {
    return DropdownButton<String>(
      value: _selectedValue,
      hint: Text('请选择'),
      items: [
        DropdownMenuItem(value: 'apple', child: Text('苹果')),
        DropdownMenuItem(value: 'banana', child: Text('香蕉')),
        DropdownMenuItem(value: 'orange', child: Text('橙子')),
      ],
      onChanged: (value) {
        setState(() {
          _selectedValue = value;
        });
      },
    );
  }
}
```

#### 带提示的下拉

```dart
DropdownButton<String>(
  value: _selectedValue,
  hint: Text('请选择水果'),
  items: [...],
  onChanged: (value) {},
)
```

#### 全宽下拉

```dart
DropdownButton<String>(
  value: _selectedValue,
  isExpanded: true,  // 占满宽度
  hint: Text('请选择'),
  items: [...],
  onChanged: (value) {},
)
```

## 2. 中级用法

### 2.1 自定义样式

```dart
DropdownButton<String>(
  value: _selectedValue,
  style: TextStyle(color: Colors.blue, fontSize: 16),
  icon: Icon(Icons.arrow_drop_down, color: Colors.blue),
  iconSize: 32,
  underline: Container(
    height: 2,
    color: Colors.blue,
  ),
  items: [...],
  onChanged: (value) {},
)
```

### 2.2 移除下划线

```dart
DropdownButton<String>(
  value: _selectedValue,
  underline: SizedBox(),  // 空组件移除下划线
  items: [...],
  onChanged: (value) {},
)
```

### 2.3 禁用选项

```dart
DropdownButton<String>(
  value: _selectedValue,
  items: [
    DropdownMenuItem(
      value: 'option1',
      child: Text('选项一'),
    ),
    DropdownMenuItem(
      value: 'option2',
      child: Text('选项二（禁用）'),
      enabled: false,  // 禁用此选项
    ),
    DropdownMenuItem(
      value: 'option3',
      child: Text('选项三'),
    ),
  ],
  onChanged: (value) {},
)
```

### 2.4 禁用整个下拉框

```dart
DropdownButton<String>(
  value: _selectedValue,
  disabledHint: Text('已禁用'),
  items: [...],
  onChanged: null,  // null 禁用整个组件
)
```

### 2.5 自定义下拉菜单颜色

```dart
DropdownButton<String>(
  value: _selectedValue,
  dropdownColor: Colors.white,
  items: [...],
  onChanged: (value) {},
)
```

### 2.6 限制菜单高度

```dart
DropdownButton<String>(
  value: _selectedValue,
  menuMaxHeight: 200,  // 最大高度 200
  items: [...],
  onChanged: (value) {},
)
```

## 3. 高级用法

### 3.1 带图标的选项

```dart
DropdownButton<String>(
  value: _selectedValue,
  items: [
    DropdownMenuItem(
      value: 'credit',
      child: Row(
        children: [
          Icon(Icons.credit_card, color: Colors.blue),
          SizedBox(width: 8),
          Text('信用卡'),
        ],
      ),
    ),
    DropdownMenuItem(
      value: 'alipay',
      child: Row(
        children: [
          Icon(Icons.payment, color: Colors.blue),
          SizedBox(width: 8),
          Text('支付宝'),
        ],
      ),
    ),
    DropdownMenuItem(
      value: 'wechat',
      child: Row(
        children: [
          Icon(Icons.chat, color: Colors.green),
          SizedBox(width: 8),
          Text('微信支付'),
        ],
      ),
    ),
  ],
  onChanged: (value) {},
)
```

### 3.2 分组下拉

```dart
DropdownButton<String>(
  value: _selectedValue,
  hint: Text('选择城市'),
  items: [
    DropdownMenuItem(
      enabled: false,
      child: Text('一线城市', style: TextStyle(color: Colors.grey, fontWeight: FontWeight.bold)),
    ),
    DropdownMenuItem(value: 'beijing', child: Text('  北京')),
    DropdownMenuItem(value: 'shanghai', child: Text('  上海')),
    DropdownMenuItem(value: 'guangzhou', child: Text('  广州')),
    DropdownMenuItem(
      enabled: false,
      child: Text('二线城市', style: TextStyle(color: Colors.grey, fontWeight: FontWeight.bold)),
    ),
    DropdownMenuItem(value: 'hangzhou', child: Text('  杭州')),
    DropdownMenuItem(value: 'nanjing', child: Text('  南京')),
  ],
  onChanged: (value) {},
)
```

### 3.3 动态选项

```dart
class DynamicDropdown extends StatefulWidget {
  @override
  _DynamicDropdownState createState() => _DynamicDropdownState();
}

class _DynamicDropdownState extends State<DynamicDropdown> {
  String? _selectedValue;
  List<String> _options = ['加载中...'];
  
  @override
  void initState() {
    super.initState();
    _loadOptions();
  }
  
  Future<void> _loadOptions() async {
    await Future.delayed(Duration(seconds: 1));
    setState(() {
      _options = ['选项一', '选项二', '选项三'];
      _selectedValue = _options.first;
    });
  }

  @override
  Widget build(BuildContext context) {
    return DropdownButton<String>(
      value: _selectedValue,
      isExpanded: true,
      items: _options.map((option) {
        return DropdownMenuItem(
          value: option,
          child: Text(option),
        );
      }).toList(),
      onChanged: (value) {
        setState(() {
          _selectedValue = value;
        });
      },
    );
  }
}
```

### 3.4 带搜索的下拉

```dart
class SearchableDropdown extends StatefulWidget {
  final List<String> items;
  final String? value;
  final ValueChanged<String?>? onChanged;

  const SearchableDropdown({
    required this.items,
    this.value,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _SearchableDropdownState createState() => _SearchableDropdownState();
}

class _SearchableDropdownState extends State<SearchableDropdown> {
  final TextEditingController _searchController = TextEditingController();
  List<String> _filteredItems = [];

  @override
  void initState() {
    super.initState();
    _filteredItems = widget.items;
  }

  void _showSearchDialog() {
    _searchController.clear();
    _filteredItems = widget.items;
    
    showDialog(
      context: context,
      builder: (context) => StatefulBuilder(
        builder: (context, setState) {
          return AlertDialog(
            title: TextField(
              controller: _searchController,
              decoration: InputDecoration(
                hintText: '搜索...',
                prefixIcon: Icon(Icons.search),
              ),
              onChanged: (value) {
                setState(() {
                  _filteredItems = widget.items
                      .where((item) => item.toLowerCase().contains(value.toLowerCase()))
                      .toList();
                });
              },
            ),
            content: SizedBox(
              width: double.maxFinite,
              height: 300,
              child: ListView.builder(
                itemCount: _filteredItems.length,
                itemBuilder: (context, index) {
                  return ListTile(
                    title: Text(_filteredItems[index]),
                    selected: _filteredItems[index] == widget.value,
                    onTap: () {
                      widget.onChanged?.call(_filteredItems[index]);
                      Navigator.pop(context);
                    },
                  );
                },
              ),
            ),
          );
        },
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: _showSearchDialog,
      child: InputDecorator(
        decoration: InputDecoration(
          border: OutlineInputBorder(),
          suffixIcon: Icon(Icons.arrow_drop_down),
        ),
        child: Text(widget.value ?? '请选择'),
      ),
    );
  }
}

// 使用
SearchableDropdown(
  items: List.generate(100, (i) => '选项 $i'),
  value: _selectedValue,
  onChanged: (value) {
    setState(() => _selectedValue = value);
  },
)
```

### 3.5 自定义下拉框

```dart
class CustomDropdown extends StatelessWidget {
  final String? value;
  final List<String> items;
  final ValueChanged<String?>? onChanged;
  final String hint;

  const CustomDropdown({
    this.value,
    required this.items,
    this.onChanged,
    this.hint = '请选择',
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return DropdownButtonFormField<String>(
      value: value,
      decoration: InputDecoration(
        labelText: hint,
        border: OutlineInputBorder(borderRadius: BorderRadius.circular(8)),
        filled: true,
        fillColor: Colors.grey[100],
      ),
      items: items.map((item) {
        return DropdownMenuItem(
          value: item,
          child: Text(item),
        );
      }).toList(),
      onChanged: onChanged,
    );
  }
}
```

### 3.6 DropdownButtonFormField

```dart
class DropdownFormExample extends StatefulWidget {
  @override
  _DropdownFormExampleState createState() => _DropdownFormExampleState();
}

class _DropdownFormExampleState extends State<DropdownFormExample> {
  final _formKey = GlobalKey<FormState>();
  String? _selectedCity;
  String? _selectedGender;

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          DropdownButtonFormField<String>(
            decoration: InputDecoration(
              labelText: '性别',
              border: OutlineInputBorder(),
            ),
            value: _selectedGender,
            items: [
              DropdownMenuItem(value: 'male', child: Text('男')),
              DropdownMenuItem(value: 'female', child: Text('女')),
            ],
            validator: (value) {
              if (value == null) {
                return '请选择性别';
              }
              return null;
            },
            onChanged: (value) {
              setState(() => _selectedGender = value);
            },
          ),
          SizedBox(height: 16),
          DropdownButtonFormField<String>(
            decoration: InputDecoration(
              labelText: '城市',
              border: OutlineInputBorder(),
            ),
            value: _selectedCity,
            items: [
              DropdownMenuItem(value: 'beijing', child: Text('北京')),
              DropdownMenuItem(value: 'shanghai', child: Text('上海')),
              DropdownMenuItem(value: 'guangzhou', child: Text('广州')),
            ],
            validator: (value) {
              if (value == null) {
                return '请选择城市';
              }
              return null;
            },
            onChanged: (value) {
              setState(() => _selectedCity = value);
            },
          ),
          SizedBox(height: 24),
          ElevatedButton(
            onPressed: () {
              if (_formKey.currentState!.validate()) {
                print('提交: 性别=$_selectedGender, 城市=$_selectedCity');
              }
            },
            child: Text('提交'),
          ),
        ],
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：选中值不在 items 中

```dart
// ❌ 错误：value 不在 items 中会导致崩溃
DropdownButton(
  value: 'not_in_list',  // 不在 items 中
  items: [
    DropdownMenuItem(value: 'a', child: Text('A')),
    DropdownMenuItem(value: 'b', child: Text('B')),
  ],
  onChanged: (value) {},
)

// ✅ 正确：确保 value 在 items 中或为 null
String? _value;  // 初始为 null
```

### 问题2：宽度溢出

```dart
DropdownButton(
  isExpanded: true,  // 占满宽度
  items: [...],
  onChanged: (value) {},
)
```

### 问题3：文字被截断

```dart
DropdownButton(
  isExpanded: true,
  items: items.map((item) {
    return DropdownMenuItem(
      value: item,
      child: Text(
        item,
        overflow: TextOverflow.ellipsis,  // 截断并显示省略号
      ),
    );
  }).toList(),
  onChanged: (value) {},
)
```

### 问题4：点击无响应

```dart
// 确保 onChanged 不为 null
DropdownButton(
  items: [...],
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)
```

## 5. DropdownButton vs Menu

| 特性 | DropdownButton | Menu |
|------|---------------|------|
| 设计风格 | 传统 Material | Material 3 |
| 子菜单 | ❌ | ✅ |
| 表单集成 | ✅ DropdownButtonFormField | 需自定义 |
| 图标支持 | 有限 | 完整 |
| 学习曲线 | 简单 | 稍复杂 |

## 总结

DropdownButton 是 Flutter 中传统的下拉选择组件：

**核心要点**：
1. value 必须在 items 中存在
2. onChanged 为 null 时禁用
3. 使用 DropdownButtonFormField 进行表单验证
4. Material 3 推荐使用 Menu 组件

**常用属性**：
- `value` - 当前选中值
- `items` - 选项列表
- `onChanged` - 选中回调
- `hint` - 提示文字
- `isExpanded` - 是否占满宽度

**最佳实践**：
1. 使用 DropdownButtonFormField 进行表单验证
2. 设置 isExpanded 防止溢出
3. 提供合理的 hint 提示
4. 长列表考虑使用搜索功能

---

*最后更新: 2026-02-19*

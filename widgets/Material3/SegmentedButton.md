# Flutter SegmentedButton 组件用法详解

## 简介

SegmentedButton 是 Material 3 的分段按钮组件，用于在一组相关选项中进行单选或多选。它提供了现代化的视觉效果，类似于 iOS 的分段控件。

## 1. 初级用法

### 1.1 基本概念

SegmentedButton 的核心特点：
- Material 3 设计风格
- 支持单选和多选
- 支持自定义样式
- 流畅的动画效果

### 1.2 基本语法

```dart
SegmentedButton<String>(
  segments: [
    ButtonSegment(value: 'day', label: Text('日')),
    ButtonSegment(value: 'week', label: Text('周')),
    ButtonSegment(value: 'month', label: Text('月')),
  ],
  selected: {_selectedValue},
  onSelectionChanged: (Set<String> value) {
    setState(() {
      _selectedValue = value.first;
    });
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `segments` | `List<ButtonSegment<T>>` | - | 分段列表（必需） |
| `selected` | `Set<T>` | - | 选中的值（必需） |
| `onSelectionChanged` | `ValueChanged<Set<T>>?` | - | 选中变化回调 |
| `multiSelectionEnabled` | `bool` | `false` | 是否允许多选 |
| `emptySelectionAllowed` | `bool` | `false` | 是否允许空选 |
| `selectedIcon` | `Widget?` | - | 选中图标 |
| `showSelectedIcon` | `bool` | `true` | 是否显示选中图标 |
| `expandedInsets` | `EdgeInsets?` | - | 扩展内边距 |
| `style` | `ButtonStyle?` | - | 按钮样式 |

### 1.4 ButtonSegment 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `value` | `T` | 分段值（必需） |
| `label` | `Widget?` | 标签 |
| `icon` | `Widget?` | 图标 |
| `enabled` | `bool` | 是否启用 |
| `tooltip` | `String?` | 提示文字 |

### 1.5 基础示例

#### 简单单选

```dart
class SimpleSegmentedButton extends StatefulWidget {
  @override
  _SimpleSegmentedButtonState createState() => _SimpleSegmentedButtonState();
}

class _SimpleSegmentedButtonState extends State<SimpleSegmentedButton> {
  String _selected = 'day';

  @override
  Widget build(BuildContext context) {
    return SegmentedButton<String>(
      segments: [
        ButtonSegment(value: 'day', label: Text('日')),
        ButtonSegment(value: 'week', label: Text('周')),
        ButtonSegment(value: 'month', label: Text('月')),
        ButtonSegment(value: 'year', label: Text('年')),
      ],
      selected: {_selected},
      onSelectionChanged: (Set<String> value) {
        setState(() {
          _selected = value.first;
        });
      },
    );
  }
}
```

#### 带图标的分段按钮

```dart
SegmentedButton<String>(
  segments: [
    ButtonSegment(
      value: 'list',
      icon: Icon(Icons.list),
      label: Text('列表'),
    ),
    ButtonSegment(
      value: 'grid',
      icon: Icon(Icons.grid_view),
      label: Text('网格'),
    ),
    ButtonSegment(
      value: 'map',
      icon: Icon(Icons.map),
      label: Text('地图'),
    ),
  ],
  selected: {_viewMode},
  onSelectionChanged: (value) {
    setState(() => _viewMode = value.first);
  },
)
```

#### 仅图标

```dart
SegmentedButton<String>(
  segments: [
    ButtonSegment(value: 'left', icon: Icon(Icons.format_align_left)),
    ButtonSegment(value: 'center', icon: Icon(Icons.format_align_center)),
    ButtonSegment(value: 'right', icon: Icon(Icons.format_align_right)),
    ButtonSegment(value: 'justify', icon: Icon(Icons.format_align_justify)),
  ],
  selected: {_alignment},
  onSelectionChanged: (value) {
    setState(() => _alignment = value.first);
  },
)
```

## 2. 中级用法

### 2.1 多选模式

```dart
class MultiSelectSegmentedButton extends StatefulWidget {
  @override
  _MultiSelectSegmentedButtonState createState() => _MultiSelectSegmentedButtonState();
}

class _MultiSelectSegmentedButtonState extends State<MultiSelectSegmentedButton> {
  Set<String> _selected = {'bold'};

  @override
  Widget build(BuildContext context) {
    return SegmentedButton<String>(
      multiSelectionEnabled: true,  // 启用多选
      segments: [
        ButtonSegment(value: 'bold', icon: Icon(Icons.format_bold)),
        ButtonSegment(value: 'italic', icon: Icon(Icons.format_italic)),
        ButtonSegment(value: 'underline', icon: Icon(Icons.format_underlined)),
        ButtonSegment(value: 'strike', icon: Icon(Icons.format_strikethrough)),
      ],
      selected: _selected,
      onSelectionChanged: (Set<String> value) {
        setState(() {
          _selected = value;
        });
      },
    );
  }
}
```

### 2.2 允许空选

```dart
SegmentedButton<String>(
  emptySelectionAllowed: true,  // 允许不选中任何项
  segments: [
    ButtonSegment(value: 'option1', label: Text('选项一')),
    ButtonSegment(value: 'option2', label: Text('选项二')),
  ],
  selected: _selected,
  onSelectionChanged: (Set<String> value) {
    setState(() {
      _selected = value;
    });
  },
)
```

### 2.3 禁用某些选项

```dart
SegmentedButton<String>(
  segments: [
    ButtonSegment(value: 'all', label: Text('全部')),
    ButtonSegment(value: 'unread', label: Text('未读')),
    ButtonSegment(value: 'starred', label: Text('已收藏'), enabled: false),
    ButtonSegment(value: 'archived', label: Text('已归档'), enabled: false),
  ],
  selected: {_filter},
  onSelectionChanged: (value) {
    setState(() => _filter = value.first);
  },
)
```

### 2.4 自定义样式

```dart
SegmentedButton<String>(
  style: ButtonStyle(
    backgroundColor: WidgetStateProperty.resolveWith((states) {
      if (states.contains(WidgetState.selected)) {
        return Colors.blue;
      }
      return Colors.grey[200];
    }),
    foregroundColor: WidgetStateProperty.resolveWith((states) {
      if (states.contains(WidgetState.selected)) {
        return Colors.white;
      }
      return Colors.black;
    }),
    textStyle: WidgetStateProperty.all(
      TextStyle(fontWeight: FontWeight.bold),
    ),
  ),
  segments: [
    ButtonSegment(value: 'a', label: Text('A')),
    ButtonSegment(value: 'b', label: Text('B')),
    ButtonSegment(value: 'c', label: Text('C')),
  ],
  selected: {_value},
  onSelectionChanged: (value) {
    setState(() => _value = value.first);
  },
)
```

### 2.5 隐藏选中图标

```dart
SegmentedButton<String>(
  showSelectedIcon: false,  // 隐藏选中图标
  segments: [
    ButtonSegment(value: 'small', label: Text('小')),
    ButtonSegment(value: 'medium', label: Text('中')),
    ButtonSegment(value: 'large', label: Text('大')),
  ],
  selected: {_size},
  onSelectionChanged: (value) {
    setState(() => _size = value.first);
  },
)
```

### 2.6 自定义选中图标

```dart
SegmentedButton<String>(
  selectedIcon: Icon(Icons.star),
  segments: [
    ButtonSegment(value: '1', label: Text('一星')),
    ButtonSegment(value: '2', label: Text('二星')),
    ButtonSegment(value: '3', label: Text('三星')),
  ],
  selected: {_rating},
  onSelectionChanged: (value) {
    setState(() => _rating = value.first);
  },
)
```

## 3. 高级用法

### 3.1 全宽分段按钮

```dart
SizedBox(
  width: double.infinity,
  child: SegmentedButton<String>(
    expandedInsets: EdgeInsets.zero,
    segments: [
      ButtonSegment(value: 'pending', label: Text('待处理')),
      ButtonSegment(value: 'processing', label: Text('处理中')),
      ButtonSegment(value: 'completed', label: Text('已完成')),
    ],
    selected: {_status},
    onSelectionChanged: (value) {
      setState(() => _status = value.first);
    },
  ),
)
```

### 3.2 带工具提示

```dart
SegmentedButton<String>(
  segments: [
    ButtonSegment(
      value: 'download',
      icon: Icon(Icons.download),
      label: Text('下载'),
      tooltip: '下载文件',
    ),
    ButtonSegment(
      value: 'upload',
      icon: Icon(Icons.upload),
      label: Text('上传'),
      tooltip: '上传文件',
    ),
    ButtonSegment(
      value: 'sync',
      icon: Icon(Icons.sync),
      label: Text('同步'),
      tooltip: '同步数据',
    ),
  ],
  selected: {_action},
  onSelectionChanged: (value) {
    setState(() => _action = value.first);
  },
)
```

### 3.3 时间选择器

```dart
class TimePeriodSelector extends StatelessWidget {
  final String selected;
  final ValueChanged<String> onChanged;

  const TimePeriodSelector({
    required this.selected,
    required this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SegmentedButton<String>(
      segments: [
        ButtonSegment(value: 'today', label: Text('今天')),
        ButtonSegment(value: 'yesterday', label: Text('昨天')),
        ButtonSegment(value: 'week', label: Text('本周')),
        ButtonSegment(value: 'month', label: Text('本月')),
      ],
      selected: {selected},
      onSelectionChanged: (value) => onChanged(value.first),
    );
  }
}
```

### 3.4 字体大小选择器

```dart
class FontSizeSelector extends StatelessWidget {
  final double fontSize;
  final ValueChanged<double> onChanged;

  const FontSizeSelector({
    required this.fontSize,
    required this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SegmentedButton<double>(
      segments: [
        ButtonSegment(value: 12.0, label: Text('小')),
        ButtonSegment(value: 16.0, label: Text('中')),
        ButtonSegment(value: 20.0, label: Text('大')),
        ButtonSegment(value: 24.0, label: Text('特大')),
      ],
      selected: {fontSize},
      onSelectionChanged: (value) => onChanged(value.first),
    );
  }
}
```

### 3.5 配合筛选功能

```dart
class FilterBar extends StatelessWidget {
  final String filter;
  final ValueChanged<String> onFilterChanged;
  final String sortOrder;
  final ValueChanged<String> onSortChanged;

  const FilterBar({
    required this.filter,
    required this.onFilterChanged,
    required this.sortOrder,
    required this.onSortChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        // 筛选
        Expanded(
          child: SegmentedButton<String>(
            expandedInsets: EdgeInsets.zero,
            segments: [
              ButtonSegment(value: 'all', label: Text('全部')),
              ButtonSegment(value: 'active', label: Text('活跃')),
              ButtonSegment(value: 'inactive', label: Text('非活跃')),
            ],
            selected: {filter},
            onSelectionChanged: (value) => onFilterChanged(value.first),
          ),
        ),
        SizedBox(width: 16),
        // 排序
        SegmentedButton<String>(
          segments: [
            ButtonSegment(value: 'asc', icon: Icon(Icons.arrow_upward)),
            ButtonSegment(value: 'desc', icon: Icon(Icons.arrow_downward)),
          ],
          selected: {sortOrder},
          onSelectionChanged: (value) => onSortChanged(value.first),
        ),
      ],
    );
  }
}
```

### 3.6 动态分段按钮

```dart
class DynamicSegmentedButton extends StatelessWidget {
  final List<String> options;
  final String selected;
  final ValueChanged<String> onChanged;

  const DynamicSegmentedButton({
    required this.options,
    required this.selected,
    required this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SingleChildScrollView(
      scrollDirection: Axis.horizontal,
      child: SegmentedButton<String>(
        segments: options.map((option) {
          return ButtonSegment(value: option, label: Text(option));
        }).toList(),
        selected: {selected},
        onSelectionChanged: (value) => onChanged(value.first),
      ),
    );
  }
}

// 使用
DynamicSegmentedButton(
  options: ['标签1', '标签2', '标签3', '标签4', '标签5'],
  selected: _tag,
  onChanged: (value) => setState(() => _tag = value),
)
```

## 4. 常见问题与解决方案

### 问题1：选中项不对

```dart
// 确保使用 Set 类型
SegmentedButton<String>(
  selected: {_selected},  // 必须是 Set
  // ...
)
```

### 问题2：无法取消选中

```dart
// 单选模式下点击已选项不会取消
// 如果需要取消，启用 emptySelectionAllowed
SegmentedButton<String>(
  emptySelectionAllowed: true,  // 允许空选
  // ...
)
```

### 问题3：样式不生效

```dart
// 使用 WidgetStateProperty
SegmentedButton<String>(
  style: ButtonStyle(
    backgroundColor: WidgetStateProperty.resolveWith((states) {
      if (states.contains(WidgetState.selected)) {
        return Colors.blue;
      }
      return Colors.white;
    }),
  ),
  // ...
)
```

### 问题4：宽度不够

```dart
// 使用 expandedInsets
SegmentedButton<String>(
  expandedInsets: EdgeInsets.zero,  // 扩展到父容器宽度
  // ...
)
```

## 5. SegmentedButton vs 其他组件

| 特性 | SegmentedButton | ToggleButtons | BottomNavigationBar |
|------|-----------------|---------------|---------------------|
| 设计风格 | Material 3 | 传统 Material | 底部导航 |
| 动画效果 | ✅ 流畅 | ❌ 无 | ✅ 有 |
| 多选支持 | ✅ | ✅ | ❌ |
| 图标支持 | ✅ | ✅ | ✅ |
| 使用场景 | 内联选择 | 工具栏 | 页面导航 |

## 总结

SegmentedButton 是 Flutter Material 3 的分段选择组件：

**核心要点**：
1. Material 3 现代设计风格
2. 支持单选和多选模式
3. 支持图标和文字组合
4. 流畅的动画效果

**常用属性**：
- `segments` - 分段列表
- `selected` - 选中值集合
- `onSelectionChanged` - 选中变化回调
- `multiSelectionEnabled` - 是否多选

**最佳实践**：
1. 选项数量适中（2-5个）
2. 使用图标增强可读性
3. 合理设置多选/空选
4. 配合滚动处理过多选项

---

*最后更新: 2026-02-19*

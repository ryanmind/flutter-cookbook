# Flutter Chip 组件用法详解

## 简介

Chip 是 Material Design 风格的标签组件，用于展示信息、筛选选项、输入标签等场景。Flutter 提供了多种 Chip 变体：Chip、InputChip、ChoiceChip、FilterChip 和 ActionChip。

## 1. Chip 基础标签

### 1.1 基本概念

Chip 的核心特点：
- 显示简短信息
- 可包含图标、头像
- 支持删除操作
- Material 3 圆角设计

### 1.2 基本语法

```dart
Chip(
  label: Text('标签'),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `label` | `Widget` | 标签内容（必需） |
| `avatar` | `Widget?` | 头像/图标 |
| `deleteIcon` | `Widget?` | 删除图标 |
| `onDeleted` | `VoidCallback?` | 删除回调 |
| `backgroundColor` | `Color?` | 背景色 |
| `side` | `BorderSide?` | 边框 |
| `shape` | `OutlinedBorder?` | 形状 |
| `labelStyle` | `TextStyle?` | 标签样式 |
| `labelPadding` | `EdgeInsetsGeometry?` | 标签内边距 |
| `padding` | `EdgeInsetsGeometry?` | 内边距 |

### 1.4 基础示例

#### 简单标签

```dart
Chip(label: Text('Flutter'))

Chip(label: Text('Dart'))
```

#### 带图标

```dart
Chip(
  avatar: Icon(Icons.star, size: 18),
  label: Text('收藏'),
)

Chip(
  avatar: CircleAvatar(
    child: Text('A'),
  ),
  label: Text('用户A'),
)
```

#### 可删除标签

```dart
Chip(
  label: Text('可删除'),
  deleteIcon: Icon(Icons.close, size: 18),
  onDeleted: () {
    print('删除标签');
  },
)
```

#### 自定义样式

```dart
Chip(
  label: Text('自定义'),
  backgroundColor: Colors.blue[100],
  side: BorderSide(color: Colors.blue),
  labelStyle: TextStyle(color: Colors.blue),
)
```

## 2. InputChip 输入标签

### 2.1 基本概念

InputChip 的核心特点：
- 可选中状态
- 支持点击和删除
- 常用于标签输入、联系人选择

### 2.2 基本语法

```dart
InputChip(
  label: Text('用户'),
  avatar: CircleAvatar(child: Text('A')),
  onDeleted: () {},
  selected: true,
  onSelected: (selected) {},
)
```

### 2.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `label` | `Widget` | 标签内容 |
| `avatar` | `Widget?` | 头像 |
| `selected` | `bool` | 是否选中 |
| `onSelected` | `ValueChanged<bool>?` | 选中回调 |
| `deleteIcon` | `Widget?` | 删除图标 |
| `onDeleted` | `VoidCallback?` | 删除回调 |
| `isEnabled` | `bool` | 是否启用 |
| `onPressed` | `VoidCallback?` | 点击回调 |

### 2.4 基础示例

#### 联系人选择

```dart
class ContactChip extends StatefulWidget {
  final List<String> contacts;

  const ContactChip({required this.contacts, Key? key}) : super(key: key);

  @override
  _ContactChipState createState() => _ContactChipState();
}

class _ContactChipState extends State<ContactChip> {
  final Set<String> _selected = {};

  @override
  Widget build(BuildContext context) {
    return Wrap(
      spacing: 8,
      children: widget.contacts.map((contact) {
        final isSelected = _selected.contains(contact);
        return InputChip(
          label: Text(contact),
          avatar: CircleAvatar(child: Text(contact[0])),
          selected: isSelected,
          onSelected: (selected) {
            setState(() {
              if (selected) {
                _selected.add(contact);
              } else {
                _selected.remove(contact);
              }
            });
          },
        );
      }).toList(),
    );
  }
}
```

#### 标签输入

```dart
class TagInput extends StatefulWidget {
  @override
  _TagInputState createState() => _TagInputState();
}

class _TagInputState extends State<TagInput> {
  final List<String> _tags = ['Flutter', 'Dart'];
  final _controller = TextEditingController();

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  void _addTag() {
    final text = _controller.text.trim();
    if (text.isNotEmpty && !_tags.contains(text)) {
      setState(() {
        _tags.add(text);
        _controller.clear();
      });
    }
  }

  void _removeTag(String tag) {
    setState(() {
      _tags.remove(tag);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Wrap(
          spacing: 8,
          runSpacing: 8,
          children: _tags.map((tag) {
            return InputChip(
              label: Text(tag),
              onDeleted: () => _removeTag(tag),
            );
          }).toList(),
        ),
        SizedBox(height: 8),
        TextField(
          controller: _controller,
          decoration: InputDecoration(
            hintText: '输入标签',
            suffixIcon: IconButton(
              icon: Icon(Icons.add),
              onPressed: _addTag,
            ),
          ),
          onSubmitted: (_) => _addTag(),
        ),
      ],
    );
  }
}
```

## 3. ChoiceChip 选择标签

### 3.1 基本概念

ChoiceChip 的核心特点：
- 单选功能
- 选中状态明显
- 常用于选项选择

### 3.2 基本语法

```dart
ChoiceChip(
  label: Text('选项'),
  selected: _selected == 'option',
  onSelected: (selected) {
    setState(() => _selected = 'option');
  },
)
```

### 3.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `label` | `Widget` | 标签内容 |
| `selected` | `bool` | 是否选中 |
| `onSelected` | `ValueChanged<bool>?` | 选中回调 |
| `avatar` | `Widget?` | 头像 |
| `selectedColor` | `Color?` | 选中背景色 |
| `disabledColor` | `Color?` | 禁用颜色 |

### 3.4 基础示例

#### 单选选择器

```dart
class SingleChoiceChips extends StatefulWidget {
  final List<String> options;
  final String? initialSelection;

  const SingleChoiceChips({
    required this.options,
    this.initialSelection,
    Key? key,
  }) : super(key: key);

  @override
  _SingleChoiceChipsState createState() => _SingleChoiceChipsState();
}

class _SingleChoiceChipsState extends State<SingleChoiceChips> {
  String? _selected;

  @override
  void initState() {
    super.initState();
    _selected = widget.initialSelection;
  }

  @override
  Widget build(BuildContext context) {
    return Wrap(
      spacing: 8,
      children: widget.options.map((option) {
        return ChoiceChip(
          label: Text(option),
          selected: _selected == option,
          selectedColor: Colors.blue[100],
          onSelected: (selected) {
            if (selected) {
              setState(() => _selected = option);
            }
          },
        );
      }).toList(),
    );
  }
}

// 使用
SingleChoiceChips(
  options: ['全部', '待付款', '待发货', '已完成'],
  initialSelection: '全部',
)
```

#### 尺寸选择

```dart
class SizeSelector extends StatefulWidget {
  final List<String> sizes;
  final ValueChanged<String>? onSelected;

  const SizeSelector({
    required this.sizes,
    this.onSelected,
    Key? key,
  }) : super(key: key);

  @override
  _SizeSelectorState createState() => _SizeSelectorState();
}

class _SizeSelectorState extends State<SizeSelector> {
  String? _selectedSize;

  @override
  Widget build(BuildContext context) {
    return Wrap(
      spacing: 8,
      children: widget.sizes.map((size) {
        return ChoiceChip(
          label: Text(size),
          selected: _selectedSize == size,
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(8),
          ),
          onSelected: (selected) {
            if (selected) {
              setState(() => _selectedSize = size);
              widget.onSelected?.call(size);
            }
          },
        );
      }).toList(),
    );
  }
}
```

## 4. FilterChip 筛选标签

### 4.1 基本概念

FilterChip 的核心特点：
- 多选功能
- 复选框指示器
- 常用于筛选条件

### 4.2 基本语法

```dart
FilterChip(
  label: Text('筛选'),
  selected: _filters.contains('filter'),
  onSelected: (selected) {
    // 处理选择
  },
)
```

### 4.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `label` | `Widget` | 标签内容 |
| `selected` | `bool` | 是否选中 |
| `onSelected` | `ValueChanged<bool>?` | 选中回调 |
| `avatar` | `Widget?` | 头像 |
| `selectedColor` | `Color?` | 选中背景色 |
| `checkmarkColor` | `Color?` | 选中标记颜色 |

### 4.4 基础示例

#### 多选筛选器

```dart
class FilterChips extends StatefulWidget {
  final List<String> options;
  final ValueChanged<Set<String>>? onChanged;

  const FilterChips({
    required this.options,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _FilterChipsState createState() => _FilterChipsState();
}

class _FilterChipsState extends State<FilterChips> {
  final Set<String> _selected = {};

  void _toggleSelection(String option, bool selected) {
    setState(() {
      if (selected) {
        _selected.add(option);
      } else {
        _selected.remove(option);
      }
      widget.onChanged?.call(_selected);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Wrap(
      spacing: 8,
      runSpacing: 8,
      children: widget.options.map((option) {
        return FilterChip(
          label: Text(option),
          selected: _selected.contains(option),
          selectedColor: Colors.blue[100],
          checkmarkColor: Colors.blue,
          onSelected: (selected) => _toggleSelection(option, selected),
        );
      }).toList(),
    );
  }
}

// 使用
FilterChips(
  options: ['外卖', '自取', '堂食', '预约'],
  onChanged: (selected) {
    print('选中: $selected');
  },
)
```

#### 商品筛选

```dart
class ProductFilter extends StatefulWidget {
  @override
  _ProductFilterState createState() => _ProductFilterState();
}

class _ProductFilterState extends State<ProductFilter> {
  final Set<String> _categories = {};
  final Set<String> _brands = {};
  String _priceRange = 'all';

  final _categoryOptions = ['手机', '电脑', '平板', '配件'];
  final _brandOptions = ['Apple', 'Samsung', '华为', '小米'];
  final _priceOptions = ['全部', '0-1000', '1000-3000', '3000+'];

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('分类', style: TextStyle(fontWeight: FontWeight.bold)),
        SizedBox(height: 8),
        Wrap(
          spacing: 8,
          children: _categoryOptions.map((cat) {
            return FilterChip(
              label: Text(cat),
              selected: _categories.contains(cat),
              onSelected: (selected) {
                setState(() {
                  if (selected) {
                    _categories.add(cat);
                  } else {
                    _categories.remove(cat);
                  }
                });
              },
            );
          }).toList(),
        ),
        SizedBox(height: 16),
        Text('品牌', style: TextStyle(fontWeight: FontWeight.bold)),
        SizedBox(height: 8),
        Wrap(
          spacing: 8,
          children: _brandOptions.map((brand) {
            return FilterChip(
              label: Text(brand),
              selected: _brands.contains(brand),
              onSelected: (selected) {
                setState(() {
                  if (selected) {
                    _brands.add(brand);
                  } else {
                    _brands.remove(brand);
                  }
                });
              },
            );
          }).toList(),
        ),
        SizedBox(height: 16),
        Text('价格', style: TextStyle(fontWeight: FontWeight.bold)),
        SizedBox(height: 8),
        Wrap(
          spacing: 8,
          children: _priceOptions.map((price) {
            return ChoiceChip(
              label: Text(price),
              selected: _priceRange == price,
              onSelected: (selected) {
                if (selected) {
                  setState(() => _priceRange = price);
                }
              },
            );
          }).toList(),
        ),
      ],
    );
  }
}
```

## 5. ActionChip 操作标签

### 5.1 基本概念

ActionChip 的核心特点：
- 可点击触发操作
- 无选中状态
- 常用于快捷操作、推荐标签

### 5.2 基本语法

```dart
ActionChip(
  avatar: Icon(Icons.add),
  label: Text('添加'),
  onPressed: () {},
)
```

### 5.3 基础示例

#### 快捷操作

```dart
Wrap(
  spacing: 8,
  children: [
    ActionChip(
      avatar: Icon(Icons.add),
      label: Text('新建'),
      onPressed: () {
        print('新建');
      },
    ),
    ActionChip(
      avatar: Icon(Icons.edit),
      label: Text('编辑'),
      onPressed: () {
        print('编辑');
      },
    ),
    ActionChip(
      avatar: Icon(Icons.share),
      label: Text('分享'),
      onPressed: () {
        print('分享');
      },
    ),
  ],
)
```

#### 推荐标签

```dart
class RecommendedTags extends StatelessWidget {
  final List<String> recommendations;
  final ValueChanged<String>? onTagTap;

  const RecommendedTags({
    required this.recommendations,
    this.onTagTap,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('推荐搜索', style: TextStyle(color: Colors.grey)),
        SizedBox(height: 8),
        Wrap(
          spacing: 8,
          runSpacing: 8,
          children: recommendations.map((tag) {
            return ActionChip(
              label: Text(tag),
              onPressed: () => onTagTap?.call(tag),
            );
          }).toList(),
        ),
      ],
    );
  }
}

// 使用
RecommendedTags(
  recommendations: ['iPhone 15', 'MacBook', 'AirPods', 'iPad'],
  onTagTap: (tag) {
    print('搜索: $tag');
  },
)
```

## 6. 高级用法

### 6.1 自定义 Chip 样式

```dart
class CustomChip extends StatelessWidget {
  final String label;
  final Color color;
  final VoidCallback? onTap;

  const CustomChip({
    required this.label,
    required this.color,
    this.onTap,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onTap,
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 12, vertical: 6),
        decoration: BoxDecoration(
          color: color.withOpacity(0.1),
          borderRadius: BorderRadius.circular(16),
          border: Border.all(color: color),
        ),
        child: Text(
          label,
          style: TextStyle(color: color),
        ),
      ),
    );
  }
}
```

### 6.2 可展开的 Chip 组

```dart
class ExpandableChips extends StatefulWidget {
  final List<String> chips;
  final int initialShowCount;

  const ExpandableChips({
    required this.chips,
    this.initialShowCount = 3,
    Key? key,
  }) : super(key: key);

  @override
  _ExpandableChipsState createState() => _ExpandableChipsState();
}

class _ExpandableChipsState extends State<ExpandableChips> {
  bool _isExpanded = false;

  @override
  Widget build(BuildContext context) {
    final displayedChips = _isExpanded
        ? widget.chips
        : widget.chips.take(widget.initialShowCount).toList();

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Wrap(
          spacing: 8,
          runSpacing: 8,
          children: displayedChips.map((chip) {
            return Chip(label: Text(chip));
          }).toList(),
        ),
        if (widget.chips.length > widget.initialShowCount)
          TextButton(
            onPressed: () {
              setState(() => _isExpanded = !_isExpanded);
            },
            child: Text(_isExpanded ? '收起' : '展开更多'),
          ),
      ],
    );
  }
}
```

### 6.3 Chip 分组

```dart
class ChipGroup extends StatelessWidget {
  final String title;
  final List<String> chips;
  final Set<String>? selectedChips;
  final bool multiSelect;
  final ValueChanged<String>? onChipTap;

  const ChipGroup({
    required this.title,
    required this.chips,
    this.selectedChips,
    this.multiSelect = true,
    this.onChipTap,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(title, style: TextStyle(fontWeight: FontWeight.bold)),
        SizedBox(height: 8),
        Wrap(
          spacing: 8,
          runSpacing: 8,
          children: chips.map((chip) {
            final isSelected = selectedChips?.contains(chip) ?? false;

            if (multiSelect) {
              return FilterChip(
                label: Text(chip),
                selected: isSelected,
                onSelected: (_) => onChipTap?.call(chip),
              );
            } else {
              return ChoiceChip(
                label: Text(chip),
                selected: isSelected,
                onSelected: (_) => onChipTap?.call(chip),
              );
            }
          }).toList(),
        ),
      ],
    );
  }
}
```

## 7. 常见问题与解决方案

### 问题1：Chip 溢出

```dart
// 使用 Wrap 自动换行
Wrap(
  spacing: 8,
  runSpacing: 8,
  children: chips.map((chip) => Chip(label: Text(chip))).toList(),
)
```

### 问题2：Chip 删除按钮不显示

```dart
// 必须同时设置 deleteIcon 和 onDeleted
Chip(
  label: Text('标签'),
  deleteIcon: Icon(Icons.close),
  onDeleted: () {
    // 删除逻辑
  },
)
```

### 问题3：Chip 样式不统一

```dart
// 使用 ChipTheme 统一样式
ChipTheme(
  data: ChipThemeData(
    backgroundColor: Colors.grey[200],
    selectedColor: Colors.blue[100],
    labelStyle: TextStyle(fontSize: 14),
  ),
  child: Wrap(
    children: [...],
  ),
)
```

### 问题4：Chip 选中状态不明显

```dart
FilterChip(
  label: Text('选项'),
  selected: true,
  selectedColor: Colors.blue,  // 选中背景色
  checkmarkColor: Colors.white,  // 选中标记颜色
  onSelected: (_) {},
)
```

### 问题5：Chip 点击区域太小

```dart
// 增加 padding
Chip(
  label: Text('标签'),
  padding: EdgeInsets.symmetric(horizontal: 12, vertical: 8),
  materialTapTargetSize: MaterialTapTargetSize.padded,
)
```

## 8. 最佳实践

### 8.1 Chip 类型选择

| 类型 | 使用场景 |
|------|---------|
| Chip | 显示信息、展示状态 |
| InputChip | 联系人选择、标签输入 |
| ChoiceChip | 单选选项 |
| FilterChip | 多选筛选 |
| ActionChip | 快捷操作、推荐标签 |

### 8.2 封装通用 Chip 组件

```dart
class AppChip extends StatelessWidget {
  final String label;
  final IconData? icon;
  final bool selected;
  final VoidCallback? onTap;
  final VoidCallback? onDelete;
  final ChipType type;

  const AppChip({
    required this.label,
    this.icon,
    this.selected = false,
    this.onTap,
    this.onDelete,
    this.type = ChipType.info,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    switch (type) {
      case ChipType.info:
        return Chip(
          label: Text(label),
          avatar: icon != null ? Icon(icon, size: 18) : null,
          onDeleted: onDelete,
        );
      case ChipType.choice:
        return ChoiceChip(
          label: Text(label),
          avatar: icon != null ? Icon(icon, size: 18) : null,
          selected: selected,
          onSelected: (_) => onTap?.call(),
        );
      case ChipType.filter:
        return FilterChip(
          label: Text(label),
          avatar: icon != null ? Icon(icon, size: 18) : null,
          selected: selected,
          onSelected: (_) => onTap?.call(),
        );
      case ChipType.action:
        return ActionChip(
          avatar: icon != null ? Icon(icon, size: 18) : null,
          label: Text(label),
          onPressed: onTap,
        );
    }
  }
}

enum ChipType { info, choice, filter, action }
```

## 总结

Flutter Chip 组件提供了丰富的标签展示方式：

**Chip** - 基础标签，展示信息
**InputChip** - 输入标签，支持选中/删除
**ChoiceChip** - 选择标签，单选场景
**FilterChip** - 筛选标签，多选场景
**ActionChip** - 操作标签，触发操作

**常用场景**：
- 标签选择
- 筛选条件
- 联系人选择
- 推荐标签
- 状态展示

**最佳实践**：
1. 使用 Wrap 处理溢出
2. 统一 Chip 样式
3. 合理选择 Chip 类型
4. 提供良好的视觉反馈

---

*最后更新: 2026-02-18*

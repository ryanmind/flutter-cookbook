# Flutter Radio 组件用法详解

## 简介

Radio 是单选框组件，用于从一组选项中选择一个。通常与 RadioListTile 配合使用，提供更好的用户体验。

## 1. 初级用法

### 1.1 基本概念

Radio 的核心特点：
- 单选互斥
- 必须配合分组使用
- 需要维护选中状态
- Material 3 新样式

### 1.2 基本语法

```dart
// 单个 Radio
Radio<String>(
  value: 'option1',
  groupValue: _selectedValue,
  onChanged: (value) {
    setState(() {
      _selectedValue = value;
    });
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `value` | `T` | - | 当前 Radio 的值（必需） |
| `groupValue` | `T?` | - | 当前分组的选中值 |
| `onChanged` | `ValueChanged<T?>?` | - | 选中回调 |
| `mouseCursor` | `MouseCursor?` | - | 鼠标悬停样式 |
| `toggleable` | `bool` | `false` | 是否可取消选中 |
| `activeColor` | `Color?` | - | 选中颜色 |
| `fillColor` | `Color?` | - | 填充颜色 |
| `focusColor` | `Color?` | - | 聚焦颜色 |
| `hoverColor` | `Color?` | - | 悬停颜色 |
| `overlayColor` | `MaterialStateProperty<Color?>?` | - | 覆盖层颜色 |
| `splashRadius` | `double?` | - | 水波纹半径 |
| `materialTapTargetSize` | `MaterialTapTargetSize?` | - | 点击区域大小 |
| `visualDensity` | `VisualDensity?` | - | 视觉密度 |
| `focusNode` | `FocusNode?` | - | 焦点节点 |
| `autofocus` | `bool` | `false` | 自动聚焦 |

### 1.4 基础示例

#### 简单单选组

```dart
class SimpleRadio extends StatefulWidget {
  @override
  _SimpleRadioState createState() => _SimpleRadioState();
}

class _SimpleRadioState extends State<SimpleRadio> {
  String _selectedValue = 'option1';

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Radio<String>(
          value: 'option1',
          groupValue: _selectedValue,
          onChanged: (value) {
            setState(() {
              _selectedValue = value!;
            });
          },
        ),
        Radio<String>(
          value: 'option2',
          groupValue: _selectedValue,
          onChanged: (value) {
            setState(() {
              _selectedValue = value!;
            });
          },
        ),
        Radio<String>(
          value: 'option3',
          groupValue: _selectedValue,
          onChanged: (value) {
            setState(() {
              _selectedValue = value!;
            });
          },
        ),
      ],
    );
  }
}
```

#### 带标签的单选

```dart
Row(
  children: [
    Radio<String>(
      value: 'male',
      groupValue: _gender,
      onChanged: (value) {
        setState(() {
          _gender = value;
        });
      },
    ),
    Text('男'),
    SizedBox(width: 20),
    Radio<String>(
      value: 'female',
      groupValue: _gender,
      onChanged: (value) {
        setState(() {
          _gender = value;
        });
      },
    ),
    Text('女'),
  ],
)
```

## 2. RadioListTile

### 2.1 基本用法

```dart
Column(
  children: [
    RadioListTile<String>(
      title: Text('选项一'),
      subtitle: Text('这是选项一的描述'),
      value: 'option1',
      groupValue: _selectedValue,
      onChanged: (value) {
        setState(() {
          _selectedValue = value;
        });
      },
    ),
    RadioListTile<String>(
      title: Text('选项二'),
      subtitle: Text('这是选项二的描述'),
      value: 'option2',
      groupValue: _selectedValue,
      onChanged: (value) {
        setState(() {
          _selectedValue = value;
        });
      },
    ),
    RadioListTile<String>(
      title: Text('选项三'),
      subtitle: Text('这是选项三的描述'),
      value: 'option3',
      groupValue: _selectedValue,
      onChanged: (value) {
        setState(() {
          _selectedValue = value;
        });
      },
    ),
  ],
)
```

### 2.2 RadioListTile 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `title` | `Widget` | 标题 |
| `subtitle` | `Widget?` | 副标题 |
| `secondary` | `Widget?` | 右侧组件 |
| `isThreeLine` | `bool` | 是否三行布局 |
| `dense` | `bool?` | 紧凑模式 |
| `controlAffinity` | `ListTileControlAffinity` | 控件位置 |
| `contentPadding` | `EdgeInsetsGeometry?` | 内边距 |
| `shape` | `ShapeBorder?` | 形状 |
| `tileColor` | `Color?` | 背景色 |
| `selectedTileColor` | `Color?` | 选中背景色 |

### 2.3 带图标的单选项

```dart
RadioListTile<String>(
  title: Text('信用卡支付'),
  secondary: Icon(Icons.credit_card),
  value: 'credit',
  groupValue: _paymentMethod,
  onChanged: (value) {
    setState(() {
      _paymentMethod = value;
    });
  },
)
```

### 2.4 控件位置

```dart
// Radio 在右侧
RadioListTile<String>(
  title: Text('选项'),
  controlAffinity: ListTileControlAffinity.trailing,
  value: 'option1',
  groupValue: _value,
  onChanged: (value) {},
)

// Radio 在左侧（默认）
RadioListTile<String>(
  title: Text('选项'),
  controlAffinity: ListTileControlAffinity.leading,
  value: 'option1',
  groupValue: _value,
  onChanged: (value) {},
)

// Radio 在平台默认位置
RadioListTile<String>(
  title: Text('选项'),
  controlAffinity: ListTileControlAffinity.platform,
  value: 'option1',
  groupValue: _value,
  onChanged: (value) {},
)
```

## 3. 中级用法

### 3.1 自定义颜色

```dart
Radio<String>(
  value: 'option1',
  groupValue: _value,
  activeColor: Colors.red,
  fillColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) {
      return Colors.red;
    }
    return Colors.grey;
  }),
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)
```

### 3.2 禁用状态

```dart
Radio<String>(
  value: 'option1',
  groupValue: _value,
  onChanged: null,  // null 表示禁用
)
```

### 3.3 可取消选中

```dart
Radio<String>(
  value: 'option1',
  groupValue: _value,
  toggleable: true,  // 允许取消选中
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)
```

### 3.4 紧凑布局

```dart
RadioListTile<String>(
  title: Text('紧凑选项'),
  dense: true,
  visualDensity: VisualDensity.compact,
  value: 'option1',
  groupValue: _value,
  onChanged: (value) {},
)
```

## 4. 高级用法

### 4.1 自定义单选组件

```dart
class CustomRadio<T> extends StatelessWidget {
  final T value;
  final T groupValue;
  final String label;
  final ValueChanged<T?> onChanged;
  final Color? activeColor;

  const CustomRadio({
    required this.value,
    required this.groupValue,
    required this.label,
    required this.onChanged,
    this.activeColor,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final isSelected = value == groupValue;
    
    return GestureDetector(
      onTap: () => onChanged(value),
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 16, vertical: 12),
        decoration: BoxDecoration(
          color: isSelected ? (activeColor ?? Colors.blue).withOpacity(0.1) : null,
          border: Border.all(
            color: isSelected ? (activeColor ?? Colors.blue) : Colors.grey,
            width: isSelected ? 2 : 1,
          ),
          borderRadius: BorderRadius.circular(8),
        ),
        child: Text(
          label,
          style: TextStyle(
            color: isSelected ? (activeColor ?? Colors.blue) : Colors.black,
            fontWeight: isSelected ? FontWeight.bold : FontWeight.normal,
          ),
        ),
      ),
    );
  }
}

// 使用
Wrap(
  spacing: 8,
  children: [
    CustomRadio(
      value: 'small',
      groupValue: _size,
      label: '小',
      onChanged: (value) => setState(() => _size = value),
    ),
    CustomRadio(
      value: 'medium',
      groupValue: _size,
      label: '中',
      onChanged: (value) => setState(() => _size = value),
    ),
    CustomRadio(
      value: 'large',
      groupValue: _size,
      label: '大',
      onChanged: (value) => setState(() => _size = value),
    ),
  ],
)
```

### 4.2 单选组构建器

```dart
class RadioGroup<T> extends StatelessWidget {
  final List<RadioOption<T>> options;
  final T? groupValue;
  final ValueChanged<T?>? onChanged;
  final Axis direction;

  const RadioGroup({
    required this.options,
    this.groupValue,
    this.onChanged,
    this.direction = Axis.vertical,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final children = options.map((option) {
      return RadioListTile<T>(
        title: Text(option.label),
        subtitle: option.subtitle != null ? Text(option.subtitle!) : null,
        value: option.value,
        groupValue: groupValue,
        onChanged: onChanged,
      );
    }).toList();

    if (direction == Axis.horizontal) {
      return Row(children: children);
    }
    return Column(children: children);
  }
}

class RadioOption<T> {
  final T value;
  final String label;
  final String? subtitle;

  RadioOption({required this.value, required this.label, this.subtitle});
}

// 使用
RadioGroup<String>(
  options: [
    RadioOption(value: 'apple', label: '苹果'),
    RadioOption(value: 'banana', label: '香蕉', subtitle: '黄色的'),
    RadioOption(value: 'orange', label: '橙子'),
  ],
  groupValue: _fruit,
  onChanged: (value) => setState(() => _fruit = value),
)
```

### 4.3 卡片式单选

```dart
class CardRadio<T> extends StatelessWidget {
  final T value;
  final T groupValue;
  final String title;
  final String? subtitle;
  final IconData? icon;
  final ValueChanged<T?> onChanged;

  const CardRadio({
    required this.value,
    required this.groupValue,
    required this.title,
    this.subtitle,
    this.icon,
    required this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final isSelected = value == groupValue;
    
    return GestureDetector(
      onTap: () => onChanged(value),
      child: Card(
        elevation: isSelected ? 4 : 1,
        color: isSelected ? Colors.blue[50] : null,
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
          side: BorderSide(
            color: isSelected ? Colors.blue : Colors.grey[300]!,
            width: isSelected ? 2 : 1,
          ),
        ),
        child: Padding(
          padding: EdgeInsets.all(16),
          child: Row(
            children: [
              if (icon != null) ...[
                Icon(icon, color: isSelected ? Colors.blue : Colors.grey),
                SizedBox(width: 16),
              ],
              Expanded(
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Text(
                      title,
                      style: TextStyle(
                        fontWeight: isSelected ? FontWeight.bold : FontWeight.normal,
                      ),
                    ),
                    if (subtitle != null)
                      Text(
                        subtitle!,
                        style: TextStyle(color: Colors.grey, fontSize: 12),
                      ),
                  ],
                ),
              ),
              Radio<T>(
                value: value,
                groupValue: groupValue,
                onChanged: onChanged,
                activeColor: Colors.blue,
              ),
            ],
          ),
        ),
      ),
    );
  }
}

// 使用
CardRadio(
  value: 'standard',
  groupValue: _shipping,
  title: '标准配送',
  subtitle: '3-5个工作日送达',
  icon: Icons.local_shipping,
  onChanged: (value) => setState(() => _shipping = value),
)
```

### 4.4 分组单选

```dart
class GroupedRadio extends StatelessWidget {
  final Map<String, List<RadioOption<String>>> groups;
  final String? groupValue;
  final ValueChanged<String?>? onChanged;

  const GroupedRadio({
    required this.groups,
    this.groupValue,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: groups.entries.map((entry) {
        return Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Padding(
              padding: EdgeInsets.all(8),
              child: Text(
                entry.key,
                style: TextStyle(fontWeight: FontWeight.bold, fontSize: 16),
              ),
            ),
            ...entry.value.map((option) {
              return RadioListTile<String>(
                title: Text(option.label),
                value: option.value,
                groupValue: groupValue,
                onChanged: onChanged,
              );
            }),
            Divider(),
          ],
        );
      }).toList(),
    );
  }
}

// 使用
GroupedRadio(
  groups: {
    '水果': [
      RadioOption(value: 'apple', label: '苹果'),
      RadioOption(value: 'banana', label: '香蕉'),
    ],
    '蔬菜': [
      RadioOption(value: 'carrot', label: '胡萝卜'),
      RadioOption(value: 'tomato', label: '西红柿'),
    ],
  },
  groupValue: _food,
  onChanged: (value) => setState(() => _food = value),
)
```

## 5. 常见问题与解决方案

### 问题1：单选框不互斥

```dart
// 确保所有 Radio 使用相同的 groupValue 变量
// ❌ 错误
Radio(value: 'a', groupValue: _value1, onChanged: ...)
Radio(value: 'b', groupValue: _value2, onChanged: ...)  // 不同变量

// ✅ 正确
Radio(value: 'a', groupValue: _value, onChanged: ...)
Radio(value: 'b', groupValue: _value, onChanged: ...)  // 相同变量
```

### 问题2：点击无响应

```dart
// 确保在 setState 中更新状态
Radio(
  value: 'a',
  groupValue: _value,
  onChanged: (value) {
    setState(() {  // 必须调用 setState
      _value = value;
    });
  },
)
```

### 问题3：禁用后样式

```dart
Radio(
  value: 'a',
  groupValue: _value,
  onChanged: isEnabled ? (value) {
    setState(() => _value = value);
  } : null,  // 禁用时为 null
)
```

## 6. Radio vs Checkbox

| 特性 | Radio | Checkbox |
|------|-------|----------|
| 选择方式 | 单选 | 多选 |
| 互斥性 | 同组互斥 | 独立选择 |
| 初始状态 | 通常有默认选中 | 可全不选 |

```dart
// Radio - 单选
Radio(value: 'a', groupValue: _value, onChanged: ...)

// Checkbox - 多选
Checkbox(value: _checked1, onChanged: ...)
Checkbox(value: _checked2, onChanged: ...)
```

## 总结

Radio 是 Flutter 中单选功能的核心组件：

**核心要点**：
1. 同一组的 Radio 共享 groupValue
2. 使用 RadioListTile 获得更好的体验
3. onChanged 回调更新选中状态
4. 支持 Material 3 新样式

**常用属性**：
- `value` - 当前项的值
- `groupValue` - 分组的选中值
- `onChanged` - 选中回调
- `activeColor` - 选中颜色

**最佳实践**：
1. 使用 RadioListTile 提供完整交互
2. 合理设置默认选中值
3. 为长列表提供滚动支持
4. 禁用时提供视觉反馈

---

*最后更新: 2026-02-19*

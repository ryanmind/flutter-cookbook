# Flutter 选择组件用法详解

## 简介

Flutter 提供了多种选择组件：Checkbox（复选框）、Radio（单选框）和 Switch（开关），用于收集用户的选择信息。

## 1. Checkbox 复选框

### 1.1 基本概念

Checkbox 的核心特点：
- 多选场景使用
- 三种状态：选中、未选中、不确定
- 支持自定义颜色和形状
- 通常配合 ListTile 使用

### 1.2 基本语法

```dart
Checkbox(
  value: true,
  onChanged: (value) {},
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `value` | `bool?` | 当前值 |
| `onChanged` | `ValueChanged<bool?>?` | 值变化回调 |
| `activeColor` | `Color?` | 选中颜色 |
| `checkColor` | `Color?` | 勾选颜色 |
| `fillColor` | `WidgetStateProperty<Color?>?` | 填充颜色 |
| `overlayColor` | `WidgetStateProperty<Color?>?` | 覆盖层颜色 |
| `tristate` | `bool` | 是否支持三态 |
| `shape` | `OutlinedBorder?` | 形状 |
| `side` | `BorderSide?` | 边框样式 |
| `isError` | `bool` | 是否显示错误状态 |

### 1.4 基础示例

#### 简单复选框

```dart
class SimpleCheckbox extends StatefulWidget {
  @override
  _SimpleCheckboxState createState() => _SimpleCheckboxState();
}

class _SimpleCheckboxState extends State<SimpleCheckbox> {
  bool _isChecked = false;

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Checkbox(
          value: _isChecked,
          onChanged: (value) {
            setState(() {
              _isChecked = value ?? false;
            });
          },
        ),
        Text('同意用户协议'),
      ],
    );
  }
}
```

#### 三态复选框

```dart
class TristateCheckbox extends StatefulWidget {
  @override
  _TristateCheckboxState createState() => _TristateCheckboxState();
}

class _TristateCheckboxState extends State<TristateCheckbox> {
  bool? _value; // null = 不确定状态

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Checkbox(
          tristate: true,
          value: _value,
          onChanged: (value) {
            setState(() {
              _value = value;
            });
          },
        ),
        Text('状态: ${_value == null ? "不确定" : _value! ? "选中" : "未选中"}'),
      ],
    );
  }
}
```

#### 自定义样式

```dart
Checkbox(
  value: true,
  activeColor: Colors.blue,
  checkColor: Colors.white,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(4),
  ),
  onChanged: (value) {},
)
```

#### CheckboxListTile

```dart
CheckboxListTile(
  title: Text('接收推送通知'),
  subtitle: Text('开启后将收到最新消息推送'),
  value: _notifications,
  onChanged: (value) {
    setState(() {
      _notifications = value ?? false;
    });
  },
  secondary: Icon(Icons.notifications),
)
```

### 1.5 多选列表

```dart
class MultiSelectList extends StatefulWidget {
  final List<String> items;

  const MultiSelectList({required this.items, Key? key}) : super(key: key);

  @override
  _MultiSelectListState createState() => _MultiSelectListState();
}

class _MultiSelectListState extends State<MultiSelectList> {
  final Set<String> _selectedItems = {};

  @override
  Widget build(BuildContext context) {
    return Column(
      children: widget.items.map((item) {
        return CheckboxListTile(
          title: Text(item),
          value: _selectedItems.contains(item),
          onChanged: (selected) {
            setState(() {
              if (selected == true) {
                _selectedItems.add(item);
              } else {
                _selectedItems.remove(item);
              }
            });
          },
        );
      }).toList(),
    );
  }
}
```

## 2. Radio 单选框

### 2.1 基本概念

Radio 的核心特点：
- 单选场景使用
- 同一组使用相同的 groupValue
- value 表示当前选项的值
- 通常配合 ListTile 使用

### 2.2 基本语法

```dart
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

### 2.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `value` | `T` | 当前 Radio 的值（必需） |
| `groupValue` | `T?` | 当前分组的选中值 |
| `onChanged` | `ValueChanged<T?>?` | 选中回调 |
| `toggleable` | `bool` | 是否可取消选中 |
| `activeColor` | `Color?` | 选中颜色 |
| `fillColor` | `Color?` | 填充颜色 |
| `overlayColor` | `WidgetStateProperty<Color?>?` | 覆盖层颜色 |
| `visualDensity` | `VisualDensity?` | 视觉密度 |
| `toggleable` | `bool` | 是否可取消选中 |
| `visualDensity` | `VisualDensity?` | 视觉密度 |

### 2.4 基础示例

#### 简单单选组

```dart
class SimpleRadioGroup extends StatefulWidget {
  @override
  _SimpleRadioGroupState createState() => _SimpleRadioGroupState();
}

class _SimpleRadioGroupState extends State<SimpleRadioGroup> {
  String? _selectedGender;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        RadioListTile<String>(
          title: Text('男'),
          value: 'male',
          groupValue: _selectedGender,
          onChanged: (value) {
            setState(() {
              _selectedGender = value;
            });
          },
        ),
        RadioListTile<String>(
          title: Text('女'),
          value: 'female',
          groupValue: _selectedGender,
          onChanged: (value) {
            setState(() {
              _selectedGender = value;
            });
          },
        ),
      ],
    );
  }
}
```

#### 水平单选组

```dart
Row(
  children: ['选项1', '选项2', '选项3'].map((option) {
    return Row(
      mainAxisSize: MainAxisSize.min,
      children: [
        Radio<String>(
          value: option,
          groupValue: _selectedOption,
          onChanged: (value) {
            setState(() {
              _selectedOption = value;
            });
          },
        ),
        Text(option),
      ],
    );
  }).toList(),
)
```

#### 自定义颜色

```dart
Radio<String>(
  value: 'option',
  groupValue: _selectedValue,
  activeColor: Colors.blue,
  onChanged: (value) {},
)
```

#### 带副标题的单选项

```dart
RadioListTile<String>(
  title: Text('标准会员'),
  subtitle: Text('¥99/月，包含基础功能'),
  value: 'standard',
  groupValue: _membership,
  onChanged: (value) {
    setState(() {
      _membership = value;
    });
  },
)
```

### 2.5 单选表单

```dart
class RadioForm extends StatefulWidget {
  @override
  _RadioFormState createState() => _RadioFormState();
}

class _RadioFormState extends State<RadioForm> {
  String _size = 'M';
  String _color = 'blue';

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('尺寸', style: TextStyle(fontWeight: FontWeight.bold)),
        ...['S', 'M', 'L', 'XL'].map((size) {
          return RadioListTile<String>(
            title: Text(size),
            value: size,
            groupValue: _size,
            onChanged: (value) {
              setState(() => _size = value!);
            },
          );
        }),
        SizedBox(height: 16),
        Text('颜色', style: TextStyle(fontWeight: FontWeight.bold)),
        Wrap(
          children: [
            {'blue': '蓝色'},
            {'red': '红色'},
            {'green': '绿色'},
          ].map((color) {
            final key = color.keys.first;
            return RadioListTile<String>(
              title: Text(color[key]!),
              value: key,
              groupValue: _color,
              onChanged: (value) {
                setState(() => _color = value!);
              },
            );
          }).toList(),
        ),
      ],
    );
  }
}
```

## 3. Switch 开关

### 3.1 基本概念

Switch 的核心特点：
- 开关状态切换
- Material 3 支持多种样式
- 通常用于设置开关
- 配合 ListTile 使用

### 3.2 基本语法

```dart
Switch(
  value: true,
  onChanged: (value) {},
)
```

### 3.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `value` | `bool` | 当前值（必需） |
| `onChanged` | `ValueChanged<bool>?` | 值变化回调 |
| `activeColor` | `Color?` | 开启时滑块颜色 |
| `activeTrackColor` | `Color?` | 开启时轨道颜色 |
| `inactiveThumbColor` | `Color?` | 关闭时滑块颜色 |
| `inactiveTrackColor` | `Color?` | 关闭时轨道颜色 |
| `thumbColor` | `WidgetStateProperty<Color?>?` | 滑块颜色 |
| `trackColor` | `WidgetStateProperty<Color?>?` | 轨道颜色 |
| `trackOutlineColor` | `WidgetStateProperty<Color?>?` | 轨道边框颜色 |
| `thumbIcon` | `WidgetStateProperty<Icon?>?` | 滑块图标 |

### 3.4 基础示例

#### 简单开关

```dart
class SimpleSwitch extends StatefulWidget {
  @override
  _SimpleSwitchState createState() => _SimpleSwitchState();
}

class _SimpleSwitchState extends State<SimpleSwitch> {
  bool _isOn = false;

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Text('开启推送'),
        Switch(
          value: _isOn,
          onChanged: (value) {
            setState(() {
              _isOn = value;
            });
          },
        ),
      ],
    );
  }
}
```

#### SwitchListTile

```dart
SwitchListTile(
  title: Text('深色模式'),
  subtitle: Text('开启后将使用深色主题'),
  value: _darkMode,
  onChanged: (value) {
    setState(() {
      _darkMode = value;
    });
  },
  secondary: Icon(Icons.dark_mode),
)
```

#### 自定义颜色

```dart
Switch(
  value: true,
  activeColor: Colors.green,
  activeTrackColor: Colors.green[200],
  inactiveThumbColor: Colors.grey,
  inactiveTrackColor: Colors.grey[300],
  onChanged: (value) {},
)
```

#### Material 3 Switch 样式

```dart
// Material 3 默认样式
Switch(
  value: true,
  onChanged: (value) {},
)

// 带图标的开关
Switch(
  value: true,
  thumbIcon: WidgetStateProperty.resolveWith<Icon?>((states) {
    if (states.contains(WidgetState.selected)) {
      return Icon(Icons.check, color: Colors.white, size: 16);
    }
    return Icon(Icons.close, size: 16);
  }),
  onChanged: (value) {},
)
```

### 3.5 设置页面示例

```dart
class SettingsPage extends StatefulWidget {
  @override
  _SettingsPageState createState() => _SettingsPageState();
}

class _SettingsPageState extends State<SettingsPage> {
  bool _notifications = true;
  bool _sound = true;
  bool _vibration = false;
  bool _darkMode = false;
  bool _autoUpdate = true;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('设置')),
      body: ListView(
        children: [
          _buildSectionHeader('通知'),
          SwitchListTile(
            title: Text('推送通知'),
            subtitle: Text('接收新消息推送'),
            value: _notifications,
            onChanged: (value) => setState(() => _notifications = value),
          ),
          SwitchListTile(
            title: Text('声音'),
            subtitle: Text('消息提示音'),
            value: _sound,
            onChanged: (value) => setState(() => _sound = value),
          ),
          SwitchListTile(
            title: Text('震动'),
            subtitle: Text('消息震动提醒'),
            value: _vibration,
            onChanged: (value) => setState(() => _vibration = value),
          ),
          Divider(),
          _buildSectionHeader('显示'),
          SwitchListTile(
            title: Text('深色模式'),
            value: _darkMode,
            onChanged: (value) => setState(() => _darkMode = value),
          ),
          Divider(),
          _buildSectionHeader('其他'),
          SwitchListTile(
            title: Text('自动更新'),
            subtitle: Text('WIFI 下自动更新应用'),
            value: _autoUpdate,
            onChanged: (value) => setState(() => _autoUpdate = value),
          ),
        ],
      ),
    );
  }

  Widget _buildSectionHeader(String title) {
    return Padding(
      padding: EdgeInsets.fromLTRB(16, 16, 16, 8),
      child: Text(
        title,
        style: TextStyle(
          color: Colors.blue,
          fontWeight: FontWeight.bold,
        ),
      ),
    );
  }
}
```

## 4. 高级用法

### 4.1 自定义选择组件

```dart
class CustomCheckbox extends StatelessWidget {
  final bool value;
  final ValueChanged<bool>? onChanged;
  final String label;
  final Color activeColor;

  const CustomCheckbox({
    required this.value,
    this.onChanged,
    required this.label,
    this.activeColor = Colors.blue,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: onChanged != null ? () => onChanged!(!value) : null,
      child: Row(
        mainAxisSize: MainAxisSize.min,
        children: [
          AnimatedContainer(
            duration: Duration(milliseconds: 200),
            width: 24,
            height: 24,
            decoration: BoxDecoration(
              color: value ? activeColor : Colors.transparent,
              borderRadius: BorderRadius.circular(4),
              border: Border.all(
                color: value ? activeColor : Colors.grey,
                width: 2,
              ),
            ),
            child: value
                ? Icon(Icons.check, size: 18, color: Colors.white)
                : null,
          ),
          SizedBox(width: 8),
          Text(label),
        ],
      ),
    );
  }
}
```

### 4.2 自定义开关组件

```dart
class CustomSwitch extends StatelessWidget {
  final bool value;
  final ValueChanged<bool>? onChanged;
  final Color activeColor;
  final Color inactiveColor;

  const CustomSwitch({
    required this.value,
    this.onChanged,
    this.activeColor = Colors.green,
    this.inactiveColor = Colors.grey,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onChanged != null ? () => onChanged!(!value) : null,
      child: AnimatedContainer(
        duration: Duration(milliseconds: 200),
        width: 50,
        height: 28,
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(14),
          color: value ? activeColor : inactiveColor,
        ),
        child: AnimatedAlign(
          duration: Duration(milliseconds: 200),
          alignment: value ? Alignment.centerRight : Alignment.centerLeft,
          child: Container(
            width: 24,
            height: 24,
            margin: EdgeInsets.symmetric(horizontal: 2),
            decoration: BoxDecoration(
              shape: BoxShape.circle,
              color: Colors.white,
              boxShadow: [
                BoxShadow(color: Colors.black26, blurRadius: 2),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

### 4.3 选择器对话框

```dart
Future<T?> showSelectionDialog<T>({
  required BuildContext context,
  required String title,
  required List<T> options,
  required String Function(T) labelBuilder,
  required T selectedValue,
}) async {
  return showDialog<T>(
    context: context,
    builder: (context) {
      return StatefulBuilder(
        builder: (context, setState) {
          return AlertDialog(
            title: Text(title),
            content: SingleChildScrollView(
              child: Column(
                mainAxisSize: MainAxisSize.min,
                children: options.map((option) {
                  return RadioListTile<T>(
                    title: Text(labelBuilder(option)),
                    value: option,
                    groupValue: selectedValue,
                    onChanged: (value) {
                      Navigator.pop(context, value);
                    },
                  );
                }).toList(),
              ),
            ),
          );
        },
      );
    },
  );
}

// 使用
final selected = await showSelectionDialog(
  context: context,
  title: '选择城市',
  options: ['北京', '上海', '广州', '深圳'],
  labelBuilder: (city) => city,
  selectedValue: currentCity,
);
```

## 5. 常见问题与解决方案

### 问题1：Checkbox 点击区域太小

```dart
// 使用 CheckboxListTile 扩大点击区域
CheckboxListTile(
  title: Text('选项'),
  value: _checked,
  onChanged: (value) {},
)

// 或使用 InkWell 包裹
InkWell(
  onTap: () {
    setState(() => _checked = !_checked);
  },
  child: Row(
    children: [
      Checkbox(value: _checked, onChanged: null),
      Text('选项'),
    ],
  ),
)
```

### 问题2：Radio 无法取消选中

```dart
// 使用 toggleable 属性
Radio<String>(
  value: 'option',
  groupValue: _selectedValue,
  toggleable: true,  // 允许取消选中
  onChanged: (value) {
    setState(() {
      _selectedValue = value;
    });
  },
)
```

### 问题3：Switch 样式不一致

```dart
// 使用主题统一样式
SwitchTheme(
  data: SwitchThemeData(
    thumbColor: WidgetStateProperty.resolveWith((states) {
      if (states.contains(WidgetState.selected)) {
        return Colors.white;
      }
      return Colors.grey;
    }),
  ),
  child: Switch(value: true, onChanged: (value) {}),
)
```

### 问题4：选择组件禁用状态

```dart
// onChanged 为 null 时自动禁用
Checkbox(
  value: true,
  onChanged: null,  // 禁用状态
)
```

### 问题5：多组 Radio 冲突

```dart
// 使用不同的类型或 groupValue 隔离
// 组1
Radio<int>(value: 1, groupValue: _group1, onChanged: ...)

// 组2
Radio<String>(value: 'a', groupValue: _group2, onChanged: ...)

// 或使用不同的值类型
Radio<Enum1>(...)
Radio<Enum2>(...)
```

## 6. 最佳实践

### 6.1 封装设置项

```dart
class SettingItem {
  final String title;
  final String? subtitle;
  final IconData? icon;
  final bool value;
  final ValueChanged<bool>? onChanged;

  const SettingItem({
    required this.title,
    this.subtitle,
    this.icon,
    required this.value,
    this.onChanged,
  });
}

Widget buildSettingItem(SettingItem item) {
  return SwitchListTile(
    title: Text(item.title),
    subtitle: item.subtitle != null ? Text(item.subtitle!) : null,
    secondary: item.icon != null ? Icon(item.icon) : null,
    value: item.value,
    onChanged: item.onChanged,
  );
}
```

### 6.2 使用枚举

```dart
enum PaymentMethod { alipay, wechat, bank }

class PaymentSelector extends StatefulWidget {
  @override
  _PaymentSelectorState createState() => _PaymentSelectorState();
}

class _PaymentSelectorState extends State<PaymentSelector> {
  PaymentMethod _method = PaymentMethod.alipay;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        RadioListTile<PaymentMethod>(
          title: Text('支付宝'),
          value: PaymentMethod.alipay,
          groupValue: _method,
          onChanged: (value) => setState(() => _method = value!),
        ),
        RadioListTile<PaymentMethod>(
          title: Text('微信支付'),
          value: PaymentMethod.wechat,
          groupValue: _method,
          onChanged: (value) => setState(() => _method = value!),
        ),
        RadioListTile<PaymentMethod>(
          title: Text('银行卡'),
          value: PaymentMethod.bank,
          groupValue: _method,
          onChanged: (value) => setState(() => _method = value!),
        ),
      ],
    );
  }
}
```

## 总结

Flutter 选择组件提供了丰富的用户交互方式：

**Checkbox 复选框**：
- 用于多选场景
- 支持 tristate 三态模式
- 配合 CheckboxListTile 使用

**Radio 单选框**：
- 用于单选场景
- 同一组使用相同 groupValue
- 配合 RadioListTile 使用

**Switch 开关**：
- 用于开关设置
- Material 3 新增 thumbIcon 属性
- 配合 SwitchListTile 使用

**最佳实践**：
1. 使用 ListTile 变体扩大点击区域
2. 使用枚举提高代码可读性
3. 合理组织分组和布局
4. 统一样式配置

---

*最后更新: 2026-02-18*

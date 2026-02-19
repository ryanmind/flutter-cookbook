# Flutter Switch 组件用法详解

## 简介

Switch 是开关组件，用于在两种状态之间切换。常用于设置页面、功能开关等场景。Material 3 中 Switch 有了全新的设计风格。

## 1. 初级用法

### 1.1 基本概念

Switch 的核心特点：
- 双状态切换（开/关）
- Material 3 新样式
- 支持自定义颜色
- 支持图标和标签

### 1.2 基本语法

```dart
// 最简单的用法
Switch(
  value: _isEnabled,
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `value` | `bool` | - | 当前值（必需） |
| `onChanged` | `ValueChanged<bool>?` | - | 值变化回调 |
| `activeColor` | `Color?` | - | 开启时滑块颜色 |
| `activeTrackColor` | `Color?` | - | 开启时轨道颜色 |
| `inactiveThumbColor` | `Color?` | - | 关闭时滑块颜色 |
| `inactiveTrackColor` | `Color?` | - | 关闭时轨道颜色 |
| `activeThumbImage` | `ImageProvider?` | - | 开启时滑块图片 |
| `inactiveThumbImage` | `ImageProvider?` | - | 关闭时滑块图片 |
| `thumbColor` | `WidgetStateProperty<Color?>?` | - | 滑块颜色 |
| `trackColor` | `WidgetStateProperty<Color?>?` | - | 轨道颜色 |
| `trackOutlineColor` | `WidgetStateProperty<Color?>?` | - | 轨道边框颜色 |
| `thumbIcon` | `WidgetStateProperty<Icon?>?` | - | 滑块图标 |
| `splashRadius` | `double?` | - | 水波纹半径 |
| `materialTapTargetSize` | `MaterialTapTargetSize?` | - | 点击区域大小 |
| `dragStartBehavior` | `DragStartBehavior` | - | 拖动行为 |
| `mouseCursor` | `MouseCursor?` | - | 鼠标样式 |
| `focusNode` | `FocusNode?` | - | 焦点节点 |
| `onFocusChange` | `ValueChanged<bool>?` | - | 焦点变化回调 |
| `autofocus` | `bool` | `false` | 自动聚焦 |

### 1.4 基础示例

#### 基本开关

```dart
class SimpleSwitch extends StatefulWidget {
  @override
  _SimpleSwitchState createState() => _SimpleSwitchState();
}

class _SimpleSwitchState extends State<SimpleSwitch> {
  bool _isEnabled = false;

  @override
  Widget build(BuildContext context) {
    return Switch(
      value: _isEnabled,
      onChanged: (value) {
        setState(() {
          _isEnabled = value;
        });
      },
    );
  }
}
```

#### 自定义颜色

```dart
Switch(
  value: _isEnabled,
  activeColor: Colors.white,
  activeTrackColor: Colors.green,
  inactiveThumbColor: Colors.white,
  inactiveTrackColor: Colors.grey,
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

#### 禁用状态

```dart
Switch(
  value: _isEnabled,
  onChanged: null,  // null 表示禁用
)
```

## 2. SwitchListTile

### 2.1 基本用法

```dart
SwitchListTile(
  title: Text('通知'),
  subtitle: Text('接收应用推送通知'),
  value: _notificationsEnabled,
  onChanged: (value) {
    setState(() {
      _notificationsEnabled = value;
    });
  },
)
```

### 2.2 SwitchListTile 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `title` | `Widget` | 标题 |
| `subtitle` | `Widget?` | 副标题 |
| `secondary` | `Widget?` | 左侧组件 |
| `isThreeLine` | `bool` | 是否三行布局 |
| `dense` | `bool?` | 紧凑模式 |
| `contentPadding` | `EdgeInsetsGeometry?` | 内边距 |
| `shape` | `ShapeBorder?` | 形状 |
| `tileColor` | `Color?` | 背景色 |
| `selectedTileColor` | `Color?` | 选中背景色 |

### 2.3 完整示例

```dart
Column(
  children: [
    SwitchListTile(
      title: Text('WiFi'),
      subtitle: Text('连接无线网络'),
      secondary: Icon(Icons.wifi),
      value: _wifiEnabled,
      onChanged: (value) {
        setState(() {
          _wifiEnabled = value;
        });
      },
    ),
    SwitchListTile(
      title: Text('蓝牙'),
      subtitle: Text('连接蓝牙设备'),
      secondary: Icon(Icons.bluetooth),
      value: _bluetoothEnabled,
      onChanged: (value) {
        setState(() {
          _bluetoothEnabled = value;
        });
      },
    ),
    SwitchListTile(
      title: Text('飞行模式'),
      subtitle: Text('禁用所有无线连接'),
      secondary: Icon(Icons.airplanemode_active),
      value: _airplaneMode,
      onChanged: (value) {
        setState(() {
          _airplaneMode = value;
        });
      },
    ),
  ],
)
```

## 3. 中级用法

### 3.1 带图标的开关

```dart
// Material 3 样式带图标
Switch(
  value: _isEnabled,
  thumbIcon: WidgetStateProperty.resolveWith<Icon?>((states) {
    if (states.contains(WidgetState.selected)) {
      return Icon(Icons.check, color: Colors.white, size: 16);
    }
    return Icon(Icons.close, color: Colors.grey, size: 16);
  }),
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

### 3.2 自定义轨道样式

```dart
Switch(
  value: _isEnabled,
  trackColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) {
      return Colors.green.withValues(alpha: 0.5);
    }
    return Colors.grey.withValues(alpha: 0.3);
  }),
  trackOutlineColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) {
      return Colors.green;
    }
    return Colors.grey;
  }),
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

### 3.3 带图片的开关

```dart
Switch(
  value: _isEnabled,
  activeThumbImage: AssetImage('images/switch_on.png'),
  inactiveThumbImage: AssetImage('images/switch_off.png'),
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

### 3.4 紧凑模式

```dart
SwitchListTile(
  title: Text('紧凑开关'),
  dense: true,
  visualDensity: VisualDensity.compact,
  value: _isEnabled,
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

## 4. 高级用法

### 4.1 自定义开关组件

```dart
class CustomSwitch extends StatelessWidget {
  final bool value;
  final ValueChanged<bool> onChanged;
  final Color activeColor;
  final Color inactiveColor;

  const CustomSwitch({
    required this.value,
    required this.onChanged,
    this.activeColor = Colors.green,
    this.inactiveColor = Colors.grey,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => onChanged(!value),
      child: AnimatedContainer(
        duration: Duration(milliseconds: 200),
        width: 56,
        height: 32,
        decoration: BoxDecoration(
          color: value ? activeColor : inactiveColor,
          borderRadius: BorderRadius.circular(16),
        ),
        child: AnimatedAlign(
          duration: Duration(milliseconds: 200),
          alignment: value ? Alignment.centerRight : Alignment.centerLeft,
          child: Container(
            width: 28,
            height: 28,
            margin: EdgeInsets.symmetric(horizontal: 2),
            decoration: BoxDecoration(
              color: Colors.white,
              shape: BoxShape.circle,
              boxShadow: [
                BoxShadow(
                  color: Colors.black12,
                  blurRadius: 4,
                  offset: Offset(0, 2),
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}

// 使用
CustomSwitch(
  value: _isEnabled,
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

### 4.2 带标签的开关

```dart
class LabeledSwitch extends StatelessWidget {
  final bool value;
  final String activeLabel;
  final String inactiveLabel;
  final ValueChanged<bool> onChanged;

  const LabeledSwitch({
    required this.value,
    this.activeLabel = 'ON',
    this.inactiveLabel = 'OFF',
    required this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => onChanged(!value),
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 4),
        decoration: BoxDecoration(
          color: value ? Colors.green : Colors.grey[300],
          borderRadius: BorderRadius.circular(20),
        ),
        child: Row(
          mainAxisSize: MainAxisSize.min,
          children: [
            AnimatedOpacity(
              duration: Duration(milliseconds: 200),
              opacity: value ? 1 : 0,
              child: Padding(
                padding: EdgeInsets.symmetric(horizontal: 8),
                child: Text(activeLabel, style: TextStyle(color: Colors.white, fontSize: 12)),
              ),
            ),
            Container(
              width: 24,
              height: 24,
              decoration: BoxDecoration(
                color: Colors.white,
                shape: BoxShape.circle,
              ),
            ),
            AnimatedOpacity(
              duration: Duration(milliseconds: 200),
              opacity: value ? 0 : 1,
              child: Padding(
                padding: EdgeInsets.symmetric(horizontal: 8),
                child: Text(inactiveLabel, style: TextStyle(color: Colors.grey, fontSize: 12)),
              ),
            ),
          ],
        ),
      ),
    );
  }
}

// 使用
LabeledSwitch(
  value: _isEnabled,
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

### 4.3 确认开关

```dart
class ConfirmSwitch extends StatefulWidget {
  final bool value;
  final String confirmTitle;
  final String confirmContent;
  final ValueChanged<bool> onChanged;

  const ConfirmSwitch({
    required this.value,
    this.confirmTitle = '确认切换',
    this.confirmContent = '确定要切换状态吗？',
    required this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _ConfirmSwitchState createState() => _ConfirmSwitchState();
}

class _ConfirmSwitchState extends State<ConfirmSwitch> {
  @override
  Widget build(BuildContext context) {
    return Switch(
      value: widget.value,
      onChanged: (value) async {
        final confirmed = await showDialog<bool>(
          context: context,
          builder: (context) => AlertDialog(
            title: Text(widget.confirmTitle),
            content: Text(widget.confirmContent),
            actions: [
              TextButton(
                onPressed: () => Navigator.pop(context, false),
                child: Text('取消'),
              ),
              TextButton(
                onPressed: () => Navigator.pop(context, true),
                child: Text('确定'),
              ),
            ],
          ),
        );
        
        if (confirmed == true) {
          widget.onChanged(value);
        }
      },
    );
  }
}

// 使用
ConfirmSwitch(
  value: _dangerousEnabled,
  confirmTitle: '警告',
  confirmContent: '此操作可能会影响系统稳定性，确定继续吗？',
  onChanged: (value) {
    setState(() {
      _dangerousEnabled = value;
    });
  },
)
```

### 4.4 设置页面开关组

```dart
class SettingsSwitchGroup extends StatelessWidget {
  final List<SettingItem> items;

  const SettingsSwitchGroup({
    required this.items,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: EdgeInsets.all(16),
      child: Column(
        children: items.asMap().entries.map((entry) {
          final index = entry.key;
          final item = entry.value;
          
          return Column(
            children: [
              SwitchListTile(
                title: Text(item.title),
                subtitle: item.subtitle != null ? Text(item.subtitle!) : null,
                secondary: item.icon != null ? Icon(item.icon) : null,
                value: item.value,
                onChanged: item.onChanged,
              ),
              if (index < items.length - 1) Divider(height: 1),
            ],
          );
        }).toList(),
      ),
    );
  }
}

class SettingItem {
  final String title;
  final String? subtitle;
  final IconData? icon;
  final bool value;
  final ValueChanged<bool>? onChanged;

  SettingItem({
    required this.title,
    this.subtitle,
    this.icon,
    required this.value,
    this.onChanged,
  });
}

// 使用
SettingsSwitchGroup(
  items: [
    SettingItem(
      title: '通知',
      subtitle: '接收推送通知',
      icon: Icons.notifications,
      value: _notifications,
      onChanged: (v) => setState(() => _notifications = v),
    ),
    SettingItem(
      title: '深色模式',
      subtitle: '切换深色主题',
      icon: Icons.dark_mode,
      value: _darkMode,
      onChanged: (v) => setState(() => _darkMode = v),
    ),
    SettingItem(
      title: '自动同步',
      icon: Icons.sync,
      value: _autoSync,
      onChanged: (v) => setState(() => _autoSync = v),
    ),
  ],
)
```

### 4.5 iOS 风格开关

```dart
// 使用 CupertinoSwitch
CupertinoSwitch(
  value: _isEnabled,
  activeColor: Colors.green,
  onChanged: (value) {
    setState(() {
      _isEnabled = value;
    });
  },
)
```

## 5. 常见问题与解决方案

### 问题1：开关颜色不生效

```dart
// 使用 WidgetStateProperty 进行细粒度控制
Switch(
  value: _value,
  thumbColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) {
      return Colors.green;
    }
    return Colors.grey;
  }),
  onChanged: (value) {},
)
```

### 问题2：禁用时样式

```dart
Switch(
  value: _value,
  thumbColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.disabled)) {
      return Colors.grey[300];
    }
    if (states.contains(WidgetState.selected)) {
      return Colors.green;
    }
    return Colors.white;
  }),
  onChanged: enabled ? (value) {} : null,
)
```

### 问题3：SwitchListTile 点击区域

```dart
SwitchListTile(
  title: Text('标题'),
  value: _value,
  onChanged: (value) {},  // 整个区域都可点击
)
```

## 6. Switch vs Checkbox

| 特性 | Switch | Checkbox |
|------|--------|----------|
| 视觉样式 | 滑动开关 | 打钩方框 |
| 使用场景 | 即时生效设置 | 表单多选 |
| 状态表示 | 更直观 | 传统样式 |

```dart
// Switch - 即时设置
Switch(value: _darkMode, onChanged: ...)

// Checkbox - 表单选项
Checkbox(value: _agreed, onChanged: ...)
```

## 总结

Switch 是 Flutter 中双状态切换的核心组件：

**核心要点**：
1. 用于开/关两种状态切换
2. 使用 SwitchListTile 提供完整交互
3. Material 3 支持滑块图标
4. CupertinoSwitch 提供 iOS 风格

**常用属性**：
- `value` - 当前值
- `onChanged` - 值变化回调
- `activeColor` - 开启时滑块颜色
- `activeTrackColor` - 开启时轨道颜色

**最佳实践**：
1. 设置页面使用 SwitchListTile
2. 即时生效的功能用 Switch
3. 提供清晰的状态反馈
4. 禁用时提供视觉提示

---

*最后更新: 2026-02-19*

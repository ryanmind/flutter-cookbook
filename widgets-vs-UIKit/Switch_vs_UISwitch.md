# Switch vs UISwitch 对比学习

本文档记录 Flutter Switch 组件与 UIKit UISwitch 的对比学习要点。

---

## 1. Switch 核心概念

### Switch 是什么

Switch 是 Flutter 中的开关组件，用于表示开/关两种状态，类似于 UIKit 的 UISwitch。

- 支持开/关两种状态
- 支持自定义颜色和样式
- 支持禁用状态
- Material 3 提供多种变体

### Switch vs UISwitch 概念对比

| Flutter Switch | UIKit UISwitch | 说明 |
|----------------|----------------|------|
| `value` | `isOn` | 当前状态 |
| `onChanged` | `addTarget` | 值变化回调 |
| `activeColor` | `onTintColor` | 开启状态颜色 |
| `activeTrackColor` | `onTintColor` | 开启轨道颜色 |
| `inactiveThumbColor` | `thumbTintColor` | 关闭状态滑块颜色 |
| `inactiveTrackColor` | 无直接对应 | 关闭轨道颜色 |
| `onChanged: null` | `enabled = NO` | 禁用状态 |

---

## 2. 源码分析

### Switch 类定义

```dart
class Switch extends StatelessWidget {
  const Switch({
    super.key,
    required this.value,              // 当前值
    required this.onChanged,          // 值变化回调
    this.activeColor,                 // 开启滑块颜色
    this.activeTrackColor,            // 开启轨道颜色
    this.inactiveThumbColor,          // 关闭滑块颜色
    this.inactiveTrackColor,          // 关闭轨道颜色
    this.activeThumbImage,            // 开启滑块图片
    this.inactiveThumbImage,          // 关闭滑块图片
    this.thumbColor,                  // 滑块颜色（WidgetState）
    this.trackColor,                  // 轨道颜色（WidgetState）
    this.trackOutlineColor,           // 轨道边框颜色
    this.splashRadius,                // 点击涟漪半径
    this.focusNode,                   // 焦点节点
    this.onFocusChange,               // 焦点变化回调
    this.autofocus = false,           // 自动获取焦点
    this.mouseCursor,                 // 鼠标样式
    this.applyCupertinoTheme,         // 应用 Cupertino 主题
  });
}

// Material 3 变体
Switch.adaptive()   // 根据平台自适应
```

### SwitchListTile

```dart
// 带标签的开关
class SwitchListTile extends StatelessWidget {
  const SwitchListTile({
    super.key,
    required this.value,
    required this.onChanged,
    this.title,           // 标题
    this.subtitle,        // 副标题
    this.secondary,       // 左侧图标
    this.isThreeLine = false,
    this.dense,
    this.contentPadding,
    // ...
  });
}
```

---

## 3. 与 UISwitch 详细对比

### 3.1 基础开关

```objc
// Objective-C - UISwitch
UISwitch *switch = [[UISwitch alloc] init];
switch.on = YES;
[switch addTarget:self action:@selector(switchChanged:) forControlEvents:UIControlEventValueChanged];
[self.view addSubview:switch];

- (void)switchChanged:(UISwitch *)sender {
    NSLog(@"Switch is: %@", sender.on ? @"ON" : @"OFF");
}
```

```dart
// Flutter - Switch
bool _isOn = true;

Switch(
  value: _isOn,
  onChanged: (value) {
    setState(() {
      _isOn = value;
    });
    print('Switch is: ${_isOn ? "ON" : "OFF"}');
  },
)
```

### 3.2 自定义颜色

```objc
// Objective-C
switch.onTintColor = [UIColor blueColor];     // 开启状态轨道颜色
switch.thumbTintColor = [UIColor whiteColor]; // 滑块颜色
switch.tintColor = [UIColor grayColor];       // 关闭状态轨道颜色
```

```dart
// Flutter
Switch(
  value: _isOn,
  onChanged: (value) {},
  activeColor: Colors.blue,              // 开启滑块颜色
  activeTrackColor: Colors.blue.withValues(alpha: 0.5),  // 开启轨道颜色
  inactiveThumbColor: Colors.white,      // 关闭滑块颜色
  inactiveTrackColor: Colors.grey,       // 关闭轨道颜色
)

// 或使用 WidgetStateProperty
Switch(
  value: _isOn,
  onChanged: (value) {},
  thumbColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) {
      return Colors.blue;
    }
    return Colors.white;
  }),
  trackColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) {
      return Colors.blue.withValues(alpha: 0.5);
    }
    return Colors.grey;
  }),
)
```

### 3.3 禁用状态

```objc
// Objective-C
switch.enabled = NO;
```

```dart
// Flutter - onChanged 为 null 时禁用
Switch(
  value: _isOn,
  onChanged: null,  // 禁用
)

// 禁用时的颜色
Switch(
  value: _isOn,
  onChanged: null,
  thumbColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.disabled)) {
      return Colors.grey;
    }
    return Colors.blue;
  }),
)
```

### 3.4 带图片的开关

```objc
// Objective-C - UISwitch 不支持图片
// 需要自定义实现
```

```dart
// Flutter - 支持滑块图片
Switch(
  value: _isOn,
  onChanged: (value) {},
  activeThumbImage: AssetImage('assets/switch_on.png'),
  inactiveThumbImage: AssetImage('assets/switch_off.png'),
)
```

### 3.5 SwitchListTile（带标签）

```objc
// Objective-C - 需要自定义 Cell 或 View
UITableViewCell *cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:nil];
cell.textLabel.text = @"Notifications";
cell.detailTextLabel.text = @"Enable push notifications";
cell.accessoryView = [[UISwitch alloc] init];
```

```dart
// Flutter - SwitchListTile
SwitchListTile(
  value: _notificationsEnabled,
  onChanged: (value) {
    setState(() {
      _notificationsEnabled = value;
    });
  },
  title: Text('Notifications'),
  subtitle: Text('Enable push notifications'),
  secondary: Icon(Icons.notifications),
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 设置页面开关列表
@interface SettingsViewController : UIViewController
@property (nonatomic, strong) UISwitch *notificationSwitch;
@property (nonatomic, strong) UISwitch *darkModeSwitch;
@property (nonatomic, strong) UISwitch *locationSwitch;
@end

@implementation SettingsViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.view.backgroundColor = [UIColor systemBackgroundColor];
    
    // 通知开关
    UILabel *notificationLabel = [[UILabel alloc] initWithFrame:CGRectMake(20, 100, 200, 30)];
    notificationLabel.text = @"Notifications";
    [self.view addSubview:notificationLabel];
    
    self.notificationSwitch = [[UISwitch alloc] initWithFrame:CGRectMake(280, 100, 50, 30)];
    self.notificationSwitch.on = YES;
    self.notificationSwitch.onTintColor = [UIColor systemBlueColor];
    [self.notificationSwitch addTarget:self action:@selector(notificationChanged:) forControlEvents:UIControlEventValueChanged];
    [self.view addSubview:self.notificationSwitch];
    
    // 深色模式开关
    UILabel *darkModeLabel = [[UILabel alloc] initWithFrame:CGRectMake(20, 150, 200, 30)];
    darkModeLabel.text = @"Dark Mode";
    [self.view addSubview:darkModeLabel];
    
    self.darkModeSwitch = [[UISwitch alloc] initWithFrame:CGRectMake(280, 150, 50, 30)];
    self.darkModeSwitch.on = NO;
    self.darkModeSwitch.onTintColor = [UIColor systemBlueColor];
    [self.darkModeSwitch addTarget:self action:@selector(darkModeChanged:) forControlEvents:UIControlEventValueChanged];
    [self.view addSubview:self.darkModeSwitch];
    
    // 位置开关
    UILabel *locationLabel = [[UILabel alloc] initWithFrame:CGRectMake(20, 200, 200, 30)];
    locationLabel.text = @"Location Services";
    [self.view addSubview:locationLabel];
    
    self.locationSwitch = [[UISwitch alloc] initWithFrame:CGRectMake(280, 200, 50, 30)];
    self.locationSwitch.on = NO;
    self.locationSwitch.enabled = NO;  // 禁用状态
    [self.view addSubview:self.locationSwitch];
}

- (void)notificationChanged:(UISwitch *)sender {
    NSLog(@"Notifications: %@", sender.on ? @"ON" : @"OFF");
}

- (void)darkModeChanged:(UISwitch *)sender {
    NSLog(@"Dark Mode: %@", sender.on ? @"ON" : @"OFF");
}
@end
```

### Flutter 实现

```dart
// 设置页面 - 更简洁
class SettingsPage extends StatefulWidget {
  @override
  _SettingsPageState createState() => _SettingsPageState();
}

class _SettingsPageState extends State<SettingsPage> {
  bool notificationsEnabled = true;
  bool darkModeEnabled = false;
  bool locationEnabled = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Settings')),
      body: ListView(
        children: [
          SwitchListTile(
            value: notificationsEnabled,
            onChanged: (value) {
              setState(() => notificationsEnabled = value);
            },
            title: Text('Notifications'),
            subtitle: Text('Enable push notifications'),
            secondary: Icon(Icons.notifications),
          ),
          SwitchListTile(
            value: darkModeEnabled,
            onChanged: (value) {
              setState(() => darkModeEnabled = value);
            },
            title: Text('Dark Mode'),
            subtitle: Text('Use dark theme'),
            secondary: Icon(Icons.dark_mode),
          ),
          SwitchListTile(
            value: locationEnabled,
            onChanged: null,  // 禁用
            title: Text('Location Services'),
            subtitle: Text('Enable in Settings app'),
            secondary: Icon(Icons.location_off),
          ),
          Divider(),
          // 自定义样式的开关
          ListTile(
            title: Text('Custom Style'),
            trailing: Switch(
              value: notificationsEnabled,
              onChanged: (value) {
                setState(() => notificationsEnabled = value);
              },
              activeColor: Colors.purple,
              activeTrackColor: Colors.purple.withValues(alpha: 0.5),
            ),
          ),
        ],
      ),
    );
  }
}
```

---

## 5. Material 3 Switch 变体

### Switch 风格

```dart
// Material 3 默认风格 - 有图标指示
Switch(
  value: _isOn,
  onChanged: (value) {},
)

// 自定义轨道边框
Switch(
  value: _isOn,
  onChanged: (value) {},
  trackOutlineColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) {
      return Colors.blue;
    }
    return Colors.grey;
  }),
)
```

---

## 6. 常见陷阱

### 陷阱1：忘记 setState

```dart
// ❌ 错误：值不会更新
Switch(
  value: _isOn,
  onChanged: (value) {
    _isOn = value;  // 不会触发 UI 更新
  },
)

// ✅ 正确：使用 setState
Switch(
  value: _isOn,
  onChanged: (value) {
    setState(() {
      _isOn = value;
    });
  },
)
```

### 陷阱2：Switch 在 ListTile 中

```dart
// ⚠️ 使用 ListTile + trailing Switch 时，点击整行不会触发开关
ListTile(
  title: Text('Title'),
  trailing: Switch(
    value: _isOn,
    onChanged: (value) {},
  ),
)

// ✅ 使用 SwitchListTile
SwitchListTile(
  title: Text('Title'),
  value: _isOn,
  onChanged: (value) {},
)
```

### 陷阱3：颜色设置不生效

```dart
// ⚠️ 如果同时设置 activeColor 和 thumbColor，thumbColor 优先级更高
Switch(
  value: _isOn,
  onChanged: (value) {},
  activeColor: Colors.blue,
  thumbColor: WidgetStateProperty.all(Colors.red),  // 会覆盖 activeColor
)

// ✅ 选择一种方式设置颜色
Switch(
  value: _isOn,
  onChanged: (value) {},
  activeColor: Colors.blue,  // 使用这个
)
// 或
Switch(
  value: _isOn,
  onChanged: (value) {},
  thumbColor: WidgetStateProperty.resolveWith((states) {
    if (states.contains(WidgetState.selected)) return Colors.blue;
    return Colors.white;
  }),
)
```

### 陷阱4：自适应平台

```dart
// Switch.adaptive 会根据平台显示不同风格
// iOS 显示 Cupertino 风格，Android 显示 Material 风格
Switch.adaptive(
  value: _isOn,
  onChanged: (value) {},
)
```

---

## 7. 学习技巧

### 技巧1：使用 SwitchListTile 简化代码

```dart
// 比手动组合 ListTile + Switch 更简洁
SwitchListTile(
  value: _isOn,
  onChanged: (value) {},
  title: Text('Title'),
  subtitle: Text('Subtitle'),
  secondary: Icon(Icons.star),
  isThreeLine: false,
  dense: false,
)
```

### 技巧2：动态禁用

```dart
SwitchListTile(
  value: _isOn,
  onChanged: canEdit ? (value) {
    setState(() => _isOn = value);
  } : null,  // 动态禁用
  title: Text(canEdit ? 'Enable Feature' : 'Feature Locked'),
)
```

### 技巧3：自定义滑块图标

```dart
// Flutter 3.41+ 支持
Switch(
  value: _isOn,
  onChanged: (value) {},
  // Material 3 开关会自动显示图标指示
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UISwitch                    → Switch
isOn                        → value
addTarget:forControlEvents: → onChanged
onTintColor                 → activeTrackColor
thumbTintColor              → inactiveThumbColor
enabled                     → onChanged != null
UITableViewCell + UISwitch  → SwitchListTile
```

### 关键差异

| 方面 | UIKit UISwitch | Flutter Switch |
|------|----------------|----------------|
| **状态管理** | 属性设置 | 回调 + setState |
| **颜色自定义** | 有限 | 丰富 |
| **带标签** | 需自定义 | SwitchListTile |
| **图片滑块** | 不支持 | 支持 |
| **禁用** | enabled 属性 | onChanged = null |

### 最佳实践

1. **用 SwitchListTile** - 带标签的开关场景
2. **Material 3 风格** - 默认就有更好的视觉效果
3. **记得 setState** - 值变化后触发 UI 更新
4. **null 表示禁用** - onChanged 为 null 时禁用

---

*最后更新: 2026-02-24*

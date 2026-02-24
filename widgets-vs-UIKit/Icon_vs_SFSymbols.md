# Icon vs SF Symbols 对比学习

本文档记录 Flutter Icon 组件与 UIKit SF Symbols 的对比学习要点。

---

## 1. Icon 核心概念

### Icon 是什么

Icon 是 Flutter 中显示图标的组件，使用预定义的图标集（Icons 类），类似于 iOS 的 SF Symbols。

- 使用 Material Design 图标集
- 支持自定义颜色和大小
- 支持文本方向
- 支持语义标签

### Icon vs SF Symbols 概念对比

| Flutter Icon | UIKit SF Symbols | 说明 |
|--------------|------------------|------|
| `Icons.home` | `"house"` | 图标名称 |
| `size` | `UIImage.SymbolConfiguration` | 图标大小 |
| `color` | `tintColor` | 图标颜色 |
| `semanticLabel` | 无直接对应 | 语义标签 |
| `textDirection` | 无直接对应 | 文本方向 |
| `IconTheme` | `SymbolConfiguration` | 统一配置 |

---

## 2. 源码分析

### Icon 类定义

```dart
class Icon extends StatelessWidget {
  const Icon(
    this.icon, {              // IconData
    super.key,
    this.size,                // 大小
    this.fill,                // 填充度（0-1）
    this.weight,              // 粗细
    this.grade,               // 等级
    this.opticalSize,         // 光学尺寸
    this.color,               // 颜色
    this.shadows,             // 阴影
    this.semanticLabel,       // 语义标签
    this.textDirection,       // 文本方向
    this.applyTextScaling,    // 应用文本缩放
  });
}

// IconData - 图标数据
class IconData {
  const IconData(
    this.codePoint,           // Unicode 码点
    this.fontFamily,          // 字体家族
    this.fontPackage,         // 包名
    this.matchTextDirection,  // 匹配文本方向
  });
}

// Icons 类 - Material 图标集
class Icons {
  static const IconData home = IconData(0xe3dc, fontFamily: 'MaterialIcons');
  static const IconData settings = IconData(0xe8b8, fontFamily: 'MaterialIcons');
  static const IconData person = IconData(0xe7fd, fontFamily: 'MaterialIcons');
  // ... 1000+ 图标
}
```

### IconTheme

```dart
// 统一配置图标样式
class IconTheme extends InheritedWidget {
  const IconTheme({
    super.key,
    required this.data,
    required super.child,
  });
  
  final IconThemeData data;
}

class IconThemeData {
  const IconThemeData({
    this.color,
    this.opacity,
    this.size,
    this.shadows,
    this.applyTextScaling,
  });
}
```

---

## 3. 与 SF Symbols 详细对比

### 3.1 基础图标显示

```objc
// Objective-C - SF Symbols
UIImage *icon = [UIImage systemImageNamed:@"house"];
UIImageView *imageView = [[UIImageView alloc] initWithImage:icon];
imageView.tintColor = [UIColor systemBlueColor];
[self.view addSubview:imageView];
```

```dart
// Flutter - Icon
Icon(
  Icons.home,
  color: Colors.blue,
)
```

### 3.2 图标大小

```objc
// Objective-C - Symbol Configuration
UIImageSymbolConfiguration *config = [UIImageSymbolConfiguration configurationWithPointSize:24 weight:UIImageSymbolWeightRegular];
UIImage *icon = [UIImage systemImageNamed:@"house" withConfiguration:config];
```

```dart
// Flutter - size 参数
Icon(
  Icons.home,
  size: 24,
)
```

### 3.3 图标颜色

```objc
// Objective-C
imageView.tintColor = [UIColor systemBlueColor];
// 或使用 UIImageRenderingModeAlwaysTemplate
UIImage *icon = [UIImage systemImageNamed:@"house"];
imageView.image = [icon imageWithTintColor:[UIColor systemBlueColor]];
```

```dart
// Flutter
Icon(
  Icons.home,
  color: Colors.blue,
  size: 32,
)

// 或使用 IconTheme
IconTheme(
  data: IconThemeData(color: Colors.blue, size: 32),
  child: Icon(Icons.home),
)
```

### 3.4 图标变体（SF Symbols 特有）

```objc
// Objective-C - SF Symbols 有多种变体
UIImage *filled = [UIImage systemImageNamed:@"house.fill"];
UIImage *circle = [UIImage systemImageNamed:@"house.circle"];
UIImage *circleFill = [UIImage systemImageNamed:@"house.circle.fill"];
```

```dart
// Flutter - 不同的 IconData
Icon(Icons.home)                    // 默认
Icon(Icons.home_filled)             // 填充版本
Icon(Icons.home_outlined)           // 轮廓版本
// 或使用 IconData 的 fill/weight/grade 参数
Icon(
  IconData(0xe3dc, fontFamily: 'MaterialIcons'),
  fill: 1.0,  // 0.0 = outlined, 1.0 = filled
)
```

### 3.5 图标粗细

```objc
// Objective-C
UIImageSymbolConfiguration *config = [UIImageSymbolConfiguration configurationWithWeight:UIImageSymbolWeightBold];
UIImage *icon = [UIImage systemImageNamed:@"house" withConfiguration:config];
```

```dart
// Flutter
Icon(
  Icons.home,
  weight: 700,  // 100-900
)
```

### 3.6 按钮中的图标

```objc
// Objective-C - UIButton
UIButton *button = [UIButton buttonWithType:UIButtonTypeSystem];
[button setImage:[UIImage systemImageNamed:@"house"] forState:UIControlStateNormal];
[button addTarget:self action:@selector(buttonTapped) forControlEvents:UIControlEventTouchUpInside];
```

```dart
// Flutter - IconButton
IconButton(
  icon: Icon(Icons.home),
  onPressed: () {},
)

// 或 ElevatedButton
ElevatedButton.icon(
  icon: Icon(Icons.home),
  label: Text('Home'),
  onPressed: () {},
)
```

### 3.7 列表项图标

```objc
// Objective-C
cell.imageView.image = [UIImage systemImageNamed:@"house"];
```

```dart
// Flutter - ListTile
ListTile(
  leading: Icon(Icons.home),
  title: Text('Home'),
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 设置页面图标列表
@interface SettingsIconsViewController : UIViewController
@property (nonatomic, strong) NSArray *items;
@end

@implementation SettingsIconsViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.items = @[
        @{@"title": @"Home", @"icon": @"house"},
        @{@"title": @"Profile", @"icon": @"person"},
        @{@"title": @"Notifications", @"icon": @"bell"},
        @{@"title": @"Settings", @"icon": @"gear"},
        @{@"title": @"Help", @"icon": @"questionmark.circle"},
    ];
    
    UITableView *tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:UITableViewStyleGrouped];
    tableView.dataSource = self;
    tableView.delegate = self;
    [self.view addSubview:tableView];
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.items.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell"];
    if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"Cell"];
    }
    
    NSDictionary *item = self.items[indexPath.row];
    cell.textLabel.text = item[@"title"];
    cell.imageView.image = [UIImage systemImageNamed:item[@"icon"]];
    cell.imageView.tintColor = [UIColor systemBlueColor];
    
    return cell;
}
@end
```

### Flutter 实现

```dart
// 设置页面图标列表 - 更简洁
class SettingsIconsPage extends StatelessWidget {
  final items = [
    {'title': 'Home', 'icon': Icons.home},
    {'title': 'Profile', 'icon': Icons.person},
    {'title': 'Notifications', 'icon': Icons.notifications},
    {'title': 'Settings', 'icon': Icons.settings},
    {'title': 'Help', 'icon': Icons.help_outline},
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Settings')),
      body: ListView.builder(
        itemCount: items.length,
        itemBuilder: (context, index) {
          final item = items[index];
          return ListTile(
            leading: Icon(
              item['icon'] as IconData,
              color: Colors.blue,
            ),
            title: Text(item['title'] as String),
            trailing: Icon(Icons.chevron_right),
            onTap: () {},
          );
        },
      ),
    );
  }
}
```

---

## 5. Material Icons vs SF Symbols 对照表

| 功能 | SF Symbols | Material Icons |
|------|------------|----------------|
| 首页 | `house` | `Icons.home` |
| 设置 | `gear` | `Icons.settings` |
| 搜索 | `magnifyingglass` | `Icons.search` |
| 用户 | `person` | `Icons.person` |
| 添加 | `plus` | `Icons.add` |
| 关闭 | `xmark` | `Icons.close` |
| 返回 | `chevron.left` | `Icons.arrow_back` |
| 前进 | `chevron.right` | `Icons.arrow_forward` |
| 分享 | `square.and.arrow.up` | `Icons.share` |
| 收藏 | `heart` / `heart.fill` | `Icons.favorite` / `Icons.favorite_border` |
| 删除 | `trash` | `Icons.delete` |
| 编辑 | `pencil` | `Icons.edit` |
| 通知 | `bell` | `Icons.notifications` |
| 邮件 | `envelope` | `Icons.email` / `Icons.mail` |
| 电话 | `phone` | `Icons.phone` |
| 相机 | `camera` | `Icons.camera` |
| 图片 | `photo` | `Icons.image` |
| 播放 | `play` | `Icons.play_arrow` |
| 暂停 | `pause` | `Icons.pause` |
| 刷新 | `arrow.clockwise` | `Icons.refresh` |

---

## 6. 常见陷阱

### 陷阱1：图标不存在

```dart
// ❌ 错误：使用了不存在的图标
Icon(Icons.house_fill)  // 不存在！

// ✅ 正确：使用正确的图标名
Icon(Icons.home_filled)  // 或
Icon(Icons.home)
```

### 陷阱2：图标颜色不生效

```dart
// ❌ 图标颜色由最近的 IconTheme 或 Theme 控制
// 如果父组件设置了 IconTheme，可能会被覆盖

// ✅ 确保 color 设置正确
Icon(
  Icons.home,
  color: Colors.blue,  // 直接设置
)

// 或检查 IconTheme
IconTheme(
  data: IconThemeData(color: Colors.blue),
  child: Icon(Icons.home),
)
```

### 陷阱3：大小不一致

```dart
// 默认大小是 24
Icon(Icons.home)  // 24x24

// IconButton 中的图标
IconButton(
  icon: Icon(Icons.home),  // 默认 24
  iconSize: 32,            // 修改大小
  onPressed: () {},
)
```

### 陷阱4：使用自定义图标

```dart
// 使用自定义图标字体
Icon(
  IconData(
    0xe900,  // 自定义 Unicode
    fontFamily: 'MyIcons',
  ),
)

// 需要在 pubspec.yaml 中声明字体
/*
flutter:
  fonts:
    - family: MyIcons
      fonts:
        - asset: assets/fonts/MyIcons.ttf
*/
```

---

## 7. 学习技巧

### 技巧1：搜索图标

```dart
// 使用 IDE 的自动补全
// 输入 Icons. 会显示所有可用图标

// 或访问 https://fonts.google.com/icons 搜索 Material Icons
```

### 技巧2：统一图标样式

```dart
// 使用 IconTheme 统一配置
IconTheme(
  data: IconThemeData(
    color: Theme.of(context).primaryColor,
    size: 24,
  ),
  child: Column(
    children: [
      Icon(Icons.home),
      Icon(Icons.settings),
      Icon(Icons.person),
    ],
  ),
)
```

### 技巧3：自适应图标

```dart
// 使用 Icons.adaptive 获取平台适配图标
Icon(Icons.adaptive.share)  // iOS 和 Android 显示不同图标
Icon(Icons.adaptive.more)
```

### 技巧4：图标动画

```dart
// 使用 AnimatedSwitcher 切换图标
AnimatedSwitcher(
  duration: Duration(milliseconds: 200),
  child: Icon(
    isPlaying ? Icons.pause : Icons.play_arrow,
    key: ValueKey(isPlaying),
  ),
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
SF Symbols                  → Material Icons
systemImageNamed:           → Icons.xxx
SymbolConfiguration         → size / color / weight
tintColor                   → color
pointSize                   → size
UIImageSymbolWeight         → weight
不同变体（.fill）            → 不同的 IconData
UIButton + systemImage      → IconButton
UITableViewCell.imageView   → ListTile.leading
```

### 关键差异

| 方面 | UIKit SF Symbols | Flutter Icons |
|------|------------------|---------------|
| **图标数量** | 5000+ | 1000+ |
| **命名** | 字符串 | IconData 常量 |
| **变体** | 后缀（.fill, .circle） | 不同常量 |
| **配置** | SymbolConfiguration | 属性参数 |
| **平台** | 仅 iOS | 跨平台 |
| **类型安全** | 字符串（运行时检查） | 编译时检查 |

### 最佳实践

1. **使用 Icons.xxx 常量** - 类型安全，编译时检查
2. **统一使用 IconTheme** - 保持风格一致
3. **语义标签** - 为无障碍设置 semanticLabel
4. **自适应图标** - 使用 Icons.adaptive 平台适配

---

*最后更新: 2026-02-24*

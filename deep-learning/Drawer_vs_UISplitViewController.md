# Drawer vs UISplitViewController 对比学习

本文档记录 Flutter Drawer 组件与 UIKit UISplitViewController 的对比学习要点。

---

## 1. Drawer 核心概念

### Drawer 是什么

Drawer 是 Flutter 中的侧边抽屉组件，通常用于导航菜单，类似于 iOS 的 UISplitViewController 主视图。

- 从屏幕边缘滑出
- 支持左侧和右侧抽屉
- 通常配合 Scaffold 使用
- Material Design 风格

### Drawer vs UISplitViewController 概念对比

| Flutter Drawer | UIKit UISplitViewController | 说明 |
|----------------|---------------------------|------|
| `Drawer` | 主视图（Master） | 侧边导航 |
| `Scaffold.drawer` | `viewControllers[0]` | 主视图 |
| `Scaffold.endDrawer` | 无直接对应 | 右侧抽屉 |
| `Scaffold.of(context).openDrawer()` | 展开/收起 | 打开抽屉 |
| `Navigator.pop()` | `preferredDisplayMode` | 关闭抽屉 |
| `DrawerHeader` | 表头视图 | 抽屉头部 |
| `UserAccountsDrawerHeader` | 用户信息头部 | 用户信息 |

---

## 2. 源码分析

### Drawer 类定义

```dart
class Drawer extends StatelessWidget {
  const Drawer({
    super.key,
    this.elevation = 16.0,          // 阴影
    this.child,                     // 子组件
    this.semanticLabel,             // 语义标签
    this.width,                     // 宽度（默认 304）
    this.shape,                     // 形状
    this.backgroundColor,           // 背景色
    this.surfaceTintColor,          // 表面色
    this.shadowColor,               // 阴影色
    this.clipBehavior,              // 裁剪行为
  });
}

// Scaffold 中的 drawer 属性
class Scaffold extends StatefulWidget {
  const Scaffold({
    this.drawer,          // 左侧抽屉
    this.endDrawer,       // 右侧抽屉
    this.drawerScrimColor, // 遮罩颜色
    this.drawerEdgeDragWidth, // 拖拽区域宽度
    this.drawerEnableOpenDragGesture, // 是否允许拖拽打开
    this.endDrawerEnableOpenDragGesture,
    // ...
  });
}
```

### DrawerHeader

```dart
// 抽屉头部
class DrawerHeader extends StatelessWidget {
  const DrawerHeader({
    super.key,
    this.decoration,        // 装饰
    this.margin,            // 外边距
    this.padding,           // 内边距
    this.duration,          // 动画时长
    this.curve,             // 动画曲线
    required this.child,
  });
}

// 用户账户头部
class UserAccountsDrawerHeader extends StatelessWidget {
  const UserAccountsDrawerHeader({
    super.key,
    this.decoration,
    this.margin,
    this.currentAccountPicture,    // 当前账户头像
    this.otherAccountsPictures,     // 其他账户头像
    this.accountName,               // 账户名
    this.accountEmail,              // 账户邮箱
    this.onDetailsPressed,          // 点击详情
    this.arrowColor,                // 箭头颜色
  });
}
```

---

## 3. 与 UISplitViewController 详细对比

### 3.1 基础抽屉

```objc
// Objective-C - UISplitViewController
UISplitViewController *splitVC = [[UISplitViewController alloc] init];

UINavigationController *masterNav = [[UINavigationController alloc] initWithRootViewController:menuVC];
UINavigationController *detailNav = [[UINavigationController alloc] initWithRootViewController:detailVC];

splitVC.viewControllers = @[masterNav, detailNav];
splitVC.preferredDisplayMode = UISplitViewControllerDisplayModeAutomatic;
```

```dart
// Flutter - Drawer
Scaffold(
  drawer: Drawer(
    child: ListView(
      children: [
        DrawerHeader(child: Text('Menu')),
        ListTile(title: Text('Item 1'), onTap: () {}),
        ListTile(title: Text('Item 2'), onTap: () {}),
      ],
    ),
  ),
  body: Center(child: Text('Content')),
)
```

### 3.2 打开/关闭抽屉

```objc
// Objective-C - UISplitViewController
if (splitVC.isCollapsed) {
    [splitVC showDetailViewController:detailVC sender:nil];
} else {
    splitVC.preferredDisplayMode = UISplitViewControllerDisplayModePrimaryOverlay;
}
```

```dart
// Flutter - 打开抽屉
Scaffold.of(context).openDrawer();

// 关闭抽屉
Navigator.pop(context);

// 或使用 ScaffoldMessenger
ScaffoldMessenger.of(context).openDrawer();
```

### 3.3 用户信息头部

```objc
// Objective-C - 需要自定义 View
UIView *headerView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 300, 150)];
headerView.backgroundColor = [UIColor systemBlueColor];

UIImageView *avatar = [[UIImageView alloc] initWithFrame:CGRectMake(20, 40, 60, 60)];
avatar.layer.cornerRadius = 30;
avatar.image = [UIImage imageNamed:@"avatar"];
[headerView addSubview:avatar];

UILabel *nameLabel = [[UILabel alloc] initWithFrame:CGRectMake(20, 110, 260, 20)];
nameLabel.text = @"John Doe";
nameLabel.textColor = [UIColor whiteColor];
[headerView addSubview:nameLabel];
```

```dart
// Flutter - UserAccountsDrawerHeader
Drawer(
  child: ListView(
    children: [
      UserAccountsDrawerHeader(
        accountName: Text('John Doe'),
        accountEmail: Text('john@example.com'),
        currentAccountPicture: CircleAvatar(
          backgroundImage: AssetImage('assets/avatar.jpg'),
        ),
        decoration: BoxDecoration(
          color: Colors.blue,
        ),
      ),
      ListTile(title: Text('Item 1')),
    ],
  ),
)
```

### 3.4 右侧抽屉

```objc
// Objective-C - UISplitViewController 不支持右侧抽屉
// 需要自定义实现
```

```dart
// Flutter - endDrawer
Scaffold(
  endDrawer: Drawer(
    child: ListView(
      children: [
        DrawerHeader(child: Text('Notifications')),
        ListTile(title: Text('Notification 1')),
      ],
    ),
  ),
  body: Center(child: Text('Content')),
)
```

### 3.5 抽屉菜单项

```objc
// Objective-C - UITableView
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
    cell.textLabel.text = self.menuItems[indexPath.row];
    cell.imageView.image = [UIImage systemImageNamed:self.menuIcons[indexPath.row]];
    return cell;
}

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    [tableView deselectRowAtIndexPath:indexPath animated:YES];
    // 导航到对应页面
    [self.splitViewController showDetailViewController:detailVC sender:nil];
}
```

```dart
// Flutter - ListTile
Drawer(
  child: ListView(
    children: [
      DrawerHeader(child: Text('Menu')),
      ListTile(
        leading: Icon(Icons.home),
        title: Text('Home'),
        selected: _selectedIndex == 0,
        onTap: () {
          Navigator.pop(context);
          setState(() => _selectedIndex = 0);
        },
      ),
      ListTile(
        leading: Icon(Icons.settings),
        title: Text('Settings'),
        selected: _selectedIndex == 1,
        onTap: () {
          Navigator.pop(context);
          setState(() => _selectedIndex = 1);
        },
      ),
    ],
  ),
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 带抽屉导航的应用
@interface MenuViewController : UITableViewController
@property (nonatomic, copy) NSArray *menuItems;
@end

@implementation MenuViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    self.menuItems = @[@"Home", @"Profile", @"Settings", @"About"];
    self.tableView.registerClass:[UITableViewCell class] forCellReuseIdentifier:@"Cell"];
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.menuItems.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
    cell.textLabel.text = self.menuItems[indexPath.row];
    return cell;
}

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    DetailViewController *detailVC = [[DetailViewController alloc] init];
    detailVC.title = self.menuItems[indexPath.row];
    
    UINavigationController *nav = [[UINavigationController alloc] initWithRootViewController:detailVC];
    [self.splitViewController showDetailViewController:nav sender:nil];
}
@end

// AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    MenuViewController *menuVC = [[MenuViewController alloc] init];
    UINavigationController *menuNav = [[UINavigationController alloc] initWithRootViewController:menuVC];
    
    UIViewController *placeholder = [[UIViewController alloc] init];
    placeholder.view.backgroundColor = [UIColor systemBackgroundColor];
    UINavigationController *detailNav = [[UINavigationController alloc] initWithRootViewController:placeholder];
    
    UISplitViewController *splitVC = [[UISplitViewController alloc] init];
    splitVC.viewControllers = @[menuNav, detailNav];
    splitVC.preferredDisplayMode = UISplitViewControllerDisplayModeAutomatic;
    
    self.window.rootViewController = splitVC;
    return YES;
}
@end
```

### Flutter 实现

```dart
// 带抽屉导航的应用 - 更简洁
class MainApp extends StatefulWidget {
  @override
  _MainAppState createState() => _MainAppState();
}

class _MainAppState extends State<MainApp> {
  int _selectedIndex = 0;
  
  final _pages = ['Home', 'Profile', 'Settings', 'About'];
  final _icons = [Icons.home, Icons.person, Icons.settings, Icons.info];
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(_pages[_selectedIndex])),
      drawer: Drawer(
        child: ListView(
          padding: EdgeInsets.zero,
          children: [
            UserAccountsDrawerHeader(
              accountName: Text('John Doe'),
              accountEmail: Text('john@example.com'),
              currentAccountPicture: CircleAvatar(
                child: Icon(Icons.person, size: 40),
              ),
              decoration: BoxDecoration(color: Colors.blue),
            ),
            ...List.generate(_pages.length, (index) {
              return ListTile(
                leading: Icon(_icons[index]),
                title: Text(_pages[index]),
                selected: _selectedIndex == index,
                onTap: () {
                  Navigator.pop(context);
                  setState(() => _selectedIndex = index);
                },
              );
            }),
            Divider(),
            ListTile(
              leading: Icon(Icons.logout),
              title: Text('Logout'),
              onTap: () {
                Navigator.pop(context);
                // 执行登出
              },
            ),
          ],
        ),
      ),
      body: Center(
        child: Text(_pages[_selectedIndex]),
      ),
    );
  }
}
```

---

## 5. Drawer 样式

### 5.1 宽度控制

```dart
Drawer(
  width: 280,  // 自定义宽度，默认 304
  child: ...,
)
```

### 5.2 圆角形状

```dart
Drawer(
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.only(
      topRight: Radius.circular(16),
      bottomRight: Radius.circular(16),
    ),
  ),
  child: ...,
)
```

### 5.3 遮罩颜色

```dart
Scaffold(
  drawerScrimColor: Colors.black54,  // 遮罩颜色
  drawer: Drawer(...),
  body: ...,
)
```

### 5.4 禁用拖拽打开

```dart
Scaffold(
  drawerEnableOpenDragGesture: false,  // 禁用边缘拖拽打开
  drawer: Drawer(...),
  body: ...,
)
```

---

## 6. 常见陷阱

### 陷阱1：Scaffold.of(context) 找不到

```dart
// ❌ 错误：在同一个 build 方法中调用
@override
Widget build(BuildContext context) {
  return Scaffold(
    drawer: Drawer(...),
    body: ElevatedButton(
      onPressed: () {
        Scaffold.of(context).openDrawer();  // 报错！
      },
      child: Text('Open'),
    ),
  );
}

// ✅ 方案1：使用 Builder
Scaffold(
  drawer: Drawer(...),
  body: Builder(
    builder: (context) => ElevatedButton(
      onPressed: () {
        Scaffold.of(context).openDrawer();
      },
      child: Text('Open'),
    ),
  ),
)

// ✅ 方案2：使用 GlobalKey
final scaffoldKey = GlobalKey<ScaffoldState>();

Scaffold(
  key: scaffoldKey,
  drawer: Drawer(...),
  body: ElevatedButton(
    onPressed: () {
      scaffoldKey.currentState?.openDrawer();
    },
    child: Text('Open'),
  ),
)
```

### 陷阱2：抽屉内容滚动

```dart
// ✅ 使用 ListView 避免内容溢出
Drawer(
  child: ListView(
    padding: EdgeInsets.zero,
    children: [
      DrawerHeader(...),
      ListTile(...),
      ListTile(...),
    ],
  ),
)
```

### 陷阱3：选中状态样式

```dart
// 使用 selected 属性高亮当前项
ListTile(
  leading: Icon(Icons.home),
  title: Text('Home'),
  selected: _selectedIndex == 0,
  selectedTileColor: Colors.blue.withValues(alpha: 0.1),
  selectedColor: Colors.blue,
  onTap: () {},
)
```

### 陷阱4：抽屉中的导航

```dart
// 选择后关闭抽屉
ListTile(
  title: Text('Page 1'),
  onTap: () {
    Navigator.pop(context);  // 先关闭抽屉
    Navigator.push(context, MaterialPageRoute(
      builder: (context) => Page1(),
    ));
  },
)
```

---

## 7. 学习技巧

### 技巧1：抽屉分组

```dart
Drawer(
  child: ListView(
    children: [
      DrawerHeader(child: Text('Menu')),
      // 主要功能
      ListTile(title: Text('Home')),
      ListTile(title: Text('Profile')),
      Divider(),
      // 设置
      ListTile(title: Text('Settings')),
      ListTile(title: Text('Help')),
    ],
  ),
)
```

### 技巧2：响应式抽屉

```dart
// 平板显示 NavigationRail，手机显示 Drawer
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth > 600) {
      return Row(
        children: [
          NavigationRail(...),
          Expanded(child: content),
        ],
      );
    } else {
      return Scaffold(
        drawer: Drawer(...),
        body: content,
      );
    }
  },
)
```

### 技巧3：抽屉状态持久化

```dart
// 使用 IndexedStack 保持页面状态
body: IndexedStack(
  index: _selectedIndex,
  children: [
    HomePage(),
    ProfilePage(),
    SettingsPage(),
  ],
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UISplitViewController      → Scaffold + Drawer
Master View                → Drawer
Detail View                → body
viewControllers[0]         → Scaffold.drawer
preferredDisplayMode       → 自动处理
自定义表头                  → DrawerHeader
UITableView 菜单           → ListView + ListTile
UITableViewCell            → ListTile
```

### 关键差异

| 方面 | UIKit UISplitViewController | Flutter Drawer |
|------|---------------------------|----------------|
| **显示方式** | 分栏 | 滑出覆盖 |
| **右侧抽屉** | 不支持 | endDrawer |
| **用户信息** | 需自定义 | UserAccountsDrawerHeader |
| **导航方式** | showDetailViewController | Navigator |
| **平台风格** | iOS 风格 | Material Design |

### 最佳实践

1. **使用 ListView** - 抽屉内容可滚动
2. **关闭后再导航** - 先 pop 再 push
3. **高亮选中项** - selected 属性
4. **响应式设计** - 大屏用 NavigationRail

---

*最后更新: 2026-02-24*

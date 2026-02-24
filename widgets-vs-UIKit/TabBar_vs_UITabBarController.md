# TabBar vs UITabBarController 对比学习

本文档记录 Flutter TabBar 与 UIKit UITabBarController 的对比学习要点。

---

## 1. TabBar 核心概念

### Flutter Tab 导航组件

Flutter 提供了多层次的 Tab 导航组件：

| 组件 | 说明 | 使用场景 |
|------|------|---------|
| `TabBar` | 顶部标签栏 | 配合 TabBarView |
| `TabBarView` | 标签页内容 | 显示对应页面 |
| `BottomNavigationBar` | 底部导航栏 | 3-5 个主要页面 |
| `NavigationBar` | Material 3 底部导航 | Material 3 风格 |

### TabBar vs UITabBarController 概念对比

| Flutter | UIKit | 说明 |
|---------|-------|------|
| `TabBar` | `UITabBar` | 标签栏 |
| `TabBarView` | `viewControllers` | 标签页内容 |
| `TabController` | `UITabBarController` | 控制器 |
| `BottomNavigationBar` | `UITabBar` | 底部导航 |
| `BottomNavigationBarItem` | `UITabBarItem` | 标签项 |
| `DefaultTabController` | 无直接对应 | 默认控制器 |

---

## 2. 源码分析

### TabBar 类定义

```dart
class TabBar extends StatefulWidget {
  const TabBar({
    super.key,
    required this.tabs,              // Tab 列表
    this.controller,                 // TabController
    this.isScrollable = false,       // 是否可滚动
    this.padding,
    this.indicatorColor,             // 指示器颜色
    this.automaticIndicatorColorAdjustment = true,
    this.indicatorWeight = 2.0,      // 指示器厚度
    this.indicatorPadding = EdgeInsets.zero,
    this.indicator,                  // 自定义指示器
    this.indicatorSize,              // 指示器尺寸
    this.labelColor,                 // 标签颜色
    this.labelStyle,                 // 标签样式
    this.labelPadding,               // 标签内边距
    this.unselectedLabelColor,       // 未选中标签颜色
    this.unselectedLabelStyle,
    this.dragStartBehavior = DragStartBehavior.start,
    this.overlayState,
    this.mouseCursor,
    this.enableFeedback = true,
    this.onTap,                      // 点击回调
    this.physics,
    this.splashFactory,
    this.splashBorderRadius,
    this.tabAlignment,
  });
}
```

### BottomNavigationBar 类定义

```dart
class BottomNavigationBar extends StatefulWidget {
  BottomNavigationBar({
    super.key,
    required this.items,             // 导航项列表
    this.onTap,                      // 点击回调
    int currentIndex = 0,            // 当前索引
    double? elevation,
    this.type = BottomNavigationBarType.fixed,  // 类型
    Color? fixedColor,
    this.backgroundColor,
    double iconSize = 24.0,
    Color? selectedItemColor,        // 选中颜色
    Color? unselectedItemColor,      // 未选中颜色
    this.selectedIconTheme,
    this.unselectedIconTheme,
    this.selectedLabelStyle,
    this.unselectedLabelStyle,
    this.showSelectedLabels = true,
    this.showUnselectedLabels = true,
    this.mouseCursor,
    this.enableFeedback = true,
    this.landscapeLayout,
    this.useLegacyColorScheme = true,
  });
}
```

---

## 3. 与 UITabBarController 详细对比

### 3.1 基础底部导航

```objc
// Objective-C - UITabBarController
@interface AppDelegate ()
@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    UITabBarController *tabBar = [[UITabBarController alloc] init];
    
    HomeViewController *home = [[HomeViewController alloc] init];
    home.tabBarItem = [[UITabBarItem alloc] initWithTitle:@"Home" image:[UIImage systemImageNamed:@"house"] tag:0];
    
    SearchViewController *search = [[SearchViewController alloc] init];
    search.tabBarItem = [[UITabBarItem alloc] initWithTitle:@"Search" image:[UIImage systemImageNamed:@"magnifyingglass"] tag:1];
    
    ProfileViewController *profile = [[ProfileViewController alloc] init];
    profile.tabBarItem = [[UITabBarItem alloc] initWithTitle:@"Profile" image:[UIImage systemImageNamed:@"person"] tag:2];
    
    tabBar.viewControllers = @[
        [[UINavigationController alloc] initWithRootViewController:home],
        [[UINavigationController alloc] initWithRootViewController:search],
        [[UINavigationController alloc] initWithRootViewController:profile]
    ];
    
    self.window.rootViewController = tabBar;
    return YES;
}
@end
```

```dart
// Flutter - BottomNavigationBar
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: MainPage(),
    );
  }
}

class MainPage extends StatefulWidget {
  @override
  _MainPageState createState() => _MainPageState();
}

class _MainPageState extends State<MainPage> {
  int _currentIndex = 0;
  
  final pages = [
    HomePage(),
    SearchPage(),
    ProfilePage(),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: pages[_currentIndex],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (index) {
          setState(() {
            _currentIndex = index;
          });
        },
        items: [
          BottomNavigationBarItem(
            icon: Icon(Icons.home),
            label: 'Home',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.search),
            label: 'Search',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.person),
            label: 'Profile',
          ),
        ],
      ),
    );
  }
}
```

### 3.2 顶部 TabBar

```objc
// Objective-C - UISegmentedControl 或自定义
// UIKit 没有内置的顶部 TabBar，通常使用 UISegmentedControl 或第三方库
UISegmentedControl *segment = [[UISegmentedControl alloc] initWithItems:@[@"All", @"Missed", @"Voicemail"]];
segment.selectedSegmentIndex = 0;
[segment addTarget:self action:@selector(segmentChanged:) forControlEvents:UIControlEventValueChanged];
```

```dart
// Flutter - TabBar
DefaultTabController(
  length: 3,
  child: Scaffold(
    appBar: AppBar(
      title: Text('Calls'),
      bottom: TabBar(
        tabs: [
          Tab(text: 'All'),
          Tab(text: 'Missed'),
          Tab(text: 'Voicemail'),
        ],
      ),
    ),
    body: TabBarView(
      children: [
        AllCallsPage(),
        MissedCallsPage(),
        VoicemailPage(),
      ],
    ),
  ),
)
```

### 3.3 Tab 样式

```objc
// Objective-C - UITabBar 外观
[UITabBar appearance].barTintColor = [UIColor whiteColor];
[UITabBar appearance].tintColor = [UIColor blueColor];
[UITabBar appearance].unselectedItemTintColor = [UIColor grayColor];

// 单独设置
UITabBarItem *item = [[UITabBarItem alloc] initWithTitle:@"Home" image:unselectedImage selectedImage:selectedImage];
```

```dart
// Flutter - BottomNavigationBar 样式
BottomNavigationBar(
  currentIndex: _currentIndex,
  onTap: (index) => setState(() => _currentIndex = index),
  selectedItemColor: Colors.blue,
  unselectedItemColor: Colors.grey,
  backgroundColor: Colors.white,
  type: BottomNavigationBarType.fixed,  // 固定模式
  items: [...],
)

// TabBar 样式
TabBar(
  labelColor: Colors.blue,
  unselectedLabelColor: Colors.grey,
  indicatorColor: Colors.blue,
  indicatorWeight: 2,
  tabs: [...],
)
```

### 3.4 Tab 图标

```objc
// Objective-C
UITabBarItem *item = [[UITabBarItem alloc] initWithTitle:@"Home"
                                                   image:[UIImage systemImageNamed:@"house"]
                                                     tag:0];
// 或带选中图标
UITabBarItem *item = [[UITabBarItem alloc] initWithTitle:@"Home"
                                                   image:[UIImage systemImageNamed:@"house"]
                                             selectedImage:[UIImage systemImageNamed:@"house.fill"]];
```

```dart
// Flutter - BottomNavigationBarItem
BottomNavigationBarItem(
  icon: Icon(Icons.home_outlined),
  activeIcon: Icon(Icons.home),
  label: 'Home',
)

// TabBar 图标
Tab(
  icon: Icon(Icons.home),
  text: 'Home',
)
```

### 3.5 Tab 数量限制

```objc
// Objective-C - 通常 3-5 个
// 超过 5 个会自动显示 "More" 按钮
```

```dart
// Flutter - BottomNavigationBar
// 3-4 个用 fixed，5 个用 shifting
BottomNavigationBar(
  type: _items.length > 4 
      ? BottomNavigationBarType.shifting 
      : BottomNavigationBarType.fixed,
  items: [...],
)
```

### 3.6 带导航的 Tab

```objc
// Objective-C - 每个 Tab 内嵌 UINavigationController
UINavigationController *nav = [[UINavigationController alloc] initWithRootViewController:home];
```

```dart
// Flutter - 使用 Navigator
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Navigator(
      onGenerateRoute: (settings) {
        return MaterialPageRoute(
          builder: (context) => HomeContent(),
        );
      },
    );
  }
}
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 完整的 Tab 应用
@interface HomeViewController : UIViewController
@end

@implementation HomeViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    self.title = @"Home";
    self.view.backgroundColor = [UIColor systemBackgroundColor];
    
    UILabel *label = [[UILabel alloc] init];
    label.text = @"Home Page";
    label.center = self.view.center;
    [self.view addSubview:label];
}
@end

// AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    UITabBarController *tabBar = [[UITabBarController alloc] init];
    tabBar.tabBar.barTintColor = [UIColor systemBackgroundColor];
    tabBar.tabBar.tintColor = [UIColor systemBlueColor];
    
    // Home
    HomeViewController *home = [[HomeViewController alloc] init];
    UINavigationController *homeNav = [[UINavigationController alloc] initWithRootViewController:home];
    homeNav.tabBarItem = [[UITabBarItem alloc] initWithTitle:@"Home" 
                                                       image:[UIImage systemImageNamed:@"house"] 
                                                         tag:0];
    
    // Search
    SearchViewController *search = [[SearchViewController alloc] init];
    UINavigationController *searchNav = [[UINavigationController alloc] initWithRootViewController:search];
    searchNav.tabBarItem = [[UITabBarItem alloc] initWithTitle:@"Search" 
                                                        image:[UIImage systemImageNamed:@"magnifyingglass"] 
                                                          tag:1];
    
    // Profile
    ProfileViewController *profile = [[ProfileViewController alloc] init];
    UINavigationController *profileNav = [[UINavigationController alloc] initWithRootViewController:profile];
    profileNav.tabBarItem = [[UITabBarItem alloc] initWithTitle:@"Profile" 
                                                         image:[UIImage systemImageNamed:@"person"] 
                                                           tag:2];
    
    tabBar.viewControllers = @[homeNav, searchNav, profileNav];
    
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    self.window.rootViewController = tabBar;
    [self.window makeKeyAndVisible];
    
    return YES;
}
```

### Flutter 实现

```dart
// 完整的 Tab 应用 - 更简洁
void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Tab Demo',
      theme: ThemeData(
        useMaterial3: true,
        primaryColor: Colors.blue,
      ),
      home: MainPage(),
    );
  }
}

class MainPage extends StatefulWidget {
  @override
  _MainPageState createState() => _MainPageState();
}

class _MainPageState extends State<MainPage> {
  int _currentIndex = 0;

  final _pages = [
    _buildPage('Home', Icons.home),
    _buildPage('Search', Icons.search),
    _buildPage('Profile', Icons.person),
  ];

  static Widget _buildPage(String title, IconData icon) {
    return Scaffold(
      appBar: AppBar(
        title: Text(title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(icon, size: 64),
            SizedBox(height: 16),
            Text('$title Page', style: TextStyle(fontSize: 24)),
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(  // 保持页面状态
        index: _currentIndex,
        children: _pages,
      ),
      bottomNavigationBar: NavigationBar(
        selectedIndex: _currentIndex,
        onDestinationSelected: (index) {
          setState(() {
            _currentIndex = index;
          });
        },
        destinations: [
          NavigationDestination(
            icon: Icon(Icons.home_outlined),
            selectedIcon: Icon(Icons.home),
            label: 'Home',
          ),
          NavigationDestination(
            icon: Icon(Icons.search_outlined),
            selectedIcon: Icon(Icons.search),
            label: 'Search',
          ),
          NavigationDestination(
            icon: Icon(Icons.person_outline),
            selectedIcon: Icon(Icons.person),
            label: 'Profile',
          ),
        ],
      ),
    );
  }
}
```

---

## 5. TabBar + TabBarView 详解

### 使用 TabController

```dart
class TabPage extends StatefulWidget {
  @override
  _TabPageState createState() => _TabPageState();
}

class _TabPageState extends State<TabPage> with SingleTickerProviderStateMixin {
  late TabController _tabController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 3, vsync: this);
    _tabController.addListener(() {
      print('Tab index: ${_tabController.index}');
    });
  }

  @override
  void dispose() {
    _tabController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Tabs'),
        bottom: TabBar(
          controller: _tabController,
          tabs: [
            Tab(text: 'All'),
            Tab(text: 'Active'),
            Tab(text: 'Completed'),
          ],
        ),
      ),
      body: TabBarView(
        controller: _tabController,
        children: [
          AllPage(),
          ActivePage(),
          CompletedPage(),
        ],
      ),
    );
  }
}
```

### DefaultTabController

```dart
// 简化版，不需要手动管理 TabController
DefaultTabController(
  length: 3,
  child: Scaffold(
    appBar: AppBar(
      title: Text('Tabs'),
      bottom: TabBar(
        tabs: [
          Tab(text: 'All'),
          Tab(text: 'Active'),
          Tab(text: 'Completed'),
        ],
      ),
    ),
    body: TabBarView(
      children: [
        AllPage(),
        ActivePage(),
        CompletedPage(),
      ],
    ),
  ),
)
```

---

## 6. 常见陷阱

### 陷阱1：TabController 未释放

```dart
// ❌ 问题：TabController 未释放
class _MyState extends State<MyWidget> with SingleTickerProviderStateMixin {
  late TabController _tabController;
  
  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 3, vsync: this);
  }
  // 没有 dispose
}

// ✅ 正确：释放 TabController
@override
void dispose() {
  _tabController.dispose();
  super.dispose();
}
```

### 陷阱2：BottomNavigationBar 页面重建

```dart
// ❌ 问题：每次切换都重建页面
body: pages[_currentIndex],

// ✅ 解决方案：使用 IndexedStack 保持状态
body: IndexedStack(
  index: _currentIndex,
  children: pages,
),
```

### 陷阱3：TabBar 标签过多

```dart
// ❌ 问题：标签太多导致挤压
TabBar(
  tabs: List.generate(10, (i) => Tab(text: 'Tab $i')),
  // 标签会被压缩得很小
)

// ✅ 解决方案：使用 isScrollable
TabBar(
  isScrollable: true,  // 可滚动
  tabs: List.generate(10, (i) => Tab(text: 'Tab $i')),
)
```

### 陷阱4：type 设置

```dart
// BottomNavigationBarType.fixed - 固定宽度，适合 3-4 个
// BottomNavigationBarType.shifting - 移动效果，适合 5 个

BottomNavigationBar(
  type: BottomNavigationBarType.fixed,  // 3-4 个
  // 或
  type: BottomNavigationBarType.shifting,  // 5 个
  items: [...],
)
```

---

## 7. 学习技巧

### 技巧1：Material 3 NavigationBar

```dart
// Material 3 风格的底部导航
NavigationBar(
  selectedIndex: _currentIndex,
  onDestinationSelected: (index) {
    setState(() => _currentIndex = index);
  },
  destinations: [
    NavigationDestination(
      icon: Icon(Icons.home_outlined),
      selectedIcon: Icon(Icons.home),
      label: 'Home',
    ),
    NavigationDestination(
      icon: Icon(Icons.search_outlined),
      selectedIcon: Icon(Icons.search),
      label: 'Search',
    ),
  ],
)
```

### 技巧2：自定义指示器

```dart
TabBar(
  indicator: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(8),
  ),
  indicatorSize: TabBarIndicatorSize.label,
  tabs: [...],
)
```

### 技巧3：TabBar 与 AppBar 集成

```dart
AppBar(
  title: Text('Title'),
  bottom: TabBar(
    tabs: [...],
  ),
)
// TabBar 会自动放在 AppBar 底部
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UITabBarController          → Scaffold + BottomNavigationBar
UITabBar                    → BottomNavigationBar / TabBar
UITabBarItem                → BottomNavigationBarItem / Tab
viewControllers             -> body + IndexedStack
selectedIndex               → currentIndex
tabBar.tintColor            → selectedItemColor
tabBar.unselectedItemTintColor → unselectedItemColor
UISegmentedControl          → TabBar
```

### 关键差异

| 方面 | UIKit UITabBarController | Flutter TabBar |
|------|--------------------------|----------------|
| **位置** | 底部固定 | 顶部 TabBar / 底部 BottomNavigationBar |
| **页面管理** | viewControllers 数组 | body + IndexedStack |
| **状态保持** | 自动保持 | 需用 IndexedStack |
| **样式** | UITabBar 外观 | BottomNavigationBar 属性 |
| **数量限制** | 自动 More 按钮 | 需手动处理 |

### 最佳实践

1. **底部导航用 BottomNavigationBar** - 主要页面切换
2. **顶部标签用 TabBar** - 分类切换
3. **使用 IndexedStack** - 保持页面状态
4. **Material 3 用 NavigationBar** - 新风格

---

*最后更新: 2026-02-24*

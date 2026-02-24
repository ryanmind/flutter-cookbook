# Navigation vs UINavigationController 对比学习

本文档记录 Flutter Navigation 与 UIKit UINavigationController 的对比学习要点。

---

## 1. Navigation 核心概念

### Flutter 导航方式

Flutter 提供了两种导航方式：

| 导航方式 | 说明 | 使用场景 |
|---------|------|---------|
| `Navigator` | 命令式导航 | 简单应用、页面跳转 |
| `GoRouter` | 声明式路由 | 复杂应用、深链接 |

### Navigation vs UINavigationController 概念对比

| Flutter | UIKit | 说明 |
|---------|-------|------|
| `Navigator.push()` | `pushViewController:animated:` | 压入页面 |
| `Navigator.pop()` | `popViewControllerAnimated:` | 弹出页面 |
| `Navigator.popUntil()` | `popToViewController:` | 弹出到指定页面 |
| `Navigator.pushReplacement()` | `setViewControllers:` | 替换页面 |
| `routes` | Storyboard Segues | 路由表 |
| `onGenerateRoute` | 自定义转场 | 动态路由 |
| `Hero` | 自定义转场动画 | 共享元素过渡 |

---

## 2. 源码分析

### Navigator 类

```dart
class Navigator extends StatefulWidget {
  const Navigator({
    super.key,
    this.pages = const <Page<dynamic>>[],
    this.onPopPage,
    this.onDidRemovePage,
    this.initialRoute,
    this.onGenerateInitialRoutes,
    this.onGenerateRoute,
    this.onUnknownRoute,
    this.observers = const <NavigatorObserver>[],
    this.transitionDelegate = const DefaultTransitionDelegate<dynamic>(),
    this.reportsRouteUpdateToEngine = false,
    this.clipBehavior = Clip.hardEdge,
    this.restorationScopeId,
    this.requestFocus = true,
  });
  
  // 核心方法
  static Future<T?> push<T extends Object?>(BuildContext context, Route<T> route);
  static bool canPop(BuildContext context);
  static void pop<T extends Object?>(BuildContext context, [T? result]);
  static void popUntil(BuildContext context, RoutePredicate predicate);
  static Future<T?> pushNamed<T extends Object?>(BuildContext context, String routeName, {Object? arguments});
}
```

### Route 类层次

```dart
Route (抽象类)
├── PageRoute (页面路由)
│   ├── MaterialPageRoute  // Material 风格转场
│   ├── CupertinoPageRoute // iOS 风格转场
│   └── PageRouteBuilder   // 自定义转场
├── PopupRoute (弹出路由)
│   ├── ModalRoute
│   └── DialogRoute
└── OverlayRoute
```

---

## 3. 与 UINavigationController 详细对比

### 3.1 页面跳转

```objc
// Objective-C - push
DetailViewController *detail = [[DetailViewController alloc] init];
detail.item = item;
[self.navigationController pushViewController:detail animated:YES];
```

```dart
// Flutter - push
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (context) => DetailPage(item: item),
  ),
);

// 或使用命名路由
Navigator.pushNamed(context, '/detail', arguments: item);
```

### 3.2 返回上一页

```objc
// Objective-C - pop
[self.navigationController popViewControllerAnimated:YES];

// 带返回值
[self.navigationController popViewControllerAnimated:YES];
// 返回值通过 delegate 或 block 传递
```

```dart
// Flutter - pop
Navigator.pop(context);

// 带返回值
Navigator.pop(context, result);

// 接收返回值
final result = await Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => DetailPage()),
);
print('Result: $result');
```

### 3.3 返回根页面

```objc
// Objective-C
[self.navigationController popToRootViewControllerAnimated:YES];

// 返回到指定页面
for (UIViewController *vc in self.navigationController.viewControllers) {
    if ([vc isKindOfClass:[HomeViewController class]]) {
        [self.navigationController popToViewController:vc animated:YES];
        break;
    }
}
```

```dart
// Flutter - popUntil
Navigator.popUntil(context, (route) => route.isFirst);

// 返回到指定路由
Navigator.popUntil(context, ModalRoute.withName('/home'));
```

### 3.4 替换当前页面

```objc
// Objective-C
NSMutableArray *viewControllers = [self.navigationController.viewControllers mutableCopy];
[viewControllers removeLastObject];
[viewControllers addObject:newViewController];
self.navigationController.viewControllers = viewControllers;
```

```dart
// Flutter - pushReplacement
Navigator.pushReplacement(
  context,
  MaterialPageRoute(builder: (context) => NewPage()),
);

// 或
Navigator.replace(context, oldRoute: oldRoute, newRoute: newRoute);
```

### 3.5 清空栈并跳转

```objc
// Objective-C
self.navigationController.viewControllers = @[newViewController];
```

```dart
// Flutter - pushAndRemoveUntil
Navigator.pushAndRemoveUntil(
  context,
  MaterialPageRoute(builder: (context) => HomePage()),
  (route) => false,  // 移除所有路由
);
```

### 3.6 导航栏

```objc
// Objective-C
self.navigationItem.title = @"Title";
self.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc] initWithTitle:@"Back" style:UIBarButtonItemStylePlain target:nil action:nil];
self.navigationItem.rightBarButtonItem = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemAdd target:self action:@selector(addTapped)];
self.navigationController.navigationBar.hidden = NO;
```

```dart
// Flutter - AppBar
Scaffold(
  appBar: AppBar(
    title: Text('Title'),
    leading: IconButton(
      icon: Icon(Icons.arrow_back),
      onPressed: () => Navigator.pop(context),
    ),
    actions: [
      IconButton(
        icon: Icon(Icons.add),
        onPressed: () {},
      ),
    ],
  ),
  body: Content(),
)

// 隐藏导航栏
Scaffold(
  appBar: null,
  body: Content(),
)
```

### 3.7 导航栏样式

```objc
// Objective-C
self.navigationController.navigationBar.barTintColor = [UIColor whiteColor];
self.navigationController.navigationBar.tintColor = [UIColor blueColor];
self.navigationController.navigationBar.titleTextAttributes = @{
    NSForegroundColorAttributeName: [UIColor blackColor],
    NSFontAttributeName: [UIFont boldSystemFontOfSize:18]
};
self.navigationController.navigationBar.largeTitleTextAttributes = ...;
```

```dart
// Flutter - Theme
MaterialApp(
  theme: ThemeData(
    appBarTheme: AppBarTheme(
      backgroundColor: Colors.white,
      foregroundColor: Colors.blue,
      titleTextStyle: TextStyle(
        color: Colors.black,
        fontSize: 18,
        fontWeight: FontWeight.bold,
      ),
    ),
  ),
  home: HomePage(),
)

// 单独设置
AppBar(
  backgroundColor: Colors.white,
  foregroundColor: Colors.blue,
  title: Text('Title'),
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// App Delegate - 配置导航控制器
@interface AppDelegate ()
@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    HomeViewController *home = [[HomeViewController alloc] init];
    UINavigationController *nav = [[UINavigationController alloc] initWithRootViewController:home];
    
    // 导航栏样式
    nav.navigationBar.prefersLargeTitles = YES;
    [UINavigationBar appearance].barTintColor = [UIColor systemBackgroundColor];
    
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    self.window.rootViewController = nav;
    self.window.backgroundColor = [UIColor systemBackgroundColor];
    [self.window makeKeyAndVisible];
    
    return YES;
}
@end

// HomeViewController
@interface HomeViewController : UIViewController <UITableViewDataSource, UITableViewDelegate>
@property (nonatomic, strong) UITableView *tableView;
@property (nonatomic, strong) NSArray *items;
@end

@implementation HomeViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    self.title = @"Home";
    self.items = @[@"Item 1", @"Item 2", @"Item 3"];
    
    self.tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:UITableViewStylePlain];
    self.tableView.dataSource = self;
    self.tableView.delegate = self;
    [self.view addSubview:self.tableView];
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.items.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell"];
    if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"Cell"];
    }
    cell.textLabel.text = self.items[indexPath.row];
    return cell;
}

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    [tableView deselectRowAtIndexPath:indexPath animated:YES];
    
    DetailViewController *detail = [[DetailViewController alloc] init];
    detail.item = self.items[indexPath.row];
    detail.onDismiss = ^(NSString *result) {
        NSLog(@"Result: %@", result);
    };
    [self.navigationController pushViewController:detail animated:YES];
}
@end

// DetailViewController
@interface DetailViewController : UIViewController
@property (nonatomic, copy) NSString *item;
@property (nonatomic, copy) void (^onDismiss)(NSString *result);
@end

@implementation DetailViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    self.title = @"Detail";
    self.view.backgroundColor = [UIColor systemBackgroundColor];
    
    UILabel *label = [[UILabel alloc] init];
    label.text = self.item;
    label.center = self.view.center;
    [self.view addSubview:label];
    
    UIBarButtonItem *doneButton = [[UIBarButtonItem alloc] initWithTitle:@"Done" style:UIBarButtonItemStyleDone target:self action:@selector(doneTapped)];
    self.navigationItem.rightBarButtonItem = doneButton;
}

- (void)doneTapped {
    if (self.onDismiss) {
        self.onDismiss(@"Result from detail");
    }
    [self.navigationController popViewControllerAnimated:YES];
}
@end
```

### Flutter 实现

```dart
// main.dart - 更简洁
void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Navigation Demo',
      theme: ThemeData(
        useMaterial3: true,
        appBarTheme: AppBarTheme(
          backgroundColor: Colors.white,
          foregroundColor: Colors.black,
        ),
      ),
      home: HomePage(),
      // 或使用命名路由
      routes: {
        '/': (context) => HomePage(),
        '/detail': (context) => DetailPage(),
      },
    );
  }
}

// HomePage
class HomePage extends StatelessWidget {
  final items = ['Item 1', 'Item 2', 'Item 3'];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Home'),
      ),
      body: ListView.builder(
        itemCount: items.length,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text(items[index]),
            onTap: () async {
              // 跳转并等待返回值
              final result = await Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => DetailPage(item: items[index]),
                ),
              );
              print('Result: $result');
            },
          );
        },
      ),
    );
  }
}

// DetailPage
class DetailPage extends StatelessWidget {
  final String item;

  const DetailPage({required this.item});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Detail'),
        actions: [
          TextButton(
            onPressed: () {
              Navigator.pop(context, 'Result from detail');
            },
            child: Text('Done'),
          ),
        ],
      ),
      body: Center(
        child: Text(item),
      ),
    );
  }
}
```

---

## 5. 命名路由

### 路由配置

```dart
MaterialApp(
  initialRoute: '/',
  routes: {
    '/': (context) => HomePage(),
    '/detail': (context) => DetailPage(),
    '/settings': (context) => SettingsPage(),
  },
  onGenerateRoute: (settings) {
    // 动态路由
    if (settings.name == '/detail') {
      final item = settings.arguments as String;
      return MaterialPageRoute(
        builder: (context) => DetailPage(item: item),
      );
    }
    return null;
  },
  onUnknownRoute: (settings) {
    // 404 页面
    return MaterialPageRoute(
      builder: (context) => NotFoundPage(),
    );
  },
)
```

### 跳转

```dart
// 带参数跳转
Navigator.pushNamed(
  context,
  '/detail',
  arguments: 'Item 1',
);

// 接收参数
class DetailPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final item = ModalRoute.of(context)!.settings.arguments as String;
    return Scaffold(
      body: Text(item),
    );
  }
}
```

---

## 6. 自定义转场

### 平台风格转场

```dart
// iOS 风格
Navigator.push(
  context,
  CupertinoPageRoute(
    builder: (context) => DetailPage(),
  ),
);

// Material 风格
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (context) => DetailPage(),
  ),
);
```

### 自定义动画

```dart
Navigator.push(
  context,
  PageRouteBuilder(
    pageBuilder: (context, animation, secondaryAnimation) {
      return DetailPage();
    },
    transitionsBuilder: (context, animation, secondaryAnimation, child) {
      return FadeTransition(
        opacity: animation,
        child: child,
      );
    },
    transitionDuration: Duration(milliseconds: 300),
  ),
);
```

### Hero 动画（共享元素）

```dart
// 页面1
Hero(
  tag: 'image-hero',
  child: Image.network('https://example.com/image.jpg'),
)

// 页面2
Hero(
  tag: 'image-hero',  // 相同的 tag
  child: Image.network('https://example.com/image.jpg'),
)
// 跳转时自动执行过渡动画
```

---

## 7. 常见陷阱

### 陷阱1：context 使用错误

```dart
// ❌ 错误：在 build 方法中直接使用 context
@override
Widget build(BuildContext context) {
  Navigator.push(context, ...);  // 每次重建都会跳转
  return Container();
}

// ✅ 正确：在事件回调中使用
ElevatedButton(
  onPressed: () {
    Navigator.push(context, ...);
  },
  child: Text('Go'),
)
```

### 陷阱2：根页面可以 pop

```dart
// 问题：根页面调用 pop 可能退出应用
Navigator.pop(context);  // 根页面时可能退出

// 解决方案：检查是否可以 pop
if (Navigator.canPop(context)) {
  Navigator.pop(context);
}
```

### 陷阱3：异步 push 的 context

```dart
// ❌ 问题：异步后 context 可能失效
onPressed: () async {
  await Future.delayed(Duration(seconds: 1));
  Navigator.push(context, ...);  // context 可能已失效
}

// ✅ 正确：使用 mounted 检查
onPressed: () async {
  await Future.delayed(Duration(seconds: 1));
  if (mounted) {
    Navigator.push(context, ...);
  }
}
```

### 陷阱4：命名路由参数

```dart
// ❌ 错误：arguments 类型不匹配
Navigator.pushNamed(context, '/detail', arguments: 123);

// 接收时类型错误
final String item = ModalRoute.of(context)!.settings.arguments;  // 类型错误

// ✅ 正确：类型一致
Navigator.pushNamed(context, '/detail', arguments: 'Item');

final String item = ModalRoute.of(context)!.settings.arguments as String;
```

---

## 8. 学习技巧

### 技巧1：NavigatorObserver

```dart
class MyNavigatorObserver extends NavigatorObserver {
  @override
  void didPush(Route route, Route? previousRoute) {
    print('Pushed: ${route.settings.name}');
  }

  @override
  void didPop(Route route, Route? previousRoute) {
    print('Popped: ${route.settings.name}');
  }
}

MaterialApp(
  navigatorObservers: [MyNavigatorObserver()],
)
```

### 技巧2：WillPopScope（返回拦截）

```dart
// Flutter 3.12+ 使用 PopScope
PopScope(
  canPop: false,  // 禁止返回
  onPopInvoked: (didPop) {
    if (didPop) return;
    // 显示确认对话框
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('确认退出？'),
        actions: [
          TextButton(onPressed: () => Navigator.pop(context), child: Text('取消')),
          TextButton(onPressed: () => Navigator.pop(context, true), child: Text('确认')),
        ],
      ),
    ).then((confirmed) {
      if (confirmed == true) {
        Navigator.pop(context);
      }
    });
  },
  child: Scaffold(...),
)
```

### 技巧3：使用 GoRouter（推荐）

```dart
// 复杂应用推荐使用 GoRouter
final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => HomePage(),
    ),
    GoRoute(
      path: '/detail/:id',
      builder: (context, state) {
        final id = state.pathParameters['id'];
        return DetailPage(id: id);
      },
    ),
  ],
);

MaterialApp.router(
  routerConfig: router,
)
```

---

## 9. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UINavigationController     → Navigator
pushViewController:animated: → Navigator.push()
popViewControllerAnimated:  → Navigator.pop()
popToRootViewControllerAnimated: → Navigator.popUntil()
viewControllers             → 路由栈（内部管理）
navigationItem              → AppBar
UINavigationBar             → AppBar + Theme
prepareForSegue             → onGenerateRoute
segue.identifier            → 路由名称
```

### 关键差异

| 方面 | UIKit UINavigationController | Flutter Navigator |
|------|------------------------------|-------------------|
| **管理方式** | 视图控制器数组 | 路由栈 |
| **跳转方式** | 命令式 | 命令式 / 声明式 |
| **返回值** | delegate / block | async/await |
| **导航栏** | UINavigationBar | AppBar |
| **转场动画** | 固定 / 自定义 | MaterialPageRoute / CupertinoPageRoute |

### 最佳实践

1. **简单应用用 Navigator** - 基本页面跳转
2. **复杂应用用 GoRouter** - 深链接、嵌套路由
3. **使用命名路由** - 集中管理路由
4. **Hero 动画** - 共享元素过渡效果

---

*最后更新: 2026-02-24*

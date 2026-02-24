# Provider vs iOS 状态管理对比学习

本文档对比 Flutter Provider 与 iOS/Objective-C 状态管理方式的差异。

---

## 1. 核心概念对比

### 状态管理模式

| Flutter Provider | iOS UIKit | 说明 |
|------------------|-----------|------|
| `ChangeNotifier` | KVO / 通知中心 | 数据变化通知机制 |
| `Provider<T>` | 单例 / AppDelegate | 数据提供方式 |
| `Consumer<T>` | 观察者 | 数据消费方式 |
| `context.watch<T>()` | 添加观察者 | 订阅数据 |
| `context.read<T>()` | 获取引用 | 读取数据 |

### 架构对比

```
iOS 架构:
┌─────────────────────────────────────────────────┐
│                   ViewController                │
│  ┌─────────────┐    ┌─────────────────────┐    │
│  │   Views     │←───│  Model / Singleton  │    │
│  └─────────────┘    └─────────────────────┘    │
│         ↑                    ↑                  │
│         └────────────────────┘                  │
│           通知中心 / KVO / 代理                 │
└─────────────────────────────────────────────────┘

Flutter Provider 架构:
┌─────────────────────────────────────────────────┐
│              Widget Tree                        │
│  ┌─────────────┐    ┌─────────────────────┐    │
│  │  Consumer   │←───│ ChangeNotifierProvider    │
│  │  (UI)       │    │  (Model)            │    │
│  └─────────────┘    └─────────────────────┘    │
│         ↑                    ↑                  │
│         └────────────────────┘                  │
│           InheritedWidget (自动)                │
└─────────────────────────────────────────────────┘
```

---

## 2. 基本用法对比

### iOS 单例模式

```objc
// UserManager.h
@interface UserManager : NSObject
+ (instancetype)sharedInstance;
@property (nonatomic, strong) User *currentUser;
@property (nonatomic, assign) BOOL isLoggedIn;
- (void)login:(NSString *)email password:(NSString *)password completion:(void(^)(BOOL success, NSError *error))completion;
- (void)logout;
@end

// UserManager.m
@implementation UserManager

+ (instancetype)sharedInstance {
    static UserManager *instance = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        instance = [[UserManager alloc] init];
    });
    return instance;
}

- (void)login:(NSString *)email password:(NSString *)password completion:(void(^)(BOOL success, NSError *error))completion {
    // 网络请求
    [APIClient loginWithEmail:email password:password completion:^(User *user, NSError *error) {
        if (user) {
            self.currentUser = user;
            self.isLoggedIn = YES;
            // 发送通知
            [[NSNotificationCenter defaultCenter] postNotificationName:@"UserDidLogin" object:nil];
        }
        completion(user != nil, error);
    }];
}

- (void)logout {
    self.currentUser = nil;
    self.isLoggedIn = NO;
    [[NSNotificationCenter defaultCenter] postNotificationName:@"UserDidLogout" object:nil];
}

@end

// ViewController.m - 使用单例
@interface ProfileViewController : UIViewController
@property (nonatomic, strong) UILabel *nameLabel;
@end

@implementation ProfileViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 读取数据
    User *user = [UserManager sharedInstance].currentUser;
    self.nameLabel.text = user.name;
    
    // 监听变化
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(userDidChange:) 
                                                 name:@"UserDidChange" 
                                               object:nil];
}

- (void)userDidChange:(NSNotification *)notification {
    User *user = [UserManager sharedInstance].currentUser;
    self.nameLabel.text = user.name;
}

- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

@end
```

### Flutter Provider 实现

```dart
// 1. 创建数据模型
class UserManager extends ChangeNotifier {
  User? _currentUser;
  bool _isLoggedIn = false;
  
  User? get currentUser => _currentUser;
  bool get isLoggedIn => _isLoggedIn;
  
  Future<void> login(String email, String password) async {
    try {
      final user = await APIClient.login(email: email, password: password);
      _currentUser = user;
      _isLoggedIn = true;
      notifyListeners();  // 通知监听者
    } catch (e) {
      rethrow;
    }
  }
  
  void logout() {
    _currentUser = null;
    _isLoggedIn = false;
    notifyListeners();
  }
}

// 2. 在应用顶层提供数据
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => UserManager(),
      child: MyApp(),
    ),
  );
}

// 3. 消费数据
class ProfilePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // watch：监听变化，自动重建
    final user = context.watch<UserManager>().currentUser;
    
    return Scaffold(
      body: Center(
        child: Text(user?.name ?? 'Guest'),
      ),
    );
  }
}

// 4. 修改数据
class LoginPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: () async {
        // read：不监听，只获取引用
        await context.read<UserManager>().login('test@example.com', 'password');
        // 登录成功后，所有 watch 的 Widget 自动重建
      },
      child: Text('Login'),
    );
  }
}
```

---

## 3. 多状态管理对比

### iOS 多状态管理

```objc
// AppDelegate.h - 全局状态
@interface AppDelegate : UIResponder <UIApplicationDelegate>
@property (nonatomic, strong) User *currentUser;
@property (nonatomic, strong) Theme *theme;
@property (nonatomic, strong) Cart *cart;
@end

// 或使用多个单例
@interface CartManager : NSObject
+ (instancetype)sharedInstance;
@property (nonatomic, strong, readonly) NSArray<CartItem *> *items;
@property (nonatomic, assign, readonly) NSInteger totalCount;
- (void)addItem:(CartItem *)item;
- (void)removeItemAtIndex:(NSInteger)index;
@end

@interface ThemeManager : NSObject
+ (instancetype)sharedInstance;
@property (nonatomic, assign) ThemeMode mode;
@end

// ViewController 中使用
@interface CartViewController : UIViewController
@property (nonatomic, strong) UITableView *tableView;
@end

@implementation CartViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 访问多个状态源
    self.title = [NSString stringWithFormat:@"Cart (%ld)", [CartManager sharedInstance].totalCount];
    
    // 监听多个通知
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(cartDidChange:) 
                                                 name:@"CartDidChange" 
                                               object:nil];
    
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(themeDidChange:) 
                                                 name:@"ThemeDidChange" 
                                               object:nil];
}

- (void)cartDidChange:(NSNotification *)notification {
    [self.tableView reloadData];
    self.title = [NSString stringWithFormat:@"Cart (%ld)", [CartManager sharedInstance].totalCount];
}

- (void)themeDidChange:(NSNotification *)notification {
    [self updateTheme];
}

@end
```

### Flutter MultiProvider

```dart
// 1. 创建多个数据模型
class Cart extends ChangeNotifier {
  final List<CartItem> _items = [];
  
  List<CartItem> get items => List.unmodifiable(_items);
  int get totalCount => _items.fold(0, (sum, item) => sum + item.quantity);
  
  void addItem(CartItem item) {
    _items.add(item);
    notifyListeners();
  }
  
  void removeItemAt(int index) {
    _items.removeAt(index);
    notifyListeners();
  }
}

class ThemeModel extends ChangeNotifier {
  ThemeMode _mode = ThemeMode.system;
  
  ThemeMode get mode => _mode;
  
  void setMode(ThemeMode mode) {
    _mode = mode;
    notifyListeners();
  }
}

// 2. 使用 MultiProvider 组合
void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => UserManager()),
        ChangeNotifierProvider(create: (_) => Cart()),
        ChangeNotifierProvider(create: (_) => ThemeModel()),
      ],
      child: MyApp(),
    ),
  );
}

// 3. 消费多个 Provider
class CartPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // watch 多个 Provider
    final cart = context.watch<Cart>();
    final theme = context.watch<ThemeModel>();
    
    return Scaffold(
      appBar: AppBar(
        title: Text('Cart (${cart.totalCount})'),
      ),
      body: ListView.builder(
        itemCount: cart.items.length,
        itemBuilder: (context, index) {
          return ListTile(title: Text(cart.items[index].name));
        },
      ),
    );
  }
}
```

---

## 4. 依赖注入对比

### iOS 依赖注入

```objc
// 通常使用属性注入或构造器注入
@interface OrderService : NSObject
@property (nonatomic, strong) APIClient *apiClient;
@property (nonatomic, strong) UserManager *userManager;
- (instancetype)initWithAPIClient:(APIClient *)apiClient userManager:(UserManager *)userManager;
@end

// 或者使用全局访问
Order *order = [[Order alloc] init];
order.userId = [UserManager sharedInstance].currentUser.id;
[[APIClient sharedInstance] submitOrder:order completion:^(...) {}];
```

### Flutter ProxyProvider

```dart
// Provider 支持依赖注入
MultiProvider(
  providers: [
    // 基础 Provider
    Provider(create: (_) => APIClient()),
    ChangeNotifierProvider(create: (_) => UserManager()),
    
    // 依赖其他 Provider
    ChangeNotifierProxyProvider<UserManager, OrderService>(
      create: (_) => OrderService.empty(),
      update: (context, userManager, previous) {
        return OrderService(
          apiClient: context.read<APIClient>(),
          userManager: userManager,
        );
      },
    ),
  ],
  child: MyApp(),
)

// OrderService 自动获取依赖
class OrderService extends ChangeNotifier {
  final APIClient apiClient;
  final UserManager userManager;
  
  OrderService({required this.apiClient, required this.userManager});
  
  Future<Order> submitOrder() async {
    return await apiClient.submitOrder(userId: userManager.currentUser!.id);
  }
}
```

---

## 5. 性能优化对比

### iOS 性能优化

```objc
// 问题：通知中心会通知所有观察者
[[NSNotificationCenter defaultCenter] postNotificationName:@"CartDidChange" object:nil];
// 所有监听此通知的 ViewController 都会收到

// 优化：使用 KVO 精确监听
[self.cart addObserver:self 
            forKeyPath:@"items" 
               options:NSKeyValueObservingOptionNew 
               context:NULL];

// 只监听特定属性
- (void)observeValueForKeyPath:(NSString *)keyPath 
                      ofObject:(id)object 
                        change:(NSDictionary *)change 
                       context:(void *)context {
    if ([keyPath isEqualToString:@"items"]) {
        [self.tableView reloadData];
    }
}
```

### Flutter Selector 优化

```dart
// 问题：watch 会监听整个对象变化
final cart = context.watch<Cart>();
// Cart 任何属性变化都会重建

// 优化：使用 Selector 精确监听
class CartItemCount extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 只监听 totalCount，其他变化不触发重建
    final count = context.select<Cart, int>((cart) => cart.totalCount);
    return Text('Items: $count');
  }
}

// 或使用 Selector Widget
class CartTotal extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Selector<Cart, double>(
      selector: (context, cart) => cart.totalPrice,
      builder: (context, total, child) {
        return Text('Total: \$$total');
      },
    );
  }
}

// Consumer 也支持 child 参数
Consumer<Cart>(
  builder: (context, cart, child) {
    return Column(
      children: [
        child!,  // 不变的部分
        Text('Total: ${cart.totalPrice}'),
      ],
    );
  },
  child: Text('Shopping Cart'),  // 不会重建
)
```

---

## 6. 异步数据对比

### iOS 异步数据加载

```objc
@interface UserViewModel : NSObject
@property (nonatomic, strong, readonly) User *user;
@property (nonatomic, assign, readonly) BOOL isLoading;
@property (nonatomic, strong, readonly) NSError *error;
- (void)loadUser;
@end

@implementation UserViewModel

- (void)loadUser {
    self.isLoading = YES;
    [[NSNotificationCenter defaultCenter] postNotificationName:@"UserLoadingDidChange" object:nil];
    
    [[APIClient sharedInstance] fetchUser:^(User *user, NSError *error) {
        self.isLoading = NO;
        if (user) {
            self.user = user;
        } else {
            self.error = error;
        }
        [[NSNotificationCenter defaultCenter] postNotificationName:@"UserDidChange" object:nil];
    }];
}

@end
```

### Flutter 异步 Provider

```dart
// 方式1：在 ChangeNotifier 中处理
class UserModel extends ChangeNotifier {
  User? _user;
  bool _isLoading = false;
  String? _error;
  
  User? get user => _user;
  bool get isLoading => _isLoading;
  String? get error => _error;
  
  Future<void> loadUser() async {
    _isLoading = true;
    _error = null;
    notifyListeners();
    
    try {
      _user = await APIClient.fetchUser();
    } catch (e) {
      _error = e.toString();
    } finally {
      _isLoading = false;
      notifyListeners();
    }
  }
}

// 方式2：使用 FutureProvider
final userProvider = FutureProvider<User>((ref) async {
  return await APIClient.fetchUser();
});

// 使用
class UserPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final asyncUser = context.watch<AsyncValue<User>>();
    
    return asyncUser.when(
      data: (user) => Text('Hello, ${user.name}'),
      loading: () => CircularProgressIndicator(),
      error: (error, stack) => Text('Error: $error'),
    );
  }
}
```

---

## 7. 生命周期对比

### iOS 资源管理

```objc
@interface DataViewController : UIViewController
@property (nonatomic, strong) NSArray *data;
@property (nonatomic, strong) NSTimer *timer;
@end

@implementation DataViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 初始化
    [[NSNotificationCenter defaultCenter] addObserver:self ...];
    self.timer = [NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(update) userInfo:nil repeats:YES];
}

- (void)dealloc {
    // 清理
    [[NSNotificationCenter defaultCenter] removeObserver:self];
    [self.timer invalidate];
}

@end
```

### Flutter Provider 生命周期

```dart
// Provider 自动管理生命周期
class DataModel extends ChangeNotifier {
  Timer? _timer;
  
  DataModel() {
    // 初始化
    _startPolling();
  }
  
  void _startPolling() {
    _timer = Timer.periodic(Duration(seconds: 1), (_) {
      // 更新数据
      notifyListeners();
    });
  }
  
  @override
  void dispose() {
    // 清理资源
    _timer?.cancel();
    super.dispose();
  }
}

// 使用 lazy: false 可立即初始化
ChangeNotifierProvider(
  create: (_) => DataModel(),
  lazy: false,  // 立即创建，不等待首次访问
  child: MyApp(),
)

// 或在 initState 中初始化
class MyPage extends StatefulWidget {
  @override
  _MyPageState createState() => _MyPageState();
}

class _MyPageState extends State<MyPage> {
  @override
  void initState() {
    super.initState();
    // 页面加载时获取数据
    WidgetsBinding.instance.addPostFrameCallback((_) {
      context.read<DataModel>().loadData();
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Consumer<DataModel>(...);
  }
}
```

---

## 8. 测试对比

### iOS 测试

```objc
// 单元测试
- (void)testUserManagerLogin {
    XCTestExpectation *expectation = [self expectationWithDescription:@"Login"];
    
    UserManager *manager = [[UserManager alloc] init];
    [manager login:@"test@example.com" password:@"password" completion:^(BOOL success, NSError *error) {
        XCTAssertTrue(success);
        XCTAssertNotNil(manager.currentUser);
        [expectation fulfill];
    }];
    
    [self waitForExpectationsWithTimeout:5.0 handler:nil];
}

// Mock 测试
@interface MockAPIClient : APIClient
@property (nonatomic, assign) BOOL loginCalled;
@end

@implementation MockAPIClient
- (void)loginWithEmail:(NSString *)email password:(NSString *)password completion:(void(^)(User *, NSError *))completion {
    self.loginCalled = YES;
    completion([[User alloc] initWithName:@"Test"], nil);
}
@end
```

### Flutter Provider 测试

```dart
// 单元测试
void main() {
  test('UserManager login updates state', () async {
    final manager = UserManager();
    
    await manager.login('test@example.com', 'password');
    
    expect(manager.isLoggedIn, true);
    expect(manager.currentUser, isNotNull);
  });
}

// Widget 测试
void main() {
  testWidgets('ProfilePage shows user name', (tester) async {
    await tester.pumpWidget(
      MaterialApp(
        home: ChangeNotifierProvider(
          create: (_) => UserManager()..setMockUser(User(name: 'Test')),
          child: ProfilePage(),
        ),
      ),
    );
    
    expect(find.text('Test'), findsOneWidget);
  });
}

// 使用 Provider 覆盖
testWidgets('with overridden provider', (tester) async {
  await tester.pumpWidget(
    MaterialApp(
      home: ChangeNotifierProvider(
        create: (_) => UserManager(),
        child: ProfilePage(),
      ),
    ),
  );
  
  // 或使用 MultiProvider
  await tester.pumpWidget(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => MockUserManager()),
      ],
      child: MaterialApp(home: ProfilePage()),
    ),
  );
});
```

---

## 9. 完整示例对比

### iOS 完整示例

```objc
// CartManager.h
@interface CartManager : NSObject
+ (instancetype)sharedInstance;
@property (nonatomic, strong, readonly) NSArray<CartItem *> *items;
@property (nonatomic, assign, readonly) NSInteger count;
@property (nonatomic, assign, readonly) double total;
- (void)addItem:(CartItem *)item;
- (void)removeItemAtIndex:(NSInteger)index;
- (void)clear;
@end

// CartManager.m
@interface CartManager ()
@property (nonatomic, strong) NSMutableArray<CartItem *> *mutableItems;
@end

@implementation CartManager

+ (instancetype)sharedInstance {
    static CartManager *instance;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        instance = [[CartManager alloc] init];
    });
    return instance;
}

- (instancetype)init {
    self = [super init];
    if (self) {
        _mutableItems = [NSMutableArray array];
    }
    return self;
}

- (NSArray<CartItem *> *)items {
    return [self.mutableItems copy];
}

- (NSInteger)count {
    return self.mutableItems.count;
}

- (double)total {
    return [self.mutableItems valueForKeyPath:@"@sum.price"].doubleValue;
}

- (void)addItem:(CartItem *)item {
    [self.mutableItems addObject:item];
    [self notifyChange];
}

- (void)removeItemAtIndex:(NSInteger)index {
    [self.mutableItems removeObjectAtIndex:index];
    [self notifyChange];
}

- (void)clear {
    [self.mutableItems removeAllObjects];
    [self notifyChange];
}

- (void)notifyChange {
    [[NSNotificationCenter defaultCenter] postNotificationName:@"CartDidChange" object:nil];
}

@end

// CartViewController.m
@interface CartViewController : UIViewController <UITableViewDataSource, UITableViewDelegate>
@property (nonatomic, strong) UITableView *tableView;
@property (nonatomic, strong) UILabel *totalLabel;
@end

@implementation CartViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:UITableViewStylePlain];
    self.tableView.dataSource = self;
    self.tableView.delegate = self;
    [self.view addSubview:self.tableView];
    
    self.totalLabel = [[UILabel alloc] init];
    [self.view addSubview:self.totalLabel];
    
    [self updateUI];
    
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(cartDidChange) 
                                                 name:@"CartDidChange" 
                                               object:nil];
}

- (void)cartDidChange {
    [self updateUI];
    [self.tableView reloadData];
}

- (void)updateUI {
    CartManager *cart = [CartManager sharedInstance];
    self.totalLabel.text = [NSString stringWithFormat:@"Total: $%.2f", cart.total];
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return [CartManager sharedInstance].items.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
    CartItem *item = [CartManager sharedInstance].items[indexPath.row];
    cell.textLabel.text = item.name;
    return cell;
}

- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

@end
```

### Flutter Provider 完整示例

```dart
// 1. 数据模型
class Cart extends ChangeNotifier {
  final List<CartItem> _items = [];
  
  List<CartItem> get items => List.unmodifiable(_items);
  int get count => _items.length;
  double get total => _items.fold(0, (sum, item) => sum + item.price);
  
  void addItem(CartItem item) {
    _items.add(item);
    notifyListeners();
  }
  
  void removeItemAt(int index) {
    _items.removeAt(index);
    notifyListeners();
  }
  
  void clear() {
    _items.clear();
    notifyListeners();
  }
}

// 2. 入口
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => Cart(),
      child: MyApp(),
    ),
  );
}

// 3. App
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: CartPage(),
    );
  }
}

// 4. 页面
class CartPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Cart')),
      body: Column(
        children: [
          Expanded(
            child: Consumer<Cart>(
              builder: (context, cart, child) {
                return ListView.builder(
                  itemCount: cart.items.length,
                  itemBuilder: (context, index) {
                    final item = cart.items[index];
                    return ListTile(
                      title: Text(item.name),
                      trailing: IconButton(
                        icon: Icon(Icons.delete),
                        onPressed: () => cart.removeItemAt(index),
                      ),
                    );
                  },
                );
              },
            ),
          ),
          TotalBar(),
        ],
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          context.read<Cart>().addItem(CartItem(name: 'Item ${DateTime.now().second}', price: 9.99));
        },
        child: Icon(Icons.add),
      ),
    );
  }
}

// 5. 使用 Selector 优化
class TotalBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final total = context.select<Cart, double>((cart) => cart.total);
    final count = context.select<Cart, int>((cart) => cart.count);
    
    return Container(
      padding: EdgeInsets.all(16),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Text('$count items'),
          Text('Total: \$${total.toStringAsFixed(2)}'),
        ],
      ),
    );
  }
}
```

---

## 10. 核心理念总结

### 关键差异

| 方面 | iOS UIKit | Flutter Provider |
|------|-----------|------------------|
| 数据共享 | 单例/代理/通知中心 | InheritedWidget |
| 变化通知 | NSNotification/KVO | ChangeNotifier |
| 订阅方式 | addObserver | context.watch |
| 取消订阅 | removeObserver | 自动管理 |
| 生命周期 | 手动管理 | 自动管理 |
| 测试友好 | 需要 Mock | 原生支持 |

### iOS 开发者迁移要点

```dart
// iOS → Flutter Provider

// 单例模式
[UserManager sharedInstance].user
→ context.watch<UserManager>().user

// 通知中心
[[NSNotificationCenter defaultCenter] postNotificationName:@"Update" object:nil]
→ notifyListeners()

// 添加观察者
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(update) name:@"Update" object:nil]
→ context.watch<Model>()

// 移除观察者
[[NSNotificationCenter defaultCenter] removeObserver:self]
→ 自动管理，无需手动移除
```

### 最佳实践

1. **Model 继承 ChangeNotifier** - 提供 notifyListeners 方法
2. **顶层提供 Provider** - 在 MaterialApp 之上
3. **watch 用于 UI，read 用于回调** - 精确控制重建
4. **使用 Selector 优化** - 只监听需要的属性
5. **Provider 自动管理生命周期** - 无需手动清理

---

*最后更新: 2026-02-24*

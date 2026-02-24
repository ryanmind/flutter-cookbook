# Riverpod vs iOS 状态管理对比学习

本文档对比 Flutter Riverpod 与 iOS/Objective-C 状态管理方式的差异。

---

## 1. 核心概念对比

### 状态管理模式

| Flutter Riverpod | iOS UIKit | 说明 |
|------------------|-----------|------|
| `Provider` | 单例 | 数据提供 |
| `StateProvider` | 可变单例 + KVO | 简单状态 |
| `StateNotifierProvider` | MVVM ViewModel | 复杂状态 |
| `FutureProvider` | 异步任务 + 回调 | 异步数据 |
| `ref.watch()` | 观察者模式 | 订阅数据 |
| `ref.read()` | 直接访问 | 读取数据 |

### Riverpod 核心优势

```
相比 Provider:
✅ 编译时安全 - 错误在编译时发现，不依赖 BuildContext
✅ 不依赖 BuildContext - 可在任何地方使用
✅ 自动处置 - 不再使用的 Provider 自动清理
✅ 易于测试 - 无需 MockBuildContext
✅ 支持多个相同类型 - 通过名称区分
```

---

## 2. 基本用法对比

### iOS 单例模式

```objc
// iOS 单例 + 通知中心
@interface UserManager : NSObject
+ (instancetype)sharedInstance;
@property (nonatomic, strong) User *currentUser;
@property (nonatomic, assign) BOOL isLoggedIn;
- (void)login:(NSString *)email password:(NSString *)password completion:(void(^)(BOOL))completion;
@end

// 使用
User *user = [UserManager sharedInstance].currentUser;
[[NSNotificationCenter defaultCenter] addObserver:self 
                                         selector:@selector(userDidChange:) 
                                             name:@"UserDidChange" 
                                           object:nil];
```

### Riverpod StateProvider

```dart
// 1. 定义 Provider（全局变量，但安全）
final userProvider = StateProvider<User?>((ref) => null);
final isLoggedInProvider = Provider<bool>((ref) {
  return ref.watch(userProvider) != null;
});

// 2. 使用 - ConsumerWidget
class ProfilePage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // watch：监听变化
    final user = ref.watch(userProvider);
    
    return Text(user?.name ?? 'Guest');
  }
}

// 3. 修改状态
class LoginPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return ElevatedButton(
      onPressed: () async {
        final user = await login('test@example.com', 'password');
        // read：不监听，只修改
        ref.read(userProvider.notifier).state = user;
      },
      child: Text('Login'),
    );
  }
}
```

---

## 3. StateNotifierProvider 对比

### iOS MVVM 模式

```objc
// ViewModel
@interface CounterViewModel : NSObject
@property (nonatomic, assign, readonly) NSInteger count;
@property (nonatomic, assign, readonly) BOOL isIncrementing;
- (void)increment;
- (void)decrement;
- (void)reset;
@end

@interface CounterViewModel ()
@property (nonatomic, assign, readwrite) NSInteger count;
@property (nonatomic, assign, readwrite) BOOL isIncrementing;
@end

@implementation CounterViewModel

- (instancetype)init {
    self = [super init];
    if (self) {
        _count = 0;
        _isIncrementing = NO;
    }
    return self;
}

- (void)increment {
    self.isIncrementing = YES;
    [self notifyChange];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        self.count++;
        self.isIncrementing = NO;
        [self notifyChange];
    });
}

- (void)decrement {
    self.count--;
    [self notifyChange];
}

- (void)reset {
    self.count = 0;
    [self notifyChange];
}

- (void)notifyChange {
    [[NSNotificationCenter defaultCenter] postNotificationName:@"CounterDidChange" object:nil];
}

@end

// ViewController
@interface CounterViewController : UIViewController
@property (nonatomic, strong) CounterViewModel *viewModel;
@property (nonatomic, strong) UILabel *countLabel;
@end

@implementation CounterViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.viewModel = [[CounterViewModel alloc] init];
    
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(updateUI) 
                                                 name:@"CounterDidChange" 
                                               object:nil];
    [self updateUI];
}

- (void)updateUI {
    self.countLabel.text = [NSString stringWithFormat:@"Count: %ld", self.viewModel.count];
    // 处理 loading 状态
}

- (void)increment {
    [self.viewModel increment];
}

@end
```

### Riverpod StateNotifierProvider

```dart
// 1. 定义状态
class CounterState {
  final int count;
  final bool isIncrementing;
  
  const CounterState({
    this.count = 0,
    this.isIncrementing = false,
  });
  
  CounterState copyWith({int? count, bool? isIncrementing}) {
    return CounterState(
      count: count ?? this.count,
      isIncrementing: isIncrementing ?? this.isIncrementing,
    );
  }
}

// 2. 定义 StateNotifier
class CounterNotifier extends StateNotifier<CounterState> {
  CounterNotifier() : super(const CounterState());
  
  Future<void> increment() async {
    state = state.copyWith(isIncrementing: true);
    
    await Future.delayed(Duration(milliseconds: 500));
    
    state = state.copyWith(
      count: state.count + 1,
      isIncrementing: false,
    );
  }
  
  void decrement() {
    state = state.copyWith(count: state.count - 1);
  }
  
  void reset() {
    state = const CounterState();
  }
}

// 3. 定义 Provider
final counterProvider = StateNotifierProvider<CounterNotifier, CounterState>((ref) {
  return CounterNotifier();
});

// 4. 使用
class CounterPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final state = ref.watch(counterProvider);
    
    return Scaffold(
      body: Center(
        child: state.isIncrementing
            ? CircularProgressIndicator()
            : Text('Count: ${state.count}'),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => ref.read(counterProvider.notifier).increment(),
        child: Icon(Icons.add),
      ),
    );
  }
}
```

---

## 4. Provider 组合对比

### iOS 依赖管理

```objc
// 通常通过属性注入
@interface OrderService : NSObject
@property (nonatomic, strong) APIClient *apiClient;
@property (nonatomic, strong) UserManager *userManager;
- (instancetype)initWithAPIClient:(APIClient *)apiClient 
                      userManager:(UserManager *)userManager;
@end

// 或者全局访问
Order *order = [[Order alloc] init];
order.userId = [UserManager sharedInstance].currentUser.id;
[[APIClient sharedInstance] submitOrder:order completion:^(...) {}];
```

### Riverpod Provider 组合

```dart
// 1. 基础 Provider
final apiClientProvider = Provider<APIClient>((ref) {
  return APIClient();
});

final userManagerProvider = StateNotifierProvider<UserManager, UserState>((ref) {
  return UserManager();
});

// 2. 依赖其他 Provider
final orderServiceProvider = Provider<OrderService>((ref) {
  return OrderService(
    apiClient: ref.watch(apiClientProvider),
    userManager: ref.watch(userManagerProvider.notifier),
  );
});

// 3. 使用
class OrderPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final orderService = ref.watch(orderServiceProvider);
    
    return ElevatedButton(
      onPressed: () => orderService.submitOrder(),
      child: Text('Submit Order'),
    );
  }
}
```

---

## 5. 异步数据对比

### iOS 异步数据加载

```objc
@interface UserViewModel : NSObject
@property (nonatomic, strong, readonly) User *user;
@property (nonatomic, assign, readonly) BOOL isLoading;
@property (nonatomic, strong, readonly) NSError *error;
- (void)loadUser;
@end

// ViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(userDidChange) 
                                                 name:@"UserDidChange" 
                                               object:nil];
    [self.viewModel loadUser];
}

- (void)userDidChange {
    if (self.viewModel.isLoading) {
        [self showLoading];
    } else if (self.viewModel.error) {
        [self showError:self.viewModel.error];
    } else {
        [self showUser:self.viewModel.user];
    }
}
```

### Riverpod FutureProvider

```dart
// 1. 定义 FutureProvider
final userProvider = FutureProvider<User>((ref) async {
  return await APIClient.fetchUser();
});

// 2. 使用 - 自动处理加载/错误/数据
class UserPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final asyncUser = ref.watch(userProvider);
    
    return asyncUser.when(
      data: (user) => Text('Hello, ${user.name}'),
      loading: () => CircularProgressIndicator(),
      error: (error, stack) => Text('Error: $error'),
    );
  }
}

// 3. 带参数的 FutureProvider（family）
final userDetailsProvider = FutureProvider.family<User, String>((ref, userId) async {
  return await APIClient.fetchUserById(userId);
});

// 使用
class UserDetails extends ConsumerWidget {
  final String userId;
  
  UserDetails({required this.userId});
  
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final asyncUser = ref.watch(userDetailsProvider(userId));
    // ...
  }
}

// 4. 刷新数据
class RefreshableUserPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return RefreshIndicator(
      onRefresh: () => ref.refresh(userProvider.future),
      child: Consumer(...),
    );
  }
}
```

---

## 6. 不依赖 BuildContext 的优势

### iOS 的优势

```objc
// iOS 可以在任何地方访问单例
@implementation NetworkMonitor
+ (void)startMonitoring {
    [[NetworkManager sharedInstance] start];
}
@end

@implementation BackgroundTask
+ (void)performTask {
    User *user = [UserManager sharedInstance].currentUser;
    // 可以直接访问
}
@end
```

### Provider 的问题

```dart
// Provider 必须依赖 BuildContext
class SomeService {
  void doSomething(BuildContext context) {
    // 必须传入 context
    final user = context.read<UserManager>();
  }
}
```

### Riverpod 解决方案

```dart
// Riverpod 可以在任何地方使用 ProviderContainer
final container = ProviderContainer();

// 全局访问（不推荐，但可以）
final user = container.read(userProvider);

// 更好的方式：在 Widget 中通过 ref
class SomeService {
  final Ref _ref;
  
  SomeService(this._ref);
  
  void doSomething() {
    // 不需要 BuildContext
    final user = _ref.read(userProvider);
  }
}

// 或使用 ProviderScope 的 container
void main() {
  final container = ProviderContainer();
  
  runApp(
    UncontrolledProviderScope(
      container: container,
      child: MyApp(),
    ),
  );
  
  // 可以在 main 函数中访问
  container.read(userProvider);
}
```

---

## 7. 选择性监听对比

### iOS KVO

```objc
// KVO 可以精确监听属性变化
[self.cart addObserver:self 
            forKeyPath:@"totalPrice" 
               options:NSKeyValueObservingOptionNew 
               context:NULL];

- (void)observeValueForKeyPath:(NSString *)keyPath 
                      ofObject:(id)object 
                        change:(NSDictionary *)change 
                       context:(void *)context {
    if ([keyPath isEqualToString:@"totalPrice"]) {
        self.totalLabel.text = [NSString stringWithFormat:@"$%.2f", change[NSKeyValueChangeNewKey]];
    }
}
```

### Riverpod select

```dart
// 使用 select 精确监听
class TotalDisplay extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // 只监听 totalPrice，其他属性变化不触发重建
    final total = ref.watch(cartProvider.select((state) => state.totalPrice));
    
    return Text('\$${total.toStringAsFixed(2)}');
  }
}

// 选择多个属性
class CartSummary extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // 使用 record 选择多个值
    final (:count, :total) = ref.watch(
      cartProvider.select((state) => (count: state.items.length, total: state.totalPrice)),
    );
    
    return Text('$count items - \$${total.toStringAsFixed(2)}');
  }
}
```

---

## 8. 自动处置对比

### iOS 资源管理

```objc
// 必须手动清理
@interface DataViewModel : NSObject
@property (nonatomic, strong) NSTimer *timer;
@end

@implementation DataViewModel

- (instancetype)init {
    self = [super init];
    if (self) {
        _timer = [NSTimer scheduledTimerWithTimeInterval:1.0 
                                                  target:self 
                                                selector:@selector(update) 
                                                userInfo:nil 
                                                 repeats:YES];
    }
    return self;
}

- (void)dealloc {
    // 必须手动清理
    [_timer invalidate];
}

@end
```

### Riverpod 自动处置

```dart
// Riverpod 自动管理生命周期
final dataProvider = StateNotifierProvider<DataNotifier, DataState>((ref) {
  return DataNotifier();
});

class DataNotifier extends StateNotifier<DataState> {
  Timer? _timer;
  
  DataNotifier() : super(DataState.initial()) {
    // ref.onDispose 自动注册清理回调
    _timer = Timer.periodic(Duration(seconds: 1), (_) {
      state = state.copyWith(count: state.count + 1);
    });
  }
  
  @override
  void dispose() {
    _timer?.cancel();
    super.dispose();
  }
}

// 或者使用 ref.onDispose
final streamProvider = StreamProvider<Data>((ref) {
  final controller = StreamController<Data>();
  
  ref.onDispose(() {
    controller.close();  // 自动清理
  });
  
  return controller.stream;
});
```

---

## 9. 测试对比

### iOS 测试

```objc
// 单元测试需要 Mock
@interface MockUserManager : UserManager
@property (nonatomic, assign) BOOL loginCalled;
@end

@implementation MockUserManager
- (void)login:(NSString *)email password:(NSString *)password completion:(void(^)(BOOL))completion {
    self.loginCalled = YES;
    completion(YES);
}
@end

- (void)testLogin {
    MockUserManager *mock = [[MockUserManager alloc] init];
    // 替换单例... 很难做到
    
    XCTestExpectation *expectation = [self expectationWithDescription:@"Login"];
    [mock login:@"test@example.com" password:@"password" completion:^(BOOL success) {
        XCTAssertTrue(success);
        XCTAssertTrue(mock.loginCalled);
        [expectation fulfill];
    }];
    [self waitForExpectationsWithTimeout:5.0 handler:nil];
}
```

### Riverpod 测试

```dart
// 单元测试 - 无需 Mock
void main() {
  test('CounterNotifier increments', () {
    final container = ProviderContainer();
    
    // 获取初始值
    expect(container.read(counterProvider).count, 0);
    
    // 修改状态
    container.read(counterProvider.notifier).increment();
    
    // 验证新值（异步）
    await container.read(counterProvider.notifier).increment();
    expect(container.read(counterProvider).count, 1);
    
    // 清理
    container.dispose();
  });
}

// Widget 测试 - 轻松覆盖 Provider
testWidgets('CounterPage shows count', (tester) async {
  await tester.pumpWidget(
    ProviderScope(
      overrides: [
        // 覆盖 Provider
        counterProvider.overrideWith((ref) => CounterNotifier()),
      ],
      child: MaterialApp(home: CounterPage()),
    ),
  );
  
  expect(find.text('Count: 0'), findsOneWidget);
});

// 使用 Mock
class MockCounterNotifier extends StateNotifier<CounterState> {
  MockCounterNotifier() : super(const CounterState(count: 10));
}

testWidgets('with mock', (tester) async {
  await tester.pumpWidget(
    ProviderScope(
      overrides: [
        counterProvider.overrideWith((ref) => MockCounterNotifier()),
      ],
      child: MaterialApp(home: CounterPage()),
    ),
  );
  
  expect(find.text('Count: 10'), findsOneWidget);  // 显示 Mock 值
});
```

---

## 10. 监听副作用对比

### iOS 回调/代理

```objc
// 使用代理模式
@protocol UserViewModelDelegate <NSObject>
- (void)userViewModelDidLogin:(UserViewModel *)viewModel;
- (void)userViewModel:(UserViewModel *)viewModel didFailWithError:(NSError *)error;
@end

@interface UserViewModel : NSObject
@property (nonatomic, weak) id<UserViewModelDelegate> delegate;
- (void)login:(NSString *)email password:(NSString *)password;
@end

@implementation UserViewModel
- (void)login:(NSString *)email password:(NSString *)password {
    [APIClient loginWithEmail:email password:password completion:^(User *user, NSError *error) {
        if (user) {
            [self.delegate userViewModelDidLogin:self];
        } else {
            [self.delegate userViewModel:self didFailWithError:error];
        }
    }];
}
@end
```

### Riverpod ref.listen

```dart
// 使用 ref.listen 监听副作用
class LoginPage extends ConsumerStatefulWidget {
  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends ConsumerState<LoginPage> {
  @override
  void initState() {
    super.initState();
    
    // 监听状态变化
    ref.listen<AsyncValue<User>>(userProvider, (previous, next) {
      next.when(
        data: (user) {
          // 导航到首页
          Navigator.of(context).pushReplacementNamed('/home');
        },
        loading: () {},
        error: (error, stack) {
          // 显示错误
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('Error: $error')),
          );
        },
      );
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: () => ref.read(userProvider.notifier).login(...),
      child: Text('Login'),
    );
  }
}
```

---

## 11. 完整示例对比

### iOS 完整示例

```objc
// TodoViewModel.h
@interface TodoViewModel : NSObject
@property (nonatomic, copy, readonly) NSArray<Todo *> *todos;
@property (nonatomic, assign, readonly) TodoFilter filter;
@property (nonatomic, assign, readonly) BOOL isLoading;
- (void)loadTodos;
- (void)addTodo:(NSString *)title;
- (void)toggleTodo:(NSString *)id;
- (void)deleteTodo:(NSString *)id;
- (void)setFilter:(TodoFilter)filter;
@end

// TodoViewModel.m
@interface TodoViewModel ()
@property (nonatomic, copy, readwrite) NSArray<Todo *> *todos;
@property (nonatomic, assign, readwrite) TodoFilter filter;
@property (nonatomic, assign, readwrite) BOOL isLoading;
@end

@implementation TodoViewModel

- (instancetype)init {
    self = [super init];
    if (self) {
        _todos = @[];
        _filter = TodoFilterAll;
    }
    return self;
}

- (NSArray<Todo *> *)filteredTodos {
    switch (self.filter) {
        case TodoFilterAll:
            return self.todos;
        case TodoFilterActive:
            return [self.todos filteredArrayUsingPredicate:[NSPredicate predicateWithFormat:@"completed == NO"]];
        case TodoFilterCompleted:
            return [self.todos filteredArrayUsingPredicate:[NSPredicate predicateWithFormat:@"completed == YES"]];
    }
}

- (void)loadTodos {
    self.isLoading = YES;
    [self notifyChange];
    
    [APIClient fetchTodos:^(NSArray<Todo *> *todos, NSError *error) {
        self.isLoading = NO;
        if (todos) {
            self.todos = todos;
        }
        [self notifyChange];
    }];
}

- (void)addTodo:(NSString *)title {
    Todo *todo = [[Todo alloc] initWithId:[[NSUUID UUID] UUIDString] title:title];
    self.todos = [self.todos arrayByAddingObject:todo];
    [self notifyChange];
}

- (void)toggleTodo:(NSString *)id {
    NSMutableArray *mutableTodos = [self.todos mutableCopy];
    for (NSInteger i = 0; i < mutableTodos.count; i++) {
        Todo *todo = mutableTodos[i];
        if ([todo.id isEqualToString:id]) {
            mutableTodos[i] = [todo toggled];
            break;
        }
    }
    self.todos = mutableTodos;
    [self notifyChange];
}

- (void)deleteTodo:(NSString *)id {
    self.todos = [self.todos filteredArrayUsingPredicate:[NSPredicate predicateWithFormat:@"id != %@", id]];
    [self notifyChange];
}

- (void)setFilter:(TodoFilter)filter {
    self.filter = filter;
    [self notifyChange];
}

- (void)notifyChange {
    [[NSNotificationCenter defaultCenter] postNotificationName:@"TodoDidChange" object:nil];
}

@end

// TodoViewController.m
@interface TodoViewController : UIViewController <UITableViewDataSource, UITableViewDelegate>
@property (nonatomic, strong) TodoViewModel *viewModel;
@property (nonatomic, strong) UITableView *tableView;
@property (nonatomic, strong) UISegmentedControl *filterControl;
@end

@implementation TodoViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.viewModel = [[TodoViewModel alloc] init];
    
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(reloadData) 
                                                 name:@"TodoDidChange" 
                                               object:nil];
    
    [self.viewModel loadTodos];
}

- (void)reloadData {
    [self.tableView reloadData];
    [self updateFilterControl];
}

- (IBAction)filterChanged:(UISegmentedControl *)sender {
    [self.viewModel setFilter:sender.selectedSegmentIndex];
}

@end
```

### Riverpod 完整示例

```dart
// 1. 定义状态和状态管理器
class Todo {
  final String id;
  final String title;
  final bool completed;
  
  Todo({required this.id, required this.title, this.completed = false});
  
  Todo toggle() => Todo(id: id, title: title, completed: !completed);
}

enum TodoFilter { all, active, completed }

class TodoState {
  final List<Todo> todos;
  final TodoFilter filter;
  final bool isLoading;
  
  const TodoState({
    this.todos = const [],
    this.filter = TodoFilter.all,
    this.isLoading = false,
  });
  
  List<Todo> get filteredTodos {
    switch (filter) {
      case TodoFilter.all:
        return todos;
      case TodoFilter.active:
        return todos.where((t) => !t.completed).toList();
      case TodoFilter.completed:
        return todos.where((t) => t.completed).toList();
    }
  }
  
  TodoState copyWith({List<Todo>? todos, TodoFilter? filter, bool? isLoading}) {
    return TodoState(
      todos: todos ?? this.todos,
      filter: filter ?? this.filter,
      isLoading: isLoading ?? this.isLoading,
    );
  }
}

class TodoNotifier extends StateNotifier<TodoState> {
  TodoNotifier() : super(const TodoState());
  
  Future<void> loadTodos() async {
    state = state.copyWith(isLoading: true);
    
    final todos = await APIClient.fetchTodos();
    
    state = state.copyWith(todos: todos, isLoading: false);
  }
  
  void addTodo(String title) {
    state = state.copyWith(
      todos: [...state.todos, Todo(id: DateTime.now().toString(), title: title)],
    );
  }
  
  void toggleTodo(String id) {
    state = state.copyWith(
      todos: state.todos.map((t) => t.id == id ? t.toggle() : t).toList(),
    );
  }
  
  void deleteTodo(String id) {
    state = state.copyWith(
      todos: state.todos.where((t) => t.id != id).toList(),
    );
  }
  
  void setFilter(TodoFilter filter) {
    state = state.copyWith(filter: filter);
  }
}

// 2. 定义 Provider
final todoProvider = StateNotifierProvider<TodoNotifier, TodoState>((ref) {
  return TodoNotifier();
});

// 3. UI
class TodoPage extends ConsumerStatefulWidget {
  @override
  _TodoPageState createState() => _TodoPageState();
}

class _TodoPageState extends ConsumerState<TodoPage> {
  @override
  void initState() {
    super.initState();
    // 页面加载时获取数据
    Future.microtask(() => ref.read(todoProvider.notifier).loadTodos());
  }
  
  @override
  Widget build(BuildContext context) {
    final state = ref.watch(todoProvider);
    
    return Scaffold(
      appBar: AppBar(
        title: Text('Todos'),
        bottom: PreferredSize(
          preferredSize: Size.fromHeight(48),
          child: SegmentedButton<TodoFilter>(
            selected: {state.filter},
            onSelectionChanged: (Set<TodoFilter> selected) {
              ref.read(todoProvider.notifier).setFilter(selected.first);
            },
            segments: [
              ButtonSegment(value: TodoFilter.all, label: Text('All')),
              ButtonSegment(value: TodoFilter.active, label: Text('Active')),
              ButtonSegment(value: TodoFilter.completed, label: Text('Done')),
            ],
          ),
        ),
      ),
      body: state.isLoading
          ? Center(child: CircularProgressIndicator())
          : ListView.builder(
              itemCount: state.filteredTodos.length,
              itemBuilder: (context, index) {
                final todo = state.filteredTodos[index];
                return ListTile(
                  leading: Checkbox(
                    value: todo.completed,
                    onChanged: (_) => ref.read(todoProvider.notifier).toggleTodo(todo.id),
                  ),
                  title: Text(
                    todo.title,
                    style: TextStyle(
                      decoration: todo.completed ? TextDecoration.lineThrough : null,
                    ),
                  ),
                  trailing: IconButton(
                    icon: Icon(Icons.delete),
                    onPressed: () => ref.read(todoProvider.notifier).deleteTodo(todo.id),
                  ),
                );
              },
            ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => _showAddDialog(context),
        child: Icon(Icons.add),
      ),
    );
  }
  
  void _showAddDialog(BuildContext context) {
    final controller = TextEditingController();
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('Add Todo'),
        content: TextField(controller: controller),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: Text('Cancel'),
          ),
          ElevatedButton(
            onPressed: () {
              if (controller.text.isNotEmpty) {
                ref.read(todoProvider.notifier).addTodo(controller.text);
                Navigator.pop(context);
              }
            },
            child: Text('Add'),
          ),
        ],
      ),
    );
  }
}
```

---

## 12. 核心理念总结

### 关键差异

| 方面 | iOS UIKit | Flutter Riverpod |
|------|-----------|------------------|
| 依赖 Context | 否 | 否（优势） |
| 编译时安全 | 部分 | 完全 |
| 类型安全 | 运行时 | 编译时 |
| 自动清理 | 手动 | 自动 |
| 测试友好 | 中等 | 高 |
| 多实例支持 | 困难 | 原生支持 |
| 代码生成 | 无 | 可选 |

### Provider vs Riverpod 选择

```dart
// 选择 Provider：
// - 团队熟悉 Provider
// - 项目简单，不需要高级特性
// - 不需要不依赖 BuildContext

// 选择 Riverpod：
// - 需要编译时安全
// - 需要不依赖 BuildContext
// - 需要更好的测试体验
// - 需要多实例支持
```

### iOS 开发者迁移要点

```dart
// iOS → Riverpod

// 单例
[UserManager sharedInstance].user
→ ref.watch(userProvider)

// 通知中心
[[NSNotificationCenter defaultCenter] postNotificationName:@"Update" object:nil]
→ state = newState（StateNotifier 自动通知）

// KVO
[self addObserver:self forKeyPath:@"value" options:0 context:NULL]
→ ref.watch(provider.select((s) => s.value))

// 代理回调
[self.delegate userDidChange:user]
→ ref.listen(userProvider, (prev, next) { ... })
```

---

*最后更新: 2026-02-24*

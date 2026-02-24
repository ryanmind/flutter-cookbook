# BLoC vs iOS 状态管理对比学习

本文档对比 Flutter BLoC 与 iOS/Objective-C 状态管理方式的差异。

---

## 1. 核心概念对比

### 状态管理模式

| Flutter BLoC | iOS UIKit | 说明 |
|--------------|-----------|------|
| `Event` | 用户动作 / 方法调用 | 输入 |
| `State` | 数据模型 | 输出 |
| `Bloc` | ViewModel | 业务逻辑 |
| `BlocBuilder` | 观察者 / 视图更新 | UI 响应 |
| `BlocListener` | 代理回调 / 通知 | 副作用 |
| `emit()` | 更新属性 + 通知 | 状态发射 |

### BLoC 核心理念

```
BLoC = Business Logic Component

核心原则：
1. 输入（Event）和输出（State）完全分离
2. 业务逻辑与 UI 完全解耦
3. 单向数据流：Event → BLoC → State → UI
4. 状态不可变
```

### 架构对比

```
iOS MVC/MVVM:
┌─────────────────────────────────────────────────┐
│                  ViewController                 │
│  ┌─────────────┐    ┌─────────────────────┐    │
│  │   View      │←───│  ViewModel/Model    │    │
│  └─────────────┘    └─────────────────────┘    │
│         ↑                    ↑                  │
│         │    方法调用/属性观察│                  │
│         └────────────────────┘                  │
└─────────────────────────────────────────────────┘

Flutter BLoC:
┌─────────────────────────────────────────────────┐
│                   Widget                        │
│  ┌─────────────┐    ┌─────────────────────┐    │
│  │ BlocBuilder │←───│       State         │    │
│  │    (UI)     │    └─────────────────────┘    │
│  └─────────────┘              ↑                 │
│         │                     │                 │
│         │    Event            │ State           │
│         └─────────────────────┤                 │
│                           ┌───┴───┐             │
│                           │ Bloc  │             │
│                           └───────┘             │
└─────────────────────────────────────────────────┘
```

---

## 2. Cubit vs BLoC 对比

### Cubit（简化版）

```dart
// Cubit：直接调用方法改变状态
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);
  
  void increment() => emit(state + 1);
  void decrement() => emit(state - 1);
}

// 使用
context.read<CounterCubit>().increment();
```

### BLoC（完整版）

```dart
// BLoC：通过事件驱动
abstract class CounterEvent {}
class Increment extends CounterEvent {}
class Decrement extends CounterEvent {}

class CounterBloc extends Bloc<CounterEvent, int> {
  CounterBloc() : super(0) {
    on<Increment>((event, emit) => emit(state + 1));
    on<Decrement>((event, emit) => emit(state - 1));
  }
}

// 使用
context.read<CounterBloc>().add(Increment());
```

### 选择指南

```
Cubit：
✅ 简单状态，方法直接
✅ 样板代码少
✅ 适合小型功能

BLoC：
✅ 复杂业务逻辑
✅ 需要事件追踪
✅ 团队协作，架构严格
✅ 需要强大的调试工具
```

---

## 3. 基本用法对比

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
@property (nonatomic, strong) UIActivityIndicatorView *activityIndicator;
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
    self.activityIndicator.hidden = !self.viewModel.isIncrementing;
}

- (IBAction)incrementTapped {
    [self.viewModel increment];
}

- (IBAction)decrementTapped {
    [self.viewModel decrement];
}

- (IBAction)resetTapped {
    [self.viewModel reset];
}

- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

@end
```

### Flutter BLoC 实现

```dart
// 1. 定义事件
abstract class CounterEvent extends Equatable {
  const CounterEvent();
  
  @override
  List<Object?> get props => [];
}

class Increment extends CounterEvent {}
class Decrement extends CounterEvent {}
class Reset extends CounterEvent {}

// 2. 定义状态
class CounterState extends Equatable {
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
  
  @override
  List<Object?> get props => [count, isIncrementing];
}

// 3. 定义 BLoC
class CounterBloc extends Bloc<CounterEvent, CounterState> {
  CounterBloc() : super(const CounterState()) {
    on<Increment>(_onIncrement);
    on<Decrement>(_onDecrement);
    on<Reset>(_onReset);
  }
  
  Future<void> _onIncrement(Increment event, Emitter<CounterState> emit) async {
    emit(state.copyWith(isIncrementing: true));
    
    await Future.delayed(Duration(milliseconds: 500));
    
    emit(state.copyWith(
      count: state.count + 1,
      isIncrementing: false,
    ));
  }
  
  void _onDecrement(Decrement event, Emitter<CounterState> emit) {
    emit(state.copyWith(count: state.count - 1));
  }
  
  void _onReset(Reset event, Emitter<CounterState> emit) {
    emit(const CounterState());
  }
}

// 4. 提供 BLoC
void main() {
  runApp(
    BlocProvider(
      create: (_) => CounterBloc(),
      child: MyApp(),
    ),
  );
}

// 5. 使用 BLoC
class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: BlocBuilder<CounterBloc, CounterState>(
          builder: (context, state) {
            if (state.isIncrementing) {
              return CircularProgressIndicator();
            }
            return Text(
              'Count: ${state.count}',
              style: Theme.of(context).textTheme.headlineMedium,
            );
          },
        ),
      ),
      floatingActionButton: Column(
        mainAxisAlignment: MainAxisAlignment.end,
        children: [
          FloatingActionButton(
            heroTag: 'inc',
            onPressed: () => context.read<CounterBloc>().add(Increment()),
            child: Icon(Icons.add),
          ),
          SizedBox(height: 10),
          FloatingActionButton(
            heroTag: 'dec',
            onPressed: () => context.read<CounterBloc>().add(Decrement()),
            child: Icon(Icons.remove),
          ),
          SizedBox(height: 10),
          FloatingActionButton(
            heroTag: 'reset',
            onPressed: () => context.read<CounterBloc>().add(Reset()),
            child: Icon(Icons.refresh),
          ),
        ],
      ),
    );
  }
}
```

---

## 4. 事件与状态设计对比

### iOS 状态管理

```objc
// 通常使用枚举或多个属性表示状态
typedef NS_ENUM(NSInteger, AuthState) {
    AuthStateInitial,
    AuthStateLoading,
    AuthStateAuthenticated,
    AuthStateUnauthenticated,
    AuthStateError
};

@interface AuthViewModel : NSObject
@property (nonatomic, assign, readonly) AuthState state;
@property (nonatomic, strong, readonly) User *user;
@property (nonatomic, copy, readonly) NSString *errorMessage;
- (void)login:(NSString *)email password:(NSString *)password;
- (void)logout;
@end
```

### BLoC 事件与状态设计

```dart
// 事件：表示用户意图
abstract class AuthEvent extends Equatable {
  const AuthEvent();
  
  @override
  List<Object?> get props => [];
}

class AuthStarted extends AuthEvent {}
class AuthLoggedIn extends AuthEvent {
  final String email;
  final String password;
  
  const AuthLoggedIn({required this.email, required this.password});
  
  @override
  List<Object?> get props => [email, password];
}

class AuthLoggedOut extends AuthEvent {}

// 状态：使用密封类（Dart 3）表示不同状态
sealed class AuthState extends Equatable {
  const AuthState();
  
  @override
  List<Object?> get props => [];
}

class AuthInitial extends AuthState {}

class AuthLoading extends AuthState {}

class AuthAuthenticated extends AuthState {
  final User user;
  
  const AuthAuthenticated(this.user);
  
  @override
  List<Object?> get props => [user];
}

class AuthUnauthenticated extends AuthState {}

class AuthFailure extends AuthState {
  final String message;
  
  const AuthFailure(this.message);
  
  @override
  List<Object?> get props => [message];
}

// BLoC
class AuthBloc extends Bloc<AuthEvent, AuthState> {
  final AuthRepository authRepository;
  
  AuthBloc({required this.authRepository}) : super(AuthInitial()) {
    on<AuthStarted>(_onStarted);
    on<AuthLoggedIn>(_onLoggedIn);
    on<AuthLoggedOut>(_onLoggedOut);
  }
  
  Future<void> _onStarted(AuthStarted event, Emitter<AuthState> emit) async {
    emit(AuthLoading());
    
    try {
      final user = await authRepository.getCurrentUser();
      if (user != null) {
        emit(AuthAuthenticated(user));
      } else {
        emit(AuthUnauthenticated());
      }
    } catch (e) {
      emit(AuthUnauthenticated());
    }
  }
  
  Future<void> _onLoggedIn(AuthLoggedIn event, Emitter<AuthState> emit) async {
    emit(AuthLoading());
    
    try {
      final user = await authRepository.login(
        email: event.email,
        password: event.password,
      );
      emit(AuthAuthenticated(user));
    } catch (e) {
      emit(AuthFailure(e.toString()));
    }
  }
  
  Future<void> _onLoggedOut(AuthLoggedOut event, Emitter<AuthState> emit) async {
    emit(AuthLoading());
    
    try {
      await authRepository.logout();
      emit(AuthUnauthenticated());
    } catch (e) {
      emit(AuthFailure(e.toString()));
    }
  }
}

// 使用 - 模式匹配
class AuthPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return BlocBuilder<AuthBloc, AuthState>(
      builder: (context, state) {
        return switch (state) {
          AuthInitial() => WelcomePage(),
          AuthLoading() => CircularProgressIndicator(),
          AuthAuthenticated(user: final user) => HomePage(user: user),
          AuthUnauthenticated() => LoginPage(),
          AuthFailure(message: final msg) => ErrorPage(message: msg),
        };
      },
    );
  }
}
```

---

## 5. BlocBuilder vs BlocListener 对比

### iOS 视图更新和回调

```objc
// 视图更新 + 回调处理
@interface LoginViewController : UIViewController
@property (nonatomic, strong) AuthViewModel *viewModel;
@end

@implementation LoginViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(authDidChange:) 
                                                 name:@"AuthDidChange" 
                                               object:nil];
}

- (void)authDidChange:(NSNotification *)notification {
    // 更新 UI
    [self updateUI];
    
    // 处理副作用
    if (self.viewModel.state == AuthStateAuthenticated) {
        [self.navigationController setViewControllers:@[[HomeViewController new]] animated:YES];
    } else if (self.viewModel.state == AuthStateError) {
        [self showErrorAlert:self.viewModel.errorMessage];
    }
}

- (void)updateUI {
    switch (self.viewModel.state) {
        case AuthStateLoading:
            [self.activityIndicator startAnimating];
            break;
        default:
            [self.activityIndicator stopAnimating];
            break;
    }
}

@end
```

### Flutter BlocBuilder + BlocListener

```dart
// BlocBuilder：只负责 UI 重建
class LoginPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: BlocBuilder<AuthBloc, AuthState>(
        builder: (context, state) {
          if (state is AuthLoading) {
            return CircularProgressIndicator();
          }
          return LoginForm();
        },
      ),
    );
  }
}

// BlocListener：处理副作用
class LoginPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: BlocListener<AuthBloc, AuthState>(
        listener: (context, state) {
          // 副作用处理
          if (state is AuthAuthenticated) {
            Navigator.of(context).pushReplacementNamed('/home');
          } else if (state is AuthFailure) {
            ScaffoldMessenger.of(context).showSnackBar(
              SnackBar(content: Text(state.message)),
            );
          }
        },
        child: BlocBuilder<AuthBloc, AuthState>(
          builder: (context, state) {
            if (state is AuthLoading) {
              return CircularProgressIndicator();
            }
            return LoginForm();
          },
        ),
      ),
    );
  }
}

// BlocConsumer：组合使用
class LoginPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: BlocConsumer<AuthBloc, AuthState>(
        listener: (context, state) {
          if (state is AuthAuthenticated) {
            Navigator.of(context).pushReplacementNamed('/home');
          }
        },
        builder: (context, state) {
          if (state is AuthLoading) {
            return CircularProgressIndicator();
          }
          return LoginForm();
        },
      ),
    );
  }
}

// buildWhen：控制重建时机
BlocBuilder<AuthBloc, AuthState>(
  buildWhen: (previous, current) {
    // 只有特定状态变化才重建
    return current is! AuthLoading;
  },
  builder: (context, state) {
    return Text('State: $state');
  },
)
```

---

## 6. 多 BLoC 组合对比

### iOS 多 ViewModel

```objc
@interface AppCoordinator : NSObject
@property (nonatomic, strong) AuthViewModel *authViewModel;
@property (nonatomic, strong) CartViewModel *cartViewModel;
@property (nonatomic, strong) ThemeViewModel *themeViewModel;
@end

@implementation AppCoordinator

- (instancetype)init {
    self = [super init];
    if (self) {
        _authViewModel = [[AuthViewModel alloc] init];
        _cartViewModel = [[CartViewModel alloc] init];
        _themeViewModel = [[ThemeViewModel alloc] init];
        
        // 监听多个通知
        [[NSNotificationCenter defaultCenter] addObserver:self 
                                                 selector:@selector(handleChange:) 
                                                     name:@"AuthDidChange" 
                                                   object:nil];
    }
    return self;
}

@end

// ViewController 使用
@interface HomePage : UIViewController
@end

@implementation HomePage

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 访问多个 ViewModel
    AuthViewModel *auth = [(AppDelegate *)[UIApplication sharedApplication].delegate].authViewModel;
    CartViewModel *cart = [(AppDelegate *)[UIApplication sharedApplication].delegate].cartViewModel;
    
    // 监听多个通知
    [[NSNotificationCenter defaultCenter] addObserver:self ...];
    [[NSNotificationCenter defaultCenter] addObserver:self ...];
}

@end
```

### Flutter MultiBlocProvider

```dart
// 1. 多个 BLoC
void main() {
  runApp(
    MultiBlocProvider(
      providers: [
        BlocProvider(create: (_) => AuthBloc(authRepository: authRepository)),
        BlocProvider(create: (_) => CartBloc()),
        BlocProvider(create: (_) => ThemeBloc()),
      ],
      child: MyApp(),
    ),
  );
}

// 2. 使用多个 BLoC
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: BlocBuilder<AuthBloc, AuthState>(
        builder: (context, authState) {
          return BlocBuilder<CartBloc, CartState>(
            builder: (context, cartState) {
              return Column(
                children: [
                  Text('User: ${authState.user?.name}'),
                  Text('Cart: ${cartState.items.length} items'),
                ],
              );
            },
          );
        },
      ),
    );
  }
}

// 3. RepositoryProvider 配合
MultiRepositoryProvider(
  providers: [
    RepositoryProvider(create: (_) => AuthRepository()),
    RepositoryProvider(create: (_) => CartRepository()),
  ],
  child: MultiBlocProvider(
    providers: [
      BlocProvider(
        create: (context) => AuthBloc(
          authRepository: context.read<AuthRepository>(),
        ),
      ),
      BlocProvider(
        create: (context) => CartBloc(
          cartRepository: context.read<CartRepository>(),
        ),
      ),
    ],
    child: MyApp(),
  ),
)
```

---

## 7. 分层架构对比

### iOS 分层架构

```
┌─────────────────────────────────────────┐
│              View/ViewController         │
├─────────────────────────────────────────┤
│                 ViewModel                │
├─────────────────────────────────────────┤
│                 Service                  │
├─────────────────────────────────────────┤
│               Repository                 │
├─────────────────────────────────────────┤
│              Data Source                 │
│          (API / Database)                │
└─────────────────────────────────────────┘
```

### Flutter BLoC 分层架构

```dart
// 1. 数据源
class UserApi {
  Future<User> fetchUser(String id) async {
    final response = await http.get(Uri.parse('https://api.example.com/users/$id'));
    return User.fromJson(jsonDecode(response.body));
  }
}

// 2. Repository
class UserRepository {
  final UserApi api;
  
  UserRepository(this.api);
  
  Future<User> getUser(String id) => api.fetchUser(id);
}

// 3. BLoC
class UserBloc extends Bloc<UserEvent, UserState> {
  final UserRepository repository;
  
  UserBloc({required this.repository}) : super(UserInitial()) {
    on<UserLoaded>(_onLoaded);
  }
  
  Future<void> _onLoaded(UserLoaded event, Emitter<UserState> emit) async {
    emit(UserLoading());
    
    try {
      final user = await repository.getUser(event.id);
      emit(UserLoadedSuccess(user));
    } catch (e) {
      emit(UserFailure(e.toString()));
    }
  }
}

// 4. UI
class UserPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (context) => UserBloc(
        repository: context.read<UserRepository>(),
      )..add(UserLoaded('123')),
      child: UserView(),
    );
  }
}
```

---

## 8. 测试对比

### iOS 测试

```objc
// ViewModel 测试
- (void)testLogin {
    XCTestExpectation *expectation = [self expectationWithDescription:@"Login"];
    
    AuthViewModel *viewModel = [[AuthViewModel alloc] init];
    
    [viewModel login:@"test@example.com" password:@"password"];
    
    // 等待异步
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        XCTAssertEqual(viewModel.state, AuthStateAuthenticated);
        XCTAssertNotNil(viewModel.user);
        [expectation fulfill];
    });
    
    [self waitForExpectationsWithTimeout:5.0 handler:nil];
}

// Mock
@interface MockAuthRepository : AuthRepository
@property (nonatomic, assign) BOOL loginCalled;
@end

@implementation MockAuthRepository
- (void)loginWithEmail:(NSString *)email password:(NSString *)password completion:(void(^)(User *, NSError *))completion {
    self.loginCalled = YES;
    completion([[User alloc] initWithName:@"Test"], nil);
}
@end
```

### Flutter BLoC 测试

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  group('AuthBloc', () {
    late AuthBloc authBloc;
    late MockAuthRepository mockRepository;
    
    setUp(() {
      mockRepository = MockAuthRepository();
      authBloc = AuthBloc(authRepository: mockRepository);
    });
    
    tearDown(() {
      authBloc.close();
    });
    
    test('initial state is AuthInitial', () {
      expect(authBloc.state, AuthInitial());
    });
    
    blocTest<AuthBloc, AuthState>(
      'emits [AuthLoading, AuthAuthenticated] when login succeeds',
      build: () {
        when(() => mockRepository.login(email: any(named: 'email'), password: any(named: 'password')))
            .thenAnswer((_) async => User(name: 'Test'));
        return authBloc;
      },
      act: (bloc) => bloc.add(AuthLoggedIn(email: 'test@example.com', password: 'password')),
      wait: Duration(milliseconds: 500),
      expect: () => [
        AuthLoading(),
        AuthAuthenticated(User(name: 'Test')),
      ],
      verify: (_) {
        verify(() => mockRepository.login(email: 'test@example.com', password: 'password')).called(1);
      },
    );
    
    blocTest<AuthBloc, AuthState>(
      'emits [AuthLoading, AuthFailure] when login fails',
      build: () {
        when(() => mockRepository.login(email: any(named: 'email'), password: any(named: 'password')))
            .thenThrow(Exception('Network error'));
        return authBloc;
      },
      act: (bloc) => bloc.add(AuthLoggedIn(email: 'test@example.com', password: 'password')),
      expect: () => [
        AuthLoading(),
        isA<AuthFailure>(),
      ],
    );
  });
}

// Widget 测试
testWidgets('LoginPage shows loading indicator', (tester) async {
  await tester.pumpWidget(
    MaterialApp(
      home: BlocProvider(
        create: (_) => AuthBloc(authRepository: MockAuthRepository()),
        child: LoginPage(),
      ),
    ),
  );
  
  // 触发登录
  await tester.tap(find.text('Login'));
  await tester.pump();
  
  // 验证 loading 显示
  expect(find.byType(CircularProgressIndicator), findsOneWidget);
});
```

---

## 9. 调试工具对比

### iOS 调试

```objc
// 日志输出
- (void)login:(NSString *)email password:(NSString *)password {
    NSLog(@"[AuthViewModel] Login started with email: %@", email);
    
    [self.api loginWithEmail:email password:password completion:^(User *user, NSError *error) {
        if (user) {
            NSLog(@"[AuthViewModel] Login success: %@", user.name);
            self.user = user;
            self.state = AuthStateAuthenticated;
        } else {
            NSLog(@"[AuthViewModel] Login failed: %@", error);
            self.state = AuthStateError;
        }
        [self notifyChange];
    }];
}

// 断点调试
// 使用 Xcode 断点
```

### Flutter BLoC 调试

```dart
// 1. 使用 BlocObserver 追踪所有事件和状态
class AppBlocObserver extends BlocObserver {
  @override
  void onCreate(BlocBase bloc) {
    super.onCreate(bloc);
    print('Created: ${bloc.runtimeType}');
  }
  
  @override
  void onEvent(Bloc bloc, Object? event) {
    super.onEvent(bloc, event);
    print('Event: ${bloc.runtimeType}, $event');
  }
  
  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    print('Transition: ${bloc.runtimeType}');
    print('  Current: ${transition.currentState}');
    print('  Event: ${transition.event}');
    print('  Next: ${transition.nextState}');
  }
  
  @override
  void onError(BlocBase bloc, Object error, StackTrace stackTrace) {
    super.onError(bloc, error, stackTrace);
    print('Error: ${bloc.runtimeType}, $error');
  }
  
  @override
  void onClose(BlocBase bloc) {
    super.onClose(bloc);
    print('Closed: ${bloc.runtimeType}');
  }
}

void main() {
  Bloc.observer = AppBlocObserver();
  runApp(MyApp());
}

// 2. 使用 bloc_devtools 扩展
// 安装 DevTools 扩展后，可以可视化查看：
// - 事件流
// - 状态变化
// - 时间旅行调试
```

---

## 10. 完整示例对比

### iOS 完整示例

```objc
// TodoViewModel.h
@interface TodoViewModel : NSObject
@property (nonatomic, copy, readonly) NSArray<Todo *> *todos;
@property (nonatomic, assign, readonly) TodoFilter filter;
@property (nonatomic, assign, readonly) BOOL isLoading;
@property (nonatomic, copy, readonly) NSString *errorMessage;
- (void)loadTodos;
- (void)addTodo:(NSString *)title;
- (void)toggleTodo:(NSString *)id;
- (void)deleteTodo:(NSString *)id;
- (void)setFilter:(TodoFilter)filter;
@end

// TodoViewModel.m
typedef NS_ENUM(NSInteger, TodoFilter) {
    TodoFilterAll,
    TodoFilterActive,
    TodoFilterCompleted
};

@interface TodoViewModel ()
@property (nonatomic, copy, readwrite) NSArray<Todo *> *todos;
@property (nonatomic, assign, readwrite) TodoFilter filter;
@property (nonatomic, assign, readwrite) BOOL isLoading;
@property (nonatomic, copy, readwrite) NSString *errorMessage;
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
    self.errorMessage = nil;
    [self notifyChange];
    
    [self.api fetchTodos:^(NSArray<Todo *> *todos, NSError *error) {
        self.isLoading = NO;
        if (todos) {
            self.todos = todos;
        } else {
            self.errorMessage = error.localizedDescription;
        }
        [self notifyChange];
    }];
}

- (void)addTodo:(NSString *)title {
    Todo *todo = [[Todo alloc] initWithId:[[NSUUID UUID] UUIDString] title:title completed:NO];
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
@interface TodoViewController : UIViewController <UITableViewDataSource>
@property (nonatomic, strong) TodoViewModel *viewModel;
@property (nonatomic, strong) UITableView *tableView;
@property (nonatomic, strong) UISegmentedControl *filterControl;
@end

@implementation TodoViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.viewModel = [[TodoViewModel alloc] init];
    
    [self setupViews];
    [self setupFilterControl];
    
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(reloadData) 
                                                 name:@"TodoDidChange" 
                                               object:nil];
    
    [self.viewModel loadTodos];
}

- (void)setupViews {
    self.tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:UITableViewStylePlain];
    self.tableView.dataSource = self;
    self.tableView.registerClass:[UITableViewCell class] forCellReuseIdentifier:@"Cell"];
    [self.view addSubview:self.tableView];
}

- (void)setupFilterControl {
    self.filterControl = [[UISegmentedControl alloc] initWithItems:@[@"All", @"Active", @"Completed"]];
    self.filterControl.selectedSegmentIndex = 0;
    [self.filterControl addTarget:self action:@selector(filterChanged:) forControlEvents:UIControlEventValueChanged];
    self.navigationItem.titleView = self.filterControl;
}

- (void)reloadData {
    [self.tableView reloadData];
    
    if (self.viewModel.isLoading) {
        // 显示 loading
    } else if (self.viewModel.errorMessage) {
        // 显示错误
    }
}

- (void)filterChanged:(UISegmentedControl *)sender {
    [self.viewModel setFilter:sender.selectedSegmentIndex];
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.viewModel.filteredTodos.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
    Todo *todo = self.viewModel.filteredTodos[indexPath.row];
    cell.textLabel.text = todo.title;
    cell.accessoryType = todo.completed ? UITableViewCellAccessoryCheckmark : UITableViewCellAccessoryNone;
    return cell;
}

- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

@end
```

### Flutter BLoC 完整示例

```dart
// 1. 事件
abstract class TodoEvent extends Equatable {
  const TodoEvent();
  
  @override
  List<Object?> get props => [];
}

class TodoStarted extends TodoEvent {}
class TodoAdded extends TodoEvent {
  final String title;
  const TodoAdded(this.title);
  @override List<Object?> get props => [title];
}
class TodoToggled extends TodoEvent {
  final String id;
  const TodoToggled(this.id);
  @override List<Object?> get props => [id];
}
class TodoDeleted extends TodoEvent {
  final String id;
  const TodoDeleted(this.id);
  @override List<Object?> get props => [id];
}
class TodoFilterChanged extends TodoEvent {
  final TodoFilter filter;
  const TodoFilterChanged(this.filter);
  @override List<Object?> get props => [filter];
}

// 2. 状态
class Todo {
  final String id;
  final String title;
  final bool completed;
  
  Todo({required this.id, required this.title, this.completed = false});
  
  Todo toggle() => Todo(id: id, title: title, completed: !completed);
}

enum TodoFilter { all, active, completed }

class TodoState extends Equatable {
  final List<Todo> todos;
  final TodoFilter filter;
  final bool isLoading;
  final String? errorMessage;
  
  const TodoState({
    this.todos = const [],
    this.filter = TodoFilter.all,
    this.isLoading = false,
    this.errorMessage,
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
  
  TodoState copyWith({
    List<Todo>? todos,
    TodoFilter? filter,
    bool? isLoading,
    String? errorMessage,
  }) {
    return TodoState(
      todos: todos ?? this.todos,
      filter: filter ?? this.filter,
      isLoading: isLoading ?? this.isLoading,
      errorMessage: errorMessage,
    );
  }
  
  @override
  List<Object?> get props => [todos, filter, isLoading, errorMessage];
}

// 3. BLoC
class TodoBloc extends Bloc<TodoEvent, TodoState> {
  final TodoRepository repository;
  
  TodoBloc({required this.repository}) : super(const TodoState()) {
    on<TodoStarted>(_onStarted);
    on<TodoAdded>(_onAdded);
    on<TodoToggled>(_onToggled);
    on<TodoDeleted>(_onDeleted);
    on<TodoFilterChanged>(_onFilterChanged);
  }
  
  Future<void> _onStarted(TodoStarted event, Emitter<TodoState> emit) async {
    emit(state.copyWith(isLoading: true));
    
    try {
      final todos = await repository.getTodos();
      emit(state.copyWith(todos: todos, isLoading: false));
    } catch (e) {
      emit(state.copyWith(isLoading: false, errorMessage: e.toString()));
    }
  }
  
  Future<void> _onAdded(TodoAdded event, Emitter<TodoState> emit) async {
    try {
      final todo = await repository.addTodo(event.title);
      emit(state.copyWith(todos: [...state.todos, todo]));
    } catch (e) {
      emit(state.copyWith(errorMessage: e.toString()));
    }
  }
  
  Future<void> _onToggled(TodoToggled event, Emitter<TodoState> emit) async {
    try {
      await repository.toggleTodo(event.id);
      final todos = state.todos.map((t) {
        return t.id == event.id ? t.toggle() : t;
      }).toList();
      emit(state.copyWith(todos: todos));
    } catch (e) {
      emit(state.copyWith(errorMessage: e.toString()));
    }
  }
  
  Future<void> _onDeleted(TodoDeleted event, Emitter<TodoState> emit) async {
    try {
      await repository.deleteTodo(event.id);
      final todos = state.todos.where((t) => t.id != event.id).toList();
      emit(state.copyWith(todos: todos));
    } catch (e) {
      emit(state.copyWith(errorMessage: e.toString()));
    }
  }
  
  void _onFilterChanged(TodoFilterChanged event, Emitter<TodoState> emit) {
    emit(state.copyWith(filter: event.filter));
  }
}

// 4. UI
class TodoPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Todos'),
        bottom: PreferredSize(
          preferredSize: Size.fromHeight(48),
          child: BlocBuilder<TodoBloc, TodoState>(
            buildWhen: (prev, curr) => prev.filter != curr.filter,
            builder: (context, state) {
              return SegmentedButton<TodoFilter>(
                selected: {state.filter},
                onSelectionChanged: (Set<TodoFilter> selected) {
                  context.read<TodoBloc>().add(TodoFilterChanged(selected.first));
                },
                segments: [
                  ButtonSegment(value: TodoFilter.all, label: Text('All')),
                  ButtonSegment(value: TodoFilter.active, label: Text('Active')),
                  ButtonSegment(value: TodoFilter.completed, label: Text('Done')),
                ],
              );
            },
          ),
        ),
      ),
      body: BlocBuilder<TodoBloc, TodoState>(
        builder: (context, state) {
          if (state.isLoading) {
            return Center(child: CircularProgressIndicator());
          }
          
          if (state.errorMessage != null) {
            return Center(child: Text('Error: ${state.errorMessage}'));
          }
          
          if (state.filteredTodos.isEmpty) {
            return Center(child: Text('No todos'));
          }
          
          return ListView.builder(
            itemCount: state.filteredTodos.length,
            itemBuilder: (context, index) {
              final todo = state.filteredTodos[index];
              return ListTile(
                leading: Checkbox(
                  value: todo.completed,
                  onChanged: (_) {
                    context.read<TodoBloc>().add(TodoToggled(todo.id));
                  },
                ),
                title: Text(
                  todo.title,
                  style: TextStyle(
                    decoration: todo.completed ? TextDecoration.lineThrough : null,
                  ),
                ),
                trailing: IconButton(
                  icon: Icon(Icons.delete),
                  onPressed: () {
                    context.read<TodoBloc>().add(TodoDeleted(todo.id));
                  },
                ),
              );
            },
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
                context.read<TodoBloc>().add(TodoAdded(controller.text));
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

## 11. 核心理念总结

### 关键差异

| 方面 | iOS UIKit | Flutter BLoC |
|------|-----------|--------------|
| 架构模式 | MVC/MVVM | BLoC |
| 数据流 | 双向绑定 | 单向数据流 |
| 状态变化 | 方法调用 | 事件触发 |
| 状态通知 | KVO/通知中心 | BlocBuilder |
| 副作用处理 | 代理/回调 | BlocListener |
| 测试 | 中等 | 高 |
| 调试工具 | Xcode | DevTools + BlocObserver |

### iOS 开发者迁移要点

```dart
// iOS → BLoC

// ViewModel 方法
[self.viewModel login:email password:password]
→ context.read<AuthBloc>().add(AuthLoggedIn(email: email, password: password))

// 状态属性
self.viewModel.user
→ state.user（在 BlocBuilder 中）

// 状态变化通知
[[NSNotificationCenter defaultCenter] postNotificationName:@"Change" object:nil]
→ emit(newState)

// 观察状态
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(update) name:@"Change" object:nil]
→ BlocBuilder(builder: (context, state) { ... })

// 代理回调
[self.delegate userDidChange:user]
→ BlocListener(listener: (context, state) { ... })
```

### 最佳实践

1. **事件命名**：使用过去式（LoggedIn、ItemAdded）
2. **状态不可变**：使用 Equatable 和 copyWith
3. **状态设计**：使用密封类表示不同状态
4. **分层架构**：UI → BLoC → Repository → Data Source
5. **测试驱动**：使用 bloc_test 进行测试

---

*最后更新: 2026-02-24*

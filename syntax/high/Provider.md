# Flutter Provider 状态管理 - 初学者指南

## 目录
1. [Provider 简介](#provider-简介)
2. [基础用法](#基础用法)
3. [Provider 类型](#provider-类型)
4. [Consumer 与Selector](#consumer-与selector)
5. [多Provider 组合](#多provider-组合)
6. [异步数据加载](#异步数据加载)
7. [最佳实践](#最佳实践)

## Provider 简介

### 什么是 Provider

Provider 是 Flutter 官方推荐的状态管理方案，基于 InheritedWidget 封装，简化了状态管理的使用。

```yaml
# pubspec.yaml
dependencies:
  provider: ^6.1.0
```

### 核心概念

```dart
// 1. Model - 数据模型
class Counter extends ChangeNotifier {
  int _count = 0;
  int get count => _count;
  
  void increment() {
    _count++;
    notifyListeners();  // 通知 UI 更新
  }
}

// 2. Provider - 提供数据
ChangeNotifierProvider(
  create: (_) => Counter(),
  child: MyApp(),
)

// 3. Consumer - 消费数据
Consumer<Counter>(
  builder: (context, counter, child) {
    return Text('${counter.count}');
  },
)
```

## 基础用法

### 创建数据模型

```dart
import 'package:flutter/foundation.dart';

// 方式 1：使用 ChangeNotifier
class Counter extends ChangeNotifier {
  int _count = 0;
  
  int get count => _count;
  
  void increment() {
    _count++;
    notifyListeners();  // 通知监听者
  }
  
  void decrement() {
    _count--;
    notifyListeners();
  }
  
  void reset() {
    _count = 0;
    notifyListeners();
  }
}

// 方式 2：使用 ChangeNotifierProxy（依赖其他 Provider）
class UserCart extends ChangeNotifier {
  final User user;
  List<Item> _items = [];
  
  UserCart(this.user);
  
  List<Item> get items => _items;
  
  void addItem(Item item) {
    _items.add(item);
    notifyListeners();
  }
}
```

### 提供数据

```dart
void main() {
  runApp(
    // 在应用顶层提供数据
    ChangeNotifierProvider(
      create: (context) => Counter(),
      child: MyApp(),
    ),
  );
}

// 或者使用 MultiProvider
void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => Counter()),
        ChangeNotifierProvider(create: (_) => User()),
        ChangeNotifierProvider(create: (_) => Cart()),
      ],
      child: MyApp(),
    ),
  );
}
```

### 消费数据

```dart
// 方式 1：context.watch（整个 Widget 重建）
class CounterDisplay extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final counter = context.watch<Counter>();
    return Text('Count: ${counter.count}');
  }
}

// 方式 2：context.read（不监听，只获取）
class IncrementButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return FloatingActionButton(
      onPressed: () => context.read<Counter>().increment(),
      child: Icon(Icons.add),
    );
  }
}

// 方式 3：Consumer（精确控制重建范围）
class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      body: Center(
        child: Consumer<Counter>(
          builder: (context, counter, child) {
            return Text('Count: ${counter.count}');
          },
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => context.read<Counter>().increment(),
        child: Icon(Icons.add),
      ),
    );
  }
}
```

## Provider 类型

### Provider

```dart
// 最基础的 Provider，用于不可变数据
Provider<String>(
  create: (_) => 'Hello, Provider!',
  child: Builder(
    builder: (context) {
      final message = context.watch<String>();
      return Text(message);
    },
  ),
)
```

### ChangeNotifierProvider

```dart
// 最常用，用于可变数据
ChangeNotifierProvider<Counter>(
  create: (_) => Counter(),
  child: MyApp(),
)
```

### StateNotifierProvider（provider 4.0+）

```dart
// 使用 StateNotifier，更现代的方式
class CounterNotifier extends StateNotifier<int> {
  CounterNotifier() : super(0);
  
  void increment() => state++;
  void decrement() => state--;
}

// 提供
StateNotifierProvider<CounterNotifier, int>(
  create: (_) => CounterNotifier(),
  child: MyApp(),
)

// 使用
class CounterDisplay extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final count = context.watch<int>();  // 直接获取状态值
    return Text('Count: $count');
  }
}

class IncrementButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return FloatingActionButton(
      onPressed: () => context.read<CounterNotifier>().increment(),
      child: Icon(Icons.add),
    );
  }
}
```

### FutureProvider

```dart
// 处理异步数据
FutureProvider<User>(
  create: (_) => fetchUser(),
  initialData: User.empty(),
  child: UserProfile(),
)

// 使用
class UserProfile extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final user = context.watch<User?>();
    if (user == null) {
      return CircularProgressIndicator();
    }
    return Text('User: ${user.name}');
  }
}
```

### StreamProvider

```dart
// 处理流数据
StreamProvider<int>(
  create: (_) => counterStream(),
  initialData: 0,
  child: CounterDisplay(),
)
```

### ProxyProvider

```dart
// 依赖其他 Provider
MultiProvider(
  providers: [
    ChangeNotifierProvider(create: (_) => User()),
    ChangeNotifierProxyProvider<User, UserCart>(
      create: (_) => UserCart(User.empty()),
      update: (context, user, previous) {
        return UserCart(user);
      },
    ),
  ],
  child: MyApp(),
)
```

## Consumer 与Selector

### Consumer

```dart
// Consumer 会重建 builder 内的Widget
class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Counter')),  // 不会重建
      body: Consumer<Counter>(
        builder: (context, counter, child) {
          // 只有这里会重建
          return Text('Count: ${counter.count}');
        },
        // child 参数用于不变的部分
        child: Text('This never changes'),
      ),
    );
  }
}

// Consumer2, Consumer3... 消费多个 Provider
Consumer2<Counter, User>(
  builder: (context, counter, user, child) {
    return Text('${user.name}: ${counter.count}');
  },
)
```

### Selector（性能优化）

```dart
// Selector 可以精确控制何时重建
class UserAvatar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 只有当 avatarUrl 变化时才重建
    return Selector<User, String>(
      selector: (context, user) => user.avatarUrl,
      builder: (context, avatarUrl, child) {
        return CircleAvatar(backgroundImage: NetworkImage(avatarUrl));
      },
    );
  }
}

// Selector 组合多个值
class UserInfo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Selector<User, ({String name, String email})>(
      selector: (context, user) => (name: user.name, email: user.email),
      builder: (context, info, child) {
        return Column(
          children: [
            Text(info.name),
            Text(info.email),
          ],
        );
      },
    );
  }
}

// 使用 shouldRebuild 自定义重建逻辑
Selector<User, String>(
  selector: (context, user) => user.name,
  shouldRebuild: (previous, next) => previous != next,
  builder: (context, name, child) {
    return Text(name);
  },
)
```

### watch vs read vs select

```dart
class Example extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // watch：监听变化，变化时重建
    final counter = context.watch<Counter>();
    
    // read：不监听，只获取引用（用于回调）
    // 不会触发重建
    context.read<Counter>().increment();
    
    // select：精确监听某个属性
    final count = context.select<Counter, int>((c) => c.count);
    
    return Text('$count');
  }
}
```

## 多Provider 组合

### MultiProvider

```dart
void main() {
  runApp(
    MultiProvider(
      providers: [
        // 基础 Provider
        Provider(create: (_) => ApiService()),
        Provider(create: (_) => DatabaseService()),
        
        // ChangeNotifierProvider
        ChangeNotifierProvider(create: (_) => AuthProvider()),
        ChangeNotifierProvider(create: (_) => ThemeProvider()),
        
        // 依赖其他 Provider
        ChangeNotifierProxyProvider<AuthProvider, CartProvider>(
          create: (_) => CartProvider(),
          update: (context, auth, previous) {
            return CartProvider(userId: auth.userId);
          },
        ),
        
        // 异步 Provider
        FutureProvider(create: (_) => loadConfig()),
        StreamProvider(create: (_) => connectionStream()),
      ],
      child: MyApp(),
    ),
  );
}
```

### 嵌套 Provider

```dart
// 有时需要嵌套而非并列
ChangeNotifierProvider(
  create: (_) => AuthProvider(),
  child: Builder(
    builder: (context) {
      final auth = context.watch<AuthProvider>();
      
      // 根据 auth 状态提供不同的 Provider
      if (auth.isAuthenticated) {
        return ChangeNotifierProvider(
          create: (_) => UserProfileProvider(auth.userId),
          child: HomePage(),
        );
      }
      
      return LoginPage();
    },
  ),
)
```

## 异步数据加载

### 初始化异步数据

```dart
class DataProvider extends ChangeNotifier {
  Data? _data;
  bool _isLoading = false;
  String? _error;
  
  Data? get data => _data;
  bool get isLoading => _isLoading;
  String? get error => _error;
  
  Future<void> loadData() async {
    _isLoading = true;
    _error = null;
    notifyListeners();
    
    try {
      _data = await fetchData();
    } catch (e) {
      _error = e.toString();
    } finally {
      _isLoading = false;
      notifyListeners();
    }
  }
}

// 在 Widget 中使用
class DataPage extends StatefulWidget {
  @override
  _DataPageState createState() => _DataPageState();
}

class _DataPageState extends State<DataPage> {
  @override
  void initState() {
    super.initState();
    // 页面加载时获取数据
    WidgetsBinding.instance.addPostFrameCallback((_) {
      context.read<DataProvider>().loadData();
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Consumer<DataProvider>(
      builder: (context, provider, child) {
        if (provider.isLoading) {
          return CircularProgressIndicator();
        }
        if (provider.error != null) {
          return Text('Error: ${provider.error}');
        }
        return Text('Data: ${provider.data}');
      },
    );
  }
}
```

### 使用 FutureProvider

```dart
// 简单场景可以直接用 FutureProvider
FutureProvider<Data?>(
  create: (_) => fetchData(),
  initialData: null,
  catchError: (context, error) => null,
  child: DataDisplay(),
)

class DataDisplay extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final data = context.watch<Data?>();
    
    if (data == null) {
      return CircularProgressIndicator();
    }
    
    return Text('Data: ${data.value}');
  }
}
```

### 刷新数据

```dart
class DataProvider extends ChangeNotifier {
  // ... 其他代码
  
  Future<void> refresh() async {
    await loadData();
  }
}

// 在 Widget 中使用 RefreshIndicator
class DataPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RefreshIndicator(
      onRefresh: () => context.read<DataProvider>().refresh(),
      child: Consumer<DataProvider>(
        builder: (context, provider, child) {
          return ListView(
            children: [
              Text('Data: ${provider.data}'),
            ],
          );
        },
      ),
    );
  }
}
```

## 最佳实践

### 1. 分离 UI 和业务逻辑

```dart
// ❌ 错误：业务逻辑混在 UI 中
class BadCounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return FloatingActionButton(
      onPressed: () {
        final counter = context.read<Counter>();
        counter._count++;  // 直接修改数据
        counter.notifyListeners();  // 手动通知
      },
    );
  }
}

// ✅ 正确：业务逻辑在 Model 中
class Counter extends ChangeNotifier {
  int _count = 0;
  int get count => _count;
  
  void increment() {
    _count++;
    notifyListeners();
  }
}

class GoodCounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return FloatingActionButton(
      onPressed: () => context.read<Counter>().increment(),
      child: Icon(Icons.add),
    );
  }
}
```

### 2. 使用 Selector 优化性能

```dart
// ❌ 低效：整个 Widget 重建
class UserCard extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final user = context.watch<User>();
    return Text(user.name);
  }
}

// ✅ 高效：只在 name 变化时重建
class UserCard extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final name = context.select<User, String>((user) => user.name);
    return Text(name);
  }
}
```

### 3. 避免在 build 中使用 read

```dart
// ❌ 错误：在 build 中调用 read
class BadPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    context.read<Counter>().increment();  // 会频繁调用！
    return Text('Hello');
  }
}

// ✅ 正确：在事件回调中使用 read
class GoodPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: () => context.read<Counter>().increment(),
      child: Text('Increment'),
    );
  }
}
```

### 4. 使用 ChangeNotifierProxyProvider 处理依赖

```dart
// 当一个 Provider 依赖另一个 Provider 时
MultiProvider(
  providers: [
    ChangeNotifierProvider(create: (_) => AuthProvider()),
    ChangeNotifierProxyProvider<AuthProvider, OrderProvider>(
      create: (_) => OrderProvider.empty(),
      update: (context, auth, previous) {
        return OrderProvider(
          userId: auth.userId,
          apiService: context.read<ApiService>(),
        );
      },
    ),
  ],
  child: MyApp(),
)
```

### 5. 正确处理生命周期

```dart
class MyProvider extends ChangeNotifier {
  Timer? _timer;
  
  MyProvider() {
    _startTimer();
  }
  
  void _startTimer() {
    _timer = Timer.periodic(Duration(seconds: 1), (_) {
      // 更新状态
      notifyListeners();
    });
  }
  
  // 记得释放资源
  @override
  void dispose() {
    _timer?.cancel();
    super.dispose();
  }
}

// Provider 会自动调用 dispose
```

### 6. 测试 Provider

```dart
// 单元测试
void main() {
  test('Counter increment', () {
    final counter = Counter();
    counter.increment();
    expect(counter.count, 1);
  });
}

// Widget 测试
void main() {
  testWidgets('Counter display', (tester) async {
    await tester.pumpWidget(
      MaterialApp(
        home: ChangeNotifierProvider(
          create: (_) => Counter()..increment(),
          child: CounterDisplay(),
        ),
      ),
    );
    
    expect(find.text('Count: 1'), findsOneWidget);
  });
}
```

## 完整示例

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

// 1. 数据模型
class Counter extends ChangeNotifier {
  int _count = 0;
  int get count => _count;
  
  void increment() {
    _count++;
    notifyListeners();
  }
  
  void decrement() {
    _count--;
    notifyListeners();
  }
}

// 2. 入口
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => Counter(),
      child: MyApp(),
    ),
  );
}

// 3. App
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: CounterPage(),
    );
  }
}

// 4. 页面
class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            // 使用 Consumer 精确控制重建
            Consumer<Counter>(
              builder: (context, counter, child) {
                return Text(
                  'Count: ${counter.count}',
                  style: Theme.of(context).textTheme.headlineMedium,
                );
              },
            ),
            SizedBox(height: 20),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                ElevatedButton(
                  onPressed: () => context.read<Counter>().decrement(),
                  child: Icon(Icons.remove),
                ),
                SizedBox(width: 20),
                ElevatedButton(
                  onPressed: () => context.read<Counter>().increment(),
                  child: Icon(Icons.add),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }
}
```

---

*最后更新: 2026-02-19*

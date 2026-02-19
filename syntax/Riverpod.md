# Flutter Riverpod 状态管理 - 初学者指南

## 目录
1. [Riverpod 简介](#riverpod-简介)
2. [基础 Provider](#基础-provider)
3. [StateProvider](#stateprovider)
4. [StateNotifierProvider](#statenotifierprovider)
5. [FutureProvider 与 StreamProvider](#futureprovider-与-streamprovider)
6. [Provider 组合](#provider-组合)
7. [Consumer Widget](#consumer-widget)
8. [最佳实践](#最佳实践)

## Riverpod 简介

### 什么是 Riverpod

Riverpod 是 Provider 的下一代状态管理方案，由同一作者开发。它解决了 Provider 的一些限制：

```yaml
# pubspec.yaml
dependencies:
  flutter_riverpod: ^2.4.0
```

### 核心优势

```dart
// ✅ 编译时安全 - 错误在编译时发现
// ✅ 不依赖 BuildContext - 可以在任何地方使用
// ✅ 自动处置 - 不再使用的 Provider 自动清理
// ✅ 易于测试 - 无需 MockBuildContext
// ✅ 支持多个相同类型 Provider - 通过名称区分
```

### 基本概念

```dart
// Provider - 数据源的定义
final counterProvider = StateProvider<int>((ref) => 0);

// Consumer - 消费数据
Consumer(builder: (context, ref, child) {
  final count = ref.watch(counterProvider);
  return Text('$count');
})

// ProviderScope - 应用的根容器
ProviderScope(child: MyApp())
```

## 基础 Provider

### Provider（只读数据）

```dart
// 定义只读 Provider
final nameProvider = Provider<String>((ref) {
  return 'Flutter';
});

// 定义带依赖的 Provider
final greetingProvider = Provider<String>((ref) {
  final name = ref.watch(nameProvider);
  return 'Hello, $name!';
});
```

### 使用 Provider

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

// 方式 1：ConsumerWidget
class GreetingWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final greeting = ref.watch(greetingProvider);
    return Text(greeting);
  }
}

// 方式 2：Consumer（在StatefulWidget 中）
class MyWidget extends StatefulWidget {
  @override
  _MyWidgetState createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> {
  @override
  Widget build(BuildContext context) {
    return Consumer(
      builder: (context, ref, child) {
        final greeting = ref.watch(greetingProvider);
        return Text(greeting);
      },
    );
  }
}

// 方式 3：ConsumerWidget + ref.watch
class MyPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return Scaffold(
      body: Center(
        child: Text(ref.watch(greetingProvider)),
      ),
    );
  }
}
```

## StateProvider

### 基本用法

```dart
// 定义 StateProvider
final counterProvider = StateProvider<int>((ref) => 0);

// 使用
class CounterPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // watch：监听变化，变化时重建
    final count = ref.watch(counterProvider);
    
    return Scaffold(
      body: Center(
        child: Text('Count: $count'),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          // read：不监听，用于修改状态
          ref.read(counterProvider.notifier).state++;
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```

### 状态更新方式

```dart
// 方式 1：直接赋值
ref.read(counterProvider.notifier).state = 10;

// 方式 2：使用 update
ref.read(counterProvider.notifier).update((state) => state + 1);

// 方式 3：在回调中修改
void increment(WidgetRef ref) {
  ref.read(counterProvider.notifier).state++;
}
```

### 复杂状态示例

```dart
// 主题状态
final themeProvider = StateProvider<ThemeMode>((ref) => ThemeMode.system);

// 使用
class ThemeToggle extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final themeMode = ref.watch(themeProvider);
    
    return DropdownButton<ThemeMode>(
      value: themeMode,
      items: [
        DropdownMenuItem(value: ThemeMode.system, child: Text('跟随系统')),
        DropdownMenuItem(value: ThemeMode.light, child: Text('浅色')),
        DropdownMenuItem(value: ThemeMode.dark, child: Text('深色')),
      ],
      onChanged: (mode) {
        if (mode != null) {
          ref.read(themeProvider.notifier).state = mode;
        }
      },
    );
  }
}
```

## StateNotifierProvider

### 创建 StateNotifier

```dart
// 定义状态类
class Todo {
  final String id;
  final String title;
  final bool completed;
  
  Todo({
    required this.id,
    required this.title,
    this.completed = false,
  });
  
  Todo copyWith({String? id, String? title, bool? completed}) {
    return Todo(
      id: id ?? this.id,
      title: title ?? this.title,
      completed: completed ?? this.completed,
    );
  }
}

// 定义 StateNotifier
class TodoNotifier extends StateNotifier<List<Todo>> {
  TodoNotifier() : super([]);
  
  void add(String title) {
    state = [
      ...state,
      Todo(id: DateTime.now().toString(), title: title),
    ];
  }
  
  void toggle(String id) {
    state = [
      for (final todo in state)
        if (todo.id == id)
          todo.copyWith(completed: !todo.completed)
        else
          todo,
    ];
  }
  
  void remove(String id) {
    state = state.where((todo) => todo.id != id).toList();
  }
  
  void clearCompleted() {
    state = state.where((todo) => !todo.completed).toList();
  }
}

// 定义 Provider
final todoProvider = StateNotifierProvider<TodoNotifier, List<Todo>>((ref) {
  return TodoNotifier();
});
```

### 使用 StateNotifier

```dart
class TodoList extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final todos = ref.watch(todoProvider);
    
    return ListView.builder(
      itemCount: todos.length,
      itemBuilder: (context, index) {
        final todo = todos[index];
        return ListTile(
          leading: Checkbox(
            value: todo.completed,
            onChanged: (_) {
              ref.read(todoProvider.notifier).toggle(todo.id);
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
              ref.read(todoProvider.notifier).remove(todo.id);
            },
          ),
        );
      },
    );
  }
}

class AddTodo extends ConsumerStatefulWidget {
  @override
  _AddTodoState createState() => _AddTodoState();
}

class _AddTodoState extends ConsumerState<AddTodo> {
  final _controller = TextEditingController();
  
  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Expanded(
          child: TextField(
            controller: _controller,
            decoration: InputDecoration(hintText: '输入待办事项'),
          ),
        ),
        IconButton(
          icon: Icon(Icons.add),
          onPressed: () {
            if (_controller.text.isNotEmpty) {
              ref.read(todoProvider.notifier).add(_controller.text);
              _controller.clear();
            }
          },
        ),
      ],
    );
  }
  
  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
}
```

### 不可变状态更新

```dart
// ✅ 正确：创建新的状态对象
class UserNotifier extends StateNotifier<User> {
  UserNotifier() : super(User.initial());
  
  void updateName(String name) {
    state = state.copyWith(name: name);  // 创建新对象
  }
  
  void updateAge(int age) {
    state = state.copyWith(age: age);
  }
}

// ❌ 错误：直接修改状态
class BadNotifier extends StateNotifier<User> {
  BadNotifier() : super(User.initial());
  
  void updateName(String name) {
    state.name = name;  // 错误！应该创建新对象
    // state = state;  // 不会触发更新
  }
}
```

## FutureProvider 与 StreamProvider

### FutureProvider

```dart
// 定义 FutureProvider
final userProvider = FutureProvider<User>((ref) async {
  final response = await http.get(Uri.parse('https://api.example.com/user'));
  return User.fromJson(jsonDecode(response.body));
});

// 使用
class UserPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final asyncValue = ref.watch(userProvider);
    
    return asyncValue.when(
      data: (user) => Text('User: ${user.name}'),
      loading: () => CircularProgressIndicator(),
      error: (error, stack) => Text('Error: $error'),
    );
  }
}
```

### 带参数的 FutureProvider

```dart
// 使用 family 创建带参数的 Provider
final userDetailsProvider = FutureProvider.family<User, String>((ref, userId) async {
  final response = await http.get(
    Uri.parse('https://api.example.com/users/$userId'),
  );
  return User.fromJson(jsonDecode(response.body));
});

// 使用
class UserDetails extends ConsumerWidget {
  final String userId;
  
  UserDetails({required this.userId});
  
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final asyncValue = ref.watch(userDetailsProvider(userId));
    
    return asyncValue.when(
      data: (user) => Text('User: ${user.name}'),
      loading: () => CircularProgressIndicator(),
      error: (error, stack) => Text('Error: $error'),
    );
  }
}
```

### StreamProvider

```dart
// 定义 StreamProvider
final messagesProvider = StreamProvider<List<Message>>((ref) {
  return FirebaseFirestore.instance
      .collection('messages')
      .snapshots()
      .map((snapshot) => snapshot.docs
          .map((doc) => Message.fromJson(doc.data()))
          .toList());
});

// 使用
class MessageList extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final asyncValue = ref.watch(messagesProvider);
    
    return asyncValue.when(
      data: (messages) => ListView.builder(
        itemCount: messages.length,
        itemBuilder: (context, index) {
          return ListTile(title: Text(messages[index].content));
        },
      ),
      loading: () => CircularProgressIndicator(),
      error: (error, stack) => Text('Error: $error'),
    );
  }
}
```

### 刷新数据

```dart
class RefreshableUser extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return RefreshIndicator(
      onRefresh: () => ref.refresh(userProvider.future),
      child: Consumer(
        builder: (context, ref, child) {
          final asyncValue = ref.watch(userProvider);
          
          return asyncValue.when(
            data: (user) => ListView(
              children: [Text('User: ${user.name}')],
            ),
            loading: () => CircularProgressIndicator(),
            error: (error, stack) => Text('Error: $error'),
          );
        },
      ),
    );
  }
}
```

## Provider 组合

### Provider 依赖

```dart
// 基础 Provider
final userIdProvider = StateProvider<String?>((ref) => null);

// 依赖其他 Provider
final userProvider = FutureProvider<User?>((ref) async {
  final userId = ref.watch(userIdProvider);
  if (userId == null) return null;
  
  return fetchUser(userId);
});

// 当 userIdProvider 变化时，userProvider 会自动重新获取
```

### 使用 ref.listen

```dart
// 监听状态变化并执行副作用
class MyPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // 监听状态变化
    ref.listen<AsyncValue<User>>(userProvider, (previous, next) {
      next.when(
        data: (user) {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('Welcome, ${user.name}')),
          );
        },
        loading: () {},
        error: (error, stack) {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('Error: $error')),
          );
        },
      );
    });
    
    return Container();
  }
}
```

### 选择性监听

```dart
// 使用 select 减少重建
class UserName extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // 只在 name 变化时重建
    final name = ref.watch(userProvider.select((user) => user?.name));
    return Text(name ?? 'Guest');
  }
}
```

## Consumer Widget

### ConsumerWidget

```dart
// 简单的无状态消费组件
class CounterText extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);
    return Text('Count: $count');
  }
}
```

### ConsumerStatefulWidget

```dart
// 需要生命周期的消费组件
class CounterPage extends ConsumerStatefulWidget {
  @override
  _CounterPageState createState() => _CounterPageState();
}

class _CounterPageState extends ConsumerState<CounterPage> {
  Timer? _timer;
  
  @override
  void initState() {
    super.initState();
    // 使用 ref.read 初始化
    _timer = Timer.periodic(Duration(seconds: 1), (_) {
      ref.read(counterProvider.notifier).state++;
    });
  }
  
  @override
  void dispose() {
    _timer?.cancel();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    final count = ref.watch(counterProvider);
    return Text('Count: $count');
  }
}
```

### Consumer

```dart
// 在普通Widget 中使用
class MyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Static content'),
        Consumer(
          builder: (context, ref, child) {
            final count = ref.watch(counterProvider);
            return Text('Count: $count');
          },
        ),
      ],
    );
  }
}
```

## 最佳实践

### 1. 使用代码生成（可选）

```yaml
dependencies:
  riverpod_annotation: ^2.3.0

dev_dependencies:
  riverpod_generator: ^2.3.0
  build_runner: ^2.4.0
```

```dart
import 'package:riverpod_annotation/riverpod_annotation.dart';

part 'my_provider.g.dart';

@riverpod
class Counter extends _$Counter {
  @override
  int build() => 0;
  
  void increment() => state++;
}

// 自动生成的代码更简洁
```

### 2. Provider 命名规范

```dart
// ✅ 推荐：使用 Provider 后缀
final counterProvider = StateProvider<int>((ref) => 0);
final todoListProvider = StateNotifierProvider<TodoList, List<Todo>>((ref) {
  return TodoList();
});

// ✅ 推荐：使用 family 表示参数化 Provider
final userProfileProvider = FutureProvider.family<User, String>((ref, id) {
  return fetchUser(id);
});
```

### 3. 分层架构

```dart
// 数据层
final apiClientProvider = Provider<ApiClient>((ref) {
  return ApiClient();
});

// 仓库层
final userRepositoryProvider = Provider<UserRepository>((ref) {
  return UserRepository(ref.read(apiClientProvider));
});

// 状态层
final userNotifierProvider = StateNotifierProvider<UserNotifier, AsyncValue<User>>((ref) {
  return UserNotifier(ref.read(userRepositoryProvider));
});

// UI 层
class UserPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final asyncUser = ref.watch(userNotifierProvider);
    // ...
  }
}
```

### 4. 测试

```dart
void main() {
  test('Counter increment', () {
    // 创建测试容器
    final container = ProviderContainer();
    
    // 获取初始值
    expect(container.read(counterProvider), 0);
    
    // 修改状态
    container.read(counterProvider.notifier).state++;
    
    // 验证新值
    expect(container.read(counterProvider), 1);
    
    // 清理
    container.dispose();
  });
}
```

## 完整示例

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

// 1. 定义状态
class Counter extends StateNotifier<int> {
  Counter() : super(0);
  
  void increment() => state++;
  void decrement() => state--;
  void reset() => state = 0;
}

// 2. 定义 Provider
final counterProvider = StateNotifierProvider<Counter, int>((ref) {
  return Counter();
});

// 3. 应用入口
void main() {
  runApp(ProviderScope(child: MyApp()));
}

// 4. App
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: CounterPage(),
    );
  }
}

// 5. 页面
class CounterPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);
    
    return Scaffold(
      appBar: AppBar(title: Text('Riverpod Counter')),
      body: Center(
        child: Text(
          'Count: $count',
          style: Theme.of(context).textTheme.headlineMedium,
        ),
      ),
      floatingActionButton: Column(
        mainAxisAlignment: MainAxisAlignment.end,
        children: [
          FloatingActionButton(
            heroTag: 'inc',
            onPressed: () => ref.read(counterProvider.notifier).increment(),
            child: Icon(Icons.add),
          ),
          SizedBox(height: 10),
          FloatingActionButton(
            heroTag: 'dec',
            onPressed: () => ref.read(counterProvider.notifier).decrement(),
            child: Icon(Icons.remove),
          ),
          SizedBox(height: 10),
          FloatingActionButton(
            heroTag: 'reset',
            onPressed: () => ref.read(counterProvider.notifier).reset(),
            child: Icon(Icons.refresh),
          ),
        ],
      ),
    );
  }
}
```

---

*最后更新: 2026-02-19*

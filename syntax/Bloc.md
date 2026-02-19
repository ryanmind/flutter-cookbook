# Flutter BLoC 状态管理 - 初学者指南

## 目录
1. [BLoC 简介](#bloc-简介)
2. [Cubit 基础](#cubit-基础)
3. [BLoC 基础](#bloc-基础)
4. [BlocBuilder 与 BlocListener](#blocbuilder-与-bloclistener)
5. [事件与状态设计](#事件与状态设计)
6. [最佳实践](#最佳实践)

## BLoC 简介

### 什么是 BLoC

BLoC (Business Logic Component) 是一种基于事件驱动的状态管理模式，将业务逻辑与 UI 完全分离。

```yaml
# pubspec.yaml
dependencies:
  flutter_bloc: ^8.1.0
  equatable: ^2.0.5  # 状态比较
```

### 核心概念

```dart
// Event - 事件（输入）
abstract class CounterEvent {}

// State - 状态（输出）
class CounterState {
  final int count;
  CounterState(this.count);
}

// BLoC - 业务逻辑组件
class CounterBloc extends Bloc<CounterEvent, CounterState> {
  CounterBloc() : super(CounterState(0)) {
    // 处理事件，发射新状态
  }
}
```

### BLoC vs Cubit

```dart
// Cubit：简化版，直接调用方法改变状态
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);
  
  void increment() => emit(state + 1);  // 直接发射
}

// BLoC：完整版，通过事件驱动
class CounterBloc extends Bloc<CounterEvent, int> {
  CounterBloc() : super(0) {
    on<Increment>((event, emit) => emit(state + 1));  // 事件处理
  }
}
```

## Cubit 基础

### 创建 Cubit

```dart
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:equatable/equatable.dart';

// 状态类
class CounterState extends Equatable {
  final int count;
  final bool isIncrementing;
  
  const CounterState({
    this.count = 0,
    this.isIncrementing = false,
  });
  
  CounterState copyWith({
    int? count,
    bool? isIncrementing,
  }) {
    return CounterState(
      count: count ?? this.count,
      isIncrementing: isIncrementing ?? this.isIncrementing,
    );
  }
  
  @override
  List<Object?> get props => [count, isIncrementing];
}

// Cubit
class CounterCubit extends Cubit<CounterState> {
  CounterCubit() : super(const CounterState());
  
  void increment() {
    emit(state.copyWith(isIncrementing: true));
    
    // 模拟异步操作
    Future.delayed(Duration(milliseconds: 500), () {
      emit(state.copyWith(
        count: state.count + 1,
        isIncrementing: false,
      ));
    });
  }
  
  void decrement() {
    if (state.count > 0) {
      emit(state.copyWith(count: state.count - 1));
    }
  }
  
  void reset() {
    emit(const CounterState());
  }
}
```

### 使用 Cubit

```dart
// 提供 Cubit
void main() {
  runApp(
    BlocProvider(
      create: (_) => CounterCubit(),
      child: MyApp(),
    ),
  );
}

// 使用 BlocBuilder
class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      body: BlocBuilder<CounterCubit, CounterState>(
        builder: (context, state) {
          if (state.isIncrementing) {
            return Center(child: CircularProgressIndicator());
          }
          return Center(
            child: Text(
              'Count: ${state.count}',
              style: Theme.of(context).textTheme.headlineMedium,
            ),
          );
        },
      ),
      floatingActionButton: Column(
        mainAxisAlignment: MainAxisAlignment.end,
        children: [
          FloatingActionButton(
            heroTag: 'inc',
            onPressed: () => context.read<CounterCubit>().increment(),
            child: Icon(Icons.add),
          ),
          SizedBox(height: 10),
          FloatingActionButton(
            heroTag: 'dec',
            onPressed: () => context.read<CounterCubit>().decrement(),
            child: Icon(Icons.remove),
          ),
        ],
      ),
    );
  }
}
```

## BLoC 基础

### 定义事件和状态

```dart
// 事件
abstract class AuthEvent extends Equatable {
  const AuthEvent();
  
  @override
  List<Object?> get props => [];
}

class AuthStarted extends AuthEvent {}

class AuthLoggedIn extends AuthEvent {
  final String email;
  final String password;
  
  const AuthLoggedIn({
    required this.email,
    required this.password,
  });
  
  @override
  List<Object?> get props => [email, password];
}

class AuthLoggedOut extends AuthEvent {}

// 状态
abstract class AuthState extends Equatable {
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
```

### 创建 BLoC

```dart
class AuthBloc extends Bloc<AuthEvent, AuthState> {
  final AuthRepository authRepository;
  
  AuthBloc({required this.authRepository}) : super(AuthInitial()) {
    on<AuthStarted>(_onStarted);
    on<AuthLoggedIn>(_onLoggedIn);
    on<AuthLoggedOut>(_onLoggedOut);
  }
  
  Future<void> _onStarted(
    AuthStarted event,
    Emitter<AuthState> emit,
  ) async {
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
  
  Future<void> _onLoggedIn(
    AuthLoggedIn event,
    Emitter<AuthState> emit,
  ) async {
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
  
  Future<void> _onLoggedOut(
    AuthLoggedOut event,
    Emitter<AuthState> emit,
  ) async {
    emit(AuthLoading());
    
    try {
      await authRepository.logout();
      emit(AuthUnauthenticated());
    } catch (e) {
      emit(AuthFailure(e.toString()));
    }
  }
}
```

## BlocBuilder 与 BlocListener

### BlocBuilder

```dart
// 基本用法
BlocBuilder<CounterBloc, CounterState>(
  builder: (context, state) {
    return Text('Count: ${state.count}');
  },
)

// 带条件构建
BlocBuilder<CounterBloc, CounterState>(
  buildWhen: (previous, current) {
    // 只有 count 变化时才重建
    return previous.count != current.count;
  },
  builder: (context, state) {
    return Text('Count: ${state.count}');
  },
)
```

### BlocListener

```dart
// 监听状态变化，执行副作用
BlocListener<AuthBloc, AuthState>(
  listener: (context, state) {
    if (state is AuthAuthenticated) {
      Navigator.of(context).pushReplacementNamed('/home');
    } else if (state is AuthFailure) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text(state.message)),
      );
    }
  },
  child: LoginForm(),
)
```

### BlocConsumer

```dart
// 组合Builder 和 Listener
BlocConsumer<AuthBloc, AuthState>(
  listener: (context, state) {
    if (state is AuthFailure) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text(state.message)),
      );
    }
  },
  builder: (context, state) {
    if (state is AuthLoading) {
      return CircularProgressIndicator();
    }
    return LoginForm();
  },
)
```

### BlocProvider

```dart
// 单个 Provider
BlocProvider(
  create: (_) => AuthBloc(authRepository: authRepository),
  child: LoginPage(),
)

// 多个 Provider
MultiBlocProvider(
  providers: [
    BlocProvider(create: (_) => AuthBloc(authRepository: authRepository)),
    BlocProvider(create: (_) => ThemeBloc()),
    BlocProvider(create: (_) => CartBloc()),
  ],
  child: MyApp(),
)
```

### RepositoryProvider

```dart
// 提供 Repository
RepositoryProvider(
  create: (_) => AuthRepository(),
  child: BlocProvider(
    create: (context) => AuthBloc(
      authRepository: context.read<AuthRepository>(),
    ),
    child: LoginPage(),
  ),
)

// 在 Bloc 中使用
class AuthBloc extends Bloc<AuthEvent, AuthState> {
  final AuthRepository authRepository;
  
  AuthBloc({required this.authRepository}) : super(AuthInitial());
}
```

## 事件与状态设计

### 复杂状态设计

```dart
// 使用密封类（Dart 3）
sealed class TodoState extends Equatable {
  const TodoState();
  
  @override
  List<Object?> get props => [];
}

class TodoInitial extends TodoState {}

class TodoLoading extends TodoState {}

class TodoLoaded extends TodoState {
  final List<Todo> todos;
  final TodoFilter filter;
  
  const TodoLoaded({
    required this.todos,
    this.filter = TodoFilter.all,
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
  
  TodoLoaded copyWith({
    List<Todo>? todos,
    TodoFilter? filter,
  }) {
    return TodoLoaded(
      todos: todos ?? this.todos,
      filter: filter ?? this.filter,
    );
  }
  
  @override
  List<Object?> get props => [todos, filter];
}

class TodoError extends TodoState {
  final String message;
  
  const TodoError(this.message);
  
  @override
  List<Object?> get props => [message];
}

enum TodoFilter { all, active, completed }
```

### 事件设计

```dart
abstract class TodoEvent extends Equatable {
  const TodoEvent();
  
  @override
  List<Object?> get props => [];
}

class TodoStarted extends TodoEvent {}

class TodoAdded extends TodoEvent {
  final String title;
  
  const TodoAdded(this.title);
  
  @override
  List<Object?> get props => [title];
}

class TodoToggled extends TodoEvent {
  final String id;
  
  const TodoToggled(this.id);
  
  @override
  List<Object?> get props => [id];
}

class TodoDeleted extends TodoEvent {
  final String id;
  
  const TodoDeleted(this.id);
  
  @override
  List<Object?> get props => [id];
}

class TodoFilterChanged extends TodoEvent {
  final TodoFilter filter;
  
  const TodoFilterChanged(this.filter);
  
  @override
  List<Object?> get props => [filter];
}

class TodoClearedCompleted extends TodoEvent {}
```

### 完整 BLoC 示例

```dart
class TodoBloc extends Bloc<TodoEvent, TodoState> {
  final TodoRepository todoRepository;
  
  TodoBloc({required this.todoRepository}) : super(TodoInitial()) {
    on<TodoStarted>(_onStarted);
    on<TodoAdded>(_onAdded);
    on<TodoToggled>(_onToggled);
    on<TodoDeleted>(_onDeleted);
    on<TodoFilterChanged>(_onFilterChanged);
    on<TodoClearedCompleted>(_onClearedCompleted);
  }
  
  Future<void> _onStarted(
    TodoStarted event,
    Emitter<TodoState> emit,
  ) async {
    emit(TodoLoading());
    
    try {
      final todos = await todoRepository.getTodos();
      emit(TodoLoaded(todos: todos));
    } catch (e) {
      emit(TodoError(e.toString()));
    }
  }
  
  Future<void> _onAdded(
    TodoAdded event,
    Emitter<TodoState> emit,
  ) async {
    final currentState = state;
    if (currentState is TodoLoaded) {
      try {
        final todo = await todoRepository.addTodo(event.title);
        emit(TodoLoaded(
          todos: [...currentState.todos, todo],
          filter: currentState.filter,
        ));
      } catch (e) {
        emit(TodoError(e.toString()));
      }
    }
  }
  
  Future<void> _onToggled(
    TodoToggled event,
    Emitter<TodoState> emit,
  ) async {
    final currentState = state;
    if (currentState is TodoLoaded) {
      try {
        await todoRepository.toggleTodo(event.id);
        final todos = currentState.todos.map((todo) {
          if (todo.id == event.id) {
            return todo.copyWith(completed: !todo.completed);
          }
          return todo;
        }).toList();
        emit(TodoLoaded(todos: todos, filter: currentState.filter));
      } catch (e) {
        emit(TodoError(e.toString()));
      }
    }
  }
  
  Future<void> _onDeleted(
    TodoDeleted event,
    Emitter<TodoState> emit,
  ) async {
    final currentState = state;
    if (currentState is TodoLoaded) {
      try {
        await todoRepository.deleteTodo(event.id);
        final todos = currentState.todos
            .where((todo) => todo.id != event.id)
            .toList();
        emit(TodoLoaded(todos: todos, filter: currentState.filter));
      } catch (e) {
        emit(TodoError(e.toString()));
      }
    }
  }
  
  void _onFilterChanged(
    TodoFilterChanged event,
    Emitter<TodoState> emit,
  ) {
    final currentState = state;
    if (currentState is TodoLoaded) {
      emit(TodoLoaded(
        todos: currentState.todos,
        filter: event.filter,
      ));
    }
  }
  
  Future<void> _onClearedCompleted(
    TodoClearedCompleted event,
    Emitter<TodoState> emit,
  ) async {
    final currentState = state;
    if (currentState is TodoLoaded) {
      try {
        await todoRepository.clearCompleted();
        final todos = currentState.todos
            .where((todo) => !todo.completed)
            .toList();
        emit(TodoLoaded(todos: todos, filter: currentState.filter));
      } catch (e) {
        emit(TodoError(e.toString()));
      }
    }
  }
}
```

## 最佳实践

### 1. 状态不可变

```dart
// ✅ 正确：使用不可变状态
class UserState extends Equatable {
  final User? user;
  final bool isLoading;
  
  const UserState({this.user, this.isLoading = false});
  
  UserState copyWith({User? user, bool? isLoading}) {
    return UserState(
      user: user ?? this.user,
      isLoading: isLoading ?? this.isLoading,
    );
  }
  
  @override
  List<Object?> get props => [user, isLoading];
}

// ❌ 错误：可变状态
class BadState {
  User? user;
  bool isLoading = false;
}
```

### 2. 使用 Equatable

```dart
// 使用 Equatable 减少不必要的重建
class CounterState extends Equatable {
  final int count;
  
  const CounterState(this.count);
  
  @override
  List<Object?> get props => [count];
}
// 当 count 相同时，BlocBuilder 不会重建
```

### 3. 分层架构

```dart
// UI层 → Bloc → Repository → Data Source

// 数据源
class TodoApi {
  Future<List<Todo>> getTodos() async { /* ... */ }
}

// Repository
class TodoRepository {
  final TodoApi api;
  
  TodoRepository(this.api);
  
  Future<List<Todo>> getTodos() => api.getTodos();
}

// Bloc
class TodoBloc extends Bloc<TodoEvent, TodoState> {
  final TodoRepository repository;
  
  TodoBloc({required this.repository}) : super(TodoInitial());
}

// UI
class TodoPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (_) => TodoBloc(
        repository: context.read<TodoRepository>(),
      ),
      child: TodoList(),
    );
  }
}
```

### 4. 测试

```dart
void main() {
  group('CounterBloc', () {
    late CounterBloc counterBloc;
    
    setUp(() {
      counterBloc = CounterBloc();
    });
    
    tearDown(() {
      counterBloc.close();
    });
    
    test('initial state is 0', () {
      expect(counterBloc.state, CounterState(count: 0));
    });
    
    blocTest<CounterBloc, CounterState>(
      'emits [1] when increment is called',
      build: () => counterBloc,
      act: (bloc) => bloc.add(CounterIncrementPressed()),
      expect: () => [CounterState(count: 1)],
    );
  });
}
```

---

*最后更新: 2026-02-19*

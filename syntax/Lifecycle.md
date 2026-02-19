# Flutter 生命周期 - 初学者指南

## 目录
1. [Widget 生命周期](#widget-生命周期)
2. [App 生命周期](#app-生命周期)
3. [路由生命周期](#路由生命周期)
4. [Stream 与Controller 生命周期](#stream-与controller-生命周期)

## Widget 生命周期

### StatefulWidget 生命周期

```dart
class LifecycleDemo extends StatefulWidget {
  const LifecycleDemo({Key? key}) : super(key: key);
  
  @override
  State<LifecycleDemo> createState() => _LifecycleDemoState();
}

class _LifecycleDemoState extends State<LifecycleDemo> {
  @override
  void initState() {
    super.initState();
    // 1. 初始化状态，只调用一次
    // 此时 Widget 已创建，但还未构建
    print('initState');
  }
  
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    // 2. 依赖变化时调用
    // initState 之后立即调用
    // InheritedWidget 更新时也会调用
    print('didChangeDependencies');
  }
  
  @override
  void didUpdateWidget(LifecycleDemo oldWidget) {
    super.didUpdateWidget(oldWidget);
    // 3. 父组件重建时调用
    // 可以比较新旧 Widget 的差异
    print('didUpdateWidget');
  }
  
  @override
  Widget build(BuildContext context) {
    // 4. 构建 Widget
    // 每次状态变化时都会调用
    print('build');
    return Container();
  }
  
  @override
  void deactivate() {
    super.deactivate();
    // 5. Widget 从树中移除时调用
    // 但可能被重新插入
    print('deactivate');
  }
  
  @override
  void dispose() {
    // 6. Widget 被永久移除时调用
    // 释放资源、取消订阅等
    print('dispose');
    super.dispose();
  }
  
  @override
  void reassemble() {
    super.reassemble();
    // 热重载时调用
    // 仅在开发模式下有用
    print('reassemble');
  }
}
```

### 生命周期图示

```
创建Widget
    │
    ▼
initState()───────────┐
    │                 │
    ▼                 │
didChangeDependencies() │
    │                 │
    ▼                 │
build() ◄─────────────┤
    │                 │
    │setState()       │父组件重建
    ▼                 │
build()◄──────────────┤
    │                 │
    │didUpdateWidget()│
    ▼                 │
build()◄──────────────┤
    │                 │
    ▼                 │
deactivate()          │
    │                 │
    ├─重新插入─────────┘
    │
    ▼
dispose()
    │
    ▼
销毁Widget
```

### 典型用法

```dart
class UserProfile extends StatefulWidget {
  final String userId;
  
  const UserProfile({required this.userId});
  
  @override
  _UserProfileState createState() => _UserProfileState();
}

class _UserProfileState extends State<UserProfile> {
  User? _user;
  bool _isLoading = true;
  StreamSubscription? _subscription;
  
  @override
  void initState() {
    super.initState();
    _loadUser();
  }
  
  @override
  void didUpdateWidget(UserProfile oldWidget) {
    super.didUpdateWidget(oldWidget);
    // userId 变化时重新加载
    if (oldWidget.userId != widget.userId) {
      _loadUser();
    }
  }
  
  Future<void> _loadUser() async {
    setState(() => _isLoading = true);
    
    final user = await fetchUser(widget.userId);
    
    if (mounted) {  // 检查Widget 是否还在树中
      setState(() {
        _user = user;
        _isLoading = false;
      });
    }
  }
  
  @override
  void dispose() {
    _subscription?.cancel();  // 取消订阅
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    if (_isLoading) {
      return CircularProgressIndicator();
    }
    return Text(_user?.name ?? 'Unknown');
  }
}
```

## App 生命周期

### 使用 WidgetsBindingObserver

```dart
class AppLifecycleDemo extends StatefulWidget {
  @override
  _AppLifecycleDemoState createState() => _AppLifecycleDemoState();
}

class _AppLifecycleDemoState extends State<AppLifecycleDemo>
    with WidgetsBindingObserver {
  AppLifecycleState? _lifecycleState;
  
  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
  }
  
  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }
  
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    super.didChangeAppLifecycleState(state);
    setState(() {
      _lifecycleState = state;
    });
    
    switch (state) {
      case AppLifecycleState.resumed:
        // App 进入前台，可见可交互
        print('App resumed');
        break;
      case AppLifecycleState.inactive:
        // App 失去焦点（如来电、下拉通知栏）
        print('App inactive');
        break;
      case AppLifecycleState.paused:
        // App 进入后台，不可见
        print('App paused');
        // 保存数据、暂停任务
        break;
      case AppLifecycleState.detached:
        // App 即将退出
        print('App detached');
        break;
      case AppLifecycleState.hidden:
        // App 完全隐藏
        print('App hidden');
        break;
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text('App State: ${_lifecycleState?.name ?? "unknown"}'),
      ),
    );
  }
}
```

### AppLifecycleState 状态流转

```
┌─────────────────────────────────────────┐
│                                         │
│              resumed                     │
│           (前台运行)                      │
│                 │                        │
│    ┌────────────┼────────────┐          │
│    │            │            │          │
│    ▼            │            ▼          │
│ inactive        │        inactive        │
│ (失去焦点)       │       (切换应用)       │
│    │            │            │          │
│    └────┬───────┘            │          │
│         │                    │          │
│         ▼                    ▼          │
│      hidden               paused         │
│    (完全隐藏)            (后台运行)       │
│         │                    │          │
│         └────────┬───────────┘          │
│                  │                      │
│                  ▼                      │
│              detached                    │
│             (即将退出)                    │
└─────────────────────────────────────────┘
```

### 内存压力回调

```dart
class MemoryAwareWidget extends StatefulWidget {
  @override
  _MemoryAwareWidgetState createState() => _MemoryAwareWidgetState();
}

class _MemoryAwareWidgetState extends State<MemoryAwareWidget>
    with WidgetsBindingObserver {
  
  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
  }
  
  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }
  
  @override
  void didHaveMemoryPressure() {
    // 系统内存不足时调用
    // 释放不必要的资源
    print('Memory pressure received');
    _clearCache();
  }
  
  void _clearCache() {
    // 清理缓存
  }
  
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}
```

## 路由生命周期

### RouteObserver

```dart
// 1. 创建 RouteObserver
final RouteObserver<PageRoute> routeObserver = RouteObserver<PageRoute>();

// 2. 在 MaterialApp 中注册
MaterialApp(
  navigatorObservers: [routeObserver],
  // ...
);

// 3. 在State 中使用 RouteAware
class RouteAwareWidget extends StatefulWidget {
  @override
  _RouteAwareWidgetState createState() => _RouteAwareWidgetState();
}

class _RouteAwareWidgetState extends State<RouteAwareWidget> with RouteAware {
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    routeObserver.subscribe(this, ModalRoute.of(context) as PageRoute);
  }
  
  @override
  void dispose() {
    routeObserver.unsubscribe(this);
    super.dispose();
  }
  
  @override
  void didPush() {
    // 当前页面被推入
    print('didPush: 页面显示');
  }
  
  @override
  void didPopNext() {
    // 上一个页面弹出，当前页面重新显示
    print('didPopNext: 页面重新显示');
    // 刷新数据等
  }
  
  @override
  void didPushNext() {
    // 有新页面推入，当前页面被覆盖
    print('didPushNext: 页面被覆盖');
    // 暂停视频播放等
  }
  
  @override
  void didPop() {
    // 当前页面弹出
    print('didPop: 页面退出');
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Route Aware')),
      body: Center(child: Text('Route Lifecycle Demo')),
    );
  }
}
```

### 路由状态流转

```
页面 A                      页面 B
  │                          │
  │      push(B)             │
  │ ─────────────────────►   │
  │                          │
  │ didPushNext()            │ didPush()
  │ (被覆盖)                  │ (显示)
  │                          │
  │      pop()               │
  │ ◄─────────────────────   │
  │                          │
  │ didPopNext()             │ didPop()
  │ (重新显示)                │ (退出)
```

## Stream 与Controller 生命周期

### StreamSubscription 管理

```dart
class StreamDemo extends StatefulWidget {
  @override
  _StreamDemoState createState() => _StreamDemoState();
}

class _StreamDemoState extends State<StreamDemo> {
  StreamSubscription<int>? _subscription;
  int _counter = 0;
  
  @override
  void initState() {
    super.initState();
    
    // 创建订阅
    _subscription = counterStream().listen((value) {
      if (mounted) {
        setState(() => _counter = value);
      }
    });
  }
  
  @override
  void dispose() {
    // 必须取消订阅，防止内存泄漏
    _subscription?.cancel();
    super.dispose();
  }
  
  Stream<int> counterStream() {
    return Stream.periodic(
      Duration(seconds: 1),
      (count) => count,
    );
  }
  
  @override
  Widget build(BuildContext context) {
    return Text('Count: $_counter');
  }
}
```

### TextEditingController 管理

```dart
class TextFieldDemo extends StatefulWidget {
  @override
  _TextFieldDemoState createState() => _TextFieldDemoState();
}

class _TextFieldDemoState extends State<TextFieldDemo> {
  final TextEditingController _controller = TextEditingController();
  
  @override
  void initState() {
    super.initState();
    
    _controller.addListener(() {
      print('Text: ${_controller.text}');
    });
  }
  
  @override
  void dispose() {
    // 必须释放 Controller
    _controller.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return TextField(controller: _controller);
  }
}
```

### AnimationController 管理

```dart
class AnimationDemo extends StatefulWidget {
  @override
  _AnimationDemoState createState() => _AnimationDemoState();
}

class _AnimationDemoState extends State<AnimationDemo>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  
  @override
  void initState() {
    super.initState();
    
    _controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    );
  }
  
  @override
  void dispose() {
    // 必须释放Controller
    _controller.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _controller,
      builder: (context, child) {
        return CircularProgressIndicator(value: _controller.value);
      },
    );
  }
}
```

### ScrollController 管理

```dart
class ScrollDemo extends StatefulWidget {
  @override
  _ScrollDemoState createState() => _ScrollDemoState();
}

class _ScrollDemoState extends State<ScrollDemo> {
  final ScrollController _scrollController = ScrollController();
  
  @override
  void initState() {
    super.initState();
    
    _scrollController.addListener(() {
      if (_scrollController.position.pixels >=
          _scrollController.position.maxScrollExtent - 100) {
        // 接近底部，加载更多
        _loadMore();
      }
    });
  }
  
  void _loadMore() {
    // 加载更多数据
  }
  
  @override
  void dispose() {
    _scrollController.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      controller: _scrollController,
      itemBuilder: (context, index) {
        return ListTile(title: Text('Item $index'));
      },
    );
  }
}
```

---

*最后更新: 2026-02-19*

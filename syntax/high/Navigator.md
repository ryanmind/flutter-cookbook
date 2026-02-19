# Flutter Navigator 路由导航 - 初学者指南

## 目录
1. [路由基础概念](#路由基础概念)
2. [Navigator 基本用法](#navigator-基本用法)
3. [命名路由](#命名路由)
4. [路由传参](#路由传参)
5. [返回数据处理](#返回数据处理)
6. [自定义路由动画](#自定义路由动画)
7. [常见场景与解决方案](#常见场景与解决方案)

## 路由基础概念

### 什么是路由

路由（Route）是页面的抽象，在 Flutter 中每个页面就是一个 Route。Navigator 管理着Route 的栈结构。

```
路由栈示意：
┌─────────────┐
│   Page C    │  ← 栈顶（当前页面）
├─────────────┤
│   Page B    │
├─────────────┤
│   Page A    │  ← 栈底
└─────────────┘

- push：压入新页面到栈顶
- pop：弹出栈顶页面
```

### Navigator 和 Route

```dart
// Navigator - 路由管理器
// Route - 单个路由/页面

MaterialPageRoute  // Material 风格路由
CupertinoPageRoute // iOS 风格路由
PageRouteBuilder   // 自定义路由
```

## Navigator 基本用法

### 页面跳转（push）

```dart
// 基本跳转
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (context) => SecondPage(),
  ),
);

// 简化写法（MaterialApp 中）
Navigator.of(context).push(
  MaterialPageRoute(builder: (_) => SecondPage()),
);
```

### 返回上一页（pop）

```dart
// 返回上一页
Navigator.pop(context);

// 返回并携带数据
Navigator.pop(context, '返回的数据');
```

### 完整示例

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    home: FirstPage(),
  ));
}

class FirstPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('第一页')),
      body: Center(
        child: ElevatedButton(
          child: Text('跳转到第二页'),
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(builder: (context) => SecondPage()),
            );
          },
        ),
      ),
    );
  }
}

class SecondPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('第二页')),
      body: Center(
        child: ElevatedButton(
          child: Text('返回'),
          onPressed: () {
            Navigator.pop(context);
          },
        ),
      ),
    );
  }
}
```

### 常用方法

```dart
// pushReplacement - 替换当前页面
Navigator.pushReplacement(
  context,
  MaterialPageRoute(builder: (_) => LoginPage()),
);
// 适用于：登录后跳转主页，不能返回登录页

// pushAndRemoveUntil - 清空栈并跳转
Navigator.pushAndRemoveUntil(
  context,
  MaterialPageRoute(builder: (_) => HomePage()),
  (route) => false,  // 清空所有路由
);
// 适用于：退出登录后跳转登录页

// pushAndRemoveUntil - 保留某些路由
Navigator.pushAndRemoveUntil(
  context,
  MaterialPageRoute(builder: (_) => CheckoutPage()),
  (route) => route.isFirst,  // 只保留首页
);

// popUntil - 返回到指定页面
Navigator.popUntil(context, (route) => route.isFirst);

// canPop - 检查能否返回
if (Navigator.canPop(context)) {
  Navigator.pop(context);
}

// maybePop - 安全返回（有则返回，无则不做操作）
Navigator.maybePop(context);

// popAndPushNamed - 弹出并推送命名路由
Navigator.popAndPushNamed(context, '/detail');
```

## 命名路由

### 定义路由表

```dart
void main() {
  runApp(MaterialApp(
    initialRoute: '/',  // 初始路由
    routes: {
      '/': (context) => HomePage(),
      '/detail': (context) => DetailPage(),
      '/settings': (context) => SettingsPage(),
      '/profile': (context) => ProfilePage(),
    },
  ));
}
```

### 使用命名路由

```dart
// 跳转到命名路由
Navigator.pushNamed(context, '/detail');

// 替换当前路由
Navigator.pushReplacementNamed(context, '/home');

// 清空栈并跳转
Navigator.pushNamedAndRemoveUntil(
  context,
  '/login',
  (route) => false,
);
```

### 动态路由生成

```dart
MaterialApp(
  onGenerateRoute: (settings) {
    // 根据路由名称动态生成
    if (settings.name == '/user') {
      final userId = settings.arguments as String;
      return MaterialPageRoute(
        builder: (_) => UserPage(userId: userId),
      );
    }
    
    // 处理未知路由
    return MaterialPageRoute(
      builder: (_) => NotFoundPage(),
    );
  },
  
  // 未知路由处理
  onUnknownRoute: (settings) {
    return MaterialPageRoute(
      builder: (_) => NotFoundPage(),
    );
  },
)
```

## 路由传参

### 基本方式（构造函数传参）

```dart
// 接收参数的页面
class DetailPage extends StatelessWidget {
  final String title;
  final String content;
  
  const DetailPage({
    required this.title,
    required this.content,
  });
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(title)),
      body: Text(content),
    );
  }
}

// 跳转时传参
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (_) => DetailPage(
      title: '详情',
      content: '这是内容',
    ),
  ),
);
```

### 命名路由传参

```dart
// 定义路由
MaterialApp(
  routes: {
    '/detail': (context) => DetailPage(),
  },
);

// DetailPage 接收参数
class DetailPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 获取参数
    final args = ModalRoute.of(context)?.settings.arguments as Map?;
    final title = args?['title'] ?? '默认标题';
    
    return Scaffold(
      appBar: AppBar(title: Text(title)),
    );
  }
}

// 跳转时传参
Navigator.pushNamed(
  context,
  '/detail',
  arguments: {
    'title': '详情标题',
    'id': 123,
  },
);
```

### 使用 onGenerateRoute 传参

```dart
MaterialApp(
  onGenerateRoute: (settings) {
    switch (settings.name) {
      case '/detail':
        final args = settings.arguments as DetailArgs;
        return MaterialPageRoute(
          builder: (_) => DetailPage(args: args),
        );
      case '/user':
        final userId = settings.arguments as String;
        return MaterialPageRoute(
          builder: (_) => UserPage(userId: userId),
        );
    }
    return null;
  },
)

// 参数类
class DetailArgs {
  final String title;
  final String content;
  
  DetailArgs({required this.title, required this.content});
}

// 跳转
Navigator.pushNamed(
  context,
  '/detail',
  arguments: DetailArgs(title: '标题', content: '内容'),
);
```

## 返回数据处理

### 接收返回数据

```dart
class FirstPage extends StatefulWidget {
  @override
  _FirstPageState createState() => _FirstPageState();
}

class _FirstPageState extends State<FirstPage> {
  String _result = '等待选择';
  
  Future<void> _navigateToSecond() async {
    // await 等待返回数据
    final result = await Navigator.push(
      context,
      MaterialPageRoute(builder: (_) => SecondPage()),
    );
    
    // 处理返回数据
    if (result != null) {
      setState(() {
        _result = result;
      });
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(_result),
            ElevatedButton(
              onPressed: _navigateToSecond,
              child: Text('选择'),
            ),
          ],
        ),
      ),
    );
  }
}

class SecondPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () => Navigator.pop(context, '选项 A'),
              child: Text('选择 A'),
            ),
            ElevatedButton(
              onPressed: () => Navigator.pop(context, '选项 B'),
              child: Text('选择 B'),
            ),
          ],
        ),
      ),
    );
  }
}
```

### 使用 then 回调

```dart
Navigator.push(
  context,
  MaterialPageRoute(builder: (_) => SecondPage()),
).then((result) {
  if (result != null) {
    print('返回数据：$result');
  }
});
```

### 使用 async/await 完整示例

```dart
// 选择联系人页面
class ContactPicker extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('选择联系人')),
      body: ListView.builder(
        itemCount: contacts.length,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text(contacts[index].name),
            onTap: () {
              // 返回选中的联系人
              Navigator.pop(context, contacts[index]);
            },
          );
        },
      ),
    );
  }
}

// 调用方
Future<void> _pickContact() async {
  final contact = await Navigator.push<Contact>(
    context,
    MaterialPageRoute(builder: (_) => ContactPicker()),
  );
  
  if (contact != null) {
    // 处理选中的联系人
    _sendMessage(contact);
  }
}
```

## 自定义路由动画

### PageRouteBuilder

```dart
Navigator.push(
  context,
  PageRouteBuilder(
    pageBuilder: (context, animation, secondaryAnimation) {
      return DetailPage();
    },
    transitionsBuilder: (context, animation, secondaryAnimation, child) {
      // 淡入淡出
      return FadeTransition(
        opacity: animation,
        child: child,
      );
    },
    transitionDuration: Duration(milliseconds: 300),
  ),
);
```

### 常见动画效果

```dart
// 1. 淡入淡出
transitionsBuilder: (context, animation, secondaryAnimation, child) {
  return FadeTransition(
    opacity: animation,
    child: child,
  );
}

// 2. 滑动
transitionsBuilder: (context, animation, secondaryAnimation, child) {
  final offset = Tween<Offset>(
    begin: Offset(1.0, 0.0),  // 从右侧进入
    end: Offset.zero,
  ).animate(CurvedAnimation(
    parent: animation,
    curve: Curves.easeInOut,
  ));
  
  return SlideTransition(
    position: offset,
    child: child,
  );
}

// 3. 缩放
transitionsBuilder: (context, animation, secondaryAnimation, child) {
  return ScaleTransition(
    scale: animation,
    child: child,
  );
}

// 4. 旋转+缩放
transitionsBuilder: (context, animation, secondaryAnimation, child) {
  return RotationTransition(
    turns: animation,
    child: ScaleTransition(
      scale: animation,
      child: child,
    ),
  );
}

// 5. 组合动画（滑动 + 淡入）
transitionsBuilder: (context, animation, secondaryAnimation, child) {
  final offset = Tween<Offset>(
    begin: Offset(0.0, 1.0),  // 从底部进入
    end: Offset.zero,
  ).animate(CurvedAnimation(
    parent: animation,
    curve: Curves.easeOut,
  ));
  
  return SlideTransition(
    position: offset,
    child: FadeTransition(
      opacity: animation,
      child: child,
    ),
  );
}
```

### 自定义PageRoute

```dart
class FadePageRoute<T> extends PageRouteBuilder<T> {
  final Widget page;
  
  FadePageRoute({required this.page})
      : super(
          pageBuilder: (context, animation, secondaryAnimation) => page,
          transitionsBuilder: (context, animation, secondaryAnimation, child) {
            return FadeTransition(
              opacity: animation,
              child: child,
            );
          },
          transitionDuration: Duration(milliseconds: 300),
        );
}

// 使用
Navigator.push(context, FadePageRoute(page: DetailPage()));
```

## 常见场景与解决方案

### 场景 1：登录流程

```dart
// 登录成功后跳转主页，清空登录相关页面
void onLoginSuccess() {
  Navigator.pushNamedAndRemoveUntil(
    context,
    '/home',
    (route) => false,  // 清空所有路由
  );
}

// 退出登录
void onLogout() {
  Navigator.pushNamedAndRemoveUntil(
    context,
    '/login',
    (route) => false,
  );
}
```

### 场景 2：引导页

```dart
// 首次启动显示引导页
void completeOnboarding() {
  // 替换引导页为主页
  Navigator.pushReplacementNamed(context, '/home');
}
```

### 场景 3：多步表单

```dart
// 方式 1：使用 push
void nextStep() {
  Navigator.push(
    context,
    MaterialPageRoute(builder: (_) => Step2Page()),
  );
}

// 方式 2：使用 PageView（推荐）
class FormWizard extends StatefulWidget {
  @override
  _FormWizardState createState() => _FormWizardState();
}

class _FormWizardState extends State<FormWizard> {
  final PageController _controller = PageController();
  
  @override
  Widget build(BuildContext context) {
    return PageView(
      controller: _controller,
      physics: NeverScrollableScrollPhysics(),
      children: [
        Step1Page(onNext: () => _controller.nextPage(...)),
        Step2Page(onNext: () => _controller.nextPage(...)),
        Step3Page(onComplete: _submitForm),
      ],
    );
  }
}
```

### 场景 4：Tab 页面保持状态

```dart
// 使用 IndexedStack 或 TabBarView
class MainPage extends StatefulWidget {
  @override
  _MainPageState createState() => _MainPageState();
}

class _MainPageState extends State<MainPage> {
  int _currentIndex = 0;
  
  final _pages = [
    HomePage(),
    CategoryPage(),
    CartPage(),
    ProfilePage(),
  ];
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(
        index: _currentIndex,
        children: _pages,
      ),
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (index) => setState(() => _currentIndex = index),
        items: [...],
      ),
    );
  }
}
```

### 场景 5：拦截返回键

```dart
class ConfirmExitPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return WillPopScope(
      onWillPop: () async {
        final shouldPop = await showDialog<bool>(
          context: context,
          builder: (context) => AlertDialog(
            title: Text('确定退出吗？'),
            actions: [
              TextButton(
                onPressed: () => Navigator.pop(context, false),
                child: Text('取消'),
              ),
              TextButton(
                onPressed: () => Navigator.pop(context, true),
                child: Text('确定'),
              ),
            ],
          ),
        );
        return shouldPop ?? false;
      },
      child: Scaffold(
        appBar: AppBar(title: Text('确认退出')),
        body: Center(child: Text('按返回键试试')),
      ),
    );
  }
}

// Flutter 3.12+ 使用 PopScope
class ConfirmExitPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return PopScope(
      canPop: false,
      onPopInvokedWithResult: (didPop, result) {
        if (didPop) return;
        
        showDialog(
          context: context,
          builder: (context) => AlertDialog(
            title: Text('确定退出吗？'),
            actions: [
              TextButton(
                onPressed: () => Navigator.pop(context),
                child: Text('取消'),
              ),
              TextButton(
                onPressed: () {
                  Navigator.pop(context);
                  Navigator.pop(context);
                },
                child: Text('确定'),
              ),
            ],
          ),
        );
      },
      child: Scaffold(
        appBar: AppBar(title: Text('确认退出')),
      ),
    );
  }
}
```

### 场景 6：深链接处理

```dart
MaterialApp(
  onGenerateRoute: (settings) {
    final uri = Uri.parse(settings.name ?? '/');
    
    // 解析路径：/product/123
    if (uri.pathSegments.length == 2 &&
        uri.pathSegments[0] == 'product') {
      final productId = uri.pathSegments[1];
      return MaterialPageRoute(
        builder: (_) => ProductPage(productId: productId),
      );
    }
    
    // 解析路径：/user/profile?tab=settings
    if (uri.path == '/user/profile') {
      final tab = uri.queryParameters['tab'] ?? 'info';
      return MaterialPageRoute(
        builder: (_) => UserProfilePage(initialTab: tab),
      );
    }
    
    return null;
  },
)
```

## 路由工具类

```dart
class Routes {
  static const String home = '/';
  static const String login = '/login';
  static const String register = '/register';
  static const String detail = '/detail';
  static const String profile = '/profile';
  static const String settings = '/settings';
  
  static Map<String, WidgetBuilder> get routes => {
    home: (_) => HomePage(),
    login: (_) => LoginPage(),
    register: (_) => RegisterPage(),
    detail: (_) => DetailPage(),
    profile: (_) => ProfilePage(),
    settings: (_) => SettingsPage(),
  };
  
  static Route<dynamic>? onGenerateRoute(RouteSettings settings) {
    final uri = Uri.parse(settings.name ?? '/');
    
    switch (uri.path) {
      case detail:
        final args = settings.arguments as Map<String, dynamic>?;
        return MaterialPageRoute(
          builder: (_) => DetailPage(
            id: args?['id'],
            title: args?['title'],
          ),
        );
    }
    
    return null;
  }
}

// 使用
MaterialApp(
  initialRoute: Routes.home,
  routes: Routes.routes,
  onGenerateRoute: Routes.onGenerateRoute,
)
```

---

*最后更新: 2026-02-19*

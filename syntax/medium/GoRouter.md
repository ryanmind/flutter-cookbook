# Flutter GoRouter 路由 - 初学者指南

## 目录
1. [GoRouter 简介](#gorouter-简介)
2. [基础配置](#基础配置)
3. [路由定义](#路由定义)
4. [路由传参](#路由传参)
5. [嵌套路由](#嵌套路由)
6. [路由守卫](#路由守卫)
7. [重定向与错误处理](#重定向与错误处理)
8. [与状态管理结合](#与状态管理结合)

## GoRouter 简介

### 什么是 GoRouter

GoRouter 是 Flutter 官方推荐的路由方案，支持声明式路由、深链接、Web URL 等。

```yaml
# pubspec.yaml
dependencies:
  go_router: ^13.0.0
```

### 核心优势

```dart
// ✅ 声明式路由配置
// ✅ 支持 Web URL 路由
// ✅ 深链接支持
// ✅ 路由守卫（重定向）
// ✅ 嵌套路由
// ✅ 底部导航栏状态保持
```

## 基础配置

### 最简配置

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

void main() {
  runApp(MyApp());
}

// 1. 定义路由配置
final GoRouter _router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => HomePage(),
    ),
    GoRoute(
      path: '/details',
      builder: (context, state) => DetailsPage(),
    ),
  ],
);

// 2. 在 MaterialApp 中使用
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      routerConfig: _router,
    );
  }
}
```

### 页面跳转

```dart
// 跳转到指定路径
context.go('/details');

// 带参数跳转
context.go('/details/123');

// 使用命名路由
context.goNamed('details', pathParameters: {'id': '123'});

// 返回上一页
context.pop();

// 替换当前路由
context.go('/login');  // 会替换当前路由栈

// 推入新页面（保留返回能力）
context.push('/details');
context.pushNamed('details');
```

## 路由定义

### 基本路由

```dart
final GoRouter _router = GoRouter(
  routes: [
    // 首页
    GoRoute(
      path: '/',
      name: 'home',
      builder: (context, state) => HomePage(),
    ),
    
    // 详情页
    GoRoute(
      path: '/details',
      name: 'details',
      builder: (context, state) => DetailsPage(),
    ),
    
    // 设置页
    GoRoute(
      path: '/settings',
      name: 'settings',
      builder: (context, state) => SettingsPage(),
    ),
  ],
);
```

### 动态路由参数

```dart
final GoRouter _router = GoRouter(
  routes: [
    // 路径参数
    GoRoute(
      path: '/user/:id',  // :id 是参数
      name: 'user',
      builder: (context, state) {
        // 获取路径参数
        final id = state.pathParameters['id']!;
        return UserPage(userId: id);
      },
    ),
    
    // 多个参数
    GoRoute(
      path: '/category/:categoryId/product/:productId',
      name: 'product',
      builder: (context, state) {
        final categoryId = state.pathParameters['categoryId']!;
        final productId = state.pathParameters['productId']!;
        return ProductPage(
          categoryId: categoryId,
          productId: productId,
        );
      },
    ),
  ],
);

// 使用
context.go('/user/123');
context.goNamed('user', pathParameters: {'id': '123'});
```

### 查询参数

```dart
final GoRouter _router = GoRouter(
  routes: [
    GoRoute(
      path: '/search',
      name: 'search',
      builder: (context, state) {
        // 获取查询参数
        final query = state.uri.queryParameters['q'];
        final page = state.uri.queryParameters['page'] ?? '1';
        return SearchPage(query: query, page: int.parse(page));
      },
    ),
  ],
);

// 使用
context.go('/search?q=flutter&page=2');
context.goNamed(
  'search',
  queryParameters: {'q': 'flutter', 'page': '2'},
);
```

### 额外参数（extra）

```dart
final GoRouter _router = GoRouter(
  routes: [
    GoRoute(
      path: '/profile',
      name: 'profile',
      builder: (context, state) {
        // 获取额外参数
        final user = state.extra as User?;
        return ProfilePage(user: user);
      },
    ),
  ],
);

// 使用
context.go('/profile', extra: User(name: 'Alice'));
context.goNamed('profile', extra: User(name: 'Alice'));
```

## 路由传参

### 路径参数

```dart
// 定义
GoRoute(
  path: '/product/:id',
  builder: (context, state) {
    final id = state.pathParameters['id']!;
    return ProductPage(id: id);
  },
)

// 跳转
context.go('/product/123');
// 或
context.goNamed('product', pathParameters: {'id': '123'});
```

### 查询参数

```dart
// 定义
GoRoute(
  path: '/products',
  builder: (context, state) {
    final category = state.uri.queryParameters['category'];
    final sort = state.uri.queryParameters['sort'] ?? 'default';
    return ProductsPage(category: category, sort: sort);
  },
)

// 跳转
context.go('/products?category=electronics&sort=price');
// 或
context.goNamed(
  'products',
  queryParameters: {'category': 'electronics', 'sort': 'price'},
);
```

### 对象参数

```dart
// 定义数据类
class ProductDetail {
  final String id;
  final String name;
  
  ProductDetail({required this.id, required this.name});
}

// 定义路由
GoRoute(
  path: '/product-detail',
  builder: (context, state) {
    final detail = state.extra as ProductDetail?;
    return ProductDetailPage(detail: detail);
  },
)

// 跳转
context.go(
  '/product-detail',
  extra: ProductDetail(id: '123', name: 'iPhone'),
);
```

## 嵌套路由

### 基本嵌套

```dart
final GoRouter _router = GoRouter(
  routes: [
    // 父路由
    GoRoute(
      path: '/settings',
      builder: (context, state) => SettingsShell(),
      routes: [
        // 子路由
        GoRoute(
          path: 'profile',  // 注意：没有前导 /
          builder: (context, state) => ProfileSettings(),
        ),
        GoRoute(
          path: 'security',
          builder: (context, state) => SecuritySettings(),
        ),
        GoRoute(
          path: 'notifications',
          builder: (context, state) => NotificationSettings(),
        ),
      ],
    ),
  ],
);

// 外壳页面
class SettingsShell extends StatelessWidget {
  final Widget child;
  
  const SettingsShell({required this.child});
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Settings')),
      body: Row(
        children: [
          NavigationRail(
            destinations: [
              NavigationRailDestination(
                icon: Icon(Icons.person),
                label: Text('Profile'),
              ),
              NavigationRailDestination(
                icon: Icon(Icons.security),
                label: Text('Security'),
              ),
              NavigationRailDestination(
                icon: Icon(Icons.notifications),
                label: Text('Notifications'),
              ),
            ],
            selectedIndex: _calculateIndex(context),
            onDestinationSelected: (index) {
              switch (index) {
                case 0:
                  context.go('/settings/profile');
                  break;
                case 1:
                  context.go('/settings/security');
                  break;
                case 2:
                  context.go('/settings/notifications');
                  break;
              }
            },
          ),
          Expanded(child: child),
        ],
      ),
    );
  }
  
  int _calculateIndex(BuildContext context) {
    final location = GoRouterState.of(context).matchedLocation;
    if (location == '/settings/profile') return 0;
    if (location == '/settings/security') return 1;
    if (location == '/settings/notifications') return 2;
    return 0;
  }
}
```

### 底部导航栏

```dart
// 使用 StatefulShellRoute
final GoRouter _router = GoRouter(
  routes: [
    StatefulShellRoute.indexedStack(
      builder: (context, state, navigationShell) {
        return ScaffoldWithNavBar(navigationShell: navigationShell);
      },
      branches: [
        // 首页分支
        StatefulShellBranch(
          routes: [
            GoRoute(
              path: '/',
              builder: (context, state) => HomePage(),
              routes: [
                GoRoute(
                  path: 'details/:id',
                  builder: (context, state) => DetailsPage(
                    id: state.pathParameters['id']!,
                  ),
                ),
              ],
            ),
          ],
        ),
        
        // 搜索分支
        StatefulShellBranch(
          routes: [
            GoRoute(
              path: '/search',
              builder: (context, state) => SearchPage(),
            ),
          ],
        ),
        
        // 个人中心分支
        StatefulShellBranch(
          routes: [
            GoRoute(
              path: '/profile',
              builder: (context, state) => ProfilePage(),
            ),
          ],
        ),
      ],
    ),
  ],
);

// 底部导航栏外壳
class ScaffoldWithNavBar extends StatelessWidget {
  final StatefulNavigationShell navigationShell;
  
  const ScaffoldWithNavBar({required this.navigationShell});
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: navigationShell,
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: navigationShell.currentIndex,
        onTap: (index) => navigationShell.goBranch(index),
        items: [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
          BottomNavigationBarItem(icon: Icon(Icons.search), label: 'Search'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: 'Profile'),
        ],
      ),
    );
  }
}
```

## 路由守卫

### 基于状态的重定向

```dart
// 定义认证状态
final isLoggedInProvider = StateProvider<bool>((ref) => false);

final GoRouter _router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => HomePage(),
    ),
    GoRoute(
      path: '/login',
      builder: (context, state) => LoginPage(),
    ),
    GoRoute(
      path: '/dashboard',
      builder: (context, state) => DashboardPage(),
    ),
  ],
  
  // 路由守卫
  redirect: (context, state) {
    // 获取认证状态
    final isLoggedIn = /* 从状态管理获取 */;
    final isGoingToLogin = state.matchedLocation == '/login';
    
    // 未登录且访问受保护页面
    if (!isLoggedIn && !isGoingToLogin) {
      return '/login';
    }
    
    // 已登录但访问登录页
    if (isLoggedIn && isGoingToLogin) {
      return '/dashboard';
    }
    
    // 不需要重定向
    return null;
  },
);
```

### RefreshListenable

```dart
// 当认证状态变化时刷新路由
final GoRouter _router = GoRouter(
  routes: [...],
  refreshListenable: GoRouterRefreshStream(authStateStream),
  redirect: (context, state) {
    final isLoggedIn = /* 检查登录状态 */;
    final isGoingToLogin = state.matchedLocation == '/login';
    
    if (!isLoggedIn && !isGoingToLogin) {
      return '/login';
    }
    
    if (isLoggedIn && isGoingToLogin) {
      return '/';
    }
    
    return null;
  },
);

// 创建刷新流
class GoRouterRefreshStream extends ChangeNotifier {
  final Stream<dynamic> stream;
  late final StreamSubscription<dynamic> _subscription;
  
  GoRouterRefreshStream(this.stream) {
    _subscription = stream.listen((_) => notifyListeners());
  }
  
  @override
  void dispose() {
    _subscription.cancel();
    super.dispose();
  }
}
```

## 重定向与错误处理

### 错误页面

```dart
final GoRouter _router = GoRouter(
  routes: [...],
  
  // 错误处理
  errorBuilder: (context, state) => ErrorPage(
    error: state.error,
  ),
  
  // 或使用 errorPageBuilder 返回 Page
  errorPageBuilder: (context, state) => MaterialPage(
    child: ErrorPage(error: state.error),
  ),
);

class ErrorPage extends StatelessWidget {
  final Exception? error;
  
  const ErrorPage({this.error});
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Error')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(Icons.error_outline, size: 64, color: Colors.red),
            SizedBox(height: 16),
            Text(
              error?.toString() ?? 'Page not found',
              textAlign: TextAlign.center,
            ),
            SizedBox(height: 16),
            ElevatedButton(
              onPressed: () => context.go('/'),
              child: Text('Go Home'),
            ),
          ],
        ),
      ),
    );
  }
}
```

### 自定义页面过渡

```dart
GoRoute(
  path: '/details',
  pageBuilder: (context, state) => CustomTransitionPage(
    key: state.pageKey,
    child: DetailsPage(),
    transitionsBuilder: (context, animation, secondaryAnimation, child) {
      return FadeTransition(
        opacity: animation,
        child: child,
      );
    },
  ),
)

// iOS 风格过渡
GoRoute(
  path: '/details',
  pageBuilder: (context, state) => CupertinoPage(
    key: state.pageKey,
    child: DetailsPage(),
  ),
)
```

## 与状态管理结合

### 与 Provider 结合

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:go_router/go_router.dart';

// 认证状态
final authProvider = StateNotifierProvider<AuthNotifier, AuthState>((ref) {
  return AuthNotifier();
});

// 路由 Provider
final routerProvider = Provider<GoRouter>((ref) {
  return GoRouter(
    routes: [
      GoRoute(
        path: '/',
        builder: (context, state) => HomePage(),
      ),
      GoRoute(
        path: '/login',
        builder: (context, state) => LoginPage(),
      ),
      GoRoute(
        path: '/dashboard',
        builder: (context, state) => DashboardPage(),
      ),
    ],
    redirect: (context, state) {
      final authState = ref.read(authProvider);
      final isLoggedIn = authState.isLoggedIn;
      final isGoingToLogin = state.matchedLocation == '/login';
      
      if (!isLoggedIn && !isGoingToLogin) {
        return '/login';
      }
      
      if (isLoggedIn && isGoingToLogin) {
        return '/dashboard';
      }
      
      return null;
    },
    refreshListenable: GoRouterRefreshStream(
      ref.read(authProvider.notifier).stream,
    ),
  );
});

// App
class MyApp extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final router = ref.watch(routerProvider);
    
    return MaterialApp.router(
      routerConfig: router,
    );
  }
}
```

## 完整示例

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

void main() {
  runApp(MyApp());
}

// 路由配置
final GoRouter _router = GoRouter(
  routes: [
    // 首页
    GoRoute(
      path: '/',
      name: 'home',
      builder: (context, state) => HomePage(),
    ),
    
    // 详情页（带参数）
    GoRoute(
      path: '/details/:id',
      name: 'details',
      builder: (context, state) {
        final id = state.pathParameters['id']!;
        return DetailsPage(id: id);
      },
    ),
    
    // 设置页（嵌套路由）
    GoRoute(
      path: '/settings',
      builder: (context, state) => SettingsShell(),
      routes: [
        GoRoute(
          path: 'profile',
          builder: (context, state) => ProfileSettings(),
        ),
        GoRoute(
          path: 'theme',
          builder: (context, state) => ThemeSettings(),
        ),
      ],
    ),
  ],
  
  errorBuilder: (context, state) => ErrorPage(error: state.error),
);

// App
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      title: 'GoRouter Demo',
      routerConfig: _router,
    );
  }
}

// 首页
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Home')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () => context.go('/details/123'),
              child: Text('Go to Details'),
            ),
            ElevatedButton(
              onPressed: () => context.go('/settings'),
              child: Text('Go to Settings'),
            ),
          ],
        ),
      ),
    );
  }
}

// 详情页
class DetailsPage extends StatelessWidget {
  final String id;
  
  const DetailsPage({required this.id});
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Details')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('ID: $id'),
            ElevatedButton(
              onPressed: () => context.pop(),
              child: Text('Back'),
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

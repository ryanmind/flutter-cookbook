# Flutter 实战项目教程

## 概述

本文通过一个完整的电商应用项目，演示 Flutter 实际开发流程、架构设计和最佳实践。

## 项目介绍

### 功能需求

- 用户认证（登录、注册、退出）
- 商品列表与搜索
- 商品详情
- 购物车管理
- 订单管理
- 用户中心

### 技术栈

| 类别 | 技术选型 |
|------|----------|
| 状态管理 | Riverpod |
| 网络请求 | Dio |
| 本地存储 | SharedPreferences + Hive |
| 路由 | GoRouter |
| UI 组件 | Material 3 |

## 项目结构

```
lib/
├── main.dart
├── app.dart
│
├── core/
│   ├── constants/
│   │   ├── app_colors.dart
│   │   ├── app_strings.dart
│   │   └── app_values.dart
│   ├── network/
│   │   ├── dio_client.dart
│   │   ├── api_endpoints.dart
│   │   └── interceptors/
│   │       ├── auth_interceptor.dart
│   │       └── logging_interceptor.dart
│   ├── storage/
│   │   ├── local_storage.dart
│   │   └── secure_storage.dart
│   ├── router/
│   │   └── app_router.dart
│   └── theme/
│       ├── app_theme.dart
│       └── text_styles.dart
│
├── features/
│   ├── auth/
│   │   ├── data/
│   │   │   ├── models/
│   │   │   │   ├── user.dart
│   │   │   │   └── auth_response.dart
│   │   │   ├── repositories/
│   │   │   │   └── auth_repository.dart
│   │   │   └── datasources/
│   │   │       ├── auth_local_datasource.dart
│   │   │       └── auth_remote_datasource.dart
│   │   ├── domain/
│   │   │   └── entities/
│   │   │       └── user_entity.dart
│   │   └── presentation/
│   │       ├── pages/
│   │       │   ├── login_page.dart
│   │       │   └── register_page.dart
│   │       ├── widgets/
│   │       │   ├── login_form.dart
│   │       │   └── social_login_button.dart
│   │       └── providers/
│   │           └── auth_provider.dart
│   │
│   ├── product/
│   │   ├── data/
│   │   │   ├── models/
│   │   │   │   ├── product.dart
│   │   │   │   └── product_list_response.dart
│   │   │   └── repositories/
│   │   │       └── product_repository.dart
│   │   └── presentation/
│   │       ├── pages/
│   │       │   ├── product_list_page.dart
│   │       │   └── product_detail_page.dart
│   │       ├── widgets/
│   │       │   ├── product_card.dart
│   │       │   └── product_grid.dart
│   │       └── providers/
│   │           └── product_provider.dart
│   │
│   ├── cart/
│   │   ├── data/
│   │   │   ├── models/
│   │   │   │   └── cart_item.dart
│   │   │   └── repositories/
│   │   │       └── cart_repository.dart
│   │   └── presentation/
│   │       ├── pages/
│   │       │   └── cart_page.dart
│   │       ├── widgets/
│   │       │   └── cart_item_tile.dart
│   │       └── providers/
│   │           └── cart_provider.dart
│   │
│   └── profile/
│       └── presentation/
│           └── profile_page.dart
│
├── shared/
│   ├── widgets/
│   │   ├── loading_overlay.dart
│   │   ├── error_widget.dart
│   │   ├── empty_state.dart
│   │   └── custom_button.dart
│   └── extensions/
│       ├── context_extension.dart
│       └── string_extension.dart
│
└── utils/
    ├── validators.dart
    ├── formatters.dart
    └── helpers.dart
```

## 核心代码实现

### 1. Dio 网络客户端

```dart
// core/network/dio_client.dart
import 'package:dio/dio.dart';

class DioClient {
  late final Dio _dio;

  DioClient() {
    _dio = Dio(
      BaseOptions(
        baseUrl: 'https://api.example.com',
        connectTimeout: const Duration(seconds: 30),
        receiveTimeout: const Duration(seconds: 30),
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
        },
      ),
    );

    _dio.interceptors.addAll([
      AuthInterceptor(),
      LoggingInterceptor(),
    ]);
  }

  Dio get dio => _dio;

  Future<T> get<T>(
    String path, {
    Map<String, dynamic>? queryParameters,
    Options? options,
  }) async {
    final response = await _dio.get<T>(
      path,
      queryParameters: queryParameters,
      options: options,
    );
    return response.data as T;
  }

  Future<T> post<T>(
    String path, {
    dynamic data,
    Options? options,
  }) async {
    final response = await _dio.post<T>(path, data: data, options: options);
    return response.data as T;
  }
}
```

### 2. 认证拦截器

```dart
// core/network/interceptors/auth_interceptor.dart
class AuthInterceptor extends Interceptor {
  final LocalStorage _storage = LocalStorage();

  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) async {
    final token = await _storage.getToken();
    if (token != null) {
      options.headers['Authorization'] = 'Bearer $token';
    }
    handler.next(options);
  }

  @override
  void onError(DioException err, ErrorInterceptorHandler handler) async {
    if (err.response?.statusCode == 401) {
      // Token 过期，清除登录状态
      await _storage.clearToken();
      // 跳转到登录页
      // 需要通过导航处理
    }
    handler.next(err);
  }
}
```

### 3. 用户认证模块

```dart
// features/auth/data/models/user.dart
@freezed
class User with _$User {
  const factory User({
    required String id,
    required String email,
    required String name,
    String? avatar,
    String? phone,
  }) = _User;

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
}

// features/auth/data/repositories/auth_repository.dart
abstract class AuthRepository {
  Future<User> login(String email, String password);
  Future<User> register(String email, String password, String name);
  Future<void> logout();
  Future<User?> getCurrentUser();
  Stream<bool> get authStateChanges;
}

class AuthRepositoryImpl implements AuthRepository {
  final AuthRemoteDataSource _remote;
  final AuthLocalDataSource _local;

  AuthRepositoryImpl(this._remote, this._local);

  @override
  Future<User> login(String email, String password) async {
    final response = await _remote.login(email, password);
    await _local.saveToken(response.token);
    await _local.saveUser(response.user);
    return response.user;
  }

  @override
  Future<void> logout() async {
    await _local.clearAll();
  }

  @override
  Future<User?> getCurrentUser() async {
    return await _local.getUser();
  }

  @override
  Stream<bool> get authStateChanges => _local.authStateChanges;
}
```

### 4. 认证 Provider

```dart
// features/auth/presentation/providers/auth_provider.dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

// 状态定义
sealed class AuthState {
  const AuthState();
}

class AuthInitial extends AuthState {
  const AuthInitial();
}

class AuthLoading extends AuthState {
  const AuthLoading();
}

class AuthAuthenticated extends AuthState {
  final User user;
  const AuthAuthenticated(this.user);
}

class AuthUnauthenticated extends AuthState {
  const AuthUnauthenticated();
}

class AuthError extends AuthState {
  final String message;
  const AuthError(this.message);
}

// Notifier
class AuthNotifier extends StateNotifier<AuthState> {
  final AuthRepository _repository;

  AuthNotifier(this._repository) : super(const AuthInitial()) {
    _checkAuth();
  }

  Future<void> _checkAuth() async {
    state = const AuthLoading();
    final user = await _repository.getCurrentUser();
    state = user != null
        ? AuthAuthenticated(user)
        : const AuthUnauthenticated();
  }

  Future<void> login(String email, String password) async {
    state = const AuthLoading();
    try {
      final user = await _repository.login(email, password);
      state = AuthAuthenticated(user);
    } catch (e) {
      state = AuthError(e.toString());
    }
  }

  Future<void> logout() async {
    await _repository.logout();
    state = const AuthUnauthenticated();
  }
}

// Provider
final authProvider = StateNotifierProvider<AuthNotifier, AuthState>((ref) {
  return AuthNotifier(ref.watch(authRepositoryProvider));
});
```

### 5. 登录页面

```dart
// features/auth/presentation/pages/login_page.dart
class LoginPage extends ConsumerStatefulWidget {
  const LoginPage({super.key});

  @override
  ConsumerState<LoginPage> createState() => _LoginPageState();
}

class _LoginPageState extends ConsumerState<LoginPage> {
  final _formKey = GlobalKey<FormState>();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  Future<void> _handleLogin() async {
    if (!_formKey.currentState!.validate()) return;

    await ref.read(authProvider.notifier).login(
          _emailController.text.trim(),
          _passwordController.text,
        );
  }

  @override
  Widget build(BuildContext context) {
    final authState = ref.watch(authProvider);

    ref.listen<AuthState>(authProvider, (previous, next) {
      if (next is AuthError) {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text(next.message)),
        );
      }
    });

    return Scaffold(
      body: SafeArea(
        child: Padding(
          padding: const EdgeInsets.all(24),
          child: Form(
            key: _formKey,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                // Logo
                Icon(Icons.shopping_bag, size: 80, color: Theme.of(context).colorScheme.primary),
                const SizedBox(height: 32),

                // 标题
                Text('欢迎回来', style: Theme.of(context).textTheme.headlineMedium),
                const SizedBox(height: 8),
                Text('登录您的账户', style: Theme.of(context).textTheme.bodyMedium),
                const SizedBox(height: 32),

                // 邮箱输入
                TextFormField(
                  controller: _emailController,
                  decoration: const InputDecoration(
                    labelText: '邮箱',
                    prefixIcon: Icon(Icons.email_outlined),
                  ),
                  keyboardType: TextInputType.emailAddress,
                  validator: Validators.email,
                ),
                const SizedBox(height: 16),

                // 密码输入
                TextFormField(
                  controller: _passwordController,
                  decoration: const InputDecoration(
                    labelText: '密码',
                    prefixIcon: Icon(Icons.lock_outlined),
                  ),
                  obscureText: true,
                  validator: Validators.password,
                ),
                const SizedBox(height: 24),

                // 登录按钮
                SizedBox(
                  width: double.infinity,
                  child: FilledButton(
                    onPressed: authState is AuthLoading ? null : _handleLogin,
                    child: authState is AuthLoading
                        ? const SizedBox(
                            height: 20,
                            width: 20,
                            child: CircularProgressIndicator(strokeWidth: 2),
                          )
                        : const Text('登录'),
                  ),
                ),
                const SizedBox(height: 16),

                // 注册链接
                Row(
                  mainAxisAlignment: MainAxisAlignment.center,
                  children: [
                    const Text('还没有账户？'),
                    TextButton(
                      onPressed: () => context.go('/register'),
                      child: const Text('立即注册'),
                    ),
                  ],
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

### 6. 商品列表页面

```dart
// features/product/presentation/pages/product_list_page.dart
class ProductListPage extends ConsumerStatefulWidget {
  const ProductListPage({super.key});

  @override
  ConsumerState<ProductListPage> createState() => _ProductListPageState();
}

class _ProductListPageState extends ConsumerState<ProductListPage> {
  final _searchController = TextEditingController();
  String _searchQuery = '';

  @override
  void dispose() {
    _searchController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    final productsAsync = ref.watch(productListProvider(_searchQuery));

    return Scaffold(
      appBar: AppBar(
        title: const Text('商品列表'),
        actions: [
          // 购物车图标
          Stack(
            alignment: Alignment.center,
            children: [
              IconButton(
                icon: const Icon(Icons.shopping_cart_outlined),
                onPressed: () => context.go('/cart'),
              ),
              // 购物车数量角标
              Positioned(
                right: 8,
                top: 8,
                child: Consumer(
                  builder: (context, ref, _) {
                    final count = ref.watch(cartItemCountProvider);
                    if (count == 0) return const SizedBox.shrink();
                    return Container(
                      padding: const EdgeInsets.all(4),
                      decoration: BoxDecoration(
                        color: Theme.of(context).colorScheme.error,
                        shape: BoxShape.circle,
                      ),
                      child: Text(
                        '$count',
                        style: const TextStyle(color: Colors.white, fontSize: 10),
                      ),
                    );
                  },
                ),
              ),
            ],
          ),
        ],
      ),
      body: Column(
        children: [
          // 搜索框
          Padding(
            padding: const EdgeInsets.all(16),
            child: SearchBar(
              controller: _searchController,
              hintText: '搜索商品',
              onChanged: (value) {
                setState(() => _searchQuery = value);
              },
              leading: const Icon(Icons.search),
            ),
          ),

          // 商品列表
          Expanded(
            child: productsAsync.when(
              data: (products) {
                if (products.isEmpty) {
                  return const EmptyState(
                    icon: Icons.inventory_2_outlined,
                    message: '暂无商品',
                  );
                }
                return ProductGrid(products: products);
              },
              loading: () => const Center(child: CircularProgressIndicator()),
              error: (error, _) => Center(child: Text('加载失败: $error')),
            ),
          ),
        ],
      ),
    );
  }
}
```

### 7. 购物车 Provider

```dart
// features/cart/presentation/providers/cart_provider.dart
class CartItem {
  final Product product;
  int quantity;

  CartItem({required this.product, this.quantity = 1});

  double get totalPrice => product.price * quantity;
}

class CartNotifier extends StateNotifier<List<CartItem>> {
  CartNotifier() : super([]);

  void addProduct(Product product) {
    final index = state.indexWhere((item) => item.product.id == product.id);
    if (index >= 0) {
      state[index].quantity++;
      state = [...state];
    } else {
      state = [...state, CartItem(product: product)];
    }
  }

  void removeProduct(String productId) {
    state = state.where((item) => item.product.id != productId).toList();
  }

  void updateQuantity(String productId, int quantity) {
    if (quantity <= 0) {
      removeProduct(productId);
      return;
    }
    final index = state.indexWhere((item) => item.product.id == productId);
    if (index >= 0) {
      state[index].quantity = quantity;
      state = [...state];
    }
  }

  void clear() {
    state = [];
  }

  int get itemCount => state.fold(0, (sum, item) => sum + item.quantity);

  double get totalPrice => state.fold(0, (sum, item) => sum + item.totalPrice);
}

final cartProvider = StateNotifierProvider<CartNotifier, List<CartItem>>((ref) {
  return CartNotifier();
});

final cartItemCountProvider = Provider<int>((ref) {
  return ref.watch(cartProvider.notifier).itemCount;
});
```

### 8. 路由配置

```dart
// core/router/app_router.dart
final goRouter = GoRouter(
  initialLocation: '/products',
  redirect: (context, state) {
    final authState = ref.read(authProvider);
    final isLoggedIn = authState is AuthAuthenticated;
    final isAuthRoute = state.matchedLocation.startsWith('/auth');

    if (!isLoggedIn && !isAuthRoute) {
      return '/auth/login';
    }
    if (isLoggedIn && isAuthRoute) {
      return '/products';
    }
    return null;
  },
  routes: [
    // 认证路由
    GoRoute(
      path: '/auth',
      routes: [
        GoRoute(
          path: 'login',
          builder: (context, state) => const LoginPage(),
        ),
        GoRoute(
          path: 'register',
          builder: (context, state) => const RegisterPage(),
        ),
      ],
    ),

    // 主应用路由
    ShellRoute(
      builder: (context, state, child) => MainShell(child: child),
      routes: [
        GoRoute(
          path: '/products',
          builder: (context, state) => const ProductListPage(),
          routes: [
            GoRoute(
              path: ':id',
              builder: (context, state) {
                final id = state.pathParameters['id']!;
                return ProductDetailPage(productId: id);
              },
            ),
          ],
        ),
        GoRoute(
          path: '/cart',
          builder: (context, state) => const CartPage(),
        ),
        GoRoute(
          path: '/profile',
          builder: (context, state) => const ProfilePage(),
        ),
      ],
    ),
  ],
);
```

## 开发流程建议

### 第一阶段：基础搭建
1. 创建项目，配置依赖
2. 搭建目录结构
3. 实现网络层和存储层
4. 配置路由

### 第二阶段：核心功能
1. 实现用户认证
2. 实现商品列表
3. 实现商品详情
4. 实现购物车

### 第三阶段：完善优化
1. 添加搜索功能
2. 添加收藏功能
3. 优化 UI 细节
4. 添加加载状态和错误处理

### 第四阶段：测试发布
1. 编写单元测试
2. 编写 Widget 测试
3. 性能优化
4. 打包发布

---

*最后更新: 2026-02-19*

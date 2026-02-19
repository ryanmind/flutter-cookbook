# Flutter 项目结构最佳实践

## 概述

良好的项目结构能提升代码可维护性、可扩展性和团队协作效率。本文介绍 Flutter 项目的推荐目录结构和组织方式。

## 推荐目录结构

### 基础结构

```
lib/
├── main.dart              # 应用入口
├── app.dart               # App 配置
├── src/                   # 源代码目录
│   ├── core/              # 核心模块
│   ├── features/          # 功能模块
│   ├── shared/            # 共享资源
│   └── utils/             # 工具类
├── l10n.dart              # 国际化配置
└── router.dart            # 路由配置
```

### 完整结构示例

```
lib/
├── main.dart                    # 应用入口
├── app.dart                     # MaterialApp 配置
│
├── src/
│   ├── core/                    # 核心模块
│   │   ├── constants/           # 常量
│   │   │   ├── app_colors.dart
│   │   │   ├── app_strings.dart
│   │   │   └── app_values.dart
│   │   ├── theme/               # 主题
│   │   │   ├── app_theme.dart
│   │   │   ├── light_theme.dart
│   │   │   └── dark_theme.dart
│   │   ├── network/             # 网络层
│   │   │   ├── api_client.dart
│   │   │   ├── api_interceptor.dart
│   │   │   └── api_exception.dart
│   │   ├── storage/             # 存储
│   │   │   ├── local_storage.dart
│   │   │   └── secure_storage.dart
│   │   └── router/              # 路由
│   │       ├── app_router.dart
│   │       └── route_guards.dart
│   │
│   ├── features/                # 功能模块（按功能划分）
│   │   ├── auth/                # 认证模块
│   │   │   ├── data/            # 数据层
│   │   │   │   ├── models/
│   │   │   │   │   └── user.dart
│   │   │   │   ├── repositories/
│   │   │   │   │   └── auth_repository.dart
│   │   │   │   └── datasources/
│   │   │   │       ├── auth_local_ds.dart
│   │   │   │       └── auth_remote_ds.dart
│   │   │   ├── domain/          # 领域层
│   │   │   │   ├── entities/
│   │   │   │   │   └── user.dart
│   │   │   │   └── usecases/
│   │   │   │       └── login_usecase.dart
│   │   │   └── presentation/    # 展示层
│   │   │       ├── pages/
│   │   │       │   ├── login_page.dart
│   │   │       │   └── register_page.dart
│   │   │       ├── widgets/
│   │   │       │   └── login_form.dart
│   │   │       └── providers/   # 或 blocs/
│   │   │           └── auth_provider.dart
│   │   │
│   │   ├── home/                # 首页模块
│   │   │   ├── data/
│   │   │   ├── domain/
│   │   │   └── presentation/
│   │   │
│   │   ├── profile/             # 个人中心模块
│   │   │
│   │   └── settings/            # 设置模块
│   │
│   ├── shared/                  # 共享资源
│   │   ├── widgets/             # 共享组件
│   │   │   ├── custom_button.dart
│   │   │   ├── loading_indicator.dart
│   │   │   └── empty_state.dart
│   │   ├── providers/           # 全局状态
│   │   │   └── app_provider.dart
│   │   └── extensions/          # 扩展方法
│   │       ├── string_extension.dart
│   │       └── context_extension.dart
│   │
│   └── utils/                   # 工具类
│       ├── logger.dart
│       ├── validators.dart
│       └── helpers.dart
│
├── l10n/                        # 国际化
│   ├── app_en.arb
│   ├── app_zh.arb
│   └── l10n.dart
│
└── gen/                         # 生成的代码
    ├── assets.gen.dart
    └── fonts.gen.dart
```

## 架构模式

### 分层架构

```
┌─────────────────────────────────────┐
│          Presentation               │  ← UI、Widget、状态管理
│  (Pages, Widgets, Providers/Bloc)   │
├─────────────────────────────────────┤
│             Domain                  │  ← 业务逻辑、实体
│      (Entities, Use Cases)          │
├─────────────────────────────────────┤
│              Data                   │  ← 数据源、仓库
│  (Models, Repositories, DataSource) │
└─────────────────────────────────────┘
```

### 目录职责

| 目录 | 职责 |
|------|------|
| `presentation/` | UI 组件、页面、状态管理 |
| `domain/` | 业务实体、用例、仓库接口 |
| `data/` | 数据模型、仓库实现、数据源 |

## 文件命名规范

### 文件命名

```dart
// 使用 snake_case
user_profile_page.dart     // ✅
user_profile_widget.dart   // ✅
auth_repository.dart       // ✅

// 避免
userProfilePage.dart       // ❌
user-profile-page.dart     // ❌
```

### 类命名

```dart
// 使用 PascalCase
class UserProfilePage {}           // ✅
class AuthRepository {}            // ✅
class UserLoginFormWidget {}       // ✅

// 文件与类对应
// user_profile_page.dart → class UserProfilePage
// auth_repository.dart → class AuthRepository
```

### 导出文件

```dart
// features/auth/index.dart
export 'data/models/user.dart';
export 'data/repositories/auth_repository.dart';
export 'presentation/pages/login_page.dart';
export 'presentation/providers/auth_provider.dart';

// 使用时简化导入
import 'package:app/features/auth/index.dart';
```

## 模块组织

### 功能优先（推荐）

按功能模块划分，每个功能模块独立完整：

```
features/
├── auth/
│   ├── data/
│   ├── domain/
│   └── presentation/
├── home/
│   └── presentation/
└── settings/
    └── presentation/
```

**优点：**
- 高内聚，功能边界清晰
- 易于添加/移除功能
- 支持团队并行开发

### 类型优先（传统）

按文件类型划分：

```
lib/
├── models/
│   ├── user.dart
│   └── product.dart
├── views/
│   ├── login_page.dart
│   └── home_page.dart
├── viewmodels/
│   └── login_viewmodel.dart
└── services/
    └── auth_service.dart
```

**缺点：**
- 低内聚，相关代码分散
- 功能扩展时需修改多处
- 不适合大型项目

## 常见模块示例

### 认证模块

```dart
// features/auth/data/models/user.dart
class User {
  final String id;
  final String email;
  final String name;

  const User({
    required this.id,
    required this.email,
    required this.name,
  });

  factory User.fromJson(Map<String, dynamic> json) => User(
        id: json['id'],
        email: json['email'],
        name: json['name'],
      );

  Map<String, dynamic> toJson() => {
        'id': id,
        'email': email,
        'name': name,
      };
}
```

```dart
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
    final user = await _remote.login(email, password);
    await _local.saveUser(user);
    return user;
  }

  // ... 其他方法实现
}
```

```dart
// features/auth/presentation/providers/auth_provider.dart
class AuthNotifier extends StateNotifier<AuthState> {
  final AuthRepository _repository;

  AuthNotifier(this._repository) : super(const AuthState.initial()) {
    _checkAuth();
  }

  Future<void> _checkAuth() async {
    final user = await _repository.getCurrentUser();
    state = user != null
        ? AuthState.authenticated(user)
        : const AuthState.unauthenticated();
  }

  Future<void> login(String email, String password) async {
    state = const AuthState.loading();
    try {
      final user = await _repository.login(email, password);
      state = AuthState.authenticated(user);
    } catch (e) {
      state = AuthState.error(e.toString());
    }
  }
}
```

```dart
// features/auth/presentation/pages/login_page.dart
class LoginPage extends ConsumerWidget {
  const LoginPage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final authState = ref.watch(authProvider);

    return Scaffold(
      body: switch (authState) {
        AuthLoading() => const Center(child: CircularProgressIndicator()),
        AuthError(:final message) => Center(child: Text(message)),
        AuthAuthenticated() => const HomePage(),
        AuthUnauthenticated() => const LoginForm(),
      },
    );
  }
}
```

### 共享组件模块

```dart
// shared/widgets/custom_button.dart
class CustomButton extends StatelessWidget {
  final String label;
  final VoidCallback? onPressed;
  final bool loading;
  final ButtonType type;

  const CustomButton({
    super.key,
    required this.label,
    this.onPressed,
    this.loading = false,
    this.type = ButtonType.filled,
  });

  @override
  Widget build(BuildContext context) {
    return switch (type) {
      ButtonType.filled => FilledButton(
          onPressed: loading ? null : onPressed,
          child: loading ? const _LoadingIndicator() : Text(label),
        ),
      ButtonType.outlined => OutlinedButton(
          onPressed: loading ? null : onPressed,
          child: loading ? const _LoadingIndicator() : Text(label),
        ),
      ButtonType.text => TextButton(
          onPressed: loading ? null : onPressed,
          child: loading ? const _LoadingIndicator() : Text(label),
        ),
    };
  }
}
```

```dart
// shared/widgets/loading_indicator.dart
class LoadingIndicator extends StatelessWidget {
  final double size;
  final Color? color;

  const LoadingIndicator({
    super.key,
    this.size = 24,
    this.color,
  });

  @override
  Widget build(BuildContext context) {
    return SizedBox(
      width: size,
      height: size,
      child: CircularProgressIndicator(
        strokeWidth: 2,
        valueColor: AlwaysStoppedAnimation<Color>(
          color ?? Theme.of(context).colorScheme.primary,
        ),
      ),
    );
  }
}
```

## 资源管理

### assets 目录

```
assets/
├── images/
│   ├── 2.0x/
│   ├── 3.0x/
│   └── logo.png
├── icons/
│   └── app_icon.svg
├── fonts/
│   └── custom_font.ttf
└── json/
    └── config.json
```

### pubspec.yaml

```yaml
flutter:
  assets:
    - assets/images/
    - assets/icons/
    - assets/json/

  fonts:
    - family: CustomFont
      fonts:
        - asset: assets/fonts/custom_font.ttf
        - asset: assets/fonts/custom_font_bold.ttf
          weight: 700
```

## 环境配置

### 多环境配置

```dart
// lib/src/core/config/app_config.dart
enum Environment { development, staging, production }

class AppConfig {
  final Environment environment;
  final String apiUrl;
  final String apiKey;

  const AppConfig({
    required this.environment,
    required this.apiUrl,
    required this.apiKey,
  });

  static late AppConfig _instance;

  static AppConfig get instance => _instance;

  static void initialize(Environment env) {
    _instance = switch (env) {
      Environment.development => const AppConfig(
          environment: Environment.development,
          apiUrl: 'https://dev.api.example.com',
          apiKey: 'dev_key',
        ),
      Environment.staging => const AppConfig(
          environment: Environment.staging,
          apiUrl: 'https://staging.api.example.com',
          apiKey: 'staging_key',
        ),
      Environment.production => const AppConfig(
          environment: Environment.production,
          apiUrl: 'https://api.example.com',
          apiKey: 'prod_key',
        ),
    };
  }
}
```

```dart
// main.dart
void main() {
  AppConfig.initialize(Environment.development);
  runApp(const MyApp());
}
```

## 最佳实践

### 1. 单一职责

每个文件只负责一件事：

```dart
// ❌ 不好：一个文件包含多个类
// user.dart
class User {}
class UserRepository {}
class UserService {}

// ✅ 好：每个文件一个类
// user.dart
class User {}

// user_repository.dart
class UserRepository {}
```

### 2. 依赖注入

```dart
// 使用依赖注入容器
final getIt = GetIt.instance;

void setupDependencies() {
  // 注册单例
  getIt.registerSingleton<Dio>(Dio());
  getIt.registerSingleton<ApiClient>(ApiClient(getIt<Dio>()));

  // 注册工厂
  getIt.registerFactory<AuthRepository>(() => AuthRepositoryImpl(getIt()));
}
```

### 3. 统一导入

```dart
// 使用 package 导入，而非相对路径
// ❌ 不好
import '../../../core/theme/app_theme.dart';
import '../../shared/widgets/custom_button.dart';

// ✅ 好
import 'package:myapp/src/core/theme/app_theme.dart';
import 'package:myapp/src/shared/widgets/custom_button.dart';
```

---

*最后更新: 2026-02-19*

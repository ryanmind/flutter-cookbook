# Flutter 常用第三方库指南

## 概述

本文介绍 Flutter 开发中常用的第三方库，帮助快速选择合适的工具。

## 网络请求

### Dio

功能强大的 HTTP 客户端。

```yaml
dependencies:
  dio: ^5.4.0
```

```dart
import 'package:dio/dio.dart';

final dio = Dio();

// GET 请求
final response = await dio.get('https://api.example.com/users');

// POST 请求
final response = await dio.post(
  'https://api.example.com/users',
  data: {'name': 'John', 'age': 25},
);

// 拦截器
dio.interceptors.add(
  InterceptorsWrapper(
    onRequest: (options, handler) {
      options.headers['Authorization'] = 'Bearer $token';
      return handler.next(options);
    },
    onResponse: (response, handler) {
      return handler.next(response);
    },
    onError: (error, handler) {
      return handler.next(error);
    },
  ),
);

// 日志拦截器
dio.interceptors.add(LogInterceptor(
  request: true,
  requestBody: true,
  responseBody: true,
));
```

### Retrofit

类型安全的 HTTP 客户端生成器。

```yaml
dependencies:
  retrofit: ^4.0.0
  dio: ^5.4.0

dev_dependencies:
  retrofit_generator: ^8.0.0
  build_runner: ^2.4.0
```

```dart
import 'package:retrofit/retrofit.dart';
import 'package:dio/dio.dart';

part 'api_client.g.dart';

@RestApi(baseUrl: 'https://api.example.com')
abstract class ApiClient {
  factory ApiClient(Dio dio) = _ApiClient;

  @GET('/users/{id}')
  Future<User> getUser(@Path('id') String id);

  @GET('/users')
  Future<List<User>> getUsers(@Query('page') int page);

  @POST('/users')
  Future<User> createUser(@Body() User user);

  @PUT('/users/{id}')
  Future<User> updateUser(@Path('id') String id, @Body() User user);

  @DELETE('/users/{id}')
  Future<void> deleteUser(@Path('id') String id);
}

// 生成代码
// flutter pub run build_runner build
```

## 状态管理

### Riverpod

响应式状态管理。

```yaml
dependencies:
  flutter_riverpod: ^2.4.0
```

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

// Provider 定义
final counterProvider = StateProvider<int>((ref) => 0);

final userListProvider = FutureProvider<List<User>>((ref) async {
  final api = ref.watch(apiProvider);
  return api.getUsers();
});

// 使用
class CounterWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);
    return Text('Count: $count');
  }
}

// 修改状态
ref.read(counterProvider.notifier).state++;
```

### Flutter Bloc

事件驱动的状态管理。

```yaml
dependencies:
  flutter_bloc: ^8.1.0
```

```dart
import 'package:flutter_bloc/flutter_bloc.dart';

// Event
sealed class CounterEvent {}
class Increment extends CounterEvent {}
class Decrement extends CounterEvent {}

// State
class CounterState {
  final int count;
  const CounterState(this.count);
}

// Bloc
class CounterBloc extends Bloc<CounterEvent, CounterState> {
  CounterBloc() : super(const CounterState(0)) {
    on<Increment>((event, emit) => emit(CounterState(state.count + 1)));
    on<Decrement>((event, emit) => emit(CounterState(state.count - 1)));
  }
}

// 使用
BlocProvider(
  create: (_) => CounterBloc(),
  child: BlocBuilder<CounterBloc, CounterState>(
    builder: (context, state) {
      return Text('Count: ${state.count}');
    },
  ),
);
```

## 本地存储

### SharedPreferences

简单的键值存储。

```yaml
dependencies:
  shared_preferences: ^2.2.0
```

```dart
import 'package:shared_preferences/shared_preferences.dart';

final prefs = await SharedPreferences.getInstance();

// 存储
await prefs.setString('name', 'John');
await prefs.setInt('age', 25);
await prefs.setBool('is_logged_in', true);
await prefs.setStringList('tags', ['flutter', 'dart']);

// 读取
final name = prefs.getString('name');
final age = prefs.getInt('age');
final isLoggedIn = prefs.getBool('is_logged_in') ?? false;

// 删除
await prefs.remove('name');
await prefs.clear();
```

### Hive

高性能 NoSQL 数据库。

```yaml
dependencies:
  hive: ^2.2.0
  hive_flutter: ^1.1.0

dev_dependencies:
  hive_generator: ^2.0.0
  build_runner: ^2.4.0
```

```dart
import 'package:hive_flutter/hive_flutter.dart';

// 初始化
await Hive.initFlutter();

// 定义模型
@HiveType(typeId: 0)
class User extends HiveObject {
  @HiveField(0)
  String name;

  @HiveField(1)
  int age;

  User({required this.name, required this.age});
}

// 注册适配器
Hive.registerAdapter(UserAdapter());

// 打开 Box
final userBox = await Hive.openBox<User>('users');

// CRUD 操作
await userBox.add(User(name: 'John', age: 25));
final user = userBox.getAt(0);
await userBox.put(0, User(name: 'Jane', age: 30));
await userBox.deleteAt(0);

// 监听变化
userBox.watch().listen((event) {
  print('Box changed: ${event.key}');
});
```

### Flutter Secure Storage

安全的本地存储。

```yaml
dependencies:
  flutter_secure_storage: ^9.0.0
```

```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

const storage = FlutterSecureStorage();

// 存储
await storage.write(key: 'token', value: 'abc123');

// 读取
final token = await storage.read(key: 'token');

// 删除
await storage.delete(key: 'token');
await storage.deleteAll();
```

## 图片处理

### CachedNetworkImage

网络图片缓存。

```yaml
dependencies:
  cached_network_image: ^3.3.0
```

```dart
import 'package:cached_network_image/cached_network_image.dart';

CachedNetworkImage(
  imageUrl: 'https://example.com/image.jpg',
  placeholder: (context, url) => CircularProgressIndicator(),
  errorWidget: (context, url, error) => Icon(Icons.error),
  fadeInDuration: Duration(milliseconds: 300),
  memCacheWidth: 300, // 内存缓存宽度
  maxWidthDiskCache: 500, // 磁盘缓存宽度
);
```

### Image Picker

图片/视频选择。

```yaml
dependencies:
  image_picker: ^1.0.0
```

```dart
import 'package:image_picker/image_picker.dart';

final picker = ImagePicker();

// 拍照
final photo = await picker.pickImage(source: ImageSource.camera);

// 从相册选择
final image = await picker.pickImage(source: ImageSource.gallery);

// 多选
final images = await picker.pickMultiImage();

// 视频
final video = await picker.pickVideo(source: ImageSource.gallery);
```

## 路由

### GoRouter

声明式路由。

```yaml
dependencies:
  go_router: ^13.0.0
```

```dart
import 'package:go_router/go_router.dart';

final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => HomePage(),
    ),
    GoRoute(
      path: '/user/:id',
      builder: (context, state) {
        final id = state.pathParameters['id'];
        return UserPage(userId: id!);
      },
    ),
    GoRoute(
      path: '/settings',
      builder: (context, state) => SettingsPage(),
    ),
  ],
);

// 使用
context.go('/user/123');
context.push('/settings');
context.pop();
```

## 工具类

### Freezed

不可变数据类生成。

```yaml
dependencies:
  freezed_annotation: ^2.4.0

dev_dependencies:
  freezed: ^2.4.0
  build_runner: ^2.4.0
```

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'user.freezed.dart';
part 'user.g.dart';

@freezed
class User with _$User {
  const factory User({
    required String id,
    required String name,
    String? email,
    @Default([]) List<String> tags,
  }) = _User;

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
}

// 使用
final user = User(id: '1', name: 'John');
final copy = user.copyWith(email: 'john@example.com');
```

### Url Launcher

打开链接、电话等。

```yaml
dependencies:
  url_launcher: ^6.2.0
```

```dart
import 'package:url_launcher/url_launcher.dart';

// 打开网页
await launchUrl(Uri.parse('https://flutter.dev'));

// 拨打电话
await launchUrl(Uri.parse('tel:+1234567890'));

// 发送短信
await launchUrl(Uri.parse('sms:+1234567890'));

// 发送邮件
await launchUrl(Uri.parse('mailto:test@example.com?subject=Hello&body=World'));
```

### Flutter Animate

动画简化。

```yaml
dependencies:
  flutter_animate: ^4.3.0
```

```dart
import 'package:flutter_animate/flutter_animate.dart';

Text('Hello World')
  .animate()
  .fadeIn(duration: 300.ms)
  .slideX(begin: -1, end: 0)
  .then()
  .shake(hz: 2, duration: 500.ms);

Container()
  .animate(
    onPlay: (controller) => controller.repeat(),
  )
  .rotate(duration: 2.seconds);
```

## UI 组件

### Flutter ScreenUtil

屏幕适配。

```yaml
dependencies:
  flutter_screenutil: ^5.9.0
```

```dart
import 'package:flutter_screenutil/flutter_screenutil.dart';

void main() {
  runApp(
    ScreenUtilInit(
      designSize: Size(375, 812),
      child: MyApp(),
    ),
  );
}

// 使用
Container(
  width: 100.w,  // 相对于设计稿宽度
  height: 100.h, // 相对于设计稿高度
  padding: EdgeInsets.all(16.sp),
  child: Text(
    'Hello',
    style: TextStyle(fontSize: 14.sp),
  ),
);
```

### Shimmer

骨架屏加载效果。

```yaml
dependencies:
  shimmer: ^3.0.0
```

```dart
import 'package:shimmer/shimmer.dart';

Shimmer.fromColors(
  baseColor: Colors.grey[300]!,
  highlightColor: Colors.grey[100]!,
  child: Container(
    width: 200,
    height: 20,
    color: Colors.white,
  ),
);
```

## 常用库速查表

| 类别 | 库名 | 用途 |
|------|------|------|
| 网络请求 | dio | HTTP 客户端 |
| 网络请求 | retrofit | 类型安全 API |
| 状态管理 | flutter_riverpod | 响应式状态 |
| 状态管理 | flutter_bloc | 事件驱动 |
| 本地存储 | shared_preferences | 简单 KV |
| 本地存储 | hive | NoSQL 数据库 |
| 安全存储 | flutter_secure_storage | 加密存储 |
| 图片 | cached_network_image | 图片缓存 |
| 图片 | image_picker | 图片选择 |
| 路由 | go_router | 声明式路由 |
| 代码生成 | freezed | 不可变类 |
| 工具 | url_launcher | 打开链接 |
| 动画 | flutter_animate | 简化动画 |
| UI | flutter_screenutil | 屏幕适配 |
| UI | shimmer | 骨架屏 |

---

*最后更新: 2026-02-19*

# Flutter 网络请求 - 初学者指南

## 目录
1. [HTTP 基础](#http-基础)
2. [使用 http 包](#使用-http-包)
3. [使用 dio 包](#使用-dio-包)
4. [JSON 解析](#json-解析)
5. [错误处理](#错误处理)
6. [网络请求架构](#网络请求架构)

## HTTP 基础

### 添加依赖

```yaml
# pubspec.yaml
dependencies:
  http: ^1.1.0        # 官方 HTTP 包
  # 或
  dio: ^5.4.0         # 功能更强大的第三方包
```

### HTTP 方法

```
GET     - 获取资源
POST    - 创建资源
PUT     - 更新资源（完整）
PATCH   - 更新资源（部分）
DELETE  - 删除资源
```

## 使用 http 包

### GET 请求

```dart
import 'package:http/http.dart' as http;
import 'dart:convert';

Future<User> fetchUser(String id) async {
  final response = await http.get(
    Uri.parse('https://api.example.com/users/$id'),
  );
  
  if (response.statusCode == 200) {
    final json = jsonDecode(response.body);
    return User.fromJson(json);
  } else {
    throw Exception('Failed to load user');
  }
}
```

### POST 请求

```dart
Future<User> createUser(User user) async {
  final response = await http.post(
    Uri.parse('https://api.example.com/users'),
    headers: {'Content-Type': 'application/json'},
    body: jsonEncode(user.toJson()),
  );
  
  if (response.statusCode == 201) {
    final json = jsonDecode(response.body);
    return User.fromJson(json);
  } else {
    throw Exception('Failed to create user');
  }
}
```

### 带请求头

```dart
Future<List<Post>> fetchPosts(String token) async {
  final response = await http.get(
    Uri.parse('https://api.example.com/posts'),
    headers: {
      'Authorization': 'Bearer $token',
      'Accept': 'application/json',
    },
  );
  
  if (response.statusCode == 200) {
    final List<dynamic> json = jsonDecode(response.body);
    return json.map((e) => Post.fromJson(e)).toList();
  } else {
    throw Exception('Failed to load posts');
  }
}
```

### 查询参数

```dart
Future<List<Product>> fetchProducts({
  String? category,
  int page = 1,
  int limit = 20,
}) async {
  final queryParameters = {
    if (category != null) 'category': category,
    'page': page.toString(),
    'limit': limit.toString(),
  };
  
  final uri = Uri.https('api.example.com', '/products', queryParameters);
  
  final response = await http.get(uri);
  
  if (response.statusCode == 200) {
    final List<dynamic> json = jsonDecode(response.body);
    return json.map((e) => Product.fromJson(e)).toList();
  } else {
    throw Exception('Failed to load products');
  }
}
```

### 文件上传

```dart
Future<void> uploadImage(File image, String token) async {
  final request = http.MultipartRequest(
    'POST',
    Uri.parse('https://api.example.com/upload'),
  );
  
  request.headers['Authorization'] = 'Bearer $token';
  request.files.add(
    await http.MultipartFile.fromPath('image', image.path),
  );
  
  final response = await request.send();
  
  if (response.statusCode != 200) {
    throw Exception('Failed to upload image');
  }
}
```

## 使用 dio 包

### 基本配置

```dart
import 'package:dio/dio.dart';

final dio = Dio(BaseOptions(
  baseUrl: 'https://api.example.com',
  connectTimeout: Duration(seconds: 5),
  receiveTimeout: Duration(seconds: 10),
  headers: {
    'Accept': 'application/json',
  },
));
```

### GET 请求

```dart
Future<User> fetchUser(String id) async {
  try {
    final response = await dio.get('/users/$id');
    return User.fromJson(response.data);
  } on DioException catch (e) {
    throw _handleError(e);
  }
}

Future<List<User>> fetchUsers({int page = 1}) async {
  try {
    final response = await dio.get(
      '/users',
      queryParameters: {'page': page},
    );
    final List<dynamic> data = response.data;
    return data.map((e) => User.fromJson(e)).toList();
  } on DioException catch (e) {
    throw _handleError(e);
  }
}
```

### POST 请求

```dart
Future<User> createUser(User user) async {
  try {
    final response = await dio.post(
      '/users',
      data: user.toJson(),
    );
    return User.fromJson(response.data);
  } on DioException catch (e) {
    throw _handleError(e);
  }
}

// 发送表单数据
Future<void> submitForm(String name, String email) async {
  try {
    await dio.post(
      '/submit',
      data: FormData.fromMap({
        'name': name,
        'email': email,
      }),
    );
  } on DioException catch (e) {
    throw _handleError(e);
  }
}
```

### 拦截器

```dart
// 认证拦截器
class AuthInterceptor extends Interceptor {
  final String Function() getToken;
  
  AuthInterceptor({required this.getToken});
  
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    final token = getToken();
    if (token.isNotEmpty) {
      options.headers['Authorization'] = 'Bearer $token';
    }
    handler.next(options);
  }
}

// 日志拦截器
class LoggingInterceptor extends Interceptor {
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    print('REQUEST[${options.method}] => ${options.uri}');
    handler.next(options);
  }
  
  @override
  void onResponse(Response response, ResponseInterceptorHandler handler) {
    print('RESPONSE[${response.statusCode}] => ${response.requestOptions.uri}');
    handler.next(response);
  }
  
  @override
  void onError(DioException err, ErrorInterceptorHandler handler) {
    print('ERROR[${err.response?.statusCode}] => ${err.requestOptions.uri}');
    handler.next(err);
  }
}

// 使用拦截器
final dio = Dio()
  ..interceptors.addAll([
    AuthInterceptor(getToken: () => 'your-token'),
    LoggingInterceptor(),
  ]);
```

### 文件上传

```dart
Future<String> uploadFile(File file) async {
  try {
    final formData = FormData.fromMap({
      'file': await MultipartFile.fromFile(
        file.path,
        filename: file.path.split('/').last,
      ),
    });
    
    final response = await dio.post('/upload', data: formData);
    return response.data['url'];
  } on DioException catch (e) {
    throw _handleError(e);
  }
}

// 带进度的上传
Future<void> uploadWithProgress(File file, Function(int, int) onProgress) async {
  try {
    await dio.post(
      '/upload',
      data: FormData.fromMap({
        'file': await MultipartFile.fromFile(file.path),
      }),
      onSendProgress: onProgress,
    );
  } on DioException catch (e) {
    throw _handleError(e);
  }
}
```

### 文件下载

```dart
Future<void> downloadFile(String url, String savePath) async {
  try {
    await dio.download(
      url,
      savePath,
      onReceiveProgress: (received, total) {
        if (total != -1) {
          print('${(received / total * 100).toStringAsFixed(0)}%');
        }
      },
    );
  } on DioException catch (e) {
    throw _handleError(e);
  }
}
```

## JSON 解析

### 手动解析

```dart
class User {
  final String id;
  final String name;
  final String email;
  final int age;
  
  User({
    required this.id,
    required this.name,
    required this.email,
    required this.age,
  });
  
  // 从 JSON 创建
  factory User.fromJson(Map<String, dynamic> json) {
    return User(
      id: json['id'] as String,
      name: json['name'] as String,
      email: json['email'] as String,
      age: json['age'] as int,
    );
  }
  
  // 转换为 JSON
  Map<String, dynamic> toJson() {
    return {
      'id': id,
      'name': name,
      'email': email,
      'age': age,
    };
  }
}
```

### 嵌套对象解析

```dart
class Post {
  final String id;
  final String title;
  final User author;
  final List<Comment> comments;
  
  Post({
    required this.id,
    required this.title,
    required this.author,
    required this.comments,
  });
  
  factory Post.fromJson(Map<String, dynamic> json) {
    return Post(
      id: json['id'] as String,
      title: json['title'] as String,
      author: User.fromJson(json['author'] as Map<String, dynamic>),
      comments: (json['comments'] as List)
          .map((e) => Comment.fromJson(e as Map<String, dynamic>))
          .toList(),
    );
  }
  
  Map<String, dynamic> toJson() {
    return {
      'id': id,
      'title': title,
      'author': author.toJson(),
      'comments': comments.map((e) => e.toJson()).toList(),
    };
  }
}
```

### 可空字段处理

```dart
class Profile {
  final String id;
  final String name;
  final String? avatar;  // 可空
  final String? bio;     // 可空
  
  Profile({
    required this.id,
    required this.name,
    this.avatar,
    this.bio,
  });
  
  factory Profile.fromJson(Map<String, dynamic> json) {
    return Profile(
      id: json['id'] as String,
      name: json['name'] as String,
      avatar: json['avatar'] as String?,  // 可空转换
      bio: json['bio'] as String?,
    );
  }
  
  Map<String, dynamic> toJson() {
    return {
      'id': id,
      'name': name,
      if (avatar != null) 'avatar': avatar,
      if (bio != null) 'bio': bio,
    };
  }
}
```

### 使用 json_serializable（代码生成）

```yaml
# pubspec.yaml
dependencies:
  json_annotation: ^4.8.0

dev_dependencies:
  json_serializable: ^6.7.0
  build_runner: ^2.4.0
```

```dart
import 'package:json_annotation/json_annotation.dart';

part 'user.g.dart';

@JsonSerializable()
class User {
  final String id;
  final String name;
  final String email;
  
  @JsonKey(name: 'created_at')
  final DateTime createdAt;
  
  User({
    required this.id,
    required this.name,
    required this.email,
    required this.createdAt,
  });
  
  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}

// 运行代码生成
// flutter pub run build_runner build
```

## 错误处理

### 自定义异常

```dart
// 网络异常基类
class NetworkException implements Exception {
  final String message;
  final int? statusCode;
  
  NetworkException(this.message, {this.statusCode});
  
  @override
  String toString() => 'NetworkException: $message (status: $statusCode)';
}

// 特定异常
class BadRequestException extends NetworkException {
  BadRequestException(String message) : super(message, statusCode: 400);
}

class UnauthorizedException extends NetworkException {
  UnauthorizedException() : super('未授权', statusCode: 401);
}

class NotFoundException extends NetworkException {
  NotFoundException(String message) : super(message, statusCode: 404);
}

class ServerException extends NetworkException {
  ServerException(String message) : super(message, statusCode: 500);
}

class NoInternetException extends NetworkException {
  NoInternetException() : super('无网络连接');
}

class TimeoutException extends NetworkException {
  TimeoutException() : super('请求超时');
}
```

### 统一错误处理

```dart
Exception _handleError(DioException error) {
  switch (error.type) {
    case DioExceptionType.connectionTimeout:
    case DioExceptionType.sendTimeout:
    case DioExceptionType.receiveTimeout:
      return TimeoutException();
    
    case DioExceptionType.badResponse:
      final statusCode = error.response?.statusCode;
      final message = error.response?.data['message'] ?? 'Unknown error';
      
      switch (statusCode) {
        case 400:
          return BadRequestException(message);
        case 401:
          return UnauthorizedException();
        case 404:
          return NotFoundException(message);
        case 500:
        case 502:
        case 503:
          return ServerException(message);
        default:
          return NetworkException(message, statusCode: statusCode);
      }
    
    case DioExceptionType.connectionError:
      return NoInternetException();
    
    default:
      return NetworkException(error.message ?? 'Unknown error');
  }
}
```

### 在 UI 中处理错误

```dart
class UserPage extends StatefulWidget {
  @override
  _UserPageState createState() => _UserPageState();
}

class _UserPageState extends State<UserPage> {
  User? _user;
  bool _isLoading = false;
  String? _error;
  
  @override
  void initState() {
    super.initState();
    _loadUser();
  }
  
  Future<void> _loadUser() async {
    setState(() {
      _isLoading = true;
      _error = null;
    });
    
    try {
      final user = await fetchUser('123');
      setState(() {
        _user = user;
        _isLoading = false;
      });
    } on NetworkException catch (e) {
      setState(() {
        _error = e.message;
        _isLoading = false;
      });
    } catch (e) {
      setState(() {
        _error = '未知错误';
        _isLoading = false;
      });
    }
  }
  
  @override
  Widget build(BuildContext context) {
    if (_isLoading) {
      return Center(child: CircularProgressIndicator());
    }
    
    if (_error != null) {
      return Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('Error: $_error'),
            ElevatedButton(
              onPressed: _loadUser,
              child: Text('Retry'),
            ),
          ],
        ),
      );
    }
    
    if (_user == null) {
      return Center(child: Text('No data'));
    }
    
    return Text('User: ${_user!.name}');
  }
}
```

## 网络请求架构

### Repository 模式

```dart
// 数据模型
class User {
  final String id;
  final String name;
  // ...
}

// API 客户端
class UserApi {
  final Dio _dio;
  
  UserApi(this._dio);
  
  Future<User> getUser(String id) async {
    final response = await _dio.get('/users/$id');
    return User.fromJson(response.data);
  }
  
  Future<List<User>> getUsers() async {
    final response = await _dio.get('/users');
    return (response.data as List)
        .map((e) => User.fromJson(e))
        .toList();
  }
}

// Repository
class UserRepository {
  final UserApi _api;
  final UserCache _cache;
  
  UserRepository(this._api, this._cache);
  
  Future<User> getUser(String id) async {
    // 先从缓存获取
    final cached = _cache.get(id);
    if (cached != null) return cached;
    
    // 从网络获取
    final user = await _api.getUser(id);
    _cache.set(id, user);
    return user;
  }
  
  Future<void> refreshUser(String id) async {
    final user = await _api.getUser(id);
    _cache.set(id, user);
  }
}

// 使用 Provider 进行依赖注入
final dioProvider = Provider<Dio>((ref) {
  return Dio(BaseOptions(baseUrl: 'https://api.example.com'));
});

final userApiProvider = Provider<UserApi>((ref) {
  return UserApi(ref.watch(dioProvider));
});

final userRepositoryProvider = Provider<UserRepository>((ref) {
  return UserRepository(
    ref.watch(userApiProvider),
    UserCache(),
  );
});
```

### 完整示例

```dart
// api_client.dart
class ApiClient {
  final Dio _dio;
  
  ApiClient({required String baseUrl}) : _dio = Dio(BaseOptions(
    baseUrl: baseUrl,
    connectTimeout: Duration(seconds: 10),
    receiveTimeout: Duration(seconds: 30),
  )) {
    _dio.interceptors.addAll([
      LogInterceptor(requestBody: true, responseBody: true),
    ]);
  }
  
  void setAuthToken(String token) {
    _dio.options.headers['Authorization'] = 'Bearer $token';
  }
  
  Future<T> get<T>(
    String path, {
    Map<String, dynamic>? queryParameters,
    required T Function(Map<String, dynamic>) fromJson,
  }) async {
    try {
      final response = await _dio.get(path, queryParameters: queryParameters);
      return fromJson(response.data);
    } on DioException catch (e) {
      throw _handleError(e);
    }
  }
  
  Future<List<T>> getList<T>(
    String path, {
    Map<String, dynamic>? queryParameters,
    required T Function(Map<String, dynamic>) fromJson,
  }) async {
    try {
      final response = await _dio.get(path, queryParameters: queryParameters);
      return (response.data as List)
          .map((e) => fromJson(e))
          .toList();
    } on DioException catch (e) {
      throw _handleError(e);
    }
  }
  
  Future<T> post<T>(
    String path, {
    dynamic data,
    required T Function(Map<String, dynamic>) fromJson,
  }) async {
    try {
      final response = await _dio.post(path, data: data);
      return fromJson(response.data);
    } on DioException catch (e) {
      throw _handleError(e);
    }
  }
}
```

---

*最后更新: 2026-02-19*

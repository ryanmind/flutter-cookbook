# Flutter 错误处理

## 概述

错误处理是 Flutter 开发的核心技能，良好的错误处理能提升应用稳定性和用户体验。

## Dart 异常体系

### 异常类型

```dart
// Dart 中所有异常都是 Exception 或 Error 的子类

// Exception - 可预期的异常，应该被捕获处理
try {
  throw FormatException('格式错误');
} on FormatException catch (e) {
  print('捕获格式异常: $e');
}

// Error - 严重错误，通常不应捕获
// 如: NoSuchMethodError, RangeError, AssertionError
```

### 常见异常类型

```dart
// FormatException - 格式解析错误
int.parse('abc'); // 抛出 FormatException

// ArgumentError - 参数错误
void validate(String name) {
  if (name.isEmpty) {
    throw ArgumentError('name 不能为空');
  }
}

// RangeError - 范围错误
List<int> list = [1, 2, 3];
list[10]; // 抛出 RangeError

// NoSuchMethodError - 方法不存在
dynamic obj = 123;
obj.unknownMethod(); // 抛出 NoSuchMethodError

// StateError - 状态错误
List<int> emptyList = [];
emptyList.first; // 抛出 StateError
```

## try-catch-finally

### 基本语法

```dart
try {
  // 可能抛出异常的代码
  final result = int.parse('abc');
} on FormatException {
  // 特定异常处理
  print('格式错误');
} on Exception catch (e) {
  // 捕获 Exception 类型异常
  print('异常: $e');
} catch (e, stackTrace) {
  // 捕获所有异常，获取堆栈信息
  print('未知错误: $e');
  print('堆栈: $stackTrace');
} finally {
  // 无论是否异常都会执行
  print('清理资源');
}
```

### 重新抛出异常

```dart
void processFile(String path) {
  try {
    final content = File(path).readAsStringSync();
    // 处理内容
  } catch (e) {
    // 记录日志后重新抛出
    print('读取文件失败: $path, 错误: $e');
    rethrow; // 重新抛出，让调用者处理
  }
}
```

## Flutter 错误处理

### Widget 错误边界

```dart
// ErrorBoundary - 捕获子组件树错误
class ErrorBoundary extends StatelessWidget {
  final Widget child;
  final Widget Function(FlutterErrorDetails)? errorBuilder;

  const ErrorBoundary({
    super.key,
    required this.child,
    this.errorBuilder,
  });

  @override
  Widget build(BuildContext context) {
    return ErrorWidget.builder = (FlutterErrorDetails details) {
      return errorBuilder?.call(details) ??
          Center(
            child: Text('发生错误: ${details.exception}'),
          );
    };
  }
}
```

### 全局错误捕获

```dart
void main() {
  // 捕获 Flutter 框架错误
  FlutterError.onError = (FlutterErrorDetails details) {
    FlutterError.presentError(details);
    // 上报错误到监控系统
    reportError(details.exception, details.stack);
  };

  // 捕获 Dart 异步错误
  PlatformDispatcher.instance.onError = (error, stack) {
    print('未捕获的异步错误: $error');
    reportError(error, stack);
    return true; // 返回 true 表示已处理
  };

  runApp(const MyApp());
}
```

### 自定义错误页面

```dart
class ErrorScreen extends StatelessWidget {
  final FlutterErrorDetails details;

  const ErrorScreen({super.key, required this.details});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('出错了')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Icon(Icons.error_outline, size: 64, color: Colors.red),
            const SizedBox(height: 16),
            const Text('应用遇到了问题'),
            const SizedBox(height: 8),
            ElevatedButton(
              onPressed: () => Navigator.of(context).pop(),
              child: const Text('返回'),
            ),
          ],
        ),
      ),
    );
  }
}
```

## 异步错误处理

### Future 错误处理

```dart
// 方式1: try-catch (推荐)
Future<void> loadData() async {
  try {
    final response = await http.get(Uri.parse(url));
    // 处理响应
  } on SocketException {
    print('网络连接失败');
  } on HttpException {
    print('HTTP 请求失败');
  } catch (e) {
    print('未知错误: $e');
  }
}

// 方式2: catchError
http.get(Uri.parse(url))
    .then((response) => print(response.body))
    .catchError((error) => print('请求失败: $error'));

// 方式3: then 的 onError 参数
http.get(Uri.parse(url)).then(
  (response) => print(response.body),
  onError: (error) => print('请求失败: $error'),
);
```

### Stream 错误处理

```dart
Stream<int> createStream() async* {
  yield 1;
  yield 2;
  throw Exception('流错误'); // 流中的错误
}

// 方式1: try-catch
await for (final value in createStream()) {
  print(value);
}

// 方式2: listen
createStream().listen(
  (data) => print(data),
  onError: (error) => print('错误: $error'),
  onDone: () => print('完成'),
);

// 方式3: handleError
createStream()
    .handleError((error) => print('错误: $error'))
    .listen((data) => print(data));
```

## Result 模式

### 定义 Result 类型

```dart
/// 成功或失败的封装
sealed class Result<T> {
  const Result();
}

class Success<T> extends Result<T> {
  final T data;
  const Success(this.data);
}

class Failure<T> extends Result<T> {
  final String message;
  final Exception? exception;
  const Failure(this.message, [this.exception]);
}
```

### 使用 Result

```dart
// 返回 Result 而非抛出异常
Future<Result<User>> fetchUser(String id) async {
  try {
    final response = await http.get(Uri.parse('/users/$id'));
    if (response.statusCode == 200) {
      return Success(User.fromJson(jsonDecode(response.body)));
    } else {
      return Failure('用户不存在');
    }
  } on SocketException {
    return Failure('网络连接失败');
  } catch (e) {
    return Failure('请求失败: $e');
  }
}

// 调用方处理
Future<void> loadUser() async {
  final result = await fetchUser('123');
  switch (result) {
    case Success<User>(:final data):
      print('用户: ${data.name}');
    case Failure<User>(:final message):
      print('错误: $message');
  }
}
```

## 最佳实践

### 1. 区分异常类型

```dart
// ✅ 好的做法：使用特定异常类型
class NetworkException implements Exception {
  final String message;
  NetworkException(this.message);
}

class ValidationException implements Exception {
  final String field;
  final String message;
  ValidationException(this.field, this.message);
}

// 使用
try {
  await fetchData();
} on NetworkException {
  showNetworkError();
} on ValidationException catch (e) {
  showFieldError(e.field, e.message);
}
```

### 2. 避免空 catch

```dart
// ❌ 糟糕的做法
try {
  doSomething();
} catch (e) {
  // 吞掉错误
}

// ✅ 好的做法
try {
  doSomething();
} catch (e, stackTrace) {
  logger.error('操作失败', error: e, stackTrace: stackTrace);
  // 或者重新抛出
  rethrow;
}
```

### 3. 资源清理

```dart
// ✅ 使用 finally 确保资源释放
File? file;
try {
  file = File('data.txt');
  final content = await file.readAsString();
  process(content);
} catch (e) {
  print('读取失败: $e');
} finally {
  // 清理资源
}

// ✅ 或使用 try-with-resources 模式
Future<void> processFile(String path) async {
  final file = File(path);
  final content = await file.readAsString(); // 自动管理
}
```

### 4. 用户友好的错误提示

```dart
String getUserFriendlyMessage(dynamic error) {
  if (error is SocketException) {
    return '网络连接失败，请检查网络设置';
  } else if (error is HttpException) {
    return '服务器暂时无法响应';
  } else if (error is FormatException) {
    return '数据格式错误';
  } else {
    return '操作失败，请稍后重试';
  }
}

// 使用
try {
  await submitForm();
} catch (e) {
  ScaffoldMessenger.of(context).showSnackBar(
    SnackBar(content: Text(getUserFriendlyMessage(e))),
  );
}
```

## 常见错误场景

### 1. 空安全错误

```dart
// LateInitializationError
late String name;
print(name); // 错误：未初始化

// Null check operator
String? nullableName;
print(nullableName!); // 如果为 null 则抛出错误

// ✅ 安全的方式
String? nullableName;
print(nullableName ?? '默认值');
```

### 2. Widget 构建错误

```dart
// setState after dispose
void updateData() {
  if (!mounted) return; // ✅ 检查 mounted
  setState(() {
    // 更新状态
  });
}
```

### 3. 异步间隙

```dart
// ❌ 不安全
Future<void> loadData() async {
  final data = await fetchData();
  setState(() {}); // 此时 widget 可能已销毁
}

// ✅ 安全
Future<void> loadData() async {
  final data = await fetchData();
  if (!mounted) return;
  setState(() {});
}
```

## 错误上报

### 简单日志记录

```dart
class Logger {
  static void error(String message, {Object? error, StackTrace? stackTrace}) {
    // 开发环境打印
    debugPrint('[ERROR] $message');
    if (error != null) debugPrint('Error: $error');
    if (stackTrace != null) debugPrint('Stack: $stackTrace');

    // 生产环境上报
    if (kReleaseMode) {
      // 上报到 Firebase Crashlytics / Sentry 等
    }
  }
}
```

---

*最后更新: 2026-02-19*

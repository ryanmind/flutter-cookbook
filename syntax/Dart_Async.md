# Dart 异步编程 - 初学者指南

## 目录
1. [同步 vs 异步](#同步-vs-异步)
2. [Future 详解](#future-详解)
3. [async/await](#asyncawait)
4. [Stream 详解](#stream-详解)
5. [Isolate 并发](#isolate-并发)
6. [常见错误与解决方案](#常见错误与解决方案)

## 同步 vs 异步

### 同步执行

```dart
// 同步：代码按顺序执行，阻塞等待
void main() {
  print('开始');
  String result = fetchDataSync();
  print(result);
  print('结束');
}

String fetchDataSync() {
  // 模拟耗时操作
  sleep(Duration(seconds: 1));  // 阻塞整个程序
  return '数据';
}

// 输出：
// 开始
// （等待 1 秒）
// 数据
// 结束
```

### 异步执行

```dart
// 异步：不阻塞，继续执行后续代码
void main() {
  print('开始');
  fetchDataAsync().then((result) {
    print(result);
  });
  print('结束');
}

Future<String> fetchDataAsync() {
  return Future.delayed(Duration(seconds: 1), () => '数据');
}

// 输出：
// 开始
// 结束
// （1 秒后）
// 数据
```

## Future 详解

### 创建 Future

```dart
// 1. Future.value - 立即完成的 Future
Future<int> immediateValue = Future.value(42);

// 2. Future.delayed - 延迟完成
Future<String> delayed = Future.delayed(
  Duration(seconds: 2),
  () => '延迟结果',
);

// 3. Future.error - 错误 Future
Future<int> errorFuture = Future.error(Exception('出错了'));

// 4. 使用 async 函数
Future<String> fetchData() async {
  await Future.delayed(Duration(seconds: 1));
  return '数据';
}

// 5. 使用 Completer（更灵活的控制）
import 'dart:async';

Future<String> withCompleter() {
  final completer = Completer<String>();
  
  // 在某个时机完成任务
  Future.delayed(Duration(seconds: 1), () {
    completer.complete('完成');
    // 或错误：completer.completeError(Exception('失败'));
  });
  
  return completer.future;
}
```

### 处理 Future 结果

```dart
void main() {
  // 1. then/catchError/whenComplete
  fetchData()
    .then((result) {
      print('成功：$result');
      return result.length;  // 可以链式传递
    })
    .then((length) {
      print('长度：$length');
    })
    .catchError((error) {
      print('错误：$error');
    })
    .whenComplete(() {
      print('完成');
    });

  // 2. 回调地狱（不推荐）
  fetchUser()
    .then((user) {
      fetchOrders(user.id)
        .then((orders) {
          processOrders(orders)
            .then((result) {
              print(result);
            });
        });
    });

  // 3. 链式调用（推荐）
  fetchUser()
    .then((user) => fetchOrders(user.id))
    .then((orders) => processOrders(orders))
    .then((result) => print(result))
    .catchError((error) => print('错误：$error'));
}
```

### Future 静态方法

```dart
void main() async {
  // Future.wait - 等待多个 Future 完成
  var results = await Future.wait([
    fetchFromServer1(),
    fetchFromServer2(),
    fetchFromServer3(),
  ]);
  print(results);  // [result1, result2, result3]

  // Future.any - 任一完成即返回
  var fastest = await Future.any([
    fetchFromServer1(),
    fetchFromServer2(),
  ]);
  print('最快的结果：$fastest');

  // Future.forEach - 依次执行
  var items = [1, 2, 3];
  await Future.forEach(items, (item) async {
    await Future.delayed(Duration(milliseconds: 100));
    print('处理：$item');
  });

  // Future.doWhile - 条件循环
  var count = 0;
  await Future.doWhile(() async {
    count++;
    print('第 $count 次');
    await Future.delayed(Duration(milliseconds: 100));
    return count < 3;  // 返回 true 继续，false 停止
  });
}
```

### 超时处理

```dart
void main() async {
  try {
    var result = await fetchData()
      .timeout(
        Duration(seconds: 5),
        onTimeout: () {
          throw TimeoutException('请求超时');
        },
      );
    print(result);
  } catch (e) {
    print('错误：$e');
  }
}
```

## async/await

### 基本用法

```dart
// async 函数返回 Future
Future<String> fetchUserName() async {
  // await 等待 Future 完成
  await Future.delayed(Duration(seconds: 1));
  return '张三';
}

// 使用
void main() async {
  print('开始');
  String name = await fetchUserName();  // 等待结果
  print(name);
  print('结束');
}

// 对比：不使用 async/await
void main() {
  print('开始');
  fetchUserName().then((name) {
    print(name);
    print('结束');
  });
}
```

### 错误处理

```dart
Future<String> fetchData() async {
  await Future.delayed(Duration(seconds: 1));
  throw Exception('网络错误');
}

void main() async {
  // 1. try-catch
  try {
    var result = await fetchData();
    print(result);
  } on Exception catch (e) {
    print('捕获异常：$e');
  } catch (e) {
    print('未知错误：$e');
  } finally {
    print('清理资源');
  }

  // 2. catchError（不推荐与 async/await 混用）
  fetchData().catchError((error) {
    print('错误：$error');
  });
}
```

### 并行执行

```dart
void main() async {
  // 顺序执行（总耗时 = 各任务耗时之和）
  var user = await fetchUser();      // 1 秒
  var orders = await fetchOrders();  // 1 秒
  var settings = await fetchSettings(); // 1 秒
  // 总耗时：3 秒

  // 并行执行（总耗时 = 最慢的任务耗时）
  var results = await Future.wait([
    fetchUser(),
    fetchOrders(),
    fetchSettings(),
  ]);
  // 总耗时：1 秒
  var user = results[0];
  var orders = results[1];
  var settings = results[2];

  // 更清晰的写法
  late User user;
  late List<Order> orders;
  late Settings settings;
  
  await Future.wait([
    fetchUser().then((u) => user = u),
    fetchOrders().then((o) => orders = o),
    fetchSettings().then((s) => settings = s),
  ]);
}
```

### async 构造函数

```dart
// ❌ 构造函数不能是 async
// class MyClass {
//   MyClass() async { }  // 错误
// }

// ✅ 方案 1：静态工厂方法
class DataService {
  final String data;
  
  DataService._(this.data);
  
  static Future<DataService> create() async {
    final data = await fetchData();
    return DataService._(data);
  }
}

// 使用
void main() async {
  var service = await DataService.create();
}

// ✅ 方案 2：初始化方法
class DataService {
  String? data;
  
  Future<void> init() async {
    data = await fetchData();
  }
}
```

## Stream 详解

### 什么是 Stream

Stream 是异步事件序列，类似于"流水线"，数据源源不断地流出。

```dart
// Future：一次性结果
Future<int> getResult();  // 返回一个值

// Stream：多个值序列
Stream<int> getNumbers();  // 返回多个值
```

### 创建 Stream

```dart
// 1. Stream.fromIterable - 从列表创建
var stream = Stream.fromIterable([1, 2, 3, 4, 5]);

// 2. Stream.periodic - 周期性生成
var timerStream = Stream.periodic(
  Duration(seconds: 1),
  (count) => count,
).take(5);  // 取前 5 个

// 3. Stream.fromFuture - 从 Future 创建
var futureStream = Stream.fromFuture(fetchData());

// 4. 使用 async* 生成器
Stream<int> countStream(int max) async* {
  for (int i = 1; i <= max; i++) {
    await Future.delayed(Duration(seconds: 1));
    yield i;  // 产出值
  }
}

// 5. 使用 StreamController
import 'dart:async';

StreamController<int> controller = StreamController<int>();

void addToStream(int value) {
  controller.add(value);
}

Stream<int> get stream => controller.stream;
```

### 监听 Stream

```dart
void main() async {
  var stream = Stream.fromIterable([1, 2, 3, 4, 5]);
  
  // 1. listen 方法
  var subscription = stream.listen(
    (data) {
      print('数据：$data');
    },
    onError: (error) {
      print('错误：$error');
    },
    onDone: () {
      print('完成');
    },
    cancelOnError: false,  // 出错后是否继续
  );
  
  // 暂停/恢复/取消
  subscription.pause();
  subscription.resume();
  subscription.cancel();

  // 2. await for（必须等待完成）
  await for (var data in stream) {
    print('数据：$data');
  }
}
```

### Stream 转换

```dart
void main() async {
  var stream = Stream.fromIterable([1, 2, 3, 4, 5]);
  
  // map - 转换每个元素
  var doubled = stream.map((n) => n * 2);
  // 2, 4, 6, 8, 10

  // where - 过滤
  var evens = stream.where((n) => n % 2 == 0);
  // 2, 4

  // take - 取前 N 个
  var first3 = stream.take(3);
  // 1, 2, 3

  // skip - 跳过前 N 个
  var skip2 = stream.skip(2);
  // 3, 4, 5

  // distinct - 去重
  var unique = Stream.fromIterable([1, 1, 2, 2, 3]).distinct();
  // 1, 2, 3

  // expand - 展开
  var expanded = stream.expand((n) => [n, n]);
  // 1, 1, 2, 2, 3, 3, 4, 4, 5, 5

  // asyncMap - 异步转换
  var asyncMapped = stream.asyncMap((n) async {
    await Future.delayed(Duration(milliseconds: 100));
    return n * 2;
  });
}
```

### Stream 组合

```dart
void main() async {
  var stream1 = Stream.fromIterable([1, 2, 3]);
  var stream2 = Stream.fromIterable([4, 5, 6]);
  
  // zip - 配对组合
  var zipped = StreamZip([stream1, stream2]);
  await for (var pair in zipped) {
    print(pair);  // [1, 4], [2, 5], [3, 6]
  }

  // merge - 合并（谁先到谁先输出）
  var merged = StreamGroup.merge([stream1, stream2]);
  
  // concat - 连接（stream1 完成后再 stream2）
  var concatenated = stream1.asyncExpand((_) => stream2);
}
```

### 广播 Stream

```dart
void main() {
  // 普通 Stream：只能被监听一次
  var singleStream = Stream.fromIterable([1, 2, 3]);
  singleStream.listen(print);
  // singleStream.listen(print);  // 错误！

  // 广播 Stream：可被多次监听
  var broadcastStream = Stream.fromIterable([1, 2, 3]).asBroadcastStream();
  broadcastStream.listen((n) => print('监听器1: $n'));
  broadcastStream.listen((n) => print('监听器2: $n'));

  // 使用 StreamController 创建广播 Stream
  var controller = StreamController<int>.broadcast();
  controller.stream.listen((n) => print('A: $n'));
  controller.stream.listen((n) => print('B: $n'));
  controller.add(1);  // A: 1, B: 1
}
```

### 实际应用示例

```dart
// 搜索防抖
Stream<String> searchStream(String query) async* {
  if (query.isEmpty) return;
  await Future.delayed(Duration(milliseconds: 300));
  yield* performSearch(query);
}

// 进度更新
class FileUploader {
  final _progressController = StreamController<double>.broadcast();
  Stream<double> get progress => _progressController.stream;
  
  Future<void> upload(File file) async {
    for (var i = 0; i <= 100; i += 10) {
      await Future.delayed(Duration(milliseconds: 100));
      _progressController.add(i / 100);
    }
    _progressController.close();
  }
}

// 使用
void main() async {
  final uploader = FileUploader();
  uploader.progress.listen((p) => print('进度: ${(p * 100).toInt()}%'));
  await uploader.upload(File('test.txt'));
}
```

## Isolate 并发

### 为什么需要 Isolate

Dart 是单线程模型，所有代码运行在主 Isolate 中。对于 CPU 密集型任务，会阻塞 UI。

```dart
// ❌ 错误：阻塞主线程
void main() {
  print('开始');
  // 耗时计算阻塞整个程序
  var result = heavyComputation(10000000);
  print('结果：$result');
  print('结束');
}

int heavyComputation(int n) {
  var sum = 0;
  for (var i = 0; i < n; i++) {
    sum += i;
  }
  return sum;
}
```

### 使用 Isolate

```dart
import 'dart:isolate';

// 计算函数（在独立 Isolate 中运行）
int heavyComputation(int n) {
  var sum = 0;
  for (var i = 0; i < n; i++) {
    sum += i;
  }
  return sum;
}

void main() async {
  print('开始');
  
  // 使用 compute（简化版 Isolate）
  // Flutter 提供的 compute 函数
  // var result = await compute(heavyComputation, 10000000);
  
  // 使用 Isolate.run（Dart 2.19+）
  var result = await Isolate.run(() => heavyComputation(10000000));
  
  print('结果：$result');
  print('结束');
}
```

### Isolate 通信

```dart
import 'dart:isolate';

void main() async {
  // 创建 ReceivePort 接收消息
  final receivePort = ReceivePort();
  
  // 启动 Isolate
  await Isolate.spawn(
    _isolateEntry,
    receivePort.sendPort,
  );
  
  // 监听消息
  receivePort.listen((message) {
    print('主 Isolate 收到：$message');
    if (message == '完成') {
      receivePort.close();
    }
  });
}

// Isolate 入口函数
void _isolateEntry(SendPort sendPort) {
  sendPort.send('你好，主 Isolate！');
  
  // 执行耗时任务
  var result = heavyComputation(1000000);
  sendPort.send('计算结果：$result');
  
  sendPort.send('完成');
}
```

### Flutter 中的 compute

```dart
import 'package:flutter/foundation.dart';

// JSON 解析是常见场景
List<User> parseUsers(String json) {
  // 耗时的 JSON 解析
  final List<dynamic> data = jsonDecode(json);
  return data.map((e) => User.fromJson(e)).toList();
}

// 在 Widget 中使用
class UserListWidget extends StatefulWidget {
  final String jsonData;
  
  @override
  _UserListWidgetState createState() => _UserListWidgetState();
}

class _UserListWidgetState extends State<UserListWidget> {
  late Future<List<User>> users;
  
  @override
  void initState() {
    super.initState();
    // compute 会在后台 Isolate 解析 JSON
    users = compute(parseUsers, widget.jsonData);
  }
  
  @override
  Widget build(BuildContext context) {
    return FutureBuilder<List<User>>(
      future: users,
      builder: (context, snapshot) {
        if (snapshot.hasData) {
          return ListView.builder(
            itemCount: snapshot.data!.length,
            itemBuilder: (context, index) {
              return ListTile(
                title: Text(snapshot.data![index].name),
              );
            },
          );
        }
        return CircularProgressIndicator();
      },
    );
  }
}
```

## 常见错误与解决方案

### 错误 1：忘记 await

```dart
// ❌ 错误：忘记 await，得到 Future 而不是结果
Future<String> fetch() async {
  var result = fetchData();  // result 是 Future<String>
  print(result);  // Instance of 'Future<String>'
}

// ✅ 正确
Future<String> fetch() async {
  var result = await fetchData();  // result 是 String
  print(result);
}
```

### 错误 2：在同步函数中使用 await

```dart
// ❌ 错误：非 async 函数不能使用 await
void fetch() {
  // var result = await fetchData();  // 编译错误
}

// ✅ 正确：添加 async
Future<void> fetch() async {
  var result = await fetchData();
}
```

### 错误 3：未处理的 Future 错误

```dart
// ❌ 错误：错误未被捕获
void main() {
  fetchData();  // 如果出错，错误会被忽略
}

// ✅ 正确：处理错误
void main() async {
  try {
    await fetchData();
  } catch (e) {
    print('错误：$e');
  }
}

// 或者
void main() {
  fetchData().catchError((e) {
    print('错误：$e');
  });
}
```

### 错误 4：Stream 未取消订阅

```dart
// ❌ 错误：忘记取消订阅，可能导致内存泄漏
class MyWidget extends StatefulWidget {
  @override
  _MyWidgetState createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> {
  @override
  void initState() {
    super.initState();
    stream.listen((data) {
      setState(() {});
    });  // 没有保存 subscription
  }
}

// ✅ 正确：保存并取消订阅
class _MyWidgetState extends State<MyWidget> {
  StreamSubscription? _subscription;
  
  @override
  void initState() {
    super.initState();
    _subscription = stream.listen((data) {
      setState(() {});
    });
  }
  
  @override
  void dispose() {
    _subscription?.cancel();
    super.dispose();
  }
}
```

### 错误 5：并行任务等待顺序错误

```dart
// ❌ 错误：实际上顺序执行
void main() async {
  // 这两个不是并行执行
  var future1 = fetchUser();      // 开始执行
  var future2 = fetchOrders();    // 开始执行
  
  var user = await future1;       // 等待完成
  var orders = await future2;     // 等待完成
  
  // 上面是正确的并行方式，但下面是错误的：
  var user2 = await fetchUser();     // 等待完成
  var orders2 = await fetchOrders(); // 然后开始执行
  // 这是顺序执行！
}

// ✅ 正确：使用 Future.wait
void main() async {
  var results = await Future.wait([
    fetchUser(),
    fetchOrders(),
  ]);
}
```

## 实战练习

### 练习 1：网络请求封装

```dart
class ApiClient {
  final String baseUrl;
  final Duration timeout;
  
  ApiClient({
    required this.baseUrl,
    this.timeout = const Duration(seconds: 30),
  });
  
  Future<T> get<T>(
    String path, {
    required T Function(Map<String, dynamic>) fromJson,
    Map<String, String>? headers,
  }) async {
    try {
      final response = await http
          .get(Uri.parse('$baseUrl$path'), headers: headers)
          .timeout(timeout);
      
      if (response.statusCode == 200) {
        final data = jsonDecode(response.body);
        return fromJson(data);
      } else {
        throw ApiException(response.statusCode, response.body);
      }
    } on TimeoutException {
      throw ApiException(-1, '请求超时');
    }
  }
  
  Future<List<T>> getList<T>(
    String path, {
    required T Function(Map<String, dynamic>) fromJson,
  }) async {
    try {
      final response = await http
          .get(Uri.parse('$baseUrl$path'))
          .timeout(timeout);
      
      if (response.statusCode == 200) {
        final List<dynamic> data = jsonDecode(response.body);
        return data.map((e) => fromJson(e)).toList();
      } else {
        throw ApiException(response.statusCode, response.body);
      }
    } catch (e) {
      rethrow;
    }
  }
}

class ApiException implements Exception {
  final int statusCode;
  final String message;
  
  ApiException(this.statusCode, this.message);
  
  @override
  String toString() => 'ApiException: $statusCode - $message';
}
```

### 练习 2：搜索防抖与取消

```dart
class SearchManager {
  final Duration debounceTime;
  Timer? _debounceTimer;
  StreamSubscription? _searchSubscription;
  final _resultController = StreamController<List<String>>.broadcast();
  
  Stream<List<String>> get results => _resultController.stream;
  
  SearchManager({this.debounceTime = const Duration(milliseconds: 300)});
  
  void search(String query) {
    // 取消之前的防抖计时器
    _debounceTimer?.cancel();
    
    // 取消之前的搜索请求
    _searchSubscription?.cancel();
    
    if (query.isEmpty) {
      _resultController.add([]);
      return;
    }
    
    // 设置新的防抖计时器
    _debounceTimer = Timer(debounceTime, () {
      _performSearch(query);
    });
  }
  
  void _performSearch(String query) async {
    try {
      // 模拟网络请求
      final results = await Future.delayed(
        Duration(milliseconds: 500),
        () => List.generate(10, (i) => '$query 结果 ${i + 1}'),
      );
      
      _resultController.add(results);
    } catch (e) {
      _resultController.addError(e);
    }
  }
  
  void dispose() {
    _debounceTimer?.cancel();
    _searchSubscription?.cancel();
    _resultController.close();
  }
}
```

---

*最后更新: 2026-02-19*

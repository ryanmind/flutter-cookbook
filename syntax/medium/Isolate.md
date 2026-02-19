# Dart Isolate 并发编程

## 概述

Isolate 是 Dart 的并发模型，每个 Isolate 拥有独立的内存和事件循环，通过消息传递通信。适合 CPU 密集型任务。

## 为什么需要 Isolate

### Dart 单线程模型

```dart
// Dart 默认在主 Isolate 运行所有代码
void main() {
  // 主 Isolate 执行
  print('开始');

  // 这个计算会阻塞 UI
  int result = heavyComputation(10000000);

  print('结果: $result');
  print('结束');
}

int heavyComputation(int n) {
  int sum = 0;
  for (int i = 0; i < n; i++) {
    sum += i;
  }
  return sum;
}
```

### 问题：UI 卡顿

```dart
// ❌ 在主线程执行耗时操作
void loadData() {
  setState(() => isLoading = true);

  // 阻塞 UI 线程，导致卡顿
  final result = heavyComputation(10000000);

  setState(() {
    isLoading = false;
    data = result;
  });
}
```

## Isolate 基础

### 创建 Isolate

```dart
import 'dart:isolate';

Future<void> main() async {
  print('主 Isolate 开始');

  // 创建新的 Isolate
  final receivePort = ReceivePort();

  await Isolate.spawn(
    _isolateEntry,        // 入口函数
    receivePort.sendPort, // 传递的参数
  );

  // 接收消息
  receivePort.listen((message) {
    print('收到消息: $message');
    receivePort.close(); // 关闭端口
  });

  print('主 Isolate 继续执行');
}

// Isolate 入口函数（必须是顶级函数或静态方法）
void _isolateEntry(SendPort sendPort) {
  print('新 Isolate 运行中');
  sendPort.send('来自新 Isolate 的消息');
}
```

### 双向通信

```dart
Future<void> communicateWithIsolate() async {
  final receivePort = ReceivePort();

  // 启动 Isolate
  final isolate = await Isolate.spawn(
    _worker,
    receivePort.sendPort,
  );

  // 接收来自 Isolate 的 SendPort
  final sendPort = await receivePort.first as SendPort;

  // 创建新的 ReceivePort 用于接收响应
  final responsePort = ReceivePort();
  sendPort.send(['计算', 10000000, responsePort.sendPort]);

  // 接收结果
  final result = await responsePort.first;
  print('结果: $result');

  // 清理
  responsePort.close();
  isolate.kill(priority: Isolate.immediate);
}

void _worker(SendPort mainSendPort) {
  final receivePort = ReceivePort();
  mainSendPort.send(receivePort.sendPort);

  receivePort.listen((message) {
    final [task, data, replyPort] = message as List;
    if (task == '计算') {
      final result = heavyComputation(data as int);
      (replyPort as SendPort).send(result);
    }
  });
}
```

## compute 函数

### 简化 Isolate 使用

```dart
import 'package:flutter/foundation.dart';

// 使用 compute 函数（Flutter 封装）
Future<void> loadDataWithCompute() async {
  setState(() => isLoading = true);

  // compute 自动创建 Isolate 并返回结果
  final result = await compute(heavyComputation, 10000000);

  setState(() {
    isLoading = false;
    data = result;
  });
}

// compute 的函数必须是顶级函数或静态方法
int heavyComputation(int n) {
  int sum = 0;
  for (int i = 0; i < n; i++) {
    sum += i;
  }
  return sum;
}
```

### compute 示例

```dart
// JSON 解析
Future<List<User>> fetchUsers() async {
  final response = await http.get(Uri.parse('/api/users'));
  // 在后台 Isolate 解析 JSON
  return compute(_parseUsers, response.body);
}

List<User> _parseUsers(String jsonString) {
  final List<dynamic> json = jsonDecode(jsonString);
  return json.map((e) => User.fromJson(e)).toList();
}
```

```dart
// 图片处理
Future<Uint8List> processImage(Uint8List imageBytes) async {
  return compute(_resizeImage, imageBytes);
}

Uint8List _resizeImage(Uint8List bytes) {
  final image = img.decodeImage(bytes)!;
  final resized = img.copyResize(image, width: 800);
  return Uint8List.fromList(img.encodeJpg(resized));
}
```

## Isolate 线程池

### 使用 Isolate.run

```dart
// Dart 3.0+ 简化 API
Future<void> isolateRun() async {
  final result = await Isolate.run(() {
    // 这个闭包在新 Isolate 中执行
    return heavyComputation(10000000);
  });

  print('结果: $result');
}
```

### 并发多个任务

```dart
Future<void> parallelComputations() async {
  final futures = List.generate(4, (i) {
    return Isolate.run(() => heavyComputation(1000000 * (i + 1)));
  });

  final results = await Future.wait(futures);
  print('所有结果: $results');
}
```

## 完整示例

### 后台计算服务

```dart
class IsolateService {
  Isolate? _isolate;
  SendPort? _sendPort;
  final ReceivePort _receivePort = ReceivePort();
  int _taskId = 0;
  final Map<int, Completer<dynamic>> _completers = {};

  Future<void> start() async {
    _isolate = await Isolate.spawn(
      _isolateWorker,
      _receivePort.sendPort,
    );

    _receivePort.listen((message) {
      final [id, result] = message as List;
      _completers[id]?.complete(result);
      _completers.remove(id);
    });
  }

  Future<T> compute<T>(Future<T> Function() task) async {
    if (_sendPort == null) {
      throw StateError('IsolateService 未启动');
    }

    final id = _taskId++;
    final completer = Completer<T>();
    _completers[id] = completer;

    _sendPort!.send([id, task]);

    return completer.future;
  }

  void stop() {
    _receivePort.close();
    _isolate?.kill(priority: Isolate.immediate);
    _isolate = null;
    _sendPort = null;
  }
}

void _isolateWorker(SendPort mainPort) {
  final receivePort = ReceivePort();
  mainPort.send(receivePort.sendPort);

  receivePort.listen((message) async {
    final [id, task] = message as List;
    final result = await (task as Future<dynamic> Function())();
    mainPort.send([id, result]);
  });
}
```

### 在 Flutter 中使用

```dart
class DataPage extends StatefulWidget {
  const DataPage({super.key});

  @override
  State<DataPage> createState() => _DataPageState();
}

class _DataPageState extends State<DataPage> {
  final IsolateService _isolateService = IsolateService();
  int? _result;
  bool _isLoading = false;

  @override
  void initState() {
    super.initState();
    _isolateService.start();
  }

  @override
  void dispose() {
    _isolateService.stop();
    super.dispose();
  }

  Future<void> _calculate() async {
    setState(() => _isLoading = true);

    try {
      final result = await _isolateService.compute(() {
        int sum = 0;
        for (int i = 0; i < 10000000; i++) {
          sum += i;
        }
        return sum;
      });

      setState(() => _result = result);
    } finally {
      setState(() => _isLoading = false);
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Isolate 示例')),
      body: Center(
        child: _isLoading
            ? const CircularProgressIndicator()
            : Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Text('结果: $_result'),
                  const SizedBox(height: 16),
                  ElevatedButton(
                    onPressed: _calculate,
                    child: const Text('开始计算'),
                  ),
                ],
              ),
      ),
    );
  }
}
```

## 限制与注意事项

### Isolate 的限制

```dart
// ❌ 不能在 Isolate 中访问主线程对象
void _isolateTask(SendPort port) {
  // 不能访问：
  // - BuildContext
  // - GlobalKey
  // - TextEditingController
  // - 任何 UI 相关对象
}

// ✅ 只能传递可序列化的数据
void _isolateTask(SendPort port) {
  // 可以传递：
  // - 基本类型（int, double, String, bool）
  // - List, Map
  // - SendPort
}
```

### 内存隔离

```dart
// 每个 Isolate 有独立内存
void main() async {
  int counter = 0;

  final receivePort = ReceivePort();
  await Isolate.spawn((sendPort) {
    // ❌ 不能访问主 Isolate 的变量
    // counter++; // 错误！

    sendPort.send('来自 Isolate');
  }, receivePort.sendPort);
}
```

## 最佳实践

### 1. 使用 compute 处理简单任务

```dart
// ✅ 简单任务用 compute
final users = await compute(parseUsers, jsonStr);

// ✅ 复杂任务用 Isolate 线程池
final results = await Future.wait([
  compute(task1, data1),
  compute(task2, data2),
]);
```

### 2. 任务函数设计

```dart
// ✅ 纯函数，无副作用
int calculate(List<int> data) {
  return data.reduce((a, b) => a + b);
}

// ❌ 避免依赖外部状态
int calculate(List<int> data) {
  // 不要访问全局变量
  return data.reduce((a, b) => a + b) + globalMultiplier; // ❌
}
```

### 3. 资源清理

```dart
class MyService {
  Isolate? _isolate;
  ReceivePort? _receivePort;

  Future<void> start() async {
    _receivePort = ReceivePort();
    _isolate = await Isolate.spawn(_worker, _receivePort!.sendPort);
  }

  void dispose() {
    _receivePort?.close();
    _isolate?.kill(priority: Isolate.immediate);
    _isolate = null;
    _receivePort = null;
  }
}
```

### 4. 错误处理

```dart
Future<T> runInIsolate<T>(T Function() task) async {
  try {
    return await Isolate.run(task);
  } on IsolateSpawnException {
    throw '无法创建 Isolate';
  } catch (e) {
    throw '任务执行失败: $e';
  }
}
```

## 何时使用 Isolate

| 场景 | 推荐 |
|------|------|
| JSON 解析（大数据） | ✅ compute |
| 图片处理 | ✅ Isolate |
| 文件加解密 | ✅ Isolate |
| 数据库操作 | ✅ Isolate |
| 简单计算 | ❌ 主线程 |
| 网络请求 | ❌ 用 async/await |

---

*最后更新: 2026-02-19*

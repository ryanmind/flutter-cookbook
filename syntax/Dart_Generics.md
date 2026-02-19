# Dart 泛型编程 - 初学者指南

## 目录
1. [泛型基础概念](#泛型基础概念)
2. [泛型类](#泛型类)
3. [泛型方法](#泛型方法)
4. [泛型约束](#泛型约束)
5. [泛型与集合](#泛型与集合)
6. [协变与逆变](#协变与逆变)
7. [实战应用](#实战应用)

## 泛型基础概念

### 什么是泛型

泛型允许我们在定义类、方法时不指定具体类型，而是在使用时再确定类型。

```dart
// 没有泛型的问题
class IntCache {
  int? _value;
  
  void set(int value) => _value = value;
  int? get() => _value;
}

class StringCache {
  String? _value;
  
  void set(String value) => _value = value;
  String? get() => _value;
}

// 需要为每种类型写一个类！

// 使用泛型解决
class Cache<T> {
  T? _value;
  
  void set(T value) => _value = value;
  T? get() => _value;
}
```

### 泛型的优势

```dart
// 1. 类型安全
var names = <String>['Alice', 'Bob'];
// names.add(123);  // ❌ 编译错误

// 2. 代码复用
Cache<int> intCache = Cache();
Cache<String> strCache = Cache();

// 3. 消除类型转换
List<String> names = ['Alice', 'Bob'];
String first = names[0];  // 无需强制转换
```

## 泛型类

### 基本泛型类

```dart
// 单个类型参数
class Box<T> {
  final T value;
  
  Box(this.value);
  
  T getValue() => value;
  
  void doSomething(void Function(T) action) {
    action(value);
  }
}

// 使用
var intBox = Box<int>(42);
print(intBox.getValue());  // 42

var strBox = Box<String>('Hello');
print(strBox.getValue());  // Hello

// 类型推断
var inferred = Box(3.14);  // Box<double>
```

### 多个类型参数

```dart
class Pair<K, V> {
  final K key;
  final V value;
  
  Pair(this.key, this.value);
  
  Pair<V, K> swap() => Pair(value, key);
  
  @override
  String toString() => 'Pair($key: $value)';
}

// 使用
var pair = Pair<String, int>('age', 25);
print(pair);  // Pair(age: 25)

var swapped = pair.swap();
print(swapped);  // Pair(25: age)
```

### 泛型容器类

```dart
class Result<T> {
  final T? data;
  final String? error;
  final bool isSuccess;
  
  Result.success(this.data)
      : error = null,
        isSuccess = true;
  
  Result.failure(this.error)
      : data = null,
        isSuccess = false;
  
  // 泛型方法
  Result<R> map<R>(R Function(T) transform) {
    if (isSuccess && data != null) {
      return Result.success(transform(data as T));
    }
    return Result.failure(error);
  }
  
  // 链式调用
  Result<R> flatMap<R>(Result<R> Function(T) transform) {
    if (isSuccess && data != null) {
      return transform(data as T);
    }
    return Result.failure(error);
  }
}

// 使用
Result<int> result = Result.success(42);
Result<String> mapped = result.map((n) => 'Value: $n');
print(mapped.data);  // Value: 42
```

## 泛型方法

### 基本泛型方法

```dart
// 泛型方法
T identity<T>(T value) {
  return value;
}

// 使用
var a = identity<int>(42);
var b = identity<String>('hello');
var c = identity(3.14);  // 类型推断

// 泛型方法在类中
class Utils {
  static T? firstOrNull<T>(List<T> list) {
    return list.isEmpty ? null : list.first;
  }
  
  static List<T> filter<T>(List<T> list, bool Function(T) test) {
    return list.where(test).toList();
  }
}
```

### 多类型参数方法

```dart
// 创建Map 的方法
Map<K, V> createMap<K, V>(List<K> keys, List<V> values) {
  final map = <K, V>{};
  for (var i = 0; i < keys.length && i < values.length; i++) {
    map[keys[i]] = values[i];
  }
  return map;
}

// 使用
var map = createMap(['a', 'b'], [1, 2]);
print(map);  // {a: 1, b: 2}
```

### 泛型工厂方法

```dart
class JsonParser {
  static T fromJson<T>(Map<String, dynamic> json, T Function(Map<String, dynamic>) fromJson) {
    return fromJson(json);
  }
  
  static List<T> fromJsonList<T>(List<dynamic> jsonList, T Function(Map<String, dynamic>) fromJson) {
    return jsonList
        .cast<Map<String, dynamic>>()
        .map(fromJson)
        .toList();
  }
}

// 使用
final user = JsonParser.fromJson<User>(
  {'name': 'Alice', 'age': 25},
  (json) => User.fromJson(json),
);
```

## 泛型约束

### extends 约束

```dart
// 约束 T 必须是num 或其子类
class NumberContainer<T extends num> {
  final T value;
  
  NumberContainer(this.value);
  
  double get doubleValue => value.toDouble();
  
  T add(T other) => (value + other) as T;
}

// 使用
var intContainer = NumberContainer(42);
var doubleContainer = NumberContainer(3.14);
// var stringContainer = NumberContainer('hello');  // ❌ 编译错误

// 约束为特定类的子类
class Animal {
  void speak() => print('...');
}

class Dog extends Animal {
  @override
  void speak() => print('汪汪');
}

class AnimalShelter<T extends Animal> {
  final List<T> animals = [];
  
  void add(T animal) => animals.add(animal);
  
  void allSpeak() {
    for (var animal in animals) {
      animal.speak();  // 可以调用 Animal 的方法
    }
  }
}
```

### 多重约束

```dart
// 使用接口实现多重约束
abstract class Serializable {
  Map<String, dynamic> toJson();
}

abstract class Identifiable {
  String get id;
}

// 泛型约束：T 必须实现两个接口
class Repository<T extends Serializable & Identifiable> {
  final List<T> items = [];
  
  void save(T item) {
    items.add(item);
    print('Saved: ${item.toJson()}');
  }
  
  T? findById(String id) {
    try {
      return items.firstWhere((item) => item.id == id);
    } catch (_) {
      return null;
    }
  }
}

// 实现
class User implements Serializable, Identifiable {
  @override
  final String id;
  final String name;
  
  User({required this.id, required this.name});
  
  @override
  Map<String, dynamic> toJson() => {'id': id, 'name': name};
}
```

## 泛型与集合

### 类型安全的集合操作

```dart
// 泛型扩展方法
extension ListExtension<T> on List<T> {
  // 安全获取
  T? getOrNull(int index) {
    if (index < 0 || index >= length) return null;
    return this[index];
  }
  
  // 分组
  Map<K, List<T>> groupBy<K>(K Function(T) keySelector) {
    final map = <K, List<T>>{};
    for (var element in this) {
      final key = keySelector(element);
      map.putIfAbsent(key, () => []).add(element);
    }
    return map;
  }
  
  // 去重
  List<T> distinctBy<K>(K Function(T) selector) {
    final seen = <K>{};
    return where((item) {
      final key = selector(item);
      if (seen.contains(key)) return false;
      seen.add(key);
      return true;
    }).toList();
  }
  
  // 转换为Map
  Map<K, T> toMap<K>(K Function(T) keySelector) {
    return {for (var item in this) keySelector(item): item};
  }
}

// 使用
var users = [
  User(id: '1', name: 'Alice'),
  User(id: '2', name: 'Bob'),
  User(id: '1', name: 'Alice Copy'),
];

var grouped = users.groupBy((u) => u.name.length);
var distinct = users.distinctBy((u) => u.id);
var userMap = users.toMap((u) => u.id);
```

### 泛型集合类

```dart
// 泛型栈
class Stack<T> {
  final List<T> _items = [];
  
  bool get isEmpty => _items.isEmpty;
  int get length => _items.length;
  
  void push(T item) => _items.add(item);
  
  T pop() {
    if (isEmpty) throw StateError('Stack is empty');
    return _items.removeLast();
  }
  
  T? peek() => _items.isEmpty ? null : _items.last;
}

// 泛型队列
class Queue<T> {
  final List<T> _items = [];
  
  bool get isEmpty => _items.isEmpty;
  int get length => _items.length;
  
  void enqueue(T item) => _items.add(item);
  
  T dequeue() {
    if (isEmpty) throw StateError('Queue is empty');
    return _items.removeAt(0);
  }
  
  T? peek() => _items.isEmpty ? null : _items.first;
}

// 使用
var stack = Stack<int>();
stack.push(1);
stack.push(2);
print(stack.pop());  // 2
```

## 协变与逆变

### 类型参数的协变

```dart
// 默认情况下，泛型类型是不变的
class Animal {}
class Dog extends Animal {}

void testCovariance() {
  // ❌ 错误：List<Dog> 不是 List<Animal> 的子类型
  // List<Dog> dogs = [];
  // List<Animal> animals = dogs;  // 编译错误
  
  // ✅ 正确
  List<Animal> animals = <Animal>[];
  animals.add(Dog());
}
```

### 使用 covariant

```dart
class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}

class AnimalHandler {
  // 使用 covariant 允许子类接受更具体的类型
  void handle(Animal animal) {
    print('Handling animal');
  }
}

class DogHandler extends AnimalHandler {
  @override
  void handle(covariant Dog dog) {  // 可以接受 Dog 类型
    print('Handling dog');
  }
}
```

### 类型检查与转换

```dart
void processList<T>(List<T> list) {
  // 运行时类型检查
  if (T == int) {
    print('处理整数列表');
    // 可以安全地进行 int 特定操作
  } else if (T == String) {
    print('处理字符串列表');
  }
  
  // 类型转换
  if (list is List<int>) {
    int sum = list.reduce((a, b) => a + b);
    print('Sum: $sum');
  }
}

// 运行时获取泛型类型
void printType<T>() {
  print(T.toString());
}

printType<int>();    // int
printType<String>(); // String
```

## 实战应用

### 泛型网络请求

```dart
class ApiResponse<T> {
  final T? data;
  final String? error;
  final int statusCode;
  
  ApiResponse({
    this.data,
    this.error,
    required this.statusCode,
  });
  
  bool get isSuccess => statusCode >= 200 && statusCode < 300;
  
  factory ApiResponse.success(T data) {
    return ApiResponse(data: data, statusCode: 200);
  }
  
  factory ApiResponse.failure(String error, {int statusCode = 400}) {
    return ApiResponse(error: error, statusCode: statusCode);
  }
}

class ApiClient {
  Future<ApiResponse<T>> get<T>(
    String path, {
    required T Function(Map<String, dynamic>) fromJson,
  }) async {
    try {
      // 模拟网络请求
      final response = await _fetch(path);
      final data = fromJson(response);
      return ApiResponse.success(data);
    } catch (e) {
      return ApiResponse.failure(e.toString());
    }
  }
  
  Future<ApiResponse<List<T>>> getList<T>(
    String path, {
    required T Function(Map<String, dynamic>) fromJson,
  }) async {
    try {
      final response = await _fetch(path);
      final list = (response as List)
          .map((e) => fromJson(e as Map<String, dynamic>))
          .toList();
      return ApiResponse.success(list);
    } catch (e) {
      return ApiResponse.failure(e.toString());
    }
  }
  
  Future<Map<String, dynamic>> _fetch(String path) async {
    // 模拟实现
    return {};
  }
}
```

### 泛型缓存

```dart
class CacheEntry<T> {
  final T value;
  final DateTime expiresAt;
  
  CacheEntry(this.value, Duration ttl)
      : expiresAt = DateTime.now().add(ttl);
  
  bool get isExpired => DateTime.now().isAfter(expiresAt);
}

class GenericCache<K, V> {
  final Map<K, CacheEntry<V>> _cache = {};
  final Duration defaultTtl;
  
  GenericCache({this.defaultTtl = const Duration(minutes: 5)});
  
  void put(K key, V value, [Duration? ttl]) {
    _cache[key] = CacheEntry(value, ttl ?? defaultTtl);
  }
  
  V? get(K key) {
    final entry = _cache[key];
    if (entry == null || entry.isExpired) {
      _cache.remove(key);
      return null;
    }
    return entry.value;
  }
  
  V? remove(K key) => _cache.remove(key)?.value;
  
  void clear() => _cache.clear();
  
  bool contains(K key) {
    final entry = _cache[key];
    if (entry == null) return false;
    if (entry.isExpired) {
      _cache.remove(key);
      return false;
    }
    return true;
  }
}
```

### 泛型状态管理

```dart
abstract class State<T> {
  const State();
}

class Initial<T> extends State<T> {
  const Initial();
}

class Loading<T> extends State<T> {
  const Loading();
}

class Success<T> extends State<T> {
  final T data;
  const Success(this.data);
}

class Error<T> extends State<T> {
  final String message;
  const Error(this.message);
}

// 使用
class StateBuilder<T> extends StatelessWidget {
  final State<T> state;
  final Widget Function(T) builder;
  final Widget? loading;
  final Widget Function(String)? error;
  
  const StateBuilder({
    required this.state,
    required this.builder,
    this.loading,
    this.error,
  });
  
  @override
  Widget build(BuildContext context) {
    return switch (state) {
      Initial<T>() => const SizedBox.shrink(),
      Loading<T>() => loading ?? const CircularProgressIndicator(),
      Success<T>(:final data) => builder(data),
      Error<T>(:final message) => 
          error?.call(message) ?? Text('Error: $message'),
    };
  }
}
```

---

*最后更新: 2026-02-19*

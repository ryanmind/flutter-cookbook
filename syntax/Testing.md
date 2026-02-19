# Flutter 测试 - 初学者指南

## 目录
1. [测试类型](#测试类型)
2. [单元测试](#单元测试)
3. [Widget 测试](#widget-测试)
4. [集成测试](#集成测试)
5. [Mock 和 Stub](#mock-和-stub)
6. [测试最佳实践](#测试最佳实践)

## 测试类型

### 三种测试类型

```dart
// 1. 单元测试 (Unit Test)
// 测试单个函数、方法或类
// 文件位置: test/unit_test.dart

// 2. Widget 测试 (Widget Test)
// 测试单个 Widget 的行为
// 文件位置: test/widget_test.dart

// 3. 集成测试 (Integration Test)
// 测试完整的应用流程
// 文件位置: integration_test/app_test.dart
```

### 测试依赖

```yaml
# pubspec.yaml
dev_dependencies:
  flutter_test:
    sdk: flutter
  integration_test:
    sdk: flutter
  mockito: ^5.4.0
  build_runner: ^2.4.0
```

## 单元测试

### 基本单元测试

```dart
// lib/calculator.dart
class Calculator {
  int add(int a, int b) => a + b;
  int subtract(int a, int b) => a - b;
  int multiply(int a, int b) => a * b;
  double divide(int a, int b) {
    if (b == 0) throw ArgumentError('Cannot divide by zero');
    return a / b;
  }
}

// test/calculator_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:my_app/calculator.dart';

void main() {
  late Calculator calculator;
  
  setUp(() {
    // 每个测试前执行
    calculator = Calculator();
  });
  
  tearDown(() {
    // 每个测试后执行
  });
  
  group('Calculator', () {
    test('add should return sum of two numbers', () {
      // Arrange
      final a = 2;
      final b = 3;
      
      // Act
      final result = calculator.add(a, b);
      
      // Assert
      expect(result, equals(5));
    });
    
    test('subtract should return difference', () {
      expect(calculator.subtract(5, 3), equals(2));
    });
    
    test('multiply should return product', () {
      expect(calculator.multiply(2, 3), equals(6));
    });
    
    test('divide should throw on zero', () {
      expect(
        () => calculator.divide(10, 0),
        throwsArgumentError,
      );
    });
  });
}
```

### 异步测试

```dart
// lib/user_service.dart
class UserService {
  final ApiClient apiClient;
  
  UserService(this.apiClient);
  
  Future<User> getUser(String id) async {
    final data = await apiClient.get('/users/$id');
    return User.fromJson(data);
  }
  
  Future<List<User>> getUsers() async {
    final data = await apiClient.get('/users');
    return (data as List).map((e) => User.fromJson(e)).toList();
  }
}

// test/user_service_test.dart
void main() {
  late UserService userService;
  late MockApiClient mockApi;
  
  setUp(() {
    mockApi = MockApiClient();
    userService = UserService(mockApi);
  });
  
  group('UserService', () {
    test('getUser returns user', () async {
      // Arrange
      when(mockApi.get('/users/1')).thenAnswer((_) async => {'id': '1', 'name': 'Test'});
      
      // Act
      final user = await userService.getUser('1');
      
      // Assert
      expect(user.id, equals('1'));
      expect(user.name, equals('Test'));
    });
    
    test('getUsers returns list of users', () async {
      // Arrange
      when(mockApi.get('/users')).thenAnswer((_) async => [
        {'id': '1', 'name': 'User 1'},
        {'id': '2', 'name': 'User 2'},
      ]);
      
      // Act
      final users = await userService.getUsers();
      
      // Assert
      expect(users.length, equals(2));
      expect(users[0].name, equals('User 1'));
    });
  });
}
```

## Widget 测试

### 基本 Widget 测试

```dart
// lib/counter_widget.dart
class CounterWidget extends StatefulWidget {
  @override
  _CounterWidgetState createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int _count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Count: $_count', key: Key('count_text')),
        ElevatedButton(
          key: Key('increment_button'),
          onPressed: () => setState(() => _count++),
          child: Text('Increment'),
        ),
      ],
    );
  }
}

// test/counter_widget_test.dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  group('CounterWidget', () {
    testWidgets('displays initial count', (WidgetTester tester) async {
      // Build widget
      await tester.pumpWidget(MaterialApp(
        home: CounterWidget(),
      ));
      
      // Verify
      expect(find.text('Count: 0'), findsOneWidget);
    });
    
    testWidgets('increments count on button press', (WidgetTester tester) async {
      // Build widget
      await tester.pumpWidget(MaterialApp(
        home: CounterWidget(),
      ));
      
      // Tap button
      await tester.tap(find.byKey(Key('increment_button')));
      
      // Rebuild
      await tester.pump();
      
      // Verify
      expect(find.text('Count: 1'), findsOneWidget);
    });
  });
}
```

### 查找 Widget

```dart
testWidgets('finding widgets example', (WidgetTester tester) async {
  await tester.pumpWidget(MyApp());
  
  // 按文本查找
  expect(find.text('Hello'), findsOneWidget);
  
  // 按Key 查找
  expect(find.byKey(Key('my_key')), findsOneWidget);
  
  // 按类型查找
  expect(find.byType(ElevatedButton), findsOneWidget);
  expect(find.byType(Text), findsNWidgets(3));
  
  // 按Icon 查找
  expect(find.byIcon(Icons.add), findsOneWidget);
  
  // 按Widget 子类查找
  expect(find.byWidgetPredicate(
    (widget) => widget is Text && widget.data?.contains('Hello') == true,
  ), findsOneWidget);
  
  // 查找祖先
  final button = find.ancestor(
    of: find.text('Submit'),
    matching: find.byType(ElevatedButton),
  );
  
  // 查找后代
  final textInButton = find.descendant(
    of: find.byType(ElevatedButton),
    matching: find.byType(Text),
  );
});
```

### 用户交互测试

```dart
testWidgets('user interactions', (WidgetTester tester) async {
  await tester.pumpWidget(MaterialApp(
    home: LoginForm(),
  ));
  
  // 输入文本
  await tester.enterText(
    find.byKey(Key('email_field')),
    'test@example.com',
  );
  
  // 点击按钮
  await tester.tap(find.byKey(Key('submit_button')));
  
  // 等待异步操作
  await tester.pumpAndSettle();
  
  // 验证结果
  expect(find.text('Login successful'), findsOneWidget);
});

testWidgets('scrolling', (WidgetTester tester) async {
  await tester.pumpWidget(MaterialApp(
    home: ListView.builder(
      itemCount: 100,
      itemBuilder: (context, index) => ListTile(title: Text('Item $index')),
    ),
  ));
  
  // 滚动到特定项
  await tester.scrollUntilVisible(
    find.text('Item 50'),
    100,  // 每次滚动的像素
  );
  
  // 验证项可见
  expect(find.text('Item 50'), findsOneWidget);
});
```

## 集成测试

### 设置集成测试

```dart
// integration_test/app_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:integration_test/integration_test.dart';
import 'package:my_app/main.dart';

void main() {
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();
  
  group('end-to-end test', () {
    testWidgets('complete login flow', (tester) async {
      // 启动应用
      await tester.pumpWidget(MyApp());
      
      // 等待启动画面
      await tester.pumpAndSettle();
      
      // 验证登录页
      expect(find.text('Login'), findsOneWidget);
      
      // 输入凭据
      await tester.enterText(
        find.byKey(Key('email_field')),
        'test@example.com',
      );
      await tester.enterText(
        find.byKey(Key('password_field')),
        'password123',
      );
      
      // 点击登录
      await tester.tap(find.byKey(Key('login_button')));
      await tester.pumpAndSettle();
      
      // 验证主页
      expect(find.text('Welcome'), findsOneWidget);
    });
  });
}
```

### 运行集成测试

```bash
# 运行集成测试
flutter test integration_test/app_test.dart

# 在特定设备上运行
flutter test integration_test/app_test.dart -d <device_id>
```

## Mock 和 Stub

### 使用 mockito

```dart
// lib/api_client.dart
class ApiClient {
  Future<Map<String, dynamic>> get(String path) async {
    // 实际实现
  }
  
  Future<Map<String, dynamic>> post(String path, dynamic data) async {
    // 实际实现
  }
}

// 生成 Mock 类
// 运行: flutter pub run build_runner build

// test/api_client.mocks.dart (自动生成)
// 或者手动创建:
class MockApiClient extends Mock implements ApiClient {}

// test/user_repository_test.dart
void main() {
  late UserRepository repository;
  late MockApiClient mockApi;
  
  setUp(() {
    mockApi = MockApiClient();
    repository = UserRepository(mockApi);
  });
  
  test('fetchUser returns user', () async {
    // Arrange
    when(mockApi.get('/users/1')).thenAnswer((_) async => {
      'id': '1',
      'name': 'Test User',
      'email': 'test@example.com',
    });
    
    // Act
    final user = await repository.fetchUser('1');
    
    // Assert
    expect(user.name, equals('Test User'));
    verify(mockApi.get('/users/1')).called(1);
  });
  
  test('fetchUser throws on error', () async {
    // Arrange
    when(mockApi.get('/users/1'))
        .thenThrow(Exception('Network error'));
    
    // Act & Assert
    expect(
      () => repository.fetchUser('1'),
      throwsException,
    );
  });
}
```

### 使用 fake 实现简单 Mock

```dart
class FakeApiClient extends Fake implements ApiClient {
  final Map<String, dynamic> _responses = {};
  
  void setResponse(String path, dynamic response) {
    _responses[path] = response;
  }
  
  @override
  Future<Map<String, dynamic>> get(String path) async {
    if (_responses.containsKey(path)) {
      return _responses[path];
    }
    throw Exception('Not found: $path');
  }
}

void main() {
  test('with fake', () async {
    final fakeApi = FakeApiClient();
    fakeApi.setResponse('/users/1', {'id': '1', 'name': 'Test'});
    
    final repository = UserRepository(fakeApi);
    final user = await repository.fetchUser('1');
    
    expect(user.name, equals('Test'));
  });
}
```

## 测试最佳实践

### 测试命名

```dart
// 好的命名
group('Calculator', () {
  test('add returns sum of two positive numbers', () {});
  test('add handles negative numbers', () {});
  test('divide throws ArgumentError when divisor is zero', () {});
});

// 不好的命名
group('Calculator', () {
  test('test1', () {});
  test('add test', () {});
});
```

### AAA 模式

```dart
test('should calculate total with discount', () {
  // Arrange (准备)
  final cart = Cart();
  cart.addItem(Item(name: 'Product', price: 100));
  final discount = 0.1;
  
  // Act (执行)
  final total = cart.calculateTotal(discount);
  
  // Assert (断言)
  expect(total, equals(90));
});
```

### 测试覆盖

```bash
# 运行测试并生成覆盖率报告
flutter test --coverage

# 查看覆盖率
genhtml coverage/lcov.info -o coverage/html
```

### 测试数据

```dart
// 使用 fixture 创建测试数据
class UserFixture {
  static User create({
    String? id,
    String? name,
    String? email,
  }) {
    return User(
      id: id ?? 'default-id',
      name: name ?? 'Default Name',
      email: email ?? 'default@example.com',
    );
  }
  
  static List<User> createList(int count) {
    return List.generate(
      count,
      (i) => create(
        id: 'user-$i',
        name: 'User $i',
      ),
    );
  }
}

void main() {
  test('with fixture', () {
    final user = UserFixture.create(name: 'Test User');
    expect(user.name, equals('Test User'));
  });
}
```

### 参数化测试

```dart
group('Calculator parameterized', () {
  final testCases = [
    {'a': 1, 'b': 2, 'expected': 3},
    {'a': 0, 'b': 0, 'expected': 0},
    {'a': -1, 'b': 1, 'expected': 0},
    {'a': 100, 'b': 200, 'expected': 300},
  ];
  
  for (final testCase in testCases) {
    test('add ${testCase['a']} + ${testCase['b']} = ${testCase['expected']}', () {
      final calculator = Calculator();
      expect(
        calculator.add(testCase['a'] as int, testCase['b'] as int),
        equals(testCase['expected']),
      );
    });
  }
});
```

---

*最后更新: 2026-02-19*

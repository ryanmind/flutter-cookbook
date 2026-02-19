# Flutter 设计模式

## 概述

设计模式是解决常见问题的可复用方案。本文介绍 Flutter 开发中常用的设计模式及其应用场景。

## 创建型模式

### 1. 单例模式 (Singleton)

确保一个类只有一个实例，提供全局访问点。

```dart
// 使用 static 实现
class ApiService {
  static final ApiService _instance = ApiService._internal();
  factory ApiService() => _instance;
  ApiService._internal();

  // 或者使用 Dart 的 lazy 初始化
  static ApiService get instance => _instance;

  Future<Response> get(String path) async {
    // ...
  }
}

// 使用
final api = ApiService.instance;
// 或
final api = ApiService();
```

### 2. 工厂模式 (Factory)

通过工厂方法创建对象，隐藏创建逻辑。

```dart
// 产品接口
abstract class Button {
  void render();
}

// 具体产品
class AndroidButton implements Button {
  @override
  void render() => print('Android Material Button');
}

class IOSButton implements Button {
  @override
  void render() => print('iOS Cupertino Button');
}

// 工厂
class ButtonFactory {
  static Button create(Platform platform) {
    return switch (platform) {
      Platform.android => AndroidButton(),
      Platform.ios => IOSButton(),
    };
  }
}

// 使用
final button = ButtonFactory.create(Platform.ios);
button.render();
```

### 3. 建造者模式 (Builder)

分步构建复杂对象。

```dart
class User {
  final String name;
  final int? age;
  final String? email;
  final String? phone;
  final List<String>? tags;

  User._({
    required this.name,
    this.age,
    this.email,
    this.phone,
    this.tags,
  });

  // 使用 Dart 的可选命名参数也可以达到类似效果
  factory User({
    required String name,
    int? age,
    String? email,
    String? phone,
    List<String>? tags,
  }) {
    return User._(
      name: name,
      age: age,
      email: email,
      phone: phone,
      tags: tags,
    );
  }
}

// 使用级联语法的 Builder
class AlertDialogBuilder {
  String? title;
  String? message;
  List<Widget>? actions;
  bool barrierDismissible = true;

  AlertDialogBuilder setTitle(String title) {
    this.title = title;
    return this;
  }

  AlertDialogBuilder setMessage(String message) {
    this.message = message;
    return this;
  }

  AlertDialogBuilder setActions(List<Widget> actions) {
    this.actions = actions;
    return this;
  }

  AlertDialog build() {
    return AlertDialog(
      title: title != null ? Text(title!) : null,
      content: message != null ? Text(message!) : null,
      actions: actions,
    );
  }
}

// 使用
final dialog = AlertDialogBuilder()
    .setTitle('确认')
    .setMessage('确定要删除吗？')
    .setActions([
      TextButton(child: Text('取消'), onPressed: () {}),
      TextButton(child: Text('确定'), onPressed: () {}),
    ])
    .build();
```

## 结构型模式

### 1. 适配器模式 (Adapter)

将一个类的接口转换为另一个接口。

```dart
// 目标接口
abstract class UserRepository {
  Future<User> getUser(String id);
}

// 第三方 SDK
class ThirdPartyUserApi {
  Future<Map<String, dynamic>> fetchUserData(String userId) async {
    return {'user_id': userId, 'user_name': 'John'};
  }
}

// 适配器
class ThirdPartyUserRepository implements UserRepository {
  final ThirdPartyUserApi _api;

  ThirdPartyUserRepository(this._api);

  @override
  Future<User> getUser(String id) async {
    final data = await _api.fetchUserData(id);
    return User(
      id: data['user_id'],
      name: data['user_name'],
    );
  }
}
```

### 2. 装饰器模式 (Decorator)

动态添加功能到对象。

```dart
// 组件接口
abstract class Widget {
  Widget build();
}

// 具体组件
class TextWidget implements Widget {
  final String text;
  TextWidget(this.text);

  @override
  Widget build() => Text(text);
}

// 装饰器基类
abstract class WidgetDecorator implements Widget {
  final Widget widget;
  WidgetDecorator(this.widget);
}

// 具体装饰器
class PaddingDecorator extends WidgetDecorator {
  final EdgeInsets padding;
  PaddingDecorator(Widget widget, this.padding) : super(widget);

  @override
  Widget build() => Padding(
        padding: padding,
        child: widget.build(),
      );
}

class BorderDecorator extends WidgetDecorator {
  final Border border;
  BorderDecorator(Widget widget, this.border) : super(widget);

  @override
  Widget build() => Container(
        decoration: BoxDecoration(border: border),
        child: widget.build(),
      );
}

// 使用
final decoratedWidget = BorderDecorator(
  PaddingDecorator(
    TextWidget('Hello'),
    EdgeInsets.all(16),
  ),
  Border.all(color: Colors.blue),
);
```

### 3. 组合模式 (Composite)

将对象组合成树形结构。

```dart
// 组件
abstract class MenuComponent {
  String get title;
  void display([int depth = 0]);
}

// 叶子节点
class MenuItem implements MenuComponent {
  @override
  final String title;
  final VoidCallback? onTap;

  MenuItem(this.title, {this.onTap});

  @override
  void display([int depth = 0]) {
    print('${'  ' * depth}- $title');
  }
}

// 组合节点
class MenuGroup implements MenuComponent {
  @override
  final String title;
  final List<MenuComponent> children = [];

  MenuGroup(this.title);

  void add(MenuComponent component) => children.add(component);

  @override
  void display([int depth = 0]) {
    print('${'  ' * depth}+ $title');
    for (var child in children) {
      child.display(depth + 1);
    }
  }
}

// 使用
final menu = MenuGroup('主菜单')
  ..add(MenuItem('首页'))
  ..add(MenuGroup('设置')
    ..add(MenuItem('账户'))
    ..add(MenuItem('通知'))
    ..add(MenuItem('隐私')))
  ..add(MenuItem('退出'));

menu.display();
// 输出:
// + 主菜单
//   - 首页
//   + 设置
//     - 账户
//     - 通知
//     - 隐私
//   - 退出
```

## 行为型模式

### 1. 观察者模式 (Observer)

对象间的一对多依赖关系。

```dart
// Flutter 内置支持：ValueNotifier, ChangeNotifier, Stream

// 使用 ValueNotifier
class Counter {
  final ValueNotifier<int> count = ValueNotifier(0);

  void increment() => count.value++;
  void decrement() => count.value--;

  void dispose() => count.dispose();
}

// 使用
class CounterWidget extends StatelessWidget {
  final Counter counter;

  const CounterWidget(this.counter);

  @override
  Widget build(BuildContext context) {
    return ValueListenableBuilder<int>(
      valueListenable: counter.count,
      builder: (context, value, _) {
        return Text('Count: $value');
      },
    );
  }
}

// 使用 Stream
class EventBus {
  final _streamController = StreamController<dynamic>.broadcast();

  Stream<T> on<T>() => _streamController.stream.where((e) => e is T).cast<T>();

  void emit<T>(T event) => _streamController.add(event);

  void dispose() => _streamController.close();
}

// 使用
final eventBus = EventBus();

// 监听
eventBus.on<UserLoggedInEvent>().listen((event) {
  print('User logged in: ${event.user.name}');
});

// 发送
eventBus.emit(UserLoggedInEvent(user));
```

### 2. 策略模式 (Strategy)

定义算法族，使其可互换。

```dart
// 策略接口
abstract class PricingStrategy {
  double calculate(double price);
}

// 具体策略
class NormalPricing implements PricingStrategy {
  @override
  double calculate(double price) => price;
}

class DiscountPricing implements PricingStrategy {
  final double discount;

  DiscountPricing(this.discount);

  @override
  double calculate(double price) => price * (1 - discount);
}

class VipPricing implements PricingStrategy {
  @override
  double calculate(double price) => price * 0.8;
}

// 上下文
class PriceCalculator {
  PricingStrategy _strategy;

  PriceCalculator(this._strategy);

  void setStrategy(PricingStrategy strategy) {
    _strategy = strategy;
  }

  double calculate(double price) {
    return _strategy.calculate(price);
  }
}

// 使用
final calculator = PriceCalculator(NormalPricing());
print(calculator.calculate(100)); // 100

calculator.setStrategy(DiscountPricing(0.2));
print(calculator.calculate(100)); // 80

calculator.setStrategy(VipPricing());
print(calculator.calculate(100)); // 80
```

### 3. 命令模式 (Command)

将请求封装为对象。

```dart
// 命令接口
abstract class Command {
  void execute();
  void undo();
}

// 接收者
class TextEditor {
  String text = '';

  void insert(String newText, int position) {
    text = text.substring(0, position) + newText + text.substring(position);
  }

  void delete(int position, int length) {
    text = text.substring(0, position) + text.substring(position + length);
  }
}

// 具体命令
class InsertCommand implements Command {
  final TextEditor editor;
  final String text;
  final int position;

  InsertCommand(this.editor, this.text, this.position);

  @override
  void execute() => editor.insert(text, position);

  @override
  void undo() => editor.delete(position, text.length);
}

// 调用者
class EditorController {
  final List<Command> _history = [];

  void execute(Command command) {
    command.execute();
    _history.add(command);
  }

  void undo() {
    if (_history.isNotEmpty) {
      _history.removeLast().undo();
    }
  }
}
```

### 4. 状态模式 (State)

允许对象在状态改变时改变行为。

```dart
// 状态接口
abstract class TrafficLightState {
  Color get color;
  Duration get duration;
  TrafficLightState get next;
}

// 具体状态
class RedLight implements TrafficLightState {
  @override
  Color get color => Colors.red;

  @override
  Duration get duration => Duration(seconds: 30);

  @override
  TrafficLightState get next => GreenLight();
}

class GreenLight implements TrafficLightState {
  @override
  Color get color => Colors.green;

  @override
  Duration get duration => Duration(seconds: 25);

  @override
  TrafficLightState get next => YellowLight();
}

class YellowLight implements TrafficLightState {
  @override
  Color get color => Colors.yellow;

  @override
  Duration get duration => Duration(seconds: 5);

  @override
  TrafficLightState get next => RedLight();
}

// 上下文
class TrafficLight extends ChangeNotifier {
  TrafficLightState _state = RedLight();

  TrafficLightState get state => _state;

  void change() {
    _state = _state.next;
    notifyListeners();
  }
}

// 在 Widget 中使用
class TrafficLightWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return AnimatedContainer(
      duration: Duration(milliseconds: 500),
      width: 60,
      height: 60,
      decoration: BoxDecoration(
        color: context.watch<TrafficLight>().state.color,
        shape: BoxShape.circle,
      ),
    );
  }
}
```

## Flutter 特有模式

### 1. Repository 模式

数据访问抽象层。

```dart
abstract class UserRepository {
  Future<User> getUser(String id);
  Future<void> saveUser(User user);
  Future<void> deleteUser(String id);
}

class UserRepositoryImpl implements UserRepository {
  final UserRemoteDataSource _remote;
  final UserLocalDataSource _local;

  UserRepositoryImpl(this._remote, this._local);

  @override
  Future<User> getUser(String id) async {
    // 先尝试本地缓存
    final cached = await _local.getUser(id);
    if (cached != null) return cached;

    // 从网络获取
    final user = await _remote.getUser(id);
    await _local.cacheUser(user);
    return user;
  }
}
```

### 2. BLoC 模式

业务逻辑与 UI 分离。

```dart
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
```

### 3. Provider 模式

依赖注入和状态管理。

```dart
// Provider 注入依赖
void main() {
  runApp(
    MultiProvider(
      providers: [
        Provider(create: (_) => Dio()),
        Provider(create: (_) => ApiClient()),
        ProxyProvider<Dio, UserRepository>(
          update: (_, dio, __) => UserRepositoryImpl(dio),
        ),
      ],
      child: MyApp(),
    ),
  );
}
```

## 最佳实践

### 1. 选择合适的模式

| 场景 | 推荐模式 |
|------|----------|
| 全局服务 | 单例 |
| 状态管理 | 观察者、状态模式 |
| 数据访问 | Repository 模式 |
| 复杂对象创建 | 建造者、工厂模式 |
| 多平台适配 | 适配器、策略模式 |

### 2. 避免过度设计

```dart
// ❌ 过度设计：简单需求使用复杂模式
class UserFactory {
  static User create(String name, int age) => User(name, age);
}

// ✅ 简单实现
const User({required this.name, required this.age});
```

### 3. 组合优于继承

```dart
// ❌ 继承
class AnimatedCard extends Card {
  // 难以组合多种功能
}

// ✅ 组合
Widget build(BuildContext context) {
  return AnimatedContainer(
    duration: Duration(milliseconds: 300),
    child: Card(child: content),
  );
}
```

---

*最后更新: 2026-02-19*

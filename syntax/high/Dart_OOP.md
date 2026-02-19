# Dart 面向对象编程 - 初学者指南

## 目录
1. [类与对象](#类与对象)
2. [构造函数](#构造函数)
3. [继承](#继承)
4. [抽象类与接口](#抽象类与接口)
5. [Mixin 混入](#mixin-混入)
6. [扩展方法](#扩展方法)
7. [枚举](#枚举)
8. [常见错误与解决方案](#常见错误与解决方案)

## 类与对象

### 类的定义

```dart
class Person {
  // 属性（字段）
  String name;
  int age;
  
  // 方法
  void introduce() {
    print('我是 $name，今年 $age 岁');
  }
  
  // Getter
  bool get isAdult => age >= 18;
  
  // Setter
  set updateAge(int newAge) {
    if (newAge > 0) {
      age = newAge;
    }
  }
}
```

### 创建对象

```dart
void main() {
  // 创建对象
  var person = Person();
  person.name = '张三';
  person.age = 25;
  
  // 调用方法
  person.introduce();  // 我是 张三，今年 25 岁
  
  // 使用 getter
  print(person.isAdult);  // true
  
  // 使用 setter
  person.updateAge = 26;
  print(person.age);  // 26
}
```

### 私有成员

Dart 使用 `_` 前缀表示私有成员（库级别私有）：

```dart
class BankAccount {
  // 私有属性
  double _balance = 0;
  
  // 公开方法
  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
    }
  }
  
  void withdraw(double amount) {
    if (amount > 0 && amount <= _balance) {
      _balance -= amount;
    }
  }
  
  // 只读访问
  double get balance => _balance;
}

void main() {
  var account = BankAccount();
  account.deposit(1000);
  account.withdraw(200);
  print(account.balance);  // 800
  // account._balance = 0;  // ❌ 错误，无法访问私有成员
}
```

### 静态成员

```dart
class MathUtils {
  // 静态属性
  static const double pi = 3.14159;
  static int calculationCount = 0;
  
  // 静态方法
  static double circleArea(double radius) {
    calculationCount++;
    return pi * radius * radius;
  }
  
  // 实例方法不能直接访问静态成员
  void printInfo() {
    print('计算次数：$calculationCount');
  }
}

void main() {
  print(MathUtils.pi);  // 3.14159
  print(MathUtils.circleArea(5));  // 78.53975
  
  // 访问静态成员
  MathUtils.calculationCount = 0;
}
```

## 构造函数

### 默认构造函数

```dart
class Person {
  String name;
  int age;
  
  // 默认构造函数
  Person(this.name, this.age);
  
  // 或者手动赋值
  // Person(String name, int age) {
  //   this.name = name;
  //   this.age = age;
  // }
}
```

### 命名构造函数

```dart
class Person {
  String name;
  int age;
  
  // 默认构造函数
  Person(this.name, this.age);
  
  // 命名构造函数
  Person.guest() : name = '访客', age = 0;
  
  Person.fromMap(Map<String, dynamic> map)
      : name = map['name'],
        age = map['age'];
  
  Person.copy(Person other)
      : name = other.name,
        age = other.age;
}

void main() {
  var p1 = Person('张三', 25);
  var p2 = Person.guest();
  var p3 = Person.fromMap({'name': '李四', 'age': 30});
  var p4 = Person.copy(p1);
}
```

### 初始化列表

```dart
class Rectangle {
  final double width;
  final double height;
  final double area;
  
  // 初始化列表在构造函数体执行前运行
  Rectangle(this.width, this.height) 
      : area = width * height,
        assert(width > 0, '宽度必须大于 0'),
        assert(height > 0, '高度必须大于 0');
  
  // 也可以用于验证
  Rectangle.square(double size)
      : width = size,
        height = size,
        area = size * size;
}

void main() {
  var rect = Rectangle(10, 5);
  print('面积：${rect.area}');  // 面积：50
  
  var square = Rectangle.square(5);
  print('正方形面积：${square.area}');  // 正方形面积：25
}
```

### 常量构造函数

```dart
class Point {
  final double x;
  final double y;
  
  // 常量构造函数
  const Point(this.x, this.y);
  
  // 常量命名构造函数
  const Point.origin() : x = 0, y = 0;
  
  @override
  String toString() => 'Point($x, $y)';
}

void main() {
  // 创建编译时常量
  const p1 = Point(0, 0);
  const p2 = Point(0, 0);
  print(identical(p1, p2));  // true，相同对象
  
  const origin = Point.origin();
  
  // 非常量创建
  var p3 = Point(1, 2);
  var p4 = Point(1, 2);
  print(identical(p3, p4));  // false，不同对象
}
```

### 工厂构造函数

```dart
class Logger {
  final String name;
  static final Map<String, Logger> _cache = {};
  
  // 工厂构造函数：可以返回现有实例
  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }
  
  // 私有命名构造函数
  Logger._internal(this.name);
  
  void log(String message) {
    print('[$name] $message');
  }
}

void main() {
  var logger1 = Logger('APP');
  var logger2 = Logger('APP');
  print(identical(logger1, logger2));  // true，同一实例
  
  logger1.log('启动应用');
}
```

### 重定向构造函数

```dart
class Person {
  String name;
  int age;
  String? email;
  
  // 主构造函数
  Person(this.name, this.age, {this.email});
  
  // 重定向到主构造函数
  Person.simple(String name) : this(name, 0);
  
  Person.withEmail(String name, int age, String email)
      : this(name, age, email: email);
}
```

## 继承

### extends 关键字

```dart
class Animal {
  String name;
  
  Animal(this.name);
  
  void speak() {
    print('$name 发出声音');
  }
  
  void eat() {
    print('$name 在吃东西');
  }
}

class Dog extends Animal {
  String breed;
  
  // 调用父类构造函数
  Dog(String name, this.breed) : super(name);
  
  // 重写父类方法
  @override
  void speak() {
    print('$name 汪汪叫');
  }
  
  // 子类特有方法
  void fetch() {
    print('$name 去捡球');
  }
}

void main() {
  var dog = Dog('小黑', '拉布拉多');
  dog.speak();   // 小黑 汪汪叫
  dog.eat();     // 小黑 在吃东西
  dog.fetch();   // 小黑 去捡球
}
```

### super 关键字

```dart
class Vehicle {
  String brand;
  int year;
  
  Vehicle(this.brand, this.year);
  
  void start() {
    print('$brand 启动');
  }
  
  String get info => '$year $brand';
}

class Car extends Vehicle {
  String model;
  
  Car(String brand, this.model, int year) : super(brand, year);
  
  @override
  void start() {
    super.start();  // 调用父类方法
    print('$brand $model 准备行驶');
  }
  
  @override
  String get info => '${super.info} $model';
}

void main() {
  var car = Car('丰田', '凯美瑞', 2024);
  car.start();
  // 丰田 启动
  // 丰田 凯美瑞 准备行驶
  
  print(car.info);  // 2024 丰田 凯美瑞
}
```

### 构造函数继承

```dart
class Parent {
  String name;
  int value;
  
  Parent(this.name, {this.value = 0});
  
  Parent.defaults() : name = '默认', value = 0;
}

class Child extends Parent {
  String extra;
  
  // 子类构造函数必须调用父类构造函数
  Child(String name, this.extra, {int value = 0})
      : super(name, value: value);
  
  // 调用父类命名构造函数
  Child.defaults(this.extra) : super.defaults();
}
```

## 抽象类与接口

### 抽象类

```dart
// 抽象类不能被实例化
abstract class Shape {
  // 抽象方法（没有实现）
  double area();
  double perimeter();
  
  // 具体方法
  void printInfo() {
    print('面积：${area()}，周长：${perimeter()}');
  }
}

class Circle extends Shape {
  double radius;
  
  Circle(this.radius);
  
  @override
  double area() => 3.14159 * radius * radius;
  
  @override
  double perimeter() => 2 * 3.14159 * radius;
}

class Rectangle extends Shape {
  double width;
  double height;
  
  Rectangle(this.width, this.height);
  
  @override
  double area() => width * height;
  
  @override
  double perimeter() => 2 * (width + height);
}

void main() {
  // Shape shape = Shape();  // ❌ 错误，不能实例化抽象类
  
  var circle = Circle(5);
  circle.printInfo();  // 面积：78.53975，周长：31.4159
  
  var rect = Rectangle(10, 5);
  rect.printInfo();  // 面积：50，周长：30
}
```

### 接口（隐式接口）

Dart 没有专门的 `interface` 关键字，每个类都隐式定义了一个接口：

```dart
// 这个类隐式定义了一个接口
class Flyer {
  void fly() {
    print('飞行中...');
  }
}

// 使用 implements 实现接口
class Bird implements Flyer {
  @override
  void fly() {
    print('鸟儿飞翔');
  }
}

class Airplane implements Flyer {
  @override
  void fly() {
    print('飞机飞行');
  }
}

// 实现多个接口
class Swimmer {
  void swim() {
    print('游泳中...');
  }
}

class Duck implements Flyer, Swimmer {
  @override
  void fly() {
    print('鸭子飞翔');
  }
  
  @override
  void swim() {
    print('鸭子游泳');
  }
}
```

### extends vs implements

```dart
class Base {
  void methodA() => print('Base.methodA');
  void methodB() => print('Base.methodB');
}

// extends：继承实现，可以重写部分方法
class Extended extends Base {
  @override
  void methodA() => print('Extended.methodA');
  // methodB 继承自 Base
}

// implements：必须实现所有方法
class Implemented implements Base {
  @override
  void methodA() => print('Implemented.methodA');
  
  @override
  void methodB() => print('Implemented.methodB');
}
```

## Mixin 混入

### 基本用法

```dart
// 定义 Mixin
mixin Musical {
  void playMusic() {
    print('播放音乐');
  }
  
  void stopMusic() {
    print('停止音乐');
  }
}

mixin Recordable {
  void startRecording() {
    print('开始录音');
  }
  
  void stopRecording() {
    print('停止录音');
  }
}

// 使用 with 关键字混入
class Phone with Musical, Recordable {
  void call() {
    print('打电话');
  }
}

void main() {
  var phone = Phone();
  phone.call();           // 打电话
  phone.playMusic();      // 播放音乐
  phone.startRecording(); // 开始录音
}
```

### Mixin 与约束

```dart
// Mixin 可以约束使用它的类必须继承自某个类
mixin Flying on Animal {
  void fly() {
    print('$name 在飞翔');
  }
}

class Animal {
  String name;
  Animal(this.name);
}

// ✅ 正确：Bird 继承自 Animal
class Bird extends Animal with Flying {
  Bird(String name) : super(name);
}

// ❌ 错误：Car 没有继承自 Animal
// class Car with Flying {}  // 编译错误
```

### Mixin 实际应用

```dart
// 日志 Mixin
mixin Loggable {
  void log(String message) {
    print('[${DateTime.now()}] $runtimeType: $message');
  }
}

// 序列化 Mixin
mixin Serializable {
  Map<String, dynamic> toJson();
  
  static T fromJson<T>(Map<String, dynamic> json) {
    // 工厂方法实现
    throw UnimplementedError();
  }
}

class User with Loggable, Serializable {
  String name;
  int age;
  
  User(this.name, this.age);
  
  @override
  Map<String, dynamic> toJson() => {
    'name': name,
    'age': age,
  };
  
  void save() {
    log('保存用户: $toJson()');
  }
}
```

## 扩展方法

### 基本语法

```dart
// 为 String 添加扩展方法
extension StringExtension on String {
  // 添加属性
  bool get isBlank => trim().isEmpty;
  
  // 添加方法
  String capitalize() {
    if (isEmpty) return this;
    return '${this[0].toUpperCase()}${substring(1)}';
  }
  
  String reverse() => split('').reversed.join('');
}

void main() {
  String text = 'hello';
  print(text.capitalize());  // Hello
  print(text.reverse());     // olleh
  print(text.isBlank);       // false
  print('   '.isBlank);      // true
}
```

### 为泛型添加扩展

```dart
extension ListExtension<T> on List<T> {
  // 安全获取元素
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
}

void main() {
  var list = [1, 2, 3, 4, 5];
  print(list.getOrNull(10));  // null
  
  var numbers = [1, 2, 3, 4, 5, 6];
  var grouped = numbers.groupBy((n) => n % 2 == 0 ? '偶数' : '奇数');
  print(grouped);  // {奇数: [1, 3, 5], 偶数: [2, 4, 6]}
}
```

## 枚举

### 基本枚举

```dart
enum Status {
  pending,
  approved,
  rejected,
}

void main() {
  Status status = Status.pending;
  
  print(status);           // Status.pending
  print(status.name);      // pending
  print(status.index);     // 0
  
  // 遍历
  for (var s in Status.values) {
    print(s);
  }
  
  // switch
  switch (status) {
    case Status.pending:
      print('待处理');
      break;
    case Status.approved:
      print('已批准');
      break;
    case Status.rejected:
      print('已拒绝');
      break;
  }
}
```

### 增强枚举（Dart 2.17+）

```dart
enum Vehicle {
  car(tires: 4),
  bicycle(tires: 2),
  tricycle(tires: 3);
  
  final int tires;
  
  const Vehicle({required this.tires});
  
  String get description => '$name 有 $tires 个轮子';
  
  bool get hasMultipleTires => tires > 2;
}

void main() {
  print(Vehicle.car.tires);           // 4
  print(Vehicle.bicycle.description); // bicycle 有 2 个轮子
  
  // 使用扩展方法
  extension on Vehicle {
    bool get isEcoFriendly => this == Vehicle.bicycle;
  }
  
  print(Vehicle.bicycle.isEcoFriendly);  // true
}
```

### 枚举与 JSON

```dart
enum Priority {
  low,
  medium,
  high;
  
  // 从字符串解析
  static Priority fromString(String value) {
    return Priority.values.firstWhere(
      (p) => p.name == value.toLowerCase(),
      orElse: () => Priority.low,
    );
  }
  
  // 转换为 JSON
  String toJson() => name;
}

void main() {
  var priority = Priority.fromString('HIGH');
  print(priority);  // Priority.high
  
  print(Priority.medium.toJson());  // medium
}
```

## 常见错误与解决方案

### 错误 1：构造函数未调用父类构造函数

```dart
// ❌ 错误
class Parent {
  String name;
  Parent(this.name);
}

class Child extends Parent {
  int age;
  // Child(this.age);  // 编译错误
}

// ✅ 正确
class Child extends Parent {
  int age;
  
  Child(String name, this.age) : super(name);
}
```

### 错误 2：重写方法签名不匹配

```dart
class Base {
  int getValue() => 0;
}

// ❌ 错误：返回类型不匹配
class Derived extends Base {
  // @override
  // String getValue() => 'hello';  // 编译错误
}

// ✅ 正确
class Derived extends Base {
  @override
  int getValue() => 10;
}
```

### 错误 3：私有成员跨文件访问

```dart
// file1.dart
class MyClass {
  int _privateField = 10;
}

// file2.dart
import 'file1.dart';

void main() {
  var obj = MyClass();
  // print(obj._privateField);  // ❌ 错误，不同文件无法访问
}
```

### 错误 4：Mixin 使用约束错误

```dart
mixin Swimmable on Animal {
  void swim() => print('游泳');
}

class Animal {}

// ❌ 错误：没有继承 Animal
// class Boat with Swimmable {}  // 编译错误

// ✅ 正确
class Fish extends Animal with Swimmable {}
```

### 错误 5：枚举比较错误

```dart
enum Color { red, green, blue }

void main() {
  var c1 = Color.red;
  var c2 = Color.red;
  
  // ❌ 错误：使用字符串比较
  // if (c1.toString() == 'Color.red') {}
  
  // ✅ 正确：直接比较枚举值
  if (c1 == c2) {
    print('颜色相同');
  }
  
  // 或者使用 switch
  switch (c1) {
    case Color.red:
      print('红色');
      break;
    default:
      print('其他颜色');
  }
}
```

## 实战练习

### 练习 1：银行账户系统

```dart
abstract class BankAccount {
  String accountNumber;
  String ownerName;
  double _balance = 0;
  
  BankAccount(this.accountNumber, this.ownerName);
  
  double get balance => _balance;
  
  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
      print('存款成功：$amount，余额：$_balance');
    }
  }
  
  bool withdraw(double amount);
  
  void transfer(BankAccount target, double amount) {
    if (withdraw(amount)) {
      target.deposit(amount);
      print('转账成功');
    }
  }
}

class SavingsAccount extends BankAccount {
  double interestRate;
  
  SavingsAccount(String accountNumber, String ownerName, this.interestRate)
      : super(accountNumber, ownerName);
  
  @override
  bool withdraw(double amount) {
    if (amount > 0 && amount <= balance) {
      _balance -= amount;
      print('取款成功：$amount，余额：$balance');
      return true;
    }
    print('取款失败');
    return false;
  }
  
  void applyInterest() {
    double interest = balance * interestRate / 100;
    _balance += interest;
    print('利息已入账：$interest，余额：$balance');
  }
}

void main() {
  var account = SavingsAccount('001', '张三', 3.5);
  account.deposit(1000);
  account.applyInterest();
  account.withdraw(500);
}
```

### 练习 2：使用 Mixin 构建功能组合

```dart
mixin Serializable {
  Map<String, dynamic> toJson();
  
  static String _formatValue(dynamic value) {
    if (value is DateTime) return value.toIso8601String();
    if (value is Serializable) return value.toJson().toString();
    return value.toString();
  }
}

mixin Validatable {
  List<String> validate();
  
  bool get isValid => validate().isEmpty;
}

class User with Serializable, Validatable {
  String name;
  String email;
  int age;
  
  User({required this.name, required this.email, required this.age});
  
  @override
  Map<String, dynamic> toJson() => {
    'name': name,
    'email': email,
    'age': age,
  };
  
  @override
  List<String> validate() {
    final errors = <String>[];
    if (name.isEmpty) errors.add('姓名不能为空');
    if (!email.contains('@')) errors.add('邮箱格式不正确');
    if (age < 0) errors.add('年龄不能为负数');
    return errors;
  }
}

void main() {
  var user = User(name: '张三', email: 'test@example.com', age: 25);
  
  if (user.isValid) {
    print('用户数据有效');
    print(user.toJson());
  } else {
    print('验证失败：${user.validate()}');
  }
}
```

---

*最后更新: 2026-02-19*

# Dart 基础语法 - 初学者指南

## 目录
1. [变量与数据类型](#变量与数据类型)
2. [常量与不可变变量](#常量与不可变变量)
3. [运算符](#运算符)
4. [控制流语句](#控制流语句)
5. [函数](#函数)
6. [集合类型](#集合类型)
7. [空安全](#空安全)
8. [常见错误与解决方案](#常见错误与解决方案)

## 变量与数据类型

### 变量声明

Dart 是强类型语言，但支持类型推断：

```dart
// 显式类型声明
String name = 'Flutter';
int age = 25;
double price = 99.9;
bool isActive = true;

// 类型推断（推荐）
var name = 'Flutter';      // 推断为 String
var age = 25;              // 推断为 int
var price = 99.9;          // 推断为 double
var isActive = true;       // 推断为 bool

// dynamic 类型（不推荐，除非必要）
dynamic anyValue = 'hello';
anyValue = 123;            // 允许改变类型
```

### 基本数据类型

#### 数值类型

```dart
// int - 整数
int count = 42;
int hexValue = 0xFF;       // 十六进制：255

// double - 浮点数
double pi = 3.14159;
double scientific = 1.5e3; // 科学计数法：1500.0

// 数值运算
int a = 10;
int b = 3;
print(a + b);   // 13
print(a - b);   // 7
print(a * b);   // 30
print(a / b);   // 3.333...（返回 double）
print(a ~/ b);  // 3（整除）
print(a % b);   // 1（取余）

// 常用方法
int value = -5;
print(value.abs());        // 5（绝对值）
print(value.toString());   // '-5'

double d = 3.7;
print(d.round());          // 4（四舍五入）
print(d.floor());          // 3（向下取整）
print(d.ceil());           // 4（向上取整）
print(d.toInt());          // 3（转整数）
```

#### 字符串类型

```dart
// 字符串声明
String greeting = 'Hello';
String message = "World";
String multiline = '''
  多行字符串
  第二行
''';

// 字符串拼接
String fullName = 'John' + ' ' + 'Doe';
String interpolated = 'My name is $fullName';
String expression = '2 + 2 = ${2 + 2}';

// 常用属性和方法
String text = 'Hello, Dart!';
print(text.length);           // 12
print(text.isEmpty);          // false
print(text.isNotEmpty);       // true

print(text.toUpperCase());    // HELLO, DART!
print(text.toLowerCase());    // hello, dart!
print(text.contains('Dart'));// true
print(text.startsWith('He')); // true
print(text.endsWith('!'));    // true

print(text.substring(0, 5));  // Hello
print(text.split(', '));      // ['Hello', 'Dart!']
print(text.replaceAll('Dart', 'Flutter')); // Hello, Flutter!

// 去除空白
String padded = '  hello  ';
print(padded.trim());         // 'hello'
print(padded.trimLeft());     // 'hello  '
print(padded.trimRight());    // '  hello'
```

#### 布尔类型

```dart
bool isValid = true;
bool isEmpty = false;

// 逻辑运算
bool a = true;
bool b = false;
print(a && b);  // false（与）
print(a || b);  // true（或）
print(!a);      // false（非）
```

### 类型转换

```dart
// 字符串转数值
String numStr = '123';
int parsed = int.parse(numStr);           // 123
double parsedDouble = double.parse('3.14'); // 3.14

// 数值转字符串
int number = 42;
String str = number.toString();           // '42'
String fixed = 3.14159.toStringAsFixed(2); // '3.14'

// 安全转换（避免异常）
String? invalid = 'abc';
int? safeInt = int.tryParse(invalid);     // null
```

## 常量与不可变变量

### final - 运行时常量

```dart
// final 变量只能赋值一次
final String name = 'Flutter';
final age = 25;  // 类型推断

// final 可以在运行时确定值
final now = DateTime.now();  // 运行时计算

// final 用于类成员
class Person {
  final String name;
  
  Person(this.name);  // 构造时赋值
}
```

### const - 编译时常量

```dart
// const 必须在编译时确定值
const pi = 3.14159;
const maxRetry = 3;
const greeting = 'Hello';

// const 不能用运行时值
// const now = DateTime.now();  // ❌ 错误

// const 构造函数
const Point(this.x, this.y);
const origin = Point(0, 0);

// const 集合
const colors = ['red', 'green', 'blue'];
const settings = {'theme': 'dark', 'fontSize': 14};
```

### final vs const 对比

```dart
// final：运行时常量，可以延迟赋值
final currentTime = DateTime.now();  // ✅ 正确

// const：编译时常量，必须编译时确定
// const compileTime = DateTime.now();  // ❌ 错误

// 集合的区别
final finalList = [1, 2, 3];
finalList.add(4);  // ✅ 可以修改内容

const constList = [1, 2, 3];
// constList.add(4);  // ❌ 不能修改
```

## 运算符

### 算术运算符

```dart
int a = 10, b = 3;

print(a + b);   // 13 加
print(a - b);   // 7  减
print(a * b);   // 30 乘
print(a / b);   // 3.333... 除（返回 double）
print(a ~/ b);  // 3  整除
print(a % b);   // 1  取余
print(-a);      // -10 负号

// 自增/自减
int i = 5;
print(i++);     // 5（先返回，后加）
print(i);       // 6
print(++i);     // 7（先加，后返回）
```

### 关系运算符

```dart
int a = 10, b = 5;

print(a == b);  // false 等于
print(a != b);  // true  不等于
print(a > b);   // true  大于
print(a < b);   // false 小于
print(a >= b);  // true  大于等于
print(a <= b);  // false 小于等于
```

### 类型测试运算符

```dart
Object value = 'Hello';

// is - 类型检查
if (value is String) {
  print('是字符串');
}

// is! - 非类型检查
if (value is! int) {
  print('不是整数');
}

// as - 类型转换
String str = value as String;
```

### 赋值运算符

```dart
int a = 10;

a += 5;   // a = a + 5   → 15
a -= 3;   // a = a - 3   → 12
a *= 2;   // a = a * 2   → 24
a ~/= 4;  // a = a ~/ 4  → 6

// ??= - 空值赋值
int? b;
b ??= 10;  // 如果 b 为 null，赋值为 10
print(b);  // 10

b ??= 20;  // b 不为 null，不赋值
print(b);  // 10
```

### 条件运算符

```dart
// 三目运算符
int age = 20;
String status = age >= 18 ? '成年' : '未成年';
print(status);  // 成年

// ?? 空值合并运算符
String? name;
String displayName = name ?? '匿名';
print(displayName);  // 匿名

name = '张三';
displayName = name ?? '匿名';
print(displayName);  // 张三
```

### 级联运算符

```dart
// .. 级联运算符，连续调用方法
class Person {
  String? name;
  int? age;
  
  void introduce() {
    print('我是 $name，$age 岁');
  }
}

var person = Person()
  ..name = '张三'
  ..age = 25
  ..introduce();
// 输出：我是 张三，25 岁

// 等价于
var person2 = Person();
person2.name = '张三';
person2.age = 25;
person2.introduce();
```

## 控制流语句

### if-else

```dart
int score = 85;

if (score >= 90) {
  print('优秀');
} else if (score >= 60) {
  print('及格');
} else {
  print('不及格');
}

// if 表达式（Dart 2.5+）
String result = score >= 60 ? '及格' : '不及格';
```

### switch-case

```dart
int day = 1;

switch (day) {
  case 1:
    print('星期一');
    break;
  case 2:
    print('星期二');
    break;
  case 3:
  case 4:
  case 5:
    print('工作日');
    break;
  case 6:
  case 7:
    print('周末');
    break;
  default:
    print('无效日期');
}

// switch 表达式（Dart 3.0+）
String dayName = switch (day) {
  1 => '星期一',
  2 => '星期二',
  3 => '星期三',
  4 => '星期四',
  5 => '星期五',
  6 || 7 => '周末',
  _ => '无效日期',  // _ 是 default
};
```

### for 循环

```dart
// 标准 for 循环
for (int i = 0; i < 5; i++) {
  print(i);
}

// for-in 循环
var fruits = ['苹果', '香蕉', '橙子'];
for (var fruit in fruits) {
  print(fruit);
}

// forEach 方法
fruits.forEach((fruit) {
  print(fruit);
});

// 带索引的遍历
for (var i = 0; i < fruits.length; i++) {
  print('$i: ${fruits[i]}');
}
```

### while 和 do-while

```dart
// while 循环
int i = 0;
while (i < 5) {
  print(i);
  i++;
}

// do-while 循环（至少执行一次）
int j = 0;
do {
  print(j);
  j++;
} while (j < 5);
```

### break 和 continue

```dart
// break - 跳出整个循环
for (int i = 0; i < 10; i++) {
  if (i == 5) break;
  print(i);  // 0, 1, 2, 3, 4
}

// continue - 跳过当前迭代
for (int i = 0; i < 5; i++) {
  if (i == 2) continue;
  print(i);  // 0, 1, 3, 4
}
```

## 函数

### 函数定义

```dart
// 基本函数
int add(int a, int b) {
  return a + b;
}

// 箭头函数（单行表达式）
int subtract(int a, int b) => a - b;

// 无返回值
void greet(String name) {
  print('Hello, $name!');
}
```

### 参数类型

```dart
// 位置参数（必须）
int add(int a, int b) {
  return a + b;
}
add(1, 2);  // 3

// 可选位置参数（用 [] 包裹）
String greet(String name, [String? title]) {
  return title != null ? '$title $name' : name;
}
greet('张三');           // 张三
greet('张三', '先生');   // 先生 张三

// 默认值参数
String greetWithDefault(String name, [String title = '先生']) {
  return '$title $name';
}
greetWithDefault('张三');  // 先生 张三

// 命名参数（用 {} 包裹，推荐）
void createUser({
  required String name,
  required int age,
  String role = 'user',
}) {
  print('创建用户：$name, $age 岁, 角色：$role');
}

createUser(
  name: '张三',
  age: 25,
  role: 'admin',
);

// 可选命名参数（不需要 required）
void setConfig({String? theme, int? fontSize}) {
  print('主题：$theme, 字号：$fontSize');
}

setConfig(theme: 'dark');  // fontSize 为 null
```

### 函数作为参数

```dart
// 高阶函数
void executeFunction(int a, int b, int Function(int, int) operation) {
  print(operation(a, b));
}

executeFunction(10, 5, add);        // 15
executeFunction(10, 5, subtract);   // 5

// 匿名函数
executeFunction(10, 5, (a, b) => a * b);  // 50

// 箭头函数作为参数
var numbers = [1, 2, 3, 4, 5];
var doubled = numbers.map((n) => n * 2);
print(doubled);  // [2, 4, 6, 8, 10]
```

### 闭包

```dart
// 闭包：函数可以访问外部变量
Function makeAdder(int addBy) {
  return (int i) => i + addBy;
}

var add2 = makeAdder(2);
var add5 = makeAdder(5);

print(add2(3));  // 5
print(add5(3));  // 8
```

## 集合类型

### List（列表）

```dart
// 创建列表
var fruits = ['苹果', '香蕉', '橙子'];
List<int> numbers = [1, 2, 3, 4, 5];

// 访问元素
print(fruits[0]);           // 苹果
print(fruits.first);        // 苹果
print(fruits.last);         // 橙子
print(fruits.length);       // 3

// 修改列表
fruits.add('葡萄');         // 添加元素
fruits.addAll(['芒果', '桃子']);  // 添加多个
fruits.insert(1, '草莓');   // 插入到指定位置

fruits.remove('香蕉');      // 删除元素
fruits.removeAt(0);         // 删除指定索引
fruits.clear();             // 清空列表

// 常用方法
var nums = [3, 1, 4, 1, 5, 9, 2, 6];
nums.sort();                // 排序（原地）
print(nums);                // [1, 1, 2, 3, 4, 5, 6, 9]

var reversed = nums.reversed.toList();  // 反转

// 映射和过滤
var squares = nums.map((n) => n * n).toList();
var evens = nums.where((n) => n % 2 == 0).toList();
var firstEven = nums.firstWhere((n) => n % 2 == 0);
var hasEven = nums.any((n) => n % 2 == 0);
var allPositive = nums.every((n) => n > 0);

// 展开
var nested = [[1, 2], [3, 4], [5]];
var flattened = nested.expand((e) => e).toList();  // [1, 2, 3, 4, 5]
```

### Set（集合）

```dart
// 创建集合（元素唯一）
var fruits = {'苹果', '香蕉', '橙子'};
Set<int> numbers = {1, 2, 3, 3, 3};
print(numbers);  // {1, 2, 3}，自动去重

// 创建空集合（注意：{} 是 Map）
var emptySet = <String>{};

// 添加和删除
fruits.add('葡萄');
fruits.addAll(['芒果', '桃子']);
fruits.remove('香蕉');

// 集合操作
var setA = {1, 2, 3};
var setB = {2, 3, 4};

print(setA.union(setB));        // {1, 2, 3, 4} 并集
print(setA.intersection(setB)); // {2, 3} 交集
print(setA.difference(setB));   // {1} 差集

// 检查
print(setA.contains(2));        // true
print(setA.containsAll({1, 2}));// true
```

### Map（映射/字典）

```dart
// 创建 Map
var person = {
  'name': '张三',
  'age': 25,
  'city': '北京',
};
Map<String, int> scores = {
  'math': 95,
  'english': 88,
};

// 访问
print(person['name']);      // 张三
print(person['unknown']);   // null

// 添加和修改
person['email'] = 'test@example.com';  // 添加
person['age'] = 26;                     // 修改

// 删除
person.remove('city');

// 常用属性和方法
print(person.length);       // 3
print(person.keys);         // (name, age, email)
print(person.values);       // (张三, 26, test@example.com)
print(person.entries);      // MapEntry 的迭代器

// 遍历
person.forEach((key, value) {
  print('$key: $value');
});

for (var entry in person.entries) {
  print('${entry.key}: ${entry.value}');
}

// 更新
person.update('age', (value) => value + 1);
person.update('country', (value) => '中国', ifAbsent: () => '中国');

// 获取或设置默认值
var city = person['city'] ?? '未知';
var height = person.putIfAbsent('height', () => 175);
```

## 空安全

Dart 2.12+ 引入了空安全特性。

### 可空类型

```dart
// 非空类型（默认）
String name = '张三';
// name = null;  // ❌ 错误，不能赋 null

// 可空类型（加 ? 后缀）
String? nullableName;
nullableName = null;  // ✅ 正确
nullableName = '李四'; // ✅ 正确

// 检查是否为 null
if (nullableName != null) {
  print(nullableName.length);  // 类型提升，不再可空
}

// 安全调用
print(nullableName?.length);  // null 或字符串长度
```

### 空安全运算符

```dart
String? name;

// ?? 空值合并
String displayName = name ?? '匿名';

// ??= 空值赋值
name ??= '默认名';

// ?. 安全访问
int? length = name?.length;

// ! 非空断言（确定不为 null 时使用）
String forcedName = name!;  // 如果 name 为 null，抛出异常
```

### late 关键字

```dart
// late 延迟初始化
class Person {
  late String name;
  
  void init(String n) {
    name = n;  // 延迟赋值
  }
}

// late final
class Config {
  static late final String apiKey;
  
  static void init(String key) {
    apiKey = key;  // 只能赋值一次
  }
}

// 懒加载
late String heavyValue = _computeHeavyValue();

String _computeHeavyValue() {
  print('计算中...');
  return '结果';
}
// heavyValue 在第一次访问时才计算
```

## 常见错误与解决方案

### 错误 1：空安全相关

```dart
// ❌ 错误：可空类型不能直接访问属性
String? name;
// print(name.length);  // 编译错误

// ✅ 解决方案 1：安全访问
print(name?.length);

// ✅ 解决方案 2：空值合并
print(name?.length ?? 0);

// ✅ 解决方案 3：null 检查
if (name != null) {
  print(name.length);
}
```

### 错误 2：类型不匹配

```dart
// ❌ 错误：类型不匹配
int a = 10;
// String b = a;  // 编译错误

// ✅ 正确：显式转换
String b = a.toString();
```

### 错误 3：集合修改冲突

```dart
// ❌ 错误：遍历时修改集合
var list = [1, 2, 3];
// for (var item in list) {
//   list.remove(item);  // 运行时错误
// }

// ✅ 正确：使用副本或索引
for (var item in list.toList()) {
  list.remove(item);
}

// ✅ 正确：使用 removeWhere
list.removeWhere((item) => item < 3);
```

### 错误 4：final/const 误用

```dart
// ❌ 错误：const 不能用运行时值
// const time = DateTime.now();

// ✅ 正确：使用 final
final time = DateTime.now();

// ❌ 错误：final 不能重新赋值
final value = 10;
// value = 20;  // 编译错误

// ⚠️ 注意：final 集合内容可以修改
final list = [1, 2, 3];
list.add(4);  // ✅ 正确，修改的是内容，不是引用
// list = [4, 5, 6];  // ❌ 错误，不能重新赋值
```

### 错误 5：命名参数遗漏

```dart
// ❌ 错误：缺少 required 参数
void createUser({required String name, required int age}) {}
// createUser(name: '张三');  // 编译错误

// ✅ 正确
createUser(name: '张三', age: 25);
```

## 实战练习

### 练习 1：用户信息处理

```dart
class UserProcessor {
  // 处理用户信息
  Map<String, dynamic> processUser({
    required String name,
    required int age,
    String? email,
    List<String>? hobbies,
  }) {
    return {
      'name': name.toUpperCase(),
      'age': age,
      'email': email ?? '未设置',
      'hobbies': hobbies ?? [],
      'isAdult': age >= 18,
    };
  }
}

void main() {
  final processor = UserProcessor();
  final result = processor.processUser(
    name: '张三',
    age: 25,
    hobbies: ['编程', '阅读'],
  );
  print(result);
}
```

### 练习 2：列表数据处理

```dart
void main() {
  final numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  
  // 过滤偶数
  final evens = numbers.where((n) => n % 2 == 0).toList();
  print('偶数：$evens');
  
  // 计算平方
  final squares = numbers.map((n) => n * n).toList();
  print('平方：$squares');
  
  // 求和
  final sum = numbers.reduce((a, b) => a + b);
  print('总和：$sum');
  
  // 分组
  final grouped = <bool, List<int>>{};
  for (var n in numbers) {
    final key = n % 2 == 0;
    grouped.putIfAbsent(key, () => []).add(n);
  }
  print('分组：$grouped');
}
```

---

*最后更新: 2026-02-19*

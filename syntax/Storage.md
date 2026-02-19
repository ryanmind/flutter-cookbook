# Flutter 数据存储 - 初学者指南

## 目录
1. [存储方案概览](#存储方案概览)
2. [SharedPreferences](#sharedpreferences)
3. [Hive](#hive)
4. [SQLite](#sqlite)
5. [安全存储](#安全存储)
6. [文件存储](#文件存储)

## 存储方案概览

### 方案对比

| 方案 | 适用场景 | 数据类型 | 性能 |
|------|---------|---------|------|
| SharedPreferences | 简单键值对 | 基本类型 | 高 |
| Hive | 对象存储 | 任意对象 | 很高 |
| SQLite | 关系型数据 | 结构化数据 | 中 |
| flutter_secure_storage | 敏感数据 | 字符串 | 中 |
| 文件存储 | 大文件/媒体 | 任意 | 低 |

### 选择建议

```dart
// 简单设置/偏好 → SharedPreferences
// 对象缓存/离线数据 → Hive
// 复杂查询/关系数据 → SQLite
// 密码/Token → flutter_secure_storage
// 图片/文件 → 文件存储
```

## SharedPreferences

### 添加依赖

```yaml
dependencies:
  shared_preferences: ^2.2.0
```

### 基本用法

```dart
import 'package:shared_preferences/shared_preferences.dart';

class StorageService {
  late final SharedPreferences _prefs;
  
  Future<void> init() async {
    _prefs = await SharedPreferences.getInstance();
  }
  
  // 保存数据
  Future<void> setString(String key, String value) async {
    await _prefs.setString(key, value);
  }
  
  Future<void> setInt(String key, int value) async {
    await _prefs.setInt(key, value);
  }
  
  Future<void> setBool(String key, bool value) async {
    await _prefs.setBool(key, value);
  }
  
  Future<void> setDouble(String key, double value) async {
    await _prefs.setDouble(key, value);
  }
  
  Future<void> setStringList(String key, List<String> value) async {
    await _prefs.setStringList(key, value);
  }
  
  // 读取数据
  String? getString(String key) => _prefs.getString(key);
  int? getInt(String key) => _prefs.getInt(key);
  bool? getBool(String key) => _prefs.getBool(key);
  double? getDouble(String key) => _prefs.getDouble(key);
  List<String>? getStringList(String key) => _prefs.getStringList(key);
  
  // 删除数据
  Future<void> remove(String key) async {
    await _prefs.remove(key);
  }
  
  // 清空所有数据
  Future<void> clear() async {
    await _prefs.clear();
  }
  
  // 检查是否存在
  bool contains(String key) => _prefs.containsKey(key);
}
```

### 实际应用示例

```dart
// 用户偏好设置
class UserPreferences {
  static const _keyTheme = 'theme_mode';
  static const _keyLanguage = 'language_code';
  static const _keyNotifications = 'notifications_enabled';
  static const _keyRecentSearches = 'recent_searches';
  
  late final SharedPreferences _prefs;
  
  Future<void> init() async {
    _prefs = await SharedPreferences.getInstance();
  }
  
  // 主题设置
  ThemeMode get themeMode {
    final index = _prefs.getInt(_keyTheme) ?? 0;
    return ThemeMode.values[index];
  }
  
  Future<void> setThemeMode(ThemeMode mode) async {
    await _prefs.setInt(_keyTheme, mode.index);
  }
  
  // 语言设置
  String get languageCode => _prefs.getString(_keyLanguage) ?? 'en';
  
  Future<void> setLanguageCode(String code) async {
    await _prefs.setString(_keyLanguage, code);
  }
  
  // 通知开关
  bool get notificationsEnabled => _prefs.getBool(_keyNotifications) ?? true;
  
  Future<void> setNotificationsEnabled(bool enabled) async {
    await _prefs.setBool(_keyNotifications, enabled);
  }
  
  // 搜索历史
  List<String> get recentSearches => _prefs.getStringList(_keyRecentSearches) ?? [];
  
  Future<void> addRecentSearch(String query) async {
    final searches = recentSearches;
    searches.remove(query);  // 移除重复
    searches.insert(0, query);
    if (searches.length > 10) {
      searches.removeLast();
    }
    await _prefs.setStringList(_keyRecentSearches, searches);
  }
  
  Future<void> clearRecentSearches() async {
    await _prefs.remove(_keyRecentSearches);
  }
}
```

## Hive

### 添加依赖

```yaml
dependencies:
  hive: ^2.2.3
  hive_flutter: ^1.1.0

dev_dependencies:
  hive_generator: ^2.0.0
  build_runner: ^2.4.0
```

### 初始化

```dart
import 'package:hive_flutter/hive_flutter.dart';

Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Hive.initFlutter();
  
  // 注册适配器（如果使用代码生成）
  Hive.registerAdapter(UserAdapter());
  
  // 打开 Box
  await Hive.openBox('settings');
  await Hive.openBox<User>('users');
  
  runApp(MyApp());
}
```

### 基本用法

```dart
// 打开 Box
final settingsBox = await Hive.openBox('settings');

// 写入数据
await settingsBox.put('theme', 'dark');
await settingsBox.put('fontSize', 16);

// 读取数据
final theme = settingsBox.get('theme', defaultValue: 'light');
final fontSize = settingsBox.get('fontSize', defaultValue: 14);

// 删除数据
await settingsBox.delete('theme');

// 清空 Box
await settingsBox.clear();

// 关闭 Box
await settingsBox.close();
```

### 存储对象

```dart
// 方式 1：手动适配器
class User {
  final String id;
  final String name;
  final int age;
  
  User({required this.id, required this.name, required this.age});
}

class UserAdapter extends TypeAdapter<User> {
  @override
  final int typeId = 0;
  
  @override
  User read(BinaryReader reader) {
    return User(
      id: reader.readString(),
      name: reader.readString(),
      age: reader.readInt(),
    );
  }
  
  @override
  void write(BinaryWriter writer, User obj) {
    writer.writeString(obj.id);
    writer.writeString(obj.name);
    writer.writeInt(obj.age);
  }
}

// 方式 2：代码生成（推荐）
import 'package:hive/hive.dart';

part 'user.g.dart';

@HiveType(typeId: 0)
class User extends HiveObject {
  @HiveField(0)
  String id;
  
  @HiveField(1)
  String name;
  
  @HiveField(2)
  int age;
  
  @HiveField(3, defaultValue: true)
  bool isActive;
  
  User({required this.id, required this.name, required this.age, this.isActive = true});
}

// 运行代码生成
// flutter pub run build_runner build
```

### 使用 HiveObject

```dart
// HiveObject 提供了 save() 和 delete() 方法
class Todo extends HiveObject {
  String title;
  bool completed;
  
  Todo({required this.title, this.completed = false});
}

// 使用
final todoBox = await Hive.openBox<Todo>('todos');

// 添加
final todo = Todo(title: 'Learn Flutter');
await todoBox.add(todo);

// 修改并保存
todo.completed = true;
await todo.save();

// 删除
await todo.delete();
```

### 监听变化

```dart
// 监听 Box 变化
final box = await Hive.openBox('settings');
box.watch().listen((event) {
  print('Key: ${event.key}, Value: ${event.value}');
});

// 监听特定 key
box.watch(key: 'theme').listen((event) {
  print('Theme changed to: ${event.value}');
});
```

### 完整示例：待办事项

```dart
// todo.dart
import 'package:hive/hive.dart';

part 'todo.g.dart';

@HiveType(typeId: 0)
class Todo extends HiveObject {
  @HiveField(0)
  String id;
  
  @HiveField(1)
  String title;
  
  @HiveField(2)
  bool completed;
  
  @HiveField(3)
  DateTime createdAt;
  
  Todo({
    required this.id,
    required this.title,
    this.completed = false,
    DateTime? createdAt,
  }) : createdAt = createdAt ?? DateTime.now();
}

// todo_repository.dart
class TodoRepository {
  static const String _boxName = 'todos';
  Box<Todo>? _box;
  
  Future<void> init() async {
    _box = await Hive.openBox<Todo>(_boxName);
  }
  
  List<Todo> get all => _box?.values.toList() ?? [];
  
  List<Todo> get pending => all.where((t) => !t.completed).toList();
  
  List<Todo> get completed => all.where((t) => t.completed).toList();
  
  Future<void> add(Todo todo) async {
    await _box?.add(todo);
  }
  
  Future<void> update(Todo todo) async {
    await todo.save();
  }
  
  Future<void> delete(Todo todo) async {
    await todo.delete();
  }
  
  Future<void> deleteCompleted() async {
    final completedTodos = completed;
    for (var todo in completedTodos) {
      await todo.delete();
    }
  }
  
  Future<void> clear() async {
    await _box?.clear();
  }
}
```

## SQLite

### 添加依赖

```yaml
dependencies:
  sqflite: ^2.3.0
  path: ^1.8.0
```

### 数据库初始化

```dart
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

class DatabaseHelper {
  static final DatabaseHelper instance = DatabaseHelper._init();
  static Database? _database;
  
  DatabaseHelper._init();
  
  Future<Database> get database async {
    if (_database != null) return _database!;
    _database = await _initDB('app.db');
    return _database!;
  }
  
  Future<Database> _initDB(String fileName) async {
    final dbPath = await getDatabasesPath();
    final path = join(dbPath, fileName);
    
    return await openDatabase(
      path,
      version: 1,
      onCreate: _createDB,
      onUpgrade: _upgradeDB,
    );
  }
  
  Future<void> _createDB(Database db, int version) async {
    await db.execute('''
      CREATE TABLE users (
        id TEXT PRIMARY KEY,
        name TEXT NOT NULL,
        email TEXT NOT NULL,
        age INTEGER,
        created_at TEXT NOT NULL
      )
    ''');
    
    await db.execute('''
      CREATE TABLE posts (
        id TEXT PRIMARY KEY,
        title TEXT NOT NULL,
        content TEXT,
        user_id TEXT NOT NULL,
        created_at TEXT NOT NULL,
        FOREIGN KEY (user_id) REFERENCES users (id)
      )
    ''');
  }
  
  Future<void> _upgradeDB(Database db, int oldVersion, int newVersion) async {
    if (oldVersion < 2) {
      await db.execute('ALTER TABLE users ADD COLUMN avatar TEXT');
    }
  }
  
  Future<void> close() async {
    final db = await instance.database;
    await db.close();
  }
}
```

### CRUD 操作

```dart
class User {
  final String id;
  final String name;
  final String email;
  final int? age;
  final DateTime createdAt;
  
  User({
    required this.id,
    required this.name,
    required this.email,
    this.age,
    required this.createdAt,
  });
  
  factory User.fromMap(Map<String, dynamic> map) {
    return User(
      id: map['id'],
      name: map['name'],
      email: map['email'],
      age: map['age'],
      createdAt: DateTime.parse(map['created_at']),
    );
  }
  
  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'name': name,
      'email': email,
      'age': age,
      'created_at': createdAt.toIso8601String(),
    };
  }
}

class UserRepository {
  final DatabaseHelper _dbHelper = DatabaseHelper.instance;
  
  // 创建
  Future<void> insert(User user) async {
    final db = await _dbHelper.database;
    await db.insert(
      'users',
      user.toMap(),
      conflictAlgorithm: ConflictAlgorithm.replace,
    );
  }
  
  // 查询所有
  Future<List<User>> getAll() async {
    final db = await _dbHelper.database;
    final List<Map<String, dynamic>> maps = await db.query('users');
    return maps.map((map) => User.fromMap(map)).toList();
  }
  
  // 查询单个
  Future<User?> getById(String id) async {
    final db = await _dbHelper.database;
    final List<Map<String, dynamic>> maps = await db.query(
      'users',
      where: 'id = ?',
      whereArgs: [id],
    );
    if (maps.isEmpty) return null;
    return User.fromMap(maps.first);
  }
  
  // 更新
  Future<void> update(User user) async {
    final db = await _dbHelper.database;
    await db.update(
      'users',
      user.toMap(),
      where: 'id = ?',
      whereArgs: [user.id],
    );
  }
  
  // 删除
  Future<void> delete(String id) async {
    final db = await _dbHelper.database;
    await db.delete(
      'users',
      where: 'id = ?',
      whereArgs: [id],
    );
  }
  
  // 复杂查询
  Future<List<User>> search(String query) async {
    final db = await _dbHelper.database;
    final List<Map<String, dynamic>> maps = await db.query(
      'users',
      where: 'name LIKE ? OR email LIKE ?',
      whereArgs: ['%$query%', '%$query%'],
      orderBy: 'name ASC',
      limit: 20,
    );
    return maps.map((map) => User.fromMap(map)).toList();
  }
}
```

## 安全存储

### 添加依赖

```yaml
dependencies:
  flutter_secure_storage: ^9.0.0
```

### 基本用法

```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

class SecureStorage {
  static const _storage = FlutterSecureStorage();
  
  // 保存
  static Future<void> save(String key, String value) async {
    await _storage.write(key: key, value: value);
  }
  
  // 读取
  static Future<String?> read(String key) async {
    return await _storage.read(key: key);
  }
  
  // 删除
  static Future<void> delete(String key) async {
    await _storage.delete(key: key);
  }
  
  // 清空
  static Future<void> deleteAll() async {
    await _storage.deleteAll();
  }
  
  // 读取所有
  static Future<Map<String, String>> readAll() async {
    return await _storage.readAll();
  }
}

// 实际应用：存储认证信息
class AuthService {
  static const _keyToken = 'auth_token';
  static const _keyRefreshToken = 'refresh_token';
  static const _keyUserId = 'user_id';
  
  // 保存 Token
  Future<void> saveTokens({
    required String token,
    required String refreshToken,
    required String userId,
  }) async {
    await SecureStorage.save(_keyToken, token);
    await SecureStorage.save(_keyRefreshToken, refreshToken);
    await SecureStorage.save(_keyUserId, userId);
  }
  
  // 获取 Token
  Future<String?> getToken() => SecureStorage.read(_keyToken);
  
  // 获取刷新Token
  Future<String?> getRefreshToken() => SecureStorage.read(_keyRefreshToken);
  
  // 获取用户ID
  Future<String?> getUserId() => SecureStorage.read(_keyUserId);
  
  // 清除认证信息
  Future<void> clearAuth() async {
    await SecureStorage.delete(_keyToken);
    await SecureStorage.delete(_keyRefreshToken);
    await SecureStorage.delete(_keyUserId);
  }
  
  // 检查是否已登录
  Future<bool> isLoggedIn() async {
    final token = await getToken();
    return token != null && token.isNotEmpty;
  }
}
```

## 文件存储

### 获取存储路径

```dart
import 'dart:io';
import 'package:path_provider/path_provider.dart';

class FileStorage {
  // 应用文档目录（会被系统备份）
  Future<Directory> get documentsDir async {
    return await getApplicationDocumentsDirectory();
  }
  
  // 应用支持目录（不会被系统备份）
  Future<Directory> get supportDir async {
    return await getApplicationSupportDirectory();
  }
  
  // 临时目录（随时可能被清理）
  Future<Directory> get tempDir async {
    return await getTemporaryDirectory();
  }
}
```

### 文件读写

```dart
class FileManager {
  Future<String> get _localPath async {
    final directory = await getApplicationDocumentsDirectory();
    return directory.path;
  }
  
  Future<File> _localFile(String filename) async {
    final path = await _localPath;
    return File('$path/$filename');
  }
  
  // 写入文件
  Future<void> writeString(String filename, String content) async {
    final file = await _localFile(filename);
    await file.writeAsString(content);
  }
  
  // 读取文件
  Future<String?> readString(String filename) async {
    try {
      final file = await _localFile(filename);
      return await file.readAsString();
    } catch (e) {
      return null;
    }
  }
  
  // 写入 JSON
  Future<void> writeJson(String filename, Map<String, dynamic> data) async {
    final file = await _localFile(filename);
    await file.writeAsString(jsonEncode(data));
  }
  
  // 读取 JSON
  Future<Map<String, dynamic>?> readJson(String filename) async {
    try {
      final file = await _localFile(filename);
      final content = await file.readAsString();
      return jsonDecode(content);
    } catch (e) {
      return null;
    }
  }
  
  // 删除文件
  Future<void> delete(String filename) async {
    final file = await _localFile(filename);
    if (await file.exists()) {
      await file.delete();
    }
  }
  
  // 检查文件是否存在
  Future<bool> exists(String filename) async {
    final file = await _localFile(filename);
    return await file.exists();
  }
  
  // 获取文件大小
  Future<int> size(String filename) async {
    final file = await _localFile(filename);
    if (await file.exists()) {
      return await file.length();
    }
    return 0;
  }
}
```

### 图片缓存

```dart
class ImageCache {
  final FileManager _fileManager = FileManager();
  
  Future<File> _getCacheFile(String url) async {
    final filename = 'image_${url.hashCode}.cache';
    final path = await getTemporaryDirectory();
    return File('${path.path}/image_cache/$filename');
  }
  
  Future<File?> getCachedImage(String url) async {
    final file = await _getCacheFile(url);
    if (await file.exists()) {
      return file;
    }
    return null;
  }
  
  Future<File> cacheImage(String url, List<int> bytes) async {
    final file = await _getCacheFile(url);
    await file.parent.create(recursive: true);
    await file.writeAsBytes(bytes);
    return file;
  }
  
  Future<void> clearCache() async {
    final path = await getTemporaryDirectory();
    final cacheDir = Directory('${path.path}/image_cache');
    if (await cacheDir.exists()) {
      await cacheDir.delete(recursive: true);
    }
  }
}
```

---

*最后更新: 2026-02-19*

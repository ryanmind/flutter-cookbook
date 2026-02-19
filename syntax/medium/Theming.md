# Flutter 主题配置 - 初学者指南

## 目录
1. [ThemeData 基础](#themedata-基础)
2. [颜色系统](#颜色系统)
3. [文本样式](#文本样式)
4. [深色模式](#深色模式)
5. [Material 3 主题](#material-3-主题)
6. [主题切换](#主题切换)
7. [自定义主题](#自定义主题)

## ThemeData 基础

### MaterialApp 主题配置

```dart
MaterialApp(
  theme: ThemeData(
    // 基础配置
    useMaterial3: true,
    brightness: Brightness.light,
    
    // 颜色
    primarySwatch: Colors.blue,
    scaffoldBackgroundColor: Colors.white,
    
    // 组件主题
    appBarTheme: AppBarTheme(
      backgroundColor: Colors.blue,
      foregroundColor: Colors.white,
    ),
  ),
  darkTheme: ThemeData(
    brightness: Brightness.dark,
    // 深色主题配置
  ),
  themeMode: ThemeMode.system,  // 跟随系统
);
```

### ThemeData 主要属性

```dart
ThemeData(
  // 基础
  brightness: Brightness.light,     // 亮度
  primaryColor: Colors.blue,        // 主色
  primaryColorLight: Colors.blue[200],
  primaryColorDark: Colors.blue[700],
  
  // 背景
  scaffoldBackgroundColor: Colors.grey[100],
  canvasColor: Colors.white,
  cardColor: Colors.white,
  dividerColor: Colors.grey[300],
  
  // 文本
  textTheme: TextTheme(...),
  primaryTextTheme: TextTheme(...),
  
  // 图标
  iconTheme: IconThemeData(...),
  primaryIconTheme: IconThemeData(...),
  
  // 组件主题
  appBarTheme: AppBarTheme(...),
  buttonTheme: ButtonThemeData(...),
  cardTheme: CardTheme(...),
  inputDecorationTheme: InputDecorationTheme(...),
);
```

## 颜色系统

### ColorScheme

```dart
ThemeData(
  useMaterial3: true,
  colorScheme: ColorScheme(
    brightness: Brightness.light,
    
    // 主要颜色
    primary: Color(0xFF6750A4),           // 主色
    onPrimary: Colors.white,              // 主色上的内容色
    primaryContainer: Color(0xFFEADDFF),  // 主色容器
    onPrimaryContainer: Color(0xFF21005D),
    
    // 次要颜色
    secondary: Color(0xFF625B71),
    onSecondary: Colors.white,
    secondaryContainer: Color(0xFFE8DEF8),
    onSecondaryContainer: Color(0xFF1D192B),
    
    // 第三颜色
    tertiary: Color(0xFF7D5260),
    onTertiary: Colors.white,
    tertiaryContainer: Color(0xFFFFD8E4),
    onTertiaryContainer: Color(0xFF31111D),
    
    // 错误颜色
    error: Color(0xFFB3261E),
    onError: Colors.white,
    errorContainer: Color(0xFFF9DEDC),
    onErrorContainer: Color(0xFF410E0B),
    
    // 背景和表面
    surface: Colors.white,
    onSurface: Color(0xFF1C1B1F),
    surfaceVariant: Color(0xFFE7E0EC),
    onSurfaceVariant: Color(0xFF49454F),
    outline: Color(0xFF79747E),
    outlineVariant: Color(0xFFCAC4D0),
    
    // 反色
    inverseSurface: Color(0xFF313033),
    onInverseSurface: Color(0xFFF4EFF4),
    inversePrimary: Color(0xFFD0BCFF),
  ),
);
```

### 从种子颜色生成

```dart
// Material 3 推荐方式
ThemeData(
  useMaterial3: true,
  colorScheme: ColorScheme.fromSeed(
    seedColor: Colors.blue,  // 从单一颜色生成完整配色
    brightness: Brightness.light,
  ),
);

// 深色主题
ThemeData(
  useMaterial3: true,
  colorScheme: ColorScheme.fromSeed(
    seedColor: Colors.blue,
    brightness: Brightness.dark,
  ),
);
```

### 自定义颜色

```dart
class AppColors {
  // 品牌色
  static const Color primary = Color(0xFF2196F3);
  static const Color secondary = Color(0xFF03DAC6);
  static const Color accent = Color(0xFFFF5722);
  
  // 功能色
  static const Color success = Color(0xFF4CAF50);
  static const Color warning = Color(0xFFFFC107);
  static const Color error = Color(0xFFF44336);
  static const Color info = Color(0xFF2196F3);
  
  // 灰度
  static const Color black = Color(0xFF000000);
  static const Color gray900 = Color(0xFF212121);
  static const Color gray700 = Color(0xFF616161);
  static const Color gray500 = Color(0xFF9E9E9E);
  static const Color gray300 = Color(0xFFE0E0E0);
  static const Color gray100 = Color(0xFFF5F5F5);
  static const Color white = Color(0xFFFFFFFF);
  
  // 深色模式
  static const Color darkBackground = Color(0xFF121212);
  static const Color darkSurface = Color(0xFF1E1E1E);
  static const Color darkCard = Color(0xFF2C2C2C);
}
```

## 文本样式

### TextTheme

```dart
ThemeData(
  textTheme: TextTheme(
    // 显示样式（大标题）
    displayLarge: TextStyle(
      fontSize: 57,
      fontWeight: FontWeight.w400,
      letterSpacing: -0.25,
      height: 1.12,
    ),
    displayMedium: TextStyle(fontSize: 45, fontWeight: FontWeight.w400),
    displaySmall: TextStyle(fontSize: 36, fontWeight: FontWeight.w400),
    
    // 标题样式
    headlineLarge: TextStyle(fontSize: 32, fontWeight: FontWeight.w400),
    headlineMedium: TextStyle(fontSize: 28, fontWeight: FontWeight.w400),
    headlineSmall: TextStyle(fontSize: 24, fontWeight: FontWeight.w400),
    
    // 标题样式（较小）
    titleLarge: TextStyle(fontSize: 22, fontWeight: FontWeight.w400),
    titleMedium: TextStyle(fontSize: 16, fontWeight: FontWeight.w500),
    titleSmall: TextStyle(fontSize: 14, fontWeight: FontWeight.w500),
    
    // 正文样式
    bodyLarge: TextStyle(fontSize: 16, fontWeight: FontWeight.w400),
    bodyMedium: TextStyle(fontSize: 14, fontWeight: FontWeight.w400),
    bodySmall: TextStyle(fontSize: 12, fontWeight: FontWeight.w400),
    
    // 标签样式
    labelLarge: TextStyle(fontSize: 14, fontWeight: FontWeight.w500),
    labelMedium: TextStyle(fontSize: 12, fontWeight: FontWeight.w500),
    labelSmall: TextStyle(fontSize: 11, fontWeight: FontWeight.w500),
  ),
);
```

### 使用文本样式

```dart
class MyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // 使用预定义样式
        Text(
          '大标题',
          style: Theme.of(context).textTheme.displayLarge,
        ),
        
        // 样式组合
        Text(
          '标题',
          style: Theme.of(context).textTheme.headlineMedium?.copyWith(
            color: Theme.of(context).colorScheme.primary,
            fontWeight: FontWeight.bold,
          ),
        ),
        
        // 正文
        Text(
          '正文内容',
          style: Theme.of(context).textTheme.bodyMedium,
        ),
        
        // 标签
        Text(
          '标签',
          style: Theme.of(context).textTheme.labelLarge,
        ),
      ],
    );
  }
}
```

### 自定义字体

```yaml
# pubspec.yaml
flutter:
  fonts:
    - family: CustomFont
      fonts:
        - asset: fonts/CustomFont-Regular.ttf
        - asset: fonts/CustomFont-Bold.ttf
          weight: 700
        - asset: fonts/CustomFont-Italic.ttf
          style: italic
```

```dart
ThemeData(
  fontFamily: 'CustomFont',
  textTheme: TextTheme(
    bodyLarge: TextStyle(
      fontFamily: 'CustomFont',
      fontSize: 16,
    ),
  ),
);
```

## 深色模式

### 配置深色主题

```dart
MaterialApp(
  // 浅色主题
  theme: ThemeData(
    useMaterial3: true,
    brightness: Brightness.light,
    colorScheme: ColorScheme.fromSeed(
      seedColor: Colors.blue,
      brightness: Brightness.light,
    ),
  ),
  
  // 深色主题
  darkTheme: ThemeData(
    useMaterial3: true,
    brightness: Brightness.dark,
    colorScheme: ColorScheme.fromSeed(
      seedColor: Colors.blue,
      brightness: Brightness.dark,
    ),
  ),
  
  // 主题模式
  themeMode: ThemeMode.system,  // 跟随系统
  // themeMode: ThemeMode.light, // 强制浅色
  // themeMode: ThemeMode.dark,  // 强制深色
);
```

### 深色模式最佳实践

```dart
// 避免使用纯白色背景
ThemeData(
  darkTheme: ThemeData(
    brightness: Brightness.dark,
    // 使用深灰而非纯黑
    scaffoldBackgroundColor: Color(0xFF121212),
    cardColor: Color(0xFF1E1E1E),
    // 提升表面层次
    dialogBackgroundColor: Color(0xFF2C2C2C),
  ),
);

// 动态颜色适配
class AdaptiveColor {
  static Color background(BuildContext context) {
    return Theme.of(context).brightness == Brightness.light
        ? Colors.white
        : Color(0xFF121212);
  }
  
  static Color text(BuildContext context) {
    return Theme.of(context).brightness == Brightness.light
        ? Colors.black87
        : Colors.white70;
  }
}
```

## Material 3 主题

### 启用 Material 3

```dart
ThemeData(
  useMaterial3: true,  // 启用 Material 3
  colorScheme: ColorScheme.fromSeed(
    seedColor: Colors.blue,
  ),
);
```

### Material 3 组件样式

```dart
ThemeData(
  useMaterial3: true,
  
  // 按钮
  filledButtonTheme: FilledButtonThemeData(
    style: ButtonStyle(
      shape: WidgetStateProperty.all(
        RoundedRectangleBorder(borderRadius: BorderRadius.circular(8)),
      ),
    ),
  ),
  
  // 卡片
  cardTheme: CardTheme(
    elevation: 0,
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(12),
    ),
  ),
  
  // 输入框
  inputDecorationTheme: InputDecorationTheme(
    filled: true,
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(8),
    ),
  ),
  
  // 导航栏
  navigationBarTheme: NavigationBarThemeData(
    indicatorColor: ColorScheme.fromSeed(seedColor: Colors.blue).primaryContainer,
  ),
);
```

## 主题切换

### 使用 Provider 管理主题

```dart
// 主题 Provider
class ThemeProvider extends ChangeNotifier {
  ThemeMode _themeMode = ThemeMode.system;
  
  ThemeMode get themeMode => _themeMode;
  
  void setThemeMode(ThemeMode mode) {
    _themeMode = mode;
    notifyListeners();
  }
  
  void toggleTheme() {
    _themeMode = _themeMode == ThemeMode.light
        ? ThemeMode.dark
        : ThemeMode.light;
    notifyListeners();
  }
}

// Provider 设置
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => ThemeProvider(),
      child: MyApp(),
    ),
  );
}

// 应用
class MyApp extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final themeMode = ref.watch(themeProvider).themeMode;
    
    return MaterialApp(
      theme: ThemeData(
        useMaterial3: true,
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.blue),
      ),
      darkTheme: ThemeData(
        useMaterial3: true,
        colorScheme: ColorScheme.fromSeed(
          seedColor: Colors.blue,
          brightness: Brightness.dark,
        ),
      ),
      themeMode: themeMode,
      home: HomePage(),
    );
  }
}
```

### 主题选择界面

```dart
class ThemeSettingsPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final themeMode = ref.watch(themeProvider).themeMode;
    
    return Scaffold(
      appBar: AppBar(title: Text('主题设置')),
      body: ListView(
        children: [
          RadioListTile<ThemeMode>(
            title: Text('跟随系统'),
            value: ThemeMode.system,
            groupValue: themeMode,
            onChanged: (mode) {
              if (mode != null) {
                ref.read(themeProvider).setThemeMode(mode);
              }
            },
          ),
          RadioListTile<ThemeMode>(
            title: Text('浅色模式'),
            value: ThemeMode.light,
            groupValue: themeMode,
            onChanged: (mode) {
              if (mode != null) {
                ref.read(themeProvider).setThemeMode(mode);
              }
            },
          ),
          RadioListTile<ThemeMode>(
            title: Text('深色模式'),
            value: ThemeMode.dark,
            groupValue: themeMode,
            onChanged: (mode) {
              if (mode != null) {
                ref.read(themeProvider).setThemeMode(mode);
              }
            },
          ),
        ],
      ),
    );
  }
}
```

### 持久化主题设置

```dart
class ThemeProvider extends ChangeNotifier {
  static const _key = 'theme_mode';
  
  final SharedPreferences _prefs;
  ThemeMode _themeMode;
  
  ThemeProvider(this._prefs) : _themeMode = _loadThemeMode(_prefs);
  
  ThemeMode get themeMode => _themeMode;
  
  static ThemeMode _loadThemeMode(SharedPreferences prefs) {
    final index = prefs.getInt(_key) ?? 0;
    return ThemeMode.values[index];
  }
  
  void setThemeMode(ThemeMode mode) {
    _themeMode = mode;
    _prefs.setInt(_key, mode.index);
    notifyListeners();
  }
}
```

## 自定义主题

### 主题扩展

```dart
// 定义扩展类
extension ThemeExtensions on ThemeData {
  // 自定义颜色
  Color get successColor => Color(0xFF4CAF50);
  Color get warningColor => Color(0xFFFFC107);
  
  // 自定义间距
  double get spacingXS => 4.0;
  double get spacingS => 8.0;
  double get spacingM => 16.0;
  double get spacingL => 24.0;
  double get spacingXL => 32.0;
  
  // 自定义圆角
  BorderRadius get radiusS => BorderRadius.circular(4);
  BorderRadius get radiusM => BorderRadius.circular(8);
  BorderRadius get radiusL => BorderRadius.circular(16);
  
  // 自定义阴影
  List<BoxShadow> get shadowS => [
    BoxShadow(
      color: Colors.black.withValues(alpha: 0.1),
      blurRadius: 4,
      offset: Offset(0, 2),
    ),
  ];
  
  List<BoxShadow> get shadowM => [
    BoxShadow(
      color: Colors.black.withValues(alpha: 0.15),
      blurRadius: 8,
      offset: Offset(0, 4),
    ),
  ];
}

// 使用
Container(
  padding: EdgeInsets.all(Theme.of(context).spacingM),
  decoration: BoxDecoration(
    color: Theme.of(context).cardColor,
    borderRadius: Theme.of(context).radiusM,
    boxShadow: Theme.of(context).shadowS,
  ),
  child: Text('Hello'),
);
```

### 完整主题配置示例

```dart
class AppTheme {
  // 种子颜色
  static const Color _seedColor = Color(0xFF6750A4);
  
  // 浅色主题
  static ThemeData get light {
    return ThemeData(
      useMaterial3: true,
      brightness: Brightness.light,
      colorScheme: ColorScheme.fromSeed(
        seedColor: _seedColor,
        brightness: Brightness.light,
      ),
      
      // 脚手架
      scaffoldBackgroundColor: Color(0xFFFAFAFA),
      
      // AppBar
      appBarTheme: AppBarTheme(
        centerTitle: true,
        elevation: 0,
        scrolledUnderElevation: 1,
      ),
      
      // 卡片
      cardTheme: CardTheme(
        elevation: 0,
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
          side: BorderSide(color: Colors.grey.shade200),
        ),
      ),
      
      // 输入框
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        fillColor: Colors.grey.shade50,
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: BorderSide.none,
        ),
        enabledBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: BorderSide(color: Colors.grey.shade300),
        ),
        focusedBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: BorderSide(color: _seedColor, width: 2),
        ),
      ),
      
      // 按钮
      filledButtonTheme: FilledButtonThemeData(
        style: ButtonStyle(
          shape: WidgetStateProperty.all(
            RoundedRectangleBorder(borderRadius: BorderRadius.circular(8)),
          ),
        ),
      ),
      outlinedButtonTheme: OutlinedButtonThemeData(
        style: ButtonStyle(
          shape: WidgetStateProperty.all(
            RoundedRectangleBorder(borderRadius: BorderRadius.circular(8)),
          ),
        ),
      ),
      
      // 底部导航
      navigationBarTheme: NavigationBarThemeData(
        elevation: 0,
      ),
    );
  }
  
  // 深色主题
  static ThemeData get dark {
    return ThemeData(
      useMaterial3: true,
      brightness: Brightness.dark,
      colorScheme: ColorScheme.fromSeed(
        seedColor: _seedColor,
        brightness: Brightness.dark,
      ),
      
      scaffoldBackgroundColor: Color(0xFF121212),
      
      appBarTheme: AppBarTheme(
        centerTitle: true,
        elevation: 0,
      ),
      
      cardTheme: CardTheme(
        elevation: 0,
        color: Color(0xFF1E1E1E),
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
        ),
      ),
      
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        fillColor: Color(0xFF2C2C2C),
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: BorderSide.none,
        ),
      ),
    );
  }
}

// 使用
MaterialApp(
  theme: AppTheme.light,
  darkTheme: AppTheme.dark,
  themeMode: ThemeMode.system,
);
```

---

*最后更新: 2026-02-19*

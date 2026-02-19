# Flutter Center 组件用法详解

## 简介

Center 是 Flutter 中最简单常用的对齐组件，用于将子组件居中显示。它是 Align 组件的一个特化版本，专门用于居中对齐场景。

## 1. 初级用法

### 1.1 基本概念

Center 组件的作用：
- 将子组件在父容器中水平和垂直居中
- 可以通过因子控制自身尺寸
- 是 `Align(alignment: Alignment.center)` 的简写形式

### 1.2 基本语法

```dart
Center(
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `widthFactor` | `double?` | 宽度因子，相对于子组件宽度 |
| `heightFactor` | `double?` | 高度因子，相对于子组件高度 |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 基本居中

```dart
Container(
  width: 200,
  height: 200,
  color: Colors.grey[200],
  child: Center(
    child: Container(
      width: 80,
      height: 80,
      color: Colors.blue,
    ),
  ),
)
```

#### 文字居中

```dart
Container(
  height: 100,
  width: double.infinity,
  color: Colors.blue,
  child: Center(
    child: Text(
      '居中文字',
      style: TextStyle(color: Colors.white, fontSize: 24),
    ),
  ),
)
```

## 2. 中级用法

### 2.1 宽高因子

`widthFactor` 和 `heightFactor` 让 Center 根据子组件尺寸计算自身大小：

```dart
// Center 宽度为子组件的 2 倍
Center(
  widthFactor: 2,
  child: Container(
    width: 50,
    height: 50,
    color: Colors.red,
  ),
)
// Center 实际尺寸: 100 x 50
```

```dart
// 同时设置宽高因子
Center(
  widthFactor: 2,
  heightFactor: 2,
  child: Container(
    width: 60,
    height: 60,
    color: Colors.green,
  ),
)
// Center 实际尺寸: 120 x 120
```

### 2.2 在列表中使用

```dart
ListView(
  children: [
    Container(height: 100, color: Colors.red),
    Center(
      child: Text('加载中...'),
    ),
    Container(height: 100, color: Colors.blue),
  ],
)
```

### 2.3 空状态页面

```dart
Center(
  child: Column(
    mainAxisSize: MainAxisSize.min,
    children: [
      Icon(Icons.inbox, size: 64, color: Colors.grey),
      SizedBox(height: 16),
      Text('暂无数据', style: TextStyle(color: Colors.grey)),
    ],
  ),
)
```

### 2.4 加载指示器

```dart
Center(
  child: CircularProgressIndicator(),
)
```

### 2.5 常见布局场景

#### 全屏居中

```dart
Scaffold(
  body: Center(
    child: Text('内容居中'),
  ),
)
```

#### 错误页面

```dart
Center(
  child: Column(
    mainAxisSize: MainAxisSize.min,
    children: [
      Icon(Icons.error_outline, size: 64, color: Colors.red),
      SizedBox(height: 16),
      Text('加载失败'),
      SizedBox(height: 16),
      ElevatedButton(
        onPressed: () {},
        child: Text('重试'),
      ),
    ],
  ),
)
```

## 3. 高级用法

### 3.1 嵌套布局

```dart
Center(
  child: Container(
    width: 300,
    height: 200,
    decoration: BoxDecoration(
      color: Colors.blue,
      borderRadius: BorderRadius.circular(12),
    ),
    child: Center(
      child: Text(
        '嵌套居中',
        style: TextStyle(color: Colors.white),
      ),
    ),
  ),
)
```

### 3.2 响应式布局

```dart
LayoutBuilder(
  builder: (context, constraints) {
    return Center(
      child: Container(
        width: constraints.maxWidth > 600 ? 400 : constraints.maxWidth * 0.8,
        padding: EdgeInsets.all(16),
        child: Card(
          child: Padding(
            padding: EdgeInsets.all(16),
            child: Text('响应式卡片'),
          ),
        ),
      ),
    );
  },
)
```

### 3.3 配合 Expanded

```dart
Row(
  children: [
    Container(width: 80, color: Colors.red),
    Expanded(
      child: Center(
        child: Text('中间居中内容'),
      ),
    ),
    Container(width: 80, color: Colors.blue),
  ],
)
```

### 3.4 欢迎页面

```dart
class WelcomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Padding(
          padding: EdgeInsets.all(32),
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              FlutterLogo(size: 100),
              SizedBox(height: 32),
              Text(
                '欢迎',
                style: Theme.of(context).textTheme.headlineMedium,
              ),
              SizedBox(height: 16),
              Text(
                '开始你的 Flutter 之旅',
                style: Theme.of(context).textTheme.bodyMedium,
              ),
              SizedBox(height: 32),
              SizedBox(
                width: double.infinity,
                child: ElevatedButton(
                  onPressed: () {},
                  child: Text('开始'),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：Center 没有效果

**原因**：父容器没有约束，或子组件已经填满父容器。

```dart
// 问题：Column 中直接使用 Center
Column(
  children: [
    Center(child: Text('居中?')),
  ],
)

// 解决方案1：给 Center 外层加约束
Column(
  children: [
    SizedBox(
      width: double.infinity,
      height: 100,
      child: Center(child: Text('居中')),
    ),
  ],
)

// 解决方案2：使用 Expanded
Column(
  children: [
    Expanded(
      child: Center(child: Text('居中')),
    ),
  ],
)
```

### 问题2：子组件太大

**原因**：子组件尺寸超过父容器，无法居中显示。

```dart
// 问题
Container(
  width: 100,
  height: 100,
  child: Center(
    child: Container(
      width: 200,  // 超出父容器
      height: 200,
      color: Colors.red,
    ),
  ),
)

// 解决方案：使用 FittedBox 自动缩放
Container(
  width: 100,
  height: 100,
  child: FittedBox(
    child: Container(
      width: 200,
      height: 200,
      color: Colors.red,
    ),
  ),
)
```

### 问题3：只水平或只垂直居中

Center 会同时居中，如果只需要单向居中：

```dart
// 只水平居中
Align(
  alignment: Alignment.center,
  heightFactor: 1,
  child: Text('水平居中'),
)

// 或使用 Row
Row(
  mainAxisAlignment: MainAxisAlignment.center,
  children: [Text('水平居中')],
)

// 只垂直居中
Align(
  alignment: Alignment.center,
  widthFactor: 1,
  child: Text('垂直居中'),
)

// 或使用 Column
Column(
  mainAxisAlignment: MainAxisAlignment.center,
  children: [Text('垂直居中')],
)
```

## 5. Center vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **Center** | 居中对齐 | 最简洁，专用于居中 |
| **Align** | 任意对齐 | 可自定义对齐位置 |
| **Container** | 综合容器 | 可设置对齐、边距、装饰 |
| **FittedBox** | 缩放适配 | 会缩放子组件 |

### 选择建议

```dart
// 只需居中 → 使用 Center
Center(child: Text('居中'))

// 需要其他对齐位置 → 使用 Align
Align(
  alignment: Alignment.topLeft,
  child: Text('左上角'),
)

// 需要居中+装饰+边距 → 使用 Container
Container(
  alignment: Alignment.center,
  padding: EdgeInsets.all(16),
  decoration: BoxDecoration(color: Colors.blue),
  child: Text('复杂需求'),
)
```

## 6. 最佳实践

### 6.1 空状态模板

```dart
class EmptyState extends StatelessWidget {
  final IconData icon;
  final String message;
  final String? actionText;
  final VoidCallback? onAction;

  const EmptyState({
    required this.icon,
    required this.message,
    this.actionText,
    this.onAction,
  });

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Padding(
        padding: EdgeInsets.all(32),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Icon(icon, size: 64, color: Colors.grey),
            SizedBox(height: 16),
            Text(
              message,
              style: TextStyle(color: Colors.grey[600]),
              textAlign: TextAlign.center,
            ),
            if (actionText != null) ...[
              SizedBox(height: 24),
              ElevatedButton(
                onPressed: onAction,
                child: Text(actionText!),
              ),
            ],
          ],
        ),
      ),
    );
  }
}
```

### 6.2 加载状态

```dart
class LoadingState extends StatelessWidget {
  final String? message;

  const LoadingState({this.message});

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          CircularProgressIndicator(),
          if (message != null) ...[
            SizedBox(height: 16),
            Text(message!),
          ],
        ],
      ),
    );
  }
}
```

### 6.3 性能优化

```dart
// 静态内容使用 const
const Center(
  child: Text('静态文字'),
)
```

## 总结

Center 是 Flutter 中最常用的布局组件之一：

**核心要点**：
1. 简单易用，专用于居中对齐
2. 通过 `widthFactor` / `heightFactor` 控制尺寸
3. 等价于 `Align(alignment: Alignment.center)`

**最佳实践**：
1. 空状态、加载状态使用 Center
2. 配合 `mainAxisSize: MainAxisSize.min` 避免不必要的扩展
3. 静态内容使用 `const` 构造函数

**常见场景**：
- 空状态页面
- 加载指示器
- 错误页面
- 欢迎页面
- 对话框内容

---

*最后更新: 2026-02-19*

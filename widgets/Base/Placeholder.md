# Flutter Placeholder 组件用法详解

## 简介

Placeholder 是 Flutter 中用于在开发阶段占位的组件。它显示一个带有 "X" 标记的占位框，帮助开发者标识尚未实现的 UI 部分。

## 1. 初级用法

### 1.1 基本概念

Placeholder 的核心作用：
- 开发阶段的临时占位
- 标识尚未实现的 UI 区域
- 可配置颜色和线条粗细

### 1.2 基本语法

```dart
Placeholder()
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `color` | `Color` | `Color(0xFF455A64)` | 线条颜色 |
| `strokeWidth` | `double` | `2.0` | 线条宽度 |
| `fallbackWidth` | `double` | `400.0` | 无约束时的默认宽度 |
| `fallbackHeight` | `double` | `400.0` | 无约束时的默认高度 |
| `child` | `Widget?` | - | 子组件（会覆盖占位符） |

### 1.4 基础示例

#### 基本占位符

```dart
Placeholder()
```

#### 指定颜色和线宽

```dart
Placeholder(
  color: Colors.blue,
  strokeWidth: 3,
)
```

#### 设置默认尺寸

```dart
Placeholder(
  fallbackWidth: 200,
  fallbackHeight: 100,
)
```

## 2. 中级用法

### 2.1 布局占位

```dart
Column(
  children: [
    Container(
      height: 60,
      color: Colors.blue,
      child: Center(child: Text('Header')),
    ),
    Expanded(
      child: Placeholder(),  // 内容区域占位
    ),
    Container(
      height: 60,
      color: Colors.green,
      child: Center(child: Text('Footer')),
    ),
  ],
)
```

### 2.2 卡片占位

```dart
Card(
  child: SizedBox(
    width: 200,
    height: 150,
    child: Placeholder(
      color: Colors.grey,
      strokeWidth: 1,
    ),
  ),
)
```

### 2.3 图片占位

```dart
Container(
  width: 150,
  height: 150,
  child: Placeholder(
    color: Colors.grey[400]!,
    strokeWidth: 1,
  ),
)
```

### 2.4 响应式占位

```dart
LayoutBuilder(
  builder: (context, constraints) {
    return Placeholder(
      fallbackWidth: constraints.maxWidth,
      fallbackHeight: constraints.maxHeight,
    );
  },
)
```

### 2.5 带文字的占位

```dart
Stack(
  children: [
    Placeholder(color: Colors.grey),
    Center(
      child: Text(
        '待实现区域',
        style: TextStyle(color: Colors.grey[600]),
      ),
    ),
  ],
)
```

## 3. 高级用法

### 3.1 条件占位

```dart
class ConditionalPlaceholder extends StatelessWidget {
  final bool isImplemented;
  final Widget child;
  final String? label;

  const ConditionalPlaceholder({
    required this.isImplemented,
    required this.child,
    this.label,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    if (isImplemented) {
      return child;
    }
    return Stack(
      children: [
        Placeholder(color: Colors.orange),
        Center(
          child: Text(
            label ?? 'Coming Soon',
            style: TextStyle(color: Colors.orange),
          ),
        ),
      ],
    );
  }
}
```

### 3.2 开发模式占位

```dart
class DevPlaceholder extends StatelessWidget {
  final Widget child;
  final String feature;

  const DevPlaceholder({
    required this.child,
    required this.feature,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    if (kDebugMode) {
      return Stack(
        children: [
          Placeholder(color: Colors.red.withOpacity(0.3)),
          child,
        ],
      );
    }
    return child;
  }
}
```

### 3.3 骨架屏效果

```dart
class SkeletonPlaceholder extends StatefulWidget {
  final double width;
  final double height;

  const SkeletonPlaceholder({
    required this.width,
    required this.height,
    Key? key,
  }) : super(key: key);

  @override
  State<SkeletonPlaceholder> createState() => _SkeletonPlaceholderState();
}

class _SkeletonPlaceholderState extends State<SkeletonPlaceholder>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 1500),
      vsync: this,
    )..repeat();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _controller,
      builder: (context, child) {
        return Container(
          width: widget.width,
          height: widget.height,
          decoration: BoxDecoration(
            color: Colors.grey[300],
            borderRadius: BorderRadius.circular(4),
            gradient: LinearGradient(
              begin: Alignment.topLeft,
              end: Alignment.bottomRight,
              colors: [
                Colors.grey[300]!,
                Colors.grey[200]!,
                Colors.grey[300]!,
              ],
              stops: [
                0.0,
                _controller.value,
                1.0,
              ],
            ),
          ),
        );
      },
    );
  }
}
```

### 3.4 多区域占位

```dart
class LayoutPlaceholder extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // Header 占位
        Container(
          height: 56,
          child: Placeholder(
            color: Colors.blue,
            strokeWidth: 1,
            fallbackHeight: 56,
          ),
        ),
        Expanded(
          child: Row(
            children: [
              // Sidebar 占位
              Container(
                width: 200,
                child: Placeholder(
                  color: Colors.green,
                  strokeWidth: 1,
                ),
              ),
              // Main content 占位
              Expanded(
                child: Placeholder(
                  color: Colors.purple,
                  strokeWidth: 1,
                ),
              ),
            ],
          ),
        ),
      ],
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：占位符大小不符合预期

```dart
// 在无约束环境中使用 fallbackWidth/fallbackHeight
ListView(
  children: [
    Placeholder(
      fallbackWidth: 200,
      fallbackHeight: 100,
    ),
  ],
)

// 或使用 SizedBox 约束
SizedBox(
  width: 200,
  height: 100,
  child: Placeholder(),
)
```

### 问题2：颜色不明显

```dart
// 根据背景色调整占位符颜色
Container(
  color: Colors.grey[200],
  child: Placeholder(
    color: Colors.red,  // 使用对比色
    strokeWidth: 2,
  ),
)
```

### 问题3：生产环境误用

```dart
// 使用 kDebugMode 避免生产环境显示
if (kDebugMode) {
  return Placeholder();
} else {
  return Container();  // 或其他替代内容
}
```

## 5. Placeholder vs 其他占位方式

| 方式 | 用途 | 特点 |
|------|------|------|
| **Placeholder** | 开发占位 | 明显的 X 标记 |
| **Container** | 空占位 | 无视觉提示 |
| **SizedBox** | 尺寸占位 | 无内容 |
| **CircularProgressIndicator** | 加载占位 | 动态加载效果 |

### 选择建议

```dart
// 开发阶段 → Placeholder
Placeholder()

// 需要指定尺寸 → SizedBox + Placeholder
SizedBox(width: 200, height: 100, child: Placeholder())

// 加载状态 → CircularProgressIndicator
CircularProgressIndicator()

// 生产环境空位 → Container 或 SizedBox
SizedBox(height: 100)
```

## 6. 最佳实践

### 6.1 开发辅助组件

```dart
class NotImplemented extends StatelessWidget {
  final String feature;

  const NotImplemented(this.feature, {Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Placeholder(color: Colors.orange),
        Center(
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              Icon(Icons.construction, color: Colors.orange, size: 32),
              SizedBox(height: 8),
              Text(
                '$feature\n待实现',
                textAlign: TextAlign.center,
                style: TextStyle(color: Colors.orange),
              ),
            ],
          ),
        ),
      ],
    );
  }
}
```

### 6.2 区域标识

```dart
// 使用不同颜色标识不同区域
Column(
  children: [
    Placeholder(color: Colors.blue, fallbackHeight: 60),    // Header
    Placeholder(color: Colors.green, fallbackHeight: 100),  // Content
    Placeholder(color: Colors.purple, fallbackHeight: 60),  // Footer
  ],
)
```

### 6.3 移除占位符

```dart
// 在发布前移除所有 Placeholder
// 或使用 debugOnly 标志

class DebugPlaceholder extends StatelessWidget {
  final double? width;
  final double? height;

  const DebugPlaceholder({this.width, this.height, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    if (kReleaseMode) {
      return SizedBox(width: width, height: height);
    }
    return SizedBox(
      width: width,
      height: height,
      child: Placeholder(strokeWidth: 1),
    );
  }
}
```

## 总结

Placeholder 是开发阶段的有用工具：

**核心要点**：
1. 显示带有 "X" 标记的占位框
2. 可配置颜色、线宽和默认尺寸
3. 仅用于开发阶段

**最佳实践**：
1. 配合 kDebugMode 使用
2. 使用不同颜色区分区域
3. 发布前移除或替换

**常见场景**：
- 布局占位
- 未实现功能标识
- UI 原型快速搭建

---

*最后更新: 2026-02-19*

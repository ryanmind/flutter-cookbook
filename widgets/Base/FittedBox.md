# Flutter FittedBox 组件用法详解

## 简介

FittedBox 是 Flutter 中用于缩放和定位子组件的布局组件。它会根据父容器约束自动缩放子组件，确保子组件完全显示在指定区域内。

## 1. 初级用法

### 1.1 基本概念

FittedBox 的作用：
- 自动缩放子组件以适应父容器
- 控制子组件的对齐方式
- 支持裁剪超出内容

### 1.2 基本语法

```dart
FittedBox(
  child: Container(
    width: 200,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `fit` | `BoxFit` | 缩放模式 |
| `alignment` | `AlignmentGeometry` | 对齐方式 |
| `clipBehavior` | `Clip` | 裁剪行为 |
| `child` | `Widget?` | 子组件 |

### 1.4 BoxFit 枚举

| 值 | 说明 |
|------|------|
| `fill` | 填充整个容器，可能变形 |
| `contain` | 完整显示，保持比例 |
| `cover` | 填满容器，保持比例，可能裁剪 |
| `fitWidth` | 宽度填满，保持比例 |
| `fitHeight` | 高度填满，保持比例 |
| `none` | 不缩放 |
| `scaleDown` | 只缩小不放大 |

### 1.5 基础示例

#### 基本缩放

```dart
Container(
  width: 150,
  height: 100,
  color: Colors.grey[200],
  child: FittedBox(
    child: Container(
      width: 300,  // 大于父容器
      height: 200,
      color: Colors.blue,
    ),
  ),
)
```

#### 对齐控制

```dart
FittedBox(
  alignment: Alignment.bottomRight,
  child: Container(
    width: 50,
    height: 50,
    color: Colors.red,
  ),
)
```

## 2. 中级用法

### 2.1 BoxFit 效果对比

```dart
class BoxFitDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GridView.count(
      crossAxisCount: 4,
      children: BoxFit.values.map((fit) {
        return Column(
          children: [
            Container(
              width: 80,
              height: 60,
              color: Colors.grey[200],
              child: FittedBox(
                fit: fit,
                child: Container(
                  width: 100,
                  height: 100,
                  color: Colors.blue,
                  child: Center(child: Text('1:1')),
                ),
              ),
            ),
            SizedBox(height: 4),
            Text(fit.name, style: TextStyle(fontSize: 10)),
          ],
        );
      }).toList(),
    );
  }
}
```

### 2.2 常见布局场景

#### 图片适配

```dart
Container(
  width: 200,
  height: 150,
  color: Colors.grey[200],
  child: FittedBox(
    fit: BoxFit.cover,
    child: Image.network('https://picsum.photos/400'),
  ),
)
```

#### 文字缩放

```dart
FittedBox(
  child: Text(
    '这是一段很长的文字会被自动缩放',
    style: TextStyle(fontSize: 24),
  ),
)
```

#### 图标适配

```dart
Container(
  width: 100,
  height: 100,
  color: Colors.grey[200],
  child: FittedBox(
    child: Icon(Icons.star, size: 200, color: Colors.yellow),
  ),
)
```

#### 卡片内容适配

```dart
Card(
  child: SizedBox(
    width: 200,
    height: 150,
    child: Column(
      children: [
        Expanded(
          child: FittedBox(
            fit: BoxFit.cover,
            child: Image.network('https://picsum.photos/300'),
          ),
        ),
        Padding(
          padding: EdgeInsets.all(8),
          child: FittedBox(
            child: Text(
              '标题文字',
              style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
            ),
          ),
        ),
      ],
    ),
  ),
)
```

### 2.3 裁剪超出内容

```dart
Container(
  width: 100,
  height: 100,
  color: Colors.grey[200],
  child: FittedBox(
    fit: BoxFit.cover,
    clipBehavior: Clip.hardEdge,
    child: Container(
      width: 200,
      height: 200,
      color: Colors.blue,
    ),
  ),
)
```

## 3. 高级用法

### 3.1 响应式文字

```dart
class ResponsiveText extends StatelessWidget {
  final String text;
  final double baseFontSize;

  const ResponsiveText({
    required this.text,
    this.baseFontSize = 16,
  });

  @override
  Widget build(BuildContext context) {
    return FittedBox(
      child: Text(
        text,
        style: TextStyle(fontSize: baseFontSize),
      ),
    );
  }
}

// 使用
Container(
  width: 100,
  height: 30,
  color: Colors.grey[200],
  child: ResponsiveText('这段文字会自动缩放'),
)
```

### 3.2 动态容器

```dart
class DynamicFittedBox extends StatefulWidget {
  @override
  State<DynamicFittedBox> createState() => _DynamicFittedBoxState();
}

class _DynamicFittedBoxState extends State<DynamicFittedBox> {
  double _size = 100;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Slider(
          value: _size,
          min: 50,
          max: 200,
          onChanged: (value) => setState(() => _size = value),
        ),
        Container(
          width: _size,
          height: _size,
          color: Colors.grey[200],
          child: FittedBox(
            child: Container(
              width: 150,
              height: 150,
              color: Colors.blue,
              child: Center(child: Text('内容')),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.3 多组件缩放

```dart
FittedBox(
  child: Row(
    children: [
      Container(width: 100, height: 100, color: Colors.red),
      Container(width: 100, height: 100, color: Colors.green),
      Container(width: 100, height: 100, color: Colors.blue),
    ],
  ),
)
```

### 3.4 保持比例缩放

```dart
class AspectRatioContainer extends StatelessWidget {
  final double aspectRatio;
  final Widget child;

  const AspectRatioContainer({
    required this.aspectRatio,
    required this.child,
  });

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final width = constraints.maxWidth;
        final height = width / aspectRatio;
        
        return SizedBox(
          height: height,
          child: FittedBox(
            fit: BoxFit.fitWidth,
            child: child,
          ),
        );
      },
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：文字模糊

```dart
// 问题：缩放后文字模糊
FittedBox(
  child: Text('文字'),  // 缩小后可能模糊
)

// 解决：使用适当的字体大小
FittedBox(
  child: Text(
    '文字',
    style: TextStyle(fontSize: 100),  // 大字体缩小更清晰
  ),
)
```

### 问题2：变形问题

```dart
// 问题：BoxFit.fill 导致变形
FittedBox(
  fit: BoxFit.fill,  // 会变形
  child: Container(...),
)

// 解决：使用 BoxFit.contain 或 cover
FittedBox(
  fit: BoxFit.contain,  // 保持比例
  child: Container(...),
)
```

### 问题3：无限约束

```dart
// 问题：FittedBox 在无限约束中无法工作
Column(
  children: [
    FittedBox(child: Container(...)),  // 高度无限
  ],
)

// 解决：使用 SizedBox 或 Expanded
Column(
  children: [
    SizedBox(
      height: 100,
      child: FittedBox(child: Container(...)),
    ),
  ],
)
```

## 5. FittedBox vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **FittedBox** | 自动缩放 | 基于约束缩放 |
| **AspectRatio** | 固定比例 | 基于比例计算尺寸 |
| **Transform.scale** | 手动缩放 | 固定缩放比例 |
| **SizedBox** | 固定尺寸 | 不自动适配 |

### 选择建议

```dart
// 需要自动缩放适应 → FittedBox
FittedBox(child: Text('自动缩放'))

// 需要保持比例 → AspectRatio
AspectRatio(aspectRatio: 16/9, child: ...)

// 需要固定缩放 → Transform.scale
Transform.scale(scale: 0.5, child: ...)

// 需要固定尺寸 → SizedBox
SizedBox(width: 100, height: 100, child: ...)
```

## 6. 最佳实践

### 6.1 封装自适应文字

```dart
class AutoSizeText extends StatelessWidget {
  final String text;
  final TextStyle? style;
  final TextAlign? textAlign;
  final BoxFit fit;

  const AutoSizeText({
    required this.text,
    this.style,
    this.textAlign,
    this.fit = BoxFit.scaleDown,
  });

  @override
  Widget build(BuildContext context) {
    return FittedBox(
      fit: fit,
      child: Text(
        text,
        style: style,
        textAlign: textAlign,
      ),
    );
  }
}
```

### 6.2 图标适配

```dart
class AdaptiveIcon extends StatelessWidget {
  final IconData icon;
  final Color? color;
  final double minSize;

  const AdaptiveIcon({
    required this.icon,
    this.color,
    this.minSize = 24,
  });

  @override
  Widget build(BuildContext context) {
    return FittedBox(
      child: Icon(icon, size: minSize, color: color),
    );
  }
}
```

### 6.3 注意性能

```dart
// FittedBox 需要计算缩放，有性能开销
// 对于静态内容，考虑使用固定尺寸

// 不推荐：频繁重建
AnimatedBuilder(
  animation: animation,
  builder: (context, child) {
    return FittedBox(child: Text('${animation.value}'));
  },
)

// 推荐：使用 AnimatedSize 或固定缩放
AnimatedBuilder(
  animation: animation,
  builder: (context, child) {
    return Transform.scale(
      scale: animation.value,
      child: Text('内容'),
    );
  },
)
```

## 总结

FittedBox 是 Flutter 中自动缩放子组件的实用工具：

**核心要点**：
1. BoxFit 控制缩放模式
2. alignment 控制对齐方式
3. clipBehavior 控制裁剪

**最佳实践**：
1. 文字缩放用大字体缩小
2. 需要保持比例用 contain/cover
3. 静态内容考虑固定尺寸

**常见场景**：
- 自适应文字
- 图片适配
- 图标缩放
- 卡片内容

---

*最后更新: 2026-02-19*

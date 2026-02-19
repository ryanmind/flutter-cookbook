# Flutter Baseline 组件用法详解

## 简介

Baseline 是 Flutter 中用于根据基线对齐子组件的布局组件。它可以将子组件的文本基线对齐到指定位置，常用于不同大小文本的对齐场景。

## 1. 初级用法

### 1.1 基本概念

Baseline 的核心作用：
- 根据文本基线对齐子组件
- 支持字母基线(Alphabetic)和表意基线(Ideographic)
- 解决不同字号文本对齐问题

### 1.2 基本语法

```dart
Baseline(
  baseline: 30,
  baselineType: TextBaseline.alphabetic,
  child: Text(
    'Hello',
    style: TextStyle(fontSize: 24),
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `baseline` | `double` | 基线距离顶部的距离 |
| `baselineType` | `TextBaseline` | 基线类型 |
| `child` | `Widget?` | 子组件 |

### 1.4 基线类型

```dart
// 字母基线 - 用于拉丁字母
TextBaseline.alphabetic

// 表意基线 - 用于中文、日文等
TextBaseline.ideographic
```

### 1.5 基础示例

#### 不同字号对齐

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [
    Text(
      'Big',
      style: TextStyle(fontSize: 32),
    ),
    Text(
      'Small',
      style: TextStyle(fontSize: 14),
    ),
  ],
)
```

#### 使用 Baseline 组件

```dart
Container(
  height: 60,
  color: Colors.grey[200],
  child: Baseline(
    baseline: 40,
    baselineType: TextBaseline.alphabetic,
    child: Text('Aligned to baseline'),
  ),
)
```

## 2. 中级用法

### 2.1 多组件基线对齐

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [
    Baseline(
      baseline: 30,
      baselineType: TextBaseline.alphabetic,
      child: Text('ABC', style: TextStyle(fontSize: 40)),
    ),
    Baseline(
      baseline: 30,
      baselineType: TextBaseline.alphabetic,
      child: Text('def', style: TextStyle(fontSize: 16)),
    ),
    Baseline(
      baseline: 30,
      baselineType: TextBaseline.alphabetic,
      child: Text('GHI', style: TextStyle(fontSize: 24)),
    ),
  ],
)
```

### 2.2 价格显示

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [
    Text(
      '¥',
      style: TextStyle(fontSize: 16),
    ),
    Text(
      '99',
      style: TextStyle(fontSize: 48, fontWeight: FontWeight.bold),
    ),
    Text(
      '.99',
      style: TextStyle(fontSize: 20),
    ),
  ],
)
```

### 2.3 中文文本基线

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.ideographic,
  children: [
    Text('中文', style: TextStyle(fontSize: 32)),
    Text('对齐', style: TextStyle(fontSize: 16)),
    Text('示例', style: TextStyle(fontSize: 24)),
  ],
)
```

### 2.4 垂直居中基线对齐

```dart
Container(
  height: 100,
  color: Colors.grey[200],
  child: Center(
    child: Row(
      crossAxisAlignment: CrossAxisAlignment.baseline,
      textBaseline: TextBaseline.alphabetic,
      children: [
        Text('Large', style: TextStyle(fontSize: 40)),
        SizedBox(width: 8),
        Text('Small', style: TextStyle(fontSize: 16)),
      ],
    ),
  ),
)
```

## 3. 高级用法

### 3.1 混合内容对齐

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [
    Icon(Icons.star, size: 32),
    SizedBox(width: 4),
    Text('4.5', style: TextStyle(fontSize: 32, fontWeight: FontWeight.bold)),
    SizedBox(width: 4),
    Text('(1,234 reviews)', style: TextStyle(fontSize: 14, color: Colors.grey)),
  ],
)
```

### 3.2 数学公式显示

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [
    Text('x', style: TextStyle(fontSize: 24)),
    Transform.translate(
      offset: Offset(0, -8),
      child: Text('2', style: TextStyle(fontSize: 14)),
    ),
    Text(' + y', style: TextStyle(fontSize: 24)),
    Transform.translate(
      offset: Offset(0, -8),
      child: Text('2', style: TextStyle(fontSize: 14)),
    ),
    Text(' = z', style: TextStyle(fontSize: 24)),
  ],
)
```

### 3.3 自定义基线组件

```dart
class BaselineText extends StatelessWidget {
  final String text;
  final TextStyle style;
  final double baseline;

  const BaselineText({
    required this.text,
    required this.style,
    required this.baseline,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Baseline(
      baseline: baseline,
      baselineType: TextBaseline.alphabetic,
      child: Text(text, style: style),
    );
  }
}

// 使用
Row(
  children: [
    BaselineText(text: 'Hello', style: TextStyle(fontSize: 32), baseline: 30),
    BaselineText(text: 'World', style: TextStyle(fontSize: 16), baseline: 30),
  ],
)
```

### 3.4 响应式基线

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final baseline = constraints.maxHeight * 0.8;
    return Container(
      height: constraints.maxHeight,
      child: Baseline(
        baseline: baseline,
        baselineType: TextBaseline.alphabetic,
        child: Text('Responsive baseline'),
      ),
    );
  },
)
```

## 4. 常见问题与解决方案

### 问题1：基线对齐无效

```dart
// ❌ 错误：Row 未设置 textBaseline
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  children: [
    Text('A', style: TextStyle(fontSize: 32)),
    Text('b', style: TextStyle(fontSize: 16)),
  ],
)

// ✅ 正确：必须设置 textBaseline
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [
    Text('A', style: TextStyle(fontSize: 32)),
    Text('b', style: TextStyle(fontSize: 16)),
  ],
)
```

### 问题2：baseline 值设置不当

```dart
// baseline 值必须大于 0

// ❌ 错误：baseline 为负数或零
Baseline(
  baseline: 0,  // 无效
  baselineType: TextBaseline.alphabetic,
  child: Text('Error'),
)

// ✅ 正确：设置合理的基线值
Baseline(
  baseline: 20,
  baselineType: TextBaseline.alphabetic,
  child: Text('Correct'),
)
```

### 问题3：中英文混合对齐

```dart
// 中英文混合时，选择合适的基线类型
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.ideographic,  // 使用表意基线
  children: [
    Text('中文', style: TextStyle(fontSize: 24)),
    Text('English', style: TextStyle(fontSize: 16)),
  ],
)
```

## 5. Baseline vs 其他对齐方式

| 方式 | 用途 | 特点 |
|------|------|------|
| **Baseline** | 文本基线对齐 | 精确控制文本位置 |
| **CrossAxisAlignment.baseline** | Row/Column 基线对齐 | 需要 textBaseline |
| **CrossAxisAlignment.center** | 中心对齐 | 按中心点对齐 |
| **CrossAxisAlignment.start/end** | 边缘对齐 | 按边缘对齐 |

### 选择建议

```dart
// 文本基线对齐 → Baseline 或 Row 的 baseline 对齐
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [Text('A'), Text('b')],
)

// 简单居中 → CrossAxisAlignment.center
Row(
  crossAxisAlignment: CrossAxisAlignment.center,
  children: [Text('A'), Text('b')],
)

// 需要精确控制位置 → Baseline 组件
Container(
  height: 100,
  child: Baseline(
    baseline: 50,
    baselineType: TextBaseline.alphabetic,
    child: Text('Precise'),
  ),
)
```

## 6. 最佳实践

### 6.1 统一基线值

```dart
abstract class AppBaselines {
  static const double title = 32;
  static const double body = 20;
  static const double caption = 16;
}
```

### 6.2 封装基线文本

```dart
class PriceDisplay extends StatelessWidget {
  final String currency;
  final String integer;
  final String decimal;

  const PriceDisplay({
    required this.currency,
    required this.integer,
    required this.decimal,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      crossAxisAlignment: CrossAxisAlignment.baseline,
      textBaseline: TextBaseline.alphabetic,
      children: [
        Text(currency, style: TextStyle(fontSize: 16)),
        Text(integer, style: TextStyle(fontSize: 48, fontWeight: FontWeight.bold)),
        Text(decimal, style: TextStyle(fontSize: 20)),
      ],
    );
  }
}
```

### 6.3 处理溢出

```dart
ClipRect(
  child: Baseline(
    baseline: 20,
    baselineType: TextBaseline.alphabetic,
    child: Text('Clipped text'),
  ),
)
```

## 总结

Baseline 是精确控制文本对齐的工具：

**核心要点**：
1. `baseline` 定义基线距顶部的距离
2. `baselineType` 区分字母/表意基线
3. Row 使用 baseline 对齐需设置 `textBaseline`

**最佳实践**：
1. 中英文混合选择合适基线类型
2. 封装常用基线组件
3. 价格、评分等场景使用基线对齐

**常见场景**：
- 价格显示（¥99.99）
- 不同字号文本对齐
- 评分显示（4.5 ⭐）
- 数学公式

---

*最后更新: 2026-02-19*

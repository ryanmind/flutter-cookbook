# Flutter Text 组件用法详解

## 简介

Text 是 Flutter 中最基础的文本显示组件，用于在界面上展示文字内容。配合 TextStyle 可以实现丰富的文本样式。

## 1. 初级用法

### 1.1 基本概念

Text 的核心特点：
- 显示单一样式的文本
- 支持样式、对齐、溢出处理
- 可配合 Theme 使用
- 支持国际化

### 1.2 基本语法

```dart
// 最简单的用法
Text('Hello World')

// 带样式
Text(
  'Hello World',
  style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `data` | `String` | - | 文本内容（必需） |
| `style` | `TextStyle?` | - | 文本样式 |
| `textAlign` | `TextAlign?` | - | 对齐方式 |
| `maxLines` | `int?` | - | 最大行数 |
| `overflow` | `TextOverflow?` | - | 溢出处理 |
| `softWrap` | `bool` | `true` | 是否自动换行 |
| `textDirection` | `TextDirection?` | - | 文本方向 |
| `textScaler` | `TextScaler?` | - | 文本缩放策略 |
| `locale` | `Locale?` | - | 语言环境 |
| `semanticsLabel` | `String?` | - | 语义标签 |

### 1.4 TextStyle 常用属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `color` | `Color?` | 文字颜色 |
| `backgroundColor` | `Color?` | 背景色 |
| `fontSize` | `double?` | 字体大小 |
| `fontWeight` | `FontWeight?` | 字重 |
| `fontStyle` | `FontStyle?` | 字体样式（斜体） |
| `letterSpacing` | `double?` | 字符间距 |
| `wordSpacing` | `double?` | 单词间距 |
| `height` | `double?` | 行高倍数 |
| `decoration` | `TextDecoration?` | 装饰线 |
| `decorationColor` | `Color?` | 装饰线颜色 |
| `decorationStyle` | `TextDecorationStyle?` | 装饰线样式 |
| `shadows` | `List<Shadow>?` | 阴影 |

### 1.5 基础示例

#### 基本文本

```dart
Text('这是一段普通文本')

Text(
  '这是一段带样式的文本',
  style: TextStyle(
    fontSize: 20,
    fontWeight: FontWeight.bold,
    color: Colors.blue,
  ),
)
```

#### 文本对齐

```dart
SizedBox(
  width: double.infinity,
  child: Text(
    '居中对齐的文本',
    textAlign: TextAlign.center,
  ),
)

SizedBox(
  width: double.infinity,
  child: Text(
    '右对齐的文本',
    textAlign: TextAlign.right,
  ),
)
```

#### 溢出处理

```dart
// 裁剪
Text(
  '这是一段很长很长的文本内容，会被裁剪显示',
  overflow: TextOverflow.clip,
  maxLines: 1,
)

// 省略号
Text(
  '这是一段很长很长的文本内容，会显示省略号',
  overflow: TextOverflow.ellipsis,
  maxLines: 1,
)

// 渐变淡出
Text(
  '这是一段很长很长的文本内容，会渐变淡出',
  overflow: TextOverflow.fade,
  maxLines: 1,
)
```

## 2. 中级用法

### 2.1 使用主题样式

```dart
// 使用主题默认样式
Text(
  '标题样式',
  style: Theme.of(context).textTheme.headlineMedium,
)

Text(
  '正文样式',
  style: Theme.of(context).textTheme.bodyMedium,
)

Text(
  '小字样式',
  style: Theme.of(context).textTheme.bodySmall,
)
```

### 2.2 文本装饰

```dart
// 下划线
Text(
  '带下划线的文本',
  style: TextStyle(decoration: TextDecoration.underline),
)

// 删除线
Text(
  '带删除线的文本',
  style: TextStyle(decoration: TextDecoration.lineThrough),
)

// 上划线
Text(
  '带上划线的文本',
  style: TextStyle(decoration: TextDecoration.overline),
)

// 组合装饰
Text(
  '带多条线的文本',
  style: TextStyle(
    decoration: TextDecoration.combine([
      TextDecoration.underline,
      TextDecoration.lineThrough,
    ]),
  ),
)

// 装饰线样式
Text(
  '波浪下划线',
  style: TextStyle(
    decoration: TextDecoration.underline,
    decorationStyle: TextDecorationStyle.wavy,
    decorationColor: Colors.red,
  ),
)
```

### 2.3 文字阴影

```dart
Text(
  '带阴影的文字',
  style: TextStyle(
    fontSize: 32,
    shadows: [
      Shadow(
        color: Colors.black45,
        offset: Offset(2, 2),
        blurRadius: 4,
      ),
    ],
  ),
)

// 多层阴影
Text(
  '霓虹效果',
  style: TextStyle(
    fontSize: 40,
    fontWeight: FontWeight.bold,
    color: Colors.white,
    shadows: [
      Shadow(color: Colors.blue, offset: Offset(0, 0), blurRadius: 10),
      Shadow(color: Colors.purple, offset: Offset(0, 0), blurRadius: 20),
      Shadow(color: Colors.pink, offset: Offset(0, 0), blurRadius: 30),
    ],
  ),
)
```

### 2.4 字间距与行高

```dart
// 字符间距
Text(
  '字符间距效果',
  style: TextStyle(letterSpacing: 4),
)

// 单词间距
Text(
  'Hello World Flutter',
  style: TextStyle(wordSpacing: 10),
)

// 行高
Text(
  '第一行\n第二行\n第三行',
  style: TextStyle(height: 2),
)
```

### 2.5 字体样式

```dart
// 斜体
Text(
  '斜体文本',
  style: TextStyle(fontStyle: FontStyle.italic),
)

// 字重
Column(
  children: [
    Text('FontWeight.w100', style: TextStyle(fontWeight: FontWeight.w100)),
    Text('FontWeight.w300', style: TextStyle(fontWeight: FontWeight.w300)),
    Text('FontWeight.w400', style: TextStyle(fontWeight: FontWeight.w400)),
    Text('FontWeight.w500', style: TextStyle(fontWeight: FontWeight.w500)),
    Text('FontWeight.w700', style: TextStyle(fontWeight: FontWeight.w700)),
    Text('FontWeight.w900', style: TextStyle(fontWeight: FontWeight.w900)),
  ],
)

// 使用命名常量
Text('FontWeight.bold', style: TextStyle(fontWeight: FontWeight.bold))
Text('FontWeight.normal', style: TextStyle(fontWeight: FontWeight.normal))
```

## 3. 高级用法

### 3.1 RichText 多样式文本

```dart
RichText(
  text: TextSpan(
    text: 'Hello ',
    style: TextStyle(color: Colors.black),
    children: [
      TextSpan(
        text: 'Flutter',
        style: TextStyle(
          color: Colors.blue,
          fontWeight: FontWeight.bold,
        ),
      ),
      TextSpan(text: '!'),
    ],
  ),
)

// 可点击的文本
RichText(
  text: TextSpan(
    text: '同意',
    style: TextStyle(color: Colors.black),
    children: [
      TextSpan(
        text: '《用户协议》',
        style: TextStyle(color: Colors.blue),
        recognizer: TapGestureRecognizer()
          ..onTap = () {
            print('点击用户协议');
          },
      ),
      TextSpan(text: '和'),
      TextSpan(
        text: '《隐私政策》',
        style: TextStyle(color: Colors.blue),
        recognizer: TapGestureRecognizer()
          ..onTap = () {
            print('点击隐私政策');
          },
      ),
    ],
  ),
)
```

### 3.2 Text.rich 构造函数

```dart
Text.rich(
  TextSpan(
    text: '价格：',
    style: TextStyle(fontSize: 16),
    children: [
      TextSpan(
        text: '¥',
        style: TextStyle(fontSize: 12),
      ),
      TextSpan(
        text: '99.00',
        style: TextStyle(
          fontSize: 24,
          fontWeight: FontWeight.bold,
          color: Colors.red,
        ),
      ),
    ],
  ),
)
```

### 3.3 SelectableText 可选择文本

```dart
SelectableText(
  '这段文字可以被选中复制',
  style: TextStyle(fontSize: 16),
)

// 带样式的可选中文本
SelectableText.rich(
  TextSpan(
    text: '可选中',
    style: TextStyle(color: Colors.black),
    children: [
      TextSpan(
        text: '富文本',
        style: TextStyle(
          color: Colors.blue,
          fontWeight: FontWeight.bold,
        ),
      ),
    ],
  ),
)
```

### 3.4 自定义字体

```dart
// 1. 在 pubspec.yaml 中声明字体
// flutter:
//   fonts:
//     - family: MyFont
//       fonts:
//         - asset: fonts/MyFont-Regular.ttf
//         - asset: fonts/MyFont-Bold.ttf
//           weight: 700

// 2. 使用字体
Text(
  '自定义字体',
  style: TextStyle(
    fontFamily: 'MyFont',
    fontSize: 24,
  ),
)

// 3. 全局字体
MaterialApp(
  theme: ThemeData(
    fontFamily: 'MyFont',
  ),
)
```

### 3.5 文本测量

```dart
final TextPainter textPainter = TextPainter(
  text: TextSpan(
    text: '测量文本',
    style: TextStyle(fontSize: 16),
  ),
  textDirection: TextDirection.ltr,
)..layout();

print('文本宽度: ${textPainter.width}');
print('文本高度: ${textPainter.height}');
```

### 3.6 自动调整文本大小

```dart
FittedBox(
  child: Text(
    '这段文字会自动缩放',
    style: TextStyle(fontSize: 50),
  ),
)

// 或使用 AutoSizeText 包
// AutoSizeText(
//   '这段文字会自动调整大小',
//   style: TextStyle(fontSize: 30),
//   maxLines: 2,
// )
```

## 4. 性能优化

### 4.1 使用 const Text

```dart
// 静态文本使用 const
const Text('静态文本')

// 动态文本不使用 const
Text(dynamicValue)
```

### 4.2 避免频繁创建 TextStyle

```dart
// ❌ 不好：每次 build 创建新样式
Text(
  '文本',
  style: TextStyle(fontSize: 16, color: Colors.black),
)

// ✅ 好：复用样式
class _MyWidgetState extends State<MyWidget> {
  static const _textStyle = TextStyle(fontSize: 16, color: Colors.black);

  @override
  Widget build(BuildContext context) {
    return Text('文本', style: _textStyle);
  }
}
```

### 4.3 使用主题样式

```dart
// 使用主题定义的样式，便于维护
Text(
  '标题',
  style: Theme.of(context).textTheme.titleLarge,
)
```

## 5. 常见问题与解决方案

### 问题1：文本溢出

```dart
// ❌ 问题：文本超出容器
Container(
  width: 100,
  child: Text('这是一段很长很长的文本'),
)

// ✅ 解决方案：使用 overflow
Container(
  width: 100,
  child: Text(
    '这是一段很长很长的文本',
    overflow: TextOverflow.ellipsis,
    maxLines: 1,
  ),
)
```

### 问题2：多行文本截断

```dart
Text(
  '这是一段很长很长的文本，需要显示多行，但超过三行时显示省略号',
  maxLines: 3,
  overflow: TextOverflow.ellipsis,
)
```

### 问题3：文本对齐不生效

```dart
// ❌ 问题：textAlign 不生效
Text(
  '居中文本',
  textAlign: TextAlign.center,
)

// ✅ 解决方案：使用父容器约束宽度
SizedBox(
  width: double.infinity,
  child: Text(
    '居中文本',
    textAlign: TextAlign.center,
  ),
)
```

### 问题4：中英文混排

```dart
Text(
  'Hello世界',
  style: TextStyle(
    fontFamily: 'Noto Sans SC',  // 支持中文的字体
  ),
)
```

### 问题5：行高不一致

```dart
// 使用 height 控制行高
Text(
  '第一行\n第二行',
  style: TextStyle(height: 1.5),
)
```

## 6. Material 3 排版系统

| 样式名称 | 默认大小 | 用途 |
|---------|---------|------|
| `displayLarge` | 57 | 大标题 |
| `displayMedium` | 45 | 中标题 |
| `displaySmall` | 36 | 小标题 |
| `headlineLarge` | 32 | 大标题 |
| `headlineMedium` | 28 | 中标题 |
| `headlineSmall` | 24 | 小标题 |
| `titleLarge` | 22 | 大标题 |
| `titleMedium` | 16 | 中标题 |
| `titleSmall` | 14 | 小标题 |
| `bodyLarge` | 16 | 大正文 |
| `bodyMedium` | 14 | 正文 |
| `bodySmall` | 12 | 小正文 |
| `labelLarge` | 14 | 大标签 |
| `labelMedium` | 12 | 中标签 |
| `labelSmall` | 11 | 小标签 |

```dart
Text('Display Large', style: Theme.of(context).textTheme.displayLarge)
Text('Headline Medium', style: Theme.of(context).textTheme.headlineMedium)
Text('Title Small', style: Theme.of(context).textTheme.titleSmall)
Text('Body Medium', style: Theme.of(context).textTheme.bodyMedium)
Text('Label Small', style: Theme.of(context).textTheme.labelSmall)
```

## 7. 最佳实践

### 7.1 统一文本样式

```dart
class AppTextStyles {
  static const title = TextStyle(
    fontSize: 20,
    fontWeight: FontWeight.bold,
  );
  
  static const body = TextStyle(
    fontSize: 14,
    height: 1.5,
  );
  
  static const caption = TextStyle(
    fontSize: 12,
    color: Colors.grey,
  );
}

Text('标题', style: AppTextStyles.title)
Text('正文内容', style: AppTextStyles.body)
Text('说明文字', style: AppTextStyles.caption)
```

### 7.2 响应式字体大小

```dart
Text(
  '响应式文本',
  style: TextStyle(
    fontSize: MediaQuery.of(context).size.width > 600 ? 24 : 16,
  ),
)
```

### 7.3 国际化处理

```dart
// 使用 Intl 包格式化日期
import 'package:intl/intl.dart';

Text(DateFormat('yyyy-MM-dd').format(DateTime.now()))

// 使用 Intl 包格式化数字
Text(NumberFormat.currency(locale: 'zh_CN', symbol: '¥').format(1234.56))
```

## 总结

Text 是 Flutter 中最基础但最重要的组件：

**核心要点**：
1. Text 显示单一样式文本
2. TextStyle 控制文本外观
3. RichText/Text.rich 支持多样式
4. SelectableText 支持文本选择

**常用属性**：
- `style` - 文本样式
- `textAlign` - 对齐方式
- `maxLines` - 最大行数
- `overflow` - 溢出处理

**最佳实践**：
1. 使用主题统一样式
2. 静态文本使用 const
3. 复用 TextStyle 对象
4. 合理设置溢出处理

---

*最后更新: 2026-02-18*

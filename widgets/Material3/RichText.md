# Flutter RichText 组件用法详解

## 简介

RichText 是富文本组件，用于显示多种样式混合的文本。与 Text 组件不同，RichText 可以在同一段文本中使用不同的颜色、字体、大小等样式。

## 1. 初级用法

### 1.1 基本概念

RichText 的核心特点：
- 支持多种样式混合
- 使用 TextSpan 构建文本树
- 支持可点击文本
- 支持嵌套结构

### 1.2 基本语法

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
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `text` | `InlineSpan` | - | 文本内容（必需） |
| `textAlign` | `TextAlign` | `TextAlign.start` | 对齐方式 |
| `textDirection` | `TextDirection?` | - | 文本方向 |
| `softWrap` | `bool` | `true` | 是否自动换行 |
| `overflow` | `TextOverflow` | `TextOverflow.clip` | 溢出处理 |
| `textScaler` | `TextScaler?` | - | 文本缩放策略 |
| `maxLines` | `int?` | - | 最大行数 |
| `locale` | `Locale?` | - | 语言环境 |
| `strutStyle` | `StrutStyle?` | - | 基线对齐样式 |

### 1.4 TextSpan 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `text` | `String?` | 文本内容 |
| `children` | `List<InlineSpan>?` | 子文本段 |
| `style` | `TextStyle?` | 文本样式 |
| `recognizer` | `GestureRecognizer?` | 手势识别器 |
| `mouseCursor` | `MouseCursor?` | 鼠标悬停样式 |
| `onEnter` | `PointerEnterEventListener?` | 鼠标进入回调 |
| `onExit` | `PointerExitEventListener?` | 鼠标离开回调 |
| `semanticsLabel` | `String?` | 语义标签 |

### 1.5 基础示例

#### 多颜色文本

```dart
RichText(
  text: TextSpan(
    text: '红色文字',
    style: TextStyle(color: Colors.red, fontSize: 16),
    children: [
      TextSpan(
        text: ' 蓝色文字',
        style: TextStyle(color: Colors.blue),
      ),
      TextSpan(
        text: ' 绿色文字',
        style: TextStyle(color: Colors.green),
      ),
    ],
  ),
)
```

#### 多字体大小

```dart
RichText(
  text: TextSpan(
    text: '小字',
    style: TextStyle(fontSize: 12, color: Colors.black),
    children: [
      TextSpan(
        text: ' 中字',
        style: TextStyle(fontSize: 16),
      ),
      TextSpan(
        text: ' 大字',
        style: TextStyle(fontSize: 24),
      ),
    ],
  ),
)
```

#### 粗细混合

```dart
RichText(
  text: TextSpan(
    text: '普通文字 ',
    style: TextStyle(fontWeight: FontWeight.normal, color: Colors.black),
    children: [
      TextSpan(
        text: '粗体文字',
        style: TextStyle(fontWeight: FontWeight.bold),
      ),
    ],
  ),
)
```

## 2. 中级用法

### 2.1 可点击文本

```dart
RichText(
  text: TextSpan(
    text: '我已阅读并同意',
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

### 2.2 文本装饰

```dart
RichText(
  text: TextSpan(
    text: '普通文本 ',
    style: TextStyle(color: Colors.black),
    children: [
      TextSpan(
        text: '下划线文本',
        style: TextStyle(
          decoration: TextDecoration.underline,
          color: Colors.blue,
        ),
      ),
      TextSpan(text: ' '),
      TextSpan(
        text: '删除线文本',
        style: TextStyle(
          decoration: TextDecoration.lineThrough,
          color: Colors.grey,
        ),
      ),
    ],
  ),
)
```

### 2.3 混合图标

```dart
RichText(
  text: TextSpan(
    children: [
      WidgetSpan(
        child: Icon(Icons.star, color: Colors.amber, size: 16),
      ),
      TextSpan(
        text: ' 5.0 分',
        style: TextStyle(color: Colors.black, fontSize: 14),
      ),
      TextSpan(text: ' (123条评价)'),
    ],
  ),
)
```

### 2.4 价格显示

```dart
RichText(
  text: TextSpan(
    text: '¥',
    style: TextStyle(
      color: Colors.red,
      fontSize: 14,
      fontWeight: FontWeight.bold,
    ),
    children: [
      TextSpan(
        text: '99',
        style: TextStyle(fontSize: 24),
      ),
      TextSpan(
        text: '.00',
        style: TextStyle(fontSize: 14),
      ),
      TextSpan(
        text: '  原价¥199',
        style: TextStyle(
          color: Colors.grey,
          fontSize: 12,
          decoration: TextDecoration.lineThrough,
        ),
      ),
    ],
  ),
)
```

### 2.5 多行对齐

```dart
RichText(
  textAlign: TextAlign.center,
  text: TextSpan(
    text: '第一行\n',
    style: TextStyle(color: Colors.black),
    children: [
      TextSpan(
        text: '第二行居中\n',
        style: TextStyle(color: Colors.blue),
      ),
      TextSpan(
        text: '第三行',
        style: TextStyle(color: Colors.green),
      ),
    ],
  ),
)
```

## 3. 高级用法

### 3.1 WidgetSpan 嵌入组件

```dart
RichText(
  text: TextSpan(
    children: [
      TextSpan(text: '点赞 '),
      WidgetSpan(
        child: Icon(Icons.thumb_up, color: Colors.blue, size: 16),
      ),
      TextSpan(text: ' 1234'),
    ],
  ),
)
```

### 3.2 富文本构建器

```dart
class RichTextBuilder {
  final List<TextSpan> _spans = [];
  TextStyle _defaultStyle;

  RichTextBuilder({TextStyle? defaultStyle}) 
    : _defaultStyle = defaultStyle ?? TextStyle(color: Colors.black);

  RichTextBuilder text(String text, {TextStyle? style}) {
    _spans.add(TextSpan(text: text, style: style));
    return this;
  }

  RichTextBuilder link(String text, VoidCallback onTap, {TextStyle? style}) {
    _spans.add(TextSpan(
      text: text,
      style: style ?? TextStyle(color: Colors.blue),
      recognizer: TapGestureRecognizer()..onTap = onTap,
    ));
    return this;
  }

  RichTextBuilder icon(Widget icon) {
    _spans.add(WidgetSpan(child: icon) as TextSpan);
    return this;
  }

  RichText build({TextAlign textAlign = TextAlign.start}) {
    return RichText(
      textAlign: textAlign,
      text: TextSpan(
        style: _defaultStyle,
        children: _spans,
      ),
    );
  }
}

// 使用
RichTextBuilder()
  .text('我已阅读并同意')
  .link('《用户协议》', () => print('用户协议'))
  .text('和')
  .link('《隐私政策》', () => print('隐私政策'))
  .build()
```

### 3.3 文本高亮

```dart
TextSpan highlightText({
  required String text,
  required String keyword,
  TextStyle? normalStyle,
  TextStyle? highlightStyle,
}) {
  if (keyword.isEmpty) {
    return TextSpan(text: text, style: normalStyle);
  }

  final spans = <TextSpan>[];
  int start = 0;
  int index;

  while ((index = text.toLowerCase().indexOf(keyword.toLowerCase(), start)) != -1) {
    if (index > start) {
      spans.add(TextSpan(
        text: text.substring(start, index),
        style: normalStyle,
      ));
    }
    spans.add(TextSpan(
      text: text.substring(index, index + keyword.length),
      style: highlightStyle ?? TextStyle(color: Colors.red, backgroundColor: Colors.yellow),
    ));
    start = index + keyword.length;
  }

  if (start < text.length) {
    spans.add(TextSpan(text: text.substring(start), style: normalStyle));
  }

  return TextSpan(children: spans);
}

// 使用
RichText(
  text: highlightText(
    text: 'Flutter 是 Google 开发的 UI 框架',
    keyword: 'Flutter',
    normalStyle: TextStyle(color: Colors.black),
    highlightStyle: TextStyle(color: Colors.red, backgroundColor: Colors.yellow),
  ),
)
```

### 3.4 标签文本

```dart
class TaggedText extends StatelessWidget {
  final String text;
  final TextStyle? textStyle;

  const TaggedText({
    required this.text,
    this.textStyle,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    // 解析 #标签 和 @提及
    final RegExp tagRegex = RegExp(r'(#\w+)|(@\w+)');
    final spans = <InlineSpan>[];
    int lastEnd = 0;

    for (final match in tagRegex.allMatches(text)) {
      // 添加普通文本
      if (match.start > lastEnd) {
        spans.add(TextSpan(
          text: text.substring(lastEnd, match.start),
          style: textStyle,
        ));
      }

      // 添加标签/提及
      final matchedText = match.group(0)!;
      final isHashTag = matchedText.startsWith('#');
      spans.add(TextSpan(
        text: matchedText,
        style: TextStyle(
          color: isHashTag ? Colors.blue : Colors.green,
          fontWeight: FontWeight.bold,
        ),
        recognizer: TapGestureRecognizer()
          ..onTap = () {
            print('点击: $matchedText');
          },
      ));

      lastEnd = match.end;
    }

    // 添加剩余文本
    if (lastEnd < text.length) {
      spans.add(TextSpan(
        text: text.substring(lastEnd),
        style: textStyle,
      ));
    }

    return RichText(
      text: TextSpan(children: spans),
    );
  }
}

// 使用
TaggedText(
  text: '今天学习了 #Flutter 开发，感谢 @Google 提供的框架！',
  textStyle: TextStyle(color: Colors.black),
)
```

### 3.5 段落首行缩进

```dart
RichText(
  text: TextSpan(
    children: [
      WidgetSpan(
        child: SizedBox(width: 24),  // 两个字符宽度
      ),
      TextSpan(
        text: '这是段落内容，首行缩进两个字符。这是段落内容，首行缩进两个字符。',
        style: TextStyle(color: Colors.black, height: 1.5),
      ),
    ],
  ),
)
```

## 4. 常见问题与解决方案

### 问题1：文本不显示

```dart
// 确保设置了样式，默认颜色可能是白色
RichText(
  text: TextSpan(
    text: '文本内容',
    style: TextStyle(color: Colors.black),  // 必须设置颜色
  ),
)
```

### 问题2：点击事件不响应

```dart
// 需要导入手势识别器
import 'package:flutter/gestures.dart';

TextSpan(
  text: '可点击文本',
  recognizer: TapGestureRecognizer()
    ..onTap = () {
      print('点击');
    },
)
```

### 问题3：文本溢出

```dart
RichText(
  maxLines: 2,
  overflow: TextOverflow.ellipsis,
  text: TextSpan(...),
)
```

### 问题4：WidgetSpan 对齐问题

```dart
WidgetSpan(
  child: Icon(Icons.star, size: 16),
  alignment: PlaceholderAlignment.middle,  // 垂直居中对齐
)
```

## 5. 性能优化

### 5.1 避免频繁创建

```dart
// 对于静态内容，创建一次复用
final TextSpan _staticSpan = TextSpan(
  text: '静态文本',
  style: TextStyle(color: Colors.black),
);
```

### 5.2 使用 const

```dart
const TextSpan(
  text: '常量文本',
  style: TextStyle(color: Colors.black),
)
```

## 6. 最佳实践

### 6.1 封装富文本工具

```dart
class AppRichText {
  static RichText agreement({
    required VoidCallback onUserAgreement,
    required VoidCallback onPrivacyPolicy,
  }) {
    return RichText(
      text: TextSpan(
        text: '我已阅读并同意',
        style: TextStyle(color: Colors.black),
        children: [
          TextSpan(
            text: '《用户协议》',
            style: TextStyle(color: Colors.blue),
            recognizer: TapGestureRecognizer()..onTap = onUserAgreement,
          ),
          TextSpan(text: '和'),
          TextSpan(
            text: '《隐私政策》',
            style: TextStyle(color: Colors.blue),
            recognizer: TapGestureRecognizer()..onTap = onPrivacyPolicy,
          ),
        ],
      ),
    );
  }

  static RichText price({
    required double price,
    double? originalPrice,
  }) {
    final spans = <TextSpan>[
      TextSpan(
        text: '¥',
        style: TextStyle(color: Colors.red, fontSize: 14),
      ),
      TextSpan(
        text: price.toStringAsFixed(2),
        style: TextStyle(color: Colors.red, fontSize: 20, fontWeight: FontWeight.bold),
      ),
    ];

    if (originalPrice != null) {
      spans.add(TextSpan(
        text: '  ¥${originalPrice.toStringAsFixed(2)}',
        style: TextStyle(
          color: Colors.grey,
          fontSize: 12,
          decoration: TextDecoration.lineThrough,
        ),
      ));
    }

    return RichText(text: TextSpan(children: spans));
  }
}
```

## 总结

RichText 是 Flutter 中显示混合样式文本的核心组件：

**核心要点**：
1. 使用 TextSpan 构建文本树
2. 每个 TextSpan 可以有独立样式
3. 支持可点击文本（配合 TapGestureRecognizer）
4. 支持 WidgetSpan 嵌入组件

**常用属性**：
- `text` - TextSpan 文本内容
- `textAlign` - 对齐方式
- `maxLines` - 最大行数
- `overflow` - 溢出处理

**最佳实践**：
1. 封装常用的富文本样式
2. 合理使用 WidgetSpan 嵌入组件
3. 处理文本溢出情况
4. 为可点击文本提供视觉反馈

---

*最后更新: 2026-02-19*

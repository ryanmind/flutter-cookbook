# Text vs UILabel 对比学习

本文档记录 Flutter Text 组件与 UIKit UILabel 的对比学习要点。

---

## 1. Text 核心概念

### Text 是什么

Text 是 Flutter 中用于显示文本的组件，功能丰富，支持多种样式。

- 支持单行和多行文本
- 支持富文本（RichText）
- 支持文本溢出处理
- 支持文本选择和交互

### Text vs UILabel 概念对比

| Flutter Text | UIKit UILabel | 说明 |
|--------------|---------------|------|
| `Text()` | `UILabel` | 文本显示 |
| `style: TextStyle()` | `font`, `textColor` 等 | 样式 |
| `textAlign` | `textAlignment` | 对齐方式 |
| `maxLines` | `numberOfLines` | 最大行数 |
| `overflow` | 无直接对应 | 溢出处理 |
| `RichText` | `NSAttributedString` | 富文本 |
| `TextSpan` | `NSAttributedString` 片段 | 文本片段 |
| `SelectableText` | `isUserInteractionEnabled` | 可选择文本 |

---

## 2. 源码分析

### Text 类定义

```dart
class Text extends StatelessWidget {
  const Text(
    String this.data, {            // 文本内容
    super.key,
    this.style,                    // 样式
    this.strutStyle,
    this.textAlign,                // 对齐
    this.textDirection,
    this.locale,
    this.softWrap,                 // 自动换行
    this.overflow,                 // 溢出处理
    this.textScaleFactor,          // 缩放因子
    this.maxLines,                 // 最大行数
    this.semanticsLabel,
    this.textWidthBasis,
    this.textHeightBehavior,
    this.selectionColor,
  });
  
  // 富文本构造函数
  const Text.rich(
    InlineSpan this.textSpan, {
    super.key,
    this.style,
    // ...
  });
}
```

### TextStyle 类定义

```dart
class TextStyle extends Diagnosticable {
  const TextStyle({
    this.inherit = true,
    this.color,                    // 文字颜色
    this.backgroundColor,          // 背景颜色
    this.fontSize,                 // 字体大小
    this.fontWeight,               // 字重
    this.fontStyle,                // 斜体
    this.letterSpacing,            // 字间距
    this.wordSpacing,              // 词间距
    this.textBaseline,
    this.height,                   // 行高
    this.leadingDistribution,
    this.locale,
    this.foreground,
    this.background,
    this.shadows,                  // 阴影
    this.fontFeatures,
    this.fontVariations,
    this.decoration,               // 装饰线
    this.decorationColor,          // 装饰线颜色
    this.decorationStyle,          // 装饰线样式
    this.decorationThickness,
    this.debugLabel,
    this.fontFamily,               // 字体
    this.fontFamilyFallback,
    this.overflow,                 // 溢出
  });
}
```

---

## 3. 与 UILabel 详细对比

### 3.1 基础文本

```objc
// Objective-C - UILabel
UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(20, 100, 280, 44)];
label.text = @"Hello, World!";
label.font = [UIFont systemFontOfSize:16];
label.textColor = [UIColor blackColor];
[self.view addSubview:label];
```

```dart
// Flutter - Text
Text(
  'Hello, World!',
  style: TextStyle(
    fontSize: 16,
    color: Colors.black,
  ),
)
```

### 3.2 字体样式

```objc
// Objective-C
label.font = [UIFont systemFontOfSize:16 weight:UIFontWeightBold];
label.font = [UIFont italicSystemFontOfSize:16];
label.textColor = [UIColor redColor];
```

```dart
// Flutter
Text(
  'Hello, World!',
  style: TextStyle(
    fontSize: 16,
    fontWeight: FontWeight.bold,
    fontStyle: FontStyle.italic,
    color: Colors.red,
  ),
)
```

### 3.3 对齐方式

```objc
// Objective-C
label.textAlignment = NSTextAlignmentLeft;
label.textAlignment = NSTextAlignmentCenter;
label.textAlignment = NSTextAlignmentRight;
```

```dart
// Flutter
Text(
  'Hello, World!',
  textAlign: TextAlign.left,
  textAlign: TextAlign.center,
  textAlign: TextAlign.right,
  textAlign: TextAlign.justify,
)
```

### 3.4 多行文本

```objc
// Objective-C
label.numberOfLines = 0;  // 无限制
label.numberOfLines = 3;  // 最多 3 行
label.lineBreakMode = NSLineBreakByTruncatingTail;
```

```dart
// Flutter
Text(
  'Long text that will wrap to multiple lines...',
  maxLines: 3,  // 最多 3 行
  overflow: TextOverflow.ellipsis,  // 溢出显示省略号
)
```

### 3.5 溢出处理

```objc
// Objective-C
label.lineBreakMode = NSLineBreakByTruncatingTail;   // 尾部省略
label.lineBreakMode = NSLineBreakByTruncatingMiddle; // 中间省略
label.lineBreakMode = NSLineBreakByTruncatingHead;   // 头部省略
```

```dart
// Flutter
Text(
  'Long text...',
  overflow: TextOverflow.ellipsis,   // 省略号
  overflow: TextOverflow.fade,       // 渐变消失
  overflow: TextOverflow.clip,       // 直接裁剪
  overflow: TextOverflow.visible,    // 可见（可能溢出）
)
```

### 3.6 行高和字间距

```objc
// Objective-C - NSAttributedString
NSMutableParagraphStyle *style = [[NSMutableParagraphStyle alloc] init];
style.lineSpacing = 4;  // 行间距
style.lineHeightMultiple = 1.5;  // 行高倍数

NSMutableAttributedString *attrString = [[NSMutableAttributedString alloc] initWithString:@"Hello"];
[attrString addAttribute:NSParagraphStyleAttributeName value:style range:NSMakeRange(0, attrString.length)];
[attrString addAttribute:NSKernAttributeName value:@(1.5) range:NSMakeRange(0, attrString.length)];

label.attributedText = attrString;
```

```dart
// Flutter - 更简洁
Text(
  'Hello, World!',
  style: TextStyle(
    height: 1.5,           // 行高倍数
    letterSpacing: 1.5,    // 字间距
    wordSpacing: 2.0,      // 词间距
  ),
)
```

### 3.7 富文本

```objc
// Objective-C - NSAttributedString
NSMutableAttributedString *attrString = [[NSMutableAttributedString alloc] initWithString:@"Hello, World!"];

[attrString addAttribute:NSForegroundColorAttributeName value:[UIColor redColor] range:NSMakeRange(0, 5)];
[attrString addAttribute:NSFontAttributeName value:[UIFont boldSystemFontOfSize:18] range:NSMakeRange(7, 5)];

label.attributedText = attrString;
```

```dart
// Flutter - Text.rich 或 RichText
Text.rich(
  TextSpan(
    text: 'Hello, ',
    style: TextStyle(color: Colors.black),
    children: [
      TextSpan(
        text: 'World',
        style: TextStyle(
          color: Colors.red,
          fontWeight: FontWeight.bold,
        ),
      ),
      TextSpan(text: '!'),
    ],
  ),
)

// 或使用 RichText
RichText(
  text: TextSpan(
    text: 'Hello, ',
    style: TextStyle(color: Colors.black),
    children: [...],
  ),
)
```

### 3.8 装饰线

```objc
// Objective-C - NSAttributedString
[attrString addAttribute:NSUnderlineStyleAttributeName value:@(NSUnderlineStyleSingle) range:NSMakeRange(0, attrString.length)];
[attrString addAttribute:NSStrikethroughStyleAttributeName value:@(NSUnderlineStyleSingle) range:NSMakeRange(0, attrString.length)];
```

```dart
// Flutter - TextStyle
Text(
  'Underlined text',
  style: TextStyle(
    decoration: TextDecoration.underline,
    decorationColor: Colors.red,
    decorationStyle: TextDecorationStyle.solid,
  ),
)

Text(
  'Strikethrough text',
  style: TextStyle(
    decoration: TextDecoration.lineThrough,
  ),
)
```

### 3.9 阴影

```objc
// Objective-C - label 无直接阴影，需要 layer
label.layer.shadowColor = [UIColor blackColor].CGColor;
label.layer.shadowOffset = CGSizeMake(1, 1);
label.layer.shadowRadius = 2;
label.layer.shadowOpacity = 0.5;
```

```dart
// Flutter - TextStyle shadows
Text(
  'Shadow text',
  style: TextStyle(
    shadows: [
      Shadow(
        color: Colors.black.withOpacity(0.5),
        offset: Offset(1, 1),
        blurRadius: 2,
      ),
    ],
  ),
)
```

### 3.10 可选择文本

```objc
// Objective-C - 需要使用 UITextView
UITextView *textView = [[UITextView alloc] init];
textView.editable = NO;
textView.selectable = YES;
```

```dart
// Flutter - SelectableText
SelectableText(
  'This text is selectable',
  onTap: () {
    print('Tapped');
  },
  onSelectionChanged: (selection, cause) {
    print('Selection changed');
  },
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 创建一个带样式的文本
UILabel *titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(20, 100, 280, 0)];
titleLabel.text = @"Welcome";
titleLabel.font = [UIFont boldSystemFontOfSize:24];
titleLabel.textColor = [UIColor blackColor];
[titleLabel sizeToFit];

UILabel *subtitleLabel = [[UILabel alloc] initWithFrame:CGRectMake(20, 140, 280, 0)];
subtitleLabel.text = @"This is a subtitle that might be longer and wrap to multiple lines";
subtitleLabel.font = [UIFont systemFontOfSize:14];
subtitleLabel.textColor = [UIColor grayColor];
subtitleLabel.numberOfLines = 2;
[subtitleLabel sizeToFit];

// 富文本
NSMutableAttributedString *attrString = [[NSMutableAttributedString alloc] initWithString:@"By John Doe • 5 min read"];
[attrString addAttribute:NSFontAttributeName value:[UIFont boldSystemFontOfSize:14] range:NSMakeRange(3, 8)];
[attrString addAttribute:NSForegroundColorAttributeName value:[UIColor blueColor] range:NSMakeRange(3, 8)];

UILabel *metaLabel = [[UILabel alloc] initWithFrame:CGRectMake(20, 180, 280, 0)];
metaLabel.attributedText = attrString;
[metaLabel sizeToFit];

[self.view addSubview:titleLabel];
[self.view addSubview:subtitleLabel];
[self.view addSubview:metaLabel];
```

### Flutter 实现

```dart
// 创建同样的文本 - 更简洁
Column(
  crossAxisAlignment: CrossAxisAlignment.start,
  children: [
    // 标题
    Text(
      'Welcome',
      style: TextStyle(
        fontSize: 24,
        fontWeight: FontWeight.bold,
        color: Colors.black,
      ),
    ),
    SizedBox(height: 8),
    // 副标题
    Text(
      'This is a subtitle that might be longer and wrap to multiple lines',
      style: TextStyle(
        fontSize: 14,
        color: Colors.grey,
      ),
      maxLines: 2,
      overflow: TextOverflow.ellipsis,
    ),
    SizedBox(height: 8),
    // 富文本
    Text.rich(
      TextSpan(
        text: 'By ',
        children: [
          TextSpan(
            text: 'John Doe',
            style: TextStyle(
              fontWeight: FontWeight.bold,
              color: Colors.blue,
            ),
          ),
          TextSpan(text: ' • 5 min read'),
        ],
      ),
      style: TextStyle(fontSize: 14),
    ),
  ],
)
```

---

## 5. TextTheme 主题

### 使用 Theme

```dart
// 定义主题
ThemeData(
  textTheme: TextTheme(
    displayLarge: TextStyle(fontSize: 57, fontWeight: FontWeight.w400),
    displayMedium: TextStyle(fontSize: 45, fontWeight: FontWeight.w400),
    displaySmall: TextStyle(fontSize: 36, fontWeight: FontWeight.w400),
    headlineLarge: TextStyle(fontSize: 32, fontWeight: FontWeight.w400),
    headlineMedium: TextStyle(fontSize: 28, fontWeight: FontWeight.w400),
    headlineSmall: TextStyle(fontSize: 24, fontWeight: FontWeight.w400),
    titleLarge: TextStyle(fontSize: 22, fontWeight: FontWeight.w400),
    titleMedium: TextStyle(fontSize: 16, fontWeight: FontWeight.w500),
    titleSmall: TextStyle(fontSize: 14, fontWeight: FontWeight.w500),
    bodyLarge: TextStyle(fontSize: 16, fontWeight: FontWeight.w400),
    bodyMedium: TextStyle(fontSize: 14, fontWeight: FontWeight.w400),
    bodySmall: TextStyle(fontSize: 12, fontWeight: FontWeight.w400),
    labelLarge: TextStyle(fontSize: 14, fontWeight: FontWeight.w500),
    labelMedium: TextStyle(fontSize: 12, fontWeight: FontWeight.w500),
    labelSmall: TextStyle(fontSize: 11, fontWeight: FontWeight.w500),
  ),
)

// 使用主题样式
Text(
  'Title',
  style: Theme.of(context).textTheme.titleLarge,
)

Text(
  'Body text',
  style: Theme.of(context).textTheme.bodyMedium,
)
```

---

## 6. 常见陷阱

### 陷阱1：Text 在无宽度约束中

```dart
// ❌ 问题：Row 中的 Text 无宽度约束
Row(
  children: [
    Text('Long text that will overflow...'),  // 报错！
  ],
)

// ✅ 解决方案：使用 Expanded
Row(
  children: [
    Expanded(
      child: Text('Long text that will overflow...'),
    ),
  ],
)

// ✅ 或使用 Flexible
Row(
  children: [
    Flexible(
      child: Text('Long text...', overflow: TextOverflow.ellipsis),
    ),
  ],
)
```

### 陷阱2：textAlign 不生效

```dart
// ❌ 问题：Text 宽度等于内容宽度，textAlign 无效
Text(
  'Short',
  textAlign: TextAlign.center,  // 无效
)

// ✅ 解决方案：设置宽度
SizedBox(
  width: double.infinity,
  child: Text(
    'Short',
    textAlign: TextAlign.center,
  ),
)
```

### 陷阱3：height 计算方式

```dart
// TextStyle.height 是字体大小的倍数，不是像素值
Text(
  'Hello',
  style: TextStyle(
    fontSize: 16,
    height: 1.5,  // 行高 = 16 * 1.5 = 24 像素
  ),
)
```

### 陷阱4：富文本点击

```dart
// 富文本中的 TextSpan 可以添加点击事件
Text.rich(
  TextSpan(
    text: 'Click ',
    children: [
      TextSpan(
        text: 'here',
        style: TextStyle(color: Colors.blue),
        recognizer: TapGestureRecognizer()
          ..onTap = () {
            print('Clicked!');
          },
      ),
    ],
  ),
)
```

---

## 7. 学习技巧

### 技巧1：使用 DefaultTextStyle

```dart
DefaultTextStyle(
  style: TextStyle(
    fontSize: 16,
    color: Colors.black,
  ),
  child: Column(
    children: [
      Text('Inherits default style'),
      Text('Also inherits', style: TextStyle(fontWeight: FontWeight.bold)),
    ],
  ),
)
```

### 技巧2：获取文本尺寸

```dart
final textPainter = TextPainter(
  text: TextSpan(
    text: 'Hello',
    style: TextStyle(fontSize: 16),
  ),
  textDirection: TextDirection.ltr,
)..layout();

final width = textPainter.width;
final height = textPainter.height;
```

### 技巧3：自动换行语言

```dart
// 某些语言需要设置 locale 来正确换行
Text(
  '中文文本',
  locale: Locale('zh', 'CN'),
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UILabel                    → Text
text                       → Text() 构造函数参数
font                       → style: TextStyle(fontSize:, fontWeight:)
textColor                  → style: TextStyle(color:)
textAlignment              → textAlign
numberOfLines              → maxLines
attributedText             → Text.rich / RichText
NSAttributedString         → TextSpan
lineBreakMode              → overflow
shadowColor/Offset         → style: TextStyle(shadows:)
```

### 关键差异

| 方面 | UIKit UILabel | Flutter Text |
|------|---------------|--------------|
| **样式设置** | 分散的属性 | TextStyle 集中管理 |
| **富文本** | NSAttributedString | TextSpan + Text.rich |
| **溢出处理** | lineBreakMode | overflow |
| **主题** | 无内置 | TextTheme |
| **可选择** | 需要 UITextView | SelectableText |

### 最佳实践

1. **使用 TextTheme** - 统一管理文本样式
2. **注意约束** - Text 在 Row 中需要 Expanded
3. **overflow** - 长文本设置 overflow
4. **RichText** - 需要多种样式时使用

---

*最后更新: 2026-02-24*

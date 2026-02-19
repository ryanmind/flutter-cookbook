# Flutter SelectableText 组件用法详解

## 简介

SelectableText 是可选择文本组件，允许用户长按或双击选中文本并复制。与普通 Text 组件不同，SelectableText 提供了原生的文本选择功能。

## 1. 初级用法

### 1.1 基本概念

SelectableText 的核心特点：
- 支持文本选择和复制
- 支持长按、双击选中
- 支持自定义选中样式
- 支持可点击文本

### 1.2 基本语法

```dart
// 最简单的用法
SelectableText('这段文字可以被选中复制')

// 带样式
SelectableText(
  '可选择的文本',
  style: TextStyle(fontSize: 16, color: Colors.black),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `data` | `String` | - | 文本内容（必需） |
| `style` | `TextStyle?` | - | 文本样式 |
| `textAlign` | `TextAlign?` | - | 对齐方式 |
| `textDirection` | `TextDirection?` | - | 文本方向 |
| `maxLines` | `int?` | - | 最大行数 |
| `onSelectionChanged` | `SelectionChangedCallback?` | - | 选择变化回调 |
| `onTap` | `GestureTapCallback?` | - | 点击回调 |
| `cursorWidth` | `double` | 2.0 | 光标宽度 |
| `cursorHeight` | `double?` | - | 光标高度 |
| `cursorRadius` | `Radius?` | - | 光标圆角 |
| `cursorColor` | `Color?` | - | 光标颜色 |
| `selectionHeightStyle` | `BoxHeightStyle` | `tight` | 选中区域高度样式 |
| `selectionWidthStyle` | `BoxWidthStyle` | `tight` | 选中区域宽度样式 |
| `toolbarOptions` | `ToolbarOptions?` | - | 工具栏选项 |
| `showCursor` | `bool` | `false` | 是否显示光标 |
| `autofocus` | `bool` | `false` | 自动聚焦 |
| `enableInteractiveSelection` | `bool` | `true` | 是否启用交互选择 |
| `scrollPhysics` | `ScrollPhysics?` | - | 滚动物理特性 |
| `semanticsLabel` | `String?` | - | 语义标签 |

### 1.4 基础示例

#### 基本可选中

```dart
SelectableText(
  '这是一段可以被选中的文本，长按或双击即可选择。',
  style: TextStyle(fontSize: 16),
)
```

#### 带样式的可选择文本

```dart
SelectableText(
  '可选择的标题文本',
  style: TextStyle(
    fontSize: 24,
    fontWeight: FontWeight.bold,
    color: Colors.blue,
  ),
)
```

#### 多行文本选择

```dart
SelectableText(
  '第一行文本\n第二行文本\n第三行文本\n这些都可以被选中复制',
  style: TextStyle(fontSize: 16, height: 1.5),
)
```

## 2. 中级用法

### 2.1 监听选择变化

```dart
SelectableText(
  '选中文本时会触发回调',
  onSelectionChanged: (selection, cause) {
    print('选中范围: ${selection.start} - ${selection.end}');
    print('触发原因: $cause');  // 用户操作类型
  },
)
```

### 2.2 自定义光标样式

```dart
SelectableText(
  '自定义光标样式',
  cursorWidth: 2,
  cursorHeight: 20,
  cursorColor: Colors.red,
  cursorRadius: Radius.circular(2),
)
```

### 2.3 选中样式

```dart
SelectableText(
  '自定义选中样式',
  style: TextStyle(fontSize: 16),
  // 选中样式由主题控制
)
```

### 2.4 工具栏选项

```dart
SelectableText(
  '自定义工具栏选项',
  toolbarOptions: ToolbarOptions(
    copy: true,
    cut: false,  // 纯文本不可剪切
    paste: false,
    selectAll: true,
  ),
)
```

### 2.5 禁用选择

```dart
SelectableText(
  '这段文字无法选择',
  enableInteractiveSelection: false,
)
```

## 3. 高级用法

### 3.1 SelectableText.rich 富文本选择

```dart
SelectableText.rich(
  TextSpan(
    text: '普通文本 ',
    style: TextStyle(color: Colors.black),
    children: [
      TextSpan(
        text: '蓝色文本',
        style: TextStyle(color: Colors.blue, fontWeight: FontWeight.bold),
      ),
      TextSpan(text: ' 又是普通文本'),
    ],
  ),
)
```

### 3.2 可点击的可选择文本

```dart
SelectableText(
  '点击这段文字',
  onTap: () {
    print('文本被点击');
  },
)
```

### 3.3 结合 TapGestureRecognizer

```dart
SelectableText.rich(
  TextSpan(
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
    ],
  ),
)
```

### 3.4 选择控制器

```dart
class ControlledSelectableText extends StatefulWidget {
  final String text;

  const ControlledSelectableText(this.text, {Key? key}) : super(key: key);

  @override
  _ControlledSelectableTextState createState() => _ControlledSelectableTextState();
}

class _ControlledSelectableTextState extends State<ControlledSelectableText> {
  final TextEditingController _controller = TextEditingController();
  final FocusNode _focusNode = FocusNode();

  @override
  void initState() {
    super.initState();
    _controller.text = widget.text;
  }

  @override
  void dispose() {
    _controller.dispose();
    _focusNode.dispose();
    super.dispose();
  }

  void selectAll() {
    _controller.selection = TextSelection(
      baseOffset: 0,
      extentOffset: _controller.text.length,
    );
    _focusNode.requestFocus();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        SelectableText(
          widget.text,
          focusNode: _focusNode,
        ),
        ElevatedButton(
          onPressed: selectAll,
          child: Text('全选'),
        ),
      ],
    );
  }
}
```

### 3.5 带高亮的可选择文本

```dart
class HighlightSelectableText extends StatelessWidget {
  final String text;
  final String highlight;
  final TextStyle? normalStyle;
  final TextStyle? highlightStyle;

  const HighlightSelectableText({
    required this.text,
    required this.highlight,
    this.normalStyle,
    this.highlightStyle,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    if (highlight.isEmpty) {
      return SelectableText(text, style: normalStyle);
    }

    final spans = <TextSpan>[];
    int start = 0;
    int index;

    while ((index = text.toLowerCase().indexOf(highlight.toLowerCase(), start)) != -1) {
      if (index > start) {
        spans.add(TextSpan(
          text: text.substring(start, index),
          style: normalStyle,
        ));
      }
      spans.add(TextSpan(
        text: text.substring(index, index + highlight.length),
        style: highlightStyle ?? TextStyle(backgroundColor: Colors.yellow),
      ));
      start = index + highlight.length;
    }

    if (start < text.length) {
      spans.add(TextSpan(text: text.substring(start), style: normalStyle));
    }

    return SelectableText.rich(
      TextSpan(children: spans),
    );
  }
}

// 使用
HighlightSelectableText(
  text: 'Flutter 是 Google 开发的 UI 框架',
  highlight: 'Flutter',
  normalStyle: TextStyle(color: Colors.black),
  highlightStyle: TextStyle(backgroundColor: Colors.yellow, color: Colors.red),
)
```

### 3.6 复制成功提示

```dart
class CopyableText extends StatelessWidget {
  final String text;
  final TextStyle? style;

  const CopyableText({
    required this.text,
    this.style,
    Key? key,
  }) : super(key: key);

  void _showCopySuccess(BuildContext context) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text('已复制到剪贴板'),
        duration: Duration(seconds: 1),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onLongPress: () {
        Clipboard.setData(ClipboardData(text: text));
        _showCopySuccess(context);
      },
      child: Row(
        children: [
          Expanded(
            child: SelectableText(text, style: style),
          ),
          IconButton(
            icon: Icon(Icons.copy, size: 18),
            onPressed: () {
              Clipboard.setData(ClipboardData(text: text));
              _showCopySuccess(context);
            },
          ),
        ],
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：文本无法选择

```dart
// 检查 enableInteractiveSelection
SelectableText(
  '可选择的文本',
  enableInteractiveSelection: true,  // 确保为 true
)

// 注意：嵌套在 GestureDetector 中可能会冲突
GestureDetector(
  onLongPress: () {},  // 可能与选择功能冲突
  child: SelectableText('文本'),
)
```

### 问题2：选中后无复制选项

```dart
// iOS/Android 默认支持，检查 toolbarOptions
SelectableText(
  '文本',
  toolbarOptions: ToolbarOptions(
    copy: true,
    selectAll: true,
  ),
)
```

### 问题3：样式不生效

```dart
// 确保在正确的位置设置样式
SelectableText(
  '文本',
  style: TextStyle(fontSize: 16),  // 文本样式
)
```

### 问题4：多行文本选中范围

```dart
// 使用 maxLines 限制行数
SelectableText(
  '多行\n文本\n内容',
  maxLines: 3,
)
```

## 5. SelectableText 与 Text 的区别

| 特性 | Text | SelectableText |
|------|------|----------------|
| 文本选择 | ❌ 不支持 | ✅ 支持 |
| 复制功能 | ❌ 不支持 | ✅ 支持 |
| 性能 | 更高 | 稍低 |
| 使用场景 | 显示文本 | 需要复制的文本 |

```dart
// Text - 仅显示
Text('普通文本')

// SelectableText - 可选择复制
SelectableText('可选择文本')
```

## 6. 最佳实践

### 6.1 适度使用

```dart
// 不需要选择时使用 Text
Text('标题')  // 更高效

// 需要选择时使用 SelectableText
SelectableText('复制这段内容')
```

### 6.2 提供复制反馈

```dart
SelectableText(
  '可复制的文本',
  onSelectionChanged: (selection, cause) {
    if (cause == SelectionChangedCause.toolbar) {
      // 用户执行了复制操作
    }
  },
)
```

### 6.3 结合上下文

```dart
Column(
  crossAxisAlignment: CrossAxisAlignment.start,
  children: [
    Text('订单号', style: TextStyle(color: Colors.grey)),
    SelectableText(
      '202401010001',
      style: TextStyle(fontWeight: FontWeight.bold),
    ),
  ],
)
```

## 总结

SelectableText 是 Flutter 中提供文本选择功能的核心组件：

**核心要点**：
1. 支持长按/双击选中文本
2. 支持复制到剪贴板
3. 支持 SelectableText.rich 富文本选择
4. 可自定义光标和选中样式

**常用属性**：
- `data` - 文本内容
- `style` - 文本样式
- `onSelectionChanged` - 选择变化回调
- `toolbarOptions` - 工具栏选项

**最佳实践**：
1. 仅在需要选择时使用
2. 提供复制成功的视觉反馈
3. 合理设置选中区域样式
4. 结合业务场景优化体验

---

*最后更新: 2026-02-19*

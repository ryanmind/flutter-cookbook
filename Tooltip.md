# Flutter Tooltip 组件用法详解

## 简介

Tooltip 是提示组件，当用户长按或悬停在元素上时显示提示文字，用于提供额外的说明信息。

## 1. 基本用法

### 1.1 基本概念

Tooltip 的核心特点：
- 长按或悬停显示
- 自动调整位置避免溢出
- 支持自定义样式
- 常用于图标按钮说明

### 1.2 基本语法

```dart
Tooltip(
  message: '这是一个提示',
  child: Icon(Icons.info),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `message` | `String` | - | 提示文字（必需） |
| `child` | `Widget` | - | 子组件（必需） |
| `height` | `double` | `32.0` | 高度 |
| `padding` | `EdgeInsetsGeometry` | `16/8` | 内边距 |
| `margin` | `EdgeInsetsGeometry` | `0` | 外边距 |
| `verticalOffset` | `double` | `24.0` | 垂直偏移 |
| `preferBelow` | `bool` | `true` | 优先显示在下方 |
| `excludeFromSemantics` | `bool` | `false` | 是否排除语义 |
| `decoration` | `Decoration?` | - | 装饰 |
| `textStyle` | `TextStyle?` | - | 文字样式 |
| `textAlign` | `TextAlign` | `start` | 文字对齐 |
| `triggerMode` | `TooltipTriggerMode` | `tap` | 触发方式 |
| `enableFeedback` | `bool` | `true` | 是否启用反馈 |
| `waitDuration` | `Duration?` | - | 等待显示时间 |
| `showDuration` | `Duration?` | - | 显示持续时间 |

### 1.4 基础示例

#### 简单提示

```dart
Tooltip(
  message: '设置',
  child: IconButton(
    icon: Icon(Icons.settings),
    onPressed: () {},
  ),
)
```

#### 文字提示

```dart
Tooltip(
  message: '点击此处查看更多信息',
  child: Text('了解更多'),
)
```

#### 图标提示

```dart
Row(
  children: [
    Text('用户名'),
    Tooltip(
      message: '用户名为 4-16 位字符',
      child: Icon(Icons.help_outline, size: 16),
    ),
  ],
)
```

## 2. 自定义样式

### 2.1 自定义装饰

```dart
Tooltip(
  message: '自定义样式提示',
  decoration: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(8),
    boxShadow: [
      BoxShadow(color: Colors.black26, blurRadius: 4),
    ],
  ),
  child: Icon(Icons.info),
)
```

### 2.2 自定义文字样式

```dart
Tooltip(
  message: '自定义文字样式',
  textStyle: TextStyle(
    color: Colors.white,
    fontSize: 14,
    fontWeight: FontWeight.bold,
  ),
  child: Icon(Icons.info),
)
```

### 2.3 自定义内边距

```dart
Tooltip(
  message: '自定义内边距',
  padding: EdgeInsets.symmetric(horizontal: 16, vertical: 12),
  child: Icon(Icons.info),
)
```

### 2.4 自定义位置偏移

```dart
Tooltip(
  message: '偏移提示',
  verticalOffset: 40,  // 增加垂直距离
  preferBelow: false,  // 优先显示在上方
  child: Icon(Icons.info),
)
```

## 3. 触发方式

### 3.1 长按触发（默认）

```dart
Tooltip(
  message: '长按显示',
  triggerMode: TooltipTriggerMode.longPress,
  child: Icon(Icons.info),
)
```

### 3.2 点击触发

```dart
Tooltip(
  message: '点击显示',
  triggerMode: TooltipTriggerMode.tap,
  child: Icon(Icons.info),
)
```

### 3.3 手动控制

```dart
class ControlledTooltip extends StatefulWidget {
  @override
  _ControlledTooltipState createState() => _ControlledTooltipState();
}

class _ControlledTooltipState extends State<ControlledTooltip> {
  final _tooltipController = TooltipController();
  bool _isVisible = false;

  @override
  void dispose() {
    _tooltipController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        if (_isVisible) {
          _tooltipController.hide();
        } else {
          _tooltipController.show();
        }
        setState(() => _isVisible = !_isVisible);
      },
      child: Tooltip(
        message: '手动控制显示',
        controller: _tooltipController,
        child: Icon(Icons.info),
      ),
    );
  }
}
```

## 4. 常见使用场景

### 4.1 图标按钮说明

```dart
Tooltip(
  message: '添加新项目',
  child: IconButton(
    icon: Icon(Icons.add),
    onPressed: () {},
  ),
)

Tooltip(
  message: '刷新',
  child: IconButton(
    icon: Icon(Icons.refresh),
    onPressed: () {},
  ),
)

Tooltip(
  message: '删除',
  child: IconButton(
    icon: Icon(Icons.delete),
    onPressed: () {},
  ),
)
```

### 4.2 表单字段提示

```dart
class FormFieldWithTooltip extends StatelessWidget {
  final String label;
  final String tooltip;
  final Widget field;

  const FormFieldWithTooltip({
    required this.label,
    required this.tooltip,
    required this.field,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Row(
          children: [
            Text(label),
            SizedBox(width: 4),
            Tooltip(
              message: tooltip,
              child: Icon(Icons.help_outline, size: 16, color: Colors.grey),
            ),
          ],
        ),
        SizedBox(height: 8),
        field,
      ],
    );
  }
}

// 使用
FormFieldWithTooltip(
  label: '密码',
  tooltip: '密码需包含大小写字母和数字，长度至少 8 位',
  field: TextField(obscureText: true),
)
```

### 4.3 数据展示提示

```dart
ListTile(
  title: Text('存储空间'),
  trailing: Row(
    mainAxisSize: MainAxisSize.min,
    children: [
      Text('64GB'),
      Tooltip(
        message: '已使用 32GB，剩余 32GB',
        child: Icon(Icons.info_outline, size: 16),
      ),
    ],
  ),
)
```

### 4.4 工具栏按钮

```dart
class ToolbarWithTooltips extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Tooltip(
          message: '撤销 (Ctrl+Z)',
          child: IconButton(icon: Icon(Icons.undo), onPressed: () {}),
        ),
        Tooltip(
          message: '重做 (Ctrl+Y)',
          child: IconButton(icon: Icon(Icons.redo), onPressed: () {}),
        ),
        Tooltip(
          message: '剪切 (Ctrl+X)',
          child: IconButton(icon: Icon(Icons.cut), onPressed: () {}),
        ),
        Tooltip(
          message: '复制 (Ctrl+C)',
          child: IconButton(icon: Icon(Icons.copy), onPressed: () {}),
        ),
        Tooltip(
          message: '粘贴 (Ctrl+V)',
          child: IconButton(icon: Icon(Icons.paste), onPressed: () {}),
        ),
      ],
    );
  }
}
```

### 4.5 状态指示

```dart
class StatusIndicator extends StatelessWidget {
  final bool isActive;
  final String activeMessage;
  final String inactiveMessage;

  const StatusIndicator({
    required this.isActive,
    required this.activeMessage,
    required this.inactiveMessage,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Tooltip(
      message: isActive ? activeMessage : inactiveMessage,
      child: Container(
        width: 12,
        height: 12,
        decoration: BoxDecoration(
          shape: BoxShape.circle,
          color: isActive ? Colors.green : Colors.grey,
        ),
      ),
    );
  }
}

// 使用
StatusIndicator(
  isActive: true,
  activeMessage: '在线',
  inactiveMessage: '离线',
)
```

## 5. 高级用法

### 5.1 富文本提示

```dart
Tooltip(
  message: '标题\n这是详细描述信息',
  child: Icon(Icons.info),
)
```

### 5.2 多行提示

```dart
Tooltip(
  message: '第一行\n第二行\n第三行',
  padding: EdgeInsets.all(12),
  child: Icon(Icons.info),
)
```

### 5.3 延迟显示

```dart
Tooltip(
  message: '延迟显示',
  waitDuration: Duration(milliseconds: 500),
  showDuration: Duration(seconds: 3),
  child: Icon(Icons.info),
)
```

### 5.4 自定义提示组件

```dart
class CustomTooltip extends StatelessWidget {
  final String title;
  final String? description;
  final Widget child;

  const CustomTooltip({
    required this.title,
    this.description,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Tooltip(
      message: description != null ? '$title\n$description' : title,
      decoration: BoxDecoration(
        color: Colors.black87,
        borderRadius: BorderRadius.circular(8),
      ),
      textStyle: TextStyle(color: Colors.white),
      padding: EdgeInsets.all(12),
      child: child,
    );
  }
}
```

### 5.5 图文提示

```dart
// 使用 Overlay 实现更复杂的提示
class ImageTooltip extends StatefulWidget {
  final String message;
  final String? imageUrl;
  final Widget child;

  const ImageTooltip({
    required this.message,
    this.imageUrl,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  _ImageTooltipState createState() => _ImageTooltipState();
}

class _ImageTooltipState extends State<ImageTooltip> {
  OverlayEntry? _overlayEntry;

  void _showOverlay() {
    _overlayEntry = OverlayEntry(
      builder: (context) => Positioned(
        top: 100,
        left: 100,
        child: Material(
          child: Container(
            width: 200,
            padding: EdgeInsets.all(12),
            decoration: BoxDecoration(
              color: Colors.white,
              borderRadius: BorderRadius.circular(8),
              boxShadow: [BoxShadow(color: Colors.black26, blurRadius: 8)],
            ),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                if (widget.imageUrl != null)
                  Image.network(widget.imageUrl!, height: 100),
                SizedBox(height: 8),
                Text(widget.message),
              ],
            ),
          ),
        ),
      ),
    );
    Overlay.of(context).insert(_overlayEntry!);
  }

  void _hideOverlay() {
    _overlayEntry?.remove();
    _overlayEntry = null;
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTapDown: (_) => _showOverlay(),
      onTapUp: (_) => _hideOverlay(),
      onTapCancel: _hideOverlay,
      child: widget.child,
    );
  }
}
```

## 6. Tooltip vs 其他提示组件

| 组件 | 显示方式 | 持续时间 | 使用场景 |
|------|---------|---------|---------|
| Tooltip | 长按/悬停 | 持续到松开 | 简短说明 |
| SnackBar | 自动 | 可配置 | 操作反馈 |
| Dialog | 手动关闭 | 用户控制 | 重要确认 |
| BottomSheet | 手动关闭 | 用户控制 | 选项/详情 |

## 7. 常见问题与解决方案

### 问题1：提示文字被截断

```dart
Tooltip(
  message: '很长的提示文字会被自动换行显示',
  padding: EdgeInsets.all(12),
  child: Icon(Icons.info),
)
```

### 问题2：提示框位置不正确

```dart
Tooltip(
  message: '提示',
  preferBelow: false,  // 改为显示在上方
  verticalOffset: 20,
  child: Icon(Icons.info),
)
```

### 问题3：提示不显示

```dart
// 确保使用长按而非点击（默认行为）
// 或更改触发模式
Tooltip(
  message: '点击显示',
  triggerMode: TooltipTriggerMode.tap,
  child: Icon(Icons.info),
)
```

### 问题4：提示框背景色不生效

```dart
Tooltip(
  message: '提示',
  decoration: BoxDecoration(
    color: Colors.blue,  // 使用 decoration 设置背景色
  ),
  child: Icon(Icons.info),
)
```

## 8. 最佳实践

### 8.1 保持提示简洁

```dart
// ✅ 好：简洁明了
Tooltip(message: '保存', child: Icon(Icons.save))

// ❌ 不好：过于冗长
Tooltip(message: '点击此按钮可以将当前编辑的内容保存到本地存储', child: Icon(Icons.save))
```

### 8.2 统一提示样式

```dart
class AppTooltip extends StatelessWidget {
  final String message;
  final Widget child;

  const AppTooltip({
    required this.message,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Tooltip(
      message: message,
      decoration: BoxDecoration(
        color: Colors.black87,
        borderRadius: BorderRadius.circular(4),
      ),
      textStyle: TextStyle(color: Colors.white, fontSize: 12),
      child: child,
    );
  }
}
```

### 8.3 为图标按钮添加提示

```dart
// 所有图标按钮都应有提示
IconButton(
  icon: Icon(Icons.settings),
  onPressed: () {},
  tooltip: '设置',  // 或使用 Tooltip 包裹
)
```

### 8.4 无障碍支持

```dart
Tooltip(
  message: '删除选中项',
  excludeFromSemantics: false,  // 包含在语义树中
  child: IconButton(
    icon: Icon(Icons.delete),
    onPressed: () {},
  ),
)
```

## 总结

Tooltip 是 Flutter 中简单但实用的提示组件：

**核心要点**：
1. 长按或悬停显示提示
2. 自动调整位置避免溢出
3. 支持自定义样式
4. 默认长按触发

**常用场景**：
- 图标按钮说明
- 表单字段提示
- 状态指示说明
- 快捷键提示

**最佳实践**：
1. 保持提示文字简洁
2. 统一提示样式
3. 为图标按钮添加提示
4. 支持无障碍访问

---

*最后更新: 2026-02-18*

# Flutter AnimatedPadding 组件用法详解

## 简介

AnimatedPadding 是 Flutter 中用于实现内边距动画的组件。它可以自动将内边距的变化转换为平滑的动画效果。

## 1. 初级用法

### 1.1 基本概念

AnimatedPadding 的核心作用：
- 动画化内边距变化
- 无需 AnimationController
- 适合布局间距过渡

### 1.2 基本语法

```dart
AnimatedPadding(
  padding: _padding,
  duration: Duration(milliseconds: 300),
  child: Container(color: Colors.blue),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `padding` | `EdgeInsetsGeometry` | 内边距 |
| `duration` | `Duration` | 动画时长 |
| `curve` | `Curve` | 动画曲线 |
| `onEnd` | `VoidCallback?` | 动画结束回调 |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 内边距动画

```dart
class PaddingAnimationExample extends StatefulWidget {
  @override
  State<PaddingAnimationExample> createState() => _PaddingAnimationExampleState();
}

class _PaddingAnimationExampleState extends State<PaddingAnimationExample> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Container(
          color: Colors.grey[200],
          child: AnimatedPadding(
            padding: _expanded ? EdgeInsets.all(32) : EdgeInsets.all(8),
            duration: Duration(milliseconds: 300),
            curve: Curves.easeInOut,
            child: Container(
              width: double.infinity,
              height: 100,
              color: Colors.blue,
              child: Center(child: Text('Content')),
            ),
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _expanded = !_expanded),
          child: Text(_expanded ? 'Shrink' : 'Expand'),
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 方向性内边距

```dart
AnimatedPadding(
  padding: EdgeInsets.only(
    left: _isRight ? 50 : 0,
    right: _isRight ? 0 : 50,
  ),
  duration: Duration(milliseconds: 300),
  child: Container(
    height: 100,
    color: Colors.green,
  ),
)
```

### 2.2 对称内边距

```dart
AnimatedPadding(
  padding: EdgeInsets.symmetric(
    horizontal: _isWide ? 32 : 16,
    vertical: _isWide ? 24 : 12,
  ),
  duration: Duration(milliseconds: 300),
  child: Container(
    color: Colors.purple,
    child: Text('Symmetric padding'),
  ),
)
```

### 2.3 悬停效果

```dart
class HoverPadding extends StatefulWidget {
  final Widget child;

  const HoverPadding({required this.child, Key? key}) : super(key: key);

  @override
  State<HoverPadding> createState() => _HoverPaddingState();
}

class _HoverPaddingState extends State<HoverPadding> {
  bool _isHovered = false;

  @override
  Widget build(BuildContext context) {
    return MouseRegion(
      onEnter: (_) => setState(() => _isHovered = true),
      onExit: (_) => setState(() => _isHovered = false),
      child: AnimatedPadding(
        padding: EdgeInsets.all(_isHovered ? 20 : 10),
        duration: Duration(milliseconds: 200),
        child: Container(
          decoration: BoxDecoration(
            color: Colors.blue,
            borderRadius: BorderRadius.circular(8),
          ),
          child: widget.child,
        ),
      ),
    );
  }
}
```

### 2.4 焦点效果

```dart
class FocusPadding extends StatefulWidget {
  @override
  State<FocusPadding> createState() => _FocusPaddingState();
}

class _FocusPaddingState extends State<FocusPadding> {
  final FocusNode _focusNode = FocusNode();
  bool _hasFocus = false;

  @override
  void initState() {
    super.initState();
    _focusNode.addListener(() {
      setState(() => _hasFocus = _focusNode.hasFocus);
    });
  }

  @override
  void dispose() {
    _focusNode.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedPadding(
      padding: EdgeInsets.all(_hasFocus ? 8 : 0),
      duration: Duration(milliseconds: 200),
      child: Container(
        decoration: BoxDecoration(
          border: Border.all(
            color: _hasFocus ? Colors.blue : Colors.grey,
            width: _hasFocus ? 2 : 1,
          ),
          borderRadius: BorderRadius.circular(8),
        ),
        child: TextField(
          focusNode: _focusNode,
          decoration: InputDecoration(
            border: InputBorder.none,
            contentPadding: EdgeInsets.all(12),
          ),
        ),
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 列表项展开

```dart
class ExpandableListItem extends StatefulWidget {
  final String title;
  final String content;

  const ExpandableListItem({
    required this.title,
    required this.content,
    Key? key,
  }) : super(key: key);

  @override
  State<ExpandableListItem> createState() => _ExpandableListItemState();
}

class _ExpandableListItemState extends State<ExpandableListItem> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => setState(() => _expanded = !_expanded),
      child: AnimatedPadding(
        padding: EdgeInsets.all(_expanded ? 24 : 16),
        duration: Duration(milliseconds: 300),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              children: [
                Expanded(child: Text(widget.title, style: TextStyle(fontWeight: FontWeight.bold))),
                Icon(_expanded ? Icons.expand_less : Icons.expand_more),
              ],
            ),
            if (_expanded) ...[
              SizedBox(height: 8),
              Text(widget.content),
            ],
          ],
        ),
      ),
    );
  }
}
```

### 3.2 响应式边距

```dart
class ResponsivePadding extends StatelessWidget {
  final Widget child;

  const ResponsivePadding({required this.child, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final isWide = constraints.maxWidth > 600;
        return AnimatedPadding(
          padding: EdgeInsets.symmetric(
            horizontal: isWide ? 48 : 16,
            vertical: 24,
          ),
          duration: Duration(milliseconds: 300),
          child: child,
        );
      },
    );
  }
}
```

### 3.3 安全区域动画

```dart
class AnimatedSafeArea extends StatefulWidget {
  final Widget child;

  const AnimatedSafeArea({required this.child, Key? key}) : super(key: key);

  @override
  State<AnimatedSafeArea> createState() => _AnimatedSafeAreaState();
}

class _AnimatedSafeAreaState extends State<AnimatedSafeArea> {
  EdgeInsets _safePadding = EdgeInsets.zero;

  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    final mediaQuery = MediaQuery.of(context);
    _safePadding = mediaQuery.padding;
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedPadding(
      padding: _safePadding,
      duration: Duration(milliseconds: 300),
      child: widget.child,
    );
  }
}
```

### 3.4 键盘避让

```dart
class KeyboardAvoidingPadding extends StatefulWidget {
  final Widget child;

  const KeyboardAvoidingPadding({required this.child, Key? key}) : super(key: key);

  @override
  State<KeyboardAvoidingPadding> createState() => _KeyboardAvoidingPaddingState();
}

class _KeyboardAvoidingPaddingState extends State<KeyboardAvoidingPadding> {
  EdgeInsets _padding = EdgeInsets.zero;

  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    final mediaQuery = MediaQuery.of(context);
    _padding = EdgeInsets.only(bottom: mediaQuery.viewInsets.bottom);
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedPadding(
      padding: _padding,
      duration: Duration(milliseconds: 200),
      child: widget.child,
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：动画不触发

```dart
// 确保使用不同的 EdgeInsets 实例
AnimatedPadding(
  padding: EdgeInsets.all(_isExpanded ? 32 : 8),  // 值必须变化
  duration: Duration(milliseconds: 300),
  child: YourWidget(),
)
```

### 问题2：布局溢出

```dart
// 使用 ClipRect 或调整父容器
ClipRect(
  child: AnimatedPadding(
    padding: _padding,
    duration: Duration(milliseconds: 300),
    child: YourWidget(),
  ),
)
```

### 问题3：与 Padding 的区别

```dart
// Padding：静态内边距
Padding(padding: EdgeInsets.all(16), child: Widget())

// AnimatedPadding：动画内边距
AnimatedPadding(
  padding: _padding,  // 变化时会动画
  duration: Duration(milliseconds: 300),
  child: Widget(),
)
```

## 5. AnimatedPadding vs 其他组件

| 组件 | 用途 | 特点 |
|------|------|------|
| **AnimatedPadding** | 内边距动画 | 自动动画 |
| **Padding** | 静态内边距 | 无动画 |
| **AnimatedContainer** | 多属性动画 | 包含 padding |
| **AnimatedMargin** | 不存在 | 使用 AnimatedContainer |

### 选择建议

```dart
// 仅内边距动画 → AnimatedPadding
AnimatedPadding(padding: _padding, duration: ..., child: ...)

// 多属性动画 → AnimatedContainer
AnimatedContainer(
  padding: _padding,
  width: _width,
  color: _color,
  duration: ...,
  child: ...,
)

// 静态内边距 → Padding
Padding(padding: EdgeInsets.all(16), child: ...)
```

## 6. 最佳实践

### 6.1 统一动画配置

```dart
abstract class PaddingConfig {
  static const Duration duration = Duration(milliseconds: 300);
  static const Curve curve = Curves.easeInOut;
  
  static const EdgeInsets compact = EdgeInsets.all(8);
  static const EdgeInsets normal = EdgeInsets.all(16);
  static const EdgeInsets relaxed = EdgeInsets.all(24);
}
```

### 6.2 结合其他动画

```dart
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  decoration: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(_expanded ? 16 : 8),
  ),
  child: AnimatedPadding(
    padding: _expanded ? EdgeInsets.all(24) : EdgeInsets.all(12),
    duration: Duration(milliseconds: 300),
    child: YourWidget(),
  ),
)
```

### 6.3 条件内边距

```dart
AnimatedPadding(
  padding: EdgeInsets.only(
    left: _alignLeft ? 0 : 50,
    right: _alignLeft ? 50 : 0,
    top: _hasHeader ? 60 : 0,
  ),
  duration: Duration(milliseconds: 300),
  child: YourWidget(),
)
```

## 总结

AnimatedPadding 是实现内边距动画的便捷工具：

**核心要点**：
1. 自动动画化内边距变化
2. 支持所有 EdgeInsets 类型
3. 简单易用，无需 Controller

**最佳实践**：
1. 统一动画时长和曲线
2. 结合其他动画组件
3. 用于响应式布局

**常见场景**：
- 展开/折叠
- 悬停效果
- 响应式边距
- 键盘避让

---

*最后更新: 2026-02-19*

# Flutter 按钮组件用法详解

## 简介

Flutter Material 3 提供了 5 种按钮类型，按视觉强调程度排序：FilledButton、ElevatedButton、OutlinedButton、TextButton 和 IconButton。每种按钮都有其特定的使用场景。

## 1. 初级用法

### 1.1 基本概念

按钮的核心特点：
- 提供用户交互入口
- Material 3 有清晰的视觉层级
- 支持图标、禁用状态
- 可自定义样式

### 1.2 按钮类型对比

| 按钮类型 | 视觉强调 | 使用场景 |
|---------|---------|---------|
| FilledButton | 最高 | 主要操作，一个页面通常只有一个 |
| FilledButton.tonal | 高 | 次要但重要的操作 |
| ElevatedButton | 中高 | 需要层次感的主要操作 |
| OutlinedButton | 中 | 次要操作 |
| TextButton | 最低 | 不重要或辅助操作 |

### 1.3 基本语法

```dart
// FilledButton - 填充按钮
FilledButton(
  onPressed: () {},
  child: Text('填充按钮'),
)

// ElevatedButton - 悬浮按钮
ElevatedButton(
  onPressed: () {},
  child: Text('悬浮按钮'),
)

// OutlinedButton - 边框按钮
OutlinedButton(
  onPressed: () {},
  child: Text('边框按钮'),
)

// TextButton - 文本按钮
TextButton(
  onPressed: () {},
  child: Text('文本按钮'),
)

// IconButton - 图标按钮
IconButton(
  onPressed: () {},
  icon: Icon(Icons.favorite),
)
```

### 1.4 主要属性

#### FilledButton / ElevatedButton / OutlinedButton / TextButton

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `onPressed` | `VoidCallback?` | - | 点击回调，null 时禁用 |
| `onLongPress` | `VoidCallback?` | - | 长按回调 |
| `child` | `Widget?` | - | 子组件（通常为 Text） |
| `style` | `ButtonStyle?` | - | 按钮样式 |
| `focusNode` | `FocusNode?` | - | 焦点节点 |
| `autofocus` | `bool` | `false` | 自动获取焦点 |
| `clipBehavior` | `Clip` | `Clip.none` | 裁剪行为 |

#### IconButton

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `onPressed` | `VoidCallback?` | - | 点击回调 |
| `icon` | `Widget` | - | 图标组件 |
| `iconSize` | `double?` | `24.0` | 图标大小 |
| `color` | `Color?` | - | 图标颜色 |
| `tooltip` | `String?` | - | 长按提示 |
| `style` | `ButtonStyle?` | - | 按钮样式 |

### 1.5 基础示例

#### 禁用状态

```dart
// onPressed 为 null 时自动禁用
FilledButton(
  onPressed: null,
  child: Text('禁用按钮'),
)

// 条件禁用
FilledButton(
  onPressed: isEnabled ? () {} : null,
  child: Text('条件禁用'),
)
```

#### 带图标按钮

```dart
FilledButton.icon(
  onPressed: () {},
  icon: Icon(Icons.add),
  label: Text('添加'),
)

ElevatedButton.icon(
  onPressed: () {},
  icon: Icon(Icons.send),
  label: Text('发送'),
)

OutlinedButton.icon(
  onPressed: () {},
  icon: Icon(Icons.edit),
  label: Text('编辑'),
)

TextButton.icon(
  onPressed: () {},
  icon: Icon(Icons.arrow_forward),
  label: Text('下一步'),
)
```

## 2. 中级用法

### 2.1 FilledButton 变体

```dart
// Filled - 实心填充
FilledButton(
  onPressed: () {},
  child: Text('Filled'),
)

// Tonal - 色调填充
FilledButton.tonal(
  onPressed: () {},
  child: Text('Tonal'),
)
```

### 2.2 自定义样式

```dart
FilledButton(
  onPressed: () {},
  style: FilledButton.styleFrom(
    backgroundColor: Colors.red,
    foregroundColor: Colors.white,
    padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(8),
    ),
  ),
  child: Text('自定义样式'),
)

ElevatedButton(
  onPressed: () {},
  style: ElevatedButton.styleFrom(
    elevation: 8,
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
    minimumSize: Size(200, 50),
  ),
  child: Text('自定义悬浮按钮'),
)

OutlinedButton(
  onPressed: () {},
  style: OutlinedButton.styleFrom(
    foregroundColor: Colors.purple,
    side: BorderSide(color: Colors.purple, width: 2),
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(24),
    ),
  ),
  child: Text('自定义边框按钮'),
)
```

### 2.3 按钮状态样式

```dart
FilledButton(
  onPressed: () {},
  style: ButtonStyle(
    backgroundColor: WidgetStateProperty.resolveWith((states) {
      if (states.contains(WidgetState.disabled)) {
        return Colors.grey;
      }
      if (states.contains(WidgetState.pressed)) {
        return Colors.blue[700];
      }
      if (states.contains(WidgetState.hovered)) {
        return Colors.blue[300];
      }
      return Colors.blue;
    }),
    foregroundColor: WidgetStateProperty.all(Colors.white),
    overlayColor: WidgetStateProperty.all(Colors.white.withValues(alpha: 0.1)),
  ),
  child: Text('状态样式'),
)
```

### 2.4 IconButton 变体

```dart
// 标准 IconButton
IconButton(
  onPressed: () {},
  icon: Icon(Icons.favorite),
)

// Filled IconButton
IconButton.filled(
  onPressed: () {},
  icon: Icon(Icons.favorite),
)

// Filled Tonal IconButton
IconButton.filledTonal(
  onPressed: () {},
  icon: Icon(Icons.favorite),
)

// Outlined IconButton
IconButton.outlined(
  onPressed: () {},
  icon: Icon(Icons.favorite),
)

// 带 tooltip
IconButton(
  onPressed: () {},
  icon: Icon(Icons.settings),
  tooltip: '设置',
)
```

### 2.5 按钮组

```dart
Row(
  children: [
    TextButton(
      onPressed: () {},
      child: Text('取消'),
    ),
    SizedBox(width: 8),
    FilledButton(
      onPressed: () {},
      child: Text('确定'),
    ),
  ],
)

// 使用 OverflowBar 自动处理溢出
OverflowBar(
  alignment: MainAxisAlignment.end,
  children: [
    TextButton(child: Text('取消'), onPressed: () {}),
    TextButton(child: Text('稍后'), onPressed: () {}),
    FilledButton(child: Text('确定'), onPressed: () {}),
  ],
)
```

## 3. 高级用法

### 3.1 加载按钮

```dart
class LoadingButton extends StatefulWidget {
  final String text;
  final Future<void> Function() onPressed;

  const LoadingButton({
    required this.text,
    required this.onPressed,
    Key? key,
  }) : super(key: key);

  @override
  _LoadingButtonState createState() => _LoadingButtonState();
}

class _LoadingButtonState extends State<LoadingButton> {
  bool _isLoading = false;

  Future<void> _handlePress() async {
    if (_isLoading) return;
    
    setState(() => _isLoading = true);
    try {
      await widget.onPressed();
    } finally {
      if (mounted) {
        setState(() => _isLoading = false);
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return FilledButton(
      onPressed: _isLoading ? null : _handlePress,
      child: _isLoading
          ? SizedBox(
              width: 20,
              height: 20,
              child: CircularProgressIndicator(
                strokeWidth: 2,
                color: Colors.white,
              ),
            )
          : Text(widget.text),
    );
  }
}

// 使用示例
LoadingButton(
  text: '提交',
  onPressed: () async {
    await Future.delayed(Duration(seconds: 2));
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text('提交成功')),
    );
  },
)
```

### 3.2 渐变按钮

```dart
class GradientButton extends StatelessWidget {
  final String text;
  final VoidCallback onPressed;
  final List<Color> colors;

  const GradientButton({
    required this.text,
    required this.onPressed,
    this.colors = const [Colors.blue, Colors.purple],
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onPressed,
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
        decoration: BoxDecoration(
          gradient: LinearGradient(colors: colors),
          borderRadius: BorderRadius.circular(8),
        ),
        child: Text(
          text,
          style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold),
        ),
      ),
    );
  }
}

// 使用示例
GradientButton(
  text: '渐变按钮',
  onPressed: () {},
  colors: [Colors.orange, Colors.red],
)
```

### 3.3 倒计时按钮

```dart
class CountdownButton extends StatefulWidget {
  final int seconds;
  final VoidCallback onPressed;

  const CountdownButton({
    required this.seconds,
    required this.onPressed,
    Key? key,
  }) : super(key: key);

  @override
  _CountdownButtonState createState() => _CountdownButtonState();
}

class _CountdownButtonState extends State<CountdownButton> {
  late int _countdown;
  bool _isCounting = false;

  @override
  void initState() {
    super.initState();
    _countdown = widget.seconds;
  }

  void _startCountdown() {
    if (_isCounting) return;
    
    widget.onPressed();
    setState(() => _isCounting = true);
    
    Timer.periodic(Duration(seconds: 1), (timer) {
      if (_countdown > 1) {
        setState(() => _countdown--);
      } else {
        timer.cancel();
        setState(() {
          _isCounting = false;
          _countdown = widget.seconds;
        });
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return TextButton(
      onPressed: _isCounting ? null : _startCountdown,
      child: Text(_isCounting ? '$_countdown 秒后重试' : '获取验证码'),
    );
  }
}
```

### 3.4 按钮组选择器

```dart
class ButtonGroupSelector extends StatefulWidget {
  final List<String> options;
  final int initialIndex;
  final ValueChanged<int> onChanged;

  const ButtonGroupSelector({
    required this.options,
    this.initialIndex = 0,
    required this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _ButtonGroupSelectorState createState() => _ButtonGroupSelectorState();
}

class _ButtonGroupSelectorState extends State<ButtonGroupSelector> {
  late int _selectedIndex;

  @override
  void initState() {
    super.initState();
    _selectedIndex = widget.initialIndex;
  }

  @override
  Widget build(BuildContext context) {
    return Row(
      children: widget.options.asMap().entries.map((entry) {
        final index = entry.key;
        final text = entry.value;
        final isSelected = index == _selectedIndex;

        return Padding(
          padding: EdgeInsets.only(right: 8),
          child: isSelected
              ? FilledButton(
                  onPressed: () {
                    setState(() => _selectedIndex = index);
                    widget.onChanged(index);
                  },
                  child: Text(text),
                )
              : OutlinedButton(
                  onPressed: () {
                    setState(() => _selectedIndex = index);
                    widget.onChanged(index);
                  },
                  child: Text(text),
                ),
        );
      }).toList(),
    );
  }
}

// 使用示例
ButtonGroupSelector(
  options: ['日', '周', '月', '年'],
  onChanged: (index) {
    print('选中: $index');
  },
)
```

### 3.5 FloatingActionButton

```dart
// 标准 FAB
FloatingActionButton(
  onPressed: () {},
  child: Icon(Icons.add),
)

// 小型 FAB
FloatingActionButton.small(
  onPressed: () {},
  child: Icon(Icons.add),
)

// 大型 FAB
FloatingActionButton.large(
  onPressed: () {},
  child: Icon(Icons.add),
)

// 扩展 FAB
FloatingActionButton.extended(
  onPressed: () {},
  icon: Icon(Icons.add),
  label: Text('创建'),
)

// 在 Scaffold 中使用
Scaffold(
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
  floatingActionButtonLocation: FloatingActionButtonLocation.endFloat,
)
```

## 4. 性能优化

### 4.1 使用 const

```dart
// 静态按钮使用 const
const TextButton(
  onPressed: null,
  child: Text('静态文本'),
)
```

### 4.2 避免在 build 中创建样式

```dart
// ❌ 不好：每次 build 都创建新样式
FilledButton(
  style: FilledButton.styleFrom(backgroundColor: Colors.blue),
  onPressed: () {},
  child: Text('按钮'),
)

// ✅ 好：复用样式
class _MyWidgetState extends State<MyWidget> {
  late final ButtonStyle _buttonStyle;

  @override
  void initState() {
    super.initState();
    _buttonStyle = FilledButton.styleFrom(backgroundColor: Colors.blue);
  }

  @override
  Widget build(BuildContext context) {
    return FilledButton(
      style: _buttonStyle,
      onPressed: () {},
      child: Text('按钮'),
    );
  }
}
```

### 4.3 使用 ButtonStyle.from

```dart
// 复用主题样式
final baseStyle = FilledButton.styleFrom();

FilledButton(
  style: baseStyle.copyWith(
    backgroundColor: WidgetStateProperty.all(Colors.red),
  ),
  onPressed: () {},
  child: Text('按钮'),
)
```

## 5. 常见问题与解决方案

### 问题1：按钮宽度不满足需求

```dart
// ❌ 问题：按钮宽度自适应内容
FilledButton(
  onPressed: () {},
  child: Text('按钮'),
)

// ✅ 解决方案1：使用 SizedBox
SizedBox(
  width: double.infinity,
  child: FilledButton(
    onPressed: () {},
    child: Text('全宽按钮'),
  ),
)

// ✅ 解决方案2：使用 minimumSize
FilledButton(
  style: FilledButton.styleFrom(
    minimumSize: Size(double.infinity, 48),
  ),
  onPressed: () {},
  child: Text('全宽按钮'),
)
```

### 问题2：按钮内边距不合适

```dart
FilledButton(
  style: FilledButton.styleFrom(
    padding: EdgeInsets.symmetric(horizontal: 32, vertical: 16),
  ),
  onPressed: () {},
  child: Text('自定义内边距'),
)
```

### 问题3：图标和文字间距

```dart
// 使用 icon 构造函数自动处理间距
FilledButton.icon(
  onPressed: () {},
  icon: Icon(Icons.add),
  label: Text('添加'),
)

// 手动控制间距
FilledButton(
  onPressed: () {},
  child: Row(
    mainAxisSize: MainAxisSize.min,
    children: [
      Icon(Icons.add, size: 18),
      SizedBox(width: 8),
      Text('添加'),
    ],
  ),
)
```

### 问题4：按钮禁用时样式

```dart
FilledButton(
  style: FilledButton.styleFrom(
    disabledBackgroundColor: Colors.grey[300],
    disabledForegroundColor: Colors.grey[600],
  ),
  onPressed: null,
  child: Text('禁用按钮'),
)
```

## 6. 按钮类型对比

| 特性 | Filled | Elevated | Outlined | Text |
|------|--------|----------|----------|------|
| 背景 | 实心 | 渐变+阴影 | 无 | 无 |
| 边框 | 无 | 无 | 有 | 无 |
| 阴影 | 无 | 有 | 无 | 无 |
| 强调度 | 最高 | 高 | 中 | 低 |

```dart
Column(
  children: [
    FilledButton(onPressed: () {}, child: Text('Filled')),
    SizedBox(height: 8),
    ElevatedButton(onPressed: () {}, child: Text('Elevated')),
    SizedBox(height: 8),
    OutlinedButton(onPressed: () {}, child: Text('Outlined')),
    SizedBox(height: 8),
    TextButton(onPressed: () {}, child: Text('Text')),
  ],
)
```

## 7. 最佳实践

### 7.1 按钮层级使用

```dart
// 一个主要操作 + 多个次要操作
Row(
  children: [
    TextButton(child: Text('取消'), onPressed: () {}),
    TextButton(child: Text('保存草稿'), onPressed: () {}),
    FilledButton(child: Text('发布'), onPressed: () {}),
  ],
)
```

### 7.2 统一按钮样式

```dart
class AppButton {
  static ButtonStyle primaryStyle() => FilledButton.styleFrom(
    minimumSize: Size(120, 48),
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(8),
    ),
  );

  static ButtonStyle secondaryStyle() => OutlinedButton.styleFrom(
    minimumSize: Size(120, 48),
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(8),
    ),
  );
}

FilledButton(
  style: AppButton.primaryStyle(),
  onPressed: () {},
  child: Text('主要按钮'),
)
```

### 7.3 响应式按钮

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final isWide = constraints.maxWidth > 400;
    
    return isWide
        ? Row(
            children: [
              Expanded(child: OutlinedButton(child: Text('取消'), onPressed: () {})),
              SizedBox(width: 16),
              Expanded(child: FilledButton(child: Text('确定'), onPressed: () {})),
            ],
          )
        : Column(
            children: [
              SizedBox(
                width: double.infinity,
                child: FilledButton(child: Text('确定'), onPressed: () {}),
              ),
              SizedBox(height: 8),
              SizedBox(
                width: double.infinity,
                child: OutlinedButton(child: Text('取消'), onPressed: () {}),
              ),
            ],
          );
  },
)
```

## 总结

Flutter Material 3 按钮组件提供了清晰的视觉层级：

**核心要点**：
1. 5 种按钮类型对应不同强调程度
2. 使用 `.icon` 构造函数添加图标
3. `onPressed` 为 null 时自动禁用
4. 通过 `ButtonStyle` 自定义样式

**选择指南**：
- **FilledButton** - 主要操作（如提交、确认）
- **OutlinedButton** - 次要操作（如取消、返回）
- **TextButton** - 辅助操作（如"了解更多"）
- **IconButton** - 工具栏操作
- **FloatingActionButton** - 页面主要操作入口

**最佳实践**：
1. 每个页面只突出一个主要按钮
2. 统一按钮样式和尺寸
3. 合理使用禁用状态
4. 提供适当的视觉反馈

---

*最后更新: 2026-02-18*

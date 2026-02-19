# Flutter 无障碍支持

## 概述

无障碍（Accessibility）确保应用对所有用户可用，包括视觉、听觉、运动障碍用户。

## 语义化基础

### Semantics Widget

```dart
// 为组件添加语义信息
Semantics(
  label: '提交按钮',
  hint: '点击提交表单',
  button: true,
  enabled: true,
  onTap: () => submitForm(),
  child: ElevatedButton(
    onPressed: () => submitForm(),
    child: Text('提交'),
  ),
)
```

### 常用语义属性

```dart
Semantics(
  // 基本属性
  label: '用户头像',           // 元素名称
  hint: '点击查看个人资料',      // 操作提示
  value: '已选择',             // 当前值

  // 类型标识
  button: true,               // 按钮
  link: true,                 // 链接
  image: true,                // 图片
  header: true,               // 标题

  // 状态标识
  enabled: true,              // 是否可用
  checked: true,              // 是否选中
  selected: true,             // 是否选择
  expanded: true,             // 是否展开
  hidden: false,              // 是否隐藏

  // 交互
  onTap: () {},               // 点击
  onLongPress: () {},         // 长按
  onScrollLeft: () {},        // 左滑
  onScrollRight: () {},       // 右滑

  child: Container(),
)
```

## 组件语义化

### 文本组件

```dart
// 图片需要描述
Image.network(
  'https://example.com/photo.jpg',
  semanticLabel: '产品展示图',
)

// 纯装饰图片隐藏语义
Image.asset(
  'assets/decoration.png',
  excludeFromSemantics: true,
)

// 文本组件
Text(
  '欢迎回来',
  semanticsLabel: '欢迎回来，用户', // 自定义语义文本
)
```

### 按钮和交互

```dart
// IconButton 需要语义标签
IconButton(
  icon: Icon(Icons.search),
  tooltip: '搜索',  // 同时作为语义标签
  onPressed: () {},
)

// 自定义按钮
Semantics(
  label: '播放视频',
  hint: '双击播放',
  button: true,
  child: GestureDetector(
    onTap: () => playVideo(),
    child: Container(
      padding: EdgeInsets.all(16),
      child: Icon(Icons.play_arrow),
    ),
  ),
)
```

### 表单组件

```dart
// TextField 自带语义
TextField(
  decoration: InputDecoration(
    labelText: '用户名',  // 作为语义标签
    hintText: '请输入用户名',
  ),
)

// 自定义表单标签
Semantics(
  label: '密码输入框',
  hint: '请输入6-12位密码',
  textField: true,
  child: TextField(
    obscureText: true,
    decoration: InputDecoration(
      labelText: '密码',
    ),
  ),
)

// 复选框
Checkbox(
  value: isChecked,
  semanticLabel: '同意用户协议',
  onChanged: (value) {},
)

// 开关
Switch(
  value: isOn,
  semanticLabel: '深色模式',
  onChanged: (value) {},
)
```

### 列表项

```dart
// ListTile 自带语义
ListTile(
  leading: Icon(Icons.person),
  title: Text('张三'),
  subtitle: Text('软件工程师'),
  onTap: () {},
)

// 自定义列表项
Semantics(
  label: '消息：张三',
  hint: '点击查看详情',
  child: Container(
    child: Row(
      children: [
        CircleAvatar(child: Text('张')),
        Column(
          children: [
            Text('张三'),
            Text('你好，最近怎么样？'),
          ],
        ),
      ],
    ),
  ),
)
```

### 滑块和进度条

```dart
// Slider
Slider(
  value: volume,
  min: 0,
  max: 100,
  semanticFormatterCallback: (value) => '音量 ${value.toInt()}%',
  onChanged: (value) {},
)

// 进度条
LinearProgressIndicator(
  semanticsLabel: '下载进度',
  semanticsValue: '已完成 70%',
  value: 0.7,
)
```

## 语义容器

### SemanticsContainer

```dart
// 合并子组件语义
Semantics(
  container: true,  // 创建语义容器
  label: '用户卡片',
  child: Card(
    child: Column(
      children: [
        Text('张三'),
        Text('软件工程师'),
      ],
    ),
  ),
)
```

### MergeSemantics

```dart
// 合并多个组件的语义为一个整体
MergeSemantics(
  child: ListTile(
    leading: Icon(Icons.phone),
    title: Text('电话'),
    subtitle: Text('123-456-7890'),
  ),
)
```

### ExcludeSemantics

```dart
// 排除语义（装饰性元素）
ExcludeSemantics(
  child: Container(
    decoration: BoxDecoration(
      gradient: LinearGradient(...),
    ),
    child: Text('装饰性背景'),
  ),
)
```

### BlockSemantics

```dart
// 阻塞背后的语义
BlockSemantics(
  child: ModalBarrier(
    dismissible: true,
    onDismiss: () {},
  ),
)
```

## 自定义语义行为

### SemanticsController

```dart
class CustomSemanticsWidget extends StatefulWidget {
  @override
  _CustomSemanticsWidgetState createState() => _CustomSemanticsWidgetState();
}

class _CustomSemanticsWidgetState extends State<CustomSemanticsWidget> {
  int _count = 0;

  void _increment() {
    setState(() => _count++);
  }

  @override
  Widget build(BuildContext context) {
    return Semantics(
      label: '计数器',
      value: '当前值: $_count',
      hint: '点击增加',
      increasedValue: '增加到 ${_count + 1}',
      decreasedValue: '减少到 ${_count - 1}',
      onIncrease: _increment,
      onDecrease: () => setState(() => _count--),
      child: GestureDetector(
        onTap: _increment,
        child: Container(
          padding: EdgeInsets.all(16),
          child: Text('$_count'),
        ),
      ),
    );
  }
}
```

### 自定义动作

```dart
Semantics(
  label: '可调节亮度',
  value: '当前亮度 50%',
  customSemanticsActions: {
    CustomSemanticsAction(label: '最大亮度'): () => setBrightness(100),
    CustomSemanticsAction(label: '最小亮度'): () => setBrightness(0),
  },
  child: Slider(...),
)
```

## 焦点管理

### FocusNode

```dart
class FocusExample extends StatefulWidget {
  @override
  _FocusExampleState createState() => _FocusExampleState();
}

class _FocusExampleState extends State<FocusExample> {
  final FocusNode _buttonFocusNode = FocusNode();

  @override
  void dispose() {
    _buttonFocusNode.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        ElevatedButton(
          focusNode: _buttonFocusNode,
          onPressed: () {},
          child: Text('按钮'),
        ),
        ElevatedButton(
          onPressed: () {
            _buttonFocusNode.requestFocus(); // 手动设置焦点
          },
          child: Text('聚焦到按钮'),
        ),
      ],
    );
  }
}
```

### FocusTraversalGroup

```dart
// 控制焦点遍历顺序
FocusTraversalGroup(
  policy: OrderedTraversalPolicy(),
  child: Column(
    children: [
      FocusTraversalOrder(
        order: NumericFocusOrder(1),
        child: TextField(decoration: InputDecoration(labelText: '姓名')),
      ),
      FocusTraversalOrder(
        order: NumericFocusOrder(2),
        child: TextField(decoration: InputDecoration(labelText: '邮箱')),
      ),
      FocusTraversalOrder(
        order: NumericFocusOrder(3),
        child: TextField(decoration: InputDecoration(labelText: '电话')),
      ),
    ],
  ),
)
```

## 颜色对比度

### WCAG 标准

```dart
// WCAG 2.0 AA 标准：
// - 普通文本：对比度至少 4.5:1
// - 大号文本：对比度至少 3:1

// 检查对比度
bool isContrastValid(Color foreground, Color background) {
  final luminance1 = foreground.computeLuminance();
  final luminance2 = background.computeLuminance();

  final lighter = luminance1 > luminance2 ? luminance1 : luminance2;
  final darker = luminance1 > luminance2 ? luminance2 : luminance1;

  final ratio = (lighter + 0.05) / (darker + 0.05);
  return ratio >= 4.5; // AA 标准
}

// 使用
Text(
  '重要提示',
  style: TextStyle(
    color: Colors.white,
    backgroundColor: Colors.blue, // 确保对比度足够
  ),
)
```

## 屏幕阅读器适配

### Live Region

```dart
// 动态内容更新通知
Semantics(
  liveRegion: true,  // 内容变化时自动播报
  child: Text(_statusMessage),
)

// 使用示例
class StatusWidget extends StatefulWidget {
  @override
  _StatusWidgetState createState() => _StatusWidgetState();
}

class _StatusWidgetState extends State<StatusWidget> {
  String _status = '准备就绪';

  void updateStatus(String newStatus) {
    setState(() => _status = newStatus);
    // 屏幕阅读器会自动播报新内容
  }

  @override
  Widget build(BuildContext context) {
    return Semantics(
      liveRegion: true,
      child: Text(_status),
    );
  }
}
```

### 公告播报

```dart
// 手动触发播报
import 'package:flutter/semantics.dart';

void announceMessage(BuildContext context, String message) {
  SemanticsService.announce(message, TextDirection.ltr);
}

// 使用
void onItemDeleted() {
  SemanticsService.announce('项目已删除', TextDirection.ltr);
}
```

## 测试无障碍

### 语义测试

```dart
testWidgets('语义测试', (tester) async {
  await tester.pumpWidget(
    MaterialApp(
      home: Scaffold(
        body: Semantics(
          label: '测试按钮',
          button: true,
          child: ElevatedButton(
            onPressed: () {},
            child: Text('按钮'),
          ),
        ),
      ),
    ),
  );

  // 验证语义
  expect(
    find.bySemanticsLabel('测试按钮'),
    findsOneWidget,
  );
});
```

### 调试语义

```dart
// 启用语义调试
MaterialApp(
  showSemanticsDebugger: true,  // 显示语义树
  home: MyHomePage(),
);
```

## 最佳实践

### 1. 所有交互元素可访问

```dart
// ✅ 好：提供语义标签
IconButton(
  icon: Icon(Icons.settings),
  tooltip: '设置',
  onPressed: () {},
)

// ❌ 差：无语义信息
GestureDetector(
  onTap: () {},
  child: Icon(Icons.settings),
)
```

### 2. 图片提供描述

```dart
// ✅ 有意义的图片
Image.asset(
  'assets/logo.png',
  semanticLabel: '公司标志',
)

// ✅ 装饰性图片
Image.asset(
  'assets/background.png',
  excludeFromSemantics: true,
)
```

### 3. 表单关联标签

```dart
// ✅ 使用 InputDecoration
TextField(
  decoration: InputDecoration(
    labelText: '邮箱地址',
    errorText: emailError,
  ),
)
```

### 4. 动态内容通知

```dart
// ✅ 更新时通知
setState(() {
  _message = '操作成功';
});
SemanticsService.announce('操作成功', TextDirection.ltr);
```

### 5. 焦点可见性

```dart
// 确保焦点状态可见
Focus(
  child: Builder(
    builder: (context) {
      final hasFocus = Focus.of(context).hasFocus;
      return Container(
        decoration: BoxDecoration(
          border: hasFocus ? Border.all(color: Colors.blue, width: 2) : null,
        ),
        child: Text('可聚焦元素'),
      );
    },
  ),
)
```

---

*最后更新: 2026-02-19*

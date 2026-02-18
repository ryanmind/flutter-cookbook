# Flutter AlertDialog 组件用法详解

## 简介

AlertDialog 是警告对话框组件，用于显示重要信息或需要用户确认的操作。Material 3 中 AlertDialog 有了全新的设计风格。

## 1. 初级用法

### 1.1 基本概念

AlertDialog 的核心特点：
- 模态对话框
- 显示标题、内容和操作按钮
- 支持滚动内容
- Material 3 新样式

### 1.2 基本语法

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('提示'),
    content: Text('确定要执行此操作吗？'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('取消'),
      ),
      TextButton(
        onPressed: () {
          // 执行操作
          Navigator.pop(context);
        },
        child: Text('确定'),
      ),
    ],
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `title` | `Widget?` | - | 标题 |
| `titlePadding` | `EdgeInsetsGeometry?` | - | 标题内边距 |
| `titleTextStyle` | `TextStyle?` | - | 标题样式 |
| `content` | `Widget?` | - | 内容 |
| `contentPadding` | `EdgeInsetsGeometry?` | - | 内容内边距 |
| `contentTextStyle` | `TextStyle?` | - | 内容样式 |
| `actions` | `List<Widget>?` | - | 操作按钮列表 |
| `actionsPadding` | `EdgeInsetsGeometry?` | - | 操作按钮内边距 |
| `actionsAlignment` | `MainAxisAlignment?` | - | 操作按钮对齐 |
| `actionsOverflowAlignment` | `OverflowBarAlignment` | - | 按钮溢出对齐 |
| `actionsOverflowDirection` | `VerticalDirection?` | - | 按钮溢出方向 |
| `actionsOverflowButtonSpacing` | `double?` | - | 按钮溢出间距 |
| `buttonPadding` | `EdgeInsetsGeometry?` | - | 按钮内边距 |
| `backgroundColor` | `Color?` | - | 背景色 |
| `elevation` | `double?` | - | 阴影高度 |
| `shadowColor` | `Color?` | - | 阴影颜色 |
| `surfaceTintColor` | `Color?` | - | 表面着色 |
| `semanticLabel` | `String?` | - | 语义标签 |
| `insetPadding` | `EdgeInsetsGeometry?` | - | 外边距 |
| `clipBehavior` | `Clip` | `Clip.none` | 裁剪行为 |
| `shape` | `ShapeBorder?` | - | 形状 |
| `alignment` | `AlignmentGeometry?` | - | 对齐方式 |
| `scrollable` | `bool` | `false` | 是否可滚动 |
| `icon` | `Widget?` | - | 图标 |
| `iconColor` | `Color?` | - | 图标颜色 |
| `iconPadding` | `EdgeInsetsGeometry?` | - | 图标内边距 |
| `iconSize` | `double?` | - | 图标大小 |

### 1.4 基础示例

#### 简单确认对话框

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('删除确认'),
    content: Text('确定要删除此项目吗？此操作无法撤销。'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('取消'),
      ),
      TextButton(
        onPressed: () {
          Navigator.pop(context, true);
        },
        child: Text('删除'),
      ),
    ],
  ),
);
```

#### 带图标的对话框

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    icon: Icon(Icons.warning, color: Colors.orange, size: 48),
    title: Text('警告'),
    content: Text('这是一个警告信息'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('知道了'),
      ),
    ],
  ),
)
```

#### 只有内容的对话框

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    content: Text('这是一个简单的提示信息'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('确定'),
      ),
    ],
  ),
)
```

## 2. 中级用法

### 2.1 获取对话框返回值

```dart
final result = await showDialog<bool>(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('确认'),
    content: Text('确定要继续吗？'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context, false),
        child: Text('取消'),
      ),
      TextButton(
        onPressed: () => Navigator.pop(context, true),
        child: Text('确定'),
      ),
    ],
  ),
);

if (result == true) {
  print('用户点击了确定');
}
```

### 2.2 自定义样式

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('自定义样式'),
    content: Text('这是一个自定义样式的对话框'),
    backgroundColor: Colors.blue[50],
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(20),
    ),
    titleTextStyle: TextStyle(
      color: Colors.blue,
      fontSize: 20,
      fontWeight: FontWeight.bold,
    ),
    contentTextStyle: TextStyle(
      color: Colors.black87,
      fontSize: 16,
    ),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('关闭'),
      ),
    ],
  ),
)
```

### 2.3 可滚动内容

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('用户协议'),
    content: SingleChildScrollView(
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        mainAxisSize: MainAxisSize.min,
        children: List.generate(20, (index) {
          return Padding(
            padding: EdgeInsets.only(bottom: 8),
            child: Text('第 $index 条条款内容...'),
          );
        }),
      ),
    ),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context, false),
        child: Text('不同意'),
      ),
      TextButton(
        onPressed: () => Navigator.pop(context, true),
        child: Text('同意'),
      ),
    ],
  ),
)
```

### 2.4 多按钮对话框

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('选择操作'),
    content: Text('请选择您要执行的操作'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context, 'save'),
        child: Text('保存'),
      ),
      TextButton(
        onPressed: () => Navigator.pop(context, 'discard'),
        child: Text('放弃'),
      ),
      TextButton(
        onPressed: () => Navigator.pop(context, 'cancel'),
        child: Text('取消'),
      ),
    ],
  ),
)
```

### 2.5 对话框位置

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('顶部对齐'),
    content: Text('此对话框显示在屏幕顶部'),
    alignment: Alignment.topCenter,
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('关闭'),
      ),
    ],
  ),
)
```

## 3. 高级用法

### 3.1 表单对话框

```dart
class FormDialog extends StatefulWidget {
  @override
  _FormDialogState createState() => _FormDialogState();
}

class _FormDialogState extends State<FormDialog> {
  final _formKey = GlobalKey<FormState>();
  final _nameController = TextEditingController();
  final _emailController = TextEditingController();

  @override
  void dispose() {
    _nameController.dispose();
    _emailController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AlertDialog(
      title: Text('添加用户'),
      content: Form(
        key: _formKey,
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextFormField(
              controller: _nameController,
              decoration: InputDecoration(labelText: '姓名'),
              validator: (value) {
                if (value == null || value.isEmpty) {
                  return '请输入姓名';
                }
                return null;
              },
            ),
            TextFormField(
              controller: _emailController,
              decoration: InputDecoration(labelText: '邮箱'),
              validator: (value) {
                if (value == null || !value.contains('@')) {
                  return '请输入有效邮箱';
                }
                return null;
              },
            ),
          ],
        ),
      ),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context),
          child: Text('取消'),
        ),
        TextButton(
          onPressed: () {
            if (_formKey.currentState!.validate()) {
              Navigator.pop(context, {
                'name': _nameController.text,
                'email': _emailController.text,
              });
            }
          },
          child: Text('添加'),
        ),
      ],
    );
  }
}

// 使用
final result = await showDialog<Map<String, String>>(
  context: context,
  builder: (context) => FormDialog(),
);
```

### 3.2 单选对话框

```dart
Future<String?> showSingleChoiceDialog({
  required BuildContext context,
  required String title,
  required List<String> options,
  String? selected,
}) async {
  return await showDialog<String>(
    context: context,
    builder: (context) {
      String? tempSelected = selected;
      
      return StatefulBuilder(
        builder: (context, setState) {
          return AlertDialog(
            title: Text(title),
            content: Column(
              mainAxisSize: MainAxisSize.min,
              children: options.map((option) {
                return RadioListTile<String>(
                  title: Text(option),
                  value: option,
                  groupValue: tempSelected,
                  onChanged: (value) {
                    setState(() {
                      tempSelected = value;
                    });
                  },
                );
              }).toList(),
            ),
            actions: [
              TextButton(
                onPressed: () => Navigator.pop(context),
                child: Text('取消'),
              ),
              TextButton(
                onPressed: () => Navigator.pop(context, tempSelected),
                child: Text('确定'),
              ),
            ],
          );
        },
      );
    },
  );
}

// 使用
final result = await showSingleChoiceDialog(
  context: context,
  title: '选择颜色',
  options: ['红色', '蓝色', '绿色'],
  selected: '蓝色',
);
```

### 3.3 多选对话框

```dart
Future<List<String>?> showMultiChoiceDialog({
  required BuildContext context,
  required String title,
  required List<String> options,
  required List<String> selected,
}) async {
  return await showDialog<List<String>>(
    context: context,
    builder: (context) {
      return StatefulBuilder(
        builder: (context, setState) {
          return AlertDialog(
            title: Text(title),
            content: SingleChildScrollView(
              child: Column(
                mainAxisSize: MainAxisSize.min,
                children: options.map((option) {
                  return CheckboxListTile(
                    title: Text(option),
                    value: selected.contains(option),
                    onChanged: (checked) {
                      setState(() {
                        if (checked == true) {
                          selected.add(option);
                        } else {
                          selected.remove(option);
                        }
                      });
                    },
                  );
                }).toList(),
              ),
            ),
            actions: [
              TextButton(
                onPressed: () => Navigator.pop(context),
                child: Text('取消'),
              ),
              TextButton(
                onPressed: () => Navigator.pop(context, selected),
                child: Text('确定'),
              ),
            ],
          );
        },
      );
    },
  );
}

// 使用
final result = await showMultiChoiceDialog(
  context: context,
  title: '选择标签',
  options: ['工作', '生活', '学习', '娱乐'],
  selected: ['工作'],
);
```

### 3.4 加载对话框

```dart
void showLoadingDialog(BuildContext context, String message) {
  showDialog(
    context: context,
    barrierDismissible: false,
    builder: (context) => AlertDialog(
      content: Row(
        children: [
          CircularProgressIndicator(),
          SizedBox(width: 20),
          Text(message),
        ],
      ),
    ),
  );
}

void hideLoadingDialog(BuildContext context) {
  Navigator.pop(context);
}

// 使用
showLoadingDialog(context, '加载中...');

try {
  await someAsyncOperation();
} finally {
  hideLoadingDialog(context);
}
```

### 3.5 自定义对话框

```dart
showDialog(
  context: context,
  builder: (context) => Dialog(
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(16),
    ),
    child: Container(
      padding: EdgeInsets.all(24),
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          Icon(Icons.check_circle, color: Colors.green, size: 64),
          SizedBox(height: 16),
          Text(
            '操作成功',
            style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
          ),
          SizedBox(height: 8),
          Text('您的数据已保存成功'),
          SizedBox(height: 24),
          FilledButton(
            onPressed: () => Navigator.pop(context),
            child: Text('确定'),
          ),
        ],
      ),
    ),
  ),
)
```

### 3.6 确认退出对话框

```dart
Future<bool> showExitConfirmation(BuildContext context) async {
  final result = await showDialog<bool>(
    context: context,
    builder: (context) => AlertDialog(
      icon: Icon(Icons.exit_to_app, color: Colors.red, size: 48),
      title: Text('退出确认'),
      content: Text('您有未保存的更改，确定要退出吗？'),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context, false),
          child: Text('取消'),
        ),
        TextButton(
          onPressed: () => Navigator.pop(context, true),
          style: TextButton.styleFrom(foregroundColor: Colors.red),
          child: Text('退出'),
        ),
      ],
    ),
  );
  
  return result ?? false;
}

// 使用
final shouldExit = await showExitConfirmation(context);
if (shouldExit) {
  Navigator.pop(context);
}
```

## 4. showDialog 参数

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `context` | `BuildContext` | - | 上下文（必需） |
| `builder` | `WidgetBuilder` | - | 对话框构建器（必需） |
| `barrierDismissible` | `bool` | `true` | 点击遮罩是否关闭 |
| `barrierColor` | `Color?` | - | 遮罩颜色 |
| `barrierLabel` | `String?` | - | 遮罩语义标签 |
| `useSafeArea` | `bool` | `true` | 是否使用安全区域 |
| `useRootNavigator` | `bool` | `true` | 是否使用根导航器 |
| `routeSettings` | `RouteSettings?` | - | 路由设置 |
| `anchorPoint` | `Offset?` | - | 锚点位置 |

## 5. 常见问题与解决方案

### 问题1：对话框无法关闭

```dart
// 确保调用 Navigator.pop(context)
AlertDialog(
  actions: [
    TextButton(
      onPressed: () => Navigator.pop(context),  // 必须调用
      child: Text('关闭'),
    ),
  ],
)
```

### 问题2：内容溢出

```dart
AlertDialog(
  content: SingleChildScrollView(  // 使用滚动
    child: Column(
      mainAxisSize: MainAxisSize.min,
      children: [...],
    ),
  ),
)
```

### 问题3：点击遮罩不关闭

```dart
showDialog(
  context: context,
  barrierDismissible: false,  // 设置为 false
  builder: (context) => AlertDialog(...),
)
```

### 问题4：键盘遮挡输入框

```dart
AlertDialog(
  content: Padding(
    padding: EdgeInsets.only(
      bottom: MediaQuery.of(context).viewInsets.bottom,
    ),
    child: TextField(...),
  ),
)
```

## 6. 最佳实践

### 6.1 封装对话框工具

```dart
class DialogUtils {
  static Future<bool?> confirm({
    required BuildContext context,
    required String title,
    required String content,
    String confirmText = '确定',
    String cancelText = '取消',
  }) {
    return showDialog<bool>(
      context: context,
      builder: (context) => AlertDialog(
        title: Text(title),
        content: Text(content),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context, false),
            child: Text(cancelText),
          ),
          TextButton(
            onPressed: () => Navigator.pop(context, true),
            child: Text(confirmText),
          ),
        ],
      ),
    );
  }

  static Future<void> alert({
    required BuildContext context,
    String? title,
    required String content,
    String buttonText = '确定',
  }) {
    return showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: title != null ? Text(title) : null,
        content: Text(content),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: Text(buttonText),
          ),
        ],
      ),
    );
  }
}

// 使用
final confirmed = await DialogUtils.confirm(
  context: context,
  title: '删除确认',
  content: '确定要删除吗？',
);

await DialogUtils.alert(
  context: context,
  title: '提示',
  content: '操作成功',
);
```

## 总结

AlertDialog 是 Flutter 中常用的对话框组件：

**核心要点**：
1. 使用 showDialog 显示对话框
2. Navigator.pop 关闭对话框
3. 支持返回值传递
4. Material 3 支持图标属性

**常用属性**：
- `title` - 标题
- `content` - 内容
- `actions` - 操作按钮
- `icon` - 图标

**最佳实践**：
1. 提供清晰的操作按钮
2. 长内容使用 SingleChildScrollView
3. 封装常用的对话框类型
4. 合理设置 barrierDismissible

---

*最后更新: 2026-02-19*

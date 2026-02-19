# Flutter Dialog 组件用法详解

## 简介

Dialog 是 Flutter 中用于显示模态对话框的组件，常用于确认操作、提示信息、表单输入等场景，需要用户进行交互后才能关闭。

## 1. 初级用法

### 1.1 基本概念

Dialog 的核心特点：
- 模态显示，阻止其他交互
- 支持自定义内容
- 可通过返回值传递数据
- Material 和 Cupertino 两种风格

### 1.2 常用对话框类型

| 类型 | 说明 |
|------|------|
| `AlertDialog` | Material 风格警告对话框 |
| `SimpleDialog` | Material 风格简单选择对话框 |
| `Dialog` | 自定义对话框 |
| `showDialog` | 显示对话框的方法 |
| `CupertinoAlertDialog` | iOS 风格对话框 |

### 1.3 基本语法

```dart
// 显示对话框
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
        onPressed: () => Navigator.pop(context, true),
        child: Text('确定'),
      ),
    ],
  ),
)
```

### 1.4 AlertDialog 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `title` | `Widget?` | 标题 |
| `titlePadding` | `EdgeInsetsGeometry?` | 标题内边距 |
| `content` | `Widget?` | 内容 |
| `contentPadding` | `EdgeInsetsGeometry?` | 内容内边距 |
| `actions` | `List<Widget>?` | 操作按钮列表 |
| `actionsPadding` | `EdgeInsetsGeometry?` | 操作区内边距 |
| `actionsAlignment` | `MainAxisAlignment?` | 操作按钮对齐 |
| `backgroundColor` | `Color?` | 背景色 |
| `elevation` | `double?` | 阴影高度 |
| `shape` | `ShapeBorder?` | 形状 |
| `scrollable` | `bool` | 内容是否可滚动 |

### 1.5 基础示例

#### 确认对话框

```dart
Future<bool?> showConfirmDialog(BuildContext context) {
  return showDialog<bool>(
    context: context,
    builder: (context) => AlertDialog(
      title: Text('确认删除'),
      content: Text('删除后无法恢复，确定要删除吗？'),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context, false),
          child: Text('取消'),
        ),
        FilledButton(
          onPressed: () => Navigator.pop(context, true),
          child: Text('删除'),
        ),
      ],
    ),
  );
}

// 使用
final result = await showConfirmDialog(context);
if (result == true) {
  // 执行删除
}
```

#### 简单提示

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('提示'),
    content: Text('操作成功'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('确定'),
      ),
    ],
  ),
)
```

#### 带图标的对话框

```dart
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Row(
      children: [
        Icon(Icons.warning, color: Colors.orange),
        SizedBox(width: 8),
        Text('警告'),
      ],
    ),
    content: Text('此操作存在风险，是否继续？'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('取消'),
      ),
      FilledButton(
        onPressed: () => Navigator.pop(context),
        child: Text('继续'),
      ),
    ],
  ),
)
```

## 2. 中级用法

### 2.1 SimpleDialog 选项对话框

```dart
Future<String?> showOptionsDialog(BuildContext context) {
  return showDialog<String>(
    context: context,
    builder: (context) => SimpleDialog(
      title: Text('选择颜色'),
      children: [
        SimpleDialogOption(
          onPressed: () => Navigator.pop(context, 'red'),
          child: Row(
            children: [
              Icon(Icons.circle, color: Colors.red),
              SizedBox(width: 8),
              Text('红色'),
            ],
          ),
        ),
        SimpleDialogOption(
          onPressed: () => Navigator.pop(context, 'blue'),
          child: Row(
            children: [
              Icon(Icons.circle, color: Colors.blue),
              SizedBox(width: 8),
              Text('蓝色'),
            ],
          ),
        ),
        SimpleDialogOption(
          onPressed: () => Navigator.pop(context, 'green'),
          child: Row(
            children: [
              Icon(Icons.circle, color: Colors.green),
              SizedBox(width: 8),
              Text('绿色'),
            ],
          ),
        ),
      ],
    ),
  );
}

// 使用
final color = await showOptionsDialog(context);
if (color != null) {
  print('选中: $color');
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
      borderRadius: BorderRadius.circular(16),
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
        child: Text('确定'),
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
    title: Text('选择项目'),
    content: SingleChildScrollView(
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: List.generate(20, (index) {
          return ListTile(
            title: Text('项目 $index'),
            onTap: () => Navigator.pop(context, index),
          );
        }),
      ),
    ),
  ),
)
```

### 2.4 带输入框的对话框

```dart
Future<String?> showInputDialog(BuildContext context) async {
  final controller = TextEditingController();
  
  return showDialog<String>(
    context: context,
    builder: (context) => AlertDialog(
      title: Text('输入名称'),
      content: TextField(
        controller: controller,
        autofocus: true,
        decoration: InputDecoration(
          hintText: '请输入名称',
        ),
      ),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context),
          child: Text('取消'),
        ),
        FilledButton(
          onPressed: () => Navigator.pop(context, controller.text),
          child: Text('确定'),
        ),
      ],
    ),
  );
}
```

### 2.5 关闭对话框

```dart
// 关闭当前对话框
Navigator.pop(context)

// 关闭并返回值
Navigator.pop(context, 'result')

// 关闭对话框并执行操作
Navigator.pop(context);
// 然后执行其他操作
```

## 3. 高级用法

### 3.1 封装对话框工具类

```dart
class AppDialog {
  static Future<bool?> confirm(
    BuildContext context, {
    String? title,
    required String message,
    String cancelText = '取消',
    String confirmText = '确定',
  }) {
    return showDialog<bool>(
      context: context,
      builder: (context) => AlertDialog(
        title: title != null ? Text(title) : null,
        content: Text(message),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context, false),
            child: Text(cancelText),
          ),
          FilledButton(
            onPressed: () => Navigator.pop(context, true),
            child: Text(confirmText),
          ),
        ],
      ),
    );
  }

  static Future<void> alert(
    BuildContext context, {
    String? title,
    required String message,
    String buttonText = '确定',
  }) {
    return showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: title != null ? Text(title) : null,
        content: Text(message),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: Text(buttonText),
          ),
        ],
      ),
    );
  }

  static Future<T?> show<T>({
    required BuildContext context,
    required Widget Function(BuildContext) builder,
    bool barrierDismissible = true,
  }) {
    return showDialog<T>(
      context: context,
      barrierDismissible: barrierDismissible,
      builder: builder,
    );
  }
}

// 使用示例
final confirmed = await AppDialog.confirm(
  context,
  title: '删除确认',
  message: '确定要删除此项吗？',
);

await AppDialog.alert(context, message: '操作成功');
```

### 3.2 自定义对话框

```dart
class CustomDialog extends StatelessWidget {
  final String title;
  final String message;
  final VoidCallback? onConfirm;

  const CustomDialog({
    required this.title,
    required this.message,
    this.onConfirm,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Dialog(
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(16),
      ),
      child: Padding(
        padding: EdgeInsets.all(24),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Icon(Icons.check_circle, size: 64, color: Colors.green),
            SizedBox(height: 16),
            Text(
              title,
              style: TextStyle(
                fontSize: 20,
                fontWeight: FontWeight.bold,
              ),
            ),
            SizedBox(height: 8),
            Text(
              message,
              textAlign: TextAlign.center,
              style: TextStyle(color: Colors.grey[600]),
            ),
            SizedBox(height: 24),
            FilledButton(
              onPressed: () {
                Navigator.pop(context);
                onConfirm?.call();
              },
              child: Text('确定'),
            ),
          ],
        ),
      ),
    );
  }
}

// 使用
showDialog(
  context: context,
  builder: (context) => CustomDialog(
    title: '成功',
    message: '您的操作已成功完成',
    onConfirm: () {
      print('确认完成');
    },
  ),
)
```

### 3.3 全屏对话框

```dart
showDialog(
  context: context,
  builder: (context) => Dialog.fullscreen(
    child: Scaffold(
      appBar: AppBar(
        title: Text('全屏对话框'),
        leading: IconButton(
          icon: Icon(Icons.close),
          onPressed: () => Navigator.pop(context),
        ),
      ),
      body: Center(
        child: Text('全屏内容'),
      ),
    ),
  ),
)
```

### 3.4 底部弹出对话框

```dart
showModalBottomSheet(
  context: context,
  builder: (context) => Container(
    padding: EdgeInsets.all(24),
    child: Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text('标题', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
        SizedBox(height: 16),
        ListTile(
          leading: Icon(Icons.camera),
          title: Text('拍照'),
          onTap: () {
            Navigator.pop(context, 'camera');
          },
        ),
        ListTile(
          leading: Icon(Icons.photo),
          title: Text('从相册选择'),
          onTap: () {
            Navigator.pop(context, 'gallery');
          },
        ),
        SizedBox(height: 16),
        SizedBox(
          width: double.infinity,
          child: OutlinedButton(
            onPressed: () => Navigator.pop(context),
            child: Text('取消'),
          ),
        ),
      ],
    ),
  ),
)
```

### 3.5 等待对话框

```dart
Future<T> showLoadingDialog<T>(
  BuildContext context,
  Future<T> Function() task, {
  String message = '加载中...',
}) async {
  showDialog(
    context: context,
    barrierDismissible: false,
    builder: (context) => WillPopScope(
      onWillPop: () async => false,
      child: Dialog(
        child: Padding(
          padding: EdgeInsets.all(24),
          child: Row(
            mainAxisSize: MainAxisSize.min,
            children: [
              CircularProgressIndicator(),
              SizedBox(width: 24),
              Text(message),
            ],
          ),
        ),
      ),
    ),
  );

  try {
    final result = await task();
    Navigator.pop(context);
    return result;
  } catch (e) {
    Navigator.pop(context);
    rethrow;
  }
}

// 使用
await showLoadingDialog(context, () async {
  await Future.delayed(Duration(seconds: 2));
  return '完成';
})
```

### 3.6 iOS 风格对话框

```dart
import 'package:flutter/cupertino.dart';

showCupertinoDialog(
  context: context,
  builder: (context) => CupertinoAlertDialog(
    title: Text('提示'),
    content: Text('确定要删除吗？'),
    actions: [
      CupertinoDialogAction(
        onPressed: () => Navigator.pop(context),
        child: Text('取消'),
      ),
      CupertinoDialogAction(
        isDestructiveAction: true,
        onPressed: () => Navigator.pop(context, true),
        child: Text('删除'),
      ),
    ],
  ),
)
```

## 4. 对话框动画

### 4.1 自定义进入动画

```dart
showGeneralDialog(
  context: context,
  barrierDismissible: true,
  barrierLabel: '关闭',
  transitionDuration: Duration(milliseconds: 300),
  pageBuilder: (context, animation, secondaryAnimation) {
    return Center(
      child: Material(
        borderRadius: BorderRadius.circular(16),
        child: Container(
          padding: EdgeInsets.all(24),
          child: Text('自定义动画对话框'),
        ),
      ),
    );
  },
  transitionBuilder: (context, animation, secondaryAnimation, child) {
    return FadeTransition(
      opacity: animation,
      child: ScaleTransition(
        scale: Tween<double>(begin: 0.8, end: 1.0).animate(animation),
        child: child,
      ),
    );
  },
)
```

## 5. 常见问题与解决方案

### 问题1：点击空白处不关闭

```dart
showDialog(
  context: context,
  barrierDismissible: false,  // 设置为 false 禁止点击外部关闭
  builder: (context) => AlertDialog(...),
)
```

### 问题2：对话框返回值

```dart
// 显示对话框
final result = await showDialog<String>(
  context: context,
  builder: (context) => AlertDialog(
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context, 'result_value'),
        child: Text('确定'),
      ),
    ],
  ),
);

print('返回值: $result');
```

### 问题3：对话框内容溢出

```dart
AlertDialog(
  content: SingleChildScrollView(
    child: Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        // 内容
      ],
    ),
  ),
)
```

### 问题4：对话框宽度调整

```dart
showDialog(
  context: context,
  builder: (context) => Dialog(
    child: Container(
      width: 300,
      padding: EdgeInsets.all(24),
      child: Text('固定宽度对话框'),
    ),
  ),
)
```

### 问题5：对话框阻塞后续代码

```dart
// 对话框是异步的，需要 await
await showDialog(...);
print('对话框关闭后执行');
```

## 6. Dialog vs BottomSheet vs Menu

| 特性 | Dialog | BottomSheet | Menu |
|------|--------|-------------|------|
| 位置 | 屏幕中央 | 屏幕底部 | 锚点位置 |
| 遮罩 | 有 | 有 | 有 |
| 交互 | 模态 | 可滑动关闭 | 点击选择 |
| 适用 | 确认、提示 | 选项、操作 | 快捷菜单 |

## 7. 最佳实践

### 7.1 使用回调处理结果

```dart
void handleDelete(BuildContext context, Item item) async {
  final confirmed = await AppDialog.confirm(
    context,
    title: '删除',
    message: '确定删除 ${item.name} 吗？',
  );
  
  if (confirmed == true) {
    // 执行删除
    await deleteItem(item);
    // 显示成功提示
    AppSnackBar.success(context, '删除成功');
  }
}
```

### 7.2 统一对话框样式

```dart
class AppTheme {
  static ShapeBorder get dialogShape =>
      RoundedRectangleBorder(borderRadius: BorderRadius.circular(16));
  
  static double get dialogElevation => 8;
}

AlertDialog(
  shape: AppTheme.dialogShape,
  elevation: AppTheme.dialogElevation,
  // ...
)
```

### 7.3 国际化支持

```dart
AppDialog.confirm(
  context,
  title: AppLocalizations.of(context)!.confirmDelete,
  message: AppLocalizations.of(context)!.deleteWarning,
  cancelText: AppLocalizations.of(context)!.cancel,
  confirmText: AppLocalizations.of(context)!.delete,
)
```

## 总结

Dialog 是 Flutter 中重要的交互组件：

**核心要点**：
1. 使用 showDialog 显示对话框
2. Navigator.pop 关闭对话框并返回值
3. AlertDialog 最常用，支持标题、内容和操作按钮
4. 可自定义样式和行为

**常用场景**：
- 确认删除
- 表单输入
- 选项选择
- 信息提示
- 加载等待

**最佳实践**：
1. 封装通用对话框工具类
2. 使用 await 处理对话框结果
3. 合理设置 barrierDismissible
4. 保持对话框内容简洁

---

*最后更新: 2026-02-18*

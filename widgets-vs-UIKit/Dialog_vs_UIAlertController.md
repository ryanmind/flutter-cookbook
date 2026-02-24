# Dialog vs UIAlertController 对比学习

本文档记录 Flutter Dialog 与 UIKit UIAlertController 的对比学习要点。

---

## 1. Dialog 核心概念

### Flutter 对话框类型

Flutter 提供了多种对话框组件：

| 类型 | 说明 | 使用场景 |
|------|------|---------|
| `AlertDialog` | 警告对话框 | 确认、警告、选择 |
| `SimpleDialog` | 简单对话框 | 选项列表 |
| `Dialog` | 自定义对话框 | 自定义内容 |
| `showModalBottomSheet` | 底部弹出 | 操作列表、表单 |
| `showDialog` | 通用对话框函数 | 显示对话框 |

### Dialog vs UIAlertController 概念对比

| Flutter | UIKit | 说明 |
|---------|-------|------|
| `AlertDialog` | `UIAlertController(style: .alert)` | 警告对话框 |
| `SimpleDialog` | `UIAlertController(style: .actionSheet)` | 选项对话框 |
| `showDialog()` | `present(_:animated:)` | 显示对话框 |
| `actions` | `addAction()` | 操作按钮 |
| `showModalBottomSheet` | 自定义实现 | 底部弹出 |

---

## 2. 源码分析

### AlertDialog 类定义

```dart
class AlertDialog extends StatelessWidget {
  const AlertDialog({
    super.key,
    this.icon,
    this.iconPadding,
    this.iconColor,
    this.title,               // 标题
    this.titlePadding,
    this.titleTextStyle,
    this.content,             // 内容
    this.contentPadding = const EdgeInsets.fromLTRB(24.0, 20.0, 24.0, 24.0),
    this.contentTextStyle,
    this.actions,             // 操作按钮
    this.actionsPadding,
    this.actionsAlignment,
    this.actionsOverflowAlignment,
    this.actionsOverflowDirection,
    this.actionsOverflowButtonSpacing,
    this.buttonPadding,
    this.backgroundColor,
    this.elevation,
    this.shadowColor,
    this.surfaceTintColor,
    this.semanticLabel,
    this.insetPadding = const EdgeInsets.symmetric(horizontal: 40.0, vertical: 24.0),
    this.clipBehavior = Clip.none,
    this.shape,
    this.alignment,
    this.scrollable = false,
  });
}
```

### showDialog 函数

```dart
Future<T?> showDialog<T>({
  required BuildContext context,
  required WidgetBuilder builder,
  bool barrierDismissible = true,  // 点击外部关闭
  Color? barrierColor = Colors.black54,
  String? barrierLabel,
  bool useSafeArea = true,
  bool useRootNavigator = true,
  RouteSettings? routeSettings,
  Offset? anchorPoint,
  TraverseEdgeCallback? onDismiss,
})
```

---

## 3. 与 UIAlertController 详细对比

### 3.1 基础警告对话框

```objc
// Objective-C - UIAlertController
UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"Delete Item"
                                                               message:@"Are you sure you want to delete this item?"
                                                        preferredStyle:UIAlertControllerStyleAlert];

UIAlertAction *cancel = [UIAlertAction actionWithTitle:@"Cancel"
                                                 style:UIAlertActionStyleCancel
                                               handler:nil];
UIAlertAction *delete = [UIAlertAction actionWithTitle:@"Delete"
                                                 style:UIAlertActionStyleDestructive
                                               handler:^(UIAlertAction *action) {
    NSLog(@"Deleted");
}];

[alert addAction:cancel];
[alert addAction:delete];

[self presentViewController:alert animated:YES completion:nil];
```

```dart
// Flutter - AlertDialog
showDialog(
  context: context,
  builder: (context) {
    return AlertDialog(
      title: Text('Delete Item'),
      content: Text('Are you sure you want to delete this item?'),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context),
          child: Text('Cancel'),
        ),
        TextButton(
          onPressed: () {
            Navigator.pop(context);
            print('Deleted');
          },
          child: Text('Delete', style: TextStyle(color: Colors.red)),
        ),
      ],
    );
  },
);
```

### 3.2 确认对话框

```objc
// Objective-C
UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"Save Changes"
                                                               message:@"Do you want to save your changes?"
                                                        preferredStyle:UIAlertControllerStyleAlert];

[alert addAction:[UIAlertAction actionWithTitle:@"Don't Save"
                                          style:UIAlertActionStyleDestructive
                                        handler:^(UIAlertAction *action) {
    // 不保存
}]];
[alert addAction:[UIAlertAction actionWithTitle:@"Cancel"
                                          style:UIAlertActionStyleCancel
                                        handler:nil]];
[alert addAction:[UIAlertAction actionWithTitle:@"Save"
                                          style:UIAlertActionStyleDefault
                                        handler:^(UIAlertAction *action) {
    // 保存
}]];
```

```dart
// Flutter
showDialog(
  context: context,
  builder: (context) {
    return AlertDialog(
      title: Text('Save Changes'),
      content: Text('Do you want to save your changes?'),
      actions: [
        TextButton(
          onPressed: () {
            Navigator.pop(context);
            // 不保存
          },
          child: Text("Don't Save", style: TextStyle(color: Colors.red)),
        ),
        TextButton(
          onPressed: () => Navigator.pop(context),
          child: Text('Cancel'),
        ),
        TextButton(
          onPressed: () {
            Navigator.pop(context);
            // 保存
          },
          child: Text('Save'),
        ),
      ],
    );
  },
);
```

### 3.3 输入对话框

```objc
// Objective-C
UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"Enter Name"
                                                               message:@"Please enter your name"
                                                        preferredStyle:UIAlertControllerStyleAlert];

[alert addTextFieldWithConfigurationHandler:^(UITextField *textField) {
    textField.placeholder = @"Name";
}];

[alert addAction:[UIAlertAction actionWithTitle:@"OK"
                                          style:UIAlertActionStyleDefault
                                        handler:^(UIAlertAction *action) {
    UITextField *textField = alert.textFields.firstObject;
    NSString *name = textField.text;
    NSLog(@"Name: %@", name);
}]];

[alert addAction:[UIAlertAction actionWithTitle:@"Cancel"
                                          style:UIAlertActionStyleCancel
                                        handler:nil]];
```

```dart
// Flutter - 需要自定义
final nameController = TextEditingController();

showDialog(
  context: context,
  builder: (context) {
    return AlertDialog(
      title: Text('Enter Name'),
      content: TextField(
        controller: nameController,
        decoration: InputDecoration(hintText: 'Name'),
      ),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context),
          child: Text('Cancel'),
        ),
        TextButton(
          onPressed: () {
            Navigator.pop(context);
            print('Name: ${nameController.text}');
          },
          child: Text('OK'),
        ),
      ],
    );
  },
);
```

### 3.4 选项列表对话框

```objc
// Objective-C - ActionSheet
UIAlertController *sheet = [UIAlertController alertControllerWithTitle:@"Select Option"
                                                               message:nil
                                                        preferredStyle:UIAlertControllerStyleActionSheet];

[sheet addAction:[UIAlertAction actionWithTitle:@"Option 1"
                                          style:UIAlertActionStyleDefault
                                        handler:^(UIAlertAction *action) {
    NSLog(@"Selected Option 1");
}]];
[sheet addAction:[UIAlertAction actionWithTitle:@"Option 2"
                                          style:UIAlertActionStyleDefault
                                        handler:^(UIAlertAction *action) {
    NSLog(@"Selected Option 2");
}]];
[sheet addAction:[UIAlertAction actionWithTitle:@"Cancel"
                                          style:UIAlertActionStyleCancel
                                        handler:nil]];

[self presentViewController:sheet animated:YES completion:nil];
```

```dart
// Flutter - SimpleDialog
showDialog(
  context: context,
  builder: (context) {
    return SimpleDialog(
      title: Text('Select Option'),
      children: [
        SimpleDialogOption(
          onPressed: () {
            Navigator.pop(context, 'Option 1');
          },
          child: Text('Option 1'),
        ),
        SimpleDialogOption(
          onPressed: () {
            Navigator.pop(context, 'Option 2');
          },
          child: Text('Option 2'),
        ),
        SimpleDialogOption(
          onPressed: () => Navigator.pop(context),
          child: Text('Cancel'),
        ),
      ],
    );
  },
);
```

### 3.5 底部弹出

```objc
// Objective-C - 自定义实现或使用 UISheetPresentationController (iOS 15+)
// UISheetPresentationController 需要自定义 UIViewController
```

```dart
// Flutter - showModalBottomSheet
showModalBottomSheet(
  context: context,
  builder: (context) {
    return Container(
      height: 200,
      child: Column(
        children: [
          ListTile(
            title: Text('Option 1'),
            onTap: () => Navigator.pop(context, 'Option 1'),
          ),
          ListTile(
            title: Text('Option 2'),
            onTap: () => Navigator.pop(context, 'Option 2'),
          ),
          ListTile(
            title: Text('Cancel'),
            onTap: () => Navigator.pop(context),
          ),
        ],
      ),
    );
  },
);
```

### 3.6 等待对话框返回值

```objc
// Objective-C - 使用回调
- (void)showConfirmDialogWithCompletion:(void (^)(BOOL confirmed))completion {
    UIAlertController *alert = ...;
    [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:^(UIAlertAction *action) {
        completion(YES);
    }]];
    [alert addAction:[UIAlertAction actionWithTitle:@"Cancel" style:UIAlertActionStyleCancel handler:^(UIAlertAction *action) {
        completion(NO);
    }]];
    [self presentViewController:alert animated:YES completion:nil];
}
```

```dart
// Flutter - async/await
final confirmed = await showDialog<bool>(
  context: context,
  builder: (context) {
    return AlertDialog(
      title: Text('Confirm'),
      content: Text('Are you sure?'),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context, false),
          child: Text('Cancel'),
        ),
        TextButton(
          onPressed: () => Navigator.pop(context, true),
          child: Text('OK'),
        ),
      ],
    );
  },
);

if (confirmed == true) {
  print('Confirmed');
}
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 完整的对话框管理
@interface DialogManager : NSObject
+ (void)showAlertWithTitle:(NSString *)title
                   message:(NSString *)message
           inViewController:(UIViewController *)viewController;
+ (void)showConfirmWithTitle:(NSString *)title
                     message:(NSString *)message
             inViewController:(UIViewController *)viewController
                   onConfirm:(void (^)(void))onConfirm;
@end

@implementation DialogManager

+ (void)showAlertWithTitle:(NSString *)title
                   message:(NSString *)message
           inViewController:(UIViewController *)viewController {
    UIAlertController *alert = [UIAlertController alertControllerWithTitle:title
                                                                   message:message
                                                            preferredStyle:UIAlertControllerStyleAlert];
    [alert addAction:[UIAlertAction actionWithTitle:@"OK"
                                              style:UIAlertActionStyleDefault
                                            handler:nil]];
    [viewController presentViewController:alert animated:YES completion:nil];
}

+ (void)showConfirmWithTitle:(NSString *)title
                     message:(NSString *)message
             inViewController:(UIViewController *)viewController
                   onConfirm:(void (^)(void))onConfirm {
    UIAlertController *alert = [UIAlertController alertControllerWithTitle:title
                                                                   message:message
                                                            preferredStyle:UIAlertControllerStyleAlert];
    [alert addAction:[UIAlertAction actionWithTitle:@"Cancel"
                                              style:UIAlertActionStyleCancel
                                            handler:nil]];
    [alert addAction:[UIAlertAction actionWithTitle:@"Confirm"
                                              style:UIAlertActionStyleDefault
                                            handler:^(UIAlertAction *action) {
        if (onConfirm) onConfirm();
    }]];
    [viewController presentViewController:alert animated:YES completion:nil];
}
@end

// 使用
[DialogManager showAlertWithTitle:@"Error"
                          message:@"Something went wrong"
                  inViewController:self];

[DialogManager showConfirmWithTitle:@"Delete"
                            message:@"Are you sure?"
                    inViewController:self
                          onConfirm:^{
    [self deleteItem];
}];
```

### Flutter 实现

```dart
// 完整的对话框管理 - 更简洁
class DialogHelper {
  static Future<void> showAlert({
    required BuildContext context,
    required String title,
    required String message,
  }) {
    return showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text(title),
        content: Text(message),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: Text('OK'),
          ),
        ],
      ),
    );
  }

  static Future<bool> showConfirm({
    required BuildContext context,
    required String title,
    required String message,
    String confirmText = 'Confirm',
    String cancelText = 'Cancel',
  }) async {
    final result = await showDialog<bool>(
      context: context,
      builder: (context) => AlertDialog(
        title: Text(title),
        content: Text(message),
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
    return result ?? false;
  }
}

// 使用
await DialogHelper.showAlert(
  context: context,
  title: 'Error',
  message: 'Something went wrong',
);

final confirmed = await DialogHelper.showConfirm(
  context: context,
  title: 'Delete',
  message: 'Are you sure?',
);
if (confirmed) {
  deleteItem();
}
```

---

## 5. 自定义对话框

### 自定义 Dialog

```dart
class CustomDialog extends StatelessWidget {
  final String title;
  final Widget content;
  final List<Widget> actions;

  const CustomDialog({
    required this.title,
    required this.content,
    this.actions = const [],
  });

  @override
  Widget build(BuildContext context) {
    return Dialog(
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(16),
      ),
      child: Container(
        padding: EdgeInsets.all(24),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text(
              title,
              style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
            ),
            SizedBox(height: 16),
            content,
            SizedBox(height: 24),
            Row(
              mainAxisAlignment: MainAxisAlignment.end,
              children: actions,
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
    title: 'Custom Dialog',
    content: Text('This is a custom dialog'),
    actions: [
      TextButton(
        onPressed: () => Navigator.pop(context),
        child: Text('Close'),
      ),
    ],
  ),
);
```

---

## 6. 常见陷阱

### 陷阱1：未使用 Navigator.pop 关闭

```dart
// ❌ 问题：对话框无法关闭
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('Error'),
    actions: [
      TextButton(
        onPressed: () {
          // 忘记关闭对话框
        },
        child: Text('OK'),
      ),
    ],
  ),
);

// ✅ 正确：使用 Navigator.pop
TextButton(
  onPressed: () => Navigator.pop(context),
  child: Text('OK'),
)
```

### 陷阱2：context 使用错误

```dart
// ❌ 问题：在 builder 外使用 context
showDialog(
  context: context,
  builder: (context) {
    // 这里使用 builder 的 context
    return AlertDialog(
      actions: [
        TextButton(
          onPressed: () {
            // Navigator.pop 应该使用 builder 的 context
            Navigator.pop(context);
          },
          child: Text('OK'),
        ),
      ],
    );
  },
);
```

### 陷阱3：barrierDismissible

```dart
// 默认点击外部可关闭
showDialog(
  context: context,
  barrierDismissible: true,  // 默认 true
  builder: ...
);

// 重要操作应禁止外部点击关闭
showDialog(
  context: context,
  barrierDismissible: false,  // 禁止外部关闭
  builder: ...
);
```

### 陷阱4：在 initState 中显示对话框

```dart
// ❌ 问题：initState 中无法显示对话框
@override
void initState() {
  super.initState();
  showDialog(...);  // 报错！context 不可用
}

// ✅ 正确：使用 WidgetsBinding
@override
void initState() {
  super.initState();
  WidgetsBinding.instance.addPostFrameCallback((_) {
    showDialog(...);
  });
}
```

---

## 7. 学习技巧

### 技巧1：使用 async/await

```dart
// 对话框结果处理
final result = await showDialog<String>(
  context: context,
  builder: (context) => SimpleDialog(
    title: Text('Choose'),
    children: [
      SimpleDialogOption(
        onPressed: () => Navigator.pop(context, 'A'),
        child: Text('Option A'),
      ),
      SimpleDialogOption(
        onPressed: () => Navigator.pop(context, 'B'),
        child: Text('Option B'),
      ),
    ],
  ),
);

print('Selected: $result');
```

### 技巧2：自定义底部弹出

```dart
showModalBottomSheet(
  context: context,
  isScrollControlled: true,  // 全屏弹出
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.vertical(top: Radius.circular(20)),
  ),
  builder: (context) => DraggableScrollableSheet(
    initialChildSize: 0.5,
    minChildSize: 0.25,
    maxChildSize: 1,
    expand: false,
    builder: (context, scrollController) {
      return ListView(
        controller: scrollController,
        children: [...],
      );
    },
  ),
);
```

### 技巧3：Cupertino 风格对话框

```dart
import 'package:flutter/cupertino.dart';

showCupertinoDialog(
  context: context,
  builder: (context) => CupertinoAlertDialog(
    title: Text('Delete'),
    content: Text('Are you sure?'),
    actions: [
      CupertinoDialogAction(
        onPressed: () => Navigator.pop(context),
        child: Text('Cancel'),
      ),
      CupertinoDialogAction(
        isDestructiveAction: true,
        onPressed: () {
          Navigator.pop(context);
          // 删除操作
        },
        child: Text('Delete'),
      ),
    ],
  ),
);
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UIAlertController            → showDialog + AlertDialog/BottomSheet
presentViewController       → showDialog()
preferredStyle: .alert      → AlertDialog
preferredStyle: .actionSheet → SimpleDialog / showModalBottomSheet
addAction:                  → actions: []
UIAlertAction                → TextButton / SimpleDialogOption
UIAlertActionStyleDestructive → 红色 TextButton
UIAlertActionStyleCancel    → 默认样式
addTextFieldWithConfigurationHandler → AlertDialog 中添加 TextField
present:animated:           → showDialog()
dismissViewControllerAnimated: → Navigator.pop()
```

### 关键差异

| 方面 | UIKit UIAlertController | Flutter Dialog |
|------|------------------------|----------------|
| **显示方式** | presentViewController | showDialog 函数 |
| **关闭方式** | dismiss | Navigator.pop |
| **返回值** | 回调 | async/await |
| **样式** | preferredStyle | 不同 Widget |
| **自定义** | 困难 | 自定义 Dialog Widget |

### 最佳实践

1. **async/await** - 使用 async/await 处理对话框结果
2. **封装 Helper** - 统一对话框样式和行为
3. **Navigator.pop** - 确保对话框正确关闭
4. **barrierDismissible** - 重要操作禁止外部关闭

---

*最后更新: 2026-02-24*

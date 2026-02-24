# SnackBar vs Toast 对比学习

本文档记录 Flutter SnackBar 组件与 UIKit Toast（自定义实现）的对比学习要点。

---

## 1. SnackBar 核心概念

### SnackBar 是什么

SnackBar 是 Flutter 中的轻量级消息提示组件，从底部短暂显示后自动消失，类似于 iOS 中的 Toast。

- 从底部滑出
- 可包含操作按钮
- 自动消失
- 支持自定义持续时间

### SnackBar vs Toast 概念对比

| Flutter SnackBar | UIKit Toast（第三方/自定义） | 说明 |
|------------------|---------------------------|------|
| `SnackBar` | 自定义 UIView | 消息视图 |
| `content` | UILabel | 消息内容 |
| `action` | UIButton | 操作按钮 |
| `duration` | `dispatch_after` | 显示时长 |
| `ScaffoldMessenger.showSnackBar` | `UIView.animate` | 显示方法 |

---

## 2. 源码分析

### SnackBar 类定义

```dart
class SnackBar extends StatefulWidget {
  const SnackBar({
    super.key,
    required this.content,            // 内容 Widget
    this.backgroundColor,             // 背景色
    this.elevation,                   // 阴影
    this.margin,                      // 外边距
    this.padding,                     // 内边距
    this.width,                       // 宽度
    this.shape,                       // 形状
    this.behavior,                    // 行为
    this.action,                      // 操作按钮
    this.showCloseIcon,               // 显示关闭图标
    this.closeIconColor,              // 关闭图标颜色
    this.duration = _snackBarDisplayDuration,  // 持续时间
    this.animation,                   // 动画
    this.onVisible,                   // 可见回调
    this.dismissDirection = DismissDirection.down,  // 滑动关闭方向
    this.clipBehavior = Clip.hardEdge,
  });
}

// SnackBarAction - 操作按钮
class SnackBarAction extends StatelessWidget {
  const SnackBarAction({
    super.key,
    this.textColor,                   // 文字颜色
    this.disabledTextColor,           // 禁用文字颜色
    required this.label,              // 按钮文字
    required this.onPressed,          // 点击回调
  });
}

// ScaffoldMessenger - 管理 SnackBar
class ScaffoldMessenger extends StatefulWidget {
  static ScaffoldMessengerState of(BuildContext context) {
    return ScaffoldMessenger.of(context);
  }
}
```

---

## 3. 与 Toast 详细对比

### 3.1 基础消息提示

```objc
// Objective-C - 自定义 Toast
- (void)showToast:(NSString *)message {
    UILabel *toastLabel = [[UILabel alloc] init];
    toastLabel.text = message;
    toastLabel.backgroundColor = [UIColor blackColor];
    toastLabel.textColor = [UIColor whiteColor];
    toastLabel.textAlignment = NSTextAlignmentCenter;
    toastLabel.layer.cornerRadius = 8;
    toastLabel.clipsToBounds = YES;
    
    [toastLabel sizeToFit];
    toastLabel.frame = CGRectMake(
        (self.view.bounds.size.width - toastLabel.bounds.size.width - 32) / 2,
        self.view.bounds.size.height - 100,
        toastLabel.bounds.size.width + 32,
        40
    );
    
    [self.view addSubview:toastLabel];
    
    [UIView animateWithDuration:0.3 animations:^{
        toastLabel.alpha = 0;
    } completion:^(BOOL finished) {
        [toastLabel removeFromSuperview];
    }];
}
```

```dart
// Flutter - SnackBar
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('Hello, SnackBar!'),
  ),
);
```

### 3.2 带操作按钮

```objc
// Objective-C - 需要自定义实现
UIView *toastView = [[UIView alloc] init];
// ... 创建视图

UIButton *actionButton = [UIButton buttonWithType:UIButtonTypeSystem];
[actionButton setTitle:@"Undo" forState:UIControlStateNormal];
[actionButton addTarget:self action:@selector(undoAction) forControlEvents:UIControlEventTouchUpInside];
[toastView addSubview:actionButton];
```

```dart
// Flutter - action 参数
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('Item deleted'),
    action: SnackBarAction(
      label: 'Undo',
      onPressed: () {
        // 撤销删除
      },
    ),
  ),
);
```

### 3.3 自定义持续时间

```objc
// Objective-C
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
    [UIView animateWithDuration:0.3 animations:^{
        toastLabel.alpha = 0;
    }];
});
```

```dart
// Flutter - duration 参数
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('This will stay for 5 seconds'),
    duration: Duration(seconds: 5),
  ),
);
```

### 3.4 自定义样式

```objc
// Objective-C
toastLabel.backgroundColor = [UIColor systemBlueColor];
toastLabel.layer.cornerRadius = 20;
toastLabel.font = [UIFont systemFontOfSize:16 weight:UIFontWeightBold];
```

```dart
// Flutter
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text(
      'Custom styled SnackBar',
      style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
    ),
    backgroundColor: Colors.blue,
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(20),
    ),
    behavior: SnackBarBehavior.floating,
    margin: EdgeInsets.all(16),
  ),
);
```

### 3.5 关闭图标

```dart
// Flutter - showCloseIcon
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('Message with close icon'),
    showCloseIcon: true,
    closeIconColor: Colors.white,
  ),
);
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// Toast 工具类
@interface Toast : NSObject
+ (void)showToast:(NSString *)message inView:(UIView *)view;
+ (void)showToast:(NSString *)message actionTitle:(NSString *)title actionHandler:(void (^)(void))handler inView:(UIView *)view;
@end

@implementation Toast

+ (void)showToast:(NSString *)message inView:(UIView *)view {
    [self showToast:message actionTitle:nil actionHandler:nil inView:view];
}

+ (void)showToast:(NSString *)message actionTitle:(NSString *)title actionHandler:(void (^)(void))handler inView:(UIView *)view {
    // 容器
    UIView *toastView = [[UIView alloc] init];
    toastView.backgroundColor = [[UIColor blackColor] colorWithAlphaComponent:0.8];
    toastView.layer.cornerRadius = 8;
    toastView.clipsToBounds = YES;
    toastView.alpha = 0;
    
    // 消息标签
    UILabel *messageLabel = [[UILabel alloc] init];
    messageLabel.text = message;
    messageLabel.textColor = [UIColor whiteColor];
    messageLabel.font = [UIFont systemFontOfSize:14];
    [messageLabel sizeToFit];
    
    CGFloat width = messageLabel.bounds.size.width + 32;
    CGFloat height = 48;
    
    if (title && handler) {
        UIButton *actionButton = [UIButton buttonWithType:UIButtonTypeSystem];
        [actionButton setTitle:title forState:UIControlStateNormal];
        [actionButton setTitleColor:[UIColor systemBlueColor] forState:UIControlStateNormal];
        actionButton.titleLabel.font = [UIFont boldSystemFontOfSize:14];
        [actionButton sizeToFit];
        [actionButton addTargetBlock:^(UIButton *button) {
            handler();
            [toastView removeFromSuperview];
        } forControlEvents:UIControlEventTouchUpInside];
        
        [toastView addSubview:actionButton];
        actionButton.frame = CGRectMake(width, 0, actionButton.bounds.size.width + 16, height);
        width += actionButton.bounds.size.width + 24;
    }
    
    [toastView addSubview:messageLabel];
    messageLabel.frame = CGRectMake(16, 0, messageLabel.bounds.size.width, height);
    
    CGFloat x = (view.bounds.size.width - width) / 2;
    CGFloat y = view.bounds.size.height - 100;
    toastView.frame = CGRectMake(x, y, width, height);
    
    [view addSubview:toastView];
    
    [UIView animateWithDuration:0.3 animations:^{
        toastView.alpha = 1;
    }];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        [UIView animateWithDuration:0.3 animations:^{
            toastView.alpha = 0;
        } completion:^(BOOL finished) {
            [toastView removeFromSuperview];
        }];
    });
}
@end

// 使用
[Toast showToast:@"Item deleted" actionTitle:@"Undo" actionHandler:^{
    // 撤销删除
} inView:self.view];
```

### Flutter 实现

```dart
// Flutter - 更简洁
class ToastHelper {
  static void showMessage(BuildContext context, String message) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(message),
        behavior: SnackBarBehavior.floating,
      ),
    );
  }

  static void showMessageWithAction(
    BuildContext context,
    String message,
    String actionLabel,
    VoidCallback onAction,
  ) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(message),
        action: SnackBarAction(
          label: actionLabel,
          onPressed: onAction,
        ),
        behavior: SnackBarBehavior.floating,
      ),
    );
  }
}

// 使用
ToastHelper.showMessageWithAction(
  context,
  'Item deleted',
  'Undo',
  () {
    // 撤销删除
  },
);

// 或直接使用
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('Item deleted'),
    action: SnackBarAction(
      label: 'Undo',
      onPressed: () {
        // 撤销删除
      },
    ),
  ),
);
```

---

## 5. SnackBar 行为

### SnackBarBehavior

```dart
// fixed - 固定在底部，忽略 margin
SnackBar(
  content: Text('Fixed SnackBar'),
  behavior: SnackBarBehavior.fixed,
)

// floating - 浮动，可以使用 margin
SnackBar(
  content: Text('Floating SnackBar'),
  behavior: SnackBarBehavior.floating,
  margin: EdgeInsets.all(16),
)
```

---

## 6. 常见陷阱

### 陷阱1：在 Scaffold 外部调用

```dart
// ❌ 错误：ScaffoldMessenger.of(context) 找不到 Scaffold
@override
Widget build(BuildContext context) {
  return ScaffoldMessenger(
    child: Scaffold(
      body: Builder(
        builder: (innerContext) {
          return ElevatedButton(
            onPressed: () {
              ScaffoldMessenger.of(context).showSnackBar(...);  // 可能出错
            },
          );
        },
      ),
    ),
  );
}

// ✅ 正确：使用正确的 context
ScaffoldMessenger.of(innerContext).showSnackBar(...);

// 或使用全局 key
final scaffoldMessengerKey = GlobalKey<ScaffoldMessengerState>();

ScaffoldMessenger(
  key: scaffoldMessengerKey,
  child: Scaffold(...),
);

scaffoldMessengerKey.currentState?.showSnackBar(...);
```

### 陷阱2：SnackBar 被覆盖

```dart
// 多个 SnackBar 会排队显示
ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('First')));
ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('Second')));
// 两个都会显示，第二个等第一个消失后显示

// 清除当前 SnackBar
ScaffoldMessenger.of(context).clearSnackBars();

// 立即显示新的
ScaffoldMessenger.of(context).hideCurrentSnackBar();
ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('New')));
```

### 陷阱3：持续时间太短

```dart
// 默认 4 秒
SnackBar(
  content: Text('Default duration: 4 seconds'),
)

// 需要更长时设置 duration
SnackBar(
  content: Text('Longer duration'),
  duration: Duration(seconds: 8),
)
```

### 陷阱4：操作按钮被隐藏

```dart
// 如果 SnackBar 太短，操作按钮可能被截断
SnackBar(
  content: Text('Short'),
  action: SnackBarAction(
    label: 'Very Long Action Label',  // 可能被截断
    onPressed: () {},
  ),
)

// 确保内容足够或使用较短的操作标签
```

---

## 7. 学习技巧

### 技巧1：封装 SnackBar 工具

```dart
class AppSnackBar {
  static void success(BuildContext context, String message) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Row(
          children: [
            Icon(Icons.check_circle, color: Colors.white),
            SizedBox(width: 8),
            Text(message),
          ],
        ),
        backgroundColor: Colors.green,
      ),
    );
  }

  static void error(BuildContext context, String message) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Row(
          children: [
            Icon(Icons.error, color: Colors.white),
            SizedBox(width: 8),
            Text(message),
          ],
        ),
        backgroundColor: Colors.red,
      ),
    );
  }
}
```

### 技巧2：配合 ScaffoldMessenger

```dart
// 使用 ScaffoldMessenger 管理 SnackBar 队列
final messenger = ScaffoldMessenger.of(context);

// 清除所有
messenger.clearSnackBars();

// 隐藏当前
messenger.hideCurrentSnackBar();

// 显示新的
messenger.showSnackBar(SnackBar(...));
```

### 技巧3：响应式设计

```dart
// 在不同屏幕尺寸下调整 SnackBar
LayoutBuilder(
  builder: (context, constraints) {
    return ScaffoldMessenger(
      child: Scaffold(
        body: ...,
      ),
    );
  },
)

// SnackBar 宽度可以自适应
SnackBar(
  content: Text('Message'),
  width: MediaQuery.of(context).size.width > 600 ? 400 : null,
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
自定义 Toast UIView          → SnackBar
UILabel + UIButton          → content + action
dispatch_after              → duration
UIView.animate              → 内置动画
手动管理显示/隐藏             → ScaffoldMessenger
添加到 view                  → showSnackBar
```

### 关键差异

| 方面 | UIKit Toast（自定义） | Flutter SnackBar |
|------|---------------------|------------------|
| **实现方式** | 需要自定义 | 内置组件 |
| **操作按钮** | 需要手动添加 | action 参数 |
| **动画** | 需要手动实现 | 内置动画 |
| **队列管理** | 需要手动实现 | ScaffoldMessenger 自动管理 |
| **样式自定义** | 完全自定义 | 有限自定义 |

### 最佳实践

1. **使用 ScaffoldMessenger** - 统一管理 SnackBar
2. **合理设置 duration** - 根据内容长度调整
3. **提供操作按钮** - 对于可撤销操作
4. **封装工具类** - 统一样式和行为

---

*最后更新: 2026-02-24*

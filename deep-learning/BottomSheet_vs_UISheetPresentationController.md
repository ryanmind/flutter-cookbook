# BottomSheet vs UISheetPresentationController 对比学习

本文档记录 Flutter BottomSheet 组件与 UIKit UISheetPresentationController 的对比学习要点。

---

## 1. BottomSheet 核心概念

### BottomSheet 是什么

BottomSheet 是 Flutter 中从底部弹出的面板组件，类似于 iOS 15+ 的 UISheetPresentationController。

- 支持持久化和模态两种模式
- 支持拖拽和手势交互
- 支持自定义高度
- Material Design 风格

### BottomSheet vs UISheetPresentationController 概念对比

| Flutter BottomSheet | UIKit UISheetPresentationController | 说明 |
|---------------------|------------------------------------|------|
| `showModalBottomSheet()` | `present(_:animated:)` | 模态底部弹出 |
| `showBottomSheet()` | 无直接对应 | 持久化底部弹出 |
| `isScrollControlled` | `prefersScrollingExpandsWhenScrolledToEdge` | 滚动控制 |
| `isDismissible` | 无直接对应 | 是否可点击外部关闭 |
| `enableDrag` | `prefersGrabberVisible` | 拖拽手势 |
| `shape` | 无直接对应 | 形状 |
| `constraints` | `detents` | 高度约束 |

---

## 2. 源码分析

### showModalBottomSheet

```dart
Future<T?> showModalBottomSheet<T>({
  required BuildContext context,
  required WidgetBuilder builder,      // 内容构建器
  Color? backgroundColor,               // 背景色
  double? elevation,                    // 阴影
  ShapeBorder? shape,                   // 形状
  Clip? clipBehavior,                   // 裁剪行为
  BoxConstraints? constraints,          // 约束
  Color? barrierColor,                  // 遮罩颜色
  bool isScrollControlled = false,      // 滚动控制
  bool useRootNavigator = false,        // 使用根导航器
  bool isDismissible = true,            // 是否可关闭
  bool enableDrag = true,               // 是否可拖拽
  RouteSettings? routeSettings,         // 路由设置
  TransitionBuilder? transitionBuilder, // 过渡构建器
  Offset? anchorPoint,                  // 锚点
  bool useSafeArea = false,             // 使用安全区
})
```

### showBottomSheet（持久化）

```dart
PersistentBottomSheetController<T> showBottomSheet<T>({
  required BuildContext context,
  required WidgetBuilder builder,
  Color? backgroundColor,
  double? elevation,
  ShapeBorder? shape,
  Clip? clipBehavior,
  BoxConstraints? constraints,
  bool enableDrag = true,
  RouteSettings? routeSettings,
  TransitionBuilder? transitionBuilder,
  Offset? anchorPoint,
})
```

---

## 3. 与 UISheetPresentationController 详细对比

### 3.1 基础底部弹出

```objc
// Objective-C - UISheetPresentationController
UIViewController *vc = [[UIViewController alloc] init];
vc.view.backgroundColor = [UIColor systemBackgroundColor];

UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(20, 20, 200, 30)];
label.text = @"Hello, Sheet!";
[vc.view addSubview:label];

if (@available(iOS 15.0, *)) {
    vc.sheetPresentationController.detents = @[UISheetPresentationControllerDetent.mediumDetent];
    vc.sheetPresentationController.prefersGrabberVisible = YES;
}

[self presentViewController:vc animated:YES completion:nil];
```

```dart
// Flutter - showModalBottomSheet
showModalBottomSheet(
  context: context,
  builder: (context) {
    return Container(
      padding: EdgeInsets.all(20),
      child: Text('Hello, BottomSheet!'),
    );
  },
);
```

### 3.2 自定义高度

```objc
// Objective-C - detents
if (@available(iOS 15.0, *)) {
    vc.sheetPresentationController.detents = @[
        [UISheetPresentationControllerDetent mediumDetent],
        [UISheetPresentationControllerDetent largeDetent]
    ];
}

// iOS 16+ 自定义高度
UISheetPresentationControllerDetent *custom = [UISheetPresentationControllerDetent 
    customDetentWithIdentifier:@"Custom" resolver:^CGFloat(id<UISheetPresentationControllerDetentResolutionContext> context) {
    return context.maximumDetentValue * 0.5;
}];
```

```dart
// Flutter - isScrollControlled + constraints
showModalBottomSheet(
  context: context,
  isScrollControlled: true,  // 允许自定义高度
  constraints: BoxConstraints(
    maxHeight: MediaQuery.of(context).size.height * 0.5,
  ),
  builder: (context) {
    return Container(
      height: 300,
      child: Text('Fixed height'),
    );
  },
)

// 或让内容决定高度
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) {
    return DraggableScrollableSheet(
      initialChildSize: 0.5,
      minChildSize: 0.25,
      maxChildSize: 0.9,
      builder: (context, scrollController) {
        return Container(
          child: ListView.builder(
            controller: scrollController,
            itemCount: 20,
            itemBuilder: (context, index) => ListTile(title: Text('Item $index')),
          ),
        );
      },
    );
  },
)
```

### 3.3 全屏底部弹出

```objc
// Objective-C
vc.sheetPresentationController.detents = @[UISheetPresentationControllerDetent.largeDetent];
```

```dart
// Flutter
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) {
    return Container(
      height: MediaQuery.of(context).size.height,
      child: FullScreenContent(),
    );
  },
)
```

### 3.4 圆角形状

```objc
// Objective-C - 自动处理
// UISheetPresentationController 默认有圆角
```

```dart
// Flutter - shape 参数
showModalBottomSheet(
  context: context,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.vertical(top: Radius.circular(20)),
  ),
  builder: (context) {
    return Container(
      padding: EdgeInsets.all(16),
      child: Text('Rounded corners'),
    );
  },
)
```

### 3.5 拖拽手柄

```objc
// Objective-C
vc.sheetPresentationController.prefersGrabberVisible = YES;
```

```dart
// Flutter - 需要自定义
showModalBottomSheet(
  context: context,
  builder: (context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        // 自定义拖拽手柄
        Container(
          margin: EdgeInsets.only(top: 8),
          width: 40,
          height: 4,
          decoration: BoxDecoration(
            color: Colors.grey[300],
            borderRadius: BorderRadius.circular(2),
          ),
        ),
        // 内容
        Expanded(child: Content()),
      ],
    );
  },
)
```

### 3.6 禁用关闭

```objc
// Objective-C
vc.modalInPresentation = YES;  // 阻止交互式关闭
```

```dart
// Flutter
showModalBottomSheet(
  context: context,
  isDismissible: false,  // 点击外部不关闭
  enableDrag: false,     // 禁用拖拽关闭
  builder: (context) {
    return Container(
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          Text('Cannot be dismissed by tapping outside'),
          ElevatedButton(
            onPressed: () => Navigator.pop(context),
            child: Text('Close'),
          ),
        ],
      ),
    );
  },
)
```

### 3.7 持久化 BottomSheet

```objc
// Objective-C - UISheetPresentationController 不支持持久化
// 需要自定义实现
```

```dart
// Flutter - showBottomSheet（持久化）
Scaffold(
  body: Center(
    child: ElevatedButton(
      onPressed: () {
        showBottomSheet(
          context: context,
          builder: (context) {
            return Container(
              height: 200,
              child: Center(child: Text('Persistent BottomSheet')),
            );
          },
        );
      },
      child: Text('Show Persistent'),
    ),
  ),
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 分享面板
@interface ShareSheetViewController : UIViewController
@end

@implementation ShareSheetViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.view.backgroundColor = [UIColor systemBackgroundColor];
    
    UILabel *titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(20, 20, self.view.bounds.size.width - 40, 30)];
    titleLabel.text = @"Share";
    titleLabel.font = [UIFont boldSystemFontOfSize:18];
    titleLabel.textAlignment = NSTextAlignmentCenter;
    [self.view addSubview:titleLabel];
    
    // 分享选项
    NSArray *items = @[
        @{@"icon": @"square.and.arrow.up", @"title": @"Copy Link"},
        @{@"icon": @"message", @"title": @"Messages"},
        @{@"icon": @"envelope", @"title": @"Mail"},
        @{@"icon": @"square.and.arrow.up.on.square", @"title": @"AirDrop"},
    ];
    
    UIStackView *stackView = [[UIStackView alloc] init];
    stackView.axis = UILayoutConstraintAxisHorizontal;
    stackView.distribution = UIStackViewDistributionEqualSpacing;
    stackView.spacing = 20;
    stackView.translatesAutoresizingMaskIntoConstraints = NO;
    [self.view addSubview:stackView];
    
    [stackView.topAnchor constraintEqualToAnchor:self.view.topAnchor constant:70].active = YES;
    [stackView.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor constant:20].active = YES;
    [stackView.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor constant:-20].active = YES;
    
    for (NSDictionary *item in items) {
        UIButton *button = [UIButton buttonWithType:UIButtonTypeSystem];
        [button setImage:[UIImage systemImageNamed:item[@"icon"]] forState:UIControlStateNormal];
        [button setTintColor:[UIColor systemBlueColor]];
        button.bounds = CGRectMake(0, 0, 60, 60);
        [stackView addArrangedSubview:button];
    }
    
    // iOS 15+
    if (@available(iOS 15.0, *)) {
        self.sheetPresentationController.detents = @[UISheetPresentationControllerDetent.mediumDetent];
        self.sheetPresentationController.prefersGrabberVisible = YES;
        self.sheetPresentationController.prefersCornerRadius = 20;
    }
}
@end

// 调用
ShareSheetViewController *shareVC = [[ShareSheetViewController alloc] init];
[self presentViewController:shareVC animated:YES completion:nil];
```

### Flutter 实现

```dart
// 分享面板 - 更简洁
class ShareSheet extends StatelessWidget {
  final items = [
    {'icon': Icons.link, 'title': 'Copy Link'},
    {'icon': Icons.message, 'title': 'Messages'},
    {'icon': Icons.email, 'title': 'Mail'},
    {'icon': Icons.share, 'title': 'AirDrop'},
  ];

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(20),
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          // 拖拽手柄
          Container(
            width: 40,
            height: 4,
            decoration: BoxDecoration(
              color: Colors.grey[300],
              borderRadius: BorderRadius.circular(2),
            ),
          ),
          SizedBox(height: 16),
          // 标题
          Text(
            'Share',
            style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
          ),
          SizedBox(height: 24),
          // 分享选项
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: items.map((item) {
              return Column(
                children: [
                  Container(
                    width: 60,
                    height: 60,
                    decoration: BoxDecoration(
                      color: Colors.blue.withOpacity(0.1),
                      shape: BoxShape.circle,
                    ),
                    child: Icon(item['icon'] as IconData, color: Colors.blue),
                  ),
                  SizedBox(height: 8),
                  Text(
                    item['title'] as String,
                    style: TextStyle(fontSize: 12),
                  ),
                ],
              );
            }).toList(),
          ),
          SizedBox(height: 20),
        ],
      ),
    );
  }
}

// 调用
showModalBottomSheet(
  context: context,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.vertical(top: Radius.circular(20)),
  ),
  builder: (context) => ShareSheet(),
);
```

---

## 5. DraggableScrollableSheet

用于创建可拖拽调整高度的 BottomSheet：

```dart
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) {
    return DraggableScrollableSheet(
      initialChildSize: 0.5,   // 初始高度 50%
      minChildSize: 0.25,      // 最小高度 25%
      maxChildSize: 0.9,       // 最大高度 90%
      expand: false,
      builder: (context, scrollController) {
        return Container(
          decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.vertical(top: Radius.circular(20)),
          ),
          child: ListView.builder(
            controller: scrollController,
            itemCount: 50,
            itemBuilder: (context, index) {
              return ListTile(title: Text('Item $index'));
            },
          ),
        );
      },
    );
  },
)
```

---

## 6. 常见陷阱

### 陷阱1：内容溢出

```dart
// ❌ 错误：内容超出 BottomSheet 高度
showModalBottomSheet(
  context: context,
  builder: (context) {
    return Column(
      children: [
        // 大量内容...
      ],
    );  // 可能溢出
  },
)

// ✅ 正确：使用 SingleChildScrollView 或 ListView
showModalBottomSheet(
  context: context,
  builder: (context) {
    return SingleChildScrollView(
      child: Column(
        children: [
          // 大量内容...
        ],
      ),
    );
  },
)
```

### 陷阱2：键盘遮挡

```dart
// ⚠️ 键盘弹出可能遮挡内容
showModalBottomSheet(
  context: context,
  builder: (context) {
    return TextField();  // 键盘弹出时可能被遮挡
  },
)

// ✅ 使用 isScrollControlled 和 padding
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) {
    return Padding(
      padding: EdgeInsets.only(
        bottom: MediaQuery.of(context).viewInsets.bottom,
      ),
      child: TextField(),
    );
  },
)
```

### 陷阱3：关闭后获取返回值

```dart
// 获取返回值
final result = await showModalBottomSheet<String>(
  context: context,
  builder: (context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        ListTile(
          title: Text('Option 1'),
          onTap: () => Navigator.pop(context, 'option1'),
        ),
        ListTile(
          title: Text('Option 2'),
          onTap: () => Navigator.pop(context, 'option2'),
        ),
      ],
    );
  },
);

print('Selected: $result');
```

### 陷阱4：模态 vs 持久化

```dart
// 模态 BottomSheet - 阻挡交互，有遮罩
showModalBottomSheet(context: context, builder: ...);

// 持久化 BottomSheet - 不阻挡交互，无遮罩
showBottomSheet(context: context, builder: ...);
```

---

## 7. 学习技巧

### 技巧1：自定义拖拽手柄

```dart
class DragHandle extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      margin: EdgeInsets.only(top: 8, bottom: 8),
      width: 40,
      height: 4,
      decoration: BoxDecoration(
        color: Colors.grey[300],
        borderRadius: BorderRadius.circular(2),
      ),
    );
  }
}

// 使用
showModalBottomSheet(
  context: context,
  builder: (context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        DragHandle(),
        // 内容...
      ],
    );
  },
)
```

### 技巧2：动画关闭

```dart
// 使用 Navigator.pop 关闭
Navigator.pop(context, result);

// 带动画
Navigator.of(context).pop();
```

### 技巧3：响应式高度

```dart
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) {
    final height = MediaQuery.of(context).size.height;
    return Container(
      height: height * 0.6,  // 60% 屏幕高度
      child: Content(),
    );
  },
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UISheetPresentationController  → showModalBottomSheet
present:animated:             → showModalBottomSheet
detents                       → isScrollControlled + constraints
prefersGrabberVisible         → 自定义拖拽手柄
mediumDetent / largeDetent    → DraggableScrollableSheet
modalInPresentation           → isDismissible: false
自定义实现持久化               → showBottomSheet
```

### 关键差异

| 方面 | UIKit UISheetPresentationController | Flutter BottomSheet |
|------|------------------------------------|---------------------|
| **系统版本** | iOS 15+ | 全版本 |
| **拖拽手柄** | 内置 | 需自定义 |
| **高度控制** | detents | constraints / DraggableScrollableSheet |
| **持久化** | 不支持 | showBottomSheet |
| **动画** | 系统动画 | 可自定义 |

### 最佳实践

1. **使用 DraggableScrollableSheet** - 可拖拽调整高度
2. **处理键盘遮挡** - 使用 viewInsets.bottom
3. **添加拖拽手柄** - 提升用户体验
4. **合理设置 isDismissible** - 根据场景决定

---

*最后更新: 2026-02-24*

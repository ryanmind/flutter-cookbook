# Button vs UIButton 对比学习

本文档记录 Flutter Button 组件与 UIKit UIButton 的对比学习要点。

---

## 1. Button 核心概念

### Flutter 按钮类型

Flutter Material 3 提供了 5 种主要按钮类型：

| 按钮类型 | 说明 | 使用场景 |
|---------|------|---------|
| `FilledButton` | 填充按钮 | 主要操作、强调 |
| `ElevatedButton` | 阴影按钮 | 主要操作（Material 2） |
| `OutlinedButton` | 边框按钮 | 次要操作 |
| `TextButton` | 文本按钮 | 低强调操作 |
| `IconButton` | 图标按钮 | 工具栏、小操作 |

### Button vs UIButton 概念对比

| Flutter Button | UIKit UIButton | 说明 |
|----------------|----------------|------|
| `FilledButton` | `UIButton(type: .filled)` | 填充按钮 |
| `OutlinedButton` | `UIButton(type: .system)` + 边框 | 边框按钮 |
| `TextButton` | `UIButton(type: .system)` | 文本按钮 |
| `IconButton` | `UIButton` + image | 图标按钮 |
| `onPressed` | `addTarget:action:` | 点击回调 |
| `child` | `setTitle:forState:` | 按钮内容 |
| `style` | 各种属性配置 | 样式配置 |

---

## 2. 源码分析

### Button 类层次

```dart
// Material 3 按钮
FilledButton      → 主要操作，填充背景
ElevatedButton    → 阴影按钮（Material 2 风格）
OutlinedButton    → 边框按钮
TextButton        → 文本按钮
IconButton        → 图标按钮

// 所有按钮都继承自 MaterialButton（内部）
// 通过 style 参数统一配置样式
```

### ButtonStyle 配置

```dart
class ButtonStyle {
  // 文字样式
  final WidgetStateProperty<TextStyle?>? textStyle;
  
  // 背景色
  final WidgetStateProperty<Color?>? backgroundColor;
  
  // 前景色（文字/图标）
  final WidgetStateProperty<Color?>? foregroundColor;
  
  // 边框
  final WidgetStateProperty<BorderSide?>? side;
  
  // 阴影
  final WidgetStateProperty<double?>? elevation;
  
  // 内边距
  final WidgetStateProperty<EdgeInsetsGeometry?>? padding;
  
  // 最小尺寸
  final WidgetStateProperty<Size?>? minimumSize;
  
  // 形状
  final WidgetStateProperty<OutlinedBorder?>? shape;
  
  // ... 更多属性
}
```

---

## 3. 与 UIButton 详细对比

### 3.1 基础按钮

```objc
// Objective-C - UIButton
UIButton *button = [UIButton buttonWithType:UIButtonTypeSystem];
[button setTitle:@"Click Me" forState:UIControlStateNormal];
[button addTarget:self action:@selector(buttonTapped:) forControlEvents:UIControlEventTouchUpInside];
button.frame = CGRectMake(0, 0, 200, 44);
[self.view addSubview:button];

- (void)buttonTapped:(UIButton *)sender {
    NSLog(@"Button tapped");
}
```

```dart
// Flutter - FilledButton
FilledButton(
  onPressed: () {
    print('Button tapped');
  },
  child: Text('Click Me'),
)

// 或其他类型
ElevatedButton(onPressed: () {}, child: Text('Elevated'));
OutlinedButton(onPressed: () {}, child: Text('Outlined'));
TextButton(onPressed: () {}, child: Text('Text'));
```

### 3.2 按钮状态

```objc
// Objective-C - UIControlState
[button setTitle:@"Normal" forState:UIControlStateNormal];
[button setTitle:@"Highlighted" forState:UIControlStateHighlighted];
[button setTitle:@"Disabled" forState:UIControlStateDisabled];
[button setTitleColor:[UIColor blueColor] forState:UIControlStateNormal];
[button setTitleColor:[UIColor redColor] forState:UIControlStateHighlighted];
button.enabled = NO;  // 禁用
```

```dart
// Flutter - MaterialState / WidgetState
// 通过 style 的 WidgetStateProperty 处理状态
FilledButton(
  onPressed: null,  // null 表示禁用
  style: ButtonStyle(
    foregroundColor: WidgetStateProperty.resolveWith((states) {
      if (states.contains(WidgetState.disabled)) {
        return Colors.grey;
      }
      if (states.contains(WidgetState.pressed)) {
        return Colors.red;
      }
      return Colors.blue;
    }),
  ),
  child: Text('Button'),
)
```

### 3.3 按钮图标

```objc
// Objective-C - 图片按钮
[button setImage:[UIImage systemImageNamed:@"star"] forState:UIControlStateNormal];
[button setTitle:@"Favorite" forState:UIControlStateNormal];
button.semanticContentAttribute = UISemanticContentAttributeForceLeftToRight;  // 图标在左
```

```dart
// Flutter - 图标 + 文字
FilledButton.icon(
  onPressed: () {},
  icon: Icon(Icons.star),
  label: Text('Favorite'),
)

// 或自定义布局
FilledButton(
  onPressed: () {},
  child: Row(
    mainAxisSize: MainAxisSize.min,
    children: [
      Icon(Icons.star),
      SizedBox(width: 8),
      Text('Favorite'),
    ],
  ),
)
```

### 3.4 按钮样式

```objc
// Objective-C - 样式配置
button.backgroundColor = [UIColor blueColor];
button.layer.cornerRadius = 8;
button.titleLabel.font = [UIFont boldSystemFontOfSize:16];
button.contentEdgeInsets = UIEdgeInsetsMake(12, 24, 12, 24);
```

```dart
// Flutter - style 配置
FilledButton(
  onPressed: () {},
  style: FilledButton.styleFrom(
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(8),
    ),
    textStyle: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
    padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
  ),
  child: Text('Styled Button'),
)
```

### 3.5 按钮尺寸

```objc
// Objective-C - frame
button.frame = CGRectMake(0, 0, 200, 50);
// 或约束
[button.widthAnchor constraintEqualToConstant:200].active = YES;
[button.heightAnchor constraintEqualToConstant:50].active = YES;
```

```dart
// Flutter - minimumSize + padding
FilledButton(
  onPressed: () {},
  style: FilledButton.styleFrom(
    minimumSize: Size(200, 50),  // 最小尺寸
    // 或固定尺寸使用 SizedBox
  ),
  child: Text('Button'),
)

// 或使用 SizedBox 包裹
SizedBox(
  width: 200,
  height: 50,
  child: FilledButton(
    onPressed: () {},
    child: Text('Button'),
  ),
)
```

### 3.6 图标按钮

```objc
// Objective-C
UIButton *iconButton = [UIButton buttonWithType:UIButtonTypeSystem];
[iconButton setImage:[UIImage systemImageNamed:@"settings"] forState:UIControlStateNormal];
iconButton.tintColor = [UIColor blueColor];
[iconButton addTarget:self action:@selector(settingsTapped) forControlEvents:UIControlEventTouchUpInside];
```

```dart
// Flutter - IconButton
IconButton(
  icon: Icon(Icons.settings),
  color: Colors.blue,
  onPressed: () {
    print('Settings tapped');
  },
  tooltip: 'Settings',  // 长按提示
)

// 或带背景的图标按钮
FloatingActionButton(
  onPressed: () {},
  child: Icon(Icons.add),
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 创建一个完整的登录按钮
UIButton *loginButton = [UIButton buttonWithType:UIButtonTypeSystem];
loginButton.translatesAutoresizingMaskIntoConstraints = NO;

// 样式
loginButton.backgroundColor = [UIColor systemBlueColor];
loginButton.layer.cornerRadius = 8;
[loginButton setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
loginButton.titleLabel.font = [UIFont boldSystemFontOfSize:16];

// 内容
[loginButton setTitle:@"Sign In" forState:UIControlStateNormal];

// 内边距
loginButton.contentEdgeInsets = UIEdgeInsetsMake(14, 24, 14, 24);

// 点击事件
[loginButton addTarget:self action:@selector(loginButtonTapped:) forControlEvents:UIControlEventTouchUpInside];

// 禁用状态
loginButton.enabled = NO;
loginButton.backgroundColor = [UIColor systemGrayColor];

[self.view addSubview:loginButton];

// 约束
[loginButton.centerXAnchor constraintEqualToAnchor:self.view.centerXAnchor].active = YES;
[loginButton.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor constant:24].active = YES;
[loginButton.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor constant:-24].active = YES;
[loginButton.bottomAnchor constraintEqualToAnchor:self.view.safeAreaLayoutGuide.bottomAnchor constant:-24].active = YES;

// 点击处理
- (void)loginButtonTapped:(UIButton *)sender {
    // 添加点击动画
    [UIView animateWithDuration:0.1 animations:^{
        sender.transform = CGAffineTransformMakeScale(0.95, 0.95);
    } completion:^(BOOL finished) {
        [UIView animateWithDuration:0.1 animations:^{
            sender.transform = CGAffineTransformIdentity;
        }];
    }];
    
    NSLog(@"Login button tapped");
}
```

### Flutter 实现

```dart
// 创建同样的登录按钮 - 更简洁
FilledButton(
  onPressed: isLoading ? null : () {
    print('Login button tapped');
  },
  style: FilledButton.styleFrom(
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
    textStyle: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
    padding: EdgeInsets.symmetric(horizontal: 24, vertical: 14),
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(8),
    ),
    disabledBackgroundColor: Colors.grey,
  ),
  child: isLoading
      ? SizedBox(
          width: 20,
          height: 20,
          child: CircularProgressIndicator(strokeWidth: 2),
        )
      : Text('Sign In'),
)

// 或使用 Padding 包裹实现响应式宽度
Padding(
  padding: EdgeInsets.symmetric(horizontal: 24),
  child: SizedBox(
    width: double.infinity,
    child: FilledButton(
      onPressed: () {},
      style: FilledButton.styleFrom(
        padding: EdgeInsets.symmetric(vertical: 14),
      ),
      child: Text('Sign In'),
    ),
  ),
)
```

---

## 5. Material 3 按钮选择指南

```
重要性：高 → 低

FilledButton   → 主要操作（提交、保存、继续）
ElevatedButton → 主要操作（需要阴影分离时）
OutlinedButton → 次要操作（取消、返回）
TextButton     → 低强调操作（了解更多、跳过）
IconButton     → 工具栏操作（设置、分享）
```

### 示例场景

```dart
// 对话框按钮
Row(
  children: [
    TextButton(
      onPressed: () => Navigator.pop(context, false),
      child: Text('Cancel'),
    ),
    FilledButton(
      onPressed: () => Navigator.pop(context, true),
      child: Text('Confirm'),
    ),
  ],
)

// 表单提交
Column(
  children: [
    TextField(...),
    SizedBox(height: 16),
    SizedBox(
      width: double.infinity,
      child: FilledButton(
        onPressed: submit,
        child: Text('Submit'),
      ),
    ),
  ],
)

// 工具栏
Row(
  children: [
    IconButton(icon: Icon(Icons.share), onPressed: share),
    IconButton(icon: Icon(Icons.bookmark), onPressed: bookmark),
    IconButton(icon: Icon(Icons.more_vert), onPressed: showMenu),
  ],
)
```

---

## 6. 常见陷阱

### 陷阱1：onPressed 为 null 时禁用

```dart
// ✅ 正确：null 表示禁用
FilledButton(
  onPressed: isEnabled ? () {} : null,  // null 禁用按钮
  child: Text('Button'),
)

// ❌ 错误：空函数不会禁用
FilledButton(
  onPressed: isEnabled ? () {} : () {},  // 仍然可点击
  child: Text('Button'),
)
```

### 陷阱2：styleFrom vs ButtonStyle

```dart
// styleFrom - 简化的静态方法，推荐使用
FilledButton(
  style: FilledButton.styleFrom(
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
  ),
  child: Text('Button'),
)

// ButtonStyle - 更灵活，支持状态
FilledButton(
  style: ButtonStyle(
    backgroundColor: WidgetStateProperty.resolveWith((states) {
      if (states.contains(WidgetState.pressed)) {
        return Colors.blue.withValues(alpha: 0.8);
      }
      return Colors.blue;
    }),
  ),
  child: Text('Button'),
)
```

### 陷阱3：按钮宽度

```dart
// ❌ 问题：按钮默认宽度由内容决定
FilledButton(
  onPressed: () {},
  child: Text('Short'),  // 按钮很短
)

// ✅ 解决方案1：SizedBox 包裹
SizedBox(
  width: double.infinity,
  child: FilledButton(...),
)

// ✅ 解决方案2：使用 Center + ConstrainedBox
Center(
  child: ConstrainedBox(
    constraints: BoxConstraints(minWidth: 200),
    child: FilledButton(...),
  ),
)
```

### 陷阱4：按钮内的图标对齐

```dart
// ⚠️ .icon 构造函数会自动处理对齐
FilledButton.icon(
  onPressed: () {},
  icon: Icon(Icons.star),
  label: Text('Favorite'),
)

// 如果自定义布局，注意对齐
FilledButton(
  onPressed: () {},
  child: Row(
    mainAxisSize: MainAxisSize.min,  // 重要！
    children: [
      Icon(Icons.star),
      SizedBox(width: 8),
      Text('Favorite'),
    ],
  ),
)
```

---

## 7. 学习技巧

### 技巧1：提取按钮样式

```dart
// 定义项目通用的按钮样式
class AppButtonStyles {
  static ButtonStyle primary = FilledButton.styleFrom(
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
    padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(8),
    ),
  );

  static ButtonStyle secondary = OutlinedButton.styleFrom(
    foregroundColor: Colors.blue,
    side: BorderSide(color: Colors.blue),
    padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
  );
}

// 使用
FilledButton(
  style: AppButtonStyles.primary,
  onPressed: () {},
  child: Text('Primary'),
)
```

### 技巧2：加载状态按钮

```dart
class LoadingButton extends StatefulWidget {
  final Future<void> Function() onPressed;
  final Widget child;

  const LoadingButton({
    required this.onPressed,
    required this.child,
  });

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
              child: CircularProgressIndicator(strokeWidth: 2),
            )
          : widget.child,
    );
  }
}
```

### 技巧3：使用 WidgetState

```dart
// Material 3 使用 WidgetState（Flutter 3.22+）
// 旧版本使用 MaterialState

// Flutter 3.22+
WidgetStateProperty.resolveWith((states) {
  if (states.contains(WidgetState.pressed)) { ... }
  if (states.contains(WidgetState.disabled)) { ... }
  if (states.contains(WidgetState.hovered)) { ... }
})

// Flutter 3.22 之前
MaterialStateProperty.resolveWith((states) {
  if (states.contains(MaterialState.pressed)) { ... }
})
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UIButton                    → FilledButton / OutlinedButton / TextButton
setTitle:forState:          → child: Text()
setImage:forState:          → icon: 或 .icon 构造函数
addTarget:action:           → onPressed:
backgroundColor              → style: backgroundColor
layer.cornerRadius          → style: shape
enabled                     → onPressed: null 禁用
UIControlState              → WidgetState
```

### 关键差异

| 方面 | UIKit UIButton | Flutter Button |
|------|----------------|----------------|
| **类型** | type 属性 | 不同组件类 |
| **点击回调** | Target-Action | 闭包 |
| **禁用** | enabled = NO | onPressed = null |
| **样式配置** | 分散的属性 | ButtonStyle 统一配置 |
| **状态管理** | UIControlState | WidgetState |

### 最佳实践

1. **选择正确的按钮类型** - 根据重要性选择 Filled/Outlined/Text
2. **onPressed: null 禁用** - 不要用空函数
3. **提取样式** - 统一管理项目按钮样式
4. **使用 .icon 构造函数** - 图标按钮用 .icon 更简洁

---

*最后更新: 2026-02-24*

# TextField vs UITextField 对比学习

本文档记录 Flutter TextField 组件与 UIKit UITextField 的对比学习要点。

---

## 1. TextField 核心概念

### TextField 是什么

TextField 是 Flutter 中用于文本输入的组件，类似于 UIKit 的 UITextField，但功能更强大。

- 支持单行和多行输入
- 丰富的装饰和样式配置
- 内置验证和格式化
- 支持输入控制器

### TextField vs UITextField 概念对比

| Flutter TextField | UIKit UITextField | 说明 |
|-------------------|-------------------|------|
| `controller` | `text` 属性 | 文本控制 |
| `decoration` | `border`, `placeholder` 等 | 装饰样式 |
| `onChanged` | `addTarget:...:UIControlEventEditingChanged` | 文本变化 |
| `onSubmitted` | `delegate` 的 `textFieldShouldReturn:` | 提交回调 |
| `keyboardType` | `keyboardType` | 键盘类型 |
| `obscureText` | `isSecureTextEntry` | 密码输入 |
| `maxLength` | 无直接对应 | 最大长度 |
| `inputFormatters` | 无直接对应 | 输入格式化 |

---

## 2. 源码分析

### TextField 类定义

```dart
class TextField extends StatefulWidget {
  const TextField({
    super.key,
    this.controller,              // 文本控制器
    this.focusNode,               // 焦点控制
    this.decoration = const InputDecoration(),  // 装饰
    this.keyboardType = TextInputType.text,     // 键盘类型
    this.textInputAction = TextInputAction.done, // 键盘动作
    this.textCapitalization = TextCapitalization.none,
    this.style,                   // 文字样式
    this.strutStyle,
    this.textAlign = TextAlign.start,  // 文字对齐
    this.textDirection,
    this.readOnly = false,        // 只读
    this.toolbarOptions,
    this.showCursor,
    this.autofocus = false,       // 自动获取焦点
    this.obscuringCharacter = '•',
    this.obscureText = false,     // 密码输入
    this.autocorrect = true,
    this.smartDashesType,
    this.smartQuotesType,
    this.enableSuggestions = true,
    this.maxLines = 1,            // 最大行数
    this.minLines,
    this.expands = false,
    this.maxLength,               // 最大长度
    this.maxLengthEnforcement,
    this.onChanged,               // 文本变化回调
    this.onEditingComplete,       // 编辑完成
    this.onSubmitted,             // 提交回调
    this.onAppPrivateCommand,
    this.inputFormatters,         // 输入格式化
    this.enabled,                 // 是否启用
    this.cursorWidth = 2.0,
    this.cursorHeight,
    this.cursorRadius,
    this.cursorColor,             // 光标颜色
    this.selectionHeightStyle = BoxHeightStyle.tight,
    this.selectionWidthStyle = BoxWidthStyle.tight,
    this.keyboardAppearance,
    this.scrollPadding = const EdgeInsets.all(20.0),
    this.dragStartBehavior = DragStartBehavior.start,
    this.enableInteractiveSelection = true,
    this.selectionControls,
    this.onTap,                   // 点击回调
    this.onTapOutside,
    this.buildCounter,
    this.scrollController,
    this.scrollPhysics,
    this.autofillHints = const <String>[],
    this.clipBehavior = Clip.hardEdge,
    this.restorationId,
    this.scribbleEnabled = true,
    this.enableIMEPersonalizedLearning = true,
    this.contextMenuBuilder = _defaultContextMenuBuilder,
    this.canRequestFocus = true,
    this.spellCheckConfiguration,
    this.magnifierConfiguration,
  });
}
```

### TextEditingController

```dart
class TextEditingController extends ValueNotifier<String> {
  TextEditingController({String? text}) : super(text ?? '');
  
  // 获取/设置文本
  String get text;
  set text(String newText);
  
  // 获取/设置选择范围
  TextSelection get selection;
  set selection(TextSelection newSelection);
  
  // 清除文本
  void clear();
  
  // 构建 TextSpan
  TextSpan buildTextSpan({
    required BuildContext context,
    TextStyle? style,
    required bool withComposing,
  });
}
```

---

## 3. 与 UITextField 详细对比

### 3.1 基础输入框

```objc
// Objective-C - UITextField
UITextField *textField = [[UITextField alloc] initWithFrame:CGRectMake(20, 100, 280, 44)];
textField.placeholder = @"Enter your name";
textField.borderStyle = UITextBorderStyleRoundedRect;
textField.delegate = self;
[self.view addSubview:textField];

// delegate
- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    [textField resignFirstResponder];
    return YES;
}
```

```dart
// Flutter - TextField
TextField(
  decoration: InputDecoration(
    hintText: 'Enter your name',
    border: OutlineInputBorder(),
  ),
  onSubmitted: (value) {
    FocusScope.of(context).unfocus();
  },
)
```

### 3.2 文本控制

```objc
// Objective-C
textField.text = @"Initial text";
NSString *currentText = textField.text;

// 清除
textField.text = @"";
```

```dart
// Flutter - TextEditingController
final controller = TextEditingController(text: 'Initial text');

TextField(
  controller: controller,
);

// 获取文本
String currentText = controller.text;

// 清除
controller.clear();

// 设置文本
controller.text = 'New text';

// 记得释放
@override
void dispose() {
  controller.dispose();
  super.dispose();
}
```

### 3.3 占位符和标签

```objc
// Objective-C
textField.placeholder = @"Username";
// 没有内置的浮动标签
```

```dart
// Flutter - InputDecoration
TextField(
  decoration: InputDecoration(
    hintText: 'Enter username',      // 占位符
    labelText: 'Username',           // 标签（浮动效果）
    helperText: 'Enter your username',  // 帮助文本
    prefixIcon: Icon(Icons.person),  // 前缀图标
    suffixIcon: Icon(Icons.check),   // 后缀图标
  ),
)
```

### 3.4 键盘类型

```objc
// Objective-C
textField.keyboardType = UIKeyboardTypeDefault;
textField.keyboardType = UIKeyboardTypeEmailAddress;
textField.keyboardType = UIKeyboardTypePhonePad;
textField.keyboardType = UIKeyboardTypeNumberPad;
textField.keyboardType = UIKeyboardTypeDecimalPad;
textField.keyboardType = UIKeyboardTypeURL;
```

```dart
// Flutter
TextField(
  keyboardType: TextInputType.text,
  keyboardType: TextInputType.emailAddress,
  keyboardType: TextInputType.phone,
  keyboardType: TextInputType.number,
  keyboardType: TextInputType.numberWithOptions(decimal: true),
  keyboardType: TextInputType.url,
  keyboardType: TextInputType.multiline,
)
```

### 3.5 键盘动作

```objc
// Objective-C
textField.returnKeyType = UIReturnKeyDone;
textField.returnKeyType = UIReturnKeyNext;
textField.returnKeyType = UIReturnKeySearch;
```

```dart
// Flutter - textInputAction
TextField(
  textInputAction: TextInputAction.done,
  textInputAction: TextInputAction.next,
  textInputAction: TextInputAction.search,
  textInputAction: TextInputAction.go,
  textInputAction: TextInputAction.send,
  onSubmitted: (value) {
    // 处理提交
  },
)
```

### 3.6 密码输入

```objc
// Objective-C
textField.secureTextEntry = YES;
```

```dart
// Flutter
TextField(
  obscureText: true,
  obscuringCharacter: '•',  // 遮挡字符
  decoration: InputDecoration(
    suffixIcon: IconButton(
      icon: Icon(_obscureText ? Icons.visibility : Icons.visibility_off),
      onPressed: () {
        setState(() {
          _obscureText = !_obscureText;
        });
      },
    ),
  ),
)
```

### 3.7 多行输入

```objc
// Objective-C - 需要 UITextView
UITextView *textView = [[UITextView alloc] initWithFrame:frame];
textView.font = [UIFont systemFontOfSize:16];
```

```dart
// Flutter - TextField 支持
TextField(
  maxLines: 5,           // 固定 5 行
  maxLines: null,        // 无限制（多行）
  minLines: 3,           // 最小 3 行
  expands: false,        // 不自动扩展
)
```

### 3.8 输入验证

```objc
// Objective-C - delegate
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string {
    // 限制输入
    NSString *newText = [textField.text stringByReplacingCharactersInRange:range withString:string];
    if (newText.length > 10) {
        return NO;
    }
    return YES;
}
```

```dart
// Flutter - inputFormatters
TextField(
  inputFormatters: [
    LengthLimitingTextInputFormatter(10),  // 限制长度
    FilteringTextInputFormatter.digitsOnly,  // 只允许数字
    FilteringTextInputFormatter.allow(RegExp(r'[a-zA-Z]')),  // 只允许字母
  ],
  maxLength: 10,  // 显示计数器
)
```

### 3.9 错误提示

```objc
// Objective-C - 需要手动实现
// 通常在下方添加 UILabel 显示错误
```

```dart
// Flutter - InputDecoration 内置支持
TextField(
  decoration: InputDecoration(
    labelText: 'Email',
    errorText: _isError ? 'Invalid email' : null,  // 显示错误
    errorStyle: TextStyle(color: Colors.red),
    errorBorder: OutlineInputBorder(
      borderSide: BorderSide(color: Colors.red),
    ),
  ),
)
```

### 3.10 焦点控制

```objc
// Objective-C
[textField becomeFirstResponder];  // 获取焦点
[textField resignFirstResponder];  // 失去焦点
```

```dart
// Flutter - FocusNode
final focusNode = FocusNode();

TextField(
  focusNode: focusNode,
);

// 获取焦点
focusNode.requestFocus();

// 失去焦点
focusNode.unfocus();

// 焦点监听
focusNode.addListener(() {
  if (focusNode.hasFocus) {
    print('Got focus');
  } else {
    print('Lost focus');
  }
});

// 记得释放
@override
void dispose() {
  focusNode.dispose();
  super.dispose();
}
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 创建一个登录表单
@interface LoginViewController : UIViewController <UITextFieldDelegate>
@property (nonatomic, strong) UITextField *emailField;
@property (nonatomic, strong) UITextField *passwordField;
@end

@implementation LoginViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // Email 字段
    self.emailField = [[UITextField alloc] initWithFrame:CGRectMake(20, 100, 280, 44)];
    self.emailField.placeholder = @"Email";
    self.emailField.keyboardType = UIKeyboardTypeEmailAddress;
    self.emailField.autocapitalizationType = UITextAutocapitalizationTypeNone;
    self.emailField.autocorrectionType = UITextAutocorrectionTypeNo;
    self.emailField.borderStyle = UITextBorderStyleRoundedRect;
    self.emailField.delegate = self;
    self.emailField.returnKeyType = UIReturnKeyNext;
    [self.view addSubview:self.emailField];
    
    // Password 字段
    self.passwordField = [[UITextField alloc] initWithFrame:CGRectMake(20, 160, 280, 44)];
    self.passwordField.placeholder = @"Password";
    self.passwordField.secureTextEntry = YES;
    self.passwordField.borderStyle = UITextBorderStyleRoundedRect;
    self.passwordField.delegate = self;
    self.passwordField.returnKeyType = UIReturnKeyDone;
    [self.view addSubview:self.passwordField];
    
    // 显示/隐藏密码按钮
    UIButton *toggleButton = [UIButton buttonWithType:UIButtonTypeSystem];
    toggleButton.frame = CGRectMake(260, 160, 40, 44);
    [toggleButton setImage:[UIImage systemImageNamed:@"eye"] forState:UIControlStateNormal];
    [toggleButton addTarget:self action:@selector(togglePassword) forControlEvents:UIControlEventTouchUpInside];
    self.passwordField.rightView = toggleButton;
    self.passwordField.rightViewMode = UITextFieldViewModeAlways;
}

- (void)togglePassword {
    self.passwordField.secureTextEntry = !self.passwordField.secureTextEntry;
}

- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    if (textField == self.emailField) {
        [self.passwordField becomeFirstResponder];
    } else {
        [textField resignFirstResponder];
        [self login];
    }
    return YES;
}

- (void)login {
    NSString *email = self.emailField.text;
    NSString *password = self.passwordField.text;
    NSLog(@"Login with: %@, %@", email, password);
}

@end
```

### Flutter 实现

```dart
// 创建同样的登录表单 - 更简洁
class LoginForm extends StatefulWidget {
  @override
  _LoginFormState createState() => _LoginFormState();
}

class _LoginFormState extends State<LoginForm> {
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  final _emailFocusNode = FocusNode();
  final _passwordFocusNode = FocusNode();
  bool _obscurePassword = true;

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    _emailFocusNode.dispose();
    _passwordFocusNode.dispose();
    super.dispose();
  }

  void _login() {
    print('Login with: ${_emailController.text}, ${_passwordController.text}');
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // Email 字段
        TextField(
          controller: _emailController,
          focusNode: _emailFocusNode,
          decoration: InputDecoration(
            labelText: 'Email',
            hintText: 'Enter your email',
            prefixIcon: Icon(Icons.email),
            border: OutlineInputBorder(),
          ),
          keyboardType: TextInputType.emailAddress,
          textInputAction: TextInputAction.next,
          onSubmitted: (_) {
            _passwordFocusNode.requestFocus();
          },
        ),
        SizedBox(height: 16),
        // Password 字段
        TextField(
          controller: _passwordController,
          focusNode: _passwordFocusNode,
          decoration: InputDecoration(
            labelText: 'Password',
            hintText: 'Enter your password',
            prefixIcon: Icon(Icons.lock),
            border: OutlineInputBorder(),
            suffixIcon: IconButton(
              icon: Icon(_obscurePassword ? Icons.visibility : Icons.visibility_off),
              onPressed: () {
                setState(() {
                  _obscurePassword = !_obscurePassword;
                });
              },
            ),
          ),
          obscureText: _obscurePassword,
          textInputAction: TextInputAction.done,
          onSubmitted: (_) => _login(),
        ),
        SizedBox(height: 24),
        SizedBox(
          width: double.infinity,
          child: FilledButton(
            onPressed: _login,
            child: Text('Login'),
          ),
        ),
      ],
    );
  }
}
```

---

## 5. InputDecoration 详解

### 常用属性

```dart
InputDecoration(
  // 标签和提示
  labelText: 'Label',           // 浮动标签
  labelStyle: TextStyle(...),
  hintText: 'Hint',             // 占位符
  helperText: 'Helper',         // 帮助文本
  errorText: 'Error',           // 错误文本
  
  // 图标
  prefixIcon: Icon(Icons.star),   // 前缀图标
  prefixText: '\$',               // 前缀文字
  suffixIcon: Icon(Icons.clear),  // 后缀图标
  suffixText: 'kg',               // 后缀文字
  
  // 边框
  border: OutlineInputBorder(),      // 默认边框
  enabledBorder: OutlineInputBorder(),  // 启用状态
  focusedBorder: OutlineInputBorder(),  // 获得焦点
  errorBorder: OutlineInputBorder(),    // 错误状态
  
  // 填充
  filled: true,
  fillColor: Colors.grey[100],
  
  // 内边距
  contentPadding: EdgeInsets.all(16),
  
  // 其他
  counterText: '0/10',          // 计数器文本
  isDense: true,                // 紧凑模式
)
```

### 边框样式

```dart
// 无边框
border: InputBorder.none,

// 下划线
border: UnderlineInputBorder(),

// 圆角矩形
border: OutlineInputBorder(
  borderRadius: BorderRadius.circular(8),
  borderSide: BorderSide(color: Colors.grey),
),

// 自定义边框
border: OutlineInputBorder(
  borderRadius: BorderRadius.only(
    topLeft: Radius.circular(16),
    topRight: Radius.circular(16),
  ),
)
```

---

## 6. 常见陷阱

### 陷阱1：忘记释放 Controller

```dart
// ❌ 问题：Controller 未释放会内存泄漏
class _MyState extends State<MyWidget> {
  final controller = TextEditingController();
  // 没有 dispose
}

// ✅ 正确：在 dispose 中释放
class _MyState extends State<MyWidget> {
  final controller = TextEditingController();
  
  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }
}
```

### 陷阱2：maxLength 与 inputFormatters 冲突

```dart
// ⚠️ 同时使用可能冲突
TextField(
  maxLength: 10,
  inputFormatters: [
    LengthLimitingTextInputFormatter(20),  // 与 maxLength 冲突
  ],
)

// ✅ 使用其中一个即可
TextField(
  maxLength: 10,  // 会自动添加计数器
)
```

### 陷阱3：键盘遮挡输入框

```dart
// ❌ 问题：键盘弹出遮挡输入框
Column(
  children: [
    TextField(),
    TextField(),
  ],
)

// ✅ 解决方案：使用 SingleChildScrollView
SingleChildScrollView(
  child: Column(
    children: [
      TextField(),
      TextField(),
    ],
  ),
)

// ✅ 或配合 Scaffold
Scaffold(
  resizeToAvoidBottomInset: true,  // 默认 true
  body: SingleChildScrollView(...),
)
```

### 陷阱4：多行 TextField 高度问题

```dart
// 问题：maxLines: null 时高度不确定
TextField(
  maxLines: null,  // 可能导致布局问题
)

// 解决方案：设置 minLines 和 maxLines
TextField(
  minLines: 3,
  maxLines: 5,
)
```

---

## 7. 学习技巧

### 技巧1：使用 Form 验证

```dart
final _formKey = GlobalKey<FormState>();

Form(
  key: _formKey,
  child: Column(
    children: [
      TextFormField(
        validator: (value) {
          if (value == null || value.isEmpty) {
            return 'Please enter text';
          }
          if (!value.contains('@')) {
            return 'Invalid email';
          }
          return null;
        },
      ),
      ElevatedButton(
        onPressed: () {
          if (_formKey.currentState!.validate()) {
            // 验证通过
          }
        },
        child: Text('Submit'),
      ),
    ],
  ),
)
```

### 技巧2：自定义输入格式化器

```dart
class UpperCaseTextFormatter extends TextInputFormatter {
  @override
  TextEditingValue formatEditUpdate(
    TextEditingValue oldValue,
    TextEditingValue newValue,
  ) {
    return TextEditingValue(
      text: newValue.text.toUpperCase(),
      selection: newValue.selection,
    );
  }
}

TextField(
  inputFormatters: [UpperCaseTextFormatter()],
)
```

### 技巧3：监听文本变化

```dart
// 方式1：onChanged
TextField(
  onChanged: (value) {
    print('Text changed: $value');
  },
)

// 方式2：Controller 监听
final controller = TextEditingController();

@override
void initState() {
  super.initState();
  controller.addListener(() {
    print('Text: ${controller.text}');
  });
}
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UITextField               → TextField
text                      → controller.text
placeholder               → decoration.hintText
delegate                  → onChanged / onSubmitted
keyboardType              → keyboardType
secureTextEntry           → obscureText
borderStyle               → decoration.border
becomeFirstResponder      → focusNode.requestFocus
resignFirstResponder      → focusNode.unfocus
shouldChangeCharacters    → inputFormatters
```

### 关键差异

| 方面 | UIKit UITextField | Flutter TextField |
|------|-------------------|-------------------|
| **文本控制** | text 属性 | TextEditingController |
| **装饰** | 分散的属性 | InputDecoration 统一 |
| **验证** | delegate 方法 | inputFormatters / Form |
| **多行** | 需要 UITextView | maxLines 参数 |
| **错误提示** | 手动实现 | InputDecoration 内置 |

### 最佳实践

1. **使用 Controller** - 需要控制文本时使用
2. **记得释放** - dispose 中释放 Controller 和 FocusNode
3. **Form 验证** - 使用 Form + TextFormField 做表单验证
4. **inputFormatters** - 限制输入格式

---

*最后更新: 2026-02-24*

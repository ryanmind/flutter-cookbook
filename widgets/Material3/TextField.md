# Flutter TextField 组件用法详解

## 简介

TextField 是 Flutter 中最常用的文本输入组件，用于接收用户输入的文本信息，支持多种输入类型、验证和格式化。

## 1. 初级用法

### 1.1 基本概念

TextField 的核心特点：
- 单行或多行文本输入
- 支持输入验证和格式化
- 支持多种键盘类型
- 可自定义装饰样式

### 1.2 基本语法

```dart
// 最简单的输入框
TextField()

// 带提示文字
TextField(
  decoration: InputDecoration(
    hintText: '请输入内容',
  ),
)

// 获取输入内容
TextField(
  onChanged: (value) {
    print('输入内容: $value');
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `controller` | `TextEditingController?` | - | 文本控制器 |
| `focusNode` | `FocusNode?` | - | 焦点节点 |
| `decoration` | `InputDecoration?` | - | 输入装饰 |
| `keyboardType` | `TextInputType` | `text` | 键盘类型 |
| `textCapitalization` | `TextCapitalization` | `none` | 大写模式 |
| `textInputAction` | `TextInputAction?` | - | 键盘操作按钮 |
| `textStyle` | `TextStyle?` | - | 文本样式 |
| `obscureText` | `bool` | `false` | 是否隐藏文本 |
| `maxLines` | `int?` | `1` | 最大行数 |
| `minLines` | `int?` | - | 最小行数 |
| `maxLength` | `int?` | - | 最大字符数 |
| `enabled` | `bool` | `true` | 是否启用 |
| `readOnly` | `bool` | `false` | 只读模式 |
| `onChanged` | `ValueChanged<String>?` | - | 内容变化回调 |
| `onSubmitted` | `ValueChanged<String>?` | - | 提交回调 |
| `onTap` | `GestureTapCallback?` | - | 点击回调 |

### 1.4 InputDecoration 常用属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `labelText` | `String?` | 标签文字 |
| `labelStyle` | `TextStyle?` | 标签样式 |
| `hintText` | `String?` | 提示文字 |
| `hintStyle` | `TextStyle?` | 提示样式 |
| `helperText` | `String?` | 帮助文字 |
| `errorText` | `String?` | 错误文字 |
| `prefixIcon` | `Widget?` | 前置图标 |
| `suffixIcon` | `Widget?` | 后置图标 |
| `prefixText` | `String?` | 前置文字 |
| `suffixText` | `String?` | 后置文字 |
| `border` | `InputBorder?` | 边框样式 |
| `filled` | `bool` | 是否填充背景 |
| `fillColor` | `Color?` | 填充颜色 |
| `contentPadding` | `EdgeInsetsGeometry?` | 内边距 |

### 1.5 基础示例

#### 带标签的输入框

```dart
TextField(
  decoration: InputDecoration(
    labelText: '用户名',
    hintText: '请输入用户名',
  ),
)
```

#### 带图标的输入框

```dart
TextField(
  decoration: InputDecoration(
    prefixIcon: Icon(Icons.person),
    labelText: '用户名',
  ),
)

TextField(
  decoration: InputDecoration(
    suffixIcon: Icon(Icons.visibility),
    labelText: '密码',
  ),
)
```

#### 密码输入框

```dart
TextField(
  obscureText: true,
  decoration: InputDecoration(
    labelText: '密码',
    prefixIcon: Icon(Icons.lock),
  ),
)
```

#### 多行输入框

```dart
TextField(
  maxLines: 4,
  decoration: InputDecoration(
    labelText: '备注',
    hintText: '请输入备注内容',
    border: OutlineInputBorder(),
  ),
)
```

## 2. 中级用法

### 2.1 键盘类型

```dart
// 文本
TextField(keyboardType: TextInputType.text)

// 数字
TextField(keyboardType: TextInputType.number)

// 电话
TextField(keyboardType: TextInputType.phone)

// 邮箱
TextField(keyboardType: TextInputType.emailAddress)

// 网址
TextField(keyboardType: TextInputType.url)

// 多行
TextField(keyboardType: TextInputType.multiline)

// 小数
TextField(
  keyboardType: TextInputType.numberWithOptions(decimal: true),
)
```

### 2.2 文本控制器

```dart
class MyForm extends StatefulWidget {
  @override
  _MyFormState createState() => _MyFormState();
}

class _MyFormState extends State<MyForm> {
  final _controller = TextEditingController();

  @override
  void initState() {
    super.initState();
    _controller.text = '初始值';
    _controller.selection = TextSelection.collapsed(offset: _controller.text.length);
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(
          controller: _controller,
          decoration: InputDecoration(labelText: '内容'),
        ),
        ElevatedButton(
          onPressed: () {
            print('输入内容: ${_controller.text}');
          },
          child: Text('获取内容'),
        ),
        ElevatedButton(
          onPressed: () {
            _controller.clear();
          },
          child: Text('清空'),
        ),
      ],
    );
  }
}
```

### 2.3 输入验证

```dart
class ValidatedTextField extends StatefulWidget {
  @override
  _ValidatedTextFieldState createState() => _ValidatedTextFieldState();
}

class _ValidatedTextFieldState extends State<ValidatedTextField> {
  String? _errorText;

  String? _validateEmail(String? value) {
    if (value == null || value.isEmpty) {
      return '请输入邮箱';
    }
    final emailRegex = RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$');
    if (!emailRegex.hasMatch(value)) {
      return '请输入有效的邮箱地址';
    }
    return null;
  }

  @override
  Widget build(BuildContext context) {
    return TextField(
      decoration: InputDecoration(
        labelText: '邮箱',
        errorText: _errorText,
        suffixIcon: Icon(Icons.email),
      ),
      onChanged: (value) {
        setState(() {
          _errorText = _validateEmail(value);
        });
      },
    );
  }
}
```

### 2.4 输入格式化

```dart
// 只允许数字
TextField(
  inputFormatters: [
    FilteringTextInputFormatter.digitsOnly,
  ],
)

// 限制长度
TextField(
  inputFormatters: [
    LengthLimitingTextInputFormatter(10),
  ],
)

// 手机号格式化 (xxx xxxx xxxx)
TextField(
  inputFormatters: [
    PhoneNumberFormatter(),
  ],
  keyboardType: TextInputType.phone,
)

// 金额格式化
TextField(
  inputFormatters: [
    FilteringTextInputFormatter.allow(RegExp(r'^\d*\.?\d{0,2}')),
  ],
  keyboardType: TextInputType.numberWithOptions(decimal: true),
)

// 自定义格式化器
class PhoneNumberFormatter extends TextInputFormatter {
  @override
  TextEditingValue formatEditUpdate(
    TextEditingValue oldValue,
    TextEditingValue newValue,
  ) {
    final text = newValue.text.replaceAll(RegExp(r'\D'), '');
    final buffer = StringBuffer();
    
    for (int i = 0; i < text.length && i < 11; i++) {
      if (i == 3 || i == 7) buffer.write(' ');
      buffer.write(text[i]);
    }
    
    return TextEditingValue(
      text: buffer.toString(),
      selection: TextSelection.collapsed(offset: buffer.length),
    );
  }
}
```

### 2.5 边框样式

```dart
// 默认下划线
TextField(
  decoration: InputDecoration(
    labelText: '默认样式',
  ),
)

// 无边框
TextField(
  decoration: InputDecoration(
    labelText: '无边框',
    border: InputBorder.none,
  ),
)

// 外边框
TextField(
  decoration: InputDecoration(
    labelText: '外边框',
    border: OutlineInputBorder(),
  ),
)

// 圆角边框
TextField(
  decoration: InputDecoration(
    labelText: '圆角边框',
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(20),
    ),
  ),
)

// 填充背景
TextField(
  decoration: InputDecoration(
    labelText: '填充背景',
    filled: true,
    fillColor: Colors.grey[200],
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(8),
      borderSide: BorderSide.none,
    ),
  ),
)
```

### 2.6 焦点控制

```dart
class FocusExample extends StatefulWidget {
  @override
  _FocusExampleState createState() => _FocusExampleState();
}

class _FocusExampleState extends State<FocusExample> {
  final _focusNode1 = FocusNode();
  final _focusNode2 = FocusNode();

  @override
  void dispose() {
    _focusNode1.dispose();
    _focusNode2.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(
          focusNode: _focusNode1,
          decoration: InputDecoration(labelText: '输入框 1'),
          textInputAction: TextInputAction.next,
          onSubmitted: (_) => _focusNode2.requestFocus(),
        ),
        TextField(
          focusNode: _focusNode2,
          decoration: InputDecoration(labelText: '输入框 2'),
        ),
        ElevatedButton(
          onPressed: () => _focusNode1.requestFocus(),
          child: Text('聚焦到输入框 1'),
        ),
      ],
    );
  }
}
```

## 3. 高级用法

### 3.1 密码显示切换

```dart
class PasswordField extends StatefulWidget {
  final String labelText;

  const PasswordField({required this.labelText, Key? key}) : super(key: key);

  @override
  _PasswordFieldState createState() => _PasswordFieldState();
}

class _PasswordFieldState extends State<PasswordField> {
  bool _obscureText = true;

  @override
  Widget build(BuildContext context) {
    return TextField(
      obscureText: _obscureText,
      decoration: InputDecoration(
        labelText: widget.labelText,
        prefixIcon: Icon(Icons.lock),
        suffixIcon: IconButton(
          icon: Icon(
            _obscureText ? Icons.visibility : Icons.visibility_off,
          ),
          onPressed: () {
            setState(() {
              _obscureText = !_obscureText;
            });
          },
        ),
      ),
    );
  }
}
```

### 3.2 搜索输入框

```dart
class SearchField extends StatefulWidget {
  final ValueChanged<String>? onSearch;
  final String? hintText;

  const SearchField({this.onSearch, this.hintText, Key? key}) : super(key: key);

  @override
  _SearchFieldState createState() => _SearchFieldState();
}

class _SearchFieldState extends State<SearchField> {
  final _controller = TextEditingController();
  bool _hasText = false;

  @override
  void initState() {
    super.initState();
    _controller.addListener(() {
      setState(() {
        _hasText = _controller.text.isNotEmpty;
      });
    });
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return TextField(
      controller: _controller,
      decoration: InputDecoration(
        hintText: widget.hintText ?? '搜索...',
        prefixIcon: Icon(Icons.search),
        suffixIcon: _hasText
            ? IconButton(
                icon: Icon(Icons.clear),
                onPressed: () {
                  _controller.clear();
                  widget.onSearch?.call('');
                },
              )
            : null,
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(24),
        ),
        contentPadding: EdgeInsets.symmetric(horizontal: 16, vertical: 12),
      ),
      textInputAction: TextInputAction.search,
      onSubmitted: widget.onSearch,
    );
  }
}
```

### 3.3 验证码输入框

```dart
class OTPInput extends StatelessWidget {
  final int length;
  final ValueChanged<String>? onCompleted;

  const OTPInput({
    this.length = 6,
    this.onCompleted,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      mainAxisAlignment: MainAxisAlignment.spaceEvenly,
      children: List.generate(length, (index) {
        return SizedBox(
          width: 48,
          child: TextField(
            textAlign: TextAlign.center,
            keyboardType: TextInputType.number,
            maxLength: 1,
            style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            decoration: InputDecoration(
              counterText: '',
              border: OutlineInputBorder(
                borderRadius: BorderRadius.circular(8),
              ),
            ),
            onChanged: (value) {
              if (value.isNotEmpty) {
                // 自动跳到下一个输入框
                FocusScope.of(context).nextFocus();
              }
            },
          ),
        );
      }),
    );
  }
}
```

### 3.4 带字数统计的输入框

```dart
class CharCountTextField extends StatefulWidget {
  final int maxLength;
  final String? labelText;

  const CharCountTextField({
    required this.maxLength,
    this.labelText,
    Key? key,
  }) : super(key: key);

  @override
  _CharCountTextFieldState createState() => _CharCountTextFieldState();
}

class _CharCountTextFieldState extends State<CharCountTextField> {
  int _currentLength = 0;

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.end,
      children: [
        TextField(
          maxLength: widget.maxLength,
          maxLines: 3,
          decoration: InputDecoration(
            labelText: widget.labelText,
            border: OutlineInputBorder(),
          ),
          onChanged: (value) {
            setState(() {
              _currentLength = value.length;
            });
          },
        ),
        Text('$_currentLength/${widget.maxLength}'),
      ],
    );
  }
}
```

### 3.5 自动完成输入框

```dart
class AutocompleteTextField extends StatefulWidget {
  final List<String> suggestions;
  final String? hintText;

  const AutocompleteTextField({
    required this.suggestions,
    this.hintText,
    Key? key,
  }) : super(key: key);

  @override
  _AutocompleteTextFieldState createState() => _AutocompleteTextFieldState();
}

class _AutocompleteTextFieldState extends State<AutocompleteTextField> {
  final _controller = TextEditingController();
  List<String> _filteredSuggestions = [];
  bool _showSuggestions = false;

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  void _filterSuggestions(String query) {
    if (query.isEmpty) {
      _filteredSuggestions = [];
    } else {
      _filteredSuggestions = widget.suggestions
          .where((s) => s.toLowerCase().contains(query.toLowerCase()))
          .toList();
    }
    setState(() {
      _showSuggestions = _filteredSuggestions.isNotEmpty;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(
          controller: _controller,
          decoration: InputDecoration(
            hintText: widget.hintText,
            suffixIcon: IconButton(
              icon: Icon(Icons.clear),
              onPressed: () {
                _controller.clear();
                _filterSuggestions('');
              },
            ),
          ),
          onChanged: _filterSuggestions,
        ),
        if (_showSuggestions)
          Container(
            height: 150,
            decoration: BoxDecoration(
              border: Border.all(color: Colors.grey),
              borderRadius: BorderRadius.circular(8),
            ),
            child: ListView.builder(
              itemCount: _filteredSuggestions.length,
              itemBuilder: (context, index) {
                return ListTile(
                  title: Text(_filteredSuggestions[index]),
                  onTap: () {
                    _controller.text = _filteredSuggestions[index];
                    setState(() {
                      _showSuggestions = false;
                    });
                  },
                );
              },
            ),
          ),
      ],
    );
  }
}
```

## 4. 与 TextFormField 配合

### 4.1 表单验证

```dart
class MyFormPage extends StatefulWidget {
  @override
  _MyFormPageState createState() => _MyFormPageState();
}

class _MyFormPageState extends State<MyFormPage> {
  final _formKey = GlobalKey<FormState>();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  String? _validateEmail(String? value) {
    if (value == null || value.isEmpty) {
      return '请输入邮箱';
    }
    if (!RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(value)) {
      return '请输入有效的邮箱';
    }
    return null;
  }

  String? _validatePassword(String? value) {
    if (value == null || value.isEmpty) {
      return '请输入密码';
    }
    if (value.length < 6) {
      return '密码至少 6 位';
    }
    return null;
  }

  void _submit() {
    if (_formKey.currentState!.validate()) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('提交成功')),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          TextFormField(
            controller: _emailController,
            decoration: InputDecoration(
              labelText: '邮箱',
              prefixIcon: Icon(Icons.email),
            ),
            keyboardType: TextInputType.emailAddress,
            validator: _validateEmail,
          ),
          TextFormField(
            controller: _passwordController,
            decoration: InputDecoration(
              labelText: '密码',
              prefixIcon: Icon(Icons.lock),
            ),
            obscureText: true,
            validator: _validatePassword,
          ),
          SizedBox(height: 24),
          ElevatedButton(
            onPressed: _submit,
            child: Text('提交'),
          ),
        ],
      ),
    );
  }
}
```

## 5. 常见问题与解决方案

### 问题1：键盘遮挡输入框

```dart
// 使用 SingleChildScrollView
SingleChildScrollView(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Column(
      children: [
        TextField(...),
        TextField(...),
      ],
    ),
  ),
)

// 或使用 padding + MediaQuery
Padding(
  padding: EdgeInsets.only(
    bottom: MediaQuery.of(context).viewInsets.bottom,
  ),
  child: TextField(...),
)
```

### 问题2：输入框聚焦时边框颜色

```dart
TextField(
  decoration: InputDecoration(
    labelText: '用户名',
    focusedBorder: OutlineInputBorder(
      borderSide: BorderSide(color: Colors.blue, width: 2),
    ),
    enabledBorder: OutlineInputBorder(
      borderSide: BorderSide(color: Colors.grey),
    ),
  ),
)
```

### 问题3：禁用输入框

```dart
TextField(
  enabled: false,
  decoration: InputDecoration(
    labelText: '禁用状态',
  ),
)

// 或只读
TextField(
  readOnly: true,
  decoration: InputDecoration(
    labelText: '只读状态',
  ),
)
```

### 问题4：去除默认下划线

```dart
TextField(
  decoration: InputDecoration(
    border: InputBorder.none,
  ),
)
```

### 问题5：输入框高度调整

```dart
TextField(
  decoration: InputDecoration(
    contentPadding: EdgeInsets.symmetric(horizontal: 16, vertical: 16),
  ),
)
```

## 6. 最佳实践

### 6.1 统一输入框样式

```dart
class AppInputDecorations {
  static InputDecoration standard({
    String? labelText,
    String? hintText,
    Widget? prefixIcon,
    Widget? suffixIcon,
  }) {
    return InputDecoration(
      labelText: labelText,
      hintText: hintText,
      prefixIcon: prefixIcon,
      suffixIcon: suffixIcon,
      border: OutlineInputBorder(
        borderRadius: BorderRadius.circular(8),
      ),
      contentPadding: EdgeInsets.symmetric(horizontal: 16, vertical: 16),
    );
  }
}

TextField(
  decoration: AppInputDecorations.standard(
    labelText: '用户名',
    prefixIcon: Icon(Icons.person),
  ),
)
```

### 6.2 清理资源

```dart
class _MyState extends State<MyWidget> {
  final _controller = TextEditingController();
  final _focusNode = FocusNode();

  @override
  void dispose() {
    _controller.dispose();
    _focusNode.dispose();
    super.dispose();
  }
}
```

### 6.3 表单字段封装

```dart
class AppTextField extends StatelessWidget {
  final String? labelText;
  final String? hintText;
  final IconData? prefixIcon;
  final bool obscureText;
  final TextInputType? keyboardType;
  final TextEditingController? controller;
  final String? Function(String?)? validator;
  final ValueChanged<String>? onChanged;
  final VoidCallback? onTap;

  const AppTextField({
    this.labelText,
    this.hintText,
    this.prefixIcon,
    this.obscureText = false,
    this.keyboardType,
    this.controller,
    this.validator,
    this.onChanged,
    this.onTap,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return TextFormField(
      controller: controller,
      obscureText: obscureText,
      keyboardType: keyboardType,
      validator: validator,
      onChanged: onChanged,
      onTap: onTap,
      decoration: InputDecoration(
        labelText: labelText,
        hintText: hintText,
        prefixIcon: prefixIcon != null ? Icon(prefixIcon) : null,
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
        ),
      ),
    );
  }
}
```

## 总结

TextField 是 Flutter 中最常用的输入组件：

**核心要点**：
1. 使用 controller 管理文本
2. InputDecoration 控制外观
3. keyboardType 控制键盘类型
4. TextFormField 配合表单验证

**常用场景**：
- 登录表单
- 搜索框
- 评论输入
- 表单填写

**最佳实践**：
1. 使用 controller 并在 dispose 中释放
2. 表单验证使用 TextFormField
3. 统一输入框样式
4. 合理处理键盘遮挡

---

*最后更新: 2026-02-18*

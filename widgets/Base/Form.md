# Flutter Form 组件用法详解

## 简介

Form 是 Flutter 中用于表单管理的核心组件，它可以协调多个表单字段的状态，提供统一的验证、保存和重置功能。

## 1. 初级用法

### 1.1 基本概念

Form 组件的作用：
- 管理多个表单字段的状态
- 提供统一的验证机制
- 支持保存和重置操作
- 通过 GlobalKey 访问表单状态

### 1.2 基本语法

```dart
final _formKey = GlobalKey<FormState>();

Form(
  key: _formKey,
  child: Column(
    children: [
      TextFormField(
        validator: (value) {
          if (value == null || value.isEmpty) {
            return '请输入内容';
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
        child: Text('提交'),
      ),
    ],
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `key` | `GlobalKey<FormState>?` | 用于访问表单状态 |
| `child` | `Widget` | 子组件 |
| `onChanged` | `VoidCallback?` | 表单字段变化回调 |
| `autovalidateMode` | `AutovalidateMode` | 自动验证模式 |

### 1.4 基础示例

#### 简单登录表单

```dart
class LoginForm extends StatefulWidget {
  @override
  State<LoginForm> createState() => _LoginFormState();
}

class _LoginFormState extends State<LoginForm> {
  final _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          TextFormField(
            decoration: InputDecoration(labelText: '用户名'),
            validator: (value) {
              if (value == null || value.isEmpty) {
                return '请输入用户名';
              }
              return null;
            },
          ),
          TextFormField(
            decoration: InputDecoration(labelText: '密码'),
            obscureText: true,
            validator: (value) {
              if (value == null || value.isEmpty) {
                return '请输入密码';
              }
              if (value.length < 6) {
                return '密码至少6位';
              }
              return null;
            },
          ),
          SizedBox(height: 20),
          ElevatedButton(
            onPressed: () {
              if (_formKey.currentState!.validate()) {
                ScaffoldMessenger.of(context).showSnackBar(
                  SnackBar(content: Text('验证通过')),
                );
              }
            },
            child: Text('登录'),
          ),
        ],
      ),
    );
  }
}
```

## 2. 中级用法

### 2.1 FormState 方法

通过 GlobalKey 访问 FormState：

| 方法 | 说明 |
|------|------|
| `validate()` | 验证所有字段，返回是否通过 |
| `save()` | 调用所有字段的 onSaved 回调 |
| `reset()` | 重置所有字段 |
| `validate()` | 验证表单 |

### 2.2 保存表单数据

```dart
class MyForm extends StatefulWidget {
  @override
  State<MyForm> createState() => _MyFormState();
}

class _MyFormState extends State<MyForm> {
  final _formKey = GlobalKey<FormState>();
  String _username = '';
  String _email = '';

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          TextFormField(
            decoration: InputDecoration(labelText: '用户名'),
            onSaved: (value) => _username = value ?? '',
          ),
          TextFormField(
            decoration: InputDecoration(labelText: '邮箱'),
            onSaved: (value) => _email = value ?? '',
          ),
          ElevatedButton(
            onPressed: () {
              if (_formKey.currentState!.validate()) {
                _formKey.currentState!.save();
                print('用户名: $_username, 邮箱: $_email');
              }
            },
            child: Text('保存'),
          ),
        ],
      ),
    );
  }
}
```

### 2.3 自动验证模式

```dart
Form(
  key: _formKey,
  autovalidateMode: AutovalidateMode.onUserInteraction,  // 用户交互时验证
  child: Column(
    children: [
      TextFormField(
        validator: (value) {
          if (value == null || value.isEmpty) {
            return '必填';
          }
          return null;
        },
      ),
    ],
  ),
)
```

AutovalidateMode 取值：
- `disabled`：禁用自动验证（默认）
- `always`：总是验证
- `onUserInteraction`：用户交互后验证

### 2.4 重置表单

```dart
ElevatedButton(
  onPressed: () {
    _formKey.currentState!.reset();
  },
  child: Text('重置'),
)
```

### 2.5 常见表单场景

#### 注册表单

```dart
class RegisterForm extends StatefulWidget {
  @override
  State<RegisterForm> createState() => _RegisterFormState();
}

class _RegisterFormState extends State<RegisterForm> {
  final _formKey = GlobalKey<FormState>();
  final _passwordController = TextEditingController();
  bool _agreeToTerms = false;

  @override
  void dispose() {
    _passwordController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          TextFormField(
            decoration: InputDecoration(
              labelText: '用户名',
              prefixIcon: Icon(Icons.person),
            ),
            validator: (value) {
              if (value == null || value.isEmpty) {
                return '请输入用户名';
              }
              if (value.length < 3) {
                return '用户名至少3个字符';
              }
              return null;
            },
          ),
          TextFormField(
            decoration: InputDecoration(
              labelText: '邮箱',
              prefixIcon: Icon(Icons.email),
            ),
            keyboardType: TextInputType.emailAddress,
            validator: (value) {
              if (value == null || value.isEmpty) {
                return '请输入邮箱';
              }
              if (!value.contains('@')) {
                return '请输入有效的邮箱';
              }
              return null;
            },
          ),
          TextFormField(
            decoration: InputDecoration(
              labelText: '密码',
              prefixIcon: Icon(Icons.lock),
            ),
            controller: _passwordController,
            obscureText: true,
            validator: (value) {
              if (value == null || value.isEmpty) {
                return '请输入密码';
              }
              if (value.length < 6) {
                return '密码至少6位';
              }
              return null;
            },
          ),
          TextFormField(
            decoration: InputDecoration(
              labelText: '确认密码',
              prefixIcon: Icon(Icons.lock),
            ),
            obscureText: true,
            validator: (value) {
              if (value != _passwordController.text) {
                return '两次密码不一致';
              }
              return null;
            },
          ),
          CheckboxListTile(
            title: Text('我同意用户协议'),
            value: _agreeToTerms,
            onChanged: (value) {
              setState(() => _agreeToTerms = value ?? false);
            },
          ),
          ElevatedButton(
            onPressed: _agreeToTerms
                ? () {
                    if (_formKey.currentState!.validate()) {
                      // 提交注册
                    }
                  }
                : null,
            child: Text('注册'),
          ),
        ],
      ),
    );
  }
}
```

#### 搜索表单

```dart
class SearchForm extends StatefulWidget {
  @override
  State<SearchForm> createState() => _SearchFormState();
}

class _SearchFormState extends State<SearchForm> {
  final _formKey = GlobalKey<FormState>();
  final _searchController = TextEditingController();

  @override
  void dispose() {
    _searchController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Row(
        children: [
          Expanded(
            child: TextFormField(
              controller: _searchController,
              decoration: InputDecoration(
                hintText: '搜索...',
                prefixIcon: Icon(Icons.search),
                border: OutlineInputBorder(),
              ),
              onFieldSubmitted: (value) {
                _performSearch();
              },
            ),
          ),
          SizedBox(width: 8),
          ElevatedButton(
            onPressed: _performSearch,
            child: Text('搜索'),
          ),
        ],
      ),
    );
  }

  void _performSearch() {
    if (_formKey.currentState!.validate()) {
      final query = _searchController.text;
      print('搜索: $query');
    }
  }
}
```

## 3. 高级用法

### 3.1 自定义表单字段

使用 FormField 创建自定义表单字段：

```dart
class RatingFormField extends FormField<int> {
  RatingFormField({
    Key? key,
    int initialValue = 0,
    String? Function(int?)? validator,
    ValueChanged<int?>? onSaved,
  }) : super(
          key: key,
          initialValue: initialValue,
          validator: validator,
          onSaved: onSaved,
          builder: (FormFieldState<int> state) {
            return Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Row(
                  mainAxisSize: MainAxisSize.min,
                  children: List.generate(5, (index) {
                    return IconButton(
                      icon: Icon(
                        index < (state.value ?? 0)
                            ? Icons.star
                            : Icons.star_border,
                        color: Colors.amber,
                      ),
                      onPressed: () {
                        state.didChange(index + 1);
                      },
                    );
                  }),
                ),
                if (state.hasError)
                  Text(
                    state.errorText!,
                    style: TextStyle(color: Colors.red, fontSize: 12),
                  ),
              ],
            );
          },
        );
}

// 使用
Form(
  child: Column(
    children: [
      RatingFormField(
        validator: (value) {
          if (value == null || value == 0) {
            return '请选择评分';
          }
          return null;
        },
        onSaved: (value) => print('评分: $value'),
      ),
    ],
  ),
)
```

### 3.2 动态表单

```dart
class DynamicForm extends StatefulWidget {
  @override
  State<DynamicForm> createState() => _DynamicFormState();
}

class _DynamicFormState extends State<DynamicForm> {
  final _formKey = GlobalKey<FormState>();
  List<TextEditingController> _controllers = [TextEditingController()];

  @override
  void dispose() {
    for (var controller in _controllers) {
      controller.dispose();
    }
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          ..._controllers.asMap().entries.map((entry) {
            return Row(
              children: [
                Expanded(
                  child: TextFormField(
                    controller: entry.value,
                    decoration: InputDecoration(
                      labelText: '项目 ${entry.key + 1}',
                    ),
                    validator: (value) {
                      if (value == null || value.isEmpty) {
                        return '必填';
                      }
                      return null;
                    },
                  ),
                ),
                IconButton(
                  icon: Icon(Icons.remove_circle),
                  onPressed: _controllers.length > 1
                      ? () {
                          setState(() {
                            _controllers[entry.key].dispose();
                            _controllers.removeAt(entry.key);
                          });
                        }
                      : null,
                ),
              ],
            );
          }),
          ElevatedButton(
            onPressed: () {
              setState(() {
                _controllers.add(TextEditingController());
              });
            },
            child: Text('添加项目'),
          ),
          ElevatedButton(
            onPressed: () {
              if (_formKey.currentState!.validate()) {
                // 处理表单
              }
            },
            child: Text('提交'),
          ),
        ],
      ),
    );
  }
}
```

### 3.3 表单状态持久化

```dart
class PersistentForm extends StatefulWidget {
  @override
  State<PersistentForm> createState() => _PersistentFormState();
}

class _PersistentFormState extends State<PersistentForm> {
  final _formKey = GlobalKey<FormState>();
  final Map<String, dynamic> _formData = {};

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      onChanged: () {
        // 实时保存表单数据
        _formKey.currentState?.save();
      },
      child: Column(
        children: [
          TextFormField(
            decoration: InputDecoration(labelText: '姓名'),
            initialValue: _formData['name'],
            onSaved: (value) => _formData['name'] = value,
          ),
          TextFormField(
            decoration: InputDecoration(labelText: '电话'),
            initialValue: _formData['phone'],
            onSaved: (value) => _formData['phone'] = value,
          ),
        ],
      ),
    );
  }
}
```

### 3.4 步骤表单

```dart
class StepperForm extends StatefulWidget {
  @override
  State<StepperForm> createState() => _StepperFormState();
}

class _StepperFormState extends State<StepperForm> {
  final _formKeys = [
    GlobalKey<FormState>(),
    GlobalKey<FormState>(),
    GlobalKey<FormState>(),
  ];
  int _currentStep = 0;

  @override
  Widget build(BuildContext context) {
    return Stepper(
      currentStep: _currentStep,
      onStepContinue: () {
        if (_formKeys[_currentStep].currentState!.validate()) {
          if (_currentStep < 2) {
            setState(() => _currentStep++);
          } else {
            // 提交所有表单
          }
        }
      },
      onStepCancel: () {
        if (_currentStep > 0) {
          setState(() => _currentStep--);
        }
      },
      steps: [
        Step(
          title: Text('个人信息'),
          content: Form(
            key: _formKeys[0],
            child: Column(
              children: [
                TextFormField(
                  decoration: InputDecoration(labelText: '姓名'),
                  validator: (value) =>
                      value?.isEmpty ?? true ? '必填' : null,
                ),
              ],
            ),
          ),
        ),
        Step(
          title: Text('联系方式'),
          content: Form(
            key: _formKeys[1],
            child: Column(
              children: [
                TextFormField(
                  decoration: InputDecoration(labelText: '电话'),
                  validator: (value) =>
                      value?.isEmpty ?? true ? '必填' : null,
                ),
              ],
            ),
          ),
        ),
        Step(
          title: Text('确认'),
          content: Form(
            key: _formKeys[2],
            child: Text('请确认信息'),
          ),
        ),
      ],
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：验证不触发

**原因**：没有设置 GlobalKey 或没有调用 validate()。

```dart
// 问题：没有 key
Form(
  child: TextFormField(
    validator: (value) => '错误信息',  // 不会显示
  ),
)

// 解决：设置 key 并调用 validate
final _formKey = GlobalKey<FormState>();
Form(
  key: _formKey,
  child: Column(
    children: [
      TextFormField(
        validator: (value) => '错误信息',
      ),
      ElevatedButton(
        onPressed: () => _formKey.currentState!.validate(),
        child: Text('验证'),
      ),
    ],
  ),
)
```

### 问题2：onSaved 不触发

**原因**：需要手动调用 save() 方法。

```dart
// 问题：直接提交没有保存
ElevatedButton(
  onPressed: () {
    if (_formKey.currentState!.validate()) {
      // onSaved 没有被调用
    }
  },
  child: Text('提交'),
)

// 解决：调用 save()
ElevatedButton(
  onPressed: () {
    if (_formKey.currentState!.validate()) {
      _formKey.currentState!.save();  // 触发 onSaved
      // 处理数据
    }
  },
  child: Text('提交'),
)
```

### 问题3：TextFormField 初始值

**原因**：同时使用 controller 和 initialValue 会冲突。

```dart
// 问题：同时使用
TextFormField(
  controller: _controller,
  initialValue: '初始值',  // 会报错
)

// 解决：只用一种方式
// 方式1：使用 controller
TextFormField(
  controller: TextEditingController(text: '初始值'),
)

// 方式2：使用 initialValue
TextFormField(
  initialValue: '初始值',
)
```

## 5. Form vs 表单管理方案对比

| 方案 | 特点 | 适用场景 |
|------|------|----------|
| **Form + TextFormField** | Flutter 内置 | 简单表单 |
| **flutter_form_builder** | 第三方库 | 复杂表单 |
| **Riverpod/Form** | 状态管理 | 大型应用 |
| **BLoC 表单** | BLoC 模式 | 企业应用 |

## 6. 最佳实践

### 6.1 表单数据模型

```dart
class FormData {
  String name = '';
  String email = '';
  String phone = '';
  
  Map<String, dynamic> toJson() => {
    'name': name,
    'email': email,
    'phone': phone,
  };
}

class MyForm extends StatefulWidget {
  @override
  State<MyForm> createState() => _MyFormState();
}

class _MyFormState extends State<MyForm> {
  final _formKey = GlobalKey<FormState>();
  final _data = FormData();

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          TextFormField(
            decoration: InputDecoration(labelText: '姓名'),
            onSaved: (value) => _data.name = value ?? '',
          ),
          TextFormField(
            decoration: InputDecoration(labelText: '邮箱'),
            onSaved: (value) => _data.email = value ?? '',
          ),
          ElevatedButton(
            onPressed: () {
              if (_formKey.currentState!.validate()) {
                _formKey.currentState!.save();
                print(_data.toJson());
              }
            },
            child: Text('提交'),
          ),
        ],
      ),
    );
  }
}
```

### 6.2 验证器封装

```dart
class Validators {
  static String? required(String? value, [String? message]) {
    if (value == null || value.isEmpty) {
      return message ?? '此字段必填';
    }
    return null;
  }

  static String? email(String? value) {
    if (value == null || value.isEmpty) return null;
    if (!RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(value)) {
      return '请输入有效的邮箱';
    }
    return null;
  }

  static String? minLength(String? value, int length) {
    if (value != null && value.length < length) {
      return '至少需要 $length 个字符';
    }
    return null;
  }

  static String? phone(String? value) {
    if (value == null || value.isEmpty) return null;
    if (!RegExp(r'^1[3-9]\d{9}$').hasMatch(value)) {
      return '请输入有效的手机号';
    }
    return null;
  }
}

// 使用
TextFormField(
  validator: (value) {
    return Validators.required(value) ?? Validators.email(value);
  },
)
```

## 总结

Form 是 Flutter 表单管理的核心组件：

**核心要点**：
1. 使用 GlobalKey 访问表单状态
2. validate() 验证、save() 保存、reset() 重置
3. TextFormField 和 FormField 配合使用

**最佳实践**：
1. 使用数据模型管理表单数据
2. 封装常用验证器
3. 合理设置自动验证模式

**常见场景**：
- 登录表单
- 注册表单
- 搜索表单
- 动态表单
- 步骤表单

---

*最后更新: 2026-02-19*

# Flutter FormField 组件用法详解

## 简介

FormField 是 Flutter 中用于创建自定义表单字段的基础组件。它是 TextFormField、DropdownButtonFormField 等表单组件的底层实现，提供了表单字段的通用功能框架。

## 1. 初级用法

### 1.1 基本概念

FormField 的作用：
- 提供表单字段的通用框架
- 自动集成 Form 的验证机制
- 管理字段状态和错误显示
- 支持初始值、保存、重置等功能

### 1.2 基本语法

```dart
FormField<String>(
  initialValue: '',
  validator: (value) {
    if (value == null || value.isEmpty) {
      return '请输入内容';
    }
    return null;
  },
  builder: (FormFieldState<String> state) {
    return TextField(
      onChanged: state.didChange,
      decoration: InputDecoration(
        labelText: '输入框',
        errorText: state.errorText,
      ),
    );
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `initialValue` | `T?` | 初始值 |
| `validator` | `String? Function(T?)?` | 验证函数 |
| `onSaved` | `void Function(T?)?` | 保存回调 |
| `builder` | `Widget Function(FormFieldState<T>)` | 构建函数 |
| `autovalidateMode` | `AutovalidateMode` | 自动验证模式 |
| `enabled` | `bool` | 是否启用 |

### 1.4 FormFieldState 属性和方法

| 属性/方法 | 说明 |
|------|------|
| `value` | 当前值 |
| `hasError` | 是否有错误 |
| `errorText` | 错误文本 |
| `isValid` | 是否验证通过 |
| `didChange(T value)` | 更新值 |
| `reset()` | 重置字段 |
| `save()` | 触发 onSaved |

## 2. 中级用法

### 2.1 创建自定义表单字段

#### 自定义开关字段

```dart
class SwitchFormField extends FormField<bool> {
  SwitchFormField({
    Key? key,
    String? label,
    bool initialValue = false,
    String? Function(bool?)? validator,
    ValueChanged<bool?>? onSaved,
    FormFieldSetter<bool>? onChanged,
  }) : super(
          key: key,
          initialValue: initialValue,
          validator: validator,
          onSaved: onSaved,
          builder: (FormFieldState<bool> state) {
            return SwitchListTile(
              title: Text(label ?? ''),
              value: state.value ?? false,
              onChanged: (value) {
                state.didChange(value);
                onChanged?.call(value);
              },
              subtitle: state.hasError
                  ? Text(
                      state.errorText!,
                      style: TextStyle(color: Colors.red),
                    )
                  : null,
            );
          },
        );
}

// 使用
Form(
  child: Column(
    children: [
      SwitchFormField(
        label: '同意条款',
        validator: (value) {
          if (value != true) {
            return '必须同意条款';
          }
          return null;
        },
        onSaved: (value) => print('同意: $value'),
      ),
    ],
  ),
)
```

#### 自定义评分字段

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
                      onPressed: () => state.didChange(index + 1),
                    );
                  }),
                ),
                if (state.hasError)
                  Padding(
                    padding: EdgeInsets.only(top: 8),
                    child: Text(
                      state.errorText!,
                      style: TextStyle(color: Colors.red, fontSize: 12),
                    ),
                  ),
              ],
            );
          },
        );
}
```

### 2.2 下拉选择字段

```dart
class DropdownFormField<T> extends FormField<T> {
  DropdownFormField({
    Key? key,
    required List<DropdownMenuItem<T>> items,
    T? initialValue,
    String? hint,
    String? Function(T?)? validator,
    ValueChanged<T?>? onSaved,
    ValueChanged<T?>? onChanged,
  }) : super(
          key: key,
          initialValue: initialValue,
          validator: validator,
          onSaved: onSaved,
          builder: (FormFieldState<T> state) {
            return InputDecorator(
              decoration: InputDecoration(
                labelText: hint,
                errorText: state.errorText,
                border: OutlineInputBorder(),
              ),
              child: DropdownButtonHideUnderline(
                child: DropdownButton<T>(
                  value: state.value,
                  isDense: true,
                  items: items,
                  onChanged: (value) {
                    state.didChange(value);
                    onChanged?.call(value);
                  },
                ),
              ),
            );
          },
        );
}

// 使用
DropdownFormField<String>(
  hint: '选择城市',
  items: [
    DropdownMenuItem(value: 'beijing', child: Text('北京')),
    DropdownMenuItem(value: 'shanghai', child: Text('上海')),
    DropdownMenuItem(value: 'guangzhou', child: Text('广州')),
  ],
  validator: (value) => value == null ? '请选择城市' : null,
  onSaved: (value) => print('选择: $value'),
)
```

### 2.3 日期选择字段

```dart
class DateFormField extends FormField<DateTime> {
  DateFormField({
    Key? key,
    DateTime? initialValue,
    String? label,
    String? Function(DateTime?)? validator,
    ValueChanged<DateTime?>? onSaved,
  }) : super(
          key: key,
          initialValue: initialValue,
          validator: validator,
          onSaved: onSaved,
          builder: (FormFieldState<DateTime> state) {
            return InputDecorator(
              decoration: InputDecoration(
                labelText: label ?? '日期',
                errorText: state.errorText,
                suffixIcon: Icon(Icons.calendar_today),
              ),
              child: InkWell(
                onTap: () async {
                  final date = await showDatePicker(
                    context: state.context,
                    initialDate: state.value ?? DateTime.now(),
                    firstDate: DateTime(2000),
                    lastDate: DateTime(2100),
                  );
                  if (date != null) {
                    state.didChange(date);
                  }
                },
                child: Text(
                  state.value != null
                      ? '${state.value!.year}-${state.value!.month.toString().padLeft(2, '0')}-${state.value!.day.toString().padLeft(2, '0')}'
                      : '选择日期',
                ),
              ),
            );
          },
        );
}
```

### 2.4 多选字段

```dart
class MultiSelectFormField<T> extends FormField<List<T>> {
  MultiSelectFormField({
    Key? key,
    required List<T> options,
    required String Function(T) labelBuilder,
    List<T>? initialValue,
    String? hint,
    String? Function(List<T>?)? validator,
    ValueChanged<List<T>?>? onSaved,
  }) : super(
          key: key,
          initialValue: initialValue ?? [],
          validator: validator,
          onSaved: onSaved,
          builder: (FormFieldState<List<T>> state) {
            return InputDecorator(
              decoration: InputDecoration(
                labelText: hint,
                errorText: state.errorText,
              ),
              child: Wrap(
                spacing: 8,
                children: options.map((option) {
                  final isSelected = state.value?.contains(option) ?? false;
                  return FilterChip(
                    label: Text(labelBuilder(option)),
                    selected: isSelected,
                    onSelected: (selected) {
                      final newValue = List<T>.from(state.value ?? []);
                      if (selected) {
                        newValue.add(option);
                      } else {
                        newValue.remove(option);
                      }
                      state.didChange(newValue);
                    },
                  );
                }).toList(),
              ),
            );
          },
        );
}

// 使用
MultiSelectFormField<String>(
  hint: '选择标签',
  options: ['工作', '生活', '学习', '娱乐'],
  labelBuilder: (tag) => tag,
  validator: (value) {
    if (value == null || value.isEmpty) {
      return '请至少选择一个标签';
    }
    return null;
  },
)
```

## 3. 高级用法

### 3.1 带图标和装饰的字段

```dart
class IconFormField extends FormField<String> {
  IconFormField({
    Key? key,
    required IconData icon,
    String? initialValue,
    String? hint,
    String? Function(String?)? validator,
    ValueChanged<String?>? onSaved,
    bool obscureText = false,
  }) : super(
          key: key,
          initialValue: initialValue,
          validator: validator,
          onSaved: onSaved,
          builder: (FormFieldState<String> state) {
            return Container(
              decoration: BoxDecoration(
                color: Colors.grey[100],
                borderRadius: BorderRadius.circular(12),
                border: state.hasError
                    ? Border.all(color: Colors.red)
                    : null,
              ),
              padding: EdgeInsets.symmetric(horizontal: 16),
              child: Row(
                children: [
                  Icon(icon, color: Colors.grey),
                  SizedBox(width: 12),
                  Expanded(
                    child: TextField(
                      onChanged: state.didChange,
                      obscureText: obscureText,
                      decoration: InputDecoration(
                        hintText: hint,
                        border: InputBorder.none,
                      ),
                    ),
                  ),
                ],
              ),
            );
          },
        );
}
```

### 3.2 异步验证

```dart
class AsyncValidatorField extends FormField<String> {
  AsyncValidatorField({
    Key? key,
    String? initialValue,
    String? Function(String?)? validator,
    Future<String?> Function(String?)? asyncValidator,
    ValueChanged<String?>? onSaved,
  }) : super(
          key: key,
          initialValue: initialValue,
          validator: validator,
          onSaved: onSaved,
          builder: (FormFieldState<String> state) {
            return Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                TextField(
                  onChanged: (value) async {
                    state.didChange(value);
                    if (asyncValidator != null) {
                      final error = await asyncValidator(value);
                      // 需要在自定义 FormFieldState 中处理
                    }
                  },
                  decoration: InputDecoration(
                    labelText: '用户名',
                    suffixIcon: state.isValidating
                        ? SizedBox(
                            width: 16,
                            height: 16,
                            child: CircularProgressIndicator(strokeWidth: 2),
                          )
                        : null,
                    errorText: state.errorText,
                  ),
                ),
              ],
            );
          },
        );
}
```

### 3.3 级联字段

```dart
class CascadeFormField extends StatefulWidget {
  @override
  State<CascadeFormField> createState() => _CascadeFormFieldState();
}

class _CascadeFormFieldState extends State<CascadeFormField> {
  String? _province;
  String? _city;
  final _cityKey = GlobalKey<FormFieldState>();

  final Map<String, List<String>> _cities = {
    '北京': ['东城区', '西城区', '朝阳区'],
    '上海': ['黄浦区', '徐汇区', '浦东新区'],
    '广东': ['广州市', '深圳市', '珠海市'],
  };

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        FormField<String>(
          initialValue: _province,
          onSaved: (value) => _province = value,
          builder: (state) {
            return DropdownButtonFormField<String>(
              value: state.value,
              decoration: InputDecoration(labelText: '省份'),
              items: _cities.keys.map((p) {
                return DropdownMenuItem(value: p, child: Text(p));
              }).toList(),
              onChanged: (value) {
                state.didChange(value);
                _cityKey.currentState?.didChange(null);
              },
            );
          },
        ),
        SizedBox(height: 16),
        FormField<String>(
          key: _cityKey,
          initialValue: _city,
          onSaved: (value) => _city = value,
          builder: (state) {
            final cities = _province != null ? _cities[_province]! : <String>[];
            return DropdownButtonFormField<String>(
              value: cities.contains(state.value) ? state.value : null,
              decoration: InputDecoration(labelText: '城市'),
              items: cities.map((c) {
                return DropdownMenuItem(value: c, child: Text(c));
              }).toList(),
              onChanged: (value) => state.didChange(value),
            );
          },
        ),
      ],
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：状态更新不触发重建

**原因**：需要调用 `state.didChange()` 而不是直接修改值。

```dart
// 问题：直接修改值
builder: (state) {
  return TextField(
    onChanged: (value) {
      // 错误：state.value = value;  // 不能直接赋值
    },
  );
}

// 解决：使用 didChange
builder: (state) {
  return TextField(
    onChanged: (value) {
      state.didChange(value);  // 正确
    },
  );
}
```

### 问题2：错误文本不显示

**原因**：需要在 builder 中使用 `state.errorText`。

```dart
// 问题：没有显示错误
builder: (state) {
  return TextField(
    decoration: InputDecoration(
      labelText: '输入',
      // 缺少 errorText
    ),
  );
}

// 解决：添加 errorText
builder: (state) {
  return TextField(
    decoration: InputDecoration(
      labelText: '输入',
      errorText: state.errorText,  // 显示错误
    ),
  );
}
```

### 问题3：泛型类型问题

```dart
// 问题：泛型推断失败
FormField(value: null, ...)  // 无法推断类型

// 解决：明确指定类型
FormField<String>(initialValue: null, ...)
```

## 5. FormField vs TextFormField 对比

| 特性 | FormField | TextFormField |
|------|-----------|---------------|
| 灵活性 | 高，可自定义任何 UI | 低，固定为 TextField |
| 使用难度 | 较高 | 简单 |
| 适用场景 | 自定义表单字段 | 标准文本输入 |

### 选择建议

```dart
// 标准文本输入 → 使用 TextFormField
TextFormField(
  decoration: InputDecoration(labelText: '用户名'),
  validator: (value) => value?.isEmpty ?? true ? '必填' : null,
)

// 自定义字段 → 使用 FormField
FormField<bool>(
  builder: (state) => SwitchListTile(
    title: Text('同意条款'),
    value: state.value ?? false,
    onChanged: state.didChange,
  ),
)
```

## 6. 最佳实践

### 6.1 可复用字段封装

```dart
abstract class BaseFormField<T> extends FormField<T> {
  BaseFormField({
    super.key,
    super.initialValue,
    super.validator,
    super.onSaved,
    super.autovalidateMode,
    super.enabled,
    required super.builder,
  });
}
```

### 6.2 统一样式

```dart
class AppFormStyles {
  static InputDecoration inputDecoration(String label) {
    return InputDecoration(
      labelText: label,
      border: OutlineInputBorder(),
      contentPadding: EdgeInsets.symmetric(horizontal: 16, vertical: 12),
    );
  }
}

// 在 FormField builder 中使用
builder: (state) {
  return TextField(
    decoration: AppFormStyles.inputDecoration('标签').copyWith(
      errorText: state.errorText,
    ),
  );
}
```

### 6.3 字段值转换

```dart
class IntFormField extends FormField<int> {
  IntFormField({
    Key? key,
    int initialValue = 0,
    String? label,
    String? Function(int?)? validator,
    ValueChanged<int?>? onSaved,
  }) : super(
          key: key,
          initialValue: initialValue,
          validator: validator,
          onSaved: onSaved,
          builder: (FormFieldState<int> state) {
            return TextField(
              decoration: InputDecoration(
                labelText: label,
                errorText: state.errorText,
              ),
              keyboardType: TextInputType.number,
              controller: TextEditingController(text: state.value.toString()),
              onChanged: (value) {
                final intValue = int.tryParse(value);
                state.didChange(intValue ?? 0);
              },
            );
          },
        );
}
```

## 总结

FormField 是创建自定义表单字段的基石：

**核心要点**：
1. 使用泛型 `FormField<T>` 支持任意类型
2. builder 函数负责构建 UI
3. `state.didChange()` 更新值和状态

**最佳实践**：
1. 封装可复用的表单字段
2. 统一样式和验证器
3. 合理使用 `errorText` 显示错误

**常见场景**：
- 自定义开关字段
- 评分字段
- 下拉选择
- 日期选择
- 多选字段

---

*最后更新: 2026-02-19*

# Flutter DatePicker 组件用法详解

## 简介

DatePicker 是日期选择器组件，用于让用户选择日期。Flutter 提供了 showDatePicker 方法来显示 Material Design 风格的日期选择对话框。

## 1. 基本用法

### 1.1 基本概念

DatePicker 的核心特点：
- 模态对话框形式显示
- 支持日期范围限制
- 支持多语言
- Material 3 重新设计

### 1.2 基本语法

```dart
final DateTime? selectedDate = await showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
);
```

### 1.3 主要参数

| 参数 | 类型 | 说明 |
|------|------|------|
| `context` | `BuildContext` | 上下文（必需） |
| `initialDate` | `DateTime` | 初始日期（必需） |
| `firstDate` | `DateTime` | 可选最早日期（必需） |
| `lastDate` | `DateTime` | 可选最晚日期（必需） |
| `currentDate` | `DateTime?` | 当前日期标记 |
| `initialEntryMode` | `DatePickerEntryMode` | 初始输入模式 |
| `selectableDayPredicate` | `SelectableDayPredicate?` | 可选日期判断 |
| `initialCalendarMode` | `DatePickerMode` | 初始日历模式 |
| `locale` | `Locale?` | 语言环境 |
| `textDirection` | `TextDirection?` | 文字方向 |
| `builder` | `TransitionBuilder?` | 自定义构建器 |
| `cancelText` | `String?` | 取消按钮文字 |
| `confirmText` | `String?` | 确认按钮文字 |
| `fieldHintText` | `String?` | 输入框提示 |
| `fieldLabelText` | `String?` | 输入框标签 |
| `helpText` | `String?` | 帮助文字 |
| `errorFormatText` | `String?` | 格式错误提示 |
| `errorInvalidText` | `String?` | 无效日期提示 |

### 1.4 基础示例

#### 简单日期选择

```dart
class SimpleDatePicker extends StatefulWidget {
  @override
  _SimpleDatePickerState createState() => _SimpleDatePickerState();
}

class _SimpleDatePickerState extends State<SimpleDatePicker> {
  DateTime? _selectedDate;

  Future<void> _selectDate() async {
    final DateTime? picked = await showDatePicker(
      context: context,
      initialDate: _selectedDate ?? DateTime.now(),
      firstDate: DateTime(2000),
      lastDate: DateTime(2030),
    );
    if (picked != null && picked != _selectedDate) {
      setState(() {
        _selectedDate = picked;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text(
          _selectedDate != null
              ? '${_selectedDate!.year}-${_selectedDate!.month}-${_selectedDate!.day}'
              : '请选择日期',
        ),
        ElevatedButton(
          onPressed: _selectDate,
          child: Text('选择日期'),
        ),
      ],
    );
  }
}
```

#### 带格式化的日期选择

```dart
class FormattedDatePicker extends StatefulWidget {
  @override
  _FormattedDatePickerState createState() => _FormattedDatePickerState();
}

class _FormattedDatePickerState extends State<FormattedDatePicker> {
  DateTime? _selectedDate;

  String _formatDate(DateTime date) {
    return '${date.year}年${date.month}月${date.day}日';
  }

  Future<void> _selectDate() async {
    final DateTime? picked = await showDatePicker(
      context: context,
      initialDate: _selectedDate ?? DateTime.now(),
      firstDate: DateTime(2000),
      lastDate: DateTime(2030),
      locale: Locale('zh', 'CN'),  // 中文显示
    );
    if (picked != null) {
      setState(() {
        _selectedDate = picked;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return ListTile(
      title: Text('选择日期'),
      subtitle: Text(_selectedDate != null ? _formatDate(_selectedDate!) : '未选择'),
      trailing: Icon(Icons.calendar_today),
      onTap: _selectDate,
    );
  }
}
```

## 2. 输入模式

### 2.1 日历模式（默认）

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  initialEntryMode: DatePickerEntryMode.calendar,
)
```

### 2.2 输入框模式

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  initialEntryMode: DatePickerEntryMode.input,
)
```

### 2.3 仅日历模式

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  initialEntryMode: DatePickerEntryMode.calendarOnly,
)
```

### 2.4 仅输入模式

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  initialEntryMode: DatePickerEntryMode.inputOnly,
)
```

## 3. 日历视图模式

### 3.1 日期模式（默认）

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  initialCalendarMode: DatePickerMode.day,
)
```

### 3.2 年份模式

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  initialCalendarMode: DatePickerMode.year,
)
```

## 4. 日期范围限制

### 4.1 基本范围限制

```dart
// 只能选择未来 7 天内的日期
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime.now(),
  lastDate: DateTime.now().add(Duration(days: 7)),
)
```

### 4.2 自定义可选日期

```dart
// 只能选择周一到周五
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  selectableDayPredicate: (DateTime date) {
    // 返回 true 表示可选
    return date.weekday != DateTime.saturday && date.weekday != DateTime.sunday;
  },
)

// 只能选择特定日期
showDatePicker(
  selectableDayPredicate: (DateTime date) {
    // 只能选择每月的 1 号和 15 号
    return date.day == 1 || date.day == 15;
  },
)
```

### 4.3 排除特定日期

```dart
final List<DateTime> holidays = [
  DateTime(2024, 1, 1),
  DateTime(2024, 12, 25),
];

showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2024),
  lastDate: DateTime(2025),
  selectableDayPredicate: (DateTime date) {
    // 排除节假日
    return !holidays.any((holiday) =>
      holiday.year == date.year &&
      holiday.month == date.month &&
      holiday.day == date.day
    );
  },
)
```

## 5. 自定义样式

### 5.1 使用 Builder 自定义

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  builder: (context, child) {
    return Theme(
      data: ThemeData.dark(),
      child: child!,
    );
  },
)
```

### 5.2 自定义颜色

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  builder: (context, child) {
    return Theme(
      data: Theme.of(context).copyWith(
        colorScheme: ColorScheme.light(
          primary: Colors.blue,
          onPrimary: Colors.white,
          surface: Colors.white,
          onSurface: Colors.black,
        ),
      ),
      child: child!,
    );
  },
)
```

### 5.3 自定义文字

```dart
showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2000),
  lastDate: DateTime(2030),
  helpText: '选择出生日期',
  cancelText: '取消',
  confirmText: '确定',
  fieldHintText: '请输入日期',
  fieldLabelText: '日期',
  errorFormatText: '日期格式错误',
  errorInvalidText: '日期不在有效范围内',
)
```

## 6. 时间选择器

### 6.1 showTimePicker

```dart
final TimeOfDay? selectedTime = await showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
);

// 使用
if (selectedTime != null) {
  print('${selectedTime.hour}:${selectedTime.minute}');
}
```

### 6.2 24 小时制

```dart
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  builder: (context, child) {
    return MediaQuery(
      data: MediaQuery.of(context).copyWith(alwaysUse24HourFormat: true),
      child: child!,
    );
  },
)
```

### 6.3 自定义时间选择器

```dart
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  helpText: '选择时间',
  cancelText: '取消',
  confirmText: '确定',
  hourLabelText: '小时',
  minuteLabelText: '分钟',
  errorInvalidText: '无效时间',
)
```

### 6.4 时间输入模式

```dart
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  initialEntryMode: TimePickerEntryMode.input,
)

// 或仅输入
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  initialEntryMode: TimePickerEntryMode.inputOnly,
)
```

## 7. 高级用法

### 7.1 日期范围选择

```dart
class DateRangePicker extends StatefulWidget {
  @override
  _DateRangePickerState createState() => _DateRangePickerState();
}

class _DateRangePickerState extends State<DateRangePicker> {
  DateTimeRange? _selectedRange;

  Future<void> _selectDateRange() async {
    final DateTimeRange? picked = await showDateRangePicker(
      context: context,
      firstDate: DateTime(2000),
      lastDate: DateTime(2030),
      initialDateRange: _selectedRange,
      saveText: '确定',
    );
    if (picked != null) {
      setState(() {
        _selectedRange = picked;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text(
          _selectedRange != null
              ? '${_selectedRange!.start.year}-${_selectedRange!.start.month}-${_selectedRange!.start.day} 至 ${_selectedRange!.end.year}-${_selectedRange!.end.month}-${_selectedRange!.end.day}'
              : '请选择日期范围',
        ),
        ElevatedButton(
          onPressed: _selectDateRange,
          child: Text('选择日期范围'),
        ),
      ],
    );
  }
}
```

### 7.2 封装日期选择组件

```dart
class DatePickerField extends StatelessWidget {
  final String label;
  final DateTime? value;
  final ValueChanged<DateTime>? onChanged;
  final DateTime? firstDate;
  final DateTime? lastDate;

  const DatePickerField({
    required this.label,
    this.value,
    this.onChanged,
    this.firstDate,
    this.lastDate,
    Key? key,
  }) : super(key: key);

  Future<void> _selectDate(BuildContext context) async {
    final DateTime? picked = await showDatePicker(
      context: context,
      initialDate: value ?? DateTime.now(),
      firstDate: firstDate ?? DateTime(1900),
      lastDate: lastDate ?? DateTime(2100),
      locale: Locale('zh', 'CN'),
    );
    if (picked != null) {
      onChanged?.call(picked);
    }
  }

  String _formatDate(DateTime date) {
    return '${date.year}-${date.month.toString().padLeft(2, '0')}-${date.day.toString().padLeft(2, '0')}';
  }

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: () => _selectDate(context),
      child: InputDecorator(
        decoration: InputDecoration(
          labelText: label,
          suffixIcon: Icon(Icons.calendar_today),
        ),
        child: Text(
          value != null ? _formatDate(value!) : '请选择',
          style: TextStyle(
            color: value != null ? null : Colors.grey,
          ),
        ),
      ),
    );
  }
}

// 使用
DatePickerField(
  label: '出生日期',
  value: _birthDate,
  firstDate: DateTime(1900),
  lastDate: DateTime.now(),
  onChanged: (date) {
    setState(() => _birthDate = date);
  },
)
```

### 7.3 时间日期组合选择

```dart
class DateTimePicker extends StatefulWidget {
  final String label;
  final DateTime? value;
  final ValueChanged<DateTime>? onChanged;

  const DateTimePicker({
    required this.label,
    this.value,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _DateTimePickerState createState() => _DateTimePickerState();
}

class _DateTimePickerState extends State<DateTimePicker> {
  late DateTime _date;
  late TimeOfDay _time;

  @override
  void initState() {
    super.initState();
    _date = widget.value ?? DateTime.now();
    _time = widget.value != null
        ? TimeOfDay.fromDateTime(widget.value!)
        : TimeOfDay.now();
  }

  Future<void> _selectDate() async {
    final picked = await showDatePicker(
      context: context,
      initialDate: _date,
      firstDate: DateTime(2000),
      lastDate: DateTime(2030),
    );
    if (picked != null) {
      setState(() => _date = picked);
      _updateValue();
    }
  }

  Future<void> _selectTime() async {
    final picked = await showTimePicker(
      context: context,
      initialTime: _time,
    );
    if (picked != null) {
      setState(() => _time = picked);
      _updateValue();
    }
  }

  void _updateValue() {
    final dateTime = DateTime(
      _date.year,
      _date.month,
      _date.day,
      _time.hour,
      _time.minute,
    );
    widget.onChanged?.call(dateTime);
  }

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Expanded(
          child: ListTile(
            title: Text('日期'),
            subtitle: Text('${_date.year}-${_date.month}-${_date.day}'),
            trailing: Icon(Icons.calendar_today),
            onTap: _selectDate,
          ),
        ),
        Expanded(
          child: ListTile(
            title: Text('时间'),
            subtitle: Text('${_time.hour}:${_time.minute.toString().padLeft(2, '0')}'),
            trailing: Icon(Icons.access_time),
            onTap: _selectTime,
          ),
        ),
      ],
    );
  }
}
```

## 8. 常见问题与解决方案

### 问题1：中文显示问题

```dart
// 在 MaterialApp 中设置本地化
MaterialApp(
  localizationsDelegates: [
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
  ],
  supportedLocales: [
    Locale('zh', 'CN'),
    Locale('en', 'US'),
  ],
)

// 或在 showDatePicker 中指定
showDatePicker(
  context: context,
  locale: Locale('zh', 'CN'),
  // ...
)
```

### 问题2：日期格式化

```dart
// 使用 intl 包
import 'package:intl/intl.dart';

String formattedDate = DateFormat('yyyy-MM-dd').format(date);
String formattedDate = DateFormat('yyyy年MM月dd日').format(date);
String formattedDate = DateFormat('MM/dd/yyyy').format(date);
```

### 问题3：默认日期超出范围

```dart
// 确保 initialDate 在 firstDate 和 lastDate 之间
DateTime initialDate = DateTime.now();
DateTime firstDate = DateTime(2000);
DateTime lastDate = DateTime(2030);

// 修正
if (initialDate.isBefore(firstDate)) {
  initialDate = firstDate;
} else if (initialDate.isAfter(lastDate)) {
  initialDate = lastDate;
}
```

### 问题4：样式与主题不一致

```dart
showDatePicker(
  builder: (context, child) {
    return Theme(
      data: Theme.of(context).copyWith(
        // 自定义样式
      ),
      child: child!,
    );
  },
)
```

## 9. 最佳实践

### 9.1 统一日期工具类

```dart
class DateUtils {
  static String formatDate(DateTime date, {String format = 'yyyy-MM-dd'}) {
    return DateFormat(format).format(date);
  }

  static String formatDateCN(DateTime date) {
    return '${date.year}年${date.month}月${date.day}日';
  }

  static bool isSameDay(DateTime a, DateTime b) {
    return a.year == b.year && a.month == b.month && a.day == b.day;
  }

  static int daysBetween(DateTime a, DateTime b) {
    return b.difference(a).inDays;
  }
}
```

### 9.2 预设日期范围

```dart
class DateRangePresets {
  static DateTimeRange get today => DateTimeRange(
    start: DateTime.now(),
    end: DateTime.now(),
  );

  static DateTimeRange get thisWeek => DateTimeRange(
    start: DateTime.now().subtract(Duration(days: DateTime.now().weekday - 1)),
    end: DateTime.now().add(Duration(days: 7 - DateTime.now().weekday)),
  );

  static DateTimeRange get thisMonth => DateTimeRange(
    start: DateTime(DateTime.now().year, DateTime.now().month, 1),
    end: DateTime(DateTime.now().year, DateTime.now().month + 1, 0),
  );

  static DateTimeRange get last7Days => DateTimeRange(
    start: DateTime.now().subtract(Duration(days: 7)),
    end: DateTime.now(),
  );

  static DateTimeRange get last30Days => DateTimeRange(
    start: DateTime.now().subtract(Duration(days: 30)),
    end: DateTime.now(),
  );
}
```

## 总结

Flutter DatePicker 提供了完整的日期选择解决方案：

**showDatePicker** - 日期选择对话框
**showTimePicker** - 时间选择对话框
**showDateRangePicker** - 日期范围选择

**核心参数**：
- `initialDate` - 初始日期
- `firstDate` / `lastDate` - 可选范围
- `selectableDayPredicate` - 可选日期判断
- `locale` - 语言环境

**最佳实践**：
1. 设置正确的日期范围
2. 配置中文本地化
3. 使用 builder 统一样式
4. 封装通用日期选择组件

---

*最后更新: 2026-02-18*

# Flutter TimePicker 组件用法详解

## 简介

TimePicker 是时间选择器组件，Material 3 中经过重新设计，提供了更现代的视觉体验。支持 12 小时制和 24 小时制两种模式。

## 1. 初级用法

### 1.1 基本概念

TimePicker 的核心特点：
- Material 3 设计风格
- 支持 12/24 小时制
- 支持输入模式切换
- 支持自定义样式

### 1.2 基本语法

```dart
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
).then((value) {
  if (value != null) {
    print('选择的时间: ${value.format(context)}');
  }
});
```

### 1.3 showTimePicker 参数

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `context` | `BuildContext` | - | 上下文（必需） |
| `initialTime` | `TimeOfDay` | - | 初始时间（必需） |
| `builder` | `TransitionBuilder?` | - | 构建器 |
| `useRootNavigator` | `bool` | `true` | 是否使用根导航器 |
| `initialEntryMode` | `TimePickerEntryMode` | `dial` | 初始输入模式 |
| `cancelText` | `String?` | - | 取消按钮文字 |
| `confirmText` | `String?` | - | 确认按钮文字 |
| `helpText` | `String?` | - | 帮助文字 |
| `errorInvalidText` | `String?` | - | 无效时间错误文字 |
| `hourLabelText` | `String?` | - | 小时标签 |
| `minuteLabelText` | `String?` | - | 分钟标签 |
| `orientation` | `Orientation?` | - | 强制方向 |
| `onEntryModeChanged` | `ValueChanged<TimePickerEntryMode>?` | - | 输入模式变化回调 |

### 1.4 TimeOfDay 属性和方法

| 属性/方法 | 类型 | 说明 |
|---------|------|------|
| `hour` | `int` | 小时 |
| `minute` | `int` | 分钟 |
| `now()` | `TimeOfDay` | 当前时间 |
| `format(context)` | `String` | 格式化显示 |
| `replacing(hour, minute)` | `TimeOfDay` | 替换时间 |

### 1.5 基础示例

#### 简单时间选择

```dart
class SimpleTimePicker extends StatefulWidget {
  @override
  _SimpleTimePickerState createState() => _SimpleTimePickerState();
}

class _SimpleTimePickerState extends State<SimpleTimePicker> {
  TimeOfDay? _selectedTime;

  Future<void> _selectTime() async {
    final time = await showTimePicker(
      context: context,
      initialTime: _selectedTime ?? TimeOfDay.now(),
    );
    
    if (time != null) {
      setState(() {
        _selectedTime = time;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text(
          _selectedTime != null
              ? '选择的时间: ${_selectedTime!.format(context)}'
              : '请选择时间',
          style: TextStyle(fontSize: 18),
        ),
        SizedBox(height: 16),
        ElevatedButton(
          onPressed: _selectTime,
          child: Text('选择时间'),
        ),
      ],
    );
  }
}
```

#### 显示选中时间

```dart
Text('选择的时间: ${_selectedTime?.format(context) ?? "未选择"}')
// 输出: 选择的时间: 下午 3:30 或 选择的时间: 15:30（根据设备设置）
```

## 2. 中级用法

### 2.1 输入模式

```dart
// 钟表模式（默认）
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  initialEntryMode: TimePickerEntryMode.dial,
)

// 输入模式
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  initialEntryMode: TimePickerEntryMode.input,
)

// 仅输入模式
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  initialEntryMode: TimePickerEntryMode.inputOnly,
)
```

### 2.2 自定义按钮文字

```dart
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  cancelText: '取消',
  confirmText: '确定',
  helpText: '选择提醒时间',
)
```

### 2.3 24 小时制

```dart
// 12/24 小时制由系统设置决定
// 可通过 builder 强制显示
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

### 2.4 自定义主题

```dart
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  builder: (context, child) {
    return Theme(
      data: Theme.of(context).copyWith(
        timePickerTheme: TimePickerThemeData(
          backgroundColor: Colors.blue[50],
          hourMinuteColor: Colors.blue[100],
          hourMinuteTextColor: Colors.blue,
          dialHandColor: Colors.blue,
          dialBackgroundColor: Colors.blue[50],
          entryModeIconColor: Colors.blue,
        ),
      ),
      child: child!,
    );
  },
)
```

### 2.5 监听模式变化

```dart
showTimePicker(
  context: context,
  initialTime: TimeOfDay.now(),
  onEntryModeChanged: (mode) {
    print('输入模式变化: $mode');
    // dial, input, inputOnly
  },
)
```

## 3. TimePickerThemeData 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `backgroundColor` | `Color?` | 背景色 |
| `hourMinuteColor` | `Color?` | 时分背景色 |
| `hourMinuteTextColor` | `Color?` | 时分文字颜色 |
| `hourMinuteShape` | `ShapeBorder?` | 时分形状 |
| `dayPeriodColor` | `Color?` | 上午/下午背景色 |
| `dayPeriodTextColor` | `Color?` | 上午/下午文字颜色 |
| `dayPeriodShape` | `OutlinedBorder?` | 上午/下午形状 |
| `dayPeriodBorderSide` | `BorderSide?` | 上午/下午边框 |
| `dialHandColor` | `Color?` | 指针颜色 |
| `dialBackgroundColor` | `Color?` | 表盘背景色 |
| `dialTextColor` | `Color?` | 表盘文字颜色 |
| `entryModeIconColor` | `Color?` | 输入模式图标颜色 |
| `helpTextStyle` | `TextStyle?` | 帮助文字样式 |
| `shape` | `ShapeBorder?` | 整体形状 |
| `hourMinuteTextStyle` | `TextStyle?` | 时分文字样式 |
| `dayPeriodTextStyle` | `TextStyle?` | 上午/下午文字样式 |
| `dialTextStyle` | `TextStyle?` | 表盘文字样式 |

## 4. 高级用法

### 4.1 时间选择按钮

```dart
class TimePickerButton extends StatefulWidget {
  final TimeOfDay? initialTime;
  final ValueChanged<TimeOfDay>? onTimeSelected;

  const TimePickerButton({
    this.initialTime,
    this.onTimeSelected,
    Key? key,
  }) : super(key: key);

  @override
  _TimePickerButtonState createState() => _TimePickerButtonState();
}

class _TimePickerButtonState extends State<TimePickerButton> {
  TimeOfDay? _selectedTime;

  @override
  void initState() {
    super.initState();
    _selectedTime = widget.initialTime;
  }

  Future<void> _selectTime() async {
    final time = await showTimePicker(
      context: context,
      initialTime: _selectedTime ?? TimeOfDay.now(),
    );
    
    if (time != null) {
      setState(() => _selectedTime = time);
      widget.onTimeSelected?.call(time);
    }
  }

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: _selectTime,
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 16, vertical: 12),
        decoration: BoxDecoration(
          border: Border.all(color: Colors.grey),
          borderRadius: BorderRadius.circular(8),
        ),
        child: Row(
          mainAxisSize: MainAxisSize.min,
          children: [
            Icon(Icons.access_time),
            SizedBox(width: 8),
            Text(
              _selectedTime?.format(context) ?? '选择时间',
              style: TextStyle(fontSize: 16),
            ),
            SizedBox(width: 8),
            Icon(Icons.arrow_drop_down),
          ],
        ),
      ),
    );
  }
}

// 使用
TimePickerButton(
  initialTime: TimeOfDay(hour: 9, minute: 0),
  onTimeSelected: (time) {
    print('选择: ${time.format(context)}');
  },
)
```

### 4.2 时间范围选择

```dart
class TimeRangePicker extends StatefulWidget {
  final TimeOfDay? startTime;
  final TimeOfDay? endTime;
  final ValueChanged<TimeRange>? onChanged;

  const TimeRangePicker({
    this.startTime,
    this.endTime,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _TimeRangePickerState createState() => _TimeRangePickerState();
}

class _TimeRangePickerState extends State<TimeRangePicker> {
  TimeOfDay? _startTime;
  TimeOfDay? _endTime;

  @override
  void initState() {
    super.initState();
    _startTime = widget.startTime;
    _endTime = widget.endTime;
  }

  Future<void> _selectStartTime() async {
    final time = await showTimePicker(
      context: context,
      initialTime: _startTime ?? TimeOfDay(hour: 9, minute: 0),
    );
    if (time != null) {
      setState(() => _startTime = time);
      _notifyChange();
    }
  }

  Future<void> _selectEndTime() async {
    final time = await showTimePicker(
      context: context,
      initialTime: _endTime ?? TimeOfDay(hour: 18, minute: 0),
    );
    if (time != null) {
      setState(() => _endTime = time);
      _notifyChange();
    }
  }

  void _notifyChange() {
    if (_startTime != null && _endTime != null) {
      widget.onChanged?.call(TimeRange(_startTime!, _endTime!));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Expanded(
          child: InkWell(
            onTap: _selectStartTime,
            child: Container(
              padding: EdgeInsets.all(12),
              decoration: BoxDecoration(
                border: Border.all(color: Colors.grey),
                borderRadius: BorderRadius.circular(8),
              ),
              child: Center(
                child: Text(
                  _startTime?.format(context) ?? '开始时间',
                  style: TextStyle(fontSize: 16),
                ),
              ),
            ),
          ),
        ),
        Padding(
          padding: EdgeInsets.symmetric(horizontal: 8),
          child: Text('至'),
        ),
        Expanded(
          child: InkWell(
            onTap: _selectEndTime,
            child: Container(
              padding: EdgeInsets.all(12),
              decoration: BoxDecoration(
                border: Border.all(color: Colors.grey),
                borderRadius: BorderRadius.circular(8),
              ),
              child: Center(
                child: Text(
                  _endTime?.format(context) ?? '结束时间',
                  style: TextStyle(fontSize: 16),
                ),
              ),
            ),
          ),
        ),
      ],
    );
  }
}

class TimeRange {
  final TimeOfDay start;
  final TimeOfDay end;

  TimeRange(this.start, this.end);
}

// 使用
TimeRangePicker(
  startTime: TimeOfDay(hour: 9, minute: 0),
  endTime: TimeOfDay(hour: 17, minute: 0),
  onChanged: (range) {
    print('时间范围: ${range.start.format(context)} - ${range.end.format(context)}');
  },
)
```

### 4.3 提醒时间选择

```dart
class ReminderTimePicker extends StatelessWidget {
  final TimeOfDay? selectedTime;
  final ValueChanged<TimeOfDay?> onChanged;

  const ReminderTimePicker({
    this.selectedTime,
    required this.onChanged,
    Key? key,
  }) : super(key: key);

  Future<void> _pickTime(BuildContext context) async {
    final time = await showTimePicker(
      context: context,
      initialTime: selectedTime ?? TimeOfDay(hour: 9, minute: 0),
      helpText: '选择提醒时间',
    );
    onChanged(time);
  }

  @override
  Widget build(BuildContext context) {
    return ListTile(
      leading: Icon(Icons.alarm),
      title: Text('提醒时间'),
      subtitle: Text(selectedTime?.format(context) ?? '未设置'),
      trailing: selectedTime != null
          ? IconButton(
              icon: Icon(Icons.clear),
              onPressed: () => onChanged(null),
            )
          : null,
      onTap: () => _pickTime(context),
    );
  }
}
```

### 4.4 闹钟时间列表

```dart
class AlarmList extends StatefulWidget {
  @override
  _AlarmListState createState() => _AlarmListState();
}

class _AlarmListState extends State<AlarmList> {
  final List<Alarm> _alarms = [];

  Future<void> _addAlarm() async {
    final time = await showTimePicker(
      context: context,
      initialTime: TimeOfDay.now(),
    );
    
    if (time != null) {
      setState(() {
        _alarms.add(Alarm(time: time, enabled: true));
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('闹钟'),
        actions: [
          IconButton(
            icon: Icon(Icons.add),
            onPressed: _addAlarm,
          ),
        ],
      ),
      body: _alarms.isEmpty
          ? Center(child: Text('没有闹钟，点击 + 添加'))
          : ListView.builder(
              itemCount: _alarms.length,
              itemBuilder: (context, index) {
                final alarm = _alarms[index];
                return ListTile(
                  title: Text(
                    alarm.time.format(context),
                    style: TextStyle(fontSize: 32),
                  ),
                  subtitle: Text(alarm.enabled ? '已开启' : '已关闭'),
                  trailing: Switch(
                    value: alarm.enabled,
                    onChanged: (value) {
                      setState(() {
                        alarm.enabled = value;
                      });
                    },
                  ),
                );
              },
            ),
    );
  }
}

class Alarm {
  final TimeOfDay time;
  bool enabled;

  Alarm({required this.time, this.enabled = true});
}
```

### 4.5 时间格式化

```dart
String formatTimeOfDay(TimeOfDay time, {bool use24Hour = false}) {
  final hour = time.hour.toString().padLeft(2, '0');
  final minute = time.minute.toString().padLeft(2, '0');
  
  if (use24Hour) {
    return '$hour:$minute';
  }
  
  final period = time.hour >= 12 ? '下午' : '上午';
  final displayHour = time.hour > 12 ? time.hour - 12 : (time.hour == 0 ? 12 : time.hour);
  return '$period ${displayHour.toString().padLeft(2, '0')}:$minute';
}

// 使用
formatTimeOfDay(TimeOfDay(hour: 15, minute: 30));  // 下午 03:30
formatTimeOfDay(TimeOfDay(hour: 15, minute: 30), use24Hour: true);  // 15:30
```

## 5. 常见问题与解决方案

### 问题1：时间格式问题

```dart
// 使用 format 方法自动适配设备设置
final formatted = time.format(context);

// 或强制使用 24 小时制
String format24Hour(TimeOfDay time) {
  return '${time.hour.toString().padLeft(2, '0')}:${time.minute.toString().padLeft(2, '0')}';
}
```

### 问题2：时间比较

```dart
int compareTime(TimeOfDay a, TimeOfDay b) {
  final aMinutes = a.hour * 60 + a.minute;
  final bMinutes = b.hour * 60 + b.minute;
  return aMinutes.compareTo(bMinutes);
}

// 判断时间范围
bool isTimeInRange(TimeOfDay time, TimeOfDay start, TimeOfDay end) {
  final timeMinutes = time.hour * 60 + time.minute;
  final startMinutes = start.hour * 60 + start.minute;
  final endMinutes = end.hour * 60 + end.minute;
  return timeMinutes >= startMinutes && timeMinutes <= endMinutes;
}
```

### 问题3：时间计算

```dart
TimeOfDay addMinutes(TimeOfDay time, int minutes) {
  int totalMinutes = time.hour * 60 + time.minute + minutes;
  totalMinutes = totalMinutes % (24 * 60);  // 处理跨天
  return TimeOfDay(
    hour: totalMinutes ~/ 60,
    minute: totalMinutes % 60,
  );
}

// 使用
addMinutes(TimeOfDay(hour: 23, minute: 30), 60);  // 00:30
```

### 问题4：禁用某些时间

```dart
// TimePicker 不支持直接禁用时间
// 可以在选择后验证并提示
Future<void> _selectTimeWithValidation() async {
  final time = await showTimePicker(
    context: context,
    initialTime: TimeOfDay.now(),
  );
  
  if (time != null) {
    // 验证时间
    if (time.hour < 9 || time.hour > 18) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('请选择工作时间（9:00-18:00）')),
      );
      return;
    }
    
    setState(() {
      _selectedTime = time;
    });
  }
}
```

## 6. TimePicker vs DatePicker

| 特性 | TimePicker | DatePicker |
|------|------------|------------|
| 选择内容 | 时间 | 日期 |
| 显示方式 | 钟表/输入 | 日历/输入 |
| 输入模式 | dial/input | calendar/input |
| 返回类型 | TimeOfDay | DateTime |

## 总结

TimePicker 是 Flutter 中选择时间的核心组件：

**核心要点**：
1. 使用 showTimePicker 显示时间选择器
2. 返回 TimeOfDay 类型
3. 支持 12/24 小时制
4. Material 3 新设计风格

**常用属性**：
- `initialTime` - 初始时间
- `initialEntryMode` - 输入模式
- `cancelText/confirmText` - 按钮文字

**最佳实践**：
1. 提供清晰的帮助文字
2. 考虑设备的时间格式设置
3. 封装时间选择组件
4. 处理时间验证和比较

---

*最后更新: 2026-02-19*

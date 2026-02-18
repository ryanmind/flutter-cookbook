# Flutter Slider 组件用法详解

## 简介

Slider 是滑动选择器组件，用于在连续或离散值范围内进行选择。Flutter 提供了 Slider（滑动条）和 RangeSlider（范围滑动条）两种类型。

## 1. Slider 滑动条

### 1.1 基本概念

Slider 的核心特点：
- 连续或离散值选择
- 支持自定义范围
- 支持分区显示
- Material 3 新样式

### 1.2 基本语法

```dart
Slider(
  value: 50,
  min: 0,
  max: 100,
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `value` | `double` | - | 当前值（必需） |
| `onChanged` | `ValueChanged<double>?` | - | 值变化回调 |
| `onChangeStart` | `ValueChanged<double>?` | - | 开始滑动回调 |
| `onChangeEnd` | `ValueChanged<double>?` | - | 结束滑动回调 |
| `min` | `double` | `0.0` | 最小值 |
| `max` | `double` | `1.0` | 最大值 |
| `divisions` | `int?` | - | 分区数 |
| `label` | `String?` | - | 标签文字 |
| `activeColor` | `Color?` | - | 激活颜色 |
| `inactiveColor` | `Color?` | - | 非激活颜色 |
| `thumbColor` | `Color?` | - | 滑块颜色 |
| `autofocus` | `bool` | `false` | 自动聚焦 |
| `secondaryTrackValue` | `double?` | - | 次级轨道值 |

### 1.4 基础示例

#### 简单滑动条

```dart
class SimpleSlider extends StatefulWidget {
  @override
  _SimpleSliderState createState() => _SimpleSliderState();
}

class _SimpleSliderState extends State<SimpleSlider> {
  double _value = 50;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Slider(
          value: _value,
          min: 0,
          max: 100,
          onChanged: (value) {
            setState(() {
              _value = value;
            });
          },
        ),
        Text('当前值: ${_value.toStringAsFixed(1)}'),
      ],
    );
  }
}
```

#### 带标签的滑动条

```dart
Slider(
  value: _value,
  min: 0,
  max: 100,
  divisions: 10,
  label: _value.round().toString(),
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)
```

#### 分区滑动条

```dart
Slider(
  value: _value,
  min: 0,
  max: 100,
  divisions: 5,  // 分成 5 段
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)
```

#### 自定义颜色

```dart
Slider(
  value: _value,
  min: 0,
  max: 100,
  activeColor: Colors.blue,
  inactiveColor: Colors.blue[100],
  thumbColor: Colors.white,
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)
```

### 1.5 常见使用场景

#### 音量控制

```dart
class VolumeControl extends StatefulWidget {
  @override
  _VolumeControlState createState() => _VolumeControlState();
}

class _VolumeControlState extends State<VolumeControl> {
  double _volume = 50;

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Icon(Icons.volume_down),
        Expanded(
          child: Slider(
            value: _volume,
            min: 0,
            max: 100,
            onChanged: (value) {
              setState(() {
                _volume = value;
              });
            },
          ),
        ),
        Icon(Icons.volume_up),
      ],
    );
  }
}
```

#### 亮度调节

```dart
class BrightnessControl extends StatefulWidget {
  @override
  _BrightnessControlState createState() => _BrightnessControlState();
}

class _BrightnessControlState extends State<BrightnessControl> {
  double _brightness = 70;

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(16),
      child: Column(
        children: [
          Row(
            children: [
              Icon(Icons.brightness_low),
              Expanded(
                child: Slider(
                  value: _brightness,
                  min: 0,
                  max: 100,
                  onChanged: (value) {
                    setState(() {
                      _brightness = value;
                    });
                  },
                ),
              ),
              Icon(Icons.brightness_high),
            ],
          ),
          Text('亮度: ${_brightness.round()}%'),
        ],
      ),
    );
  }
}
```

#### 价格筛选

```dart
class PriceSlider extends StatefulWidget {
  @override
  _PriceSliderState createState() => _PriceSliderState();
}

class _PriceSliderState extends State<PriceSlider> {
  double _price = 500;
  final double _minPrice = 0;
  final double _maxPrice = 1000;

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Text('¥$_minPrice'),
            Text('¥$_maxPrice'),
          ],
        ),
        Slider(
          value: _price,
          min: _minPrice,
          max: _maxPrice,
          divisions: 20,
          label: '¥${_price.round()}',
          onChanged: (value) {
            setState(() {
              _price = value;
            });
          },
        ),
        Text('选中价格: ¥${_price.round()}'),
      ],
    );
  }
}
```

## 2. RangeSlider 范围滑动条

### 2.1 基本概念

RangeSlider 的核心特点：
- 双滑块选择范围
- 支持最小/最大值
- 支持分区显示
- 常用于价格/时间范围筛选

### 2.2 基本语法

```dart
RangeSlider(
  values: RangeValues(20, 80),
  min: 0,
  max: 100,
  onChanged: (values) {
    setState(() {
      _rangeValues = values;
    });
  },
)
```

### 2.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `values` | `RangeValues` | 当前范围值（必需） |
| `onChanged` | `ValueChanged<RangeValues>?` | 值变化回调 |
| `onChangeStart` | `ValueChanged<RangeValues>?` | 开始滑动回调 |
| `onChangeEnd` | `ValueChanged<RangeValues>?` | 结束滑动回调 |
| `min` | `double` | 最小值 |
| `max` | `double` | 最大值 |
| `divisions` | `int?` | 分区数 |
| `labels` | `RangeLabels?` | 标签文字 |
| `activeColor` | `Color?` | 激活颜色 |
| `inactiveColor` | `Color?` | 非激活颜色 |

### 2.4 基础示例

#### 简单范围滑动条

```dart
class SimpleRangeSlider extends StatefulWidget {
  @override
  _SimpleRangeSliderState createState() => _SimpleRangeSliderState();
}

class _SimpleRangeSliderState extends State<SimpleRangeSlider> {
  RangeValues _values = RangeValues(20, 80);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        RangeSlider(
          values: _values,
          min: 0,
          max: 100,
          onChanged: (values) {
            setState(() {
              _values = values;
            });
          },
        ),
        Text('范围: ${_values.start.round()} - ${_values.end.round()}'),
      ],
    );
  }
}
```

#### 价格范围筛选

```dart
class PriceRangeSlider extends StatefulWidget {
  final double minPrice;
  final double maxPrice;
  final ValueChanged<RangeValues>? onChanged;

  const PriceRangeSlider({
    this.minPrice = 0,
    this.maxPrice = 10000,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _PriceRangeSliderState createState() => _PriceRangeSliderState();
}

class _PriceRangeSliderState extends State<PriceRangeSlider> {
  late RangeValues _values;

  @override
  void initState() {
    super.initState();
    _values = RangeValues(widget.minPrice, widget.maxPrice);
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Text('¥${widget.minPrice.toInt()}'),
            Text('¥${widget.maxPrice.toInt()}'),
          ],
        ),
        RangeSlider(
          values: _values,
          min: widget.minPrice,
          max: widget.maxPrice,
          divisions: 50,
          labels: RangeLabels(
            '¥${_values.start.round()}',
            '¥${_values.end.round()}',
          ),
          onChanged: (values) {
            setState(() {
              _values = values;
            });
            widget.onChanged?.call(values);
          },
        ),
        Text(
          '选中: ¥${_values.start.round()} - ¥${_values.end.round()}',
          style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
        ),
      ],
    );
  }
}
```

#### 年龄范围选择

```dart
class AgeRangeSlider extends StatefulWidget {
  @override
  _AgeRangeSliderState createState() => _AgeRangeSliderState();
}

class _AgeRangeSliderState extends State<AgeRangeSlider> {
  RangeValues _values = RangeValues(18, 60);

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('年龄范围', style: TextStyle(fontWeight: FontWeight.bold)),
        SizedBox(height: 8),
        RangeSlider(
          values: _values,
          min: 0,
          max: 100,
          divisions: 100,
          labels: RangeLabels(
            '${_values.start.round()}岁',
            '${_values.end.round()}岁',
          ),
          onChanged: (values) {
            setState(() {
              _values = values;
            });
          },
        ),
        Text('选择范围: ${_values.start.round()} - ${_values.end.round()} 岁'),
      ],
    );
  }
}
```

## 3. 高级用法

### 3.1 自定义滑块样式

```dart
SliderTheme(
  data: SliderTheme.of(context).copyWith(
    activeTrackColor: Colors.blue[700],
    inactiveTrackColor: Colors.blue[100],
    thumbShape: RoundSliderThumbShape(enabledThumbRadius: 12),
    thumbColor: Colors.blue,
    overlayColor: Colors.blue.withOpacity(0.2),
    overlayShape: RoundSliderOverlayShape(overlayRadius: 24),
    tickMarkShape: RoundSliderTickMarkShape(),
    activeTickMarkColor: Colors.blue[700],
    inactiveTickMarkColor: Colors.blue[100],
    valueIndicatorShape: PaddleSliderValueIndicatorShape(),
    valueIndicatorColor: Colors.blue,
    valueIndicatorTextStyle: TextStyle(color: Colors.white),
  ),
  child: Slider(
    value: _value,
    min: 0,
    max: 100,
    divisions: 10,
    label: _value.round().toString(),
    onChanged: (value) {
      setState(() {
        _value = value;
      });
    },
  ),
)
```

### 3.2 无动画滑块

```dart
Slider(
  value: _value,
  min: 0,
  max: 100,
  onChanged: null,  // 禁用滑动
)
```

### 3.3 次级轨道

```dart
Slider(
  value: _value,
  min: 0,
  max: 100,
  secondaryTrackValue: 70,  // 显示次级进度
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)
```

### 3.4 垂直滑动条

```dart
// Flutter 没有内置垂直滑动条，可以自定义
class VerticalSlider extends StatelessWidget {
  final double value;
  final double min;
  final double max;
  final ValueChanged<double>? onChanged;

  const VerticalSlider({
    required this.value,
    this.min = 0,
    this.max = 1,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return RotatedBox(
      quarterTurns: 3,  // 旋转 90 度
      child: Slider(
        value: value,
        min: min,
        max: max,
        onChanged: onChanged,
      ),
    );
  }
}
```

### 3.5 步进滑块

```dart
class StepSlider extends StatefulWidget {
  final List<String> steps;
  final int initialIndex;
  final ValueChanged<int>? onChanged;

  const StepSlider({
    required this.steps,
    this.initialIndex = 0,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _StepSliderState createState() => _StepSliderState();
}

class _StepSliderState extends State<StepSlider> {
  late int _index;

  @override
  void initState() {
    super.initState();
    _index = widget.initialIndex;
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: widget.steps
              .map((step) => Text(step, style: TextStyle(fontSize: 12)))
              .toList(),
        ),
        Slider(
          value: _index.toDouble(),
          min: 0,
          max: (widget.steps.length - 1).toDouble(),
          divisions: widget.steps.length - 1,
          label: widget.steps[_index],
          onChanged: (value) {
            setState(() {
              _index = value.round();
            });
            widget.onChanged?.call(_index);
          },
        ),
      ],
    );
  }
}

// 使用
StepSlider(
  steps: ['XS', 'S', 'M', 'L', 'XL'],
  initialIndex: 2,
  onChanged: (index) {
    print('选中: $index');
  },
)
```

### 3.6 播放进度条

```dart
class PlaybackSlider extends StatefulWidget {
  final Duration totalDuration;
  final ValueChanged<Duration>? onSeek;

  const PlaybackSlider({
    required this.totalDuration,
    this.onSeek,
    Key? key,
  }) : super(key: key);

  @override
  _PlaybackSliderState createState() => _PlaybackSliderState();
}

class _PlaybackSliderState extends State<PlaybackSlider> {
  Duration _position = Duration.zero;
  bool _isDragging = false;

  String _formatDuration(Duration duration) {
    final minutes = duration.inMinutes;
    final seconds = duration.inSeconds.remainder(60);
    return '$minutes:${seconds.toString().padLeft(2, '0')}';
  }

  @override
  Widget build(BuildContext context) {
    final totalMs = widget.totalDuration.inMilliseconds.toDouble();
    final positionMs = _position.inMilliseconds.toDouble();

    return Column(
      children: [
        Slider(
          value: positionMs.clamp(0, totalMs),
          min: 0,
          max: totalMs,
          onChangeStart: (_) => _isDragging = true,
          onChangeEnd: (value) {
            _isDragging = false;
            widget.onSeek?.call(Duration(milliseconds: value.round()));
          },
          onChanged: (value) {
            setState(() {
              _position = Duration(milliseconds: value.round());
            });
          },
        ),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Text(_formatDuration(_position)),
            Text(_formatDuration(widget.totalDuration)),
          ],
        ),
      ],
    );
  }
}
```

## 4. 自定义滑块组件

### 4.1 带数值显示的滑块

```dart
class ValueSlider extends StatelessWidget {
  final double value;
  final double min;
  final double max;
  final int? divisions;
  final String prefix;
  final String suffix;
  final ValueChanged<double>? onChanged;

  const ValueSlider({
    required this.value,
    this.min = 0,
    this.max = 100,
    this.divisions,
    this.prefix = '',
    this.suffix = '',
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Expanded(
          child: Slider(
            value: value,
            min: min,
            max: max,
            divisions: divisions,
            onChanged: onChanged,
          ),
        ),
        SizedBox(
          width: 60,
          child: Text(
            '$prefix${value.toStringAsFixed(1)}$suffix',
            textAlign: TextAlign.right,
            style: TextStyle(fontWeight: FontWeight.bold),
          ),
        ),
      ],
    );
  }
}

// 使用
ValueSlider(
  value: _temperature,
  min: 0,
  max: 100,
  suffix: '°C',
  onChanged: (value) {
    setState(() => _temperature = value);
  },
)
```

### 4.2 带图标的滑块

```dart
class IconSlider extends StatelessWidget {
  final double value;
  final double min;
  final double max;
  final IconData minIcon;
  final IconData maxIcon;
  final ValueChanged<double>? onChanged;

  const IconSlider({
    required this.value,
    this.min = 0,
    this.max = 100,
    required this.minIcon,
    required this.maxIcon,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Icon(minIcon, color: Colors.grey),
        Expanded(
          child: Slider(
            value: value,
            min: min,
            max: max,
            onChanged: onChanged,
          ),
        ),
        Icon(maxIcon, color: Colors.grey),
      ],
    );
  }
}

// 使用
IconSlider(
  value: _volume,
  minIcon: Icons.volume_down,
  maxIcon: Icons.volume_up,
  onChanged: (value) {
    setState(() => _volume = value);
  },
)
```

## 5. 常见问题与解决方案

### 问题1：滑块值超出范围

```dart
// 使用 clamp 确保值在范围内
Slider(
  value: value.clamp(min, max),
  min: min,
  max: max,
  onChanged: onChanged,
)
```

### 问题2：滑块颜色不生效

```dart
// 使用 SliderTheme 统一设置
SliderTheme(
  data: SliderThemeData(
    activeTrackColor: Colors.blue,
    thumbColor: Colors.blue,
  ),
  child: Slider(...),
)
```

### 问题3：RangeSlider 滑块重叠

```dart
// 设置最小间隔
RangeSlider(
  values: _values,
  min: 0,
  max: 100,
  onChanged: (values) {
    // 确保最小间隔
    if (values.end - values.start >= 5) {
      setState(() => _values = values);
    }
  },
)
```

### 问题4：滑动条太细

```dart
SliderTheme(
  data: SliderThemeData(
    trackHeight: 8,  // 设置轨道高度
  ),
  child: Slider(...),
)
```

### 问题5：滑块禁用状态

```dart
// onChanged 为 null 时禁用
Slider(
  value: value,
  onChanged: enabled ? onChanged : null,
)
```

## 6. 最佳实践

### 6.1 统一滑块样式

```dart
class AppSliderTheme {
  static SliderThemeData get standard => SliderThemeData(
    activeTrackColor: Colors.blue,
    inactiveTrackColor: Colors.blue[100],
    thumbColor: Colors.white,
    overlayColor: Colors.blue.withOpacity(0.2),
    trackHeight: 4,
    thumbShape: RoundSliderThumbShape(enabledThumbRadius: 8),
  );
}

SliderTheme(
  data: AppSliderTheme.standard,
  child: Slider(...),
)
```

### 6.2 合理设置分区

```dart
// 分区数量适中，避免过于密集
Slider(
  divisions: 10,  // 推荐 5-20
  // ...
)
```

### 6.3 提供即时反馈

```dart
// 使用 label 显示当前值
Slider(
  label: value.round().toString(),
  // ...
)
```

## 总结

Slider 是 Flutter 中常用的值选择组件：

**Slider**：
- 单值滑动选择
- 支持连续/离散模式
- 常用于音量、亮度、进度调节

**RangeSlider**：
- 双值范围选择
- 支持最小/最大值
- 常用于价格、时间范围筛选

**常用属性**：
- `value/values` - 当前值
- `min/max` - 范围
- `divisions` - 分区数
- `label/labels` - 标签显示
- `activeColor/inactiveColor` - 颜色

**最佳实践**：
1. 使用 SliderTheme 统一样式
2. 合理设置分区数量
3. 提供清晰的标签反馈
4. 处理边界值情况

---

*最后更新: 2026-02-18*

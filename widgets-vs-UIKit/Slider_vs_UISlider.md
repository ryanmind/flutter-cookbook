# Slider vs UISlider 对比学习

本文档记录 Flutter Slider 组件与 UIKit UISlider 的对比学习要点。

---

## 1. Slider 核心概念

### Slider 是什么

Slider 是 Flutter 中的滑块组件，用于在一个范围内选择值，类似于 UIKit 的 UISlider。

- 支持连续和离散值
- 支持自定义范围
- 支持自定义颜色和样式
- 支持显示标签

### Slider vs UISlider 概念对比

| Flutter Slider | UIKit UISlider | 说明 |
|----------------|----------------|------|
| `value` | `value` | 当前值 |
| `min` | `minimumValue` | 最小值 |
| `max` | `maximumValue` | 最大值 |
| `onChanged` | `addTarget` | 值变化回调 |
| `onChangeStart` | `UIControlEventTouchDown` | 开始拖动回调 |
| `onChangeEnd` | `UIControlEventTouchUpInside` | 结束拖动回调 |
| `activeColor` | `minimumTrackTintColor` | 已选择部分颜色 |
| `inactiveColor` | `maximumTrackTintColor` | 未选择部分颜色 |
| `thumbColor` | `thumbTintColor` | 滑块颜色 |
| `divisions` | 无直接对应 | 离散值分格 |
| `label` | 无直接对应 | 值标签 |

---

## 2. 源码分析

### Slider 类定义

```dart
class Slider extends StatefulWidget {
  const Slider({
    super.key,
    required this.value,           // 当前值
    required this.onChanged,       // 值变化回调
    this.onChangeStart,            // 开始拖动回调
    this.onChangeEnd,              // 结束拖动回调
    this.min = 0.0,                // 最小值
    this.max = 1.0,                // 最大值
    this.divisions,                // 分格数（离散值）
    this.label,                    // 标签文本
    this.activeColor,              // 已选部分颜色
    this.inactiveColor,            // 未选部分颜色
    this.thumbColor,               // 滑块颜色
    this.overlayColor,             // 按下时涟漪颜色
    this.mouseCursor,              // 鼠标样式
    this.semanticFormatterCallback, // 语义格式化
    this.focusNode,                // 焦点节点
    this.autofocus = false,        // 自动聚焦
    this.allowedInteraction,       // 允许的交互方式
  });
}

// Material 3 变体
Slider.adaptive()  // 平台自适应
```

### RangeSlider

```dart
// 范围滑块（选择一个区间）
class RangeSlider extends StatefulWidget {
  const RangeSlider({
    super.key,
    required this.values,          // RangeValues
    required this.onChanged,
    this.onChangeStart,
    this.onChangeEnd,
    this.min = 0.0,
    this.max = 1.0,
    this.divisions,
    this.labels,                   // RangeLabels
    this.activeColor,
    this.inactiveColor,
    this.overlayColor,
    this.mouseCursor,
    this.semanticFormatterCallback,
  });
}
```

---

## 3. 与 UISlider 详细对比

### 3.1 基础滑块

```objc
// Objective-C - UISlider
UISlider *slider = [[UISlider alloc] initWithFrame:CGRectMake(20, 100, 280, 30)];
slider.minimumValue = 0;
slider.maximumValue = 100;
slider.value = 50;
[slider addTarget:self action:@selector(sliderChanged:) forControlEvents:UIControlEventValueChanged];
[self.view addSubview:slider];

- (void)sliderChanged:(UISlider *)sender {
    NSLog(@"Value: %.1f", sender.value);
}
```

```dart
// Flutter - Slider
double _value = 50;

Slider(
  value: _value,
  min: 0,
  max: 100,
  onChanged: (value) {
    setState(() {
      _value = value;
    });
    print('Value: $value');
  },
)
```

### 3.2 自定义颜色

```objc
// Objective-C
slider.minimumTrackTintColor = [UIColor blueColor];   // 已选部分
slider.maximumTrackTintColor = [UIColor grayColor];   // 未选部分
slider.thumbTintColor = [UIColor whiteColor];         // 滑块颜色
```

```dart
// Flutter
Slider(
  value: _value,
  min: 0,
  max: 100,
  onChanged: (value) {},
  activeColor: Colors.blue,      // 已选部分
  inactiveColor: Colors.grey,    // 未选部分
  thumbColor: Colors.white,      // 滑块颜色
)
```

### 3.3 离散值（分格）

```objc
// Objective-C - UISlider 不支持离散值
// 需要在 valueChanged 回调中手动取整
- (void)sliderChanged:(UISlider *)sender {
    sender.value = roundf(sender.value);  // 取整
}
```

```dart
// Flutter - divisions 设置分格
Slider(
  value: _value,
  min: 0,
  max: 100,
  divisions: 10,  // 分成 10 格，每格 10
  onChanged: (value) {
    setState(() => _value = value);
  },
)

// 滑块会自动吸附到最近的分格点
```

### 3.4 显示值标签

```objc
// Objective-C - 需要自定义 UILabel 显示
```

```dart
// Flutter - label 属性
Slider(
  value: _value,
  min: 0,
  max: 100,
  divisions: 10,
  label: _value.round().toString(),  // 拖动时显示
  onChanged: (value) {
    setState(() => _value = value);
  },
)
```

### 3.5 拖动回调

```objc
// Objective-C
[slider addTarget:self action:@selector(sliderTouchDown:) forControlEvents:UIControlEventTouchDown];
[slider addTarget:self action:@selector(sliderTouchUp:) forControlEvents:UIControlEventTouchUpInside | UIControlEventTouchUpOutside];

- (void)sliderTouchDown:(UISlider *)sender {
    NSLog(@"Started dragging");
}

- (void)sliderTouchUp:(UISlider *)sender {
    NSLog(@"Ended dragging at: %.1f", sender.value);
}
```

```dart
// Flutter
Slider(
  value: _value,
  min: 0,
  max: 100,
  onChanged: (value) {
    setState(() => _value = value);
  },
  onChangeStart: (value) {
    print('Started dragging at: $value');
  },
  onChangeEnd: (value) {
    print('Ended dragging at: $value');
  },
)
```

### 3.6 范围滑块

```objc
// Objective-C - UISlider 不支持范围选择
// 需要自定义实现或使用第三方库
```

```dart
// Flutter - RangeSlider
RangeValues _values = RangeValues(20, 80);

RangeSlider(
  values: _values,
  min: 0,
  max: 100,
  divisions: 10,
  labels: RangeLabels(
    _values.start.round().toString(),
    _values.end.round().toString(),
  ),
  onChanged: (values) {
    setState(() => _values = values);
  },
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 音量控制页面
@interface VolumeViewController : UIViewController
@property (nonatomic, strong) UISlider *volumeSlider;
@property (nonatomic, strong) UILabel *volumeLabel;
@property (nonatomic, strong) UISlider *brightnessSlider;
@property (nonatomic, strong) UILabel *brightnessLabel;
@end

@implementation VolumeViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.view.backgroundColor = [UIColor systemBackgroundColor];
    
    // 音量标题
    UILabel *volumeTitle = [[UILabel alloc] initWithFrame:CGRectMake(20, 100, 100, 30)];
    volumeTitle.text = @"Volume";
    [self.view addSubview:volumeTitle];
    
    // 音量值
    self.volumeLabel = [[UILabel alloc] initWithFrame:CGRectMake(260, 100, 40, 30)];
    self.volumeLabel.text = @"50%";
    self.volumeLabel.textAlignment = NSTextAlignmentRight;
    [self.view addSubview:self.volumeLabel];
    
    // 音量滑块
    self.volumeSlider = [[UISlider alloc] initWithFrame:CGRectMake(20, 140, 280, 30)];
    self.volumeSlider.minimumValue = 0;
    self.volumeSlider.maximumValue = 100;
    self.volumeSlider.value = 50;
    self.volumeSlider.minimumTrackTintColor = [UIColor systemBlueColor];
    [self.volumeSlider addTarget:self action:@selector(volumeChanged:) forControlEvents:UIControlEventValueChanged];
    [self.view addSubview:self.volumeSlider];
    
    // 亮度标题
    UILabel *brightnessTitle = [[UILabel alloc] initWithFrame:CGRectMake(20, 200, 100, 30)];
    brightnessTitle.text = @"Brightness";
    [self.view addSubview:brightnessTitle];
    
    // 亮度值
    self.brightnessLabel = [[UILabel alloc] initWithFrame:CGRectMake(260, 200, 40, 30)];
    self.brightnessLabel.text = @"75%";
    self.brightnessLabel.textAlignment = NSTextAlignmentRight;
    [self.view addSubview:self.brightnessLabel];
    
    // 亮度滑块
    self.brightnessSlider = [[UISlider alloc] initWithFrame:CGRectMake(20, 240, 280, 30)];
    self.brightnessSlider.minimumValue = 0;
    self.brightnessSlider.maximumValue = 100;
    self.brightnessSlider.value = 75;
    self.brightnessSlider.minimumTrackTintColor = [UIColor systemYellowColor];
    [self.brightnessSlider addTarget:self action:@selector(brightnessChanged:) forControlEvents:UIControlEventValueChanged];
    [self.view addSubview:self.brightnessSlider];
}

- (void)volumeChanged:(UISlider *)sender {
    self.volumeLabel.text = [NSString stringWithFormat:@"%.0f%%", sender.value];
}

- (void)brightnessChanged:(UISlider *)sender {
    self.brightnessLabel.text = [NSString stringWithFormat:@"%.0f%%", sender.value];
}
@end
```

### Flutter 实现

```dart
// 音量控制页面 - 更简洁
class VolumeControlPage extends StatefulWidget {
  @override
  _VolumeControlPageState createState() => _VolumeControlPageState();
}

class _VolumeControlPageState extends State<VolumeControlPage> {
  double _volume = 50;
  double _brightness = 75;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Controls')),
      body: Padding(
        padding: EdgeInsets.all(20),
        child: Column(
          children: [
            // 音量
            ListTile(
              title: Text('Volume'),
              trailing: Text('${_volume.round()}%'),
            ),
            Slider(
              value: _volume,
              min: 0,
              max: 100,
              divisions: 100,
              activeColor: Colors.blue,
              onChanged: (value) {
                setState(() => _volume = value);
              },
            ),
            SizedBox(height: 20),
            // 亮度
            ListTile(
              title: Text('Brightness'),
              trailing: Text('${_brightness.round()}%'),
            ),
            Slider(
              value: _brightness,
              min: 0,
              max: 100,
              divisions: 100,
              activeColor: Colors.amber,
              onChanged: (value) {
                setState(() => _brightness = value);
              },
            ),
          ],
        ),
      ),
    );
  }
}
```

---

## 5. Slider vs RangeSlider

| 组件 | 特点 | 使用场景 |
|------|------|---------|
| **Slider** | 单值选择 | 音量、亮度等 |
| **RangeSlider** | 范围选择 | 价格范围、年龄范围等 |

```dart
// Slider - 单值
Slider(
  value: _value,
  min: 0,
  max: 100,
  onChanged: (value) {},
)

// RangeSlider - 范围
RangeSlider(
  values: _rangeValues,
  min: 0,
  max: 1000,
  divisions: 100,
  labels: RangeLabels(
    '\$${_rangeValues.start.round()}',
    '\$${_rangeValues.end.round()}',
  ),
  onChanged: (values) {},
)
```

---

## 6. 常见陷阱

### 陷阱1：忘记 setState

```dart
// ❌ 错误：值不会更新
Slider(
  value: _value,
  onChanged: (value) {
    _value = value;  // 不会触发 UI 更新
  },
)

// ✅ 正确：使用 setState
Slider(
  value: _value,
  onChanged: (value) {
    setState(() => _value = value);
  },
)
```

### 陷阱2：value 超出范围

```dart
// ❌ 错误：value 超出 min-max 范围
Slider(
  value: 150,  // 超出 max=100
  min: 0,
  max: 100,
  onChanged: (value) {},
)  // 报错！

// ✅ 正确：确保 value 在范围内
double _value = 50.clamp(0, 100);  // 确保在范围内
Slider(
  value: _value,
  min: 0,
  max: 100,
  onChanged: (value) {
    setState(() => _value = value.clamp(0, 100));
  },
)
```

### 陷阱3：禁用滑块

```dart
// onChanged 为 null 时禁用
Slider(
  value: _value,
  min: 0,
  max: 100,
  onChanged: null,  // 禁用
)
```

### 陷阱4：divisions 与 label 配合

```dart
// 有 divisions 时，label 通常显示离散值
Slider(
  value: _value,
  min: 0,
  max: 100,
  divisions: 10,  // 0, 10, 20, ..., 100
  label: _value.round().toString(),
  onChanged: (value) {
    setState(() => _value = value);
  },
)
```

---

## 7. 学习技巧

### 技巧1：显示当前值

```dart
Column(
  children: [
    Row(
      mainAxisAlignment: MainAxisAlignment.spaceBetween,
      children: [
        Text('Volume'),
        Text('${_volume.round()}%'),
      ],
    ),
    Slider(
      value: _volume,
      min: 0,
      max: 100,
      onChanged: (value) {
        setState(() => _volume = value);
      },
    ),
  ],
)
```

### 技巧2：自定义语义

```dart
Slider(
  value: _value,
  min: 0,
  max: 100,
  semanticFormatterCallback: (value) {
    return '${value.round()} percent';
  },
  onChanged: (value) {},
)
```

### 技巧3：延迟更新（性能优化）

```dart
// 使用 onChangeEnd 延迟执行昂贵操作
Slider(
  value: _value,
  onChanged: (value) {
    setState(() => _value = value);  // 只更新 UI
  },
  onChangeEnd: (value) {
    // 拖动结束后执行昂贵操作
    _performExpensiveOperation(value);
  },
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UISlider                    → Slider / RangeSlider
value                       → value
minimumValue                → min
maximumValue                → max
UIControlEventValueChanged  → onChanged
UIControlEventTouchDown     → onChangeStart
UIControlEventTouchUpInside → onChangeEnd
minimumTrackTintColor       → activeColor
maximumTrackTintColor       → inactiveColor
thumbTintColor              → thumbColor
无                          → divisions（离散值）
无                          → label（值标签）
无                          → RangeSlider（范围滑块）
```

### 关键差异

| 方面 | UIKit UISlider | Flutter Slider |
|------|----------------|----------------|
| **离散值** | 需手动实现 | divisions |
| **值标签** | 需自定义 | label |
| **范围选择** | 不支持 | RangeSlider |
| **颜色自定义** | 有限 | 丰富 |
| **状态管理** | 属性设置 | 回调 + setState |

### 最佳实践

1. **需要离散值用 divisions** - 自动吸附
2. **显示标签用 label** - Material 3 内置
3. **范围选择用 RangeSlider** - 内置支持
4. **昂贵操作放 onChangeEnd** - 性能优化

---

*最后更新: 2026-02-24*

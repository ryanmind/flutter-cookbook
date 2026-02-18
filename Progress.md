# Flutter Progress 组件用法详解

## 简介

Progress 组件用于向用户展示操作进度。Flutter 提供了 LinearProgressIndicator（线性进度条）和 CircularProgressIndicator（圆形进度条）两种主要类型。

## 1. CircularProgressIndicator 圆形进度条

### 1.1 基本概念

CircularProgressIndicator 的核心特点：
- 圆形旋转动画
- 支持确定和不确定两种模式
- 可自定义颜色和大小
- 常用于加载状态

### 1.2 基本语法

```dart
// 不确定进度
CircularProgressIndicator()

// 确定进度
CircularProgressIndicator(value: 0.5)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `value` | `double?` | - | 进度值（0-1），null 为不确定模式 |
| `backgroundColor` | `Color?` | - | 背景轨道颜色 |
| `color` | `Color?` | - | 进度颜色 |
| `strokeWidth` | `double` | `4.0` | 线条宽度 |
| `strokeAlign` | `double` | `0` | 线条对齐方式 |
| `semanticsLabel` | `String?` | - | 语义标签 |
| `semanticsValue` | `String?` | - | 语义值 |
| `strokeCap` | `StrokeCap?` | - | 线条端点样式 |

### 1.4 基础示例

#### 不确定进度

```dart
// 默认大小
CircularProgressIndicator()

// 自定义颜色
CircularProgressIndicator(
  color: Colors.blue,
)

// 自定义大小
SizedBox(
  width: 24,
  height: 24,
  child: CircularProgressIndicator(strokeWidth: 2),
)
```

#### 确定进度

```dart
CircularProgressIndicator(
  value: 0.7,
  color: Colors.blue,
  backgroundColor: Colors.grey[200],
)
```

#### 自定义样式

```dart
CircularProgressIndicator(
  value: 0.6,
  strokeWidth: 8,
  strokeCap: StrokeCap.round,
  color: Colors.green,
  backgroundColor: Colors.green[100],
)
```

## 2. LinearProgressIndicator 线性进度条

### 2.1 基本概念

LinearProgressIndicator 的核心特点：
- 水平条形进度
- 支持确定和不确定两种模式
- 可自定义颜色和高度
- 常用于下载/上传进度

### 2.2 基本语法

```dart
// 不确定进度
LinearProgressIndicator()

// 确定进度
LinearProgressIndicator(value: 0.5)
```

### 2.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `value` | `double?` | - | 进度值（0-1），null 为不确定模式 |
| `backgroundColor` | `Color?` | - | 背景轨道颜色 |
| `color` | `Color?` | - | 进度颜色 |
| `minHeight` | `double?` | `4.0` | 最小高度 |
| `borderRadius` | `BorderRadiusGeometry?` | - | 圆角 |
| `semanticsLabel` | `String?` | - | 语义标签 |
| `semanticsValue` | `String?` | - | 语义值 |

### 2.4 基础示例

#### 不确定进度

```dart
LinearProgressIndicator()

// 自定义颜色
LinearProgressIndicator(
  color: Colors.blue,
  backgroundColor: Colors.blue[100],
)
```

#### 确定进度

```dart
LinearProgressIndicator(
  value: 0.8,
  color: Colors.green,
  backgroundColor: Colors.grey[200],
)
```

#### 自定义高度和圆角

```dart
LinearProgressIndicator(
  value: 0.5,
  minHeight: 8,
  borderRadius: BorderRadius.circular(4),
  color: Colors.blue,
  backgroundColor: Colors.grey[200],
)
```

## 3. 中级用法

### 3.1 带百分比的进度条

```dart
class PercentageProgress extends StatelessWidget {
  final double value;
  final double height;
  final Color color;

  const PercentageProgress({
    required this.value,
    this.height = 20,
    this.color = Colors.blue,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Stack(
          children: [
            LinearProgressIndicator(
              value: value,
              minHeight: height,
              backgroundColor: Colors.grey[200],
              color: color,
              borderRadius: BorderRadius.circular(4),
            ),
            Positioned.fill(
              child: Center(
                child: Text(
                  '${(value * 100).toInt()}%',
                  style: TextStyle(
                    fontSize: 12,
                    fontWeight: FontWeight.bold,
                    color: value > 0.5 ? Colors.white : Colors.black,
                  ),
                ),
              ),
            ),
          ],
        ),
      ],
    );
  }
}
```

### 3.2 渐变进度条

```dart
class GradientProgressIndicator extends StatelessWidget {
  final double value;

  const GradientProgressIndicator({required this.value, Key? key})
      : super(key: key);

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        return Stack(
          children: [
            Container(
              height: 8,
              decoration: BoxDecoration(
                color: Colors.grey[200],
                borderRadius: BorderRadius.circular(4),
              ),
            ),
            FractionallySizedBox(
              widthFactor: value,
              child: Container(
                height: 8,
                decoration: BoxDecoration(
                  gradient: LinearGradient(
                    colors: [Colors.blue, Colors.purple],
                  ),
                  borderRadius: BorderRadius.circular(4),
                ),
              ),
            ),
          ],
        );
      },
    );
  }
}
```

### 3.3 分段进度条

```dart
class SegmentedProgressIndicator extends StatelessWidget {
  final int total;
  final int completed;
  final double height;
  final Color completedColor;
  final Color remainingColor;

  const SegmentedProgressIndicator({
    required this.total,
    required this.completed,
    this.height = 8,
    this.completedColor = Colors.blue,
    this.remainingColor = Colors.grey,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: List.generate(total, (index) {
        final isCompleted = index < completed;
        return Expanded(
          child: Container(
            height: height,
            margin: EdgeInsets.symmetric(horizontal: 2),
            decoration: BoxDecoration(
              color: isCompleted ? completedColor : remainingColor,
              borderRadius: BorderRadius.circular(4),
            ),
          ),
        );
      }),
    );
  }
}

// 使用示例
SegmentedProgressIndicator(total: 5, completed: 3)
```

### 3.4 带动画的进度条

```dart
class AnimatedProgressIndicator extends StatefulWidget {
  final double targetValue;
  final Duration duration;

  const AnimatedProgressIndicator({
    required this.targetValue,
    this.duration = const Duration(milliseconds: 1000),
    Key? key,
  }) : super(key: key);

  @override
  _AnimatedProgressIndicatorState createState() =>
      _AnimatedProgressIndicatorState();
}

class _AnimatedProgressIndicatorState extends State<AnimatedProgressIndicator>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: widget.duration,
      vsync: this,
    );
    _animation = Tween<double>(
      begin: 0,
      end: widget.targetValue,
    ).animate(CurvedAnimation(
      parent: _controller,
      curve: Curves.easeInOut,
    ));
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _animation,
      builder: (context, child) {
        return LinearProgressIndicator(value: _animation.value);
      },
    );
  }
}
```

## 4. 高级用法

### 4.1 圆形进度按钮

```dart
class ProgressButton extends StatefulWidget {
  final String text;
  final Future<void> Function() onPressed;

  const ProgressButton({
    required this.text,
    required this.onPressed,
    Key? key,
  }) : super(key: key);

  @override
  _ProgressButtonState createState() => _ProgressButtonState();
}

class _ProgressButtonState extends State<ProgressButton> {
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
              child: CircularProgressIndicator(
                strokeWidth: 2,
                color: Colors.white,
              ),
            )
          : Text(widget.text),
    );
  }
}
```

### 4.2 下载进度组件

```dart
class DownloadProgress extends StatelessWidget {
  final double progress;
  final String fileName;
  final VoidCallback? onCancel;

  const DownloadProgress({
    required this.progress,
    required this.fileName,
    this.onCancel,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Padding(
        padding: EdgeInsets.all(16),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              children: [
                Icon(Icons.download, size: 24),
                SizedBox(width: 12),
                Expanded(
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text(
                        fileName,
                        style: TextStyle(fontWeight: FontWeight.bold),
                      ),
                      SizedBox(height: 4),
                      Text(
                        '${(progress * 100).toStringAsFixed(1)}%',
                        style: TextStyle(color: Colors.grey),
                      ),
                    ],
                  ),
                ),
                IconButton(
                  icon: Icon(Icons.close),
                  onPressed: onCancel,
                ),
              ],
            ),
            SizedBox(height: 12),
            LinearProgressIndicator(
              value: progress,
              minHeight: 6,
              borderRadius: BorderRadius.circular(3),
            ),
          ],
        ),
      ),
    );
  }
}
```

### 4.3 上传文件列表

```dart
class UploadList extends StatelessWidget {
  final List<UploadItem> items;

  const UploadList({required this.items, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: items.map((item) {
        return ListTile(
          leading: item.isComplete
              ? Icon(Icons.check_circle, color: Colors.green)
              : SizedBox(
                  width: 24,
                  height: 24,
                  child: CircularProgressIndicator(
                    value: item.progress,
                    strokeWidth: 2,
                  ),
                ),
          title: Text(item.fileName),
          subtitle: LinearProgressIndicator(
            value: item.progress,
          ),
          trailing: item.isComplete
              ? Text('完成')
              : Text('${(item.progress * 100).toInt()}%'),
        );
      }).toList(),
    );
  }
}

class UploadItem {
  final String fileName;
  final double progress;

  UploadItem({required this.fileName, required this.progress});

  bool get isComplete => progress >= 1.0;
}
```

### 4.4 自定义圆形进度条

```dart
class CustomCircularProgress extends StatelessWidget {
  final double value;
  final double size;
  final double strokeWidth;
  final Color progressColor;
  final Color backgroundColor;
  final Widget? center;

  const CustomCircularProgress({
    required this.value,
    this.size = 100,
    this.strokeWidth = 8,
    this.progressColor = Colors.blue,
    this.backgroundColor = Colors.grey,
    this.center,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SizedBox(
      width: size,
      height: size,
      child: Stack(
        alignment: Alignment.center,
        children: [
          SizedBox(
            width: size,
            height: size,
            child: CircularProgressIndicator(
              value: 1,
              strokeWidth: strokeWidth,
              color: backgroundColor,
            ),
          ),
          SizedBox(
            width: size,
            height: size,
            child: CircularProgressIndicator(
              value: value,
              strokeWidth: strokeWidth,
              color: progressColor,
              strokeCap: StrokeCap.round,
            ),
          ),
          if (center != null) center!,
        ],
      ),
    );
  }
}

// 使用示例
CustomCircularProgress(
  value: 0.75,
  size: 120,
  strokeWidth: 10,
  progressColor: Colors.green,
  backgroundColor: Colors.green[100]!,
  center: Text(
    '75%',
    style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
  ),
)
```

### 4.5 步骤进度指示器

```dart
class StepProgressIndicator extends StatelessWidget {
  final int totalSteps;
  final int currentStep;
  final Color completedColor;
  final Color currentColor;
  final Color remainingColor;

  const StepProgressIndicator({
    required this.totalSteps,
    required this.currentStep,
    this.completedColor = Colors.green,
    this.currentColor = Colors.blue,
    this.remainingColor = Colors.grey,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: List.generate(totalSteps, (index) {
        final isCompleted = index < currentStep;
        final isCurrent = index == currentStep;
        
        return Expanded(
          child: Row(
            children: [
              Container(
                width: 24,
                height: 24,
                decoration: BoxDecoration(
                  shape: BoxShape.circle,
                  color: isCompleted
                      ? completedColor
                      : isCurrent
                          ? currentColor
                          : remainingColor,
                ),
                child: Center(
                  child: isCompleted
                      ? Icon(Icons.check, color: Colors.white, size: 16)
                      : Text(
                          '${index + 1}',
                          style: TextStyle(
                            color: isCurrent ? Colors.white : Colors.black,
                            fontSize: 12,
                          ),
                        ),
                ),
              ),
              if (index < totalSteps - 1)
                Expanded(
                  child: Container(
                    height: 2,
                    color: isCompleted ? completedColor : remainingColor,
                  ),
                ),
            ],
          ),
        );
      }),
    );
  }
}

// 使用示例
StepProgressIndicator(totalSteps: 4, currentStep: 2)
```

## 5. 加载状态场景

### 5.1 页面加载

```dart
class LoadingPage extends StatelessWidget {
  final bool isLoading;
  final Widget child;

  const LoadingPage({
    required this.isLoading,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    if (isLoading) {
      return Center(child: CircularProgressIndicator());
    }
    return child;
  }
}
```

### 5.2 下拉刷新加载

```dart
RefreshIndicator(
  onRefresh: () async {
    await Future.delayed(Duration(seconds: 1));
  },
  child: ListView.builder(
    itemCount: 20,
    itemBuilder: (context, index) {
      return ListTile(title: Text('Item $index'));
    },
  ),
)
```

### 5.3 列表底部加载更多

```dart
ListView.builder(
  itemCount: items.length + 1,
  itemBuilder: (context, index) {
    if (index == items.length) {
      return Padding(
        padding: EdgeInsets.all(16),
        child: Center(child: CircularProgressIndicator()),
      );
    }
    return ListTile(title: Text(items[index]));
  },
)
```

### 5.4 空状态 + 刷新按钮

```dart
class EmptyState extends StatelessWidget {
  final String message;
  final VoidCallback? onRefresh;

  const EmptyState({
    required this.message,
    this.onRefresh,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Icon(Icons.inbox, size: 64, color: Colors.grey),
          SizedBox(height: 16),
          Text(message, style: TextStyle(color: Colors.grey)),
          if (onRefresh != null) ...[
            SizedBox(height: 16),
            TextButton.icon(
              onPressed: onRefresh,
              icon: Icon(Icons.refresh),
              label: Text('刷新'),
            ),
          ],
        ],
      ),
    );
  }
}
```

## 6. 常见问题与解决方案

### 问题1：进度条颜色不生效

```dart
// 使用 color 和 backgroundColor
LinearProgressIndicator(
  color: Colors.blue,
  backgroundColor: Colors.blue[100],
)
```

### 问题2：进度条高度太小

```dart
LinearProgressIndicator(
  minHeight: 8,  // 设置最小高度
)
```

### 问题3：圆形进度条大小调整

```dart
// 使用 SizedBox 包裹
SizedBox(
  width: 48,
  height: 48,
  child: CircularProgressIndicator(),
)
```

### 问题4：进度动画不流畅

```dart
// 使用 AnimatedContainer 或 AnimationController
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  child: LinearProgressIndicator(value: progress),
)
```

### 问题5：进度值超出范围

```dart
// 确保值在 0-1 范围内
LinearProgressIndicator(
  value: progress.clamp(0.0, 1.0),
)
```

## 7. 最佳实践

### 7.1 统一加载组件

```dart
class AppLoading extends StatelessWidget {
  final double size;
  final double strokeWidth;
  final Color? color;

  const AppLoading({
    this.size = 24,
    this.strokeWidth = 2,
    this.color,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SizedBox(
      width: size,
      height: size,
      child: CircularProgressIndicator(
        strokeWidth: strokeWidth,
        color: color ?? Theme.of(context).primaryColor,
      ),
    );
  }
}
```

### 7.2 统一进度条样式

```dart
class AppProgress {
  static LinearProgressIndicator linear({
    double? value,
    double height = 4,
    Color? color,
  }) {
    return LinearProgressIndicator(
      value: value,
      minHeight: height,
      color: color ?? Colors.blue,
      backgroundColor: Colors.grey[200],
      borderRadius: BorderRadius.circular(height / 2),
    );
  }
}
```

### 7.3 语义化支持

```dart
LinearProgressIndicator(
  value: 0.5,
  semanticsLabel: '下载进度',
  semanticsValue: '50%',
)
```

## 总结

Flutter Progress 组件提供了简洁的进度展示方式：

**CircularProgressIndicator**：
- 圆形旋转进度
- 适合加载状态
- 可自定义大小和颜色

**LinearProgressIndicator**：
- 线性水平进度
- 适合下载/上传进度
- 可自定义高度和圆角

**两种模式**：
- 确定模式：设置 value（0-1）
- 不确定模式：value 为 null

**最佳实践**：
1. 统一加载组件样式
2. 添加语义化支持
3. 合理使用动画
4. 确保进度值在有效范围内

---

*最后更新: 2026-02-18*

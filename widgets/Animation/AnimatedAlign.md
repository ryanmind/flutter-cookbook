# Flutter AnimatedAlign 组件用法详解

## 简介

AnimatedAlign 是 Flutter 中用于实现对齐位置动画的组件。它可以自动将对齐方式的变化转换为平滑的位置动画效果。

## 1. 初级用法

### 1.1 基本概念

AnimatedAlign 的核心作用：
- 动画化对齐方式变化
- 无需 AnimationController
- 适合位置过渡动画

### 1.2 基本语法

```dart
AnimatedAlign(
  alignment: _alignment,
  duration: Duration(milliseconds: 300),
  child: Container(width: 50, height: 50, color: Colors.blue),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `alignment` | `AlignmentGeometry` | 对齐方式 |
| `duration` | `Duration` | 动画时长 |
| `curve` | `Curve` | 动画曲线 |
| `heightFactor` | `double?` | 高度因子 |
| `widthFactor` | `double?` | 宽度因子 |
| `onEnd` | `VoidCallback?` | 动画结束回调 |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 位置动画

```dart
class AlignAnimationExample extends StatefulWidget {
  @override
  State<AlignAnimationExample> createState() => _AlignAnimationExampleState();
}

class _AlignAnimationExampleState extends State<AlignAnimationExample> {
  Alignment _alignment = Alignment.topLeft;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Container(
          width: 300,
          height: 200,
          color: Colors.grey[200],
          child: AnimatedAlign(
            alignment: _alignment,
            duration: Duration(milliseconds: 500),
            curve: Curves.easeInOut,
            child: Container(
              width: 80,
              height: 80,
              color: Colors.blue,
            ),
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() {
            _alignment = _alignment == Alignment.topLeft
                ? Alignment.bottomRight
                : Alignment.topLeft;
          }),
          child: Text('Move'),
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 多位置切换

```dart
class MultiPositionExample extends StatefulWidget {
  @override
  State<MultiPositionExample> createState() => _MultiPositionExampleState();
}

class _MultiPositionExampleState extends State<MultiPositionExample> {
  Alignment _alignment = Alignment.center;

  void _changeAlignment(Alignment newAlignment) {
    setState(() => _alignment = newAlignment);
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Container(
          width: 300,
          height: 200,
          color: Colors.grey[200],
          child: AnimatedAlign(
            alignment: _alignment,
            duration: Duration(milliseconds: 300),
            child: Container(
              width: 60,
              height: 60,
              color: Colors.purple,
            ),
          ),
        ),
        Wrap(
          spacing: 8,
          children: [
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.topLeft),
              child: Text('↖'),
            ),
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.topCenter),
              child: Text('↑'),
            ),
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.topRight),
              child: Text('↗'),
            ),
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.centerLeft),
              child: Text('←'),
            ),
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.center),
              child: Text('●'),
            ),
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.centerRight),
              child: Text('→'),
            ),
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.bottomLeft),
              child: Text('↙'),
            ),
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.bottomCenter),
              child: Text('↓'),
            ),
            ElevatedButton(
              onPressed: () => _changeAlignment(Alignment.bottomRight),
              child: Text('↘'),
            ),
          ],
        ),
      ],
    );
  }
}
```

### 2.2 自定义坐标

```dart
AnimatedAlign(
  alignment: Alignment(0.5, -0.3),  // 自定义坐标
  duration: Duration(milliseconds: 300),
  child: Container(width: 50, height: 50, color: Colors.green),
)
```

### 2.3 配合宽高因子

```dart
AnimatedAlign(
  alignment: Alignment.center,
  widthFactor: 2,
  heightFactor: 2,
  duration: Duration(milliseconds: 300),
  child: Container(
    width: 50,
    height: 50,
    color: Colors.orange,
  ),
)
```

### 2.4 RTL 支持

```dart
AnimatedAlign(
  alignment: AlignmentDirectional.centerStart,
  duration: Duration(milliseconds: 300),
  child: Container(
    width: 80,
    height: 80,
    color: Colors.teal,
  ),
)
```

## 3. 高级用法

### 3.1 滑动菜单

```dart
class SlideMenu extends StatefulWidget {
  @override
  State<SlideMenu> createState() => _SlideMenuState();
}

class _SlideMenuState extends State<SlideMenu> {
  bool _isOpen = false;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        // 背景内容
        Container(
          color: Colors.grey[200],
          child: Center(child: Text('Main Content')),
        ),
        // 滑动菜单
        AnimatedAlign(
          alignment: _isOpen ? Alignment.centerLeft : Alignment.centerRight,
          duration: Duration(milliseconds: 300),
          curve: Curves.easeOut,
          child: Container(
            width: 200,
            height: double.infinity,
            color: Colors.blue,
            child: Column(
              children: [
                ListTile(
                  title: Text('Menu Item 1', style: TextStyle(color: Colors.white)),
                  onTap: () {},
                ),
                ListTile(
                  title: Text('Menu Item 2', style: TextStyle(color: Colors.white)),
                  onTap: () {},
                ),
              ],
            ),
          ),
        ),
        // 切换按钮
        Positioned(
          top: 10,
          right: 10,
          child: IconButton(
            icon: Icon(Icons.menu),
            onPressed: () => setState(() => _isOpen = !_isOpen),
          ),
        ),
      ],
    );
  }
}
```

### 3.2 进度指示器

```dart
class ProgressIndicator extends StatefulWidget {
  final double progress;
  final int steps;

  const ProgressIndicator({
    required this.progress,
    this.steps = 4,
    Key? key,
  }) : super(key: key);

  @override
  State<ProgressIndicator> createState() => _ProgressIndicatorState();
}

class _ProgressIndicatorState extends State<ProgressIndicator> {
  @override
  Widget build(BuildContext context) {
    final stepWidth = 1.0 / widget.steps;
    final alignment = Alignment(
      -1 + (widget.progress * 2),  // -1 to 1
      0,
    );

    return Container(
      height: 40,
      decoration: BoxDecoration(
        color: Colors.grey[200],
        borderRadius: BorderRadius.circular(20),
      ),
      child: Stack(
        children: [
          // 步骤点
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: List.generate(widget.steps, (index) {
              return Container(
                width: 12,
                height: 12,
                decoration: BoxDecoration(
                  shape: BoxShape.circle,
                  color: index < widget.progress * widget.steps
                      ? Colors.blue
                      : Colors.grey,
                ),
              );
            }),
          ),
          // 指示器
          AnimatedAlign(
            alignment: alignment,
            duration: Duration(milliseconds: 300),
            child: Container(
              width: 20,
              height: 20,
              decoration: BoxDecoration(
                shape: BoxShape.circle,
                color: Colors.blue,
                border: Border.all(color: Colors.white, width: 3),
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

### 3.3 拖动跟随

```dart
class DraggableBox extends StatefulWidget {
  @override
  State<DraggableBox> createState() => _DraggableBoxState();
}

class _DraggableBoxState extends State<DraggableBox> {
  Alignment _alignment = Alignment.center;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanUpdate: (details) {
        setState(() {
          // 计算新的对齐位置
          final dx = details.delta.dx / 150;  // 缩放因子
          final dy = details.delta.dy / 100;
          final currentX = (_alignment.x + dx).clamp(-1.0, 1.0);
          final currentY = (_alignment.y + dy).clamp(-1.0, 1.0);
          _alignment = Alignment(currentX, currentY);
        });
      },
      onPanEnd: (_) {
        // 可选：松手后回到中心
        // setState(() => _alignment = Alignment.center);
      },
      child: Container(
        width: 300,
        height: 200,
        color: Colors.grey[200],
        child: AnimatedAlign(
          alignment: _alignment,
          duration: Duration(milliseconds: 50),
          child: Container(
            width: 60,
            height: 60,
            color: Colors.red,
          ),
        ),
      ),
    );
  }
}
```

### 3.4 视差效果

```dart
class ParallaxEffect extends StatefulWidget {
  @override
  State<ParallaxEffect> createState() => _ParallaxEffectState();
}

class _ParallaxEffectState extends State<ParallaxEffect> {
  Alignment _foregroundAlignment = Alignment.center;
  Alignment _backgroundAlignment = Alignment.center;

  void _onHover(PointerEvent event) {
    final dx = (event.localPosition.dx - 150) / 150;  // -1 to 1
    final dy = (event.localPosition.dy - 100) / 100;

    setState(() {
      _foregroundAlignment = Alignment(dx, dy);
      _backgroundAlignment = Alignment(dx * 0.3, dy * 0.3);
    });
  }

  @override
  Widget build(BuildContext context) {
    return MouseRegion(
      onHover: _onHover,
      child: Container(
        width: 300,
        height: 200,
        color: Colors.grey[200],
        child: Stack(
          children: [
            // 背景层（慢速）
            AnimatedAlign(
              alignment: _backgroundAlignment,
              duration: Duration(milliseconds: 100),
              child: Container(
                width: 100,
                height: 100,
                color: Colors.blue.withValues(alpha: 0.5),
              ),
            ),
            // 前景层（快速）
            AnimatedAlign(
              alignment: _foregroundAlignment,
              duration: Duration(milliseconds: 100),
              child: Container(
                width: 50,
                height: 50,
                color: Colors.red,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：动画不触发

```dart
// 确保使用不同的 Alignment 实例
AnimatedAlign(
  alignment: _alignment,  // 必须改变这个值
  duration: Duration(milliseconds: 300),
  child: YourWidget(),
)
```

### 问题2：容器大小问题

```dart
// AnimatedAlign 需要有边界才能正确对齐
Container(
  width: 300,  // 必须有明确的尺寸
  height: 200,
  child: AnimatedAlign(
    alignment: _alignment,
    duration: Duration(milliseconds: 300),
    child: YourWidget(),
  ),
)
```

### 问题3：与 Align 的区别

```dart
// Align：静态对齐
Align(alignment: Alignment.center, child: Widget())

// AnimatedAlign：动画对齐
AnimatedAlign(
  alignment: _alignment,  // 变化时会动画
  duration: Duration(milliseconds: 300),
  child: Widget(),
)
```

## 5. AnimatedAlign vs 其他组件

| 组件 | 用途 | 特点 |
|------|------|------|
| **AnimatedAlign** | 对齐动画 | 自动动画 |
| **Align** | 静态对齐 | 无动画 |
| **AnimatedPositioned** | 位置动画 | 绝对定位 |
| **AnimatedContainer** | 多属性动画 | 更通用 |

### 选择建议

```dart
// 对齐位置动画 → AnimatedAlign
AnimatedAlign(alignment: _alignment, ...)

// 绝对位置动画 → AnimatedPositioned
AnimatedPositioned(left: _x, top: _y, ...)

// 多属性动画 → AnimatedContainer
AnimatedContainer(alignment: _alignment, width: _width, ...)
```

## 6. 最佳实践

### 6.1 统一动画配置

```dart
class AnimatedAlignConfig {
  static const Duration duration = Duration(milliseconds: 300);
  static const Curve curve = Curves.easeInOut;
}

AnimatedAlign(
  alignment: _alignment,
  duration: AnimatedAlignConfig.duration,
  curve: AnimatedAlignConfig.curve,
  child: YourWidget(),
)
```

### 6.2 结合其他动画

```dart
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  child: AnimatedAlign(
    alignment: _alignment,
    duration: Duration(milliseconds: 300),
    child: AnimatedOpacity(
      opacity: _opacity,
      duration: Duration(milliseconds: 300),
      child: YourWidget(),
    ),
  ),
)
```

### 6.3 状态管理

```dart
// 使用枚举管理对齐状态
enum Position { topLeft, topRight, bottomLeft, bottomRight }

extension PositionAlignment on Position {
  Alignment get alignment {
    switch (this) {
      case Position.topLeft:
        return Alignment.topLeft;
      case Position.topRight:
        return Alignment.topRight;
      case Position.bottomLeft:
        return Alignment.bottomLeft;
      case Position.bottomRight:
        return Alignment.bottomRight;
    }
  }
}
```

## 总结

AnimatedAlign 是实现位置动画的便捷工具：

**核心要点**：
1. 自动动画化对齐方式变化
2. 需要父容器有明确尺寸
3. 支持自定义坐标和 RTL

**最佳实践**：
1. 统一动画时长和曲线
2. 结合其他动画组件
3. 使用枚举管理对齐状态

**常见场景**：
- 位置切换动画
- 滑动菜单
- 进度指示
- 视差效果

---

*最后更新: 2026-02-19*

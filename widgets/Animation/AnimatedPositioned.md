# Flutter AnimatedPositioned 组件用法详解

## 简介

AnimatedPositioned 是 Flutter 中用于实现位置动画的组件。它只能在 Stack 中使用，可以自动将位置属性的变化转换为平滑的动画效果。

## 1. 初级用法

### 1.1 基本概念

AnimatedPositioned 的核心作用：
- 动画化位置属性变化
- 只能在 Stack 中使用
- 无需 AnimationController

### 1.2 基本语法

```dart
Stack(
  children: [
    AnimatedPositioned(
      left: _left,
      top: _top,
      duration: Duration(milliseconds: 300),
      child: Container(width: 50, height: 50, color: Colors.blue),
    ),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `left` | `double?` | 左边距 |
| `top` | `double?` | 上边距 |
| `right` | `double?` | 右边距 |
| `bottom` | `double?` | 下边距 |
| `width` | `double?` | 宽度 |
| `height` | `double?` | 高度 |
| `duration` | `Duration` | 动画时长 |
| `curve` | `Curve` | 动画曲线 |
| `onEnd` | `VoidCallback?` | 动画结束回调 |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 位置动画

```dart
class PositionAnimationExample extends StatefulWidget {
  @override
  State<PositionAnimationExample> createState() => _PositionAnimationExampleState();
}

class _PositionAnimationExampleState extends State<PositionAnimationExample> {
  bool _moved = false;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Container(
          width: 300,
          height: 200,
          color: Colors.grey[200],
          child: Stack(
            children: [
              AnimatedPositioned(
                left: _moved ? 200 : 50,
                top: _moved ? 150 : 50,
                duration: Duration(milliseconds: 500),
                curve: Curves.easeInOut,
                child: Container(
                  width: 60,
                  height: 60,
                  color: Colors.blue,
                ),
              ),
            ],
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _moved = !_moved),
          child: Text('Move'),
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 尺寸和位置动画

```dart
AnimatedPositioned(
  left: _expanded ? 20 : 50,
  top: _expanded ? 20 : 50,
  width: _expanded ? 260 : 100,
  height: _expanded ? 160 : 100,
  duration: Duration(milliseconds: 300),
  child: Container(
    color: Colors.green,
    child: Center(child: Text('Positioned')),
  ),
)
```

### 2.2 边缘对齐

```dart
AnimatedPositioned(
  left: _isLeft ? 0 : null,
  right: _isLeft ? null : 0,
  top: 50,
  width: 100,
  height: 100,
  duration: Duration(milliseconds: 300),
  child: Container(color: Colors.orange),
)
```

### 2.3 滑动面板

```dart
class SlidePanel extends StatefulWidget {
  @override
  State<SlidePanel> createState() => _SlidePanelState();
}

class _SlidePanelState extends State<SlidePanel> {
  bool _isOpen = false;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        // 主内容
        Container(
          color: Colors.grey[200],
          child: Center(child: Text('Main Content')),
        ),
        // 滑动面板
        AnimatedPositioned(
          left: 0,
          right: 0,
          bottom: _isOpen ? 0 : -200,
          height: 200,
          duration: Duration(milliseconds: 300),
          curve: Curves.easeOut,
          child: Container(
            color: Colors.blue,
            child: Column(
              children: [
                Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    Text('Panel', style: TextStyle(color: Colors.white)),
                    IconButton(
                      icon: Icon(Icons.close, color: Colors.white),
                      onPressed: () => setState(() => _isOpen = false),
                    ),
                  ],
                ),
                // 面板内容
              ],
            ),
          ),
        ),
      ],
    );
  }
}
```

### 2.4 侧边菜单

```dart
class SideMenu extends StatefulWidget {
  @override
  State<SideMenu> createState() => _SideMenuState();
}

class _SideMenuState extends State<SideMenu> {
  bool _isOpen = false;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        // 主内容
        AnimatedPositioned(
          left: _isOpen ? 250 : 0,
          width: MediaQuery.of(context).size.width,
          height: MediaQuery.of(context).size.height,
          duration: Duration(milliseconds: 300),
          child: GestureDetector(
            onTap: () {
              if (_isOpen) setState(() => _isOpen = false);
            },
            child: Container(
              color: Colors.grey[200],
              child: Center(
                child: ElevatedButton(
                  onPressed: () => setState(() => _isOpen = true),
                  child: Text('Open Menu'),
                ),
              ),
            ),
          ),
        ),
        // 侧边菜单
        AnimatedPositioned(
          left: _isOpen ? 0 : -250,
          width: 250,
          top: 0,
          bottom: 0,
          duration: Duration(milliseconds: 300),
          child: Container(
            color: Colors.blue,
            child: Column(
              children: [
                DrawerHeader(child: Text('Menu')),
                ListTile(title: Text('Item 1', style: TextStyle(color: Colors.white))),
                ListTile(title: Text('Item 2', style: TextStyle(color: Colors.white))),
              ],
            ),
          ),
        ),
      ],
    );
  }
}
```

## 3. 高级用法

### 3.1 浮动按钮

```dart
class FloatingButtons extends StatefulWidget {
  @override
  State<FloatingButtons> createState() => _FloatingButtonsState();
}

class _FloatingButtonsState extends State<FloatingButtons> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return Stack(
      clipBehavior: Clip.none,
      children: [
        // 子按钮
        for (int i = 0; i < 3; i++)
          AnimatedPositioned(
            right: _expanded ? 80 + i * 60.0 : 20,
            bottom: 20,
            duration: Duration(milliseconds: 300),
            curve: Interval(i * 0.1, 1.0, curve: Curves.easeOut),
            child: ScaleTransition(
              scale: _expanded ? AlwaysStoppedAnimation(1) : AlwaysStoppedAnimation(0),
              child: FloatingActionButton(
                heroTag: 'btn_$i',
                mini: true,
                onPressed: () {},
                child: Icon(Icons.star),
              ),
            ),
          ),
        // 主按钮
        Positioned(
          right: 20,
          bottom: 20,
          child: FloatingActionButton(
            onPressed: () => setState(() => _expanded = !_expanded),
            child: AnimatedRotation(
              turns: _expanded ? 0.125 : 0,
              duration: Duration(milliseconds: 300),
              child: Icon(Icons.add),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.2 拖动元素

```dart
class DraggableElement extends StatefulWidget {
  @override
  State<DraggableElement> createState() => _DraggableElementState();
}

class _DraggableElementState extends State<DraggableElement> {
  double _x = 100;
  double _y = 100;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        AnimatedPositioned(
          left: _x,
          top: _y,
          duration: Duration(milliseconds: 50),
          child: GestureDetector(
            onPanUpdate: (details) {
              setState(() {
                _x += details.delta.dx;
                _y += details.delta.dy;
                // 限制边界
                _x = _x.clamp(0.0, 250.0);
                _y = _y.clamp(0.0, 150.0);
              });
            },
            child: Container(
              width: 60,
              height: 60,
              color: Colors.red,
              child: Center(child: Text('Drag')),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.3 弹出提示

```dart
class AnimatedTooltip extends StatefulWidget {
  final String message;
  final bool show;

  const AnimatedTooltip({
    required this.message,
    required this.show,
    Key? key,
  }) : super(key: key);

  @override
  State<AnimatedTooltip> createState() => _AnimatedTooltipState();
}

class _AnimatedTooltipState extends State<AnimatedTooltip> {
  @override
  Widget build(BuildContext context) {
    return Stack(
      clipBehavior: Clip.none,
      children: [
        AnimatedPositioned(
          top: widget.show ? -50 : -100,
          left: 0,
          right: 0,
          height: 40,
          duration: Duration(milliseconds: 200),
          curve: Curves.easeOut,
          child: Center(
            child: Container(
              padding: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
              decoration: BoxDecoration(
                color: Colors.black87,
                borderRadius: BorderRadius.circular(4),
              ),
              child: Text(
                widget.message,
                style: TextStyle(color: Colors.white),
              ),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.4 进度条动画

```dart
class AnimatedProgressBar extends StatelessWidget {
  final double progress;
  final double height;
  final Color color;

  const AnimatedProgressBar({
    required this.progress,
    this.height = 8,
    this.color = Colors.blue,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Container(
          height: height,
          decoration: BoxDecoration(
            color: Colors.grey[200],
            borderRadius: BorderRadius.circular(height / 2),
          ),
        ),
        AnimatedPositioned(
          left: 0,
          top: 0,
          bottom: 0,
          width: progress * MediaQuery.of(context).size.width,
          duration: Duration(milliseconds: 300),
          child: Container(
            decoration: BoxDecoration(
              color: color,
              borderRadius: BorderRadius.circular(height / 2),
            ),
          ),
        ),
      ],
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：必须在 Stack 中使用

```dart
// ❌ 错误：不在 Stack 中
Column(
  children: [
    AnimatedPositioned(left: 0, top: 0, child: Container()),  // 错误
  ],
)

// ✅ 正确：在 Stack 中使用
Stack(
  children: [
    AnimatedPositioned(left: 0, top: 0, child: Container()),
  ],
)
```

### 问题2：位置冲突

```dart
// ❌ 错误：同时设置 left 和 right 且设置 width
AnimatedPositioned(
  left: 0,
  right: 0,
  width: 100,  // 冲突
)

// ✅ 正确：使用 left + right 或 left + width
AnimatedPositioned(
  left: 0,
  right: 0,  // 宽度自动计算
)
```

### 问题3：超出边界

```dart
// 使用 clipBehavior 控制裁剪
Stack(
  clipBehavior: Clip.hardEdge,
  children: [
    AnimatedPositioned(...),
  ],
)
```

## 5. AnimatedPositioned vs 其他组件

| 组件 | 用途 | 特点 |
|------|------|------|
| **AnimatedPositioned** | 位置动画 | 只在 Stack 中 |
| **Positioned** | 静态定位 | 只在 Stack 中 |
| **AnimatedAlign** | 对齐动画 | 任意容器 |
| **AnimatedContainer** | 多属性动画 | 更通用 |

### 选择建议

```dart
// Stack 中的位置动画 → AnimatedPositioned
AnimatedPositioned(left: _x, top: _y, ...)

// 对齐方式动画 → AnimatedAlign
AnimatedAlign(alignment: _alignment, ...)

// 多属性动画 → AnimatedContainer
AnimatedContainer(margin: ..., width: ..., ...)
```

## 6. 最佳实践

### 6.1 边界约束

```dart
AnimatedPositioned(
  left: _x.clamp(0.0, maxWidth - itemWidth),
  top: _y.clamp(0.0, maxHeight - itemHeight),
  duration: Duration(milliseconds: 50),
  child: YourWidget(),
)
```

### 6.2 动画配置

```dart
class PositionAnimationConfig {
  static const Duration duration = Duration(milliseconds: 300);
  static const Curve curve = Curves.easeOutCubic;
}
```

### 6.3 组合动画

```dart
AnimatedPositioned(
  left: _x,
  top: _y,
  duration: Duration(milliseconds: 300),
  child: AnimatedOpacity(
    opacity: _visible ? 1.0 : 0.0,
    duration: Duration(milliseconds: 300),
    child: YourWidget(),
  ),
)
```

## 总结

AnimatedPositioned 是 Stack 中实现位置动画的便捷工具：

**核心要点**：
1. 只能在 Stack 中使用
2. 支持位置和尺寸属性动画
3. 无需 AnimationController

**最佳实践**：
1. 注意边界约束
2. 使用 clipBehavior 控制裁剪
3. 组合其他动画组件

**常见场景**：
- 滑动面板
- 侧边菜单
- 浮动按钮
- 拖动元素

---

*最后更新: 2026-02-19*

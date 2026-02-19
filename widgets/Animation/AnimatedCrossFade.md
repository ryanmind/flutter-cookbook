# Flutter AnimatedCrossFade 组件用法详解

## 简介

AnimatedCrossFade 是 Flutter 中用于在两个子组件之间实现淡入淡出切换动画的组件。它可以自动处理两个组件之间的过渡动画。

## 1. 初级用法

### 1.1 基本概念

AnimatedCrossFade 的核心作用：
- 在两个组件间平滑切换
- 自动处理淡入淡出动画
- 支持自定义动画曲线和时长

### 1.2 基本语法

```dart
AnimatedCrossFade(
  firstChild: Widget1(),
  secondChild: Widget2(),
  crossFadeState: _showFirst 
      ? CrossFadeState.showFirst 
      : CrossFadeState.showSecond,
  duration: Duration(milliseconds: 300),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `firstChild` | `Widget` | 第一个子组件 |
| `secondChild` | `Widget` | 第二个子组件 |
| `crossFadeState` | `CrossFadeState` | 显示哪个组件 |
| `duration` | `Duration` | 动画时长 |
| `firstCurve` | `Curve` | 第一个组件动画曲线 |
| `secondCurve` | `Curve` | 第二个组件动画曲线 |
| `sizeCurve` | `Curve` | 尺寸动画曲线 |
| `alignment` | `Alignment` | 对齐方式 |
| `layoutBuilder` | `Widget Function` | 自定义布局 |
| `excludeBottomFocus` | `bool` | 是否排除底部焦点 |

### 1.4 基础示例

#### 组件切换

```dart
class CrossFadeExample extends StatefulWidget {
  @override
  State<CrossFadeExample> createState() => _CrossFadeExampleState();
}

class _CrossFadeExampleState extends State<CrossFadeExample> {
  bool _showFirst = true;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedCrossFade(
          firstChild: Container(
            width: 200,
            height: 100,
            color: Colors.blue,
            child: Center(child: Text('First', style: TextStyle(color: Colors.white))),
          ),
          secondChild: Container(
            width: 200,
            height: 150,
            color: Colors.red,
            child: Center(child: Text('Second', style: TextStyle(color: Colors.white))),
          ),
          crossFadeState: _showFirst 
              ? CrossFadeState.showFirst 
              : CrossFadeState.showSecond,
          duration: Duration(milliseconds: 300),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _showFirst = !_showFirst),
          child: Text('Toggle'),
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 自定义曲线

```dart
AnimatedCrossFade(
  firstChild: Container(...),
  secondChild: Container(...),
  crossFadeState: _state,
  duration: Duration(milliseconds: 500),
  firstCurve: Curves.easeOut,
  secondCurve: Curves.easeIn,
  sizeCurve: Curves.easeInOutCubic,
)
```

### 2.2 加载状态切换

```dart
class LoadingCrossFade extends StatelessWidget {
  final bool isLoading;
  final Widget child;

  const LoadingCrossFade({
    required this.isLoading,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return AnimatedCrossFade(
      firstChild: Center(
        child: CircularProgressIndicator(),
      ),
      secondChild: child,
      crossFadeState: isLoading 
          ? CrossFadeState.showFirst 
          : CrossFadeState.showSecond,
      duration: Duration(milliseconds: 300),
    );
  }
}
```

### 2.3 展开/折叠

```dart
class ExpandableCard extends StatefulWidget {
  final String title;
  final String content;

  const ExpandableCard({
    required this.title,
    required this.content,
    Key? key,
  }) : super(key: key);

  @override
  State<ExpandableCard> createState() => _ExpandableCardState();
}

class _ExpandableCardState extends State<ExpandableCard> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Column(
        children: [
          ListTile(
            title: Text(widget.title),
            trailing: IconButton(
              icon: Icon(_expanded ? Icons.expand_less : Icons.expand_more),
              onPressed: () => setState(() => _expanded = !_expanded),
            ),
          ),
          AnimatedCrossFade(
            firstChild: SizedBox.shrink(),
            secondChild: Padding(
              padding: EdgeInsets.all(16),
              child: Text(widget.content),
            ),
            crossFadeState: _expanded 
                ? CrossFadeState.showSecond 
                : CrossFadeState.showFirst,
            duration: Duration(milliseconds: 300),
          ),
        ],
      ),
    );
  }
}
```

### 2.4 图标切换

```dart
class AnimatedIconButton extends StatefulWidget {
  final IconData icon1;
  final IconData icon2;
  final VoidCallback onPressed;

  const AnimatedIconButton({
    required this.icon1,
    required this.icon2,
    required this.onPressed,
    Key? key,
  }) : super(key: key);

  @override
  State<AnimatedIconButton> createState() => _AnimatedIconButtonState();
}

class _AnimatedIconButtonState extends State<AnimatedIconButton> {
  bool _isFirst = true;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        setState(() => _isFirst = !_isFirst);
        widget.onPressed();
      },
      child: AnimatedCrossFade(
        firstChild: Icon(widget.icon1, size: 32),
        secondChild: Icon(widget.icon2, size: 32),
        crossFadeState: _isFirst 
            ? CrossFadeState.showFirst 
            : CrossFadeState.showSecond,
        duration: Duration(milliseconds: 200),
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 登录/注册表单切换

```dart
class AuthFormSwitcher extends StatefulWidget {
  @override
  State<AuthFormSwitcher> createState() => _AuthFormSwitcherState();
}

class _AuthFormSwitcherState extends State<AuthFormSwitcher> {
  bool _isLogin = true;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedCrossFade(
          firstChild: _LoginForm(),
          secondChild: _RegisterForm(),
          crossFadeState: _isLogin 
              ? CrossFadeState.showFirst 
              : CrossFadeState.showSecond,
          duration: Duration(milliseconds: 400),
          sizeCurve: Curves.easeInOut,
        ),
        TextButton(
          onPressed: () => setState(() => _isLogin = !_isLogin),
          child: Text(_isLogin ? 'Create Account' : 'Sign In'),
        ),
      ],
    );
  }
}
```

### 3.2 自定义布局

```dart
AnimatedCrossFade(
  firstChild: Container(width: 100, height: 100, color: Colors.blue),
  secondChild: Container(width: 200, height: 50, color: Colors.red),
  crossFadeState: _state,
  duration: Duration(milliseconds: 300),
  alignment: Alignment.topLeft,
  layoutBuilder: (topChild, topChildKey, bottomChild, bottomChildKey) {
    return Stack(
      clipBehavior: Clip.none,
      children: [
        Positioned(
          key: bottomChildKey,
          child: bottomChild,
        ),
        Positioned(
          key: topChildKey,
          child: topChild,
        ),
      ],
    );
  },
)
```

### 3.3 多状态切换

```dart
class MultiStateWidget extends StatefulWidget {
  @override
  State<MultiStateWidget> createState() => _MultiStateWidgetState();
}

class _MultiStateWidgetState extends State<MultiStateWidget> {
  int _state = 0;

  Widget _buildContent() {
    switch (_state) {
      case 0:
        return Container(height: 50, color: Colors.blue, child: Text('State 0'));
      case 1:
        return Container(height: 80, color: Colors.green, child: Text('State 1'));
      case 2:
        return Container(height: 60, color: Colors.red, child: Text('State 2'));
      default:
        return SizedBox.shrink();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedCrossFade(
          firstChild: _buildContent(),
          secondChild: Container(height: 100, color: Colors.grey),
          crossFadeState: CrossFadeState.showFirst,
          duration: Duration(milliseconds: 300),
        ),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: List.generate(3, (index) {
            return ElevatedButton(
              onPressed: () => setState(() => _state = index),
              child: Text('State $index'),
            );
          }),
        ),
      ],
    );
  }
}
```

### 3.4 响应式布局切换

```dart
class ResponsiveLayout extends StatelessWidget {
  final Widget mobile;
  final Widget desktop;

  const ResponsiveLayout({
    required this.mobile,
    required this.desktop,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final isDesktop = MediaQuery.of(context).size.width > 600;
    
    return AnimatedCrossFade(
      firstChild: mobile,
      secondChild: desktop,
      crossFadeState: isDesktop 
          ? CrossFadeState.showSecond 
          : CrossFadeState.showFirst,
      duration: Duration(milliseconds: 300),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：尺寸差异导致跳跃

```dart
// 使用 sizeCurve 平滑过渡
AnimatedCrossFade(
  firstChild: Container(height: 100, ...),
  secondChild: Container(height: 200, ...),
  sizeCurve: Curves.easeInOut,  // 平滑尺寸变化
  duration: Duration(milliseconds: 300),
)
```

### 问题2：动画不同步

```dart
// 确保时长一致
AnimatedCrossFade(
  duration: Duration(milliseconds: 300),
  firstCurve: Curves.easeOut,
  secondCurve: Curves.easeIn,
  sizeCurve: Curves.easeInOut,
)
```

### 问题3：焦点问题

```dart
// 使用 excludeBottomFocus 控制焦点
AnimatedCrossFade(
  excludeBottomFocus: true,
  firstChild: TextField(),
  secondChild: Container(),
  ...
)
```

## 5. AnimatedCrossFade vs 其他切换方式

| 方式 | 用途 | 特点 |
|------|------|------|
| **AnimatedCrossFade** | 两个组件切换 | 自动淡入淡出 |
| **AnimatedSwitcher** | 多组件切换 | 更灵活 |
| **AnimatedOpacity** | 单组件透明度 | 需手动管理 |
| **Visibility** | 显示隐藏 | 无动画 |

### 选择建议

```dart
// 两个组件切换 → AnimatedCrossFade
AnimatedCrossFade(firstChild: A, secondChild: B, ...)

// 多组件切换 → AnimatedSwitcher
AnimatedSwitcher(child: currentWidget, ...)

// 简单显示隐藏 → Visibility
Visibility(visible: show, child: widget)
```

## 6. 最佳实践

### 6.1 统一配置

```dart
class CrossFadeConfig {
  static const Duration duration = Duration(milliseconds: 300);
  static const Curve curve = Curves.easeInOut;
}

AnimatedCrossFade(
  duration: CrossFadeConfig.duration,
  firstCurve: CrossFadeConfig.curve,
  secondCurve: CrossFadeConfig.curve,
  ...
)
```

### 6.2 保持组件 Key

```dart
AnimatedCrossFade(
  firstChild: Container(
    key: ValueKey('first'),
    ...
  ),
  secondChild: Container(
    key: ValueKey('second'),
    ...
  ),
  ...
)
```

### 6.3 空组件处理

```dart
AnimatedCrossFade(
  firstChild: actualContent,
  secondChild: SizedBox.shrink(),  // 空组件
  crossFadeState: showContent 
      ? CrossFadeState.showFirst 
      : CrossFadeState.showSecond,
  duration: Duration(milliseconds: 300),
)
```

## 总结

AnimatedCrossFade 是实现两个组件间切换动画的便捷工具：

**核心要点**：
1. 在两个固定子组件间切换
2. 自动处理淡入淡出和尺寸变化
3. 支持 excludeBottomFocus 焦点管理

**最佳实践**：
1. 统一动画配置
2. 使用 sizeCurve 平滑尺寸过渡
3. 处理焦点问题

**常见场景**：
- 加载状态切换
- 展开/折叠
- 表单切换
- 响应式布局

---

*最后更新: 2026-02-19*

# Flutter AnimatedSwitcher 组件用法详解

## 简介

AnimatedSwitcher 是 Flutter 中用于在多个子组件之间实现切换动画的组件。与 AnimatedCrossFade 不同，它可以处理任意数量的子组件切换。

## 1. 初级用法

### 1.1 基本概念

AnimatedSwitcher 的核心作用：
- 子组件切换时自动添加动画
- 支持任意数量组件切换
- 根据 Key 识别组件变化

### 1.2 基本语法

```dart
AnimatedSwitcher(
  duration: Duration(milliseconds: 300),
  child: Text(
    _count.toString(),
    key: ValueKey(_count),  // Key 决定是否触发动画
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `child` | `Widget?` | 当前子组件 |
| `duration` | `Duration` | 动画时长 |
| `reverseDuration` | `Duration?` | 反向动画时长 |
| `switchInCurve` | `Curve` | 进入曲线 |
| `switchOutCurve` | `Curve` | 退出曲线 |
| `transitionBuilder` | `Widget Function` | 自定义过渡动画 |
| `layoutBuilder` | `Widget Function` | 自定义布局 |
| `clipBehavior` | `Clip` | 裁剪行为 |

### 1.4 基础示例

#### 数字切换

```dart
class CounterAnimation extends StatefulWidget {
  @override
  State<CounterAnimation> createState() => _CounterAnimationState();
}

class _CounterAnimationState extends State<CounterAnimation> {
  int _count = 0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedSwitcher(
          duration: Duration(milliseconds: 300),
          transitionBuilder: (child, animation) {
            return FadeTransition(
              opacity: animation,
              child: SlideTransition(
                position: Tween<Offset>(
                  begin: Offset(0, 0.5),
                  end: Offset.zero,
                ).animate(animation),
                child: child,
              ),
            );
          },
          child: Text(
            '$_count',
            key: ValueKey(_count),
            style: TextStyle(fontSize: 48),
          ),
        ),
        Row(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            IconButton(
              icon: Icon(Icons.remove),
              onPressed: () => setState(() => _count--),
            ),
            IconButton(
              icon: Icon(Icons.add),
              onPressed: () => setState(() => _count++),
            ),
          ],
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 淡入淡出

```dart
AnimatedSwitcher(
  duration: Duration(milliseconds: 300),
  child: Container(
    key: ValueKey(_color),
    width: 100,
    height: 100,
    color: _color,
  ),
)
```

### 2.2 缩放过渡

```dart
AnimatedSwitcher(
  duration: Duration(milliseconds: 300),
  transitionBuilder: (child, animation) {
    return ScaleTransition(
      scale: animation,
      child: child,
    );
  },
  child: Icon(
    _isActive ? Icons.check_circle : Icons.circle_outlined,
    key: ValueKey(_isActive),
    size: 48,
  ),
)
```

### 2.3 滑动过渡

```dart
AnimatedSwitcher(
  duration: Duration(milliseconds: 300),
  transitionBuilder: (child, animation) {
    return SlideTransition(
      position: Tween<Offset>(
        begin: Offset(1, 0),
        end: Offset.zero,
      ).animate(animation),
      child: child,
    );
  },
  child: Text(
    _text,
    key: ValueKey(_text),
  ),
)
```

### 2.4 组合过渡

```dart
AnimatedSwitcher(
  duration: Duration(milliseconds: 300),
  transitionBuilder: (child, animation) {
    return FadeTransition(
      opacity: animation,
      child: SlideTransition(
        position: Tween<Offset>(
          begin: Offset(0, -1),
          end: Offset.zero,
        ).animate(animation),
        child: child,
      ),
    );
  },
  child: _widget,
)
```

## 3. 高级用法

### 3.1 页面内容切换

```dart
class PageSwitcher extends StatefulWidget {
  @override
  State<PageSwitcher> createState() => _PageSwitcherState();
}

class _PageSwitcherState extends State<PageSwitcher> {
  int _currentIndex = 0;
  final List<Widget> _pages = [
    HomePage(),
    SearchPage(),
    ProfilePage(),
  ];

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Expanded(
          child: AnimatedSwitcher(
            duration: Duration(milliseconds: 300),
            transitionBuilder: (child, animation) {
              return FadeTransition(
                opacity: animation,
                child: child,
              );
            },
            child: _pages[_currentIndex],
          ),
        ),
        BottomNavigationBar(
          currentIndex: _currentIndex,
          onTap: (index) => setState(() => _currentIndex = index),
          items: [
            BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
            BottomNavigationBarItem(icon: Icon(Icons.search), label: 'Search'),
            BottomNavigationBarItem(icon: Icon(Icons.person), label: 'Profile'),
          ],
        ),
      ],
    );
  }
}
```

### 3.2 加载状态切换

```dart
class LoadingSwitcher extends StatelessWidget {
  final bool isLoading;
  final Widget child;

  const LoadingSwitcher({
    required this.isLoading,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return AnimatedSwitcher(
      duration: Duration(milliseconds: 200),
      child: isLoading
          ? Center(
              key: ValueKey('loading'),
              child: CircularProgressIndicator(),
            )
          : child,
    );
  }
}
```

### 3.3 图标状态切换

```dart
class AnimatedFavoriteButton extends StatefulWidget {
  final bool isFavorite;
  final VoidCallback onTap;

  const AnimatedFavoriteButton({
    required this.isFavorite,
    required this.onTap,
    Key? key,
  }) : super(key: key);

  @override
  State<AnimatedFavoriteButton> createState() => _AnimatedFavoriteButtonState();
}

class _AnimatedFavoriteButtonState extends State<AnimatedFavoriteButton> {
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: widget.onTap,
      child: AnimatedSwitcher(
        duration: Duration(milliseconds: 300),
        transitionBuilder: (child, animation) {
          return ScaleTransition(
            scale: Tween<double>(begin: 0.5, end: 1.0).animate(
              CurvedAnimation(parent: animation, curve: Curves.elasticOut),
            ),
            child: child,
          );
        },
        child: Icon(
          widget.isFavorite ? Icons.favorite : Icons.favorite_border,
          key: ValueKey(widget.isFavorite),
          color: widget.isFavorite ? Colors.red : Colors.grey,
          size: 32,
        ),
      ),
    );
  }
}
```

### 3.4 自定义布局

```dart
AnimatedSwitcher(
  duration: Duration(milliseconds: 300),
  layoutBuilder: (currentChild, previousChildren) {
    return Stack(
      alignment: Alignment.center,
      children: <Widget>[
        ...previousChildren,
        if (currentChild != null) currentChild,
      ],
    );
  },
  transitionBuilder: (child, animation) {
    return RotationTransition(
      turns: animation,
      child: FadeTransition(
        opacity: animation,
        child: child,
      ),
    );
  },
  child: _widget,
)
```

### 3.5 评分星星

```dart
class AnimatedRating extends StatelessWidget {
  final int rating;
  final int maxRating;

  const AnimatedRating({
    required this.rating,
    this.maxRating = 5,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      mainAxisSize: MainAxisSize.min,
      children: List.generate(maxRating, (index) {
        return AnimatedSwitcher(
          duration: Duration(milliseconds: 200),
          transitionBuilder: (child, animation) {
            return ScaleTransition(
              scale: animation,
              child: child,
            );
          },
          child: Icon(
            index < rating ? Icons.star : Icons.star_border,
            key: ValueKey(index < rating),
            color: Colors.amber,
          ),
        );
      }),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：动画不触发

```dart
// ❌ 错误：没有设置 Key
AnimatedSwitcher(
  child: Text(_text),  // 不会触发动画
)

// ✅ 正确：设置 Key
AnimatedSwitcher(
  child: Text(_text, key: ValueKey(_text)),  // 触发动画
)
```

### 问题2：退出动画问题

```dart
// 设置 reverseDuration 和 switchOutCurve
AnimatedSwitcher(
  duration: Duration(milliseconds: 300),
  reverseDuration: Duration(milliseconds: 200),
  switchOutCurve: Curves.easeIn,
  switchInCurve: Curves.easeOut,
  child: _widget,
)
```

### 问题3：布局跳动

```dart
// 确保切换的组件大小一致或使用 layoutBuilder
AnimatedSwitcher(
  layoutBuilder: (currentChild, previousChildren) {
    return SizedBox(
      width: 100,
      height: 100,
      child: Stack(
        alignment: Alignment.center,
        children: [...previousChildren, if (currentChild != null) currentChild],
      ),
    );
  },
  child: _widget,
)
```

## 5. AnimatedSwitcher vs 其他切换方式

| 方式 | 用途 | 特点 |
|------|------|------|
| **AnimatedSwitcher** | 多组件切换 | 基于 Key 识别 |
| **AnimatedCrossFade** | 两组件切换 | 固定两个子组件 |
| **AnimatedOpacity** | 单组件透明度 | 手动控制 |
| **IndexedStack** | 无动画切换 | 保持状态 |

### 选择建议

```dart
// 多个组件切换 → AnimatedSwitcher
AnimatedSwitcher(child: currentWidget, ...)

// 固定两个组件 → AnimatedCrossFade
AnimatedCrossFade(firstChild: A, secondChild: B, ...)

// 保持状态无动画 → IndexedStack
IndexedStack(index: currentIndex, children: [...])
```

## 6. 最佳实践

### 6.1 Key 管理

```dart
// 使用 ValueKey 标识不同状态
AnimatedSwitcher(
  child: Text(
    _text,
    key: ValueKey(_text),  // 或 ObjectKey, UniqueKey
  ),
)
```

### 6.2 复用过渡动画

```dart
Widget buildAnimatedSwitcher(Widget child, {Duration? duration}) {
  return AnimatedSwitcher(
    duration: duration ?? Duration(milliseconds: 300),
    transitionBuilder: (widget, animation) {
      return FadeTransition(
        opacity: animation,
        child: SlideTransition(
          position: Tween<Offset>(
            begin: Offset(0.5, 0),
            end: Offset.zero,
          ).animate(animation),
          child: widget,
        ),
      );
    },
    child: child,
  );
}
```

### 6.3 性能优化

```dart
// 使用 const 子组件
AnimatedSwitcher(
  child: const Text('Static Content', key: ValueKey('static')),
)
```

## 总结

AnimatedSwitcher 是实现组件切换动画的灵活工具：

**核心要点**：
1. Key 决定是否触发动画
2. 支持自定义过渡动画
3. 可处理任意数量组件切换

**最佳实践**：
1. 始终设置 Key
2. 自定义 transitionBuilder
3. 复用过渡动画逻辑

**常见场景**：
- 数字/文本切换
- 加载状态
- 图标状态
- 页面切换

---

*最后更新: 2026-02-19*

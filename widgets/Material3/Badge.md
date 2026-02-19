# Flutter Badge 组件用法详解

## 简介

Badge 是 Material 3 新增的徽章组件，用于在图标或文字上显示状态提示，如未读消息数、新通知、状态标记等。

## 1. 基本用法

### 1.1 基本概念

Badge 的核心特点：
- Material 3 标准徽章
- 支持数字和自定义内容
- 可自定义位置、颜色、大小
- 通常配合图标使用

### 1.2 基本语法

```dart
Badge(
  child: Icon(Icons.mail),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `label` | `Widget?` | - | 徽章内容 |
| `backgroundColor` | `Color?` | - | 背景色 |
| `textColor` | `Color?` | - | 文字颜色 |
| `smallSize` | `double?` | `6` | 小徽章尺寸 |
| `largeSize` | `double?` | `16` | 大徽章尺寸 |
| `offset` | `Offset?` | - | 偏移位置 |
| `alignment` | `Alignment?` | - | 对齐方式 |
| `padding` | `EdgeInsetsGeometry?` | - | 内边距 |
| `child` | `Widget?` | - | 子组件 |

### 1.4 基础示例

#### 小圆点徽章

```dart
Badge(
  child: Icon(Icons.mail),
)
```

#### 数字徽章

```dart
Badge(
  label: Text('3'),
  child: Icon(Icons.mail),
)
```

#### 大数字徽章

```dart
Badge(
  label: Text('99+'),
  child: Icon(Icons.notifications),
)
```

## 2. 自定义样式

### 2.1 自定义颜色

```dart
Badge(
  backgroundColor: Colors.red,
  textColor: Colors.white,
  label: Text('5'),
  child: Icon(Icons.mail, color: Colors.grey),
)
```

### 2.2 自定义位置

```dart
Badge(
  offset: Offset(-4, 4),
  label: Text('1'),
  child: Icon(Icons.shopping_cart),
)
```

### 2.3 自定义对齐

```dart
Badge(
  alignment: Alignment.bottomLeft,
  label: Text('●'),
  child: Icon(Icons.person),
)
```

### 2.4 自定义大小

```dart
Badge(
  largeSize: 20,
  label: Text('10'),
  child: Icon(Icons.mail),
)

// 小圆点大小
Badge(
  smallSize: 8,
  child: Icon(Icons.mail),
)
```

## 3. 常见使用场景

### 3.1 消息通知

```dart
class MessageIcon extends StatelessWidget {
  final int unreadCount;

  const MessageIcon({required this.unreadCount, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        IconButton(
          icon: Icon(Icons.mail),
          onPressed: () {},
        ),
        if (unreadCount > 0)
          Positioned(
            right: 8,
            top: 8,
            child: Container(
              padding: EdgeInsets.symmetric(horizontal: 6, vertical: 2),
              decoration: BoxDecoration(
                color: Colors.red,
                borderRadius: BorderRadius.circular(10),
              ),
              child: Text(
                unreadCount > 99 ? '99+' : '$unreadCount',
                style: TextStyle(color: Colors.white, fontSize: 12),
              ),
            ),
          ),
      ],
    );
  }
}

// 或使用 Badge
class MessageBadge extends StatelessWidget {
  final int count;

  const MessageBadge({required this.count, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Badge(
      label: Text(count > 99 ? '99+' : '$count'),
      backgroundColor: Colors.red,
      child: IconButton(
        icon: Icon(Icons.mail),
        onPressed: () {},
      ),
    );
  }
}
```

### 3.2 底部导航栏徽章

```dart
NavigationBar(
  destinations: [
    NavigationDestination(
      icon: Icon(Icons.home),
      label: '首页',
    ),
    NavigationDestination(
      icon: Badge(
        label: Text('5'),
        child: Icon(Icons.message),
      ),
      label: '消息',
    ),
    NavigationDestination(
      icon: Badge(
        child: Icon(Icons.notifications),
      ),
      label: '通知',
    ),
    NavigationDestination(
      icon: Icon(Icons.person),
      label: '我的',
    ),
  ],
)
```

### 3.3 列表项徽章

```dart
ListTile(
  leading: Badge(
    backgroundColor: Colors.green,
    child: CircleAvatar(child: Icon(Icons.person)),
  ),
  title: Text('在线用户'),
  subtitle: Text('正在输入...'),
)
```

### 3.4 TabBar 徽章

```dart
TabBar(
  tabs: [
    Tab(text: '首页'),
    Tab(
      child: Row(
        mainAxisSize: MainAxisSize.min,
        children: [
          Text('消息'),
          SizedBox(width: 4),
          Badge(
            label: Text('3'),
            backgroundColor: Colors.red,
          ),
        ],
      ),
    ),
    Tab(text: '我的'),
  ],
)
```

## 4. 高级用法

### 4.1 状态徽章

```dart
class StatusBadge extends StatelessWidget {
  final bool isOnline;
  final Widget child;

  const StatusBadge({
    required this.isOnline,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Badge(
      backgroundColor: isOnline ? Colors.green : Colors.grey,
      offset: Offset(4, 4),
      alignment: Alignment.bottomRight,
      child: child,
    );
  }
}

// 使用
StatusBadge(
  isOnline: true,
  child: CircleAvatar(
    backgroundImage: NetworkImage('https://picsum.photos/100'),
  ),
)
```

### 4.2 动态徽章

```dart
class DynamicBadge extends StatefulWidget {
  final Widget child;
  final int count;

  const DynamicBadge({
    required this.child,
    required this.count,
    Key? key,
  }) : super(key: key);

  @override
  _DynamicBadgeState createState() => _DynamicBadgeState();
}

class _DynamicBadgeState extends State<DynamicBadge>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 300),
      vsync: this,
    );
    _animation = Tween<double>(begin: 1, end: 1.2).animate(
      CurvedAnimation(parent: _controller, curve: Curves.elasticOut),
    );
  }

  @override
  void didUpdateWidget(DynamicBadge oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (oldWidget.count != widget.count) {
      _controller.forward(from: 0);
    }
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return ScaleTransition(
      scale: _animation,
      child: Badge(
        label: Text('${widget.count}'),
        child: widget.child,
      ),
    );
  }
}
```

### 4.3 多种徽章类型

```dart
enum BadgeType { dot, number, text, icon }

class CustomBadge extends StatelessWidget {
  final BadgeType type;
  final String? label;
  final Color backgroundColor;
  final Widget child;

  const CustomBadge({
    required this.type,
    this.label,
    this.backgroundColor = Colors.red,
    required this.child,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    switch (type) {
      case BadgeType.dot:
        return Badge(
          backgroundColor: backgroundColor,
          child: child,
        );
      case BadgeType.number:
      case BadgeType.text:
        return Badge(
          label: Text(label ?? ''),
          backgroundColor: backgroundColor,
          child: child,
        );
      case BadgeType.icon:
        return Badge(
          label: Icon(Icons.check, size: 12, color: Colors.white),
          backgroundColor: backgroundColor,
          child: child,
        );
    }
  }
}
```

### 4.4 徽章按钮

```dart
class BadgeButton extends StatelessWidget {
  final IconData icon;
  final int? count;
  final VoidCallback? onPressed;

  const BadgeButton({
    required this.icon,
    this.count,
    this.onPressed,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    if (count == null || count == 0) {
      return IconButton(
        icon: Icon(icon),
        onPressed: onPressed,
      );
    }

    return Badge(
      label: Text(count! > 99 ? '99+' : '$count'),
      backgroundColor: Colors.red,
      child: IconButton(
        icon: Icon(icon),
        onPressed: onPressed,
      ),
    );
  }
}
```

## 5. Badge vs 自定义实现

### 5.1 使用 Badge（推荐）

```dart
Badge(
  label: Text('5'),
  backgroundColor: Colors.red,
  child: Icon(Icons.mail),
)
```

### 5.2 自定义 Stack 实现

```dart
Stack(
  clipBehavior: Clip.none,
  children: [
    Icon(Icons.mail),
    Positioned(
      right: -6,
      top: -6,
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 6, vertical: 2),
        decoration: BoxDecoration(
          color: Colors.red,
          borderRadius: BorderRadius.circular(10),
        ),
        child: Text('5', style: TextStyle(color: Colors.white, fontSize: 10)),
      ),
    ),
  ],
)
```

## 6. 常见问题与解决方案

### 问题1：徽章位置不正确

```dart
Badge(
  offset: Offset(-4, -4),  // 调整偏移
  label: Text('5'),
  child: Icon(Icons.mail),
)
```

### 问题2：徽章被裁剪

```dart
// 确保父容器有足够空间
Padding(
  padding: EdgeInsets.all(8),
  child: Badge(
    label: Text('5'),
    child: Icon(Icons.mail),
  ),
)
```

### 问题3：数字太大

```dart
Badge(
  label: Text(count > 99 ? '99+' : '$count'),
  child: Icon(Icons.mail),
)
```

### 问题4：徽章颜色不生效

```dart
// 确保同时设置背景色和文字颜色
Badge(
  backgroundColor: Colors.red,
  textColor: Colors.white,
  label: Text('5'),
  child: Icon(Icons.mail),
)
```

## 7. 最佳实践

### 7.1 统一徽章样式

```dart
class AppBadge {
  static Badge notification({int? count}) {
    return Badge(
      label: count != null ? Text(count > 99 ? '99+' : '$count') : null,
      backgroundColor: Colors.red,
    );
  }

  static Widget withIcon(IconData icon, {int? count}) {
    return Badge(
      label: count != null ? Text(count > 99 ? '99+' : '$count') : null,
      backgroundColor: Colors.red,
      child: Icon(icon),
    );
  }

  static Widget online(Widget child) {
    return Badge(
      backgroundColor: Colors.green,
      alignment: Alignment.bottomRight,
      child: child,
    );
  }
}

// 使用
AppBadge.withIcon(Icons.mail, count: 5)
```

### 7.2 条件显示徽章

```dart
// 没有数据时不显示徽章
if (count > 0)
  Badge(
    label: Text('$count'),
    child: Icon(Icons.mail),
  )
else
  Icon(Icons.mail)
```

### 7.3 响应式大小

```dart
Badge(
  largeSize: MediaQuery.of(context).size.width > 600 ? 20 : 16,
  label: Text('5'),
  child: Icon(Icons.mail),
)
```

## 总结

Badge 是 Material 3 中简单但实用的徽章组件：

**核心要点**：
1. Material 3 标准徽章组件
2. 支持小圆点和内容两种模式
3. 可自定义位置、颜色、大小
4. 常用于通知、状态提示

**常用场景**：
- 消息未读数
- 通知提示
- 在线状态
- 新内容标记

**最佳实践**：
1. 数字超过 99 显示 99+
2. 条件显示徽章
3. 统一徽章样式
4. 合理设置位置偏移

---

*最后更新: 2026-02-18*

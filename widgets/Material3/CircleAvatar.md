# Flutter CircleAvatar 组件用法详解

## 简介

CircleAvatar 是圆形头像组件，常用于显示用户头像、图标或文字首字母。它提供了圆形裁剪和背景色支持，是 Material Design 的标准头像组件。

## 1. 初级用法

### 1.1 基本概念

CircleAvatar 的核心特点：
- 自动圆形裁剪
- 支持图片、图标、文字
- 可设置背景色和前景色
- 支持边框装饰

### 1.2 基本语法

```dart
// 最简单的用法
CircleAvatar(
  child: Text('A'),
)

// 带背景色
CircleAvatar(
  backgroundColor: Colors.blue,
  child: Text('A', style: TextStyle(color: Colors.white)),
)

// 显示网络图片
CircleAvatar(
  backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `child` | `Widget?` | - | 子组件 |
| `backgroundColor` | `Color?` | 灰色 | 背景色 |
| `backgroundImage` | `ImageProvider?` | - | 背景图片 |
| `foregroundImage` | `ImageProvider?` | - | 前景图片 |
| `radius` | `double?` | - | 半径 |
| `minRadius` | `double?` | - | 最小半径 |
| `maxRadius` | `double?` | - | 最大半径 |
| `foregroundColor` | `Color?` | 白色 | 前景色（文字/图标颜色） |
| `onBackgroundImageError` | `ImageErrorListener?` | - | 图片加载错误回调 |
| `onForegroundImageError` | `ImageErrorListener?` | - | 前景图片加载错误回调 |

### 1.4 基础示例

#### 文字头像

```dart
// 单字母头像
CircleAvatar(
  child: Text('A'),
)

// 带背景色的头像
CircleAvatar(
  backgroundColor: Colors.blue,
  child: Text('A', style: TextStyle(color: Colors.white)),
)

// 双字母头像
CircleAvatar(
  backgroundColor: Colors.purple,
  child: Text('JD', style: TextStyle(color: Colors.white, fontSize: 18)),
)
```

#### 图标头像

```dart
// 图标头像
CircleAvatar(
  child: Icon(Icons.person),
)

// 带背景色的图标头像
CircleAvatar(
  backgroundColor: Colors.green,
  child: Icon(Icons.person, color: Colors.white),
)
```

#### 图片头像

```dart
// 网络图片
CircleAvatar(
  backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
)

// 本地图片
CircleAvatar(
  backgroundImage: AssetImage('images/avatar.jpg'),
)

// 设置大小
CircleAvatar(
  radius: 40,
  backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
)
```

## 2. 中级用法

### 2.1 控制大小

```dart
Row(
  children: [
    CircleAvatar(radius: 16, child: Text('S')),
    SizedBox(width: 8),
    CircleAvatar(radius: 24, child: Text('M')),
    SizedBox(width: 8),
    CircleAvatar(radius: 32, child: Text('L')),
    SizedBox(width: 8),
    CircleAvatar(radius: 48, child: Text('XL')),
  ],
)
```

### 2.2 带边框的头像

```dart
Container(
  decoration: BoxDecoration(
    shape: BoxShape.circle,
    border: Border.all(color: Colors.blue, width: 3),
  ),
  child: CircleAvatar(
    radius: 30,
    backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
  ),
)

// 或使用两个 CircleAvatar 叠加
CircleAvatar(
  radius: 35,
  backgroundColor: Colors.blue,
  child: CircleAvatar(
    radius: 32,
    backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
  ),
)
```

### 2.3 前景图片与背景图片

```dart
CircleAvatar(
  radius: 40,
  backgroundColor: Colors.grey[200],
  backgroundImage: NetworkImage('https://example.com/background.jpg'),
  foregroundImage: NetworkImage('https://example.com/avatar.jpg'),
  onBackgroundImageError: (error, stackTrace) {
    print('背景图片加载失败: $error');
  },
)
```

### 2.4 状态指示器

```dart
Stack(
  children: [
    CircleAvatar(
      radius: 30,
      backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
    ),
    Positioned(
      right: 0,
      bottom: 0,
      child: Container(
        width: 16,
        height: 16,
        decoration: BoxDecoration(
          color: Colors.green,
          shape: BoxShape.circle,
          border: Border.all(color: Colors.white, width: 2),
        ),
      ),
    ),
  ],
)
```

### 2.5 头像组

```dart
class AvatarGroup extends StatelessWidget {
  final List<String> avatars;
  final double radius;
  final double overlap;

  const AvatarGroup({
    required this.avatars,
    this.radius = 20,
    this.overlap = 10,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: avatars.asMap().entries.map((entry) {
        return Positioned(
          left: entry.key * (radius * 2 - overlap),
          child: CircleAvatar(
            radius: radius,
            backgroundImage: NetworkImage(entry.value),
          ),
        );
      }).toList(),
    );
  }
}

// 使用
AvatarGroup(
  avatars: [
    'https://example.com/avatar1.jpg',
    'https://example.com/avatar2.jpg',
    'https://example.com/avatar3.jpg',
  ],
)
```

## 3. 高级用法

### 3.1 渐变背景头像

```dart
Container(
  width: 60,
  height: 60,
  decoration: BoxDecoration(
    shape: BoxShape.circle,
    gradient: LinearGradient(
      colors: [Colors.purple, Colors.blue],
      begin: Alignment.topLeft,
      end: Alignment.bottomRight,
    ),
  ),
  child: Center(
    child: Text('A', style: TextStyle(color: Colors.white, fontSize: 24)),
  ),
)
```

### 3.2 在线状态头像组件

```dart
class StatusAvatar extends StatelessWidget {
  final String imageUrl;
  final bool isOnline;
  final double radius;

  const StatusAvatar({
    required this.imageUrl,
    this.isOnline = false,
    this.radius = 24,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        CircleAvatar(
          radius: radius,
          backgroundImage: NetworkImage(imageUrl),
        ),
        if (isOnline)
          Positioned(
            right: 0,
            bottom: 0,
            child: Container(
              width: radius * 0.6,
              height: radius * 0.6,
              decoration: BoxDecoration(
                color: Colors.green,
                shape: BoxShape.circle,
                border: Border.all(color: Colors.white, width: 2),
              ),
            ),
          ),
      ],
    );
  }
}

// 使用
StatusAvatar(
  imageUrl: 'https://example.com/avatar.jpg',
  isOnline: true,
)
```

### 3.3 头像带徽章

```dart
class BadgeAvatar extends StatelessWidget {
  final String imageUrl;
  final String badge;
  final double radius;

  const BadgeAvatar({
    required this.imageUrl,
    required this.badge,
    this.radius = 24,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        CircleAvatar(
          radius: radius,
          backgroundImage: NetworkImage(imageUrl),
        ),
        Positioned(
          right: 0,
          bottom: 0,
          child: Container(
            padding: EdgeInsets.symmetric(horizontal: 4, vertical: 2),
            decoration: BoxDecoration(
              color: Colors.red,
              borderRadius: BorderRadius.circular(10),
              border: Border.all(color: Colors.white, width: 1),
            ),
            child: Text(
              badge,
              style: TextStyle(color: Colors.white, fontSize: 10),
            ),
          ),
        ),
      ],
    );
  }
}

// 使用
BadgeAvatar(
  imageUrl: 'https://example.com/avatar.jpg',
  badge: '99+',
)
```

### 3.4 首字母头像生成器

```dart
class InitialAvatar extends StatelessWidget {
  final String name;
  final double radius;
  final Color? backgroundColor;
  final Color? textColor;

  const InitialAvatar({
    required this.name,
    this.radius = 24,
    this.backgroundColor,
    this.textColor,
    Key? key,
  }) : super(key: key);

  Color _getAvatarColor(String name) {
    final colors = [
      Colors.red,
      Colors.blue,
      Colors.green,
      Colors.orange,
      Colors.purple,
      Colors.teal,
      Colors.pink,
      Colors.indigo,
    ];
    final index = name.isNotEmpty ? name.codeUnitAt(0) % colors.length : 0;
    return colors[index];
  }

  String _getInitials(String name) {
    final parts = name.trim().split(' ');
    if (parts.length >= 2) {
      return '${parts[0][0]}${parts[1][0]}'.toUpperCase();
    }
    return name.isNotEmpty ? name[0].toUpperCase() : '?';
  }

  @override
  Widget build(BuildContext context) {
    return CircleAvatar(
      radius: radius,
      backgroundColor: backgroundColor ?? _getAvatarColor(name),
      child: Text(
        _getInitials(name),
        style: TextStyle(
          color: textColor ?? Colors.white,
          fontSize: radius * 0.8,
          fontWeight: FontWeight.bold,
        ),
      ),
    );
  }
}

// 使用
InitialAvatar(name: 'John Doe')
InitialAvatar(name: '张三')
InitialAvatar(name: 'Alice', backgroundColor: Colors.purple)
```

### 3.5 点击放大的头像

```dart
class TappableAvatar extends StatefulWidget {
  final String imageUrl;
  final double radius;

  const TappableAvatar({
    required this.imageUrl,
    this.radius = 24,
    Key? key,
  }) : super(key: key);

  @override
  _TappableAvatarState createState() => _TappableAvatarState();
}

class _TappableAvatarState extends State<TappableAvatar> {
  bool _isExpanded = false;

  void _showFullScreenAvatar() {
    showDialog(
      context: context,
      builder: (context) => GestureDetector(
        onTap: () => Navigator.pop(context),
        child: Scaffold(
          backgroundColor: Colors.black87,
          body: Center(
            child: CircleAvatar(
              radius: 150,
              backgroundImage: NetworkImage(widget.imageUrl),
            ),
          ),
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: _showFullScreenAvatar,
      child: CircleAvatar(
        radius: widget.radius,
        backgroundImage: NetworkImage(widget.imageUrl),
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：图片变形

```dart
// 使用 backgroundImage 而不是 child + Image
// ❌ 不好
CircleAvatar(
  child: Image.network('https://example.com/avatar.jpg'),
)

// ✅ 好
CircleAvatar(
  backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
)
```

### 问题2：图片加载失败

```dart
CircleAvatar(
  backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
  onBackgroundImageError: (error, stackTrace) {
    // 处理加载失败
    print('图片加载失败: $error');
  },
  child: Text('A'),  // 作为后备显示
)
```

### 问题3：边框不居中

```dart
// 使用 Container 而不是嵌套 CircleAvatar
Container(
  padding: EdgeInsets.all(2),
  decoration: BoxDecoration(
    shape: BoxShape.circle,
    border: Border.all(color: Colors.blue, width: 3),
  ),
  child: CircleAvatar(
    radius: 28,
    backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
  ),
)
```

### 问题4：radius 与 minRadius/maxRadius 冲突

```dart
// 只使用一种方式
// 方式1：固定大小
CircleAvatar(radius: 30, child: Text('A'))

// 方式2：范围大小
CircleAvatar(minRadius: 20, maxRadius: 40, child: Text('A'))
```

## 5. 最佳实践

### 5.1 统一头像尺寸

```dart
class AvatarSizes {
  static const double small = 20;
  static const double medium = 32;
  static const double large = 48;
  static const double extraLarge = 64;
}

CircleAvatar(radius: AvatarSizes.medium, child: Text('A'))
```

### 5.2 错误处理

```dart
class SafeAvatar extends StatelessWidget {
  final String? imageUrl;
  final String fallbackText;
  final double radius;

  const SafeAvatar({
    this.imageUrl,
    required this.fallbackText,
    this.radius = 24,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return CircleAvatar(
      radius: radius,
      backgroundColor: Colors.grey[300],
      backgroundImage: imageUrl != null ? NetworkImage(imageUrl!) : null,
      onBackgroundImageError: imageUrl != null ? (_, __) {} : null,
      child: imageUrl == null
          ? Text(
              fallbackText.isNotEmpty ? fallbackText[0].toUpperCase() : '?',
              style: TextStyle(fontSize: radius * 0.8),
            )
          : null,
    );
  }
}
```

### 5.3 无障碍支持

```dart
CircleAvatar(
  child: Text('A'),
  // 配合 Semantics 使用
)
```

## 总结

CircleAvatar 是 Flutter 中显示圆形头像的标准组件：

**核心要点**：
1. 自动圆形裁剪内容
2. 支持图片、图标、文字
3. 可设置背景色和大小
4. 常用于用户头像展示

**常用属性**：
- `radius` - 半径（控制大小）
- `backgroundColor` - 背景色
- `backgroundImage` - 背景图片
- `child` - 子组件

**最佳实践**：
1. 使用 backgroundImage 显示图片
2. 提供加载失败的备用显示
3. 统一管理头像尺寸
4. 可结合 Stack 添加状态指示

---

*最后更新: 2026-02-19*

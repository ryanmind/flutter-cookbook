# Flutter Icon 组件用法详解

## 简介

Icon 是 Flutter 中用于显示图标的组件。Flutter 内置了丰富的 Material Icons 和 Cupertino Icons 图标库，同时支持自定义图标。

## 1. 初级用法

### 1.1 基本概念

Icon 的核心特点：
- 显示矢量图标
- 支持颜色、大小调整
- 内置 Material 和 Cupertino 图标库
- 支持自定义图标字体

### 1.2 基本语法

```dart
// 最简单的用法
Icon(Icons.favorite)

// 带颜色和大小
Icon(
  Icons.favorite,
  color: Colors.red,
  size: 32,
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `icon` | `IconData?` | - | 图标数据 |
| `size` | `double?` | 24.0 | 图标大小 |
| `fill` | `double?` | 0.0 | 填充程度（0-1） |
| `weight` | `double?` | - | 线条粗细 |
| `grade` | `double?` | - | 粗细等级 |
| `opticalSize` | `double?` | - | 光学尺寸 |
| `color` | `Color?` | - | 图标颜色 |
| `shadows` | `List<Shadow>?` | - | 阴影效果 |
| `semanticLabel` | `String?` | - | 语义标签 |
| `textDirection` | `TextDirection?` | - | 文本方向 |

### 1.4 基础示例

#### 基本图标

```dart
Icon(Icons.home)
Icon(Icons.settings)
Icon(Icons.person)
Icon(Icons.star)
Icon(Icons.favorite)
```

#### 图标大小

```dart
Row(
  children: [
    Icon(Icons.favorite, size: 16),
    Icon(Icons.favorite, size: 24),
    Icon(Icons.favorite, size: 32),
    Icon(Icons.favorite, size: 48),
    Icon(Icons.favorite, size: 64),
  ],
)
```

#### 图标颜色

```dart
Row(
  children: [
    Icon(Icons.favorite, color: Colors.red),
    Icon(Icons.favorite, color: Colors.blue),
    Icon(Icons.favorite, color: Colors.green),
    Icon(Icons.favorite, color: Colors.orange),
    Icon(Icons.favorite, color: Colors.purple),
  ],
)
```

## 2. Material Icons 分类

### 2.1 常用图标

| 分类 | 图标示例 |
|------|---------|
| 导航 | `Icons.home`, `Icons.arrow_back`, `Icons.menu`, `Icons.close` |
| 操作 | `Icons.add`, `Icons.edit`, `Icons.delete`, `Icons.save` |
| 通信 | `Icons.email`, `Icons.phone`, `Icons.message`, `Icons.share` |
| 文件 | `Icons.folder`, `Icons.file_copy`, `Icons.download`, `Icons.upload` |
| 媒体 | `Icons.play_arrow`, `Icons.pause`, `Icons.volume_up`, `Icons.camera` |
| 用户 | `Icons.person`, `Icons.group`, `Icons.account_circle`, `Icons.login` |
| 设置 | `Icons.settings`, `Icons.build`, `Icons.tune`, `Icons.search` |
| 状态 | `Icons.check`, `Icons.error`, `Icons.warning`, `Icons.info` |

### 2.2 使用示例

```dart
// 导航图标
Icon(Icons.home)
Icon(Icons.arrow_back)
Icon(Icons.arrow_forward)
Icon(Icons.menu)
Icon(Icons.close)

// 操作图标
Icon(Icons.add)
Icon(Icons.remove)
Icon(Icons.edit)
Icon(Icons.delete)
Icon(Icons.save)

// 状态图标
Icon(Icons.check_circle, color: Colors.green)
Icon(Icons.error, color: Colors.red)
Icon(Icons.warning, color: Colors.orange)
Icon(Icons.info, color: Colors.blue)
```

## 3. Cupertino Icons

### 3.1 iOS 风格图标

```dart
// 需要添加依赖：cupertino_icons
Icon(CupertinoIcons.home)
Icon(CupertinoIcons.settings)
Icon(CupertinoIcons.person)
Icon(CupertinoIcons.heart)
Icon(CupertinoIcons.star)
```

### 3.2 常用 Cupertino 图标

```dart
// 导航
Icon(CupertinoIcons.back)
Icon(CupertinoIcons.forward)
Icon(CupertinoIcons.home)

// 操作
Icon(CupertinoIcons.add)
Icon(CupertinoIcons.minus)
Icon(CupertinoIcons.pencil)
Icon(CupertinoIcons.trash)

// 通信
Icon(CupertinoIcons.mail)
Icon(CupertinoIcons.phone)
Icon(CupertinoIcons.chat_bubble)
```

## 4. 中级用法

### 4.1 图标阴影

```dart
Icon(
  Icons.favorite,
  size: 48,
  shadows: [
    Shadow(
      color: Colors.black26,
      offset: Offset(2, 2),
      blurRadius: 4,
    ),
  ],
)
```

### 4.2 图标按钮组合

```dart
// IconButton 会自动处理点击效果
IconButton(
  onPressed: () {},
  icon: Icon(Icons.favorite),
  iconSize: 32,
  color: Colors.red,
)

// 带标签的图标
Column(
  mainAxisSize: MainAxisSize.min,
  children: [
    Icon(Icons.home, size: 32),
    SizedBox(height: 4),
    Text('首页'),
  ],
)
```

### 4.3 条件图标

```dart
Icon(
  isSelected ? Icons.star : Icons.star_border,
  color: isSelected ? Colors.amber : Colors.grey,
)
```

### 4.4 图标填充程度

```dart
// fill 控制图标填充程度（0-1）
Icon(Icons.favorite, fill: 0)    // 仅轮廓
Icon(Icons.favorite, fill: 0.5)  // 半填充
Icon(Icons.favorite, fill: 1)    // 完全填充
```

### 4.5 圆形背景图标

```dart
Container(
  padding: EdgeInsets.all(8),
  decoration: BoxDecoration(
    color: Colors.blue,
    shape: BoxShape.circle,
  ),
  child: Icon(Icons.add, color: Colors.white, size: 24),
)

// 使用 CircleAvatar
CircleAvatar(
  backgroundColor: Colors.blue,
  child: Icon(Icons.person, color: Colors.white),
)
```

## 5. 高级用法

### 5.1 自定义图标字体

```dart
// 1. 在 pubspec.yaml 中声明字体
// flutter:
//   fonts:
//     - family: MyIcons
//       fonts:
//         - asset: fonts/MyIcons.ttf

// 2. 使用自定义图标
Icon(
  IconData(0xe900, fontFamily: 'MyIcons'),
  size: 32,
  color: Colors.blue,
)
```

### 5.2 图标生成器

```dart
class IconGenerator {
  static IconData fromCodePoint(int codePoint, {String fontFamily = 'MaterialIcons'}) {
    return IconData(codePoint, fontFamily: fontFamily);
  }
}

Icon(IconGenerator.fromCodePoint(0xe87d))
```

### 5.3 渐变图标

```dart
class GradientIcon extends StatelessWidget {
  final IconData icon;
  final double size;
  final List<Color> colors;

  const GradientIcon({
    required this.icon,
    this.size = 24,
    this.colors = const [Colors.blue, Colors.purple],
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ShaderMask(
      shaderCallback: (bounds) => LinearGradient(
        colors: colors,
      ).createShader(bounds),
      child: Icon(
        icon,
        size: size,
        color: Colors.white,
      ),
    );
  }
}

// 使用
GradientIcon(
  icon: Icons.favorite,
  size: 48,
  colors: [Colors.red, Colors.pink],
)
```

### 5.4 动画图标

```dart
class AnimatedIconWidget extends StatefulWidget {
  final IconData icon;
  final double size;
  final Color color;

  const AnimatedIconWidget({
    required this.icon,
    this.size = 24,
    this.color = Colors.blue,
    Key? key,
  }) : super(key: key);

  @override
  _AnimatedIconWidgetState createState() => _AnimatedIconWidgetState();
}

class _AnimatedIconWidgetState extends State<AnimatedIconWidget>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    )..repeat(reverse: true);
    _animation = Tween<double>(begin: 0.8, end: 1.2).animate(_controller);
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
      child: Icon(
        widget.icon,
        size: widget.size,
        color: widget.color,
      ),
    );
  }
}
```

### 5.5 ImageIcon 图片图标

```dart
// 从图片资源创建图标
ImageIcon(
  AssetImage('images/custom_icon.png'),
  size: 32,
  color: Colors.blue,
)

// 从网络图片创建
ImageIcon(
  NetworkImage('https://example.com/icon.png'),
  size: 32,
  color: Colors.red,
)
```

## 6. 常见问题与解决方案

### 问题1：图标颜色不生效

```dart
// 检查是否被父组件主题影响
Icon(
  Icons.favorite,
  color: Colors.red,  // 确保设置了 color
)
```

### 问题2：图标模糊

```dart
// 确保使用合适的尺寸
Icon(
  Icons.favorite,
  size: 24,  // 使用标准尺寸
)
```

### 问题3：找不到图标

```dart
// 检查图标是否存在
// Material Icons: https://fonts.google.com/icons
// Cupertino Icons: https://cupertino_icons.web.app
```

### 问题4：自定义图标不显示

```dart
// 确保 pubspec.yaml 配置正确
// flutter:
//   fonts:
//     - family: MyIcons
//       fonts:
//         - asset: fonts/MyIcons.ttf

// 确保 codePoint 正确
Icon(IconData(0xe900, fontFamily: 'MyIcons'))
```

## 7. 最佳实践

### 7.1 统一图标管理

```dart
class AppIcons {
  // 导航
  static const home = Icons.home;
  static const settings = Icons.settings;
  static const profile = Icons.person;
  
  // 操作
  static const add = Icons.add;
  static const edit = Icons.edit;
  static const delete = Icons.delete;
  
  // 状态
  static const success = Icons.check_circle;
  static const error = Icons.error;
  static const warning = Icons.warning;
  static const info = Icons.info;
}

Icon(AppIcons.home)
Icon(AppIcons.success, color: Colors.green)
```

### 7.2 语义化标签

```dart
Icon(
  Icons.favorite,
  semanticLabel: '收藏',  // 为无障碍功能提供标签
)
```

### 7.3 响应式图标大小

```dart
Icon(
  Icons.favorite,
  size: MediaQuery.of(context).size.width > 600 ? 32 : 24,
)
```

## 8. 图标查找资源

- **Material Icons**: https://fonts.google.com/icons
- **Cupertino Icons**: https://cupertino_icons.web.app
- **Flutter Icons 查找**: https://api.flutter.dev/flutter/material/Icons-class.html

## 总结

Icon 是 Flutter 中显示图标的核心组件：

**核心要点**：
1. 使用 `Icons.xxx` 访问 Material 图标
2. 使用 `CupertinoIcons.xxx` 访问 iOS 风格图标
3. 支持大小、颜色、阴影等自定义
4. 可通过自定义字体扩展

**常用属性**：
- `icon` - IconData 图标数据
- `size` - 图标大小（默认 24）
- `color` - 图标颜色
- `semanticLabel` - 语义标签

**最佳实践**：
1. 统一管理图标常量
2. 为无障碍添加语义标签
3. 使用合适的图标尺寸
4. 保持图标风格一致

---

*最后更新: 2026-02-19*

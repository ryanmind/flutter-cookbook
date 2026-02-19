# Flutter Hero 组件用法详解

## 简介

Hero 是 Flutter 中用于实现页面间共享元素动画的组件。当页面跳转时，Hero 组件会产生平滑的过渡动画效果，提升用户体验。

## 1. 初级用法

### 1.1 基本概念

Hero 的作用：
- 页面间共享元素动画
- 自动计算过渡动画
- 支持自定义动画效果
- 通过 tag 标识配对

### 1.2 基本语法

```dart
// 页面 A
Hero(
  tag: 'image-hero',
  child: Image.network('url'),
)

// 页面 B
Hero(
  tag: 'image-hero',  // 相同的 tag
  child: Image.network('url'),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `tag` | `Object` | 唯一标识，用于配对 |
| `child` | `Widget` | 子组件 |
| `flightShuttleBuilder` | `FlightShuttleBuilder?` | 飞行中组件构建器 |
| `placeholderBuilder` | `PlaceholderBuilder?` | 占位组件构建器 |
| `transitionOnUserGestures` | `bool` | 是否支持手势返回动画 |

### 1.4 基础示例

#### 图片详情页

```dart
// 列表页
class ListPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('列表')),
      body: GridView.builder(
        gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 2,
        ),
        itemCount: 10,
        itemBuilder: (context, index) {
          return GestureDetector(
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => DetailPage(imageIndex: index),
                ),
              );
            },
            child: Hero(
              tag: 'image-$index',
              child: Image.network(
                'https://picsum.photos/200?random=$index',
                fit: BoxFit.cover,
              ),
            ),
          );
        },
      ),
    );
  }
}

// 详情页
class DetailPage extends StatelessWidget {
  final int imageIndex;

  const DetailPage({required this.imageIndex});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('详情')),
      body: Center(
        child: Hero(
          tag: 'image-$imageIndex',
          child: Image.network(
            'https://picsum.photos/400?random=$imageIndex',
          ),
        ),
      ),
    );
  }
}
```

## 2. 中级用法

### 2.1 自定义飞行组件

```dart
Hero(
  tag: 'custom-hero',
  flightShuttleBuilder: (flightContext, animation, flightDirection,
      fromHeroContext, toHeroContext) {
    return AnimatedBuilder(
      animation: animation,
      builder: (context, child) {
        return Opacity(
          opacity: animation.value,
          child: Transform.scale(
            scale: 1 + animation.value * 0.5,
            child: child,
          ),
        );
      },
      child: toHeroContext.widget,
    );
  },
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 2.2 占位组件

```dart
Hero(
  tag: 'placeholder-hero',
  placeholderBuilder: (context, heroSize, child) {
    return Opacity(
      opacity: 0.5,
      child: child,
    );
  },
  child: Container(
    width: 100,
    height: 100,
    color: Colors.green,
  ),
)
```

### 2.3 常见布局场景

#### 卡片详情

```dart
// 卡片列表
class CardListPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: ListView.builder(
        itemCount: 10,
        itemBuilder: (context, index) {
          return GestureDetector(
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => CardDetailPage(cardIndex: index),
                ),
              );
            },
            child: Hero(
              tag: 'card-$index',
              child: Card(
                child: Padding(
                  padding: EdgeInsets.all(16),
                  child: Text('卡片 $index'),
                ),
              ),
            ),
          );
        },
      ),
    );
  }
}

// 卡片详情
class CardDetailPage extends StatelessWidget {
  final int cardIndex;

  const CardDetailPage({required this.cardIndex});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Hero(
        tag: 'card-$cardIndex',
        child: Card(
          margin: EdgeInsets.all(16),
          child: Padding(
            padding: EdgeInsets.all(24),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                Text('卡片 $cardIndex 详情'),
                SizedBox(height: 16),
                Text('这是卡片的详细内容'),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

#### 头像详情

```dart
class AvatarDetailExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: ListView(
        children: List.generate(5, (index) {
          return ListTile(
            leading: GestureDetector(
              onTap: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(
                    builder: (context) => AvatarDetailPage(userIndex: index),
                  ),
                );
              },
              child: Hero(
                tag: 'avatar-$index',
                child: CircleAvatar(
                  backgroundImage: NetworkImage(
                    'https://i.pravatar.cc/100?img=$index',
                  ),
                ),
              ),
            ),
            title: Text('用户 $index'),
          );
        }),
      ),
    );
  }
}

class AvatarDetailPage extends StatelessWidget {
  final int userIndex;

  const AvatarDetailPage({required this.userIndex});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('用户详情')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Hero(
              tag: 'avatar-$userIndex',
              child: CircleAvatar(
                radius: 60,
                backgroundImage: NetworkImage(
                  'https://i.pravatar.cc/200?img=$userIndex',
                ),
              ),
            ),
            SizedBox(height: 16),
            Text('用户 $userIndex', style: TextStyle(fontSize: 24)),
          ],
        ),
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 多个 Hero 动画

```dart
// 多个元素可以同时进行 Hero 动画
class MultiHeroPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GestureDetector(
        onTap: () {
          Navigator.push(
            context,
            MaterialPageRoute(builder: (context) => MultiHeroDetailPage()),
          );
        },
        child: Row(
          children: [
            Hero(
              tag: 'hero-1',
              child: Container(width: 50, height: 50, color: Colors.red),
            ),
            Hero(
              tag: 'hero-2',
              child: Container(width: 50, height: 50, color: Colors.green),
            ),
            Hero(
              tag: 'hero-3',
              child: Container(width: 50, height: 50, color: Colors.blue),
            ),
          ],
        ),
      ),
    );
  }
}

class MultiHeroDetailPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: [
          Hero(
            tag: 'hero-1',
            child: Container(width: 100, height: 100, color: Colors.red),
          ),
          Hero(
            tag: 'hero-2',
            child: Container(width: 100, height: 100, color: Colors.green),
          ),
          Hero(
            tag: 'hero-3',
            child: Container(width: 100, height: 100, color: Colors.blue),
          ),
        ],
      ),
    );
  }
}
```

### 3.2 Radial Hero 动画

```dart
class RadialHeroExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: GestureDetector(
          onTap: () {
            Navigator.push(
              context,
              MaterialPageRoute(
                builder: (context) => RadialHeroDetailPage(),
              ),
            );
          },
          child: Hero(
            tag: 'radial-hero',
            createRectTween: (begin, end) {
              return MaterialRectArcTween(begin: begin, end: end);
            },
            child: Container(
              width: 100,
              height: 100,
              decoration: BoxDecoration(
                shape: BoxShape.circle,
                color: Colors.blue,
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

### 3.3 手势返回动画

```dart
Hero(
  tag: 'gesture-hero',
  transitionOnUserGestures: true,  // 启用手势返回动画
  child: Image.network('https://picsum.photos/200'),
)
```

### 3.4 自定义 RectTween

```dart
Hero(
  tag: 'custom-tween',
  createRectTween: (begin, end) {
    return MaterialRectCenterArcTween(begin: begin, end: end);
  },
  child: Container(
    width: 100,
    height: 100,
    color: Colors.purple,
  ),
)
```

## 4. 常见问题与解决方案

### 问题1：Hero 动画不执行

```dart
// 问题：tag 不匹配
// 页面 A
Hero(tag: 'hero-1', ...)
// 页面 B
Hero(tag: 'hero-2', ...)  // 不匹配

// 解决：确保 tag 完全相同
// 页面 A
Hero(tag: 'hero-1', ...)
// 页面 B
Hero(tag: 'hero-1', ...)  // 匹配
```

### 问题2：动画方向错误

```dart
// 问题：Hero 动画方向与预期不符
// 原因：Hero 会自动计算飞行路径

// 解决：检查页面结构和布局方向
// 确保 Hero 在两个页面中的相对位置合理
```

### 问题3：性能问题

```dart
// 问题：复杂组件导致动画卡顿
Hero(
  tag: 'complex',
  child: VeryComplexWidget(),  // 复杂组件
)

// 解决：在飞行中使用简化版本
Hero(
  tag: 'complex',
  flightShuttleBuilder: (context, animation, direction, fromContext, toContext) {
    // 使用简化的飞行组件
    return Container(color: Colors.blue);
  },
  child: VeryComplexWidget(),
)
```

## 5. Hero vs 其他动画方案对比

| 方案 | 用途 | 特点 |
|------|------|------|
| **Hero** | 页面间共享元素 | 自动处理过渡 |
| **AnimatedContainer** | 属性动画 | 手动控制 |
| **TweenAnimationBuilder** | 自定义动画 | 灵活但复杂 |
| **PageRouteBuilder** | 页面过渡 | 整体页面动画 |

### 选择建议

```dart
// 页面间共享元素 → Hero
Hero(tag: 'image', child: Image.network(...))

// 单页面属性动画 → AnimatedContainer
AnimatedContainer(duration: ..., color: color)

// 自定义页面过渡 → PageRouteBuilder
PageRouteBuilder(
  pageBuilder: ...,
  transitionsBuilder: ...,
)
```

## 6. 最佳实践

### 6.1 统一 tag 管理

```dart
class HeroTags {
  static String image(int id) => 'image-$id';
  static String avatar(int id) => 'avatar-$id';
  static String card(int id) => 'card-$id';
}

// 使用
Hero(tag: HeroTags.image(1), ...)
```

### 6.2 简化飞行组件

```dart
Hero(
  tag: 'optimized',
  flightShuttleBuilder: (context, animation, direction, fromContext, toContext) {
    // 飞行时使用简化版本
    return const ColorFiltered(
      colorFilter: ColorFilter.mode(Colors.grey, BlendMode.saturation),
      child: Placeholder(),
    );
  },
  child: ComplexWidget(),
)
```

### 6.3 调试技巧

```dart
// 使用 HeroController 监听动画
class MyHeroController extends HeroController {
  @override
  void startFlight() {
    super.startFlight();
    debugPrint('Hero 动画开始');
  }
}
```

## 总结

Hero 是 Flutter 中实现页面间共享元素动画的核心组件：

**核心要点**：
1. 通过 tag 配对两个页面的 Hero
2. 自动计算过渡动画
3. 支持自定义飞行组件

**最佳实践**：
1. 统一管理 tag
2. 简化飞行组件提升性能
3. 复杂场景使用 flightShuttleBuilder

**常见场景**：
- 图片详情
- 卡片详情
- 头像详情
- 列表项详情

---

*最后更新: 2026-02-19*

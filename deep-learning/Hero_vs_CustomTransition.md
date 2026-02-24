# Hero vs Custom Transition 对比学习

本文档记录 Flutter Hero 动画与 UIKit UIViewControllerAnimatedTransitioning 的对比学习要点。

---

## 1. Hero 核心概念

### Hero 是什么

Hero 是 Flutter 中的共享元素过渡动画组件，可以在页面间实现元素的平滑过渡，类似于 iOS 的共享元素转场。

- 页面间共享元素动画
- 自动处理位置、大小变化
- 简单易用，只需设置 tag
- 支持自定义飞行路径

### Hero vs Custom Transition 概念对比

| Flutter Hero | UIKit UIViewControllerAnimatedTransitioning | 说明 |
|--------------|-------------------------------------------|------|
| `Hero(tag:)` | 共享元素标记 | 元素标识 |
| `tag` | 无直接对应 | 匹配标识 |
| `flightShuttleBuilder` | 自定义动画视图 | 飞行动画 |
| `placeholderBuilder` | 占位视图 | 源/目标占位 |
| `createRectTween` | 动画路径 | 插值路径 |
| `Navigator.push()` | `present(_:animated:)` | 页面跳转 |

---

## 2. 源码分析

### Hero 类定义

```dart
class Hero extends StatefulWidget {
  const Hero({
    super.key,
    required this.tag,              // 标识（必需）
    this.createRectTween,           // Rect 插值
    this.flightShuttleBuilder,      // 飞行组件构建器
    this.placeholderBuilder,        // 占位组件构建器
    this.transitionOnUserGestures = false,  // 手势返回时动画
    this.flightShuttleBuilder,      // 飞行中的组件
    required this.child,            // 子组件
  });
}

// HeroController - 管理 Hero 动画
class HeroController {
  // 查找匹配的 Hero 对象
  Map<Object, _HeroState> _getHeroes([...]);
  
  // 启动 Hero 动画
  void _startHeroAnimation([...]);
}
```

### Hero 动画流程

```
1. Navigator.push() 触发
2. HeroController 查找匹配 tag 的 Hero
3. 创建 Overlay 层用于绘制飞行的 Hero
4. 使用 RectTween 插值位置和大小
5. 动画完成后移除 Overlay
6. 目标页面显示 Hero
```

---

## 3. 与 Custom Transition 详细对比

### 3.1 基础 Hero 动画

```objc
// Objective-C - UIViewControllerAnimatedTransitioning
// 需要大量代码实现共享元素过渡

@interface SharedElementTransition : NSObject <UIViewControllerAnimatedTransitioning>
@property (nonatomic, strong) UIView *sourceView;
@property (nonatomic, strong) UIView *destinationView;
@end

@implementation SharedElementTransition

- (NSTimeInterval)transitionDuration:(id<UIViewControllerContextTransitioning>)transitionContext {
    return 0.3;
}

- (void)animateTransition:(id<UIViewControllerContextTransitioning>)transitionContext {
    UIViewController *fromVC = [transitionContext viewControllerForKey:UITransitionContextFromViewControllerKey];
    UIViewController *toVC = [transitionContext viewControllerForKey:UITransitionContextToViewControllerKey];
    
    UIView *containerView = transitionContext.containerView;
    
    // 创建快照
    UIView *snapshot = [self.sourceView snapshotViewAfterScreenUpdates:NO];
    snapshot.frame = [self.sourceView.superview convertRect:self.sourceView.frame toView:containerView];
    [containerView addSubview:snapshot];
    
    // 隐藏源视图
    self.sourceView.hidden = YES;
    
    // 添加目标视图
    toVC.view.frame = [transitionContext finalFrameForViewController:toVC];
    [containerView addSubview:toVC.view];
    toVC.view.alpha = 0;
    
    // 获取目标位置
    [toVC.view layoutIfNeeded];
    CGRect destFrame = [self.destinationView.superview convertRect:self.destinationView.frame toView:containerView];
    self.destinationView.hidden = YES;
    
    // 动画
    [UIView animateWithDuration:[self transitionDuration:transitionContext] animations:^{
        snapshot.frame = destFrame;
        toVC.view.alpha = 1;
    } completion:^(BOOL finished) {
        self.sourceView.hidden = NO;
        self.destinationView.hidden = NO;
        [snapshot removeFromSuperview];
        [transitionContext completeTransition:![transitionContext transitionWasCancelled]];
    }];
}
@end
```

```dart
// Flutter - Hero 只需设置 tag
// 页面 1
Hero(
  tag: 'image-hero',
  child: Image.asset('photo.jpg'),
)

// 页面 2
Hero(
  tag: 'image-hero',  // 相同的 tag
  child: Image.asset('photo.jpg'),
)

// 导航
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => DetailPage()),
);
// Hero 动画自动执行！
```

### 3.2 自定义飞行路径

```objc
// Objective-C - 需要自定义插值逻辑
// 使用 CADisplayLink 或 UIViewAnimationWithKeyframes
```

```dart
// Flutter - createRectTween
Hero(
  tag: 'custom-hero',
  createRectTween: (begin, end) {
    return MaterialRectArcTween(begin: begin, end: end);  // 弧线路径
    // return RectTween(begin: begin, end: end);  // 直线路径
  },
  child: Container(...),
)
```

### 3.3 自定义飞行组件

```dart
// Flutter - flightShuttleBuilder
Hero(
  tag: 'shuttle-hero',
  flightShuttleBuilder: (flightContext, animation, flightDirection, fromHeroContext, toHeroContext) {
    // 返回飞行中的组件
    return AnimatedBuilder(
      animation: animation,
      builder: (context, child) {
        return Opacity(
          opacity: animation.value,
          child: Container(
            decoration: BoxDecoration(
              boxShadow: [BoxShadow(blurRadius: 8 * animation.value)],
            ),
            child: toHeroContext.widget,
          ),
        );
      },
    );
  },
  child: Image.asset('photo.jpg'),
)
```

### 3.4 占位组件

```dart
// Flutter - placeholderBuilder
Hero(
  tag: 'placeholder-hero',
  placeholderBuilder: (context, heroSize, child) {
    return Container(
      width: heroSize.width,
      height: heroSize.height,
      color: Colors.grey,  // 占位颜色
      child: child,
    );
  },
  child: Image.asset('photo.jpg'),
)
```

### 3.5 手势返回动画

```dart
// iOS 风格的手势返回会自动包含 Hero 动画
// transitionOnUserGestures 默认为 true

Hero(
  tag: 'gesture-hero',
  transitionOnUserGestures: true,  // 手势返回时也动画
  child: Image.asset('photo.jpg'),
)

// 配合 CupertinoPageRoute
Navigator.push(
  context,
  CupertinoPageRoute(builder: (context) => DetailPage()),
);
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 图片列表 → 详情页的共享元素过渡
// 需要大量代码...

@interface CollectionViewController : UICollectionViewController
@end

@implementation CollectionViewController

- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath {
    DetailViewController *detailVC = [[DetailViewController alloc] init];
    detailVC.transitioningDelegate = self;
    detailVC.imageIndex = indexPath.item;
    [self presentViewController:detailVC animated:YES completion:nil];
}

#pragma mark - UIViewControllerTransitioningDelegate

- (id<UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented presentingController:(UIViewController *)presenting sourceController:(UIViewController *)source {
    SharedElementTransition *transition = [[SharedElementTransition alloc] init];
    // 设置 sourceView 和 destinationView...
    return transition;
}

- (id<UIViewControllerAnimatedTransitioning>)animationControllerForDismissedController:(UIViewController *)dismissed {
    // 返回动画...
}
@end
```

### Flutter 实现

```dart
// 图片列表 → 详情页 - 极简
class ImageListPage extends StatelessWidget {
  final images = List.generate(20, (i) => 'https://picsum.photos/200?random=$i');

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Gallery')),
      body: GridView.builder(
        gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 3,
          crossAxisSpacing: 2,
          mainAxisSpacing: 2,
        ),
        itemCount: images.length,
        itemBuilder: (context, index) {
          return GestureDetector(
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => ImageDetailPage(
                    imageUrl: images[index],
                    tag: 'image-$index',
                  ),
                ),
              );
            },
            child: Hero(
              tag: 'image-$index',
              child: Image.network(images[index], fit: BoxFit.cover),
            ),
          );
        },
      ),
    );
  }
}

class ImageDetailPage extends StatelessWidget {
  final String imageUrl;
  final String tag;

  const ImageDetailPage({required this.imageUrl, required this.tag});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.black,
      appBar: AppBar(backgroundColor: Colors.transparent),
      body: Center(
        child: Hero(
          tag: tag,  // 相同的 tag
          child: Image.network(imageUrl, fit: BoxFit.contain),
        ),
      ),
    );
  }
}
```

---

## 5. Hero 动画效果

```
Hero 动画过程：

源页面：
┌─────────────────────────┐
│  ┌─────┐                │
│  │     │ ← Hero 元素    │
│  │     │                │
│  └─────┘                │
│                         │
└─────────────────────────┘

过渡中（飞行）：
┌─────────────────────────┐
│                         │
│       ┌────────┐        │
│       │        │ ← Hero │
│       │ Flying │   飞行 │
│       │        │        │
│       └────────┘        │
│                         │
└─────────────────────────┘

目标页面：
┌─────────────────────────┐
│                         │
│   ┌───────────────┐     │
│   │               │     │
│   │    Hero       │ ← 到达
│   │               │     │
│   └───────────────┘     │
│                         │
└─────────────────────────┘
```

---

## 6. 常见陷阱

### 陷阱1：tag 不唯一

```dart
// ❌ 错误：同一页面有多个相同 tag 的 Hero
Column(
  children: [
    Hero(tag: 'image', child: Image.asset('1.jpg')),
    Hero(tag: 'image', child: Image.asset('2.jpg')),  // 冲突！
  ],
)

// ✅ 正确：使用唯一 tag
Hero(tag: 'image-1', child: Image.asset('1.jpg')),
Hero(tag: 'image-2', child: Image.asset('2.jpg')),
```

### 陷阱2：目标页面没有匹配的 Hero

```dart
// ❌ 错误：目标页面没有对应 tag 的 Hero
// 源页面
Hero(tag: 'image', child: ...)

// 目标页面
Image.asset('photo.jpg')  // 没有 Hero 包装

// 结果：没有 Hero 动画，普通页面转场
```

### 陷阱3：Hero 尺寸差距过大

```dart
// ⚠️ 源 Hero 和目标 Hero 尺寸差距过大时
// 可能出现变形或跳动

// ✅ 使用 flightShuttleBuilder 控制过渡效果
Hero(
  tag: 'image',
  flightShuttleBuilder: (context, animation, direction, fromContext, toContext) {
    return AnimatedBuilder(
      animation: animation,
      builder: (context, child) {
        // 自定义过渡效果
        return ClipRRect(
          borderRadius: BorderRadius.circular(animation.value * 16),
          child: toContext.widget,
        );
      },
    );
  },
  child: Image.asset('photo.jpg'),
)
```

### 陷阱4：嵌套导航

```dart
// ⚠️ 在嵌套导航中 Hero 可能找不到
// 确保 Hero 在 Navigator 之下
```

---

## 7. 学习技巧

### 技巧1：列表中的 Hero

```dart
// 使用索引生成唯一 tag
GridView.builder(
  itemBuilder: (context, index) {
    return Hero(
      tag: 'item-$index',
      child: ItemWidget(item: items[index]),
    );
  },
)

// 详情页接收索引
Hero(
  tag: 'item-$index',
  child: DetailWidget(item: items[index]),
)
```

### 技巧2：多个共享元素

```dart
// 多个 Hero 同时动画
// 源页面
Column(
  children: [
    Hero(tag: 'avatar', child: Avatar()),
    Hero(tag: 'name', child: Text('Name')),
  ],
)

// 目标页面
Column(
  children: [
    Hero(tag: 'avatar', child: Avatar()),
    Hero(tag: 'name', child: Text('Name')),
  ],
)
// 两个元素同时飞行！
```

### 技巧3：圆角过渡

```dart
Hero(
  tag: 'rounded',
  flightShuttleBuilder: (context, animation, direction, fromContext, toContext) {
    return AnimatedBuilder(
      animation: animation,
      builder: (context, child) {
        return ClipRRect(
          borderRadius: BorderRadius.circular(
            direction == HeroFlightDirection.push
              ? animation.value * 16
              : (1 - animation.value) * 16,
          ),
          child: child,
        );
      },
      child: toContext.widget,
    );
  },
  child: ClipRRect(
    borderRadius: BorderRadius.circular(8),
    child: Image.asset('photo.jpg'),
  ),
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UIViewControllerAnimatedTransitioning  → Hero
自定义过渡代理                          → 自动匹配 tag
快照视图                               → 自动处理
frame 动画                             → RectTween
CADisplayLink                          → AnimatedBuilder
大量代码                               → 几行代码
```

### 关键差异

| 方面 | UIKit Custom Transition | Flutter Hero |
|------|------------------------|--------------|
| **代码量** | 几百行 | 几行 |
| **匹配方式** | 手动设置 | 自动 tag 匹配 |
| **快照处理** | 手动创建 | 自动处理 |
| **位置计算** | 手动计算 | 自动插值 |
| **手势返回** | 需要额外处理 | 自动支持 |

### 最佳实践

1. **使用唯一 tag** - 避免冲突
2. **保持组件相似** - 视觉过渡更平滑
3. **flightShuttleBuilder** - 自定义飞行效果
4. **配合手势返回** - 使用 CupertinoPageRoute

---

*最后更新: 2026-02-24*

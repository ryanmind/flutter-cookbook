# PageView vs UIPageViewController 对比学习

本文档记录 Flutter PageView 组件与 UIKit UIPageViewController 的对比学习要点。

---

## 1. PageView 核心概念

### PageView 是什么

PageView 是 Flutter 中用于分页滚动的组件，类似于 UIKit 的 UIPageViewController。

- 支持水平/垂直分页
- 支持滑动切换和程序控制切换
- 支持页面指示器配合
- 支持无限滚动

### PageView vs UIPageViewController 概念对比

| Flutter PageView | UIKit UIPageViewController | 说明 |
|------------------|---------------------------|------|
| `PageView()` | `UIPageViewController` | 基础分页 |
| `PageView.builder()` | `dataSource` | 按需构建 |
| `controller` | `dataSource` + `delegate` | 控制器 |
| `onPageChanged` | `pageViewController:didFinishAnimating` | 页面变化回调 |
| `physics: NeverScrollableScrollPhysics()` | 禁用手势 | 禁用滑动 |
| `PageController.jumpToPage()` | `setViewControllers:` | 跳转页面 |
| `PageController.animateToPage()` | 无内置动画跳转 | 动画跳转 |

---

## 2. 源码分析

### PageView 类定义

```dart
class PageView extends StatefulWidget {
  const PageView({
    super.key,
    this.scrollDirection = Axis.horizontal,  // 滚动方向
    this.reverse = false,                    // 反向
    this.controller,                         // 控制器
    this.physics,                            // 滚动物理
    this.pageSnapping = true,                // 页面吸附
    this.onPageChanged,                      // 页面变化回调
    List<Widget> children = const <Widget>[], // 子组件列表
  });
  
  // Builder 模式
  PageView.builder({
    required IndexedWidgetBuilder itemBuilder,  // 构建器
    int? itemCount,                            // 数量
    // ...
  });
  
  // 自定义布局
  PageView.custom({
    required SliverChildDelegate childrenDelegate,
    // ...
  });
}
```

### PageController

```dart
class PageController extends ScrollController {
  PageController({
    this.initialPage = 0,              // 初始页面
    this.keepPage = true,              // 保持页面状态
    this.viewportFraction = 1.0,       // 视口比例（用于显示部分相邻页面）
  });
  
  // 跳转到指定页面
  void jumpToPage(int page);
  
  // 动画跳转
  Future<void> animateToPage(
    int page, {
    required Duration duration,
    required Curve curve,
  });
  
  // 跳转到下一页
  void nextPage({required Duration duration, required Curve curve});
  
  // 跳转到上一页
  void previousPage({required Duration duration, required Curve curve});
  
  // 当前页面索引
  int? get page;
}
```

---

## 3. 与 UIPageViewController 详细对比

### 3.1 基础分页

```objc
// Objective-C - UIPageViewController
UIPageViewController *pageVC = [[UIPageViewController alloc] 
    initWithTransitionStyle:UIPageViewControllerTransitionStyleScroll
      navigationOrientation:UIPageViewControllerNavigationOrientationHorizontal
                    options:nil];
pageVC.dataSource = self;
pageVC.delegate = self;

// 设置初始页面
UIViewController *firstVC = [self viewControllerAtIndex:0];
[pageVC setViewControllers:@[firstVC] direction:UIPageViewControllerNavigationDirectionForward animated:NO completion:nil];
```

```dart
// Flutter - PageView
PageView(
  children: [
    Container(color: Colors.red, child: Center(child: Text('Page 1'))),
    Container(color: Colors.green, child: Center(child: Text('Page 2'))),
    Container(color: Colors.blue, child: Center(child: Text('Page 3'))),
  ],
)
```

### 3.2 使用 Controller 控制页面

```objc
// Objective-C - 切换页面
- (void)goToPage:(NSInteger)index {
    UIViewController *vc = [self viewControllerAtIndex:index];
    [self.pageViewController setViewControllers:@[vc]
                                     direction:UIPageViewControllerNavigationDirectionForward
                                      animated:YES
                                    completion:nil];
}
```

```dart
// Flutter - 使用 PageController
final controller = PageController(initialPage: 0);

// 跳转到指定页面
controller.jumpToPage(2);

// 动画跳转
controller.animateToPage(
  2,
  duration: Duration(milliseconds: 300),
  curve: Curves.easeInOut,
);

// 下一页/上一页
controller.nextPage(duration: Duration(milliseconds: 300), curve: Curves.ease);
controller.previousPage(duration: Duration(milliseconds: 300), curve: Curves.ease);
```

### 3.3 页面变化监听

```objc
// Objective-C - delegate 方法
- (void)pageViewController:(UIPageViewController *)pageViewController 
        didFinishAnimating:(BOOL)finished 
   previousViewControllers:(NSArray<UIViewController *> *)previousViewControllers 
       transitionCompleted:(BOOL)completed {
    if (completed) {
        UIViewController *currentVC = pageViewController.viewControllers.firstObject;
        NSUInteger index = [self indexOfViewController:currentVC];
        // 更新页面指示器
        self.pageControl.currentPage = index;
    }
}
```

```dart
// Flutter - onPageChanged
PageView(
  onPageChanged: (index) {
    print('Current page: $index');
    // 更新页面指示器
    setState(() => _currentPage = index);
  },
  children: [...],
)
```

### 3.4 垂直分页

```objc
// Objective-C
UIPageViewController *pageVC = [[UIPageViewController alloc] 
    initWithTransitionStyle:UIPageViewControllerTransitionStyleScroll
      navigationOrientation:UIPageViewControllerNavigationOrientationVertical
                    options:nil];
```

```dart
// Flutter
PageView(
  scrollDirection: Axis.vertical,
  children: [...],
)
```

### 3.5 显示部分相邻页面

```objc
// Objective-C - 需要调整 UIPageViewController 的 frame 或使用自定义布局
```

```dart
// Flutter - viewportFraction
PageView(
  controller: PageController(
    viewportFraction: 0.8,  // 每页占 80% 宽度，可看到相邻页面
  ),
  children: [...],
)

// 常用于卡片轮播
```

### 3.6 无限循环

```objc
// Objective-C - 需要手动实现循环逻辑
- (UIViewController *)pageViewController:(UIPageViewController *)pageViewController 
       viewControllerAfterViewController:(UIViewController *)viewController {
    NSInteger index = [self indexOfViewController:viewController];
    index = (index + 1) % self.numberOfPages;
    return [self viewControllerAtIndex:index];
}
```

```dart
// Flutter - 使用 builder 实现无限循环
PageView.builder(
  itemCount: null,  // 无限
  itemBuilder: (context, index) {
    final actualIndex = index % pages.length;
    return pages[actualIndex];
  },
)

// 或使用第三方包如 infinite_carousel
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 引导页
@interface OnboardingViewController : UIViewController <UIPageViewControllerDataSource, UIPageViewControllerDelegate>
@property (nonatomic, strong) UIPageViewController *pageViewController;
@property (nonatomic, strong) UIPageControl *pageControl;
@property (nonatomic, strong) NSArray<UIViewController *> *pages;
@end

@implementation OnboardingViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 创建页面
    UIViewController *page1 = [self createPageWithTitle:@"Welcome" color:[UIColor systemBlueColor]];
    UIViewController *page2 = [self createPageWithTitle:@"Features" color:[UIColor systemGreenColor]];
    UIViewController *page3 = [self createPageWithTitle:@"Get Started" color:[UIColor systemOrangeColor]];
    self.pages = @[page1, page2, page3];
    
    // PageViewController
    self.pageViewController = [[UIPageViewController alloc] 
        initWithTransitionStyle:UIPageViewControllerTransitionStyleScroll
          navigationOrientation:UIPageViewControllerNavigationOrientationHorizontal
                        options:nil];
    self.pageViewController.dataSource = self;
    self.pageViewController.delegate = self;
    [self.pageViewController setViewControllers:@[page1] direction:UIPageViewControllerNavigationDirectionForward animated:NO completion:nil];
    
    [self addChildViewController:self.pageViewController];
    [self.view addSubview:self.pageViewController.view];
    [self.pageViewController didMoveToParentViewController:self];
    
    // PageControl
    self.pageControl = [[UIPageControl alloc] init];
    self.pageControl.numberOfPages = self.pages.count;
    self.pageControl.currentPage = 0;
    self.pageControl.center = CGPointMake(self.view.center.x, self.view.bounds.size.height - 50);
    [self.view addSubview:self.pageControl];
    
    // 下一页按钮
    UIButton *nextButton = [UIButton buttonWithType:UIButtonTypeSystem];
    [nextButton setTitle:@"Next" forState:UIControlStateNormal];
    nextButton.frame = CGRectMake(self.view.bounds.size.width - 80, self.view.bounds.size.height - 50, 60, 30);
    [nextButton addTarget:self action:@selector(nextPage) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:nextButton];
}

- (UIViewController *)createPageWithTitle:(NSString *)title color:(UIColor *)color {
    UIViewController *vc = [[UIViewController alloc] init];
    vc.view.backgroundColor = color;
    
    UILabel *label = [[UILabel alloc] init];
    label.text = title;
    label.font = [UIFont boldSystemFontOfSize:24];
    label.textColor = [UIColor whiteColor];
    [label sizeToFit];
    label.center = vc.view.center;
    [vc.view addSubview:label];
    
    return vc;
}

- (UIViewController *)pageViewController:(UIPageViewController *)pageViewController viewControllerBeforeViewController:(UIViewController *)viewController {
    NSInteger index = [self.pages indexOfObject:viewController];
    if (index == 0) return nil;
    return self.pages[index - 1];
}

- (UIViewController *)pageViewController:(UIPageViewController *)pageViewController viewControllerAfterViewController:(UIViewController *)viewController {
    NSInteger index = [self.pages indexOfObject:viewController];
    if (index == self.pages.count - 1) return nil;
    return self.pages[index + 1];
}

- (void)pageViewController:(UIPageViewController *)pageViewController didFinishAnimating:(BOOL)finished previousViewControllers:(NSArray<UIViewController *> *)previousViewControllers transitionCompleted:(BOOL)completed {
    UIViewController *currentVC = pageViewController.viewControllers.firstObject;
    self.pageControl.currentPage = [self.pages indexOfObject:currentVC];
}

- (void)nextPage {
    NSInteger currentIndex = self.pageControl.currentPage;
    if (currentIndex < self.pages.count - 1) {
        [self.pageViewController setViewControllers:@[self.pages[currentIndex + 1]]
                                           direction:UIPageViewControllerNavigationDirectionForward
                                            animated:YES
                                          completion:nil];
        self.pageControl.currentPage = currentIndex + 1;
    } else {
        // 完成引导
        [self dismissViewControllerAnimated:YES completion:nil];
    }
}
@end
```

### Flutter 实现

```dart
// 引导页 - 更简洁
class OnboardingPage extends StatefulWidget {
  @override
  _OnboardingPageState createState() => _OnboardingPageState();
}

class _OnboardingPageState extends State<OnboardingPage> {
  final controller = PageController();
  int currentPage = 0;
  
  final pages = [
    {'title': 'Welcome', 'color': Colors.blue},
    {'title': 'Features', 'color': Colors.green},
    {'title': 'Get Started', 'color': Colors.orange},
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Stack(
        children: [
          // PageView
          PageView.builder(
            controller: controller,
            itemCount: pages.length,
            onPageChanged: (index) {
              setState(() => currentPage = index);
            },
            itemBuilder: (context, index) {
              return Container(
                color: pages[index]['color'] as Color,
                child: Center(
                  child: Text(
                    pages[index]['title'] as String,
                    style: TextStyle(
                      fontSize: 24,
                      fontWeight: FontWeight.bold,
                      color: Colors.white,
                    ),
                  ),
                ),
              );
            },
          ),
          // PageControl
          Positioned(
            bottom: 100,
            left: 0,
            right: 0,
            child: Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: List.generate(
                pages.length,
                (index) => Container(
                  margin: EdgeInsets.symmetric(horizontal: 4),
                  width: 8,
                  height: 8,
                  decoration: BoxDecoration(
                    shape: BoxShape.circle,
                    color: currentPage == index ? Colors.white : Colors.white38,
                  ),
                ),
              ),
            ),
          ),
          // Next Button
          Positioned(
            bottom: 40,
            right: 20,
            child: TextButton(
              onPressed: () {
                if (currentPage < pages.length - 1) {
                  controller.nextPage(
                    duration: Duration(milliseconds: 300),
                    curve: Curves.easeInOut,
                  );
                } else {
                  Navigator.pop(context);
                }
              },
              child: Text(
                currentPage < pages.length - 1 ? 'Next' : 'Done',
                style: TextStyle(color: Colors.white, fontSize: 18),
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

---

## 5. PageView 变体

| 构造函数 | 特点 | 使用场景 |
|---------|------|---------|
| `PageView()` | 固定子组件 | 少量固定页面 |
| `PageView.builder()` | 按需构建 | 大量页面 |
| `PageView.custom()` | 自定义 delegate | 特殊需求 |

---

## 6. 常见陷阱

### 陷阱1：页面高度不正确

```dart
// ⚠️ PageView 默认填满父容器
// 如果父容器没有约束，可能会出问题

// ❌ 在无约束容器中
Column(
  children: [
    PageView(children: [...]),  // 高度为 0 或报错
  ],
)

// ✅ 给 PageView 约束
Column(
  children: [
    SizedBox(
      height: 300,
      child: PageView(children: [...]),
    ),
  ],
)

// ✅ 或使用 Expanded
Column(
  children: [
    Expanded(
      child: PageView(children: [...]),
    ),
  ],
)
```

### 陷阱2：viewportFraction 理解错误

```dart
// viewportFraction 是每页占视口的比例
// 不是页面之间的间距

PageController(viewportFraction: 0.8)
// 每页占 80%，相邻页面各露出一部分

// 如果想要页面间有间距，在页面内容上添加
PageView.builder(
  controller: PageController(viewportFraction: 0.9),
  itemBuilder: (context, index) {
    return Padding(
      padding: EdgeInsets.symmetric(horizontal: 8),  // 页面间距
      child: Content(),
    );
  },
)
```

### 陷阱3：禁用滑动后无法程序控制

```dart
// ❌ 禁用滑动后，animateToPage 也会失效
PageView(
  physics: NeverScrollableScrollPhysics(),  // 禁用滑动
  controller: controller,
  children: [...],
)

controller.animateToPage(1, ...);  // 无效！

// ✅ 使用自定义 physics 允许程序滚动
class CustomPageViewScrollPhysics extends ScrollPhysics {
  const CustomPageViewScrollPhysics({super.parent});
  
  @override
  bool get allowImplicitScrolling => false;
  
  // 允许程序滚动，但禁用手势
}
```

### 陷阱4：Controller 未释放

```dart
// ❌ 在 StatefulWidget 中忘记释放
class _MyPageState extends State<MyPage> {
  final controller = PageController();  // 忘记释放
  
  @override
  Widget build(BuildContext context) {
    return PageView(controller: controller, children: [...]);
  }
}

// ✅ 在 dispose 中释放
class _MyPageState extends State<MyPage> {
  final controller = PageController();
  
  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return PageView(controller: controller, children: [...]);
  }
}
```

---

## 7. 学习技巧

### 技巧1：配合 TabBar

```dart
class _MyPageState extends State<MyPage> with SingleTickerProviderStateMixin {
  late TabController tabController;
  late PageController pageController;
  
  @override
  void initState() {
    super.initState();
    tabController = TabController(length: 3, vsync: this);
    pageController = PageController();
    
    tabController.addListener(() {
      if (tabController.indexIsChanging) {
        pageController.jumpToPage(tabController.index);
      }
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TabBar(
          controller: tabController,
          tabs: [Tab(text: 'Tab 1'), Tab(text: 'Tab 2'), Tab(text: 'Tab 3')],
        ),
        Expanded(
          child: PageView(
            controller: pageController,
            onPageChanged: (index) {
              tabController.animateTo(index);
            },
            children: [...],
          ),
        ),
      ],
    );
  }
}
```

### 技巧2：视差效果

```dart
PageView.builder(
  controller: PageController(viewportFraction: 1.0),
  itemBuilder: (context, index) {
    return AnimatedBuilder(
      animation: pageController,
      builder: (context, child) {
        double value = 1.0;
        if (pageController.position.haveDimensions) {
          value = pageController.page! - index;
          value = (1 - (value.abs() * 0.3)).clamp(0.7, 1.0);
        }
        return Transform.scale(
          scale: value,
          child: child,
        );
      },
      child: Container(...),
    );
  },
)
```

### 技巧3：自定义页面指示器

```dart
// 使用 SmoothPageIndicator 包
SmoothPageIndicator(
  controller: controller,
  count: pages.length,
  effect: WormEffect(
    dotHeight: 8,
    dotWidth: 8,
    activeColor: Colors.blue,
  ),
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UIPageViewController         → PageView
UIPageViewControllerDataSource → PageView.builder + itemBuilder
UIPageViewControllerDelegate   → onPageChanged
setViewControllers:animated:   → controller.animateToPage()
UIPageControl                → 自定义或使用包
transitionStyle              → 统一为 scroll
navigationOrientation        → scrollDirection
```

### 关键差异

| 方面 | UIKit UIPageViewController | Flutter PageView |
|------|---------------------------|------------------|
| **页面管理** | ViewController | Widget |
| **数据源** | DataSource 协议 | itemBuilder |
| **动画跳转** | 需手动实现 | animateToPage |
| **页面指示器** | UIPageControl | 需自己实现或用包 |
| **视口控制** | 需自定义 | viewportFraction |

### 最佳实践

1. **少量页面用 PageView()** - 固定子组件
2. **大量页面用 builder** - 按需构建
3. **配合 Controller** - 程序控制页面
4. **记得释放 Controller** - 避免内存泄漏

---

*最后更新: 2026-02-24*

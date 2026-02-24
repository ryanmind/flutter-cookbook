# CustomScrollView vs UIScrollView 对比学习

本文档记录 Flutter CustomScrollView 组件与 UIKit UIScrollView 多子视图的对比学习要点。

---

## 1. CustomScrollView 核心概念

### CustomScrollView 是什么

CustomScrollView 是 Flutter 中用于组合多种滚动效果的组件，可以使用 Sliver 组件构建复杂的滚动布局。

- 支持组合多种 Sliver 组件
- 统一的滚动行为
- 支持复杂的滚动效果（如折叠头部）
- 共享滚动控制器

### CustomScrollView vs UIScrollView 概念对比

| Flutter CustomScrollView | UIKit UIScrollView | 说明 |
|--------------------------|--------------------|------|
| `slivers` | 多个子视图 | 滚动内容 |
| `SliverList` | UITableView | 列表 |
| `SliverGrid` | UICollectionView | 网格 |
| `SliverAppBar` | 自定义 Header | 折叠头部 |
| `SliverPersistentHeader` | 自定义 Header | 持久头部 |
| `SliverToBoxAdapter` | 普通 UIView | 普通组件包装 |
| `controller` | 无直接对应 | 滚动控制器 |

---

## 2. 源码分析

### CustomScrollView 类定义

```dart
class CustomScrollView extends ScrollView {
  const CustomScrollView({
    super.key,
    super.scrollDirection = Axis.vertical,
    super.reverse = false,
    super.controller,
    super.primary,
    super.physics,
    super.scrollBehavior,
    super.shrinkWrap = false,
    super.center,
    super.anchor = 0.0,
    super.cacheExtent,
    this.slivers = const <Widget>[],  // Sliver 列表
    super.semanticChildCount,
    super.dragStartBehavior = DragStartBehavior.start,
    super.keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
    super.restorationId,
    super.clipBehavior = Clip.hardEdge,
  });
}
```

### 常用 Sliver 组件

```dart
// Sliver 组件类型
SliverList          // 列表
SliverGrid          // 网格
SliverAppBar        // 可折叠的应用栏
SliverPersistentHeader  // 持久化头部
SliverToBoxAdapter  // 包装普通 Widget
SliverPadding       // 内边距
SliverSafeArea      // 安全区
SliverFillRemaining // 填充剩余空间
SliverOpacity       // 透明度
SliverIgnorePointer // 忽略触摸
SliverOffstage      // 隐藏
```

---

## 3. 与 UIScrollView 详细对比

### 3.1 基础组合滚动

```objc
// Objective-C - UIScrollView + 多个子视图
UIScrollView *scrollView = [[UIScrollView alloc] initWithFrame:frame];

UIView *header = [[UIView alloc] initWithFrame:CGRectMake(0, 0, width, 200)];
header.backgroundColor = [UIColor blueColor];
[scrollView addSubview:header];

UITableView *tableView = [[UITableView alloc] initWithFrame:CGRectMake(0, 200, width, 500)];
tableView.scrollEnabled = NO;  // 禁用内部滚动
[scrollView addSubview:tableView];

UIView *footer = [[UIView alloc] initWithFrame:CGRectMake(0, 700, width, 100)];
footer.backgroundColor = [UIColor greenColor];
[scrollView addSubview:footer];

scrollView.contentSize = CGSizeMake(width, 800);
```

```dart
// Flutter - CustomScrollView
CustomScrollView(
  slivers: [
    // 头部
    SliverToBoxAdapter(
      child: Container(
        height: 200,
        color: Colors.blue,
      ),
    ),
    // 列表
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 20,
      ),
    ),
    // 底部
    SliverToBoxAdapter(
      child: Container(
        height: 100,
        color: Colors.green,
      ),
    ),
  ],
)
```

### 3.2 折叠头部（SliverAppBar）

```objc
// Objective-C - 需要自定义实现或使用第三方库
// 通常需要手动处理 contentOffset 和头部视图的缩放
```

```dart
// Flutter - SliverAppBar 内置支持
CustomScrollView(
  slivers: [
    SliverAppBar(
      expandedHeight: 200,
      floating: false,
      pinned: true,          // 固定在顶部
      flexibleSpace: FlexibleSpaceBar(
        title: Text('Title'),
        background: Image.asset('header.jpg', fit: BoxFit.cover),
      ),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 20,
      ),
    ),
  ],
)
```

### 3.3 SliverAppBar 效果图示

```
SliverAppBar 行为：

expandedHeight = 200

初始状态：
┌────────────────────────────┐
│                            │
│      展开的 AppBar         │
│      (200px 高度)          │
│                            │
├────────────────────────────┤
│  Item 1                    │
│  Item 2                    │
│  ...                       │
└────────────────────────────┘

滚动后（pinned: true）：
┌────────────────────────────┐
│  折叠的 AppBar (56px)      │
├────────────────────────────┤
│  Item 5                    │
│  Item 6                    │
│  ...                       │
└────────────────────────────┘

滚动后（floating: true）：
向下滚动时 AppBar 隐藏，向上滚动时立即显示
```

### 3.4 分组列表

```objc
// Objective-C - UITableView styleGrouped
UITableView *tableView = [[UITableView alloc] initWithFrame:frame style:UITableViewStyleGrouped];
```

```dart
// Flutter - CustomScrollView + 多个 SliverMainAxisGroup
CustomScrollView(
  slivers: [
    SliverMainAxisGroup(
      slivers: [
        SliverToBoxAdapter(
          child: Container(
            padding: EdgeInsets.all(16),
            child: Text('Section 1', style: TextStyle(fontWeight: FontWeight.bold)),
          ),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text('Item $index')),
            childCount: 5,
          ),
        ),
      ],
    ),
    SliverMainAxisGroup(
      slivers: [
        SliverToBoxAdapter(
          child: Container(
            padding: EdgeInsets.all(16),
            child: Text('Section 2', style: TextStyle(fontWeight: FontWeight.bold)),
          ),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text('Item $index')),
            childCount: 3,
          ),
        ),
      ],
    ),
  ],
)
```

### 3.5 网格 + 列表组合

```dart
CustomScrollView(
  slivers: [
    // 头部
    SliverToBoxAdapter(
      child: Container(height: 100, color: Colors.blue),
    ),
    // 网格
    SliverGrid(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        mainAxisSpacing: 8,
        crossAxisSpacing: 8,
      ),
      delegate: SliverChildBuilderDelegate(
        (context, index) => Container(color: Colors.primaries[index % 6]),
        childCount: 6,
      ),
    ),
    // 分隔
    SliverToBoxAdapter(
      child: Divider(),
    ),
    // 列表
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 10,
      ),
    ),
  ],
)
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 简单的个人主页
@interface ProfileViewController : UIViewController <UITableViewDataSource, UITableViewDelegate>
@property (nonatomic, strong) UIScrollView *scrollView;
@end

@implementation ProfileViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.scrollView = [[UIScrollView alloc] initWithFrame:self.view.bounds];
    self.scrollView.contentSize = CGSizeMake(self.view.bounds.size.width, 1000);
    [self.view addSubview:self.scrollView];
    
    // 头像区
    UIView *headerView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, self.view.bounds.size.width, 250)];
    headerView.backgroundColor = [UIColor systemBlueColor];
    [self.scrollView addSubview:headerView];
    
    UIImageView *avatar = [[UIImageView alloc] initWithFrame:CGRectMake(self.view.bounds.size.width/2 - 50, 50, 100, 100)];
    avatar.layer.cornerRadius = 50;
    avatar.backgroundColor = [UIColor whiteColor];
    [headerView addSubview:avatar];
    
    UILabel *nameLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 170, self.view.bounds.size.width, 30)];
    nameLabel.text = @"John Doe";
    nameLabel.textAlignment = NSTextAlignmentCenter;
    nameLabel.font = [UIFont boldSystemFontOfSize:20];
    [headerView addSubview:nameLabel];
    
    // 统计区
    UIView *statsView = [[UIView alloc] initWithFrame:CGRectMake(0, 250, self.view.bounds.size.width, 80)];
    [self.scrollView addSubview:statsView];
    
    // 内容区（模拟列表）
    for (int i = 0; i < 10; i++) {
        UILabel *item = [[UILabel alloc] initWithFrame:CGRectMake(16, 350 + i * 50, self.view.bounds.size.width - 32, 44)];
        item.text = [NSString stringWithFormat:@"Post %d", i + 1];
        [self.scrollView addSubview:item];
    }
}
@end
```

### Flutter 实现

```dart
// 个人主页 - 更简洁，性能更好
class ProfilePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        // 头部
        SliverAppBar(
          expandedHeight: 250,
          pinned: true,
          flexibleSpace: FlexibleSpaceBar(
            background: Container(
              color: Colors.blue,
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  CircleAvatar(
                    radius: 50,
                    backgroundColor: Colors.white,
                  ),
                  SizedBox(height: 16),
                  Text(
                    'John Doe',
                    style: TextStyle(
                      fontSize: 20,
                      fontWeight: FontWeight.bold,
                      color: Colors.white,
                    ),
                  ),
                ],
              ),
            ),
          ),
        ),
        // 统计区
        SliverToBoxAdapter(
          child: Container(
            height: 80,
            child: Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                _buildStat('Posts', '128'),
                _buildStat('Followers', '1.2K'),
                _buildStat('Following', '256'),
              ],
            ),
          ),
        ),
        // 内容列表
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(
              title: Text('Post ${index + 1}'),
              subtitle: Text('Description'),
            ),
            childCount: 10,
          ),
        ),
      ],
    );
  }

  Widget _buildStat(String label, String value) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text(value, style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
        Text(label, style: TextStyle(color: Colors.grey)),
      ],
    );
  }
}
```

---

## 5. Sliver 组件详解

### SliverList

```dart
// 列表
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => ListTile(title: Text('Item $index')),
    childCount: 100,
    // 可选：添加语义信息
    findChildIndexCallback: (key) {
      // 根据 Key 查找索引
      return null;
    },
  ),
)

// 固定子组件
SliverList(
  delegate: SliverChildListDelegate([
    ListTile(title: Text('Item 1')),
    ListTile(title: Text('Item 2')),
  ]),
)
```

### SliverGrid

```dart
SliverGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 3,
    mainAxisSpacing: 8,
    crossAxisSpacing: 8,
    childAspectRatio: 1.0,
  ),
  delegate: SliverChildBuilderDelegate(
    (context, index) => Container(color: Colors.primaries[index % 6]),
    childCount: 20,
  ),
)
```

### SliverPersistentHeader

```dart
SliverPersistentHeader(
  pinned: true,  // 固定在顶部
  delegate: _MyHeaderDelegate(),
)

class _MyHeaderDelegate extends SliverPersistentHeaderDelegate {
  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.blue,
      child: Center(child: Text('Header')),
    );
  }

  @override
  double get maxExtent => 100;

  @override
  double get minExtent => 50;

  @override
  bool shouldRebuild(covariant _MyHeaderDelegate oldDelegate) => false;
}
```

---

## 6. 常见陷阱

### 陷阱1：使用普通 Widget 而非 Sliver

```dart
// ❌ 错误：直接使用普通 Widget
CustomScrollView(
  slivers: [
    Container(height: 100, color: Colors.red),  // 报错！
  ],
)

// ✅ 正确：使用 SliverToBoxAdapter 包装
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(
      child: Container(height: 100, color: Colors.red),
    ),
  ],
)
```

### 陷阱2：嵌套滚动视图

```dart
// ❌ 错误：CustomScrollView 中嵌套 ListView
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(
      child: ListView(...),  // 滚动冲突！
    ),
  ],
)

// ✅ 正确：使用 SliverList
CustomScrollView(
  slivers: [
    SliverList(
      delegate: SliverChildBuilderDelegate(...),
    ),
  ],
)
```

### 陷阱3：SliverAppBar 的 pinned 和 floating

```dart
// pinned: true - 滚动时固定在顶部
// floating: true - 向上滚动时立即显示
// snap: true - 配合 floating，松手时自动展开或收起

SliverAppBar(
  pinned: true,     // 固定
  floating: false,  // 不浮动
)

SliverAppBar(
  pinned: false,
  floating: true,   // 浮动
  snap: true,       // 快速展开
)
```

### 陷阱4：SliverList 性能

```dart
// ⚠️ SliverChildListDelegate 会一次性创建所有子组件
SliverList(
  delegate: SliverChildListDelegate([
    // 大量子组件...
  ]),
)

// ✅ SliverChildBuilderDelegate 按需创建
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => Item(index),
    childCount: 1000,
  ),
)
```

---

## 7. 学习技巧

### 技巧1：使用 SliverPadding

```dart
CustomScrollView(
  slivers: [
    SliverPadding(
      padding: EdgeInsets.all(16),
      sliver: SliverList(
        delegate: SliverChildBuilderDelegate(...),
      ),
    ),
  ],
)
```

### 技巧2：SliverFillRemaining

```dart
// 填充剩余空间
CustomScrollView(
  slivers: [
    SliverList(...),
    SliverFillRemaining(
      hasScrollBody: false,  // 表示没有更多滚动内容
      child: Container(
        color: Colors.grey[200],
        child: Center(child: Text('No more content')),
      ),
    ),
  ],
)
```

### 技巧3：滚动监听

```dart
final controller = ScrollController();

@override
void initState() {
  super.initState();
  controller.addListener(() {
    print('Offset: ${controller.offset}');
  });
}

CustomScrollView(
  controller: controller,
  slivers: [...],
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UIScrollView + 多子视图     → CustomScrollView + Slivers
UITableView                 → SliverList
UICollectionView            → SliverGrid
自定义折叠头部              → SliverAppBar
contentSize                 → Sliver 自动计算
contentOffset               → ScrollController.offset
```

### 关键差异

| 方面 | UIKit UIScrollView | Flutter CustomScrollView |
|------|--------------------|--------------------------|
| **内容组织** | 多个子视图 | Sliver 列表 |
| **滚动控制** | 单一滚动 | 统一滚动 |
| **折叠头部** | 手动实现 | SliverAppBar |
| **布局计算** | 手动计算 frame | Sliver 自动 |
| **性能优化** | 复用机制 | builder 按需创建 |

### 最佳实践

1. **组合多种布局用 CustomScrollView** - 列表 + 网格 + 头部
2. **折叠头部用 SliverAppBar** - 内置支持
3. **大数据用 builder** - SliverChildBuilderDelegate
4. **普通 Widget 用 SliverToBoxAdapter** - 包装普通组件

---

*最后更新: 2026-02-24*

# ListView vs UITableView 对比学习

本文档记录 Flutter ListView 组件与 UIKit UITableView 的对比学习要点。

---

## 1. ListView 核心概念

### ListView 是什么

ListView 是 Flutter 中最常用的滚动列表组件，类似于 UIKit 的 UITableView。

- 支持垂直/水平滚动
- 支持列表项复用（自动管理）
- 支持分隔线、头部/尾部
- 多种构造函数适应不同场景

### ListView vs UITableView 概念对比

| Flutter ListView | UIKit UITableView | 说明 |
|------------------|-------------------|------|
| `ListView.builder()` | `UITableView` + `dequeueReusableCell` | 列表复用 |
| `itemBuilder` | `cellForRowAt` | 构建/返回 cell |
| `itemCount` | `numberOfRowsInSection` | 数据源数量 |
| `separatorBuilder` | 自定义分隔线 | 分隔线 |
| `ScrollController` | 无直接对应 | 滚动控制 |
| `onRefresh` | `UIRefreshControl` | 下拉刷新 |
| `ListView.separated()` | 自定义实现 | 带分隔线列表 |

---

## 2. 源码分析

### ListView 构造函数

```dart
class ListView extends BoxScrollView {
  // 1. 默认构造函数 - 适合少量子组件
  ListView({
    super.key,
    super.scrollDirection = Axis.vertical,
    super.reverse = false,
    super.controller,
    super.primary,
    super.physics,
    super.shrinkWrap = false,
    super.padding,
    this.itemExtent,               // 固定 item 高度（性能优化）
    this.prototypeItem,            // 原型 item（动态高度优化）
    super.children = const <Widget>[],
  });
  
  // 2. builder 构造函数 - 适合大量/无限列表
  ListView.builder({
    super.key,
    super.scrollDirection = Axis.vertical,
    super.reverse = false,
    super.controller,
    super.primary,
    super.physics,
    super.shrinkWrap = false,
    super.padding,
    this.itemExtent,
    this.prototypeItem,
    required IndexedWidgetBuilder itemBuilder,  // 必需
    int? itemCount,                              // 可选，null 表示无限
  });
  
  // 3. separated 构造函数 - 带分隔线
  ListView.separated({
    required IndexedWidgetBuilder itemBuilder,
    required IndexedWidgetBuilder separatorBuilder,  // 分隔线构建器
    required int itemCount,
    // ...
  });
}
```

### 与 UITableView 的关键差异

```dart
// UITableView 需要：
// 1. 注册 cell
// 2. 实现 dataSource 方法
// 3. 手动处理复用

// ListView 自动处理：
// 1. Widget 自动创建和销毁
// 2. 不需要注册
// 3. 复用由 Flutter 框架管理
```

---

## 3. 与 UITableView 详细对比

### 3.1 基础列表

```objc
// Objective-C - UITableView
@interface MyViewController : UIViewController <UITableViewDataSource, UITableViewDelegate>
@property (nonatomic, strong) UITableView *tableView;
@property (nonatomic, strong) NSArray *items;
@end

@implementation MyViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:UITableViewStylePlain];
    self.tableView.dataSource = self;
    self.tableView.delegate = self;
    [self.view addSubview:self.tableView];
    
    [self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@"Cell"];
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.items.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
    cell.textLabel.text = self.items[indexPath.row];
    return cell;
}
@end
```

```dart
// Flutter - ListView.builder
class MyWidget extends StatelessWidget {
  final List<String> items;
  
  const MyWidget({required this.items});
  
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: items.length,
      itemBuilder: (context, index) {
        return ListTile(
          title: Text(items[index]),
        );
      },
    );
  }
}
```

### 3.2 固定高度优化

```objc
// Objective-C - UITableView 自动优化
// rowHeight 或 estimatedRowHeight
tableView.rowHeight = 60;
tableView.estimatedRowHeight = 60;
```

```dart
// Flutter - itemExtent 性能优化
ListView.builder(
  itemExtent: 60,  // 固定高度，跳过高度计算
  itemCount: items.length,
  itemBuilder: (context, index) {
    return ListTile(title: Text(items[index]));
  },
)

// 或使用 prototypeItem（Flutter 3.0+）
ListView.builder(
  prototypeItem: ListTile(title: Text('')),  // 用于测量高度
  itemCount: items.length,
  itemBuilder: (context, index) {
    return ListTile(title: Text(items[index]));
  },
)
```

### 3.3 分隔线

```objc
// Objective-C - UITableView 自带分隔线
tableView.separatorStyle = UITableViewCellSeparatorStyleSingleLine;
tableView.separatorColor = [UIColor grayColor];
tableView.separatorInset = UIEdgeInsetsMake(0, 16, 0, 0);
```

```dart
// Flutter - ListView.separated
ListView.separated(
  itemCount: items.length,
  itemBuilder: (context, index) {
    return ListTile(title: Text(items[index]));
  },
  separatorBuilder: (context, index) {
    return Divider(
      height: 1,
      thickness: 1,
      indent: 16,
      color: Colors.grey,
    );
  },
)

// 或自定义分隔线
separatorBuilder: (context, index) {
  return Container(
    height: 8,
    color: Colors.grey[200],
  );
}
```

### 3.4 分组列表

```objc
// Objective-C - UITableView styleGrouped
UITableView *tableView = [[UITableView alloc] initWithFrame:frame style:UITableViewStyleGrouped];

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return sections.count;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return sections[section].items.count;
}

- (NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section {
    return sections[section].title;
}
```

```dart
// Flutter - 多种方式实现

// 方式1：Column + 多个 ListView
Column(
  children: sections.map((section) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Container(
          padding: EdgeInsets.all(16),
          child: Text(section.title, style: TextStyle(fontWeight: FontWeight.bold)),
        ),
        ListView.builder(
          shrinkWrap: true,        // 包裹内容
          physics: NeverScrollableScrollPhysics(),  // 禁用滚动
          itemCount: section.items.length,
          itemBuilder: (context, index) {
            return ListTile(title: Text(section.items[index]));
          },
        ),
      ],
    );
  }).toList(),
)

// 方式2：CustomScrollView + SliverList
CustomScrollView(
  slivers: sections.map((section) {
    return SliverMainAxisGroup(
      slivers: [
        SliverToBoxAdapter(
          child: Container(
            padding: EdgeInsets.all(16),
            child: Text(section.title),
          ),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text(section.items[index])),
            childCount: section.items.length,
          ),
        ),
      ],
    );
  }).toList(),
)
```

### 3.5 下拉刷新

```objc
// Objective-C - UIRefreshControl
UIRefreshControl *refreshControl = [[UIRefreshControl alloc] init];
[refreshControl addTarget:self action:@selector(refresh) forControlEvents:UIControlEventValueChanged];
tableView.refreshControl = refreshControl;

- (void)refresh {
    // 加载数据
    [self.tableView reloadData];
    [self.refreshControl endRefreshing];
}
```

```dart
// Flutter - RefreshIndicator
RefreshIndicator(
  onRefresh: () async {
    // 加载数据
    await fetchData();
  },
  child: ListView.builder(
    itemCount: items.length,
    itemBuilder: (context, index) {
      return ListTile(title: Text(items[index]));
    },
  ),
)
```

### 3.6 滚动监听

```objc
// Objective-C - UIScrollViewDelegate
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    CGFloat offsetY = scrollView.contentOffset.y;
    // 处理滚动
}

- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {
    // 滚动结束
}
```

```dart
// Flutter - ScrollController
final controller = ScrollController();

@override
void initState() {
  super.initState();
  controller.addListener(() {
    print('Offset: ${controller.offset}');
    
    // 滚动到底部加载更多
    if (controller.position.pixels >= controller.position.maxScrollExtent - 100) {
      loadMore();
    }
  });
}

ListView.builder(
  controller: controller,
  itemBuilder: (context, index) => ListTile(title: Text('$index')),
)

@override
void dispose() {
  controller.dispose();  // 记得释放
  super.dispose();
}
```

### 3.7 滚动到指定位置

```objc
// Objective-C
NSIndexPath *indexPath = [NSIndexPath indexPathForRow:10 inSection:0];
[tableView scrollToRowAtIndexPath:indexPath atScrollPosition:UITableViewScrollPositionMiddle animated:YES];
```

```dart
// Flutter
controller.animateTo(
  index * itemHeight,  // 目标位置
  duration: Duration(milliseconds: 300),
  curve: Curves.easeInOut,
);

// 或使用 Scrollable.ensureVisible
Scrollable.ensureVisible(
  targetContext,
  duration: Duration(milliseconds: 300),
);
```

---

## 4. 完整示例对比

### UIKit 实现

```objc
// 完整的列表实现
@interface TodoListViewController : UIViewController <UITableViewDataSource, UITableViewDelegate>
@property (nonatomic, strong) UITableView *tableView;
@property (nonatomic, strong) NSMutableArray *items;
@end

@implementation TodoListViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    self.items = [NSMutableArray array];
    
    // 创建 TableView
    self.tableView = [[UITableView alloc] initWithFrame:self.view.bounds style:UITableViewStylePlain];
    self.tableView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
    self.tableView.dataSource = self;
    self.tableView.delegate = self;
    [self.view addSubview:self.tableView];
    
    // 注册 Cell
    [self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@"Cell"];
    
    // 下拉刷新
    UIRefreshControl *refreshControl = [[UIRefreshControl alloc] init];
    [refreshControl addTarget:self action:@selector(handleRefresh:) forControlEvents:UIControlEventValueChanged];
    self.tableView.refreshControl = refreshControl;
    
    // 加载数据
    [self loadData];
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.items.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
    NSDictionary *item = self.items[indexPath.row];
    cell.textLabel.text = item[@"title"];
    cell.accessoryType = [item[@"done"] boolValue] ? UITableViewCellAccessoryCheckmark : UITableViewCellAccessoryNone;
    return cell;
}

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    [tableView deselectRowAtIndexPath:indexPath animated:YES];
    // 处理点击
}

- (void)handleRefresh:(UIRefreshControl *)refreshControl {
    [self loadData];
}

- (void)loadData {
    // 模拟网络请求
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, 1 * NSEC_PER_SEC), dispatch_get_main_queue(), ^{
        self.items = [@[
            @{@"title": @"Task 1", @"done": @NO},
            @{@"title": @"Task 2", @"done": @YES},
            @{@"title": @"Task 3", @"done": @NO},
        ] mutableCopy];
        [self.tableView reloadData];
        [self.tableView.refreshControl endRefreshing];
    });
}
@end
```

### Flutter 实现

```dart
// 完整的列表实现 - 更简洁
class TodoListWidget extends StatefulWidget {
  @override
  _TodoListWidgetState createState() => _TodoListWidgetState();
}

class _TodoListWidgetState extends State<TodoListWidget> {
  List<Map<String, dynamic>> items = [];
  bool isLoading = false;

  @override
  void initState() {
    super.initState();
    loadData();
  }

  Future<void> loadData() async {
    setState(() => isLoading = true);
    
    // 模拟网络请求
    await Future.delayed(Duration(seconds: 1));
    
    setState(() {
      items = [
        {'title': 'Task 1', 'done': false},
        {'title': 'Task 2', 'done': true},
        {'title': 'Task 3', 'done': false},
      ];
      isLoading = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    return RefreshIndicator(
      onRefresh: loadData,
      child: ListView.builder(
        itemCount: items.length,
        itemBuilder: (context, index) {
          final item = items[index];
          return ListTile(
            title: Text(item['title']),
            trailing: item['done'] ? Icon(Icons.check) : null,
            onTap: () {
              setState(() {
                items[index]['done'] = !items[index]['done'];
              });
            },
          );
        },
      ),
    );
  }
}
```

---

## 5. ListView 构造函数选择

| 构造函数 | 场景 | 特点 |
|---------|------|------|
| `ListView()` | 少量固定子组件 | 直接传入 children |
| `ListView.builder()` | 大量/动态数据 | 按需构建，复用优化 |
| `ListView.separated()` | 需要分隔线 | 支持 separatorBuilder |
| `ListView.custom()` | 高度自定义 | 自定义 SliverChildDelegate |

```dart
// 少量固定子组件
ListView(
  children: [
    ListTile(title: Text('Item 1')),
    ListTile(title: Text('Item 2')),
    ListTile(title: Text('Item 3')),
  ],
)

// 大量数据
ListView.builder(
  itemCount: 1000,
  itemBuilder: (context, index) {
    return ListTile(title: Text('Item $index'));
  },
)

// 无限列表
ListView.builder(
  itemBuilder: (context, index) {
    return ListTile(title: Text('Item $index'));
  },
  // 不设置 itemCount，无限滚动
)

// 带分隔线
ListView.separated(
  itemCount: 10,
  itemBuilder: (context, index) => ListTile(title: Text('Item $index')),
  separatorBuilder: (context, index) => Divider(),
)
```

---

## 6. 常见陷阱

### 陷阱1：shrinkWrap 误用

```dart
// ❌ 问题：shrinkWrap: true 在嵌套列表中性能问题
Column(
  children: [
    ListView.builder(
      shrinkWrap: true,  // 会一次性构建所有 item
      physics: NeverScrollableScrollPhysics(),
      itemCount: 1000,   // 大量数据会很慢
      itemBuilder: ...,
    ),
  ],
)

// ✅ 解决方案：使用 CustomScrollView + Sliver
CustomScrollView(
  slivers: [
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(...),
        childCount: 1000,
      ),
    ),
  ],
)
```

### 陷阱2：itemExtent 与动态高度

```dart
// ❌ 错误：itemExtent 与动态高度冲突
ListView.builder(
  itemExtent: 60,  // 强制所有 item 高度 60
  itemBuilder: (context, index) {
    return Container(
      height: 100,  // 无效！会被忽略
      child: Text('Item $index'),
    );
  },
)

// ✅ 如果高度动态，不要使用 itemExtent
ListView.builder(
  itemBuilder: (context, index) {
    return Container(
      height: index % 2 == 0 ? 60 : 100,  // 动态高度生效
      child: Text('Item $index'),
    );
  },
)
```

### 陷阱3：列表项点击状态

```dart
// ❌ 问题：ListTile 点击无水波纹效果
ListTile(
  title: Text('Item'),
  onTap: () {},
)

// ✅ 默认就有水波纹效果，确保使用 Material 祖先
Material(
  child: ListTile(
    title: Text('Item'),
    onTap: () {},
  ),
)
```

### 陷阱4：NestedScrollView 滚动冲突

```dart
// 嵌套滚动时需要特殊处理
NestedScrollView(
  headerSliverBuilder: (context, innerBoxIsScrolled) {
    return [
      SliverAppBar(
        floating: true,
        snap: true,
      ),
    ];
  },
  body: ListView.builder(
    itemBuilder: ...,
  ),
)
```

---

## 7. 性能优化技巧

### 技巧1：itemExtent / prototypeItem

```dart
// 固定高度 - 最快
ListView.builder(
  itemExtent: 60,
  itemBuilder: ...,
)

// 原型 item - 次优
ListView.builder(
  prototypeItem: ListTile(title: Text('')),
  itemBuilder: ...,
)
```

### 技巧2：addAutomaticKeepAlives

```dart
// 保持列表项状态（默认 true）
ListView.builder(
  addAutomaticKeepAlives: true,  // 保持已滚过的 item 状态
  itemBuilder: ...,
)

// 如果不需要保持状态，可以关闭提升性能
ListView.builder(
  addAutomaticKeepAlives: false,
  itemBuilder: ...,
)
```

### 技巧3：addRepaintBoundaries

```dart
// 添加重绘边界（默认 true）
ListView.builder(
  addRepaintBoundaries: true,  // 减少 item 间重绘
  itemBuilder: ...,
)
```

### 技巧4：cacheExtent

```dart
// 控制预缓存范围
ListView.builder(
  cacheExtent: 500,  // 预缓存 500 像素范围
  itemBuilder: ...,
)
```

---

## 8. 核心理念总结

### UIKit 思维 → Flutter 思维

```
UITableView                 → ListView
dequeueReusableCell        → Flutter 自动管理
numberOfRowsInSection      → itemCount
cellForRowAt               → itemBuilder
separatorStyle             → ListView.separated + separatorBuilder
UIRefreshControl           → RefreshIndicator
scrollViewDidScroll        → ScrollController.addListener
scrollToRowAtIndexPath     → controller.animateTo
registerClass:forCell      → 不需要
```

### 关键差异

| 方面 | UIKit UITableView | Flutter ListView |
|------|-------------------|------------------|
| **复用机制** | 手动 dequeue | 自动管理 |
| **Cell 注册** | 必须注册 | 不需要 |
| **分隔线** | 内置属性 | separatorBuilder |
| **下拉刷新** | UIRefreshControl | RefreshIndicator |
| **高度计算** | 自动或 delegate | 自动或 itemExtent |

### 最佳实践

1. **大数据用 builder** - 按需构建，性能好
2. **固定高度用 itemExtent** - 跳过高度计算
3. **分隔线用 separated** - 简洁方便
4. **滚动控制用 ScrollController** - 监听、跳转

---

*最后更新: 2026-02-24*

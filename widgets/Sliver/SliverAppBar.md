# Flutter SliverAppBar 组件用法详解

## 简介

SliverAppBar 是 Flutter 中与 CustomScrollView 配合使用的应用栏组件。它支持滚动时的折叠、展开、固定等效果，是实现 Material Design 滚动效果的核心组件。

## 1. 初级用法

### 1.1 基本概念

SliverAppBar 的核心作用：
- 与 CustomScrollView 无缝配合
- 支持滚动时的折叠展开
- 可设置浮动、固定、吸附等行为

### 1.2 基本语法

```dart
CustomScrollView(
  slivers: [
    SliverAppBar(
      title: Text('Title'),
      expandedHeight: 200,
      floating: true,
    ),
    SliverList(delegate: SliverChildBuilderDelegate(...)),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `title` | `Widget?` | 标题 |
| `expandedHeight` | `double?` | 展开高度 |
| `floating` | `bool` | 浮动模式 |
| `pinned` | `bool` | 固定模式 |
| `snap` | `bool` | 快速吸附 |
| `flexibleSpace` | `Widget?` | 弹性空间 |
| `background` | `Widget?` | 背景内容 |
| `leading` | `Widget?` | 前导组件 |
| `actions` | `List<Widget>?` | 操作按钮 |
| `bottom` | `PreferredSizeWidget?` | 底部组件 |

### 1.4 基础示例

#### 可折叠 AppBar

```dart
class CollapsibleAppBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverAppBar(
          expandedHeight: 200,
          floating: false,
          pinned: true,
          flexibleSpace: FlexibleSpaceBar(
            title: Text('Collapsible AppBar'),
            background: Container(color: Colors.blue),
          ),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text('Item $index')),
            childCount: 50,
          ),
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 三种滚动模式

```dart
// 1. 浮动模式 - 向上滚动立即出现
SliverAppBar(
  floating: true,
  title: Text('Floating'),
)

// 2. 固定模式 - 始终保持在顶部
SliverAppBar(
  pinned: true,
  title: Text('Pinned'),
)

// 3. 吸附模式 - 需要配合 floating
SliverAppBar(
  floating: true,
  snap: true,
  title: Text('Snap'),
)
```

### 2.2 带图片背景

```dart
SliverAppBar(
  expandedHeight: 250,
  pinned: true,
  flexibleSpace: FlexibleSpaceBar(
    title: Text(
      'Image Background',
      style: TextStyle(color: Colors.white),
    ),
    background: Image.network(
      'https://example.com/image.jpg',
      fit: BoxFit.cover,
    ),
  ),
)
```

### 2.3 带底部 TabBar

```dart
class AppBarWithTabs extends StatefulWidget {
  @override
  State<AppBarWithTabs> createState() => _AppBarWithTabsState();
}

class _AppBarWithTabsState extends State<AppBarWithTabs>
    with SingleTickerProviderStateMixin {
  late TabController _tabController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 3, vsync: this);
  }

  @override
  void dispose() {
    _tabController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverAppBar(
          pinned: true,
          title: Text('Tabs'),
          bottom: TabBar(
            controller: _tabController,
            tabs: [
              Tab(text: 'Tab 1'),
              Tab(text: 'Tab 2'),
              Tab(text: 'Tab 3'),
            ],
          ),
        ),
        SliverFillRemaining(
          child: TabBarView(
            controller: _tabController,
            children: [
              Center(child: Text('Content 1')),
              Center(child: Text('Content 2')),
              Center(child: Text('Content 3')),
            ],
          ),
        ),
      ],
    );
  }
}
```

### 2.4 带操作按钮

```dart
SliverAppBar(
  expandedHeight: 150,
  pinned: true,
  title: Text('Actions'),
  actions: [
    IconButton(
      icon: Icon(Icons.search),
      onPressed: () {},
    ),
    IconButton(
      icon: Icon(Icons.more_vert),
      onPressed: () {},
    ),
  ],
  flexibleSpace: FlexibleSpaceBar(
    background: Container(color: Colors.purple),
  ),
)
```

### 2.5 自定义 FlexibleSpace

```dart
SliverAppBar(
  expandedHeight: 200,
  flexibleSpace: LayoutBuilder(
    builder: (context, constraints) {
      final expandRatio = (constraints.maxHeight - kToolbarHeight) / 
                          (200 - kToolbarHeight);
      final animation = AlwaysStoppedAnimation(expandRatio.clamp(0.0, 1.0));
      
      return Stack(
        children: [
          FadeTransition(
            opacity: animation,
            child: Container(
              color: Colors.blue,
              child: Center(
                child: Text(
                  'Custom FlexibleSpace',
                  style: TextStyle(color: Colors.white, fontSize: 24),
                ),
              ),
            ),
          ),
        ],
      );
    },
  ),
)
```

## 3. 高级用法

### 3.1 完整页面示例

```dart
class SliverAppBarPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverAppBar(
          expandedHeight: 250,
          floating: false,
          pinned: true,
          snap: false,
          leading: IconButton(
            icon: Icon(Icons.arrow_back),
            onPressed: () => Navigator.pop(context),
          ),
          actions: [
            IconButton(icon: Icon(Icons.favorite_border), onPressed: () {}),
            IconButton(icon: Icon(Icons.share), onPressed: () {}),
          ],
          flexibleSpace: FlexibleSpaceBar(
            title: Text(
              'Detail Page',
              style: TextStyle(
                color: Colors.white,
                fontWeight: FontWeight.bold,
              ),
            ),
            background: Stack(
              fit: StackFit.expand,
              children: [
                Image.network(
                  'https://picsum.photos/400/300',
                  fit: BoxFit.cover,
                ),
                Container(
                  decoration: BoxDecoration(
                    gradient: LinearGradient(
                      begin: Alignment.topCenter,
                      end: Alignment.bottomCenter,
                      colors: [
                        Colors.transparent,
                        Colors.black54,
                      ],
                    ),
                  ),
                ),
              ],
            ),
          ),
        ),
        SliverToBoxAdapter(
          child: Padding(
            padding: EdgeInsets.all(16),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  'Title',
                  style: Theme.of(context).textTheme.headlineSmall,
                ),
                SizedBox(height: 8),
                Text('Description text goes here...'),
              ],
            ),
          ),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(
              leading: CircleAvatar(child: Text('${index + 1}')),
              title: Text('Item ${index + 1}'),
            ),
            childCount: 20,
          ),
        ),
      ],
    );
  }
}
```

### 3.2 视差效果

```dart
class ParallaxAppBar extends StatefulWidget {
  @override
  State<ParallaxAppBar> createState() => _ParallaxAppBarState();
}

class _ParallaxAppBarState extends State<ParallaxAppBar> {
  final ScrollController _scrollController = ScrollController();
  double _offset = 0;

  @override
  void initState() {
    super.initState();
    _scrollController.addListener(() {
      setState(() => _offset = _scrollController.offset);
    });
  }

  @override
  void dispose() {
    _scrollController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      controller: _scrollController,
      slivers: [
        SliverAppBar(
          expandedHeight: 300,
          pinned: true,
          flexibleSpace: FlexibleSpaceBar(
            background: Transform.translate(
              offset: Offset(0, _offset * 0.5),
              child: Image.network(
                'https://picsum.photos/400/400',
                fit: BoxFit.cover,
              ),
            ),
          ),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text('Item $index')),
            childCount: 30,
          ),
        ),
      ],
    );
  }
}
```

### 3.3 搜索栏集成

```dart
class SearchAppBar extends StatefulWidget {
  @override
  State<SearchAppBar> createState() => _SearchAppBarState();
}

class _SearchAppBarState extends State<SearchAppBar> {
  bool _isSearching = false;
  final TextEditingController _searchController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverAppBar(
          pinned: true,
          title: _isSearching
              ? TextField(
                  controller: _searchController,
                  autofocus: true,
                  decoration: InputDecoration(
                    hintText: 'Search...',
                    border: InputBorder.none,
                  ),
                )
              : Text('Search Demo'),
          actions: [
            IconButton(
              icon: Icon(_isSearching ? Icons.close : Icons.search),
              onPressed: () {
                setState(() {
                  _isSearching = !_isSearching;
                  if (!_isSearching) {
                    _searchController.clear();
                  }
                });
              },
            ),
          ],
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text('Item $index')),
            childCount: 20,
          ),
        ),
      ],
    );
  }
}
```

### 3.4 多级折叠

```dart
class MultiLevelAppBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        // 主 AppBar
        SliverAppBar(
          expandedHeight: 200,
          pinned: true,
          flexibleSpace: FlexibleSpaceBar(
            title: Text('Multi Level'),
            background: Container(
              color: Colors.blue[700],
              child: Center(
                child: Icon(Icons.account_balance, size: 64, color: Colors.white),
              ),
            ),
          ),
        ),
        // 信息栏
        SliverPersistentHeader(
          pinned: true,
          delegate: _InfoBarDelegate(
            minHeight: 60,
            maxHeight: 60,
          ),
        ),
        // 内容
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text('Item $index')),
            childCount: 30,
          ),
        ),
      ],
    );
  }
}

class _InfoBarDelegate extends SliverPersistentHeaderDelegate {
  final double minHeight;
  final double maxHeight;

  _InfoBarDelegate({required this.minHeight, required this.maxHeight});

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.blue[100],
      padding: EdgeInsets.symmetric(horizontal: 16),
      child: Row(
        children: [
          Icon(Icons.info_outline),
          SizedBox(width: 8),
          Text('Information bar'),
        ],
      ),
    );
  }

  @override
  double get maxExtent => maxHeight;

  @override
  double get minExtent => minHeight;

  @override
  bool shouldRebuild(covariant _InfoBarDelegate oldDelegate) {
    return minHeight != oldDelegate.minHeight || maxHeight != oldDelegate.maxHeight;
  }
}
```

## 4. 常见问题与解决方案

### 问题1：标题显示时机

```dart
// 使用 collapseMode 控制标题显示
FlexibleSpaceBar(
  title: Text('Title'),
  collapseMode: CollapseMode.pin,  // pin / parallax / none
)
```

### 问题2：状态栏遮盖

```dart
SliverAppBar(
  primary: true,  // 默认 true，会自动处理状态栏
  // ...
)
```

### 问题3：与 TabBar 配合

```dart
// 使用 NestedScrollView 替代 CustomScrollView
NestedScrollView(
  headerSliverBuilder: (context, innerBoxIsScrolled) {
    return [
      SliverAppBar(...),
    ];
  },
  body: TabBarView(...),
)
```

## 5. SliverAppBar vs AppBar

| 特性 | SliverAppBar | AppBar |
|------|--------------|--------|
| 使用场景 | CustomScrollView | Scaffold |
| 折叠展开 | 支持 | 不支持 |
| 滚动效果 | 支持 | 不支持 |
| 灵活性 | 高 | 低 |

### 选择建议

```dart
// 需要滚动效果 → SliverAppBar
CustomScrollView(slivers: [SliverAppBar(...)])

// 简单静态 AppBar → AppBar
Scaffold(appBar: AppBar(...))
```

## 6. 最佳实践

### 6.1 展开高度设置

```dart
// 合理设置展开高度
SliverAppBar(
  expandedHeight: MediaQuery.of(context).size.height * 0.3,
  // ...
)
```

### 6.2 标题样式

```dart
FlexibleSpaceBar(
  title: Text(
    'Title',
    style: TextStyle(
      color: Colors.white,
      fontSize: 16,
      fontWeight: FontWeight.w500,
    ),
  ),
  titlePadding: EdgeInsets.only(left: 16, bottom: 16),
)
```

### 6.3 性能优化

```dart
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => ListTile(title: Text('Item $index')),
    childCount: 100,
    addAutomaticKeepAlives: true,  // 自动保持状态
  ),
)
```

## 总结

SliverAppBar 是实现高级滚动效果的核心组件：

**核心要点**：
1. 配合 CustomScrollView 使用
2. floating、pinned、snap 控制滚动行为
3. FlexibleSpaceBar 定义展开内容

**最佳实践**：
1. 合理设置展开高度
2. 使用正确的滚动模式组合
3. 注意性能优化

**常见场景**：
- 详情页面
- 个人主页
- 图片浏览
- 带搜索的列表

---

*最后更新: 2026-02-19*

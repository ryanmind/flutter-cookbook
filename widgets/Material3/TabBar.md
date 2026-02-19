# Flutter TabBar 组件用法详解

## 简介

TabBar 是 Material Design 风格的标签页导航组件，配合 TabBarView 实现内容切换，常用于分类展示、页面切换等场景。

## 1. 初级用法

### 1.1 基本概念

TabBar 的核心特点：
- 顶部或底部标签导航
- 支持滑动切换内容
- 支持图标和文字
- 可自定义样式

### 1.2 核心组件

| 组件 | 说明 |
|------|------|
| `TabBar` | 标签栏，显示标签按钮 |
| `TabBarView` | 标签内容视图 |
| `TabController` | 控制标签切换 |
| `DefaultTabController` | 默认标签控制器 |
| `Tab` | 单个标签 |

### 1.3 基本语法

```dart
DefaultTabController(
  length: 3,
  child: Scaffold(
    appBar: AppBar(
      title: Text('TabBar 示例'),
      bottom: TabBar(
        tabs: [
          Tab(text: '首页'),
          Tab(text: '消息'),
          Tab(text: '我的'),
        ],
      ),
    ),
    body: TabBarView(
      children: [
        Center(child: Text('首页内容')),
        Center(child: Text('消息内容')),
        Center(child: Text('我的内容')),
      ],
    ),
  ),
)
```

### 1.4 TabBar 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `tabs` | `List<Widget>` | - | 标签列表（必需） |
| `controller` | `TabController?` | - | 控制器 |
| `isScrollable` | `bool` | `false` | 是否可滚动 |
| `indicatorColor` | `Color?` | - | 指示器颜色 |
| `indicatorWeight` | `double` | `2.0` | 指示器高度 |
| `indicatorPadding` | `EdgeInsetsGeometry` | `zero` | 指示器内边距 |
| `indicator` | `Decoration?` | - | 自定义指示器 |
| `indicatorSize` | `TabBarIndicatorSize?` | - | 指示器尺寸 |
| `labelColor` | `Color?` | - | 标签颜色 |
| `labelStyle` | `TextStyle?` | - | 标签样式 |
| `unselectedLabelColor` | `Color?` | - | 未选中标签颜色 |
| `unselectedLabelStyle` | `TextStyle?` | - | 未选中标签样式 |
| `onTap` | `ValueChanged<int>?` | - | 点击回调 |

### 1.5 Tab 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `text` | `String?` | 标签文字 |
| `icon` | `Widget?` | 标签图标 |
| `child` | `Widget?` | 自定义内容 |

### 1.6 基础示例

#### 文字标签

```dart
TabBar(
  tabs: [
    Tab(text: '推荐'),
    Tab(text: '热门'),
    Tab(text: '关注'),
  ],
)
```

#### 图标标签

```dart
TabBar(
  tabs: [
    Tab(icon: Icon(Icons.home)),
    Tab(icon: Icon(Icons.message)),
    Tab(icon: Icon(Icons.person)),
  ],
)
```

#### 图标+文字标签

```dart
TabBar(
  tabs: [
    Tab(icon: Icon(Icons.home), text: '首页'),
    Tab(icon: Icon(Icons.message), text: '消息'),
    Tab(icon: Icon(Icons.person), text: '我的'),
  ],
)
```

## 2. 中级用法

### 2.1 自定义指示器

```dart
TabBar(
  indicatorColor: Colors.blue,
  indicatorWeight: 3,
  indicatorSize: TabBarIndicatorSize.label,
  tabs: [
    Tab(text: '首页'),
    Tab(text: '消息'),
    Tab(text: '我的'),
  ],
)

// 自定义圆角指示器
TabBar(
  indicator: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(20),
  ),
  indicatorSize: TabBarIndicatorSize.tab,
  labelColor: Colors.white,
  unselectedLabelColor: Colors.black,
  tabs: [
    Tab(text: '首页'),
    Tab(text: '消息'),
    Tab(text: '我的'),
  ],
)
```

### 2.2 可滚动标签

```dart
TabBar(
  isScrollable: true,
  tabs: [
    Tab(text: '推荐'),
    Tab(text: '热门'),
    Tab(text: '关注'),
    Tab(text: '科技'),
    Tab(text: '游戏'),
    Tab(text: '体育'),
    Tab(text: '娱乐'),
    Tab(text: '财经'),
  ],
)
```

### 2.3 标签样式

```dart
TabBar(
  labelColor: Colors.blue,
  labelStyle: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
  unselectedLabelColor: Colors.grey,
  unselectedLabelStyle: TextStyle(fontSize: 14),
  tabs: [
    Tab(text: '首页'),
    Tab(text: '消息'),
    Tab(text: '我的'),
  ],
)
```

### 2.4 自定义 TabController

```dart
class TabBarPage extends StatefulWidget {
  @override
  _TabBarPageState createState() => _TabBarPageState();
}

class _TabBarPageState extends State<TabBarPage>
    with SingleTickerProviderStateMixin {
  late TabController _tabController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 3, vsync: this);
    _tabController.addListener(() {
      if (!_tabController.indexIsChanging) {
        print('选中标签: ${_tabController.index}');
      }
    });
  }

  @override
  void dispose() {
    _tabController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('TabBar'),
        bottom: TabBar(
          controller: _tabController,
          tabs: [
            Tab(text: '首页'),
            Tab(text: '消息'),
            Tab(text: '我的'),
          ],
        ),
      ),
      body: TabBarView(
        controller: _tabController,
        children: [
          Center(child: Text('首页')),
          Center(child: Text('消息')),
          Center(child: Text('我的')),
        ],
      ),
    );
  }
}
```

### 2.5 监听标签切换

```dart
TabBar(
  onTap: (index) {
    print('点击标签: $index');
  },
  tabs: [
    Tab(text: '首页'),
    Tab(text: '消息'),
    Tab(text: '我的'),
  ],
)

// 或通过 Controller 监听
_tabController.addListener(() {
  if (!_tabController.indexIsChanging) {
    print('切换到: ${_tabController.index}');
  }
})
```

### 2.6 底部 TabBar

```dart
Scaffold(
  appBar: AppBar(title: Text('底部标签')),
  body: TabBarView(
    children: [
      Center(child: Text('首页')),
      Center(child: Text('消息')),
      Center(child: Text('我的')),
    ],
  ),
  bottomNavigationBar: Material(
    color: Colors.blue,
    child: TabBar(
      labelColor: Colors.white,
      unselectedLabelColor: Colors.white70,
      tabs: [
        Tab(icon: Icon(Icons.home), text: '首页'),
        Tab(icon: Icon(Icons.message), text: '消息'),
        Tab(icon: Icon(Icons.person), text: '我的'),
      ],
    ),
  ),
)
```

## 3. 高级用法

### 3.1 带徽章的标签

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
          Container(
            padding: EdgeInsets.symmetric(horizontal: 6, vertical: 2),
            decoration: BoxDecoration(
              color: Colors.red,
              borderRadius: BorderRadius.circular(10),
            ),
            child: Text(
              '5',
              style: TextStyle(color: Colors.white, fontSize: 10),
            ),
          ),
        ],
      ),
    ),
    Tab(text: '我的'),
  ],
)
```

### 3.2 带搜索框的 AppBar + TabBar

```dart
Scaffold(
  appBar: AppBar(
    title: TextField(
      decoration: InputDecoration(
        hintText: '搜索',
        border: InputBorder.none,
      ),
    ),
    actions: [
      IconButton(icon: Icon(Icons.search), onPressed: () {}),
    ],
    bottom: TabBar(
      tabs: [
        Tab(text: '全部'),
        Tab(text: '文章'),
        Tab(text: '用户'),
      ],
    ),
  ),
  body: TabBarView(
    children: [
      Center(child: Text('全部')),
      Center(child: Text('文章')),
      Center(child: Text('用户')),
    ],
  ),
)
```

### 3.3 嵌套 TabBar

```dart
DefaultTabController(
  length: 2,
  child: Scaffold(
    appBar: AppBar(
      title: Text('外层标签'),
      bottom: TabBar(
        tabs: [
          Tab(text: '推荐'),
          Tab(text: '关注'),
        ],
      ),
    ),
    body: TabBarView(
      children: [
        // 内层 TabBar
        DefaultTabController(
          length: 3,
          child: Column(
            children: [
              TabBar(
                tabs: [
                  Tab(text: '最新'),
                  Tab(text: '热门'),
                  Tab(text: '精华'),
                ],
              ),
              Expanded(
                child: TabBarView(
                  children: [
                    Center(child: Text('最新')),
                    Center(child: Text('热门')),
                    Center(child: Text('精华')),
                  ],
                ),
              ),
            ],
          ),
        ),
        Center(child: Text('关注')),
      ],
    ),
  ),
)
```

### 3.4 动态添加标签

```dart
class DynamicTabsPage extends StatefulWidget {
  @override
  _DynamicTabsPageState createState() => _DynamicTabsPageState();
}

class _DynamicTabsPageState extends State<DynamicTabsPage>
    with SingleTickerProviderStateMixin {
  late TabController _tabController;
  List<String> _tabs = ['首页', '消息'];

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: _tabs.length, vsync: this);
  }

  void _addTab() {
    setState(() {
      _tabs.add('标签 ${_tabs.length + 1}');
      _tabController.dispose();
      _tabController = TabController(length: _tabs.length, vsync: this);
    });
  }

  @override
  void dispose() {
    _tabController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('动态标签'),
        actions: [
          IconButton(icon: Icon(Icons.add), onPressed: _addTab),
        ],
        bottom: TabBar(
          controller: _tabController,
          isScrollable: true,
          tabs: _tabs.map((tab) => Tab(text: tab)).toList(),
        ),
      ),
      body: TabBarView(
        controller: _tabController,
        children: _tabs.map((tab) => Center(child: Text(tab))).toList(),
      ),
    );
  }
}
```

### 3.5 自定义标签按钮

```dart
class CustomTab extends StatelessWidget {
  final String text;
  final bool isSelected;
  final VoidCallback onTap;

  const CustomTab({
    required this.text,
    required this.isSelected,
    required this.onTap,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onTap,
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
        decoration: BoxDecoration(
          color: isSelected ? Colors.blue : Colors.transparent,
          borderRadius: BorderRadius.circular(20),
        ),
        child: Text(
          text,
          style: TextStyle(
            color: isSelected ? Colors.white : Colors.black,
            fontWeight: isSelected ? FontWeight.bold : FontWeight.normal,
          ),
        ),
      ),
    );
  }
}

// 使用
Row(
  children: List.generate(tabs.length, (index) {
    return CustomTab(
      text: tabs[index],
      isSelected: currentIndex == index,
      onTap: () {
        setState(() => currentIndex = index);
      },
    );
  }),
)
```

### 3.6 懒加载 TabBarView

```dart
class LazyTabBarView extends StatefulWidget {
  final int itemCount;
  final Widget Function(BuildContext, int) itemBuilder;

  const LazyTabBarView({
    required this.itemCount,
    required this.itemBuilder,
    Key? key,
  }) : super(key: key);

  @override
  _LazyTabBarViewState createState() => _LazyTabBarViewState();
}

class _LazyTabBarViewState extends State<LazyTabBarView>
    with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);
    return TabBarView(
      children: List.generate(
        widget.itemCount,
        (index) => KeepAliveWrapper(
          child: widget.itemBuilder(context, index),
        ),
      ),
    );
  }
}

class KeepAliveWrapper extends StatefulWidget {
  final Widget child;

  const KeepAliveWrapper({required this.child, Key? key}) : super(key: key);

  @override
  _KeepAliveWrapperState createState() => _KeepAliveWrapperState();
}

class _KeepAliveWrapperState extends State<KeepAliveWrapper>
    with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);
    return widget.child;
  }
}
```

## 4. 与其他组件配合

### 4.1 与 NavigationBar 配合

```dart
// Material 3 推荐使用 NavigationBar 替代底部 TabBar
Scaffold(
  bottomNavigationBar: NavigationBar(
    selectedIndex: _currentIndex,
    onDestinationSelected: (index) {
      setState(() => _currentIndex = index);
    },
    destinations: [
      NavigationDestination(icon: Icon(Icons.home), label: '首页'),
      NavigationDestination(icon: Icon(Icons.message), label: '消息'),
      NavigationDestination(icon: Icon(Icons.person), label: '我的'),
    ],
  ),
  body: IndexedStack(
    index: _currentIndex,
    children: [
      HomePage(),
      MessagePage(),
      ProfilePage(),
    ],
  ),
)
```

### 4.2 与 PageView 配合

```dart
class SyncedTabsPage extends StatefulWidget {
  @override
  _SyncedTabsPageState createState() => _SyncedTabsPageState();
}

class _SyncedTabsPageState extends State<SyncedTabsPage> {
  final PageController _pageController = PageController();
  int _currentIndex = 0;

  @override
  void dispose() {
    _pageController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Container(
          color: Colors.blue,
          child: Row(
            children: ['首页', '消息', '我的'].asMap().entries.map((entry) {
              return Expanded(
                child: GestureDetector(
                  onTap: () {
                    _pageController.animateToPage(
                      entry.key,
                      duration: Duration(milliseconds: 300),
                      curve: Curves.ease,
                    );
                  },
                  child: Container(
                    padding: EdgeInsets.all(16),
                    child: Text(
                      entry.value,
                      textAlign: TextAlign.center,
                      style: TextStyle(
                        color: _currentIndex == entry.key
                            ? Colors.white
                            : Colors.white70,
                        fontWeight: _currentIndex == entry.key
                            ? FontWeight.bold
                            : FontWeight.normal,
                      ),
                    ),
                  ),
                ),
              );
            }).toList(),
          ),
        ),
        Expanded(
          child: PageView(
            controller: _pageController,
            onPageChanged: (index) {
              setState(() => _currentIndex = index);
            },
            children: [
              Center(child: Text('首页')),
              Center(child: Text('消息')),
              Center(child: Text('我的')),
            ],
          ),
        ),
      ],
    );
  }
}
```

## 5. 常见问题与解决方案

### 问题1：TabBarView 高度不正确

```dart
// TabBarView 默认占满剩余空间
// 如果需要自适应高度，使用 shrinkWrap
TabBarView(
  children: [
    SingleChildScrollView(child: Column(children: [...])),
    // ...
  ],
)
```

### 问题2：标签过多时挤压

```dart
// 使用 isScrollable
TabBar(
  isScrollable: true,
  tabs: [...],
)
```

### 问题3：滑动切换和点击切换冲突

```dart
TabBar(
  onTap: (index) {
    // 注意：onTap 在滑动时也会触发
    // 使用 controller.indexIsUpdating 判断
  },
  tabs: [...],
)
```

### 问题4：保持 TabBarView 状态

```dart
// 使用 AutomaticKeepAliveClientMixin
class _TabPageState extends State<TabPage>
    with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);
    return Center(child: Text('内容'));
  }
}
```

### 问题5：TabBar 与 AppBar 颜色不一致

```dart
AppBar(
  backgroundColor: Colors.blue,
  bottom: TabBar(
    indicatorColor: Colors.white,
    labelColor: Colors.white,
    unselectedLabelColor: Colors.white70,
    tabs: [...],
  ),
)
```

## 6. 最佳实践

### 6.1 封装通用 Tab 页面

```dart
class AppTabPage extends StatelessWidget {
  final String title;
  final List<String> tabs;
  final List<Widget> pages;
  final bool scrollable;

  const AppTabPage({
    required this.title,
    required this.tabs,
    required this.pages,
    this.scrollable = false,
    Key? key,
  })  : assert(tabs.length == pages.length),
        super(key: key);

  @override
  Widget build(BuildContext context) {
    return DefaultTabController(
      length: tabs.length,
      child: Scaffold(
        appBar: AppBar(
          title: Text(title),
          bottom: TabBar(
            isScrollable: scrollable,
            tabs: tabs.map((tab) => Tab(text: tab)).toList(),
          ),
        ),
        body: TabBarView(children: pages),
      ),
    );
  }
}

// 使用
AppTabPage(
  title: '我的应用',
  tabs: ['首页', '消息', '我的'],
  pages: [HomePage(), MessagePage(), ProfilePage()],
)
```

### 6.2 统一 TabBar 样式

```dart
class AppTabBarTheme {
  static TabBar defaultTabBar(List<Tab> tabs) {
    return TabBar(
      labelColor: Colors.blue,
      unselectedLabelColor: Colors.grey,
      indicatorColor: Colors.blue,
      indicatorWeight: 3,
      labelStyle: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
      tabs: tabs,
    );
  }
}
```

### 6.3 响应式标签数量

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final showMoreTabs = constraints.maxWidth > 600;
    final tabCount = showMoreTabs ? 5 : 3;
    
    return DefaultTabController(
      length: tabCount,
      child: Scaffold(...),
    );
  },
)
```

## 总结

TabBar 是 Flutter 中常用的页面切换组件：

**核心要点**：
1. DefaultTabController 或 TabController 管理状态
2. TabBar + TabBarView 配合使用
3. isScrollable 处理多标签情况
4. 使用 AutomaticKeepAliveClientMixin 保持状态

**常用场景**：
- 分类导航
- 内容切换
- 底部导航栏
- 筛选标签

**最佳实践**：
1. 标签数量控制在 5 个以内
2. 多标签使用 isScrollable
3. 保持页面状态使用 AutomaticKeepAliveClientMixin
4. 统一样式配置

---

*最后更新: 2026-02-18*

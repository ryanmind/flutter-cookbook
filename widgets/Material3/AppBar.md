# Flutter AppBar 组件用法详解

## 简介

AppBar 是 Material Design 风格的顶部应用栏，通常作为 Scaffold 的 appBar 属性使用，用于显示标题、导航按钮和操作按钮。

## 1. 初级用法

### 1.1 基本概念

AppBar 的核心特点：
- Material Design 标准顶部栏
- 支持标题、副标题、导航按钮
- 支持操作按钮和溢出菜单
- 可自定义背景、高度、阴影

### 1.2 基本语法

```dart
Scaffold(
  appBar: AppBar(
    title: Text('标题'),
  ),
  body: Container(),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `title` | `Widget?` | - | 标题组件 |
| `leading` | `Widget?` | - | 左侧导航组件 |
| `actions` | `List<Widget>?` | - | 右侧操作按钮列表 |
| `bottom` | `PreferredSizeWidget?` | - | 底部组件（如 TabBar） |
| `elevation` | `double?` | - | 阴影高度 |
| `shadowColor` | `Color?` | - | 阴影颜色 |
| `backgroundColor` | `Color?` | - | 背景色 |
| `foregroundColor` | `Color?` | - | 前景色 |
| `centerTitle` | `bool?` | - | 标题居中 |
| `automaticallyImplyLeading` | `bool` | `true` | 自动添加返回按钮 |
| `titleSpacing` | `double?` | - | 标题间距 |
| `toolbarHeight` | `double?` | - | 工具栏高度 |
| `toolbarOpacity` | `double` | `1.0` | 工具栏透明度 |
| `bottomOpacity` | `double` | `1.0` | 底部透明度 |
| `flexibleSpace` | `Widget?` | - | 弹性空间 |

### 1.4 基础示例

#### 简单 AppBar

```dart
AppBar(
  title: Text('应用标题'),
)
```

#### 带返回按钮

```dart
AppBar(
  leading: IconButton(
    icon: Icon(Icons.arrow_back),
    onPressed: () => Navigator.pop(context),
  ),
  title: Text('返回'),
)
```

#### 带操作按钮

```dart
AppBar(
  title: Text('操作'),
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
)
```

#### 居中标题

```dart
AppBar(
  title: Text('居中标题'),
  centerTitle: true,
)
```

## 2. 中级用法

### 2.1 自定义样式

```dart
AppBar(
  title: Text('自定义样式'),
  backgroundColor: Colors.blue,
  foregroundColor: Colors.white,
  elevation: 4,
  shadowColor: Colors.black26,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.vertical(
      bottom: Radius.circular(16),
    ),
  ),
)
```

### 2.2 带副标题

```dart
AppBar(
  title: Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    children: [
      Text('主标题'),
      Text(
        '副标题',
        style: TextStyle(fontSize: 12, fontWeight: FontWeight.normal),
      ),
    ],
  ),
)
```

### 2.3 搜索栏

```dart
AppBar(
  title: TextField(
    decoration: InputDecoration(
      hintText: '搜索...',
      border: InputBorder.none,
      hintStyle: TextStyle(color: Colors.white70),
    ),
    style: TextStyle(color: Colors.white),
  ),
  actions: [
    IconButton(
      icon: Icon(Icons.clear),
      onPressed: () {},
    ),
  ],
)
```

### 2.4 带 TabBar

```dart
DefaultTabController(
  length: 3,
  child: Scaffold(
    appBar: AppBar(
      title: Text('标签页'),
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
        Center(child: Text('首页')),
        Center(child: Text('消息')),
        Center(child: Text('我的')),
      ],
    ),
  ),
)
```

### 2.5 渐变背景

```dart
AppBar(
  title: Text('渐变背景'),
  flexibleSpace: Container(
    decoration: BoxDecoration(
      gradient: LinearGradient(
        colors: [Colors.blue, Colors.purple],
        begin: Alignment.topLeft,
        end: Alignment.bottomRight,
      ),
    ),
  ),
)
```

### 2.6 自定义左侧按钮

```dart
AppBar(
  leading: Builder(
    builder: (context) => IconButton(
      icon: Icon(Icons.menu),
      onPressed: () => Scaffold.of(context).openDrawer(),
    ),
  ),
  title: Text('菜单'),
)
```

### 2.7 溢出菜单

```dart
AppBar(
  title: Text('更多操作'),
  actions: [
    PopupMenuButton<String>(
      onSelected: (value) {
        print('选中: $value');
      },
      itemBuilder: (context) => [
        PopupMenuItem(value: 'edit', child: Text('编辑')),
        PopupMenuItem(value: 'delete', child: Text('删除')),
        PopupMenuItem(value: 'share', child: Text('分享')),
      ],
    ),
  ],
)
```

## 3. 高级用法

### 3.1 SliverAppBar

```dart
CustomScrollView(
  slivers: [
    SliverAppBar(
      expandedHeight: 200,
      floating: false,
      pinned: true,
      flexibleSpace: FlexibleSpaceBar(
        title: Text('可折叠 AppBar'),
        background: Image.network(
          'https://picsum.photos/400/200',
          fit: BoxFit.cover,
        ),
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

### 3.2 带搜索切换

```dart
class SearchAppBar extends StatefulWidget {
  final String title;

  const SearchAppBar({required this.title, Key? key}) : super(key: key);

  @override
  _SearchAppBarState createState() => _SearchAppBarState();
}

class _SearchAppBarState extends State<SearchAppBar> {
  bool _isSearching = false;
  final TextEditingController _searchController = TextEditingController();

  @override
  void dispose() {
    _searchController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AppBar(
      title: _isSearching
          ? TextField(
              controller: _searchController,
              autofocus: true,
              decoration: InputDecoration(
                hintText: '搜索...',
                border: InputBorder.none,
              ),
              onChanged: (value) {
                // 搜索逻辑
              },
            )
          : Text(widget.title),
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
    );
  }
}
```

### 3.3 透明 AppBar

```dart
Scaffold(
  extendBodyBehindAppBar: true,
  appBar: AppBar(
    title: Text('透明 AppBar'),
    backgroundColor: Colors.transparent,
    elevation: 0,
  ),
  body: Container(
    decoration: BoxDecoration(
      image: DecorationImage(
        image: NetworkImage('https://picsum.photos/400/800'),
        fit: BoxFit.cover,
      ),
    ),
    child: Center(child: Text('内容')),
  ),
)
```

### 3.4 带进度条

```dart
AppBar(
  title: Text('加载中'),
  bottom: PreferredSize(
    preferredSize: Size.fromHeight(4),
    child: LinearProgressIndicator(),
  ),
)
```

### 3.5 自定义高度

```dart
AppBar(
  title: Text('自定义高度'),
  toolbarHeight: 80,
  titleSpacing: 24,
)
```

## 4. 性能优化

### 4.1 避免频繁重建

```dart
// ❌ 不好：每次 build 创建新的 Widget
AppBar(
  title: Text(getTitle()),
  actions: [
    IconButton(icon: Icon(Icons.search), onPressed: () {}),
  ],
)

// ✅ 好：使用 const
AppBar(
  title: const Text('标题'),
  actions: const [
    IconButton(icon: Icon(Icons.search), onPressed: null),
  ],
)
```

### 4.2 使用 preferredSize

```dart
class CustomAppBar extends StatelessWidget implements PreferredSizeWidget {
  final String title;

  const CustomAppBar({required this.title, Key? key}) : super(key: key);

  @override
  Size get preferredSize => Size.fromHeight(kToolbarHeight);

  @override
  Widget build(BuildContext context) {
    return AppBar(title: Text(title));
  }
}

// 使用
Scaffold(
  appBar: CustomAppBar(title: '自定义 AppBar'),
)
```

## 5. 常见问题与解决方案

### 问题1：返回按钮不显示

```dart
// 默认情况下，Navigator 会自动添加返回按钮
// 如果返回按钮不显示，检查路由配置

// 或手动添加
AppBar(
  leading: IconButton(
    icon: Icon(Icons.arrow_back),
    onPressed: () => Navigator.pop(context),
  ),
  title: Text('标题'),
)
```

### 问题2：标题不居中

```dart
AppBar(
  title: Text('居中标题'),
  centerTitle: true,  // Android 默认 false，iOS 默认 true
)
```

### 问题3：AppBar 遮挡内容

```dart
// 使用 SafeArea
Scaffold(
  body: SafeArea(
    child: Container(),
  ),
)

// 或使用 MediaQuery
Container(
  padding: EdgeInsets.only(
    top: MediaQuery.of(context).padding.top,
  ),
)
```

### 问题4：深色主题适配

```dart
AppBar(
  title: Text('标题'),
  backgroundColor: Theme.of(context).colorScheme.surface,
  foregroundColor: Theme.of(context).colorScheme.onSurface,
)
```

### 问题5：底部溢出

```dart
// 确保正确设置 preferredSize
AppBar(
  bottom: PreferredSize(
    preferredSize: Size.fromHeight(48),
    child: Container(
      height: 48,
      child: TabBar(...),
    ),
  ),
)
```

## 6. AppBar 与其他组件配合

### 6.1 与 Drawer 配合

```dart
Scaffold(
  appBar: AppBar(
    title: Text('侧边栏'),
  ),
  drawer: Drawer(
    child: ListView(
      children: [
        DrawerHeader(child: Text('Header')),
        ListTile(title: Text('Item 1')),
        ListTile(title: Text('Item 2')),
      ],
    ),
  ),
  body: Container(),
)
```

### 6.2 与 FloatingActionButton 配合

```dart
Scaffold(
  appBar: AppBar(title: Text('FAB')),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
  body: Container(),
)
```

### 6.3 与 BottomNavigationBar 配合

```dart
Scaffold(
  appBar: AppBar(title: Text('底部导航')),
  body: Container(),
  bottomNavigationBar: BottomNavigationBar(
    items: [
      BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
      BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
    ],
  ),
)
```

## 7. 最佳实践

### 7.1 统一 AppBar 样式

```dart
class AppAppBar extends StatelessWidget implements PreferredSizeWidget {
  final String title;
  final List<Widget>? actions;

  const AppAppBar({
    required this.title,
    this.actions,
    Key? key,
  }) : super(key: key);

  @override
  Size get preferredSize => Size.fromHeight(kToolbarHeight);

  @override
  Widget build(BuildContext context) {
    return AppBar(
      title: Text(title),
      centerTitle: true,
      elevation: 0,
      actions: actions,
    );
  }
}
```

### 7.2 主题配置

```dart
MaterialApp(
  theme: ThemeData(
    appBarTheme: AppBarTheme(
      backgroundColor: Colors.blue,
      foregroundColor: Colors.white,
      centerTitle: true,
      elevation: 0,
    ),
  ),
)
```

### 7.3 响应式 AppBar

```dart
AppBar(
  title: LayoutBuilder(
    builder: (context, constraints) {
      return constraints.maxWidth > 400
          ? Text('完整标题文字')
          : Text('短标题');
    },
  ),
)
```

## 总结

AppBar 是 Flutter 中最常用的顶部导航组件：

**核心要点**：
1. 通常作为 Scaffold 的 appBar 属性使用
2. 支持标题、导航按钮、操作按钮
3. 可配合 TabBar、Drawer 等使用
4. SliverAppBar 支持折叠效果

**常用属性**：
- `title` - 标题
- `leading` - 左侧按钮
- `actions` - 操作按钮
- `bottom` - 底部组件
- `backgroundColor` - 背景色

**最佳实践**：
1. 统一使用主题配置
2. 合理设置 centerTitle
3. 使用 PreferredSizeWidget 封装
4. 避免过度复杂的 AppBar

---

*最后更新: 2026-02-18*

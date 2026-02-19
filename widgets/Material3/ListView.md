# Flutter ListView 组件用法详解

## 简介

ListView 是 Flutter 中最常用的滚动列表组件，用于展示大量数据的垂直或水平滚动列表。它是 Flutter 实现列表视图的核心组件，支持多种构造方式和优化策略。

## 1. 初级用法

### 1.1 基本概念

ListView 是一个可滚动的组件列表，主要特点：
- 支持垂直和水平滚动
- 自动处理子组件的布局和滚动
- 提供多种构造函数适应不同场景

### 1.2 基本语法

```dart
ListView(
  children: [
    ListTile(title: Text('项目 1')),
    ListTile(title: Text('项目 2')),
    ListTile(title: Text('项目 3')),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `scrollDirection` | `Axis` | `Axis.vertical` | 滚动方向 |
| `reverse` | `bool` | `false` | 是否反向滚动 |
| `controller` | `ScrollController?` | - | 滚动控制器 |
| `primary` | `bool?` | - | 是否使用主滚动控制器 |
| `physics` | `ScrollPhysics?` | - | 滚动物理特性 |
| `shrinkWrap` | `bool` | `false` | 是否收缩包裹内容 |
| `padding` | `EdgeInsetsGeometry?` | - | 内边距 |
| `itemExtent` | `double?` | - | 子项固定高度 |
| `children` | `List<Widget>` | - | 子组件列表 |

### 1.4 基础示例

#### 简单列表

```dart
ListView(
  children: [
    ListTile(
      leading: Icon(Icons.person),
      title: Text('张三'),
      subtitle: Text('Flutter 开发工程师'),
    ),
    ListTile(
      leading: Icon(Icons.person),
      title: Text('李四'),
      subtitle: Text('Android 开发工程师'),
    ),
    ListTile(
      leading: Icon(Icons.person),
      title: Text('王五'),
      subtitle: Text('iOS 开发工程师'),
    ),
  ],
)
```

#### 水平列表

```dart
ListView(
  scrollDirection: Axis.horizontal,
  children: [
    Container(width: 100, color: Colors.red),
    Container(width: 100, color: Colors.green),
    Container(width: 100, color: Colors.blue),
  ],
)
```

#### 带分隔线的列表

```dart
ListView(
  children: [
    ListTile(title: Text('项目 1')),
    Divider(),
    ListTile(title: Text('项目 2')),
    Divider(),
    ListTile(title: Text('项目 3')),
  ],
)
```

## 2. 中级用法

### 2.1 四种构造函数

#### ListView() - 默认构造函数

适用于子组件数量较少的情况：

```dart
ListView(
  padding: EdgeInsets.all(16),
  children: [
    Container(height: 100, color: Colors.red),
    Container(height: 100, color: Colors.green),
    Container(height: 100, color: Colors.blue),
  ],
)
```

#### ListView.builder() - 构建器构造函数

适用于大量或无限列表，按需构建子组件：

```dart
ListView.builder(
  itemCount: 100,  // 可选，不设置则为无限列表
  itemBuilder: (context, index) {
    return ListTile(
      title: Text('项目 ${index + 1}'),
      leading: CircleAvatar(child: Text('${index + 1}')),
    );
  },
)
```

#### ListView.separated() - 分隔构造函数

自动添加分隔组件：

```dart
ListView.separated(
  itemCount: 20,
  separatorBuilder: (context, index) {
    return Divider(height: 1);  // 分隔线
    // 或者自定义分隔组件
    // return SizedBox(height: 8);
  },
  itemBuilder: (context, index) {
    return ListTile(
      title: Text('项目 ${index + 1}'),
    );
  },
)
```

#### ListView.custom() - 自定义构造函数

使用自定义的 SliverChildDelegate：

```dart
ListView.custom(
  childrenDelegate: SliverChildBuilderDelegate(
    (context, index) {
      return ListTile(title: Text('项目 $index'));
    },
    childCount: 50,
    findItemIndexCallback: (Key key) {
      // 用于在子组件重排序时查找索引
      return null;
    },
  ),
)
```

### 2.2 滚动控制

#### ScrollController 基础用法

```dart
class ScrollControllerExample extends StatefulWidget {
  @override
  _ScrollControllerExampleState createState() => _ScrollControllerExampleState();
}

class _ScrollControllerExampleState extends State<ScrollControllerExample> {
  final ScrollController _controller = ScrollController();
  bool _showButton = false;

  @override
  void initState() {
    super.initState();
    _controller.addListener(_onScroll);
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  void _onScroll() {
    // 滚动超过 200 像素显示返回顶部按钮
    setState(() {
      _showButton = _controller.offset > 200;
    });
  }

  void _scrollToTop() {
    _controller.animateTo(
      0,
      duration: Duration(milliseconds: 500),
      curve: Curves.easeInOut,
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: ListView.builder(
        controller: _controller,
        itemCount: 100,
        itemBuilder: (context, index) {
          return ListTile(title: Text('项目 ${index + 1}'));
        },
      ),
      floatingActionButton: _showButton
          ? FloatingActionButton(
              onPressed: _scrollToTop,
              child: Icon(Icons.arrow_upward),
            )
          : null,
    );
  }
}
```

#### 滚动到指定位置

```dart
// 跳转到指定索引（需要 GlobalKey 配合）
final GlobalKey itemKey = GlobalKey();

// 在需要跳转的地方
Scrollable.ensureVisible(itemKey.currentContext!);

// 或者使用 ScrollController
_controller.animateTo(
  500,  // 目标位置（像素）
  duration: Duration(milliseconds: 300),
  curve: Curves.ease,
);
```

### 2.3 滚动物理特性

```dart
// 弹性滚动（iOS 风格，iOS 平台默认）
ListView(
  physics: BouncingScrollPhysics(),
  children: [...],
)

// 离合器滚动（Android 风格，Android 平台默认）
ListView(
  physics: ClampingScrollPhysics(),
  children: [...],
)

// 禁用滚动
ListView(
  physics: NeverScrollableScrollPhysics(),
  children: [...],
)

// 始终可滚动（即使内容不足）
ListView(
  physics: AlwaysScrollableScrollPhysics(),
  children: [...],
)
```

### 2.4 shrinkWrap 属性

当 ListView 需要在另一个滚动组件内部时使用：

```dart
// 嵌套在 Column 中
Column(
  children: [
    Text('标题'),
    Expanded(
      child: ListView(
        shrinkWrap: true,  // 收缩包裹内容
        children: [
          ListTile(title: Text('项目 1')),
          ListTile(title: Text('项目 2')),
        ],
      ),
    ),
  ],
)

// 注意：shrinkWrap 会一次性构建所有子组件，大量数据时性能较差
```

### 2.5 itemExtent 属性

当子组件高度固定时使用，提升性能：

```dart
ListView.builder(
  itemExtent: 56,  // 每个 ListTile 的默认高度
  itemCount: 1000,
  itemBuilder: (context, index) {
    return ListTile(title: Text('项目 ${index + 1}'));
  },
)

// 水平列表使用 itemExtent
ListView.builder(
  scrollDirection: Axis.horizontal,
  itemExtent: 100,  // 固定宽度
  itemCount: 20,
  itemBuilder: (context, index) {
    return Container(color: Colors.primaries[index % Colors.primaries.length]);
  },
)
```

## 3. 高级用法

### 3.1 下拉刷新和上拉加载

#### RefreshIndicator 下拉刷新

```dart
class RefreshListExample extends StatefulWidget {
  @override
  _RefreshListExampleState createState() => _RefreshListExampleState();
}

class _RefreshListExampleState extends State<RefreshListExample> {
  List<String> _items = List.generate(10, (index) => '项目 ${index + 1}');

  Future<void> _onRefresh() async {
    await Future.delayed(Duration(seconds: 1));  // 模拟网络请求
    setState(() {
      _items = List.generate(10, (index) => '新项目 ${index + 1}');
    });
  }

  @override
  Widget build(BuildContext context) {
    return RefreshIndicator(
      onRefresh: _onRefresh,
      child: ListView.builder(
        itemCount: _items.length,
        itemBuilder: (context, index) {
          return ListTile(title: Text(_items[index]));
        },
      ),
    );
  }
}
```

#### 上拉加载更多

```dart
class LoadMoreListExample extends StatefulWidget {
  @override
  _LoadMoreListExampleState createState() => _LoadMoreListExampleState();
}

class _LoadMoreListExampleState extends State<LoadMoreListExample> {
  final ScrollController _controller = ScrollController();
  List<String> _items = List.generate(20, (index) => '项目 ${index + 1}');
  bool _isLoading = false;
  bool _hasMore = true;

  @override
  void initState() {
    super.initState();
    _controller.addListener(_onScroll);
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  void _onScroll() {
    if (_controller.position.pixels >= _controller.position.maxScrollExtent - 100) {
      if (!_isLoading && _hasMore) {
        _loadMore();
      }
    }
  }

  Future<void> _loadMore() async {
    if (_isLoading || !_hasMore) return;

    setState(() => _isLoading = true);

    await Future.delayed(Duration(seconds: 1));

    setState(() {
      _items.addAll(List.generate(20, (index) => '项目 ${_items.length + index + 1}'));
      _isLoading = false;
      if (_items.length >= 100) _hasMore = false;  // 最大100条
    });
  }

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      controller: _controller,
      itemCount: _items.length + 1,  // +1 为加载指示器
      itemBuilder: (context, index) {
        if (index == _items.length) {
          return _buildLoadingIndicator();
        }
        return ListTile(title: Text(_items[index]));
      },
    );
  }

  Widget _buildLoadingIndicator() {
    if (!_hasMore) {
      return Padding(
        padding: EdgeInsets.all(16),
        child: Center(child: Text('没有更多数据了')),
      );
    }
    return Padding(
      padding: EdgeInsets.all(16),
      child: Center(child: CircularProgressIndicator()),
    );
  }
}
```

### 3.2 Sticky Header 吸顶效果

**方案1：使用第三方包 flutter_sticky_header**

需在 `pubspec.yaml` 添加依赖：
```yaml
dependencies:
  flutter_sticky_header: ^0.6.5
```

```dart
// 使用 flutter_sticky_header 包
class StickyHeaderList extends StatefulWidget {
  @override
  _StickyHeaderListState createState() => _StickyHeaderListState();
}

class _StickyHeaderListState extends State<StickyHeaderList> {
  final ScrollController _controller = ScrollController();
  final List<String> _headers = ['A', 'B', 'C', 'D', 'E'];

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      controller: _controller,
      slivers: _headers.map((header) {
        return SliverStickyHeader(
          header: Container(
            height: 50,
            color: Colors.blue,
            child: Center(
              child: Text(header, style: TextStyle(color: Colors.white, fontSize: 18)),
            ),
          ),
          sliver: SliverList(
            delegate: SliverChildBuilderDelegate(
              (context, index) => ListTile(title: Text('$header-${index + 1}')),
              childCount: 5,
            ),
          ),
        );
      }).toList(),
    );
  }
}

// 方案2：纯 Flutter 实现（无需第三方依赖）
class SimpleStickyHeader extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverPersistentHeader(
          pinned: true,  // 吸顶
          delegate: _StickyHeaderDelegate('标题'),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text('项目 $index')),
            childCount: 20,
          ),
        ),
      ],
    );
  }
}

class _StickyHeaderDelegate extends SliverPersistentHeaderDelegate {
  final String title;

  _StickyHeaderDelegate(this.title);

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.blue,
      child: Center(child: Text(title, style: TextStyle(color: Colors.white))),
    );
  }

  @override
  double get maxExtent => 50;

  @override
  double get minExtent => 50;

  @override
  bool shouldRebuild(covariant _StickyHeaderDelegate oldDelegate) {
    return title != oldDelegate.title;
  }
}
```

### 3.3 滑动删除和拖拽排序

#### Dismissible 滑动删除

```dart
class DismissibleList extends StatefulWidget {
  @override
  _DismissibleListState createState() => _DismissibleListState();
}

class _DismissibleListState extends State<DismissibleList> {
  List<String> _items = List.generate(10, (index) => '项目 ${index + 1}');

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: _items.length,
      itemBuilder: (context, index) {
        final item = _items[index];
        return Dismissible(
          key: Key(item),
          background: Container(
            color: Colors.red,
            alignment: Alignment.centerRight,
            padding: EdgeInsets.only(right: 20),
            child: Icon(Icons.delete, color: Colors.white),
          ),
          direction: DismissDirection.endToStart,
          onDismissed: (direction) {
            setState(() {
              _items.removeAt(index);
            });
            ScaffoldMessenger.of(context).showSnackBar(
              SnackBar(content: Text('已删除 $item')),
            );
          },
          child: ListTile(
            title: Text(item),
          ),
        );
      },
    );
  }
}
```

#### ReorderableListView 拖拽排序

```dart
class ReorderableListExample extends StatefulWidget {
  @override
  _ReorderableListExampleState createState() => _ReorderableListExampleState();
}

class _ReorderableListExampleState extends State<ReorderableListExample> {
  final List<String> _items = List.generate(10, (index) => '项目 ${index + 1}');

  @override
  Widget build(BuildContext context) {
    return ReorderableListView.builder(
      itemCount: _items.length,
      onReorder: (oldIndex, newIndex) {
        setState(() {
          if (oldIndex < newIndex) {
            newIndex -= 1;
          }
          final item = _items.removeAt(oldIndex);
          _items.insert(newIndex, item);
        });
      },
      itemBuilder: (context, index) {
        return ListTile(
          key: ValueKey(_items[index]),
          title: Text(_items[index]),
          trailing: Icon(Icons.drag_handle),
        );
      },
    );
  }
}
```

### 3.4 分组列表

```dart
class GroupedList extends StatelessWidget {
  final Map<String, List<String>> _data = {
    'A': ['Apple', 'Avocado', 'Apricot'],
    'B': ['Banana', 'Blueberry', 'Blackberry'],
    'C': ['Cherry', 'Coconut', 'Cranberry'],
  };

  @override
  Widget build(BuildContext context) {
    return ListView(
      children: _data.entries.expand((entry) {
        return [
          Container(
            padding: EdgeInsets.all(12),
            color: Colors.grey[200],
            child: Text(
              entry.key,
              style: TextStyle(fontWeight: FontWeight.bold, fontSize: 16),
            ),
          ),
          ...entry.value.map((item) => ListTile(title: Text(item))),
        ];
      }).toList(),
    );
  }
}
```

### 3.5 带索引的字母列表

```dart
class AlphabetIndexList extends StatefulWidget {
  @override
  _AlphabetIndexListState createState() => _AlphabetIndexListState();
}

class _AlphabetIndexListState extends State<AlphabetIndexList> {
  final ScrollController _controller = ScrollController();
  final Map<String, List<String>> _data = {
    'A': ['Apple', 'Avocado'],
    'B': ['Banana', 'Blueberry'],
    'C': ['Cherry', 'Coconut'],
    'D': ['Dragonfruit'],
    'E': ['Elderberry'],
    'F': ['Fig', 'Feijoa'],
  };

  final List<String> _alphabet = List.generate(26, (i) => String.fromCharCode(65 + i));

  void _scrollToLetter(String letter) {
    final index = _data.keys.toList().indexOf(letter);
    if (index != -1) {
      final offset = index * 200.0;  // 估算偏移
      _controller.animateTo(
        offset,
        duration: Duration(milliseconds: 300),
        curve: Curves.ease,
      );
    }
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        ListView.builder(
          controller: _controller,
          itemCount: _data.length,
          itemBuilder: (context, index) {
            final letter = _data.keys.elementAt(index);
            final items = _data[letter]!;
            return Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Container(
                  padding: EdgeInsets.all(8),
                  color: Colors.grey[200],
                  child: Text(letter, style: TextStyle(fontWeight: FontWeight.bold)),
                ),
                ...items.map((item) => ListTile(title: Text(item))),
              ],
            );
          },
        ),
        Positioned(
          right: 0,
          top: 0,
          bottom: 0,
          child: Container(
            width: 30,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: _alphabet.map((letter) {
                return GestureDetector(
                  onTap: () => _scrollToLetter(letter),
                  child: Container(
                    width: 20,
                    height: 16,
                    child: Text(letter, style: TextStyle(fontSize: 10)),
                  ),
                );
              }).toList(),
            ),
          ),
        ),
      ],
    );
  }
}
```

## 4. 性能优化

### 4.1 选择正确的构造函数

| 场景 | 推荐构造函数 |
|------|-------------|
| 少量固定子组件（< 20个） | `ListView()` |
| 大量或无限子组件 | `ListView.builder()` |
| 需要分隔组件 | `ListView.separated()` |
| 高度固定的子组件 | `ListView.builder()` + `itemExtent` |

### 4.2 使用 itemExtent 提升性能

```dart
// ✅ 性能最佳：固定高度
ListView.builder(
  itemExtent: 56,
  itemCount: 10000,
  itemBuilder: (context, index) => ListTile(title: Text('项目 $index')),
)

// 如果高度不完全固定但有最大值，使用 prototypeItem
ListView.builder(
  prototypeItem: ListTile(title: Text('')),
  itemCount: 10000,
  itemBuilder: (context, index) => ListTile(title: Text('项目 $index')),
)
```

### 4.3 避免在 itemBuilder 中创建复杂对象

```dart
// ❌ 每次构建都创建新对象
ListView.builder(
  itemBuilder: (context, index) {
    return ListTile(
      leading: CircleAvatar(child: Text('$index')),  // 每次创建
    );
  },
)

// ✅ 使用 const 构造函数
ListView.builder(
  itemBuilder: (context, index) {
    return ListTile(
      leading: CircleAvatar(child: Text('$index')),
    );
  },
)

// ✅ 更好：提取为独立的 StatelessWidget
class MyListItem extends StatelessWidget {
  final int index;

  const MyListItem({required this.index, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ListTile(
      leading: CircleAvatar(child: Text('$index')),
      title: Text('项目 $index'),
    );
  }
}

ListView.builder(
  itemBuilder: (context, index) => MyListItem(index: index),
)
```

### 4.4 使用 addAutomaticKeepAlives

```dart
// 默认 true，会保持已滚出视图的子组件状态
// 如果子组件是无状态的，可以设为 false 提升性能
ListView.builder(
  addAutomaticKeepAlives: false,  // 适用于纯展示列表
  itemCount: 1000,
  itemBuilder: (context, index) => ListTile(title: Text('项目 $index')),
)
```

### 4.5 嵌套优化

```dart
// ❌ 避免嵌套 ListView
Column(
  children: [
    ListView(...),  // 需要设置 shrinkWrap，性能差
    ListView(...),
  ],
)

// ✅ 使用 CustomScrollView + Sliver
CustomScrollView(
  slivers: [
    SliverList(delegate: SliverChildBuilderDelegate(...)),
    SliverList(delegate: SliverChildBuilderDelegate(...)),
  ],
)

// 或者合并为一个列表
ListView.builder(
  itemBuilder: (context, index) {
    if (index < 10) return _buildFirstSectionItem(index);
    return _buildSecondSectionItem(index - 10);
  },
  itemCount: 20,
)
```

## 5. 常见问题与解决方案

### 问题1：ListView 高度无限错误

**错误信息**：`RenderFlex children have non-zero flex but incoming height constraints are unbounded`

**原因**：ListView 在无约束的环境中（如另一个 ListView 或 Column）没有设置 shrinkWrap

```dart
// ❌ 错误
Column(
  children: [
    ListView(...),  // 高度无限
  ],
)

// ✅ 解决方案1：使用 Expanded
Column(
  children: [
    Expanded(
      child: ListView(...),
    ),
  ],
)

// ✅ 解决方案2：使用 shrinkWrap（适用于少量数据）
Column(
  children: [
    ListView(
      shrinkWrap: true,
      physics: NeverScrollableScrollPhysics(),
      children: [...],
    ),
  ],
)
```

### 问题2：列表滚动冲突

**原因**：嵌套滚动组件时，滚动事件冲突

```dart
// 解决方案：内层设置 NeverScrollableScrollPhysics
Column(
  children: [
    Expanded(
      child: ListView(
        children: [
          Container(height: 200, child: ListView(
            physics: NeverScrollableScrollPhysics(),  // 禁用内层滚动
            children: [...],
          )),
        ],
      ),
    ),
  ],
)

// 或使用 CustomScrollView 统一管理
CustomScrollView(
  slivers: [
    SliverList(delegate: ...),
    SliverGrid(delegate: ..., gridDelegate: ...),
  ],
)
```

### 问题3：列表滚动位置丢失

**原因**：页面切换时 ListView 重建

```dart
// 解决方案：使用 PageStorageKey
ListView.builder(
  key: const PageStorageKey<String>('my_list'),
  itemBuilder: (context, index) => ListTile(title: Text('项目 $index')),
)

// 或使用 AutomaticKeepAliveClientMixin
class MyListItem extends StatefulWidget {
  @override
  _MyListItemState createState() => _MyListItemState();
}

class _MyListItemState extends State<MyListItem> with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);
    return ListTile(...);
  }
}
```

### 问题4：键盘弹出导致溢出

**解决方案**：

```dart
// 使用 resizeToAvoidBottomInset
Scaffold(
  resizeToAvoidBottomInset: true,
  body: ListView(
    padding: EdgeInsets.all(16),
    children: [
      TextField(...),
      // ...
    ],
  ),
)

// 或者使用 SingleChildScrollView 替代
Scaffold(
  body: SingleChildScrollView(
    child: Column(
      children: [
        TextField(...),
      ],
    ),
  ),
)
```

## 6. ListView vs 其他滚动组件

| 组件 | 特点 | 适用场景 |
|------|------|---------|
| **ListView** | 线性列表 | 简单的垂直/水平列表 |
| **GridView** | 网格布局 | 需要多列展示 |
| **CustomScrollView** | 自定义滚动 | 复杂布局、多种 Sliver 组合 |
| **SingleChildScrollView** | 单子组件滚动 | 内容较少、表单页面 |
| **PageView** | 页面滑动 | 轮播图、引导页 |

## 总结

ListView 是 Flutter 中最重要的滚动组件之一：

**核心要点**：
1. 四种构造函数：`ListView()`、`ListView.builder()`、`ListView.separated()`、`ListView.custom()`
2. 滚动控制：`ScrollController`
3. 性能优化：`itemExtent`、正确的构造函数选择

**最佳实践**：
1. 大量数据使用 `ListView.builder()`
2. 固定高度使用 `itemExtent`
3. 嵌套时使用 `CustomScrollView` + `Sliver`
4. 避免在 itemBuilder 中创建复杂对象

**常见场景**：
- 数据列表展示
- 下拉刷新/上拉加载
- 滑动删除/拖拽排序
- 分组列表

---

*最后更新: 2026-02-18*

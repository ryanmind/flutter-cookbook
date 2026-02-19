# Flutter SliverList 组件用法详解

## 简介

SliverList 是 Flutter 中用于在 CustomScrollView 中显示列表的组件。它按需构建列表项，支持大量数据的懒加载。

## 1. 初级用法

### 1.1 基本概念

SliverList 的核心作用：
- 在 CustomScrollView 中显示列表
- 支持懒加载，按需构建
- 适合大量数据的列表

### 1.2 基本语法

```dart
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => ListTile(title: Text('Item $index')),
    childCount: 100,
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `delegate` | `SliverChildDelegate` | 子组件代理 |

### 1.4 Delegate 类型

| Delegate | 说明 |
|----------|------|
| `SliverChildBuilderDelegate` | 按需构建，适合大量数据 |
| `SliverChildListDelegate` | 一次性构建所有子组件 |

### 1.5 基础示例

#### 使用 Builder

```dart
CustomScrollView(
  slivers: [
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) {
          return ListTile(
            title: Text('Item $index'),
            trailing: Icon(Icons.arrow_forward),
          );
        },
        childCount: 50,
      ),
    ),
  ],
)
```

#### 使用 List

```dart
SliverList(
  delegate: SliverChildListDelegate([
    ListTile(title: Text('Item 0')),
    ListTile(title: Text('Item 1')),
    ListTile(title: Text('Item 2')),
  ]),
)
```

## 2. 中级用法

### 2.1 列表头部

```dart
CustomScrollView(
  slivers: [
    // 头部区域
    SliverToBoxAdapter(
      child: Container(
        height: 200,
        color: Colors.blue,
        child: Center(child: Text('Header')),
      ),
    ),
    // 列表区域
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 20,
      ),
    ),
  ],
)
```

### 2.2 分组列表

```dart
class GroupedList extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        _buildGroup('Group A', 5),
        _buildGroup('Group B', 3),
        _buildGroup('Group C', 8),
      ],
    );
  }

  Widget _buildGroup(String title, int count) {
    return SliverMainAxisGroup(
      slivers: [
        SliverToBoxAdapter(
          child: Container(
            padding: EdgeInsets.all(8),
            color: Colors.grey[200],
            child: Text(title, style: TextStyle(fontWeight: FontWeight.bold)),
          ),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) => ListTile(title: Text('$title - Item $index')),
            childCount: count,
          ),
        ),
      ],
    );
  }
}
```

### 2.3 带分割线

```dart
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) {
      return Column(
        children: [
          ListTile(title: Text('Item $index')),
          Divider(height: 1),
        ],
      );
    },
    childCount: 20,
  ),
)
```

### 2.4 不同类型列表项

```dart
class MixedList extends StatelessWidget {
  final List<dynamic> items = [
    {'type': 'header', 'title': 'Header 1'},
    {'type': 'item', 'title': 'Item 1'},
    {'type': 'item', 'title': 'Item 2'},
    {'type': 'header', 'title': 'Header 2'},
    {'type': 'item', 'title': 'Item 3'},
  ];

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) {
              final item = items[index];
              if (item['type'] == 'header') {
                return Container(
                  padding: EdgeInsets.all(12),
                  color: Colors.blue[100],
                  child: Text(
                    item['title']!,
                    style: TextStyle(fontWeight: FontWeight.bold),
                  ),
                );
              }
              return ListTile(title: Text(item['title']!));
            },
            childCount: items.length,
          ),
        ),
      ],
    );
  }
}
```

### 2.5 列表尾部

```dart
CustomScrollView(
  slivers: [
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 20,
      ),
    ),
    // 底部加载更多
    SliverToBoxAdapter(
      child: Container(
        padding: EdgeInsets.all(16),
        child: Center(child: CircularProgressIndicator()),
      ),
    ),
  ],
)
```

## 3. 高级用法

### 3.1 无限滚动

```dart
class InfiniteList extends StatefulWidget {
  @override
  State<InfiniteList> createState() => _InfiniteListState();
}

class _InfiniteListState extends State<InfiniteList> {
  List<String> _items = [];
  bool _isLoading = false;
  final ScrollController _scrollController = ScrollController();

  @override
  void initState() {
    super.initState();
    _loadMore();
    _scrollController.addListener(_onScroll);
  }

  @override
  void dispose() {
    _scrollController.dispose();
    super.dispose();
  }

  void _onScroll() {
    if (_scrollController.position.pixels >=
        _scrollController.position.maxScrollExtent - 200) {
      _loadMore();
    }
  }

  Future<void> _loadMore() async {
    if (_isLoading) return;
    setState(() => _isLoading = true);
    
    await Future.delayed(Duration(seconds: 1));
    
    setState(() {
      _items.addAll(List.generate(20, (i) => 'Item ${_items.length + i}'));
      _isLoading = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      controller: _scrollController,
      slivers: [
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) {
              if (index < _items.length) {
                return ListTile(title: Text(_items[index]));
              }
              return null;
            },
            childCount: _items.length + (_isLoading ? 1 : 0),
          ),
        ),
        if (_isLoading)
          SliverToBoxAdapter(
            child: Padding(
              padding: EdgeInsets.all(16),
              child: Center(child: CircularProgressIndicator()),
            ),
          ),
      ],
    );
  }
}
```

### 3.2 下拉刷新

```dart
class RefreshableList extends StatelessWidget {
  final List<String> items;

  const RefreshableList({required this.items, Key? key}) : super(key: key);

  Future<void> _onRefresh() async {
    await Future.delayed(Duration(seconds: 1));
  }

  @override
  Widget build(BuildContext context) {
    return RefreshIndicator(
      onRefresh: _onRefresh,
      child: CustomScrollView(
        slivers: [
          SliverList(
            delegate: SliverChildBuilderDelegate(
              (context, index) => ListTile(title: Text(items[index])),
              childCount: items.length,
            ),
          ),
        ],
      ),
    );
  }
}
```

### 3.3 列表动画

```dart
class AnimatedSliverList extends StatefulWidget {
  @override
  State<AnimatedSliverList> createState() => _AnimatedSliverListState();
}

class _AnimatedSliverListState extends State<AnimatedSliverList> {
  final List<String> _items = List.generate(10, (i) => 'Item $i');

  void _removeItem(int index) {
    setState(() => _items.removeAt(index));
  }

  void _addItem() {
    setState(() => _items.insert(0, 'New Item ${DateTime.now().second}'));
  }

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverPersistentHeader(
          pinned: true,
          delegate: _HeaderDelegate(
            child: ElevatedButton(
              onPressed: _addItem,
              child: Text('Add Item'),
            ),
          ),
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) {
              return Dismissible(
                key: Key(_items[index]),
                onDismissed: (_) => _removeItem(index),
                child: ListTile(
                  title: Text(_items[index]),
                ),
              );
            },
            childCount: _items.length,
          ),
        ),
      ],
    );
  }
}

class _HeaderDelegate extends SliverPersistentHeaderDelegate {
  final Widget child;

  _HeaderDelegate({required this.child});

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.white,
      child: child,
    );
  }

  @override
  double get maxExtent => 60;

  @override
  double get minExtent => 60;

  @override
  bool shouldRebuild(covariant _HeaderDelegate oldDelegate) => child != oldDelegate.child;
}
```

### 3.4 粘性头部

```dart
class StickyHeaderList extends StatelessWidget {
  final List<String> letters = List.generate(26, (i) => String.fromCharCode(65 + i));

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: letters.map((letter) {
        return SliverMainAxisGroup(
          slivers: [
            SliverPersistentHeader(
              pinned: true,
              delegate: _StickyHeaderDelegate(letter),
            ),
            SliverList(
              delegate: SliverChildBuilderDelegate(
                (context, index) => ListTile(title: Text('$letter$index')),
                childCount: 5,
              ),
            ),
          ],
        );
      }).toList(),
    );
  }
}

class _StickyHeaderDelegate extends SliverPersistentHeaderDelegate {
  final String letter;

  _StickyHeaderDelegate(this.letter);

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.grey[300],
      padding: EdgeInsets.symmetric(horizontal: 16),
      child: Align(
        alignment: Alignment.centerLeft,
        child: Text(
          letter,
          style: TextStyle(fontWeight: FontWeight.bold, fontSize: 18),
        ),
      ),
    );
  }

  @override
  double get maxExtent => 40;

  @override
  double get minExtent => 40;

  @override
  bool shouldRebuild(covariant _StickyHeaderDelegate oldDelegate) => letter != oldDelegate.letter;
}
```

## 4. 常见问题与解决方案

### 问题1：列表项不显示

```dart
// 确保 childCount 正确
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => ListTile(title: Text('Item $index')),
    childCount: 10,  // 必须设置
  ),
)
```

### 问题2：性能问题

```dart
// 使用 SliverChildBuilderDelegate 而非 SliverChildListDelegate
// 懒加载

SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => ListTile(title: Text('Item $index')),
    childCount: 1000,  // 大量数据
    addAutomaticKeepAlives: true,
  ),
)
```

### 问题3：与 ListView 的区别

```dart
// ListView 独立使用
ListView.builder(...)

// SliverList 在 CustomScrollView 中使用
CustomScrollView(
  slivers: [
    SliverList(...),
  ],
)
```

## 5. SliverList vs 其他列表组件

| 组件 | 使用场景 | 特点 |
|------|----------|------|
| **SliverList** | CustomScrollView | 可与其他 Sliver 组合 |
| **ListView** | 独立列表 | 简单易用 |
| **SliverGrid** | 网格列表 | 在 CustomScrollView 中 |
| **SliverChildBuilderDelegate** | 大量数据 | 懒加载 |

### 选择建议

```dart
// 需要组合多种滚动效果 → SliverList
CustomScrollView(slivers: [SliverAppBar(...), SliverList(...)])

// 简单列表 → ListView
ListView.builder(...)

// 网格列表 → SliverGrid
SliverGrid(delegate: ..., gridDelegate: ...)
```

## 6. 最佳实践

### 6.1 使用 Builder

```dart
// ✅ 推荐：使用 Builder 懒加载
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => ListTile(title: Text('Item $index')),
    childCount: 1000,
  ),
)

// ❌ 避免：一次性构建所有
SliverList(
  delegate: SliverChildListDelegate(
    List.generate(1000, (i) => ListTile(title: Text('Item $i'))),
  ),
)
```

### 6.2 复用 Widget

```dart
class MyListItem extends StatelessWidget {
  final String title;

  const MyListItem({required this.title, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ListTile(title: Text(title));
  }
}

SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => MyListItem(title: 'Item $index'),
    childCount: 100,
  ),
)
```

### 6.3 条件渲染

```dart
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) {
      // 返回 null 会跳过该项
      if (index == specialIndex) return null;
      return ListTile(title: Text('Item $index'));
    },
    childCount: items.length,
  ),
)
```

## 总结

SliverList 是 CustomScrollView 中显示列表的核心组件：

**核心要点**：
1. 配合 CustomScrollView 使用
2. 使用 SliverChildBuilderDelegate 懒加载
3. 可与其他 Sliver 组件组合

**最佳实践**：
1. 大量数据使用 Builder
2. 合理设置 childCount
3. 使用 addAutomaticKeepAlives

**常见场景**：
- 无限滚动列表
- 分组列表
- 带头部/尾部的列表

---

*最后更新: 2026-02-19*

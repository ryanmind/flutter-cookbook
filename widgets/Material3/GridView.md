# Flutter GridView 组件用法详解

## 简介

GridView 是 Flutter 中的网格布局组件，用于将子组件按网格形式排列。它支持滚动、多种构造方式，非常适合展示图片网格、商品列表、应用图标等需要多列展示的场景。

## 1. 初级用法

### 1.1 基本概念

GridView 的核心特点：
- 子组件按网格排列
- 支持垂直和水平滚动
- 提供多种构造函数适应不同场景
- 可自定义网格布局规则

### 1.2 基本语法

```dart
GridView.count(
  crossAxisCount: 2,  // 每行两列
  children: [
    Container(color: Colors.red),
    Container(color: Colors.green),
    Container(color: Colors.blue),
    Container(color: Colors.orange),
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
| `gridDelegate` | `SliverGridDelegate` | - | 网格布局代理 |

### 1.4 SliverGridDelegate 属性

通过 `gridDelegate` 控制网格布局：

| 属性 | 说明 |
|------|------|
| `crossAxisCount` | 交叉轴方向的子组件数量 |
| `mainAxisSpacing` | 主轴方向的间距 |
| `crossAxisSpacing` | 交叉轴方向的间距 |
| `childAspectRatio` | 子组件宽高比 |

### 1.5 基础示例

#### 简单网格

```dart
GridView.count(
  crossAxisCount: 3,  // 每行 3 个
  mainAxisSpacing: 10,  // 行间距
  crossAxisSpacing: 10,  // 列间距
  padding: EdgeInsets.all(10),
  children: List.generate(12, (index) {
    return Container(
      color: Colors.primaries[index % Colors.primaries.length],
      child: Center(
        child: Text(
          'Item ${index + 1}',
          style: TextStyle(color: Colors.white, fontSize: 18),
        ),
      ),
    );
  }),
)
```

#### 图片网格

```dart
GridView.count(
  crossAxisCount: 2,
  mainAxisSpacing: 8,
  crossAxisSpacing: 8,
  padding: EdgeInsets.all(8),
  children: List.generate(10, (index) {
    return ClipRRect(
      borderRadius: BorderRadius.circular(8),
      child: Image.network(
        'https://picsum.photos/200/200?random=$index',
        fit: BoxFit.cover,
      ),
    );
  }),
)
```

## 2. 中级用法

### 2.1 四种构造函数

#### GridView() - 默认构造函数

使用 `gridDelegate` 自定义网格布局：

```dart
GridView(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    mainAxisSpacing: 10,
    crossAxisSpacing: 10,
    childAspectRatio: 1.0,
  ),
  children: [
    Container(color: Colors.red),
    Container(color: Colors.green),
    Container(color: Colors.blue),
    Container(color: Colors.orange),
  ],
)
```

#### GridView.count() - 固定列数

最常用的构造函数，指定列数：

```dart
GridView.count(
  crossAxisCount: 3,
  mainAxisSpacing: 8,
  crossAxisSpacing: 8,
  padding: EdgeInsets.all(8),
  children: [...],
)
```

#### GridView.extent() - 最大宽度

指定子组件的最大宽度，自动计算列数：

```dart
GridView.extent(
  maxCrossAxisExtent: 150,  // 每个子组件最大宽度 150
  mainAxisSpacing: 8,
  crossAxisSpacing: 8,
  padding: EdgeInsets.all(8),
  children: [...],
)
```

#### GridView.builder() - 按需构建

适用于大量数据，按需构建子组件：

```dart
GridView.builder(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    mainAxisSpacing: 10,
    crossAxisSpacing: 10,
  ),
  itemCount: 100,
  itemBuilder: (context, index) {
    return Container(
      color: Colors.primaries[index % Colors.primaries.length],
      child: Center(child: Text('Item ${index + 1}')),
    );
  },
)
```

### 2.2 childAspectRatio 宽高比

控制子组件的宽高比例：

```dart
GridView.count(
  crossAxisCount: 2,
  childAspectRatio: 1.0,  // 宽高相等（正方形）
  // childAspectRatio: 0.8,  // 宽比高大（竖长）
  // childAspectRatio: 1.5,  // 高比宽大（扁平）
  children: [...],
)
```

### 2.3 滚动控制

```dart
class GridViewWithScroll extends StatefulWidget {
  @override
  _GridViewWithScrollState createState() => _GridViewWithScrollState();
}

class _GridViewWithScrollState extends State<GridViewWithScroll> {
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
    setState(() {
      _showButton = _controller.offset > 200;
    });
  }

  void _scrollToTop() {
    _controller.animateTo(
      0,
      duration: Duration(milliseconds: 300),
      curve: Curves.easeOut,
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: GridView.builder(
        controller: _controller,
        gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 2,
          mainAxisSpacing: 10,
          crossAxisSpacing: 10,
        ),
        itemCount: 50,
        itemBuilder: (context, index) {
          return Container(
            color: Colors.primaries[index % Colors.primaries.length],
            child: Center(child: Text('Item ${index + 1}')),
          );
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

### 2.4 响应式列数

```dart
class ResponsiveGridView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        // 根据宽度计算列数
        int crossAxisCount = 2;
        if (constraints.maxWidth > 600) crossAxisCount = 3;
        if (constraints.maxWidth > 900) crossAxisCount = 4;
        if (constraints.maxWidth > 1200) crossAxisCount = 5;

        return GridView.builder(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: crossAxisCount,
            mainAxisSpacing: 10,
            crossAxisSpacing: 10,
            childAspectRatio: 0.8,
          ),
          itemCount: 20,
          itemBuilder: (context, index) {
            return Card(
              child: Column(
                children: [
                  Expanded(
                    child: Container(
                      color: Colors.primaries[index % Colors.primaries.length],
                    ),
                  ),
                  Padding(
                    padding: EdgeInsets.all(8),
                    child: Text('产品 ${index + 1}'),
                  ),
                ],
              ),
            );
          },
        );
      },
    );
  }
}
```

### 2.5 下拉刷新

```dart
class RefreshableGridView extends StatefulWidget {
  @override
  _RefreshableGridViewState createState() => _RefreshableGridViewState();
}

class _RefreshableGridViewState extends State<RefreshableGridView> {
  List<int> _items = List.generate(20, (index) => index);

  Future<void> _onRefresh() async {
    await Future.delayed(Duration(seconds: 1));
    setState(() {
      _items = List.generate(20, (index) => index);
    });
  }

  @override
  Widget build(BuildContext context) {
    return RefreshIndicator(
      onRefresh: _onRefresh,
      child: GridView.builder(
        gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 2,
          mainAxisSpacing: 10,
          crossAxisSpacing: 10,
        ),
        itemCount: _items.length,
        itemBuilder: (context, index) {
          return Container(
            color: Colors.primaries[index % Colors.primaries.length],
            child: Center(child: Text('Item ${index + 1}')),
          );
        },
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 自定义 SliverGridDelegate

```dart
class CustomGridDelegate extends SliverGridDelegate {
  final int crossAxisCount;
  final double mainAxisSpacing;
  final double crossAxisSpacing;
  final double childAspectRatio;

  CustomGridDelegate({
    required this.crossAxisCount,
    this.mainAxisSpacing = 0.0,
    this.crossAxisSpacing = 0.0,
    this.childAspectRatio = 1.0,
  });

  @override
  SliverGridLayout getLayout(SliverConstraints constraints) {
    double tileWidth = (constraints.crossAxisExtent - crossAxisSpacing * (crossAxisCount - 1)) / crossAxisCount;
    double tileHeight = tileWidth / childAspectRatio;

    return SliverGridRegularTileLayout(
      crossAxisCount: crossAxisCount,
      mainAxisStride: tileHeight + mainAxisSpacing,
      crossAxisStride: tileWidth + crossAxisSpacing,
      childMainAxisExtent: tileHeight,
      childCrossAxisExtent: tileWidth,
      reverseCrossAxis: axisDirectionIsReversed(constraints.crossAxisDirection),
    );
  }

  @override
  bool shouldRelayout(covariant CustomGridDelegate oldDelegate) {
    return oldDelegate.crossAxisCount != crossAxisCount ||
        oldDelegate.mainAxisSpacing != mainAxisSpacing ||
        oldDelegate.crossAxisSpacing != crossAxisSpacing ||
        oldDelegate.childAspectRatio != childAspectRatio;
  }
}
```

### 3.2 瀑布流布局（使用第三方包）

使用 `flutter_staggered_grid_view` 包实现瀑布流：

```yaml
# pubspec.yaml
dependencies:
  flutter_staggered_grid_view: ^0.7.0
```

```dart
import 'package:flutter_staggered_grid_view/flutter_staggered_grid_view.dart';

MasonryGridView.count(
  crossAxisCount: 2,
  mainAxisSpacing: 8,
  crossAxisSpacing: 8,
  itemCount: 20,
  itemBuilder: (context, index) {
    return Container(
      height: 100 + (index % 3) * 50,  // 不同高度
      color: Colors.primaries[index % Colors.primaries.length],
      child: Center(child: Text('Item ${index + 1}')),
    );
  },
)
```

### 3.3 嵌套在 CustomScrollView 中

```dart
CustomScrollView(
  slivers: [
    // 顶部标题
    SliverToBoxAdapter(
      child: Container(
        height: 100,
        color: Colors.blue,
        child: Center(child: Text('Header')),
      ),
    ),
    // 网格内容
    SliverGrid(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 2,
        mainAxisSpacing: 10,
        crossAxisSpacing: 10,
      ),
      delegate: SliverChildBuilderDelegate(
        (context, index) {
          return Container(
            color: Colors.primaries[index % Colors.primaries.length],
            child: Center(child: Text('Item ${index + 1}')),
          );
        },
        childCount: 20,
      ),
    ),
    // 底部加载更多
    SliverToBoxAdapter(
      child: Container(
        height: 50,
        child: Center(child: CircularProgressIndicator()),
      ),
    ),
  ],
)
```

### 3.4 上拉加载更多

```dart
class LoadMoreGridView extends StatefulWidget {
  @override
  _LoadMoreGridViewState createState() => _LoadMoreGridViewState();
}

class _LoadMoreGridViewState extends State<LoadMoreGridView> {
  final ScrollController _controller = ScrollController();
  List<int> _items = List.generate(20, (index) => index);
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
      _items.addAll(List.generate(20, (index) => _items.length + index));
      _isLoading = false;
      if (_items.length >= 100) _hasMore = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      controller: _controller,
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 2,
        mainAxisSpacing: 10,
        crossAxisSpacing: 10,
      ),
      itemCount: _items.length + 1,
      itemBuilder: (context, index) {
        if (index == _items.length) {
          return Center(
            child: _hasMore
                ? CircularProgressIndicator()
                : Text('没有更多了'),
          );
        }
        return Container(
          color: Colors.primaries[index % Colors.primaries.length],
          child: Center(child: Text('Item ${index + 1}')),
        );
      },
    );
  }
}
```

### 3.5 带筛选的网格

```dart
class FilterableGridView extends StatefulWidget {
  @override
  _FilterableGridViewState createState() => _FilterableGridViewState();
}

class _FilterableGridViewState extends State<FilterableGridView> {
  final List<String> _categories = ['全部', '红色', '绿色', '蓝色'];
  int _selectedIndex = 0;

  final List<Map<String, dynamic>> _allItems = List.generate(50, (index) {
    final colors = ['红色', '绿色', '蓝色'];
    return {
      'id': index,
      'category': colors[index % 3],
      'color': Colors.primaries[index % Colors.primaries.length],
    };
  });

  List<Map<String, dynamic>> get _filteredItems {
    if (_selectedIndex == 0) return _allItems;
    return _allItems.where((item) => item['category'] == _categories[_selectedIndex]).toList();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // 筛选栏
        SingleChildScrollView(
          scrollDirection: Axis.horizontal,
          padding: EdgeInsets.symmetric(horizontal: 8, vertical: 8),
          child: Row(
            children: List.generate(_categories.length, (index) {
              return Padding(
                padding: EdgeInsets.symmetric(horizontal: 4),
                child: FilterChip(
                  label: Text(_categories[index]),
                  selected: _selectedIndex == index,
                  onSelected: (selected) {
                    setState(() => _selectedIndex = index);
                  },
                ),
              );
            }),
          ),
        ),
        // 网格
        Expanded(
          child: GridView.builder(
            padding: EdgeInsets.all(8),
            gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
              crossAxisCount: 2,
              mainAxisSpacing: 8,
              crossAxisSpacing: 8,
              childAspectRatio: 0.8,
            ),
            itemCount: _filteredItems.length,
            itemBuilder: (context, index) {
              final item = _filteredItems[index];
              return Card(
                child: Column(
                  children: [
                    Expanded(
                      child: Container(color: item['color']),
                    ),
                    Padding(
                      padding: EdgeInsets.all(8),
                      child: Text('Item ${item['id']}'),
                    ),
                  ],
                ),
              );
            },
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
| 少量固定子组件 | `GridView.count()` |
| 大量数据 | `GridView.builder()` |
| 自定义列宽 | `GridView.extent()` |
| 完全自定义 | `GridView()` + 自定义 delegate |

### 4.2 使用 cacheExtent

```dart
GridView.builder(
  cacheExtent: 500,  // 预缓存区域大小
  gridDelegate: ...,
  itemBuilder: ...,
)
```

### 4.3 使用 const 和状态管理

```dart
class GridItem extends StatelessWidget {
  final int index;

  const GridItem({required this.index, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      color: Colors.primaries[index % Colors.primaries.length],
      child: Center(child: Text('Item ${index + 1}')),
    );
  }
}

GridView.builder(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
  ),
  itemCount: 100,
  itemBuilder: (context, index) => GridItem(index: index),
)
```

### 4.4 避免不必要的重建

```dart
class OptimizedGridView extends StatelessWidget {
  final List<String> items;

  const OptimizedGridView({required this.items, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 2,
        mainAxisSpacing: 10,
        crossAxisSpacing: 10,
      ),
      itemCount: items.length,
      itemBuilder: (context, index) {
        return _GridItem(title: items[index]);
      },
    );
  }
}

class _GridItem extends StatelessWidget {
  final String title;

  const _GridItem({required this.title});

  @override
  Widget build(BuildContext context) {
    return Card(child: Center(child: Text(title)));
  }
}
```

## 5. 常见问题与解决方案

### 问题1：GridView 高度无限错误

```dart
// ❌ 错误：在 Column 中使用 GridView
Column(
  children: [
    GridView(...),  // 高度无限
  ],
)

// ✅ 解决方案1：使用 Expanded
Column(
  children: [
    Expanded(
      child: GridView(...),
    ),
  ],
)

// ✅ 解决方案2：使用 shrinkWrap
Column(
  children: [
    GridView(
      shrinkWrap: true,
      physics: NeverScrollableScrollPhysics(),
      children: [...],
    ),
  ],
)
```

### 问题2：子组件尺寸异常

```dart
// 问题：childAspectRatio 设置不当导致变形
GridView.count(
  crossAxisCount: 2,
  childAspectRatio: 2.0,  // 宽度是高度的2倍
  children: [...],
)

// 解决方案：根据内容调整比例
GridView.count(
  crossAxisCount: 2,
  childAspectRatio: 0.75,  // 根据实际需求调整
  children: [...],
)
```

### 问题3：网格滚动冲突

```dart
// 嵌套滚动时冲突
Column(
  children: [
    ListView(...),
    GridView(...),  // 滚动冲突
  ],
)

// 解决方案：使用 CustomScrollView
CustomScrollView(
  slivers: [
    SliverList(delegate: ...),
    SliverGrid(gridDelegate: ..., delegate: ...),
  ],
)
```

### 问题4：间距计算错误

```dart
// 注意：间距会影响子组件的实际尺寸
// 子组件宽度 = (总宽度 - padding - crossAxisSpacing * (列数-1)) / 列数
GridView.count(
  crossAxisCount: 2,
  crossAxisSpacing: 10,  // 列间距
  mainAxisSpacing: 10,   // 行间距
  padding: EdgeInsets.all(10),  // 内边距
  children: [...],
)
```

## 6. GridView vs 其他组件对比

| 组件 | 特点 | 适用场景 |
|------|------|---------|
| **GridView** | 网格布局，滚动 | 图片网格、商品列表 |
| **ListView** | 列表布局，滚动 | 单列列表 |
| **Wrap** | 流式布局，不滚动 | 标签云 |
| **Table** | 表格布局 | 数据表格 |
| **Row/Column** | 线性布局 | 固定子组件 |

## 7. 最佳实践

### 7.1 响应式列数

```dart
int _calculateColumns(double width) {
  if (width > 1200) return 4;
  if (width > 900) return 3;
  if (width > 600) return 2;
  return 1;
}

LayoutBuilder(
  builder: (context, constraints) {
    return GridView.count(
      crossAxisCount: _calculateColumns(constraints.maxWidth),
      children: [...],
    );
  },
)
```

### 7.2 合理的间距和内边距

```dart
GridView.count(
  crossAxisCount: 2,
  mainAxisSpacing: 16,  // 根据设计规范
  crossAxisSpacing: 16,
  padding: EdgeInsets.all(16),
  children: [...],
)
```

### 7.3 使用 Card 增强视觉效果

```dart
GridView.builder(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    mainAxisSpacing: 16,
    crossAxisSpacing: 16,
  ),
  itemBuilder: (context, index) {
    return Card(
      elevation: 4,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(12),
      ),
      child: ...,
    );
  },
)
```

## 总结

GridView 是 Flutter 中展示网格数据的核心组件：

**核心要点**：
1. 四种构造函数：`GridView()`、`count()`、`extent()`、`builder()`
2. `gridDelegate` 控制网格布局规则
3. `childAspectRatio` 控制子组件宽高比
4. 支持滚动控制和刷新

**常见场景**：
- 图片相册
- 商品列表
- 应用图标
- 卡片布局

**最佳实践**：
1. 大量数据使用 `GridView.builder()`
2. 响应式调整列数
3. 使用 CustomScrollView 处理复杂布局
4. 合理设置间距和内边距

---

*最后更新: 2026-02-18*

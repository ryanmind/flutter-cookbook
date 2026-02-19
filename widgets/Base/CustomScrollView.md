# Flutter CustomScrollView 组件用法详解

## 简介

CustomScrollView 是 Flutter 中用于创建自定义滚动效果的滚动组件。它可以组合多个 sliver 组件，实现复杂的滚动布局效果。

## 1. 初级用法

### 1.1 基本概念

CustomScrollView 的作用：
- 组合多个 sliver 组件
- 实现复杂的滚动效果
- 统一管理滚动行为

### 1.2 基本语法

```dart
CustomScrollView(
  slivers: [
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('项目 $index')),
        childCount: 20,
      ),
    ),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `slivers` | `List<Widget>` | sliver 组件列表 |
| `scrollDirection` | `Axis` | 滚动方向 |
| `reverse` | `bool` | 是否反向 |
| `controller` | `ScrollController?` | 滚动控制器 |
| `physics` | `ScrollPhysics?` | 滚动物理效果 |
| `anchor` | `double` | 锚点位置 |

### 1.4 常用 Sliver 组件

| Sliver | 说明 |
|------|------|
| `SliverList` | 列表 |
| `SliverGrid` | 网格 |
| `SliverAppBar` | 应用栏 |
| `SliverToBoxAdapter` | 普通组件包装 |
| `SliverPadding` | 内边距 |
| `SliverPersistentHeader` | 持久头部 |
| `SliverFillRemaining` | 填充剩余空间 |

### 1.5 基础示例

#### 组合列表和网格

```dart
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(
      child: Container(
        height: 200,
        color: Colors.blue,
        child: Center(child: Text('头部区域')),
      ),
    ),
    SliverGrid(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 2,
        mainAxisSpacing: 10,
        crossAxisSpacing: 10,
      ),
      delegate: SliverChildBuilderDelegate(
        (context, index) => Container(
          color: Colors.primaries[index % Colors.primaries.length],
        ),
        childCount: 10,
      ),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('列表项 $index')),
        childCount: 20,
      ),
    ),
  ],
)
```

## 2. 中级用法

### 2.1 SliverAppBar

```dart
CustomScrollView(
  slivers: [
    SliverAppBar(
      expandedHeight: 200,
      floating: false,
      pinned: true,
      flexibleSpace: FlexibleSpaceBar(
        title: Text('可折叠标题'),
        background: Image.network(
          'https://picsum.photos/400',
          fit: BoxFit.cover,
        ),
      ),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('项目 $index')),
        childCount: 20,
      ),
    ),
  ],
)
```

### 2.2 SliverPersistentHeader

```dart
class StickyHeaderDelegate extends SliverPersistentHeaderDelegate {
  final Widget child;
  final double height;

  StickyHeaderDelegate({required this.child, required this.height});

  @override
  double get minExtent => height;

  @override
  double get maxExtent => height;

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return child;
  }

  @override
  bool shouldRebuild(StickyHeaderDelegate oldDelegate) {
    return child != oldDelegate.child;
  }
}

// 使用
CustomScrollView(
  slivers: [
    SliverPersistentHeader(
      pinned: true,
      delegate: StickyHeaderDelegate(
        height: 50,
        child: Container(
          color: Colors.blue,
          child: Center(child: Text('粘性头部')),
        ),
      ),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('项目 $index')),
        childCount: 20,
      ),
    ),
  ],
)
```

### 2.3 常见布局场景

#### 带头部的列表

```dart
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(
      child: Container(
        padding: EdgeInsets.all(16),
        child: Column(
          children: [
            CircleAvatar(radius: 40),
            SizedBox(height: 8),
            Text('用户名', style: TextStyle(fontSize: 20)),
          ],
        ),
      ),
    ),
    SliverToBoxAdapter(
      child: Divider(),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(
          leading: Icon(Icons.list),
          title: Text('菜单项 $index'),
        ),
        childCount: 10,
      ),
    ),
  ],
)
```

#### 分组列表

```dart
CustomScrollView(
  slivers: [
    SliverPersistentHeader(
      pinned: true,
      delegate: StickyHeaderDelegate(
        height: 40,
        child: Container(
          color: Colors.grey[300],
          child: Center(child: Text('分组 A')),
        ),
      ),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('A-$index')),
        childCount: 5,
      ),
    ),
    SliverPersistentHeader(
      pinned: true,
      delegate: StickyHeaderDelegate(
        height: 40,
        child: Container(
          color: Colors.grey[300],
          child: Center(child: Text('分组 B')),
        ),
      ),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('B-$index')),
        childCount: 5,
      ),
    ),
  ],
)
```

### 2.4 SliverFillRemaining

```dart
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(
      child: Container(
        height: 100,
        color: Colors.blue,
      ),
    ),
    SliverFillRemaining(
      hasScrollBody: false,
      child: Container(
        color: Colors.green,
        child: Center(child: Text('填充剩余空间')),
      ),
    ),
  ],
)
```

## 3. 高级用法

### 3.1 下拉刷新

```dart
RefreshIndicator(
  onRefresh: () async {
    await Future.delayed(Duration(seconds: 1));
  },
  child: CustomScrollView(
    slivers: [
      SliverAppBar(
        title: Text('下拉刷新'),
      ),
      SliverList(
        delegate: SliverChildBuilderDelegate(
          (context, index) => ListTile(title: Text('项目 $index')),
          childCount: 20,
        ),
      ),
    ],
  ),
)
```

### 3.2 滚动监听

```dart
class ScrollListenerExample extends StatefulWidget {
  @override
  State<ScrollListenerExample> createState() => _ScrollListenerExampleState();
}

class _ScrollListenerExampleState extends State<ScrollListenerExample> {
  final ScrollController _controller = ScrollController();
  double _offset = 0;

  @override
  void initState() {
    super.initState();
    _controller.addListener(() {
      setState(() => _offset = _controller.offset);
    });
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('滚动位置: ${_offset.toStringAsFixed(0)}'),
        Expanded(
          child: CustomScrollView(
            controller: _controller,
            slivers: [
              SliverList(
                delegate: SliverChildBuilderDelegate(
                  (context, index) => ListTile(title: Text('项目 $index')),
                  childCount: 50,
                ),
              ),
            ],
          ),
        ),
      ],
    );
  }
}
```

### 3.3 视差效果

```dart
class ParallaxExample extends StatefulWidget {
  @override
  State<ParallaxExample> createState() => _ParallaxExampleState();
}

class _ParallaxExampleState extends State<ParallaxExample> {
  final ScrollController _controller = ScrollController();
  double _offset = 0;

  @override
  void initState() {
    super.initState();
    _controller.addListener(() {
      setState(() => _offset = _controller.offset);
    });
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      controller: _controller,
      slivers: [
        SliverToBoxAdapter(
          child: Transform.translate(
            offset: Offset(0, _offset * 0.5),
            child: Image.network(
              'https://picsum.photos/400/300',
              height: 300,
              fit: BoxFit.cover,
            ),
          ),
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
```

### 3.4 自定义 Sliver

```dart
class SliverIndexedStack extends StatefulWidget {
  final int index;
  final List<Widget> children;

  const SliverIndexedStack({
    required this.index,
    required this.children,
  });

  @override
  State<SliverIndexedStack> createState() => _SliverIndexedStackState();
}

class _SliverIndexedStackState extends State<SliverIndexedStack> {
  @override
  Widget build(BuildContext context) {
    return SliverToBoxAdapter(
      child: IndexedStack(
        index: widget.index,
        children: widget.children,
      ),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：SliverToBoxAdapter 嵌套问题

```dart
// 问题：SliverToBoxAdapter 中嵌套可滚动组件
SliverToBoxAdapter(
  child: ListView(...),  // 错误！嵌套滚动
)

// 解决：使用 shrinkWrap 或移除内层滚动
SliverToBoxAdapter(
  child: Column(
    children: [...],
  ),
)
```

### 问题2：SliverList 性能

```dart
// 问题：大量数据时 SliverChildListDelegate 性能差
SliverList(
  delegate: SliverChildListDelegate([
    // 100个组件
  ]),
)

// 解决：使用 SliverChildBuilderDelegate
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => ListTile(title: Text('$index')),
    childCount: 100,
  ),
)
```

### 问题3：SliverAppBar 闪退

```dart
// 问题：SliverAppBar 在某些配置下可能闪退
// 解决：确保 flexibleSpace 使用正确

SliverAppBar(
  expandedHeight: 200,
  flexibleSpace: FlexibleSpaceBar(
    title: Text('标题'),
    background: Container(color: Colors.blue),  // 确保有背景
  ),
)
```

## 5. CustomScrollView vs 其他滚动组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **CustomScrollView** | 自定义滚动 | 最灵活，可组合 sliver |
| **ListView** | 列表滚动 | 简单，适合纯列表 |
| **GridView** | 网格滚动 | 简单，适合纯网格 |
| **SingleChildScrollView** | 单子滚动 | 适合少量内容 |

### 选择建议

```dart
// 纯列表 → ListView
ListView.builder(itemBuilder: ...)

// 纯网格 → GridView
GridView.count(crossAxisCount: 2, children: ...)

// 复杂布局 → CustomScrollView
CustomScrollView(slivers: [
  SliverAppBar(...),
  SliverGrid(...),
  SliverList(...),
])
```

## 6. 最佳实践

### 6.1 封装 Sliver 组件

```dart
class SectionHeader extends StatelessWidget {
  final String title;

  const SectionHeader({required this.title});

  @override
  Widget build(BuildContext context) {
    return SliverPersistentHeader(
      pinned: true,
      delegate: StickyHeaderDelegate(
        height: 44,
        child: Container(
          color: Theme.of(context).scaffoldBackgroundColor,
          padding: EdgeInsets.symmetric(horizontal: 16),
          child: Align(
            alignment: Alignment.centerLeft,
            child: Text(
              title,
              style: TextStyle(fontWeight: FontWeight.bold),
            ),
          ),
        ),
      ),
    );
  }
}
```

### 6.2 统一滚动控制器

```dart
class ScrollConfig {
  static ScrollPhysics get defaultPhysics => BouncingScrollPhysics();
  
  static ScrollController createController() {
    return ScrollController();
  }
}
```

### 6.3 懒加载

```dart
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) {
      // 实现懒加载
      if (index == items.length - 1) {
        loadMore();
      }
      return ItemWidget(items[index]);
    },
    childCount: items.length + (hasMore ? 1 : 0),
  ),
)
```

## 总结

CustomScrollView 是 Flutter 中最灵活的滚动组件：

**核心要点**：
1. 组合多个 sliver 组件
2. SliverAppBar 实现折叠头部
3. SliverPersistentHeader 实现粘性头部

**最佳实践**：
1. 大量数据使用 BuilderDelegate
2. 封装可复用的 sliver 组件
3. 合理使用 SliverToBoxAdapter

**常见场景**：
- 折叠头部
- 分组列表
- 视差效果
- 混合布局

---

*最后更新: 2026-02-19*

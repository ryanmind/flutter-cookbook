# Flutter SliverPersistentHeader 组件用法详解

## 简介

SliverPersistentHeader 是 Flutter 中用于创建可折叠、可固定的头部组件。它提供了高度自定义能力，可以实现各种滚动效果。

## 1. 初级用法

### 1.1 基本概念

SliverPersistentHeader 的核心作用：
- 创建可折叠的头部
- 支持 pinned 和 floating 模式
- 需要配合 SliverPersistentHeaderDelegate

### 1.2 基本语法

```dart
SliverPersistentHeader(
  delegate: MyHeaderDelegate(),
  pinned: true,
  floating: false,
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `delegate` | `SliverPersistentHeaderDelegate` | - | 头部代理 |
| `pinned` | `bool` | `false` | 是否固定在顶部 |
| `floating` | `bool` | `false` | 是否浮动 |
| `snap` | `bool` | `false` | 快速吸附（需配合 floating） |

### 1.4 SliverPersistentHeaderDelegate

```dart
class MyHeaderDelegate extends SliverPersistentHeaderDelegate {
  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.blue,
      child: Center(child: Text('Header')),
    );
  }

  @override
  double get maxExtent => 200;

  @override
  double get minExtent => 60;

  @override
  bool shouldRebuild(covariant MyHeaderDelegate oldDelegate) => false;
}
```

### 1.5 基础示例

#### 固定头部

```dart
CustomScrollView(
  slivers: [
    SliverPersistentHeader(
      pinned: true,
      delegate: _SimpleHeaderDelegate(),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 50,
      ),
    ),
  ],
)

class _SimpleHeaderDelegate extends SliverPersistentHeaderDelegate {
  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.blue,
      alignment: Alignment.center,
      child: Text('Fixed Header'),
    );
  }

  @override
  double get maxExtent => 60;

  @override
  double get minExtent => 60;

  @override
  bool shouldRebuild(covariant _SimpleHeaderDelegate oldDelegate) => false;
}
```

## 2. 中级用法

### 2.1 可折叠头部

```dart
class CollapsibleHeaderDelegate extends SliverPersistentHeaderDelegate {
  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    final progress = shrinkOffset / (maxExtent - minExtent);
    
    return Container(
      color: Color.lerp(Colors.blue, Colors.blue[700], progress),
      child: Stack(
        children: [
          // 展开时显示的内容
          Opacity(
            opacity: 1 - progress,
            child: Center(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Icon(Icons.star, size: 48, color: Colors.white),
                  Text('Expanded', style: TextStyle(color: Colors.white, fontSize: 24)),
                ],
              ),
            ),
          ),
          // 折叠时显示的内容
          Opacity(
            opacity: progress,
            child: Center(
              child: Text('Collapsed', style: TextStyle(color: Colors.white)),
            ),
          ),
        ],
      ),
    );
  }

  @override
  double get maxExtent => 200;

  @override
  double get minExtent => 60;

  @override
  bool shouldRebuild(covariant CollapsibleHeaderDelegate oldDelegate) => false;
}
```

### 2.2 带标题栏

```dart
class TitleHeaderDelegate extends SliverPersistentHeaderDelegate {
  final String title;
  
  TitleHeaderDelegate({required this.title});

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    final progress = shrinkOffset / (maxExtent - minExtent);
    final isCollapsed = progress > 0.5;

    return Container(
      color: Colors.white,
      child: Stack(
        children: [
          // 背景
          AnimatedContainer(
            duration: Duration(milliseconds: 100),
            color: isCollapsed ? Colors.blue : Colors.transparent,
          ),
          // 标题
          Positioned(
            left: 16,
            bottom: 16,
            child: Text(
              title,
              style: TextStyle(
                fontSize: isCollapsed ? 18 : 32,
                fontWeight: FontWeight.bold,
                color: isCollapsed ? Colors.white : Colors.black,
              ),
            ),
          ),
        ],
      ),
    );
  }

  @override
  double get maxExtent => 150;

  @override
  double get minExtent => 60;

  @override
  bool shouldRebuild(covariant TitleHeaderDelegate oldDelegate) => title != oldDelegate.title;
}
```

### 2.3 浮动头部

```dart
SliverPersistentHeader(
  floating: true,
  delegate: _FloatingHeaderDelegate(),
)

class _FloatingHeaderDelegate extends SliverPersistentHeaderDelegate {
  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.purple,
      padding: EdgeInsets.symmetric(horizontal: 16),
      child: Row(
        children: [
          Text('Floating Header', style: TextStyle(color: Colors.white)),
          Spacer(),
          IconButton(
            icon: Icon(Icons.search, color: Colors.white),
            onPressed: () {},
          ),
        ],
      ),
    );
  }

  @override
  double get maxExtent => 56;

  @override
  double get minExtent => 56;

  @override
  bool shouldRebuild(covariant _FloatingHeaderDelegate oldDelegate) => false;
}
```

### 2.4 分段头部

```dart
class SectionHeader extends StatelessWidget {
  final List<String> sections;
  final int currentIndex;
  final ValueChanged<int> onTap;

  const SectionHeader({
    required this.sections,
    required this.currentIndex,
    required this.onTap,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SliverPersistentHeader(
      pinned: true,
      delegate: _SectionHeaderDelegate(
        sections: sections,
        currentIndex: currentIndex,
        onTap: onTap,
      ),
    );
  }
}

class _SectionHeaderDelegate extends SliverPersistentHeaderDelegate {
  final List<String> sections;
  final int currentIndex;
  final ValueChanged<int> onTap;

  _SectionHeaderDelegate({
    required this.sections,
    required this.currentIndex,
    required this.onTap,
  });

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.white,
      child: Row(
        children: sections.asMap().entries.map((entry) {
          final isSelected = entry.key == currentIndex;
          return Expanded(
            child: GestureDetector(
              onTap: () => onTap(entry.key),
              child: Container(
                padding: EdgeInsets.symmetric(vertical: 12),
                decoration: BoxDecoration(
                  border: Border(
                    bottom: BorderSide(
                      color: isSelected ? Colors.blue : Colors.transparent,
                      width: 2,
                    ),
                  ),
                ),
                child: Text(
                  entry.value,
                  textAlign: TextAlign.center,
                  style: TextStyle(
                    color: isSelected ? Colors.blue : Colors.grey,
                    fontWeight: isSelected ? FontWeight.bold : FontWeight.normal,
                  ),
                ),
              ),
            ),
          );
        }).toList(),
      ),
    );
  }

  @override
  double get maxExtent => 48;

  @override
  double get minExtent => 48;

  @override
  bool shouldRebuild(covariant _SectionHeaderDelegate oldDelegate) {
    return currentIndex != oldDelegate.currentIndex || 
           sections != oldDelegate.sections;
  }
}
```

## 3. 高级用法

### 3.1 带搜索栏

```dart
class SearchHeader extends StatefulWidget {
  @override
  State<SearchHeader> createState() => _SearchHeaderState();
}

class _SearchHeaderState extends State<SearchHeader> {
  bool _isSearching = false;
  final TextEditingController _controller = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return SliverPersistentHeader(
      pinned: true,
      delegate: _SearchHeaderDelegate(
        isSearching: _isSearching,
        controller: _controller,
        onToggleSearch: () => setState(() => _isSearching = !_isSearching),
      ),
    );
  }
}

class _SearchHeaderDelegate extends SliverPersistentHeaderDelegate {
  final bool isSearching;
  final TextEditingController controller;
  final VoidCallback onToggleSearch;

  _SearchHeaderDelegate({
    required this.isSearching,
    required this.controller,
    required this.onToggleSearch,
  });

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.white,
      padding: EdgeInsets.all(8),
      child: Row(
        children: [
          Expanded(
            child: AnimatedSwitcher(
              duration: Duration(milliseconds: 200),
              child: isSearching
                  ? TextField(
                      key: ValueKey('search'),
                      controller: controller,
                      autofocus: true,
                      decoration: InputDecoration(
                        hintText: 'Search...',
                        border: OutlineInputBorder(),
                        contentPadding: EdgeInsets.symmetric(horizontal: 12),
                      ),
                    )
                  : Text(
                      'Title',
                      key: ValueKey('title'),
                      style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
                    ),
            ),
          ),
          IconButton(
            icon: Icon(isSearching ? Icons.close : Icons.search),
            onPressed: onToggleSearch,
          ),
        ],
      ),
    );
  }

  @override
  double get maxExtent => 60;

  @override
  double get minExtent => 60;

  @override
  bool shouldRebuild(covariant _SearchHeaderDelegate oldDelegate) {
    return isSearching != oldDelegate.isSearching;
  }
}
```

### 3.2 视差效果

```dart
class ParallaxHeaderDelegate extends SliverPersistentHeaderDelegate {
  final String imageUrl;

  ParallaxHeaderDelegate({required this.imageUrl});

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    final progress = shrinkOffset / maxExtent;
    final offset = (shrinkOffset * 0.5).clamp(0.0, double.infinity);

    return Stack(
      fit: StackFit.expand,
      children: [
        // 视差背景
        Transform.translate(
          offset: Offset(0, offset),
          child: Opacity(
            opacity: 1 - progress,
            child: Image.network(
              imageUrl,
              fit: BoxFit.cover,
            ),
          ),
        ),
        // 渐变遮罩
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
        // 标题
        Positioned(
          left: 16,
          bottom: 16 + (shrinkOffset * 0.5),
          child: Text(
            'Parallax Header',
            style: TextStyle(
              color: Colors.white,
              fontSize: 24 - (progress * 8),
              fontWeight: FontWeight.bold,
            ),
          ),
        ),
      ],
    );
  }

  @override
  double get maxExtent => 250;

  @override
  double get minExtent => 60;

  @override
  bool shouldRebuild(covariant ParallaxHeaderDelegate oldDelegate) {
    return imageUrl != oldDelegate.imageUrl;
  }
}
```

### 3.3 动态背景色

```dart
class DynamicColorHeader extends StatelessWidget {
  final Color expandedColor;
  final Color collapsedColor;

  const DynamicColorHeader({
    required this.expandedColor,
    required this.collapsedColor,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SliverPersistentHeader(
      pinned: true,
      delegate: _DynamicColorHeaderDelegate(
        expandedColor: expandedColor,
        collapsedColor: collapsedColor,
      ),
    );
  }
}

class _DynamicColorHeaderDelegate extends SliverPersistentHeaderDelegate {
  final Color expandedColor;
  final Color collapsedColor;

  _DynamicColorHeaderDelegate({
    required this.expandedColor,
    required this.collapsedColor,
  });

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    final progress = shrinkOffset / (maxExtent - minExtent);
    final color = Color.lerp(expandedColor, collapsedColor, progress);

    return Container(
      color: color,
      child: Center(
        child: Text(
          'Dynamic Color',
          style: TextStyle(
            color: Colors.white,
            fontSize: 24 - (progress * 8),
          ),
        ),
      ),
    );
  }

  @override
  double get maxExtent => 200;

  @override
  double get minExtent => 60;

  @override
  bool shouldRebuild(covariant _DynamicColorHeaderDelegate oldDelegate) {
    return expandedColor != oldDelegate.expandedColor || 
           collapsedColor != oldDelegate.collapsedColor;
  }
}
```

### 3.4 粘性分类头部

```dart
class StickyCategoryHeader extends StatelessWidget {
  final List<String> categories;
  final int selectedCategory;

  const StickyCategoryHeader({
    required this.categories,
    required this.selectedCategory,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SliverPersistentHeader(
      pinned: true,
      delegate: _StickyCategoryHeaderDelegate(
        categories: categories,
        selectedCategory: selectedCategory,
      ),
    );
  }
}

class _StickyCategoryHeaderDelegate extends SliverPersistentHeaderDelegate {
  final List<String> categories;
  final int selectedCategory;

  _StickyCategoryHeaderDelegate({
    required this.categories,
    required this.selectedCategory,
  });

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.white,
      child: ListView.builder(
        scrollDirection: Axis.horizontal,
        itemCount: categories.length,
        itemBuilder: (context, index) {
          final isSelected = index == selectedCategory;
          return GestureDetector(
            onTap: () {},
            child: Container(
              padding: EdgeInsets.symmetric(horizontal: 20),
              decoration: BoxDecoration(
                color: isSelected ? Colors.blue : Colors.transparent,
                borderRadius: BorderRadius.circular(20),
              ),
              margin: EdgeInsets.symmetric(vertical: 8, horizontal: 4),
              alignment: Alignment.center,
              child: Text(
                categories[index],
                style: TextStyle(
                  color: isSelected ? Colors.white : Colors.black87,
                  fontWeight: isSelected ? FontWeight.bold : FontWeight.normal,
                ),
              ),
            ),
          );
        },
      ),
    );
  }

  @override
  double get maxExtent => 50;

  @override
  double get minExtent => 50;

  @override
  bool shouldRebuild(covariant _StickyCategoryHeaderDelegate oldDelegate) {
    return selectedCategory != oldDelegate.selectedCategory ||
           categories != oldDelegate.categories;
  }
}
```

## 4. 常见问题与解决方案

### 问题1：状态不更新

```dart
// 确保 shouldRebuild 返回 true 当需要重建时
@override
bool shouldRebuild(covariant MyDelegate oldDelegate) {
  return title != oldDelegate.title;  // 条件判断
}
```

### 问题2：高度计算

```dart
// 使用 MediaQuery 获取安全高度
@override
double get maxExtent => MediaQuery.of(context).padding.top + 200;

// 或使用固定值
@override
double get minExtent => kToolbarHeight;
```

### 问题3：重叠内容

```dart
// 使用 overlapsContent 参数处理重叠
@override
Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
  return Container(
    decoration: BoxDecoration(
      boxShadow: overlapsContent 
          ? [BoxShadow(color: Colors.black12, blurRadius: 4)]
          : null,
    ),
    // ...
  );
}
```

## 5. SliverPersistentHeader vs 其他头部

| 组件 | 用途 | 特点 |
|------|------|------|
| **SliverPersistentHeader** | 自定义头部 | 最灵活 |
| **SliverAppBar** | 应用栏 | 内置功能丰富 |
| **SliverToBoxAdapter** | 简单包装 | 无滚动效果 |

### 选择建议

```dart
// 需要自定义滚动效果 → SliverPersistentHeader
SliverPersistentHeader(delegate: MyDelegate(), pinned: true)

// 标准 AppBar 效果 → SliverAppBar
SliverAppBar(title: Text('Title'), ...)

// 简单固定内容 → SliverToBoxAdapter
SliverToBoxAdapter(child: Container(...))
```

## 6. 最佳实践

### 6.1 封装可复用 Delegate

```dart
class SimpleHeaderDelegate extends SliverPersistentHeaderDelegate {
  final Widget Function(double shrinkOffset) builder;
  final double minHeight;
  final double maxHeight;

  SimpleHeaderDelegate({
    required this.builder,
    required this.minHeight,
    required this.maxHeight,
  });

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return builder(shrinkOffset);
  }

  @override
  double get maxExtent => maxHeight;

  @override
  double get minExtent => minHeight;

  @override
  bool shouldRebuild(covariant SimpleHeaderDelegate oldDelegate) => false;
}
```

### 6.2 性能优化

```dart
// 避免在 build 方法中创建复杂对象
// 使用预计算的值

class OptimizedHeaderDelegate extends SliverPersistentHeaderDelegate {
  final double _maxExtent;
  final double _minExtent;
  
  OptimizedHeaderDelegate({
    required double maxExtent,
    required double minExtent,
  }) : _maxExtent = maxExtent, _minExtent = minExtent;

  // 缓存计算结果
  double? _cachedProgress;
  
  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    // 简单的 build 方法
  }
  
  @override
  double get maxExtent => _maxExtent;

  @override
  double get minExtent => _minExtent;
  
  @override
  bool shouldRebuild(covariant OptimizedHeaderDelegate oldDelegate) => false;
}
```

## 总结

SliverPersistentHeader 是创建自定义滚动头部的核心组件：

**核心要点**：
1. 需要实现 SliverPersistentHeaderDelegate
2. maxExtent 和 minExtent 定义高度范围
3. pinned 和 floating 控制滚动行为

**最佳实践**：
1. 封装可复用的 Delegate
2. 正确实现 shouldRebuild
3. 处理 overlapsContent 状态

**常见场景**：
- 可折叠头部
- 分段导航
- 搜索栏
- 视差效果

---

*最后更新: 2026-02-19*

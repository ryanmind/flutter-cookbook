# Flutter SliverGrid 组件用法详解

## 简介

SliverGrid 是 Flutter 中用于在 CustomScrollView 中显示网格布局的组件。它支持多种网格布局方式，可以与其他 Sliver 组件无缝组合。

## 1. 初级用法

### 1.1 基本概念

SliverGrid 的核心作用：
- 在 CustomScrollView 中显示网格
- 支持固定列数和自适应布局
- 可与其他 Sliver 组件组合

### 1.2 基本语法

```dart
SliverGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    mainAxisSpacing: 10,
    crossAxisSpacing: 10,
    childAspectRatio: 1,
  ),
  delegate: SliverChildBuilderDelegate(
    (context, index) => Container(color: Colors.blue),
    childCount: 20,
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `gridDelegate` | `SliverGridDelegate` | 网格布局代理 |
| `delegate` | `SliverChildDelegate` | 子组件代理 |

### 1.4 GridDelegate 类型

| Delegate | 说明 |
|----------|------|
| `SliverGridDelegateWithFixedCrossAxisCount` | 固定列数 |
| `SliverGridDelegateWithMaxCrossAxisExtent` | 固定单元格最大宽度 |

### 1.5 基础示例

#### 固定列数网格

```dart
CustomScrollView(
  slivers: [
    SliverGrid(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        mainAxisSpacing: 8,
        crossAxisSpacing: 8,
        childAspectRatio: 1.0,
      ),
      delegate: SliverChildBuilderDelegate(
        (context, index) {
          return Container(
            color: Colors.primaries[index % Colors.primaries.length],
            child: Center(child: Text('$index')),
          );
        },
        childCount: 30,
      ),
    ),
  ],
)
```

#### 自适应宽度网格

```dart
SliverGrid(
  gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
    maxCrossAxisExtent: 150,  // 单元格最大宽度
    mainAxisSpacing: 8,
    crossAxisSpacing: 8,
    childAspectRatio: 1.0,
  ),
  delegate: SliverChildBuilderDelegate(
    (context, index) => Container(
      color: Colors.primaries[index % Colors.primaries.length],
    ),
    childCount: 20,
  ),
)
```

## 2. 中级用法

### 2.1 不同宽高比

```dart
SliverGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    childAspectRatio: 0.75,  // 宽:高 = 3:4
  ),
  delegate: SliverChildBuilderDelegate(
    (context, index) {
      return Card(
        child: Column(
          children: [
            Expanded(child: Container(color: Colors.blue)),
            Padding(
              padding: EdgeInsets.all(8),
              child: Text('Item $index'),
            ),
          ],
        ),
      );
    },
    childCount: 10,
  ),
)
```

### 2.2 带间距的网格

```dart
SliverGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    mainAxisSpacing: 16,
    crossAxisSpacing: 16,
    childAspectRatio: 1.5,
  ),
  delegate: SliverChildBuilderDelegate(
    (context, index) {
      return Container(
        decoration: BoxDecoration(
          color: Colors.white,
          borderRadius: BorderRadius.circular(8),
          boxShadow: [
            BoxShadow(color: Colors.black12, blurRadius: 4),
          ],
        ),
        child: Center(child: Text('Card $index')),
      );
    },
    childCount: 10,
  ),
)
```

### 2.3 响应式网格

```dart
class ResponsiveGrid extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final width = MediaQuery.of(context).size.width;
    final columns = (width / 150).floor().clamp(2, 4);
    
    return CustomScrollView(
      slivers: [
        SliverGrid(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: columns,
            mainAxisSpacing: 8,
            crossAxisSpacing: 8,
          ),
          delegate: SliverChildBuilderDelegate(
            (context, index) => Container(
              color: Colors.primaries[index % Colors.primaries.length],
            ),
            childCount: 20,
          ),
        ),
      ],
    );
  }
}
```

### 2.4 网格与列表组合

```dart
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(
      child: Padding(
        padding: EdgeInsets.all(16),
        child: Text('Featured', style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold)),
      ),
    ),
    SliverGrid(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 2,
        mainAxisSpacing: 8,
        crossAxisSpacing: 8,
      ),
      delegate: SliverChildBuilderDelegate(
        (context, index) => Container(color: Colors.blue),
        childCount: 4,
      ),
    ),
    SliverToBoxAdapter(
      child: Padding(
        padding: EdgeInsets.all(16),
        child: Text('All Items', style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold)),
      ),
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 10,
      ),
    ),
  ],
)
```

### 2.5 图库网格

```dart
class GalleryGrid extends StatelessWidget {
  final List<String> images;

  const GalleryGrid({required this.images, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverGrid(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: 3,
            mainAxisSpacing: 2,
            crossAxisSpacing: 2,
          ),
          delegate: SliverChildBuilderDelegate(
            (context, index) {
              return GestureDetector(
                onTap: () => _openImage(context, index),
                child: Image.network(
                  images[index],
                  fit: BoxFit.cover,
                ),
              );
            },
            childCount: images.length,
          ),
        ),
      ],
    );
  }

  void _openImage(BuildContext context, int index) {
    // 打开图片详情
  }
}
```

## 3. 高级用法

### 3.1 瀑布流布局

```dart
// 使用 flutter_staggered_grid_view 包实现真正的瀑布流
// 或使用自定义布局模拟

class MasonryGrid extends StatelessWidget {
  final List<Widget> children;
  final int columns;

  const MasonryGrid({
    required this.children,
    this.columns = 2,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final List<List<Widget>> columnChildren = List.generate(columns, (_) => []);
    
    for (int i = 0; i < children.length; i++) {
      columnChildren[i % columns].add(children[i]);
    }
    
    return CustomScrollView(
      slivers: [
        SliverToBoxAdapter(
          child: Row(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: columnChildren.map((col) {
              return Expanded(
                child: Column(
                  children: col,
                ),
              );
            }).toList(),
          ),
        ),
      ],
    );
  }
}
```

### 3.2 可变尺寸网格

```dart
class VariableSizeGrid extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverGrid(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: 4,
            mainAxisSpacing: 8,
            crossAxisSpacing: 8,
          ),
          delegate: SliverChildBuilderDelegate(
            (context, index) {
              // 每隔 5 个项目创建一个大卡片
              if (index % 5 == 0) {
                return Container(
                  color: Colors.red,
                  child: Center(child: Text('Large $index')),
                );
              }
              return Container(
                color: Colors.blue,
                child: Center(child: Text('$index')),
              );
            },
            childCount: 20,
          ),
        ),
      ],
    );
  }
}
```

### 3.3 网格内嵌套滚动

```dart
SliverGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    childAspectRatio: 0.6,
  ),
  delegate: SliverChildBuilderDelegate(
    (context, index) {
      return Card(
        child: Column(
          children: [
            Container(
              height: 100,
              color: Colors.blue,
            ),
            Expanded(
              child: ListView.builder(
                itemCount: 5,
                itemBuilder: (context, i) => ListTile(
                  title: Text('Item $i'),
                  dense: true,
                ),
              ),
            ),
          ],
        ),
      );
    },
    childCount: 4,
  ),
)
```

### 3.4 网格动画

```dart
class AnimatedGrid extends StatefulWidget {
  @override
  State<AnimatedGrid> createState() => _AnimatedGridState();
}

class _AnimatedGridState extends State<AnimatedGrid> {
  final List<Color> _colors = List.generate(20, (i) => 
    Colors.primaries[i % Colors.primaries.length]);

  void _shuffleColors() {
    setState(() {
      _colors.shuffle();
    });
  }

  @override
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: [
        SliverPersistentHeader(
          pinned: true,
          delegate: _ButtonHeader(onTap: _shuffleColors),
        ),
        SliverGrid(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: 3,
            mainAxisSpacing: 8,
            crossAxisSpacing: 8,
          ),
          delegate: SliverChildBuilderDelegate(
            (context, index) {
              return AnimatedContainer(
                key: ValueKey(_colors[index]),
                duration: Duration(milliseconds: 300),
                color: _colors[index],
              );
            },
            childCount: _colors.length,
          ),
        ),
      ],
    );
  }
}

class _ButtonHeader extends SliverPersistentHeaderDelegate {
  final VoidCallback onTap;

  _ButtonHeader({required this.onTap});

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    return Container(
      color: Colors.white,
      child: TextButton(
        onPressed: onTap,
        child: Text('Shuffle'),
      ),
    );
  }

  @override
  double get maxExtent => 50;

  @override
  double get minExtent => 50;

  @override
  bool shouldRebuild(covariant _ButtonHeader oldDelegate) => false;
}
```

## 4. 常见问题与解决方案

### 问题1：网格项超出边界

```dart
// 检查 childAspectRatio 设置
SliverGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    childAspectRatio: 1.0,  // 调整比例
  ),
  ...
)
```

### 问题2：间距不一致

```dart
// 统一设置 mainAxisSpacing 和 crossAxisSpacing
SliverGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    mainAxisSpacing: 16,   // 主轴间距
    crossAxisSpacing: 16,  // 交叉轴间距
  ),
  ...
)
```

### 问题3：与 GridView 的区别

```dart
// GridView 独立使用
GridView.count(crossAxisCount: 2, children: [...])

// SliverGrid 在 CustomScrollView 中
CustomScrollView(
  slivers: [SliverGrid(...)],
)
```

## 5. SliverGrid vs 其他网格组件

| 组件 | 使用场景 | 特点 |
|------|----------|------|
| **SliverGrid** | CustomScrollView | 可组合 |
| **GridView** | 独立网格 | 简单 |
| **GridTile** | 网格项 | 带头尾 |

### 选择建议

```dart
// 需要组合滚动效果 → SliverGrid
CustomScrollView(slivers: [SliverAppBar(...), SliverGrid(...)])

// 简单网格页面 → GridView
GridView.count(...)
```

## 6. 最佳实践

### 6.1 合理设置宽高比

```dart
// 根据内容类型设置宽高比
// 图片网格
childAspectRatio: 1.0

// 卡片网格
childAspectRatio: 0.75

// 横向卡片
childAspectRatio: 1.5
```

### 6.2 响应式设计

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final columns = _calculateColumns(constraints.maxWidth);
    return CustomScrollView(
      slivers: [
        SliverGrid(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: columns,
            ...
          ),
          ...
        ),
      ],
    );
  },
)

int _calculateColumns(double width) {
  if (width > 1200) return 4;
  if (width > 800) return 3;
  if (width > 500) return 2;
  return 1;
}
```

### 6.3 性能优化

```dart
SliverGrid(
  delegate: SliverChildBuilderDelegate(
    (context, index) => const GridItem(),  // 使用 const
    childCount: 100,
    addAutomaticKeepAlives: true,
  ),
  gridDelegate: ...,
)
```

## 总结

SliverGrid 是 CustomScrollView 中实现网格布局的核心组件：

**核心要点**：
1. gridDelegate 定义网格布局
2. delegate 提供子组件
3. 可与其他 Sliver 组件组合

**最佳实践**：
1. 根据内容设置 childAspectRatio
2. 响应式调整列数
3. 使用 Builder 懒加载

**常见场景**：
- 图片画廊
- 产品列表
- 应用图标网格
- 卡片布局

---

*最后更新: 2026-02-19*

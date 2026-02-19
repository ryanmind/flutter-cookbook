# Flutter LayoutBuilder 组件用法详解

## 简介

LayoutBuilder 是 Flutter 中用于根据父容器约束构建子组件的布局组件。它允许你在构建时获取父容器的约束信息，从而实现响应式布局。

## 1. 初级用法

### 1.1 基本概念

LayoutBuilder 的作用：
- 获取父容器的约束（BoxConstraints）
- 根据约束动态构建子组件
- 实现响应式布局
- 避免不必要的 MediaQuery 调用

### 1.2 基本语法

```dart
LayoutBuilder(
  builder: (BuildContext context, BoxConstraints constraints) {
    return Text('宽度: ${constraints.maxWidth}');
  },
)
```

### 1.3 BoxConstraints 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `minWidth` | `double` | 最小宽度 |
| `maxWidth` | `double` | 最大宽度 |
| `minHeight` | `double` | 最小高度 |
| `maxHeight` | `double` | 最大高度 |
| `hasBoundedWidth` | `bool` | 宽度是否有界 |
| `hasBoundedHeight` | `bool` | 高度是否有界 |
| `hasInfiniteWidth` | `bool` | 宽度是否无限 |
| `hasInfiniteHeight` | `bool` | 高度是否无限 |

### 1.4 基础示例

#### 获取父容器宽度

```dart
LayoutBuilder(
  builder: (context, constraints) {
    return Container(
      width: constraints.maxWidth,
      height: 100,
      color: Colors.blue,
      child: Center(
        child: Text('宽度: ${constraints.maxWidth}'),
      ),
    );
  },
)
```

#### 响应式布局

```dart
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth > 600) {
      return Row(
        children: [
          Expanded(child: Container(color: Colors.red)),
          Expanded(child: Container(color: Colors.blue)),
        ],
      );
    } else {
      return Column(
        children: [
          Expanded(child: Container(color: Colors.red)),
          Expanded(child: Container(color: Colors.blue)),
        ],
      );
    }
  },
)
```

## 2. 中级用法

### 2.1 根据宽度切换布局

```dart
class ResponsiveLayout extends StatelessWidget {
  final Widget mobile;
  final Widget tablet;
  final Widget desktop;
  
  const ResponsiveLayout({
    required this.mobile,
    required this.tablet,
    required this.desktop,
  });
  
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth >= 1200) {
          return desktop;
        } else if (constraints.maxWidth >= 600) {
          return tablet;
        } else {
          return mobile;
        }
      },
    );
  }
}
```

### 2.2 计算网格列数

```dart
class ResponsiveGrid extends StatelessWidget {
  final List<Widget> children;
  
  const ResponsiveGrid({required this.children});
  
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        // 根据宽度计算列数
        int crossAxisCount;
        if (constraints.maxWidth > 1200) {
          crossAxisCount = 4;
        } else if (constraints.maxWidth > 800) {
          crossAxisCount = 3;
        } else if (constraints.maxWidth > 500) {
          crossAxisCount = 2;
        } else {
          crossAxisCount = 1;
        }
        
        return GridView.count(
          crossAxisCount: crossAxisCount,
          children: children,
        );
      },
    );
  }
}
```

### 2.3 宽高比计算

```dart
LayoutBuilder(
  builder: (context, constraints) {
    final aspectRatio = constraints.maxWidth / constraints.maxHeight;
    
    if (aspectRatio > 1) {
      // 横屏
      return Row(children: [...]);
    } else {
      // 竖屏
      return Column(children: [...]);
    }
  },
)
```

### 2.4 常见布局场景

#### 卡片布局

```dart
class CardLayout extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final cardWidth = constraints.maxWidth > 600 ? 300.0 : constraints.maxWidth - 32;
        final cardsPerRow = (constraints.maxWidth / cardWidth).floor();
        
        return Wrap(
          spacing: 16,
          runSpacing: 16,
          children: List.generate(10, (index) {
            return SizedBox(
              width: cardWidth,
              height: 200,
              child: Card(child: Center(child: Text('卡片 $index'))),
            );
          }),
        );
      },
    );
  }
}
```

#### 侧边栏布局

```dart
class SidebarLayout extends StatelessWidget {
  final Widget sidebar;
  final Widget content;
  
  const SidebarLayout({
    required this.sidebar,
    required this.content,
  });
  
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth >= 800) {
          return Row(
            children: [
              SizedBox(width: 250, child: sidebar),
              Expanded(child: content),
            ],
          );
        } else {
          return content;
        }
      },
    );
  }
}
```

#### 自适应字体大小

```dart
class AdaptiveText extends StatelessWidget {
  final String text;
  final double baseFontSize;
  
  const AdaptiveText({
    required this.text,
    this.baseFontSize = 16,
  });
  
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final scaleFactor = constraints.maxWidth / 375;
        final fontSize = baseFontSize * scaleFactor.clamp(0.8, 1.5);
        
        return Text(
          text,
          style: TextStyle(fontSize: fontSize),
        );
      },
    );
  }
}
```

### 2.5 检测无限约束

```dart
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.hasInfiniteWidth) {
      return Text('宽度无限，需要 Expanded 或 SizedBox');
    }
    if (constraints.hasInfiniteHeight) {
      return Text('高度无限，需要 Expanded 或 SizedBox');
    }
    return Container(
      width: constraints.maxWidth,
      height: constraints.maxHeight,
      color: Colors.blue,
    );
  },
)
```

## 3. 高级用法

### 3.1 ConstrainedLayoutBuilder

封装一个带约束范围的 LayoutBuilder：

```dart
class ConstrainedLayoutBuilder extends StatelessWidget {
  final double minWidth;
  final double maxWidth;
  final Widget Function(BuildContext, BoxConstraints) builder;
  
  const ConstrainedLayoutBuilder({
    this.minWidth = 0,
    this.maxWidth = double.infinity,
    required this.builder,
  });
  
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final effectiveConstraints = BoxConstraints(
          minWidth: minWidth,
          maxWidth: constraints.maxWidth.clamp(minWidth, maxWidth),
        );
        return builder(context, effectiveConstraints);
      },
    );
  }
}
```

### 3.2 缓存布局结果

避免约束不变时重复计算：

```dart
class CachedLayoutBuilder extends StatefulWidget {
  final Widget Function(BoxConstraints) builder;
  
  const CachedLayoutBuilder({required this.builder});
  
  @override
  State<CachedLayoutBuilder> createState() => _CachedLayoutBuilderState();
}

class _CachedLayoutBuilderState extends State<CachedLayoutBuilder> {
  BoxConstraints? _lastConstraints;
  Widget? _cachedWidget;
  
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (_lastConstraints != constraints) {
          _lastConstraints = constraints;
          _cachedWidget = widget.builder(constraints);
        }
        return _cachedWidget!;
      },
    );
  }
}
```

### 3.3 嵌套 LayoutBuilder

```dart
LayoutBuilder(
  builder: (context, outerConstraints) {
    return Container(
      width: outerConstraints.maxWidth * 0.8,
      child: LayoutBuilder(
        builder: (context, innerConstraints) {
          return Container(
            width: innerConstraints.maxWidth,
            height: 100,
            color: Colors.blue,
          );
        },
      ),
    );
  },
)
```

### 3.4 与 CustomMultiChildLayout 配合

```dart
LayoutBuilder(
  builder: (context, constraints) {
    return CustomMultiChildLayout(
      delegate: MyLayoutDelegate(constraints),
      children: [
        LayoutId(id: 1, child: Text('左')),
        LayoutId(id: 2, child: Text('右')),
      ],
    );
  },
)
```

### 3.5 实现类似 CSS 的 Flexbox

```dart
class FlexLayout extends StatelessWidget {
  final List<Widget> children;
  final double gap;
  final int forceColumns;
  
  const FlexLayout({
    required this.children,
    this.gap = 16,
    this.forceColumns = 0,
  });
  
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final columns = forceColumns > 0 
            ? forceColumns 
            : _calculateColumns(constraints.maxWidth);
        final itemWidth = (constraints.maxWidth - gap * (columns - 1)) / columns;
        
        return Wrap(
          spacing: gap,
          runSpacing: gap,
          children: children.map((child) {
            return SizedBox(width: itemWidth, child: child);
          }).toList(),
        );
      },
    );
  }
  
  int _calculateColumns(double width) {
    if (width > 1200) return 4;
    if (width > 900) return 3;
    if (width > 600) return 2;
    return 1;
  }
}
```

## 4. 常见问题与解决方案

### 问题1：约束为无限

```dart
// 问题：在 ListView 中使用没有约束的 LayoutBuilder
ListView(
  children: [
    LayoutBuilder(
      builder: (context, constraints) {
        // constraints.maxHeight 是无限的！
        return Container(height: constraints.maxHeight);  // 错误
      },
    ),
  ],
)

// 解决：设置固定高度或使用 shrinkWrap
ListView(
  children: [
    SizedBox(
      height: 200,
      child: LayoutBuilder(
        builder: (context, constraints) {
          return Container(height: constraints.maxHeight);  // 正确
        },
      ),
    ),
  ],
)
```

### 问题2：与 MediaQuery 的选择

```dart
// MediaQuery：获取屏幕尺寸
final screenWidth = MediaQuery.of(context).size.width;

// LayoutBuilder：获取父容器约束
// 区别：LayoutBuilder 获取的是父组件的实际约束，更精确
```

**选择建议**：
- 需要屏幕尺寸 → MediaQuery
- 需要父容器约束 → LayoutBuilder
- 简单的宽度判断 → LayoutBuilder 性能更好

### 问题3：重建问题

```dart
// 问题：约束不变时仍会重建
LayoutBuilder(
  builder: (context, constraints) {
    // 这里的 widget 每次都会重建
    return expensiveWidget();
  },
)

// 解决：将静态部分提取出来
class MyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        return _buildLayout(constraints);
      },
    );
  }
  
  Widget _buildLayout(BoxConstraints constraints) {
    // 返回简单的布局结构
    if (constraints.maxWidth > 600) {
      return _WideLayout();
    } else {
      return _NarrowLayout();
    }
  }
}
```

## 5. LayoutBuilder vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **LayoutBuilder** | 获取父容器约束 | 最通用，精确 |
| **MediaQuery** | 获取屏幕信息 | 全局，包含更多数据 |
| **OrientationBuilder** | 获取方向 | 简化版，专用于方向 |
| **Builder** | 获取 context | 不提供约束信息 |

### 选择建议

```dart
// 需要精确的父容器约束 → LayoutBuilder
LayoutBuilder(
  builder: (context, constraints) {
    return Container(width: constraints.maxWidth);
  },
)

// 只需要屏幕方向 → OrientationBuilder
OrientationBuilder(
  builder: (context, orientation) {
    return orientation == Orientation.portrait
        ? Column(...)
        : Row(...);
  },
)

// 需要 context 但不需要约束 → Builder
Builder(
  builder: (context) {
    return Text('${Theme.of(context).primaryColor}');
  },
)
```

## 6. 最佳实践

### 6.1 定义断点常量

```dart
abstract class Breakpoints {
  static const double mobile = 600;
  static const double tablet = 900;
  static const double desktop = 1200;
  
  static bool isMobile(double width) => width < mobile;
  static bool isTablet(double width) => width >= mobile && width < desktop;
  static bool isDesktop(double width) => width >= desktop;
}
```

### 6.2 封装响应式组件

```dart
class ResponsiveBuilder extends StatelessWidget {
  final Widget Function(BuildContext, BoxConstraints) builder;
  
  const ResponsiveBuilder({required this.builder});
  
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(builder: builder);
  }
}

// 使用
ResponsiveBuilder(
  builder: (context, constraints) {
    return Container(width: constraints.maxWidth);
  },
)
```

### 6.3 避免过度使用

```dart
// 不推荐：嵌套太多 LayoutBuilder
LayoutBuilder(
  builder: (context, constraints1) {
    return LayoutBuilder(
      builder: (context, constraints2) {
        return LayoutBuilder(
          builder: (context, constraints3) {
            return Container();
          },
        );
      },
    );
  },
)

// 推荐：一次获取，按需使用
LayoutBuilder(
  builder: (context, constraints) {
    // 根据 constraints 做所有判断
    return Container();
  },
)
```

## 总结

LayoutBuilder 是 Flutter 响应式布局的核心组件：

**核心要点**：
1. 获取父容器的 BoxConstraints
2. 根据约束动态构建子组件
3. 比 MediaQuery 更精确，性能更好

**最佳实践**：
1. 定义统一的断点常量
2. 封装可复用的响应式组件
3. 避免过度嵌套

**常见场景**：
- 响应式布局切换
- 自适应网格
- 侧边栏显示/隐藏
- 卡片布局

---

*最后更新: 2026-02-19*

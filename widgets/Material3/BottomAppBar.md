# Flutter BottomAppBar 组件用法详解

## 简介

BottomAppBar 是 Material 3 中用于创建底部应用栏的组件，通常与 Scaffold 配合使用，提供底部导航、操作按钮和 FAB（浮动操作按钮）的集成。

## 1. 初级用法

### 1.1 基本概念

BottomAppBar 的核心作用：
- 提供底部导航区域
- 承载操作按钮
- 与 FloatingActionButton 集成
- 支持缺口（notch）设计

### 1.2 基本语法

```dart
Scaffold(
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
  floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
  bottomNavigationBar: BottomAppBar(
    child: Row(
      children: [
        IconButton(icon: Icon(Icons.menu), onPressed: () {}),
        Spacer(),
        IconButton(icon: Icon(Icons.search), onPressed: () {}),
      ],
    ),
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `child` | `Widget?` | 子组件 |
| `color` | `Color?` | 背景颜色 |
| `elevation` | `double?` | 阴影高度 |
| `shape` `NotchedShape?` | 形状（通常用 CircularNotchedRectangle） |
| `padding` | `EdgeInsetsGeometry?` | 内边距 |
| `height` | `double?` | 高度 |
| `surfaceTintColor` | `Color?` | 表面着色 |

### 1.4 基础示例

#### 简单底部栏

```dart
Scaffold(
  bottomNavigationBar: BottomAppBar(
    child: Row(
      mainAxisAlignment: MainAxisAlignment.spaceAround,
      children: [
        IconButton(icon: Icon(Icons.home), onPressed: () {}),
        IconButton(icon: Icon(Icons.search), onPressed: () {}),
        IconButton(icon: Icon(Icons.notifications), onPressed: () {}),
        IconButton(icon: Icon(Icons.person), onPressed: () {}),
      ],
    ),
  ),
)
```

#### 带 FAB 缺口

```dart
Scaffold(
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
  floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
  bottomNavigationBar: BottomAppBar(
    shape: CircularNotchedRectangle(),
    notchMargin: 8,
    child: Row(
      children: [
        IconButton(icon: Icon(Icons.home), onPressed: () {}),
        Spacer(),
        IconButton(icon: Icon(Icons.settings), onPressed: () {}),
      ],
    ),
  ),
)
```

## 2. 中级用法

### 2.1 FAB 位置

```dart
// 中心停靠
floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked

// 末端停靠
floatingActionButtonLocation: FloatingActionButtonLocation.endDocked

// 末端浮动（默认）
floatingActionButtonLocation: FloatingActionButtonLocation.endFloat

// 中心浮动
floatingActionButtonLocation: FloatingActionButtonLocation.centerFloat

// 自定义位置
floatingActionButtonLocation: FloatingActionButtonLocation(
  FloatingActionButtonLocation.endFloat.x,
  FloatingActionButtonLocation.endFloat.y - 20,
)
```

### 2.2 多按钮布局

```dart
BottomAppBar(
  child: Row(
    children: [
      // 左侧按钮组
      IconButton(icon: Icon(Icons.menu), onPressed: () {}),
      IconButton(icon: Icon(Icons.search), onPressed: () {}),
      Spacer(),
      // 右侧按钮组
      IconButton(icon: Icon(Icons.favorite), onPressed: () {}),
      IconButton(icon: Icon(Icons.more_vert), onPressed: () {}),
    ],
  ),
)
```

### 2.3 带标签按钮

```dart
BottomAppBar(
  child: Row(
    mainAxisAlignment: MainAxisAlignment.spaceAround,
    children: [
      _buildNavItem(Icons.home, '首页', true),
      _buildNavItem(Icons.explore, '发现', false),
      // 中间留空给 FAB
      SizedBox(width: 56),
      _buildNavItem(Icons.message, '消息', false),
      _buildNavItem(Icons.profile, '我的', false),
    ],
  ),
)

Widget _buildNavItem(IconData icon, String label, bool selected) {
  return Column(
    mainAxisSize: MainAxisSize.min,
    children: [
      Icon(icon, color: selected ? Colors.blue : Colors.grey),
      Text(label, style: TextStyle(fontSize: 12, color: selected ? Colors.blue : Colors.grey)),
    ],
  );
}
```

### 2.4 自定义高度

```dart
BottomAppBar(
  height: 80,
  child: Row(
    children: [
      // 自定义内容
    ],
  ),
)
```

### 2.5 配合 TabBar

```dart
Scaffold(
  bottomNavigationBar: BottomAppBar(
    child: TabBar(
      tabs: [
        Tab(icon: Icon(Icons.home), text: '首页'),
        Tab(icon: Icon(Icons.search), text: '搜索'),
        Tab(icon: Icon(Icons.person), text: '我的'),
      ],
    ),
  ),
)
```

## 3. 高级用法

### 3.1 动态显示/隐藏

```dart
class AnimatedBottomAppBar extends StatefulWidget {
  @override
  State<AnimatedBottomAppBar> createState() => _AnimatedBottomAppBarState();
}

class _AnimatedBottomAppBarState extends State<AnimatedBottomAppBar> {
  bool _isVisible = true;
  late ScrollController _scrollController;

  @override
  void initState() {
    super.initState();
    _scrollController = ScrollController();
    _scrollController.addListener(_onScroll);
  }

  void _onScroll() {
    if (_scrollController.position.userScrollDirection == ScrollDirection.reverse) {
      if (_isVisible) setState(() => _isVisible = false);
    } else {
      if (!_isVisible) setState(() => _isVisible = true);
    }
  }

  @override
  void dispose() {
    _scrollController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: ListView.builder(
        controller: _scrollController,
        itemCount: 100,
        itemBuilder: (context, index) => ListTile(title: Text('Item $index')),
      ),
      bottomNavigationBar: AnimatedContainer(
        duration: Duration(milliseconds: 200),
        height: _isVisible ? kBottomNavigationBarHeight : 0,
        child: BottomAppBar(
          child: Row(
            mainAxisAlignment: MainAxisAlignment.spaceAround,
            children: [
              IconButton(icon: Icon(Icons.home), onPressed: () {}),
              IconButton(icon: Icon(Icons.search), onPressed: () {}),
              IconButton(icon: Icon(Icons.person), onPressed: () {}),
            ],
          ),
        ),
      ),
    );
  }
}
```

### 3.2 底部操作面板

```dart
BottomAppBar(
  height: 120,
  child: Column(
    children: [
      Row(
        mainAxisAlignment: MainAxisAlignment.spaceAround,
        children: [
          IconButton(icon: Icon(Icons.home), onPressed: () {}),
          IconButton(icon: Icon(Icons.search), onPressed: () {}),
          IconButton(icon: Icon(Icons.person), onPressed: () {}),
        ],
      ),
      Divider(height: 1),
      Padding(
        padding: EdgeInsets.all(8),
        child: Text('提示信息或版权声明'),
      ),
    ],
  ),
)
```

### 3.3 可展开底部栏

```dart
class ExpandableBottomAppBar extends StatefulWidget {
  @override
  State<ExpandableBottomAppBar> createState() => _ExpandableBottomAppBarState();
}

class _ExpandableBottomAppBarState extends State<ExpandableBottomAppBar> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onVerticalDragEnd: (details) {
        if (details.primaryVelocity! < 0) {
          setState(() => _expanded = true);
        } else if (details.primaryVelocity! > 0) {
          setState(() => _expanded = false);
        }
      },
      child: BottomAppBar(
        height: _expanded ? 200 : 56,
        child: Column(
          children: [
            Row(
              children: [
                IconButton(
                  icon: Icon(Icons.keyboard_arrow_up),
                  onPressed: () => setState(() => _expanded = !_expanded),
                ),
                Spacer(),
                Text(_expanded ? '上滑收起' : '下拉展开'),
              ],
            ),
            if (_expanded) ...[
              Divider(),
              Expanded(
                child: GridView.count(
                  crossAxisCount: 4,
                  children: [
                    _buildAction(Icons.share, '分享'),
                    _buildAction(Icons.edit, '编辑'),
                    _buildAction(Icons.delete, '删除'),
                    _buildAction(Icons.archive, '归档'),
                  ],
                ),
              ),
            ],
          ],
        ),
      ),
    );
  }

  Widget _buildAction(IconData icon, String label) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Icon(icon),
        Text(label, style: TextStyle(fontSize: 12)),
      ],
    );
  }
}
```

### 3.4 带搜索框

```dart
BottomAppBar(
  height: 72,
  child: Column(
    children: [
      Padding(
        padding: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
        child: TextField(
          decoration: InputDecoration(
            hintText: '搜索...',
            prefixIcon: Icon(Icons.search),
            border: OutlineInputBorder(
              borderRadius: BorderRadius.circular(24),
            ),
            contentPadding: EdgeInsets.zero,
            filled: true,
          ),
        ),
      ),
    ],
  ),
)
```

## 4. 常见问题与解决方案

### 问题1：FAB 缺口位置不对

```dart
// 确保 FAB 位置和 BottomAppBar shape 匹配

// 中心缺口
Scaffold(
  floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
  bottomNavigationBar: BottomAppBar(
    shape: CircularNotchedRectangle(),
    notchMargin: 8,
    child: Row(
      children: [
        IconButton(icon: Icon(Icons.home), onPressed: () {}),
        Spacer(),  // 留出中间空间
        IconButton(icon: Icon(Icons.settings), onPressed: () {}),
      ],
    ),
  ),
)

// 末端缺口
Scaffold(
  floatingActionButtonLocation: FloatingActionButtonLocation.endDocked,
  bottomNavigationBar: BottomAppBar(
    shape: CircularNotchedRectangle(),
    notchMargin: 8,
    child: Row(
      children: [
        IconButton(icon: Icon(Icons.home), onPressed: () {}),
        IconButton(icon: Icon(Icons.search), onPressed: () {}),
        Spacer(),  // 留出右侧空间
      ],
    ),
  ),
)
```

### 问题2：底部栏遮挡内容

```dart
// 使用 padding 或 SafeArea
Scaffold(
  body: Padding(
    padding: EdgeInsets.only(bottom: kBottomNavigationBarHeight),
    child: ListView(...),
  ),
  bottomNavigationBar: BottomAppBar(...),
)
```

### 问题3：键盘弹出时底部栏上移

```dart
// 设置 resizeToAvoidBottomInset
Scaffold(
  resizeToAvoidBottomInset: false,
  bottomNavigationBar: BottomAppBar(...),
)
```

## 5. BottomAppBar vs BottomNavigationBar

| 特性 | BottomAppBar | BottomNavigationBar |
|------|--------------|---------------------|
| 灵活性 | 高，可自定义内容 | 低，固定模式 |
| FAB 集成 | 支持 notch | 不支持 |
| 自定义布局 | 完全自由 | 固定项目 |
| 适用场景 | 工具栏、自定义导航 | 标准导航 |

### 选择建议

```dart
// 需要集成 FAB → BottomAppBar
Scaffold(
  floatingActionButton: FloatingActionButton(...),
  bottomNavigationBar: BottomAppBar(
    shape: CircularNotchedRectangle(),
    child: Row(...),
  ),
)

// 标准底部导航 → BottomNavigationBar
Scaffold(
  bottomNavigationBar: BottomNavigationBar(
    items: [
      BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
      BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
    ],
  ),
)

// Material 3 风格导航 → NavigationBar
Scaffold(
  bottomNavigationBar: NavigationBar(
    destinations: [
      NavigationDestination(icon: Icon(Icons.home), label: '首页'),
      NavigationDestination(icon: Icon(Icons.person), label: '我的'),
    ],
  ),
)
```

## 6. 最佳实践

### 6.1 与 FAB 配合

```dart
// 标准 Material 3 底部栏 + FAB 配置
Scaffold(
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
  floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
  bottomNavigationBar: BottomAppBar(
    shape: CircularNotchedRectangle(),
    notchMargin: 8.0,
    child: Row(
      mainAxisAlignment: MainAxisAlignment.spaceBetween,
      children: <Widget>[
        IconButton(icon: Icon(Icons.menu), onPressed: () {}),
        IconButton(icon: Icon(Icons.search), onPressed: () {}),
      ],
    ),
  ),
)
```

### 6.2 统一样式封装

```dart
class AppBottomAppBar extends StatelessWidget {
  final List<Widget> children;
  final VoidCallback? onFabPressed;

  const AppBottomAppBar({
    required this.children,
    this.onFabPressed,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return BottomAppBar(
      shape: onFabPressed != null ? CircularNotchedRectangle() : null,
      notchMargin: 8,
      child: Row(children: children),
    );
  }
}
```

### 6.3 安全区域处理

```dart
BottomAppBar(
  child: SafeArea(
    top: false,
    child: Row(
      children: [
        IconButton(icon: Icon(Icons.home), onPressed: () {}),
        Spacer(),
        IconButton(icon: Icon(Icons.settings), onPressed: () {}),
      ],
    ),
  ),
)
```

## 总结

BottomAppBar 是创建底部工具栏的核心组件：

**核心要点**：
1. 支持 FAB 缺口设计
2. 可完全自定义子组件
3. 与 Scaffold 配合使用
4. 支持动态高度

**最佳实践**：
1. 使用 `CircularNotchedRectangle` 创建 FAB 缺口
2. 合理布局按钮位置
3. 注意键盘弹出时的处理
4. 考虑安全区域

**常见场景**：
- 底部工具栏
- 集成 FAB 的导航栏
- 自定义底部操作面板

---

*最后更新: 2026-02-19*

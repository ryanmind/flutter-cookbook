# Flutter Drawer 组件用法详解

## 简介

Drawer 是侧边抽屉导航组件，通常从屏幕左侧滑出，用于显示应用的主要导航菜单、用户信息或设置选项。

## 1. 基本用法

### 1.1 基本概念

Drawer 的核心特点：
- 从左侧滑出的抽屉面板
- 通常作为 Scaffold 的 drawer 属性
- 支持手势滑动打开
- 可自定义内容和样式

### 1.2 基本语法

```dart
Scaffold(
  appBar: AppBar(title: Text('Drawer 示例')),
  drawer: Drawer(
    child: ListView(
      children: [
        DrawerHeader(child: Text('头部')),
        ListTile(title: Text('首页')),
        ListTile(title: Text('设置')),
      ],
    ),
  ),
  body: Container(),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `child` | `Widget?` | 子组件 |
| `backgroundColor` | `Color?` | 背景色 |
| `elevation` | `double?` | 阴影高度 |
| `shadowColor` | `Color?` | 阴影颜色 |
| `shape` | `ShapeBorder?` | 形状 |
| `width` | `double?` | 宽度 |
| `semanticLabel` | `String?` | 语义标签 |
| `clipBehavior` | `Clip` | 裁剪行为 |

### 1.4 基础示例

#### 简单抽屉

```dart
Scaffold(
  appBar: AppBar(title: Text('Drawer 示例')),
  drawer: Drawer(
    child: ListView(
      padding: EdgeInsets.zero,
      children: [
        DrawerHeader(
          decoration: BoxDecoration(color: Colors.blue),
          child: Text('菜单', style: TextStyle(color: Colors.white, fontSize: 24)),
        ),
        ListTile(
          leading: Icon(Icons.home),
          title: Text('首页'),
          onTap: () {
            Navigator.pop(context);
          },
        ),
        ListTile(
          leading: Icon(Icons.settings),
          title: Text('设置'),
          onTap: () {
            Navigator.pop(context);
          },
        ),
      ],
    ),
  ),
  body: Center(child: Text('主内容')),
)
```

## 2. DrawerHeader 抽屉头部

### 2.1 基本用法

```dart
DrawerHeader(
  decoration: BoxDecoration(color: Colors.blue),
  child: Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    children: [
      Text('应用名称', style: TextStyle(color: Colors.white, fontSize: 20)),
      SizedBox(height: 8),
      Text('版本 1.0.0', style: TextStyle(color: Colors.white70)),
    ],
  ),
)
```

### 2.2 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `child` | `Widget?` | 子组件 |
| `decoration` | `Decoration?` | 装饰 |
| `duration` | `Duration` | 动画时长 |
| `curve` | `Curve` | 动画曲线 |
| `padding` | `EdgeInsetsGeometry?` | 内边距 |
| `margin` | `EdgeInsetsGeometry?` | 外边距 |

### 2.3 用户信息头部

```dart
DrawerHeader(
  decoration: BoxDecoration(
    color: Colors.blue,
  ),
  child: Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    mainAxisAlignment: MainAxisAlignment.end,
    children: [
      CircleAvatar(
        radius: 32,
        backgroundImage: NetworkImage('https://picsum.photos/100'),
      ),
      SizedBox(height: 12),
      Text(
        '用户名',
        style: TextStyle(color: Colors.white, fontSize: 18),
      ),
      Text(
        'user@example.com',
        style: TextStyle(color: Colors.white70, fontSize: 14),
      ),
    ],
  ),
)
```

## 3. UserAccountsDrawerHeader 用户账户头部

### 3.1 基本用法

```dart
UserAccountsDrawerHeader(
  accountName: Text('用户名'),
  accountEmail: Text('user@example.com'),
  currentAccountPicture: CircleAvatar(
    backgroundImage: NetworkImage('https://picsum.photos/100'),
  ),
  otherAccountsPictures: [
    CircleAvatar(child: Text('A')),
    CircleAvatar(child: Text('B')),
  ],
  decoration: BoxDecoration(color: Colors.blue),
)
```

### 3.2 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `accountName` | `Widget?` | 账户名称 |
| `accountEmail` | `Widget?` | 账户邮箱 |
| `currentAccountPicture` | `Widget?` | 当前账户头像 |
| `otherAccountsPictures` | `List<Widget>?` | 其他账户头像 |
| `decoration` | `Decoration?` | 装饰 |
| `arrowColor` | `Color?` | 箭头颜色 |
| `onDetailsPressed` | `VoidCallback?` | 点击详情回调 |

### 3.3 完整示例

```dart
UserAccountsDrawerHeader(
  accountName: Text(
    '张三',
    style: TextStyle(fontWeight: FontWeight.bold),
  ),
  accountEmail: Text('zhangsan@example.com'),
  currentAccountPicture: GestureDetector(
    onTap: () {
      print('切换账户');
    },
    child: CircleAvatar(
      backgroundImage: NetworkImage('https://picsum.photos/100'),
    ),
  ),
  otherAccountsPictures: [
    GestureDetector(
      onTap: () {
        print('切换到账户 B');
      },
      child: CircleAvatar(child: Text('B')),
    ),
  ],
  onDetailsPressed: () {
    print('显示账户详情');
  },
  decoration: BoxDecoration(
    color: Colors.blue,
    image: DecorationImage(
      image: NetworkImage('https://picsum.photos/400/200'),
      fit: BoxFit.cover,
      colorFilter: ColorFilter.mode(Colors.black45, BlendMode.darken),
    ),
  ),
)
```

## 4. 完整导航抽屉

### 4.1 带分组的抽屉

```dart
Drawer(
  child: ListView(
    padding: EdgeInsets.zero,
    children: [
      UserAccountsDrawerHeader(
        accountName: Text('用户名'),
        accountEmail: Text('user@example.com'),
        currentAccountPicture: CircleAvatar(child: Icon(Icons.person)),
      ),
      ListTile(
        leading: Icon(Icons.home),
        title: Text('首页'),
        selected: true,
        onTap: () => Navigator.pop(context),
      ),
      ListTile(
        leading: Icon(Icons.favorite),
        title: Text('收藏'),
        onTap: () => Navigator.pop(context),
      ),
      ListTile(
        leading: Icon(Icons.history),
        title: Text('历史'),
        onTap: () => Navigator.pop(context),
      ),
      Divider(),
      ListTile(
        leading: Icon(Icons.settings),
        title: Text('设置'),
        onTap: () => Navigator.pop(context),
      ),
      ListTile(
        leading: Icon(Icons.help),
        title: Text('帮助'),
        onTap: () => Navigator.pop(context),
      ),
      ListTile(
        leading: Icon(Icons.info),
        title: Text('关于'),
        onTap: () => Navigator.pop(context),
      ),
      Divider(),
      ListTile(
        leading: Icon(Icons.logout),
        title: Text('退出登录'),
        onTap: () {
          Navigator.pop(context);
          // 退出登录逻辑
        },
      ),
    ],
  ),
)
```

### 4.2 带选中状态的抽屉

```dart
class NavigationDrawer extends StatefulWidget {
  @override
  _NavigationDrawerState createState() => _NavigationDrawerState();
}

class _NavigationDrawerState extends State<NavigationDrawer> {
  int _selectedIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Drawer(
      child: ListView(
        padding: EdgeInsets.zero,
        children: [
          DrawerHeader(
            decoration: BoxDecoration(color: Colors.blue),
            child: Text('导航', style: TextStyle(color: Colors.white, fontSize: 24)),
          ),
          _buildItem(0, Icons.home, '首页'),
          _buildItem(1, Icons.message, '消息'),
          _buildItem(2, Icons.person, '我的'),
          Divider(),
          _buildItem(3, Icons.settings, '设置'),
        ],
      ),
    );
  }

  Widget _buildItem(int index, IconData icon, String title) {
    return ListTile(
      leading: Icon(icon),
      title: Text(title),
      selected: _selectedIndex == index,
      selectedTileColor: Colors.blue[50],
      selectedColor: Colors.blue,
      onTap: () {
        setState(() => _selectedIndex = index);
        Navigator.pop(context);
      },
    );
  }
}
```

## 5. 右侧抽屉

```dart
Scaffold(
  appBar: AppBar(title: Text('右侧抽屉')),
  endDrawer: Drawer(
    child: ListView(
      children: [
        DrawerHeader(child: Text('右侧菜单')),
        ListTile(title: Text('选项 1')),
        ListTile(title: Text('选项 2')),
      ],
    ),
  ),
  body: Center(child: Text('主内容')),
)
```

## 6. 高级用法

### 6.1 自定义宽度

```dart
Scaffold(
  drawer: SizedBox(
    width: 250,
    child: Drawer(
      child: ListView(
        children: [
          DrawerHeader(child: Text('自定义宽度')),
          ListTile(title: Text('选项')),
        ],
      ),
    ),
  ),
  body: Container(),
)
```

### 6.2 带展开/收起的抽屉

```dart
class ExpandableDrawer extends StatefulWidget {
  @override
  _ExpandableDrawerState createState() => _ExpandableDrawerState();
}

class _ExpandableDrawerState extends State<ExpandableDrawer> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return Drawer(
      child: ListView(
        padding: EdgeInsets.zero,
        children: [
          DrawerHeader(child: Text('可展开抽屉')),
          ListTile(
            leading: Icon(Icons.category),
            title: Text('分类'),
            trailing: Icon(_expanded ? Icons.expand_less : Icons.expand_more),
            onTap: () {
              setState(() => _expanded = !_expanded);
            },
          ),
          if (_expanded) ...[
            ListTile(
              leading: SizedBox(width: 24),
              title: Text('子分类 1'),
              onTap: () {},
            ),
            ListTile(
              leading: SizedBox(width: 24),
              title: Text('子分类 2'),
              onTap: () {},
            ),
          ],
          ListTile(
            leading: Icon(Icons.settings),
            title: Text('设置'),
            onTap: () {},
          ),
        ],
      ),
    );
  }
}
```

### 6.3 动态菜单

```dart
class DynamicDrawer extends StatelessWidget {
  final List<MenuItem> menuItems;
  final String? selectedId;

  const DynamicDrawer({
    required this.menuItems,
    this.selectedId,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Drawer(
      child: ListView.builder(
        padding: EdgeInsets.zero,
        itemCount: menuItems.length + 1,
        itemBuilder: (context, index) {
          if (index == 0) {
            return DrawerHeader(child: Text('菜单'));
          }
          final item = menuItems[index - 1];
          if (item.isDivider) {
            return Divider();
          }
          return ListTile(
            leading: item.icon != null ? Icon(item.icon) : null,
            title: Text(item.title),
            selected: item.id == selectedId,
            onTap: () {
              Navigator.pop(context);
              item.onTap?.call();
            },
          );
        },
      ),
    );
  }
}

class MenuItem {
  final String id;
  final String title;
  final IconData? icon;
  final bool isDivider;
  final VoidCallback? onTap;

  MenuItem({
    required this.id,
    required this.title,
    this.icon,
    this.isDivider = false,
    this.onTap,
  });

  MenuItem.divider()
      : id = '',
        title = '',
        icon = null,
        isDivider = true,
        onTap = null;
}
```

### 6.4 抽屉内嵌套导航

```dart
class NestedNavigationDrawer extends StatefulWidget {
  @override
  _NestedNavigationDrawerState createState() => _NestedNavigationDrawerState();
}

class _NestedNavigationDrawerState extends State<NestedNavigationDrawer> {
  bool _showSubMenu = false;

  @override
  Widget build(BuildContext context) {
    return Drawer(
      child: AnimatedSwitcher(
        duration: Duration(milliseconds: 200),
        child: _showSubMenu
            ? _buildSubMenu()
            : _buildMainMenu(),
      ),
    );
  }

  Widget _buildMainMenu() {
    return ListView(
      key: ValueKey('main'),
      padding: EdgeInsets.zero,
      children: [
        DrawerHeader(child: Text('主菜单')),
        ListTile(
          title: Text('首页'),
          onTap: () => Navigator.pop(context),
        ),
        ListTile(
          title: Text('分类'),
          trailing: Icon(Icons.chevron_right),
          onTap: () => setState(() => _showSubMenu = true),
        ),
      ],
    );
  }

  Widget _buildSubMenu() {
    return ListView(
      key: ValueKey('sub'),
      padding: EdgeInsets.zero,
      children: [
        ListTile(
          leading: Icon(Icons.arrow_back),
          title: Text('返回'),
          onTap: () => setState(() => _showSubMenu = false),
        ),
        Divider(),
        ListTile(title: Text('分类 1')),
        ListTile(title: Text('分类 2')),
        ListTile(title: Text('分类 3')),
      ],
    );
  }
}
```

## 7. 手动控制抽屉

```dart
class DrawerControlPage extends StatelessWidget {
  final GlobalKey<ScaffoldState> scaffoldKey = GlobalKey<ScaffoldState>();

  DrawerControlPage({Key? key}) : super(key: key);

  void _openDrawer(BuildContext context) {
    scaffoldKey.currentState?.openDrawer();
  }

  void _closeDrawer(BuildContext context) {
    Navigator.pop(context);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      key: scaffoldKey,
      appBar: AppBar(
        title: Text('手动控制抽屉'),
        leading: IconButton(
          icon: Icon(Icons.menu),
          onPressed: () => _openDrawer(context),
        ),
      ),
      drawer: Drawer(
        child: Column(
          children: [
            DrawerHeader(child: Text('抽屉')),
            ListTile(
              title: Text('关闭抽屉'),
              onTap: () => _closeDrawer(context),
            ),
          ],
        ),
      ),
      body: Center(
        child: ElevatedButton(
          onPressed: () => _openDrawer(context),
          child: Text('打开抽屉'),
        ),
      ),
    );
  }
}
```

## 8. 常见问题与解决方案

### 问题1：抽屉手势冲突

```dart
// 禁用抽屉手势
Scaffold(
  drawer: Drawer(...),
  drawerEnableOpenDragGesture: false,
  body: Container(),
)
```

### 问题2：抽屉边缘阴影

```dart
Drawer(
  elevation: 16,  // 增加阴影
  child: Container(),
)
```

### 问题3：抽屉内容滚动问题

```dart
// 使用 ListView 确保内容可滚动
Drawer(
  child: ListView(
    children: [...],
  ),
)

// 或 SingleChildScrollView
Drawer(
  child: SingleChildScrollView(
    child: Column(
      children: [...],
    ),
  ),
)
```

### 问题4：抽屉宽度固定

```dart
// 使用 SizedBox 包裹设置固定宽度
Scaffold(
  drawer: SizedBox(
    width: MediaQuery.of(context).size.width * 0.8,  // 屏幕宽度的 80%
    child: Drawer(child: Container()),
  ),
  body: Container(),
)
```

### 问题5：DrawerHeader 高度调整

```dart
// 默认高度固定，使用 Container 自定义
Container(
  height: 200,
  color: Colors.blue,
  child: Column(
    mainAxisAlignment: MainAxisAlignment.end,
    children: [
      Text('用户名'),
      Text('邮箱'),
    ],
  ),
)
```

## 9. Drawer vs NavigationDrawer

| 特性 | Drawer | NavigationDrawer (M3) |
|------|--------|----------------------|
| 设计规范 | Material 2 | Material 3 |
| 推荐程度 | 传统组件 | 推荐使用 |
| 样式 | 自定义 | 现代化样式 |
| 导航项 | 手动实现 | NavigationDestination |

```dart
// Material 3 NavigationDrawer
Scaffold(
  drawer: NavigationDrawer(
    selectedIndex: _selectedIndex,
    onDestinationSelected: (index) {
      setState(() => _selectedIndex = index);
      Navigator.pop(context);
    },
    children: [
      Padding(
        padding: EdgeInsets.fromLTRB(28, 16, 16, 10),
        child: Text('邮件'),
      ),
      NavigationDrawerDestination(
        icon: Icon(Icons.inbox),
        label: Text('收件箱'),
      ),
      NavigationDrawerDestination(
        icon: Icon(Icons.send),
        label: Text('已发送'),
      ),
    ],
  ),
  body: Container(),
)
```

## 10. 最佳实践

### 10.1 统一抽屉样式

```dart
class AppDrawer extends StatelessWidget {
  final int selectedIndex;
  final ValueChanged<int>? onItemSelected;

  const AppDrawer({
    required this.selectedIndex,
    this.onItemSelected,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Drawer(
      child: ListView(
        padding: EdgeInsets.zero,
        children: [
          UserAccountsDrawerHeader(
            accountName: Text('用户名'),
            accountEmail: Text('user@example.com'),
            currentAccountPicture: CircleAvatar(child: Icon(Icons.person)),
          ),
          _buildItem(0, Icons.home, '首页'),
          _buildItem(1, Icons.message, '消息'),
          _buildItem(2, Icons.person, '我的'),
          Divider(),
          _buildItem(3, Icons.settings, '设置'),
          _buildItem(4, Icons.help, '帮助'),
        ],
      ),
    );
  }

  Widget _buildItem(int index, IconData icon, String title) {
    return ListTile(
      leading: Icon(icon),
      title: Text(title),
      selected: selectedIndex == index,
      onTap: () => onItemSelected?.call(index),
    );
  }
}
```

### 10.2 关闭抽屉后再导航

```dart
ListTile(
  title: Text('设置'),
  onTap: () {
    Navigator.pop(context);  // 先关闭抽屉
    Navigator.pushNamed(context, '/settings');  // 再导航
  },
)
```

## 总结

Drawer 是 Flutter 中常用的侧边导航组件：

**核心要点**：
1. 作为 Scaffold 的 drawer 或 endDrawer 属性
2. 通常配合 DrawerHeader 或 UserAccountsDrawerHeader 使用
3. 点击菜单项后需要手动关闭抽屉
4. Material 3 推荐使用 NavigationDrawer

**常用组件**：
- `Drawer` - 抽屉容器
- `DrawerHeader` - 基础头部
- `UserAccountsDrawerHeader` - 用户账户头部
- `NavigationDrawer` - Material 3 抽屉

**最佳实践**：
1. 使用 ListView 确保内容可滚动
2. 关闭抽屉后再执行导航
3. 提供选中状态反馈
4. 合理分组菜单项

---

*最后更新: 2026-02-18*

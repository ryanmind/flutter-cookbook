# Flutter Navigation 组件用法详解

## 简介

Navigation 组件用于实现应用的导航结构。Material 3 提供了现代化的导航组件：NavigationBar（底部导航栏）和 NavigationRail（侧边导航栏），替代传统的 BottomNavigationBar。

## 1. NavigationBar 底部导航栏

### 1.1 基本概念

NavigationBar 的核心特点：
- Material 3 标准底部导航
- 支持图标和标签
- 支持指示器样式
- 推荐 3-5 个导航项

### 1.2 基本语法

```dart
Scaffold(
  body: _pages[_currentIndex],
  bottomNavigationBar: NavigationBar(
    selectedIndex: _currentIndex,
    onDestinationSelected: (index) {
      setState(() {
        _currentIndex = index;
      });
    },
    destinations: [
      NavigationDestination(icon: Icon(Icons.home), label: '首页'),
      NavigationDestination(icon: Icon(Icons.message), label: '消息'),
      NavigationDestination(icon: Icon(Icons.person), label: '我的'),
    ],
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `selectedIndex` | `int` | 当前选中索引 |
| `destinations` | `List<Widget>` | 导航项列表 |
| `onDestinationSelected` | `ValueChanged<int>?` | 选中回调 |
| `backgroundColor` | `Color?` | 背景色 |
| `indicatorColor` | `Color?` | 指示器颜色 |
| `indicatorShape` | `ShapeBorder?` | 指示器形状 |
| `height` | `double?` | 导航栏高度 |
| `elevation` | `double?` | 阴影高度 |
| `labelBehavior` | `NavigationDestinationLabelBehavior?` | 标签显示行为 |

### 1.4 NavigationDestination 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `icon` | `Widget` | 图标 |
| `selectedIcon` | `Widget?` | 选中时图标 |
| `label` | `String` | 标签文字 |
| `tooltip` | `String?` | 长按提示 |
| `enabled` | `bool` | 是否启用 |

### 1.5 基础示例

#### 简单导航栏

```dart
class SimpleNavigationPage extends StatefulWidget {
  @override
  _SimpleNavigationPageState createState() => _SimpleNavigationPageState();
}

class _SimpleNavigationPageState extends State<SimpleNavigationPage> {
  int _currentIndex = 0;

  final _pages = [
    Center(child: Text('首页')),
    Center(child: Text('消息')),
    Center(child: Text('我的')),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _pages[_currentIndex],
      bottomNavigationBar: NavigationBar(
        selectedIndex: _currentIndex,
        onDestinationSelected: (index) {
          setState(() {
            _currentIndex = index;
          });
        },
        destinations: [
          NavigationDestination(icon: Icon(Icons.home), label: '首页'),
          NavigationDestination(icon: Icon(Icons.message), label: '消息'),
          NavigationDestination(icon: Icon(Icons.person), label: '我的'),
        ],
      ),
    );
  }
}
```

#### 带指示器样式

```dart
NavigationBar(
  selectedIndex: _currentIndex,
  onDestinationSelected: (index) {
    setState(() => _currentIndex = index);
  },
  indicatorColor: Colors.blue[100],
  destinations: [
    NavigationDestination(icon: Icon(Icons.home), label: '首页'),
    NavigationDestination(icon: Icon(Icons.message), label: '消息'),
    NavigationDestination(icon: Icon(Icons.person), label: '我的'),
  ],
)
```

#### 选中时不同图标

```dart
NavigationDestination(
  icon: Icon(Icons.home_outlined),
  selectedIcon: Icon(Icons.home),
  label: '首页',
)
```

#### 隐藏标签

```dart
NavigationBar(
  labelBehavior: NavigationDestinationLabelBehavior.onlyShowSelected,
  destinations: [
    NavigationDestination(icon: Icon(Icons.home), label: '首页'),
    NavigationDestination(icon: Icon(Icons.message), label: '消息'),
    NavigationDestination(icon: Icon(Icons.person), label: '我的'),
  ],
)

// 或完全隐藏
NavigationBar(
  labelBehavior: NavigationDestinationLabelBehavior.alwaysHide,
  destinations: [...],
)
```

### 1.6 保持页面状态

```dart
class KeepStateNavigation extends StatefulWidget {
  @override
  _KeepStateNavigationState createState() => _KeepStateNavigationState();
}

class _KeepStateNavigationState extends State<KeepStateNavigation> {
  int _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(
        index: _currentIndex,
        children: [
          HomePage(),
          MessagePage(),
          ProfilePage(),
        ],
      ),
      bottomNavigationBar: NavigationBar(
        selectedIndex: _currentIndex,
        onDestinationSelected: (index) {
          setState(() => _currentIndex = index);
        },
        destinations: [
          NavigationDestination(icon: Icon(Icons.home), label: '首页'),
          NavigationDestination(icon: Icon(Icons.message), label: '消息'),
          NavigationDestination(icon: Icon(Icons.person), label: '我的'),
        ],
      ),
    );
  }
}
```

## 2. NavigationRail 侧边导航栏

### 2.1 基本概念

NavigationRail 的核心特点：
- 侧边垂直导航
- 适合平板/桌面端
- 支持扩展/收缩模式
- Material 3 推荐组件

### 2.2 基本语法

```dart
Row(
  children: [
    NavigationRail(
      selectedIndex: _currentIndex,
      onDestinationSelected: (index) {
        setState(() => _currentIndex = index);
      },
      labelType: NavigationRailLabelType.all,
      destinations: [
        NavigationRailDestination(icon: Icon(Icons.home), label: Text('首页')),
        NavigationRailDestination(icon: Icon(Icons.message), label: Text('消息')),
        NavigationRailDestination(icon: Icon(Icons.person), label: Text('我的')),
      ],
    ),
    Expanded(child: _pages[_currentIndex]),
  ],
)
```

### 2.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `selectedIndex` | `int` | 当前选中索引 |
| `destinations` | `List<NavigationRailDestination>` | 导航项列表 |
| `onDestinationSelected` | `ValueChanged<int>?` | 选中回调 |
| `backgroundColor` | `Color?` | 背景色 |
| `indicatorColor` | `Color?` | 指示器颜色 |
| `indicatorShape` | `ShapeBorder?` | 指示器形状 |
| `labelType` | `NavigationRailLabelType?` | 标签显示类型 |
| `leading` | `Widget?` | 顶部组件 |
| `trailing` | `Widget?` | 底部组件 |
| `extended` | `bool` | 是否扩展模式 |
| `minWidth` | `double?` | 最小宽度 |
| `minExtendedWidth` | `double?` | 扩展时最小宽度 |

### 2.4 NavigationRailLabelType

| 类型 | 说明 |
|------|------|
| `none` | 不显示标签 |
| `selected` | 只显示选中项标签 |
| `all` | 显示所有标签 |

### 2.5 基础示例

#### 简单侧边导航

```dart
class SimpleRailPage extends StatefulWidget {
  @override
  _SimpleRailPageState createState() => _SimpleRailPageState();
}

class _SimpleRailPageState extends State<SimpleRailPage> {
  int _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        NavigationRail(
          selectedIndex: _currentIndex,
          onDestinationSelected: (index) {
            setState(() => _currentIndex = index);
          },
          labelType: NavigationRailLabelType.all,
          destinations: [
            NavigationRailDestination(
              icon: Icon(Icons.home_outlined),
              selectedIcon: Icon(Icons.home),
              label: Text('首页'),
            ),
            NavigationRailDestination(
              icon: Icon(Icons.message_outlined),
              selectedIcon: Icon(Icons.message),
              label: Text('消息'),
            ),
            NavigationRailDestination(
              icon: Icon(Icons.person_outline),
              selectedIcon: Icon(Icons.person),
              label: Text('我的'),
            ),
          ],
        ),
        VerticalDivider(thickness: 1, width: 1),
        Expanded(
          child: Center(child: Text('页面 $_currentIndex')),
        ),
      ],
    );
  }
}
```

#### 扩展模式

```dart
NavigationRail(
  selectedIndex: _currentIndex,
  extended: true,
  onDestinationSelected: (index) {
    setState(() => _currentIndex = index);
  },
  destinations: [
    NavigationRailDestination(icon: Icon(Icons.home), label: Text('首页')),
    NavigationRailDestination(icon: Icon(Icons.message), label: Text('消息')),
    NavigationRailDestination(icon: Icon(Icons.person), label: Text('我的')),
  ],
)
```

#### 带 leading 和 trailing

```dart
NavigationRail(
  selectedIndex: _currentIndex,
  leading: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
  trailing: Expanded(
    child: Align(
      alignment: Alignment.bottomCenter,
      child: IconButton(
        onPressed: () {},
        icon: Icon(Icons.settings),
      ),
    ),
  ),
  onDestinationSelected: (index) {
    setState(() => _currentIndex = index);
  },
  destinations: [
    NavigationRailDestination(icon: Icon(Icons.home), label: Text('首页')),
    NavigationRailDestination(icon: Icon(Icons.message), label: Text('消息')),
    NavigationRailDestination(icon: Icon(Icons.person), label: Text('我的')),
  ],
)
```

### 2.6 响应式切换

```dart
class ResponsiveNavigation extends StatefulWidget {
  @override
  _ResponsiveNavigationState createState() => _ResponsiveNavigationState();
}

class _ResponsiveNavigationState extends State<ResponsiveNavigation> {
  int _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    final isWide = MediaQuery.of(context).size.width > 600;

    if (isWide) {
      return Row(
        children: [
          NavigationRail(
            selectedIndex: _currentIndex,
            onDestinationSelected: (index) {
              setState(() => _currentIndex = index);
            },
            labelType: NavigationRailLabelType.all,
            destinations: [
              NavigationRailDestination(icon: Icon(Icons.home), label: Text('首页')),
              NavigationRailDestination(icon: Icon(Icons.message), label: Text('消息')),
              NavigationRailDestination(icon: Icon(Icons.person), label: Text('我的')),
            ],
          ),
          VerticalDivider(thickness: 1, width: 1),
          Expanded(child: _buildBody()),
        ],
      );
    }

    return Scaffold(
      body: _buildBody(),
      bottomNavigationBar: NavigationBar(
        selectedIndex: _currentIndex,
        onDestinationSelected: (index) {
          setState(() => _currentIndex = index);
        },
        destinations: [
          NavigationDestination(icon: Icon(Icons.home), label: '首页'),
          NavigationDestination(icon: Icon(Icons.message), label: '消息'),
          NavigationDestination(icon: Icon(Icons.person), label: '我的'),
        ],
      ),
    );
  }

  Widget _buildBody() {
    return Center(child: Text('页面 $_currentIndex'));
  }
}
```

## 3. NavigationDrawer 抽屉导航

### 3.1 基本概念

NavigationDrawer 是 Material 3 推荐的侧边抽屉导航组件，替代传统的 Drawer。

### 3.2 基本语法

```dart
Scaffold(
  drawer: NavigationDrawer(
    selectedIndex: _selectedIndex,
    onDestinationSelected: (index) {
      setState(() => _selectedIndex = index);
      Navigator.pop(context);
    },
    children: [
      DrawerHeader(child: Text('应用名称')),
      NavigationDrawerDestination(icon: Icon(Icons.home), label: Text('首页')),
      NavigationDrawerDestination(icon: Icon(Icons.message), label: Text('消息')),
      NavigationDrawerDestination(icon: Icon(Icons.person), label: Text('我的')),
    ],
  ),
  body: Container(),
)
```

### 3.3 基础示例

```dart
class DrawerPage extends StatefulWidget {
  @override
  _DrawerPageState createState() => _DrawerPageState();
}

class _DrawerPageState extends State<DrawerPage> {
  int _selectedIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('抽屉导航')),
      drawer: NavigationDrawer(
        selectedIndex: _selectedIndex,
        onDestinationSelected: (index) {
          setState(() => _selectedIndex = index);
          Navigator.pop(context);
        },
        children: [
          Padding(
            padding: EdgeInsets.fromLTRB(28, 16, 16, 10),
            child: Text(
              '邮件',
              style: Theme.of(context).textTheme.titleSmall,
            ),
          ),
          NavigationDrawerDestination(
            icon: Icon(Icons.inbox_outlined),
            selectedIcon: Icon(Icons.inbox),
            label: Text('收件箱'),
          ),
          NavigationDrawerDestination(
            icon: Icon(Icons.send_outlined),
            selectedIcon: Icon(Icons.send),
            label: Text('已发送'),
          ),
          NavigationDrawerDestination(
            icon: Icon(Icons.drafts_outlined),
            selectedIcon: Icon(Icons.drafts),
            label: Text('草稿箱'),
          ),
          Divider(indent: 28, endIndent: 28),
          NavigationDrawerDestination(
            icon: Icon(Icons.settings_outlined),
            selectedIcon: Icon(Icons.settings),
            label: Text('设置'),
          ),
        ],
      ),
      body: Center(child: Text('页面 $_selectedIndex')),
    );
  }
}
```

## 4. 高级用法

### 4.1 带徽章的导航项

```dart
NavigationDestination(
  icon: Badge(
    label: Text('5'),
    child: Icon(Icons.message),
  ),
  label: '消息',
)
```

### 4.2 完整应用示例

```dart
class MainApp extends StatefulWidget {
  @override
  _MainAppState createState() => _MainAppState();
}

class _MainAppState extends State<MainApp> {
  int _currentIndex = 0;
  bool _isExtended = false;

  final _destinations = [
    (Icons.home_outlined, Icons.home, '首页'),
    (Icons.message_outlined, Icons.message, '消息'),
    (Icons.favorite_outlined, Icons.favorite, '收藏'),
    (Icons.person_outlined, Icons.person, '我的'),
  ];

  @override
  Widget build(BuildContext context) {
    final isWide = MediaQuery.of(context).size.width > 800;

    return Scaffold(
      body: Row(
        children: [
          if (isWide) _buildNavigationRail(isWide),
          Expanded(
            child: IndexedStack(
              index: _currentIndex,
              children: [
                HomePage(),
                MessagePage(),
                FavoritePage(),
                ProfilePage(),
              ],
            ),
          ),
        ],
      ),
      bottomNavigationBar: isWide ? null : _buildNavigationBar(),
    );
  }

  Widget _buildNavigationBar() {
    return NavigationBar(
      selectedIndex: _currentIndex,
      onDestinationSelected: (index) {
        setState(() => _currentIndex = index);
      },
      destinations: _destinations
          .map((d) => NavigationDestination(
                icon: Icon(d.$1),
                selectedIcon: Icon(d.$2),
                label: d.$3,
              ))
          .toList(),
    );
  }

  Widget _buildNavigationRail(bool isWide) {
    return NavigationRail(
      selectedIndex: _currentIndex,
      extended: _isExtended,
      onDestinationSelected: (index) {
        setState(() => _currentIndex = index);
      },
      leading: isWide
          ? IconButton(
              icon: Icon(_isExtended ? Icons.menu_open : Icons.menu),
              onPressed: () {
                setState(() => _isExtended = !_isExtended);
              },
            )
          : null,
      labelType: _isExtended
          ? NavigationRailLabelType.none
          : NavigationRailLabelType.all,
      destinations: _destinations
          .map((d) => NavigationRailDestination(
                icon: Icon(d.$1),
                selectedIcon: Icon(d.$2),
                label: Text(d.$3),
              ))
          .toList(),
    );
  }
}
```

## 5. 常见问题与解决方案

### 问题1：页面状态丢失

```dart
// 使用 IndexedStack 保持状态
IndexedStack(
  index: _currentIndex,
  children: [Page1(), Page2(), Page3()],
)
```

### 问题2：导航栏遮挡内容

```dart
// 确保使用正确的布局结构
Row(
  children: [
    NavigationRail(...),
    Expanded(child: _body),  // 使用 Expanded
  ],
)
```

### 问题3：扩展模式动画闪烁

```dart
// 使用 AnimatedContainer
AnimatedContainer(
  duration: Duration(milliseconds: 200),
  child: NavigationRail(
    extended: _isExtended,
    ...
  ),
)
```

### 问题4：NavigationBar vs BottomNavigationBar

```dart
// Material 3 推荐 NavigationBar
NavigationBar(...)  // ✅ 推荐

// 传统组件
BottomNavigationBar(...)  // ⚠️ 不推荐
```

## 6. 最佳实践

### 6.1 导航项数量

- NavigationBar: 3-5 个
- NavigationRail: 3-7 个
- 超过建议数量使用 NavigationDrawer

### 6.2 响应式布局

```dart
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth > 1200) {
      return _buildDesktopLayout();
    } else if (constraints.maxWidth > 600) {
      return _buildTabletLayout();
    }
    return _buildMobileLayout();
  },
)
```

### 6.3 统一目的地配置

```dart
class NavDestination {
  final IconData icon;
  final IconData selectedIcon;
  final String label;
  final Widget page;

  const NavDestination({
    required this.icon,
    required this.selectedIcon,
    required this.label,
    required this.page,
  });
}

final destinations = [
  NavDestination(
    icon: Icons.home_outlined,
    selectedIcon: Icons.home,
    label: '首页',
    page: HomePage(),
  ),
  // ...
];
```

## 总结

Flutter Material 3 导航组件提供了现代化的导航体验：

**NavigationBar**：
- 移动端底部导航
- 推荐 3-5 个导航项
- 支持指示器样式

**NavigationRail**：
- 平板/桌面端侧边导航
- 支持扩展/收缩模式
- 适合宽屏布局

**NavigationDrawer**：
- 抽屉式导航
- 适合导航项较多的场景

**最佳实践**：
1. 使用 IndexedStack 保持页面状态
2. 响应式切换不同导航组件
3. 统一配置导航项
4. 遵循 Material 3 设计规范

---

*最后更新: 2026-02-18*

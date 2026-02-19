# Flutter BottomNavigationBar 组件用法详解

## 简介

BottomNavigationBar 是底部导航栏组件，用于在应用底部显示导航选项。这是 Material 2 的传统组件，Material 3 推荐使用 NavigationBar 替代。

## 1. 基本用法

### 1.1 基本概念

BottomNavigationBar 的核心特点：
- 底部固定导航栏
- 支持 3-5 个导航项
- 支持图标和文字
- 固定或滚动模式

### 1.2 基本语法

```dart
Scaffold(
  body: _pages[_currentIndex],
  bottomNavigationBar: BottomNavigationBar(
    currentIndex: _currentIndex,
    onTap: (index) {
      setState(() {
        _currentIndex = index;
      });
    },
    items: [
      BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
      BottomNavigationBarItem(icon: Icon(Icons.message), label: '消息'),
      BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
    ],
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `items` | `List<BottomNavigationBarItem>` | - | 导航项列表（必需） |
| `currentIndex` | `int` | `0` | 当前选中索引 |
| `onTap` | `ValueChanged<int>?` | - | 点击回调 |
| `type` | `BottomNavigationBarType` | `fixed` | 类型 |
| `fixedColor` | `Color?` | - | 固定项颜色 |
| `selectedItemColor` | `Color?` | - | 选中项颜色 |
| `unselectedItemColor` | `Color?` | - | 未选中项颜色 |
| `backgroundColor` | `Color?` | - | 背景色 |
| `selectedFontSize` | `double` | `14` | 选中字体大小 |
| `unselectedFontSize` | `double` | `12` | 未选中字体大小 |
| `selectedLabelStyle` | `TextStyle?` | - | 选中标签样式 |
| `unselectedLabelStyle` | `TextStyle?` | - | 未选中标签样式 |
| `showSelectedLabels` | `bool` | `true` | 显示选中标签 |
| `showUnselectedLabels` | `bool` | `true` | 显示未选中标签 |
| `elevation` | `double?` | `8` | 阴影高度 |
| `iconSize` | `double` | `24` | 图标大小 |
| `selectedIconTheme` | `IconThemeData?` | - | 选中图标主题 |
| `unselectedIconTheme` | `IconThemeData?` | - | 未选中图标主题 |

### 1.4 BottomNavigationBarItem 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `icon` | `Widget` | 图标（必需） |
| `label` | `String?` | 标签文字 |
| `activeIcon` | `Widget?` | 选中时图标 |
| `backgroundColor` | `Color?` | 背景色（滚动模式） |
| `tooltip` | `String?` | 长按提示 |

### 1.5 基础示例

#### 简单底部导航

```dart
class SimpleBottomNav extends StatefulWidget {
  @override
  _SimpleBottomNavState createState() => _SimpleBottomNavState();
}

class _SimpleBottomNavState extends State<SimpleBottomNav> {
  int _currentIndex = 0;

  final _pages = [
    Center(child: Text('首页', style: TextStyle(fontSize: 24))),
    Center(child: Text('消息', style: TextStyle(fontSize: 24))),
    Center(child: Text('我的', style: TextStyle(fontSize: 24))),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _pages[_currentIndex],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (index) {
          setState(() {
            _currentIndex = index;
          });
        },
        items: [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
          BottomNavigationBarItem(icon: Icon(Icons.message), label: '消息'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
        ],
      ),
    );
  }
}
```

#### 带选中图标的导航

```dart
BottomNavigationBar(
  currentIndex: _currentIndex,
  onTap: (index) {
    setState(() => _currentIndex = index);
  },
  items: [
    BottomNavigationBarItem(
      icon: Icon(Icons.home_outlined),
      activeIcon: Icon(Icons.home),
      label: '首页',
    ),
    BottomNavigationBarItem(
      icon: Icon(Icons.message_outlined),
      activeIcon: Icon(Icons.message),
      label: '消息',
    ),
    BottomNavigationBarItem(
      icon: Icon(Icons.person_outline),
      activeIcon: Icon(Icons.person),
      label: '我的',
    ),
  ],
)
```

## 2. 导航类型

### 2.1 fixed 模式（默认）

```dart
BottomNavigationBar(
  type: BottomNavigationBarType.fixed,
  items: [
    BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
    BottomNavigationBarItem(icon: Icon(Icons.message), label: '消息'),
    BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
  ],
)
```

特点：
- 所有项平均分布
- 始终显示标签
- 适合 3-4 个导航项

### 2.2 shifting 模式

```dart
BottomNavigationBar(
  type: BottomNavigationBarType.shifting,
  currentIndex: _currentIndex,
  onTap: (index) {
    setState(() => _currentIndex = index);
  },
  items: [
    BottomNavigationBarItem(
      icon: Icon(Icons.home),
      label: '首页',
      backgroundColor: Colors.blue,
    ),
    BottomNavigationBarItem(
      icon: Icon(Icons.message),
      label: '消息',
      backgroundColor: Colors.green,
    ),
    BottomNavigationBarItem(
      icon: Icon(Icons.person),
      label: '我的',
      backgroundColor: Colors.orange,
    ),
  ],
)
```

特点：
- 选中项放大，未选中缩小
- 点击时背景色变化
- 未选中项隐藏标签
- 适合 4-5 个导航项

## 3. 自定义样式

### 3.1 自定义颜色

```dart
BottomNavigationBar(
  currentIndex: _currentIndex,
  selectedItemColor: Colors.blue,
  unselectedItemColor: Colors.grey,
  onTap: (index) {
    setState(() => _currentIndex = index);
  },
  items: [
    BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
    BottomNavigationBarItem(icon: Icon(Icons.message), label: '消息'),
    BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
  ],
)
```

### 3.2 自定义字体大小

```dart
BottomNavigationBar(
  currentIndex: _currentIndex,
  selectedFontSize: 14,
  unselectedFontSize: 12,
  onTap: (index) {
    setState(() => _currentIndex = index);
  },
  items: [
    BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
    BottomNavigationBarItem(icon: Icon(Icons.message), label: '消息'),
    BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
  ],
)
```

### 3.3 隐藏标签

```dart
// 隐藏未选中标签
BottomNavigationBar(
  showUnselectedLabels: false,
  items: [...],
)

// 隐藏所有标签
BottomNavigationBar(
  showSelectedLabels: false,
  showUnselectedLabels: false,
  items: [...],
)
```

### 3.4 自定义背景色

```dart
BottomNavigationBar(
  backgroundColor: Colors.white,
  elevation: 8,
  items: [...],
)
```

### 3.5 自定义图标大小

```dart
BottomNavigationBar(
  iconSize: 28,
  selectedIconTheme: IconThemeData(size: 32),
  items: [...],
)
```

## 4. 完整示例

### 4.1 保持页面状态

```dart
class PersistentBottomNav extends StatefulWidget {
  @override
  _PersistentBottomNavState createState() => _PersistentBottomNavState();
}

class _PersistentBottomNavState extends State<PersistentBottomNav> {
  int _currentIndex = 0;

  final _pages = <Widget>[
    HomePage(),
    MessagePage(),
    ProfilePage(),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(
        index: _currentIndex,
        children: _pages,
      ),
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        type: BottomNavigationBarType.fixed,
        selectedItemColor: Colors.blue,
        onTap: (index) {
          setState(() {
            _currentIndex = index;
          });
        },
        items: [
          BottomNavigationBarItem(
            icon: Icon(Icons.home_outlined),
            activeIcon: Icon(Icons.home),
            label: '首页',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.message_outlined),
            activeIcon: Icon(Icons.message),
            label: '消息',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.person_outline),
            activeIcon: Icon(Icons.person),
            label: '我的',
          ),
        ],
      ),
    );
  }
}

// 页面使用 AutomaticKeepAliveClientMixin 保持状态
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage>
    with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);
    return Center(child: Text('首页'));
  }
}
```

### 4.2 带徽章的导航

```dart
BottomNavigationBar(
  currentIndex: _currentIndex,
  onTap: (index) {
    setState(() => _currentIndex = index);
  },
  items: [
    BottomNavigationBarItem(
      icon: Icon(Icons.home),
      label: '首页',
    ),
    BottomNavigationBarItem(
      icon: Stack(
        children: [
          Icon(Icons.message),
          Positioned(
            right: 0,
            top: 0,
            child: Container(
              padding: EdgeInsets.all(4),
              decoration: BoxDecoration(
                color: Colors.red,
                shape: BoxShape.circle,
              ),
              child: Text(
                '3',
                style: TextStyle(color: Colors.white, fontSize: 10),
              ),
            ),
          ),
        ],
      ),
      label: '消息',
    ),
    BottomNavigationBarItem(
      icon: Icon(Icons.person),
      label: '我的',
    ),
  ],
)
```

### 4.3 四个导航项

```dart
BottomNavigationBar(
  type: BottomNavigationBarType.fixed,
  currentIndex: _currentIndex,
  onTap: (index) {
    setState(() => _currentIndex = index);
  },
  items: [
    BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
    BottomNavigationBarItem(icon: Icon(Icons.category), label: '分类'),
    BottomNavigationBarItem(icon: Icon(Icons.shopping_cart), label: '购物车'),
    BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
  ],
)
```

## 5. BottomNavigationBar vs NavigationBar

| 特性 | BottomNavigationBar | NavigationBar (M3) |
|------|---------------------|-------------------|
| 设计规范 | Material 2 | Material 3 |
| 推荐程度 | 传统组件 | 推荐 |
| 指示器 | 无 | 有 |
| 样式 | 相对固定 | 更现代 |
| 最大项数 | 5 | 5 |

### 5.1 使用 NavigationBar（推荐）

```dart
NavigationBar(
  selectedIndex: _currentIndex,
  onDestinationSelected: (index) {
    setState(() => _currentIndex = index);
  },
  destinations: [
    NavigationDestination(icon: Icon(Icons.home), label: '首页'),
    NavigationDestination(icon: Icon(Icons.message), label: '消息'),
    NavigationDestination(icon: Icon(Icons.person), label: '我的'),
  ],
)
```

## 6. 高级用法

### 6.1 双击退出

```dart
class DoubleBackExit extends StatefulWidget {
  @override
  _DoubleBackExitState createState() => _DoubleBackExitState();
}

class _DoubleBackExitState extends State<DoubleBackExit> {
  int _currentIndex = 0;
  DateTime? _lastPressed;

  @override
  Widget build(BuildContext context) {
    return WillPopScope(
      onWillPop: () async {
        if (_currentIndex != 0) {
          setState(() => _currentIndex = 0);
          return false;
        }
        
        final now = DateTime.now();
        if (_lastPressed == null || now.difference(_lastPressed!) > Duration(seconds: 2)) {
          _lastPressed = now;
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('再按一次退出应用')),
          );
          return false;
        }
        return true;
      },
      child: Scaffold(
        body: IndexedStack(
          index: _currentIndex,
          children: [HomePage(), MessagePage(), ProfilePage()],
        ),
        bottomNavigationBar: BottomNavigationBar(
          currentIndex: _currentIndex,
          onTap: (index) => setState(() => _currentIndex = index),
          items: [
            BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
            BottomNavigationBarItem(icon: Icon(Icons.message), label: '消息'),
            BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
          ],
        ),
      ),
    );
  }
}
```

### 6.2 隐藏特定页面的导航栏

```dart
class HideableBottomNav extends StatefulWidget {
  @override
  _HideableBottomNavState createState() => _HideableBottomNavState();
}

class _HideableBottomNavState extends State<HideableBottomNav> {
  int _currentIndex = 0;
  bool _showBottomNav = true;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(
        index: _currentIndex,
        children: [
          HomePage(),
          MessagePage(onScroll: (show) {
            setState(() => _showBottomNav = show);
          }),
          ProfilePage(),
        ],
      ),
      bottomNavigationBar: _showBottomNav
          ? BottomNavigationBar(
              currentIndex: _currentIndex,
              onTap: (index) => setState(() => _currentIndex = index),
              items: [
                BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
                BottomNavigationBarItem(icon: Icon(Icons.message), label: '消息'),
                BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
              ],
            )
          : null,
    );
  }
}
```

## 7. 常见问题与解决方案

### 问题1：超过 3 个导航项颜色问题

```dart
// 超过 3 个导航项默认使用 shifting 模式
// 需要显式设置 type
BottomNavigationBar(
  type: BottomNavigationBarType.fixed,  // 固定为 fixed 模式
  items: [
    // 4-5 个导航项
  ],
)
```

### 问题2：页面状态丢失

```dart
// 使用 IndexedStack 保持状态
IndexedStack(
  index: _currentIndex,
  children: _pages,
)
```

### 问题3：图标颜色不正确

```dart
// 确保没有同时使用 fixedColor 和 selectedItemColor
BottomNavigationBar(
  // fixedColor: Colors.blue,  // 移除
  selectedItemColor: Colors.blue,
  unselectedItemColor: Colors.grey,
  items: [...],
)
```

### 问题4：标签显示问题

```dart
// shifting 模式默认隐藏未选中标签
// 使用 fixed 模式或显式设置
BottomNavigationBar(
  showUnselectedLabels: true,
  items: [...],
)
```

### 问题5：导航栏遮挡内容

```dart
// 确保使用 Scaffold 的 bottomNavigationBar 属性
Scaffold(
  body: SafeArea(child: _pages[_currentIndex]),
  bottomNavigationBar: BottomNavigationBar(...),
)
```

## 8. 最佳实践

### 8.1 统一导航配置

```dart
class AppBottomNav extends StatelessWidget {
  final int currentIndex;
  final ValueChanged<int> onTap;

  const AppBottomNav({
    required this.currentIndex,
    required this.onTap,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return BottomNavigationBar(
      type: BottomNavigationBarType.fixed,
      currentIndex: currentIndex,
      selectedItemColor: Theme.of(context).primaryColor,
      unselectedItemColor: Colors.grey,
      onTap: onTap,
      items: [
        BottomNavigationBarItem(
          icon: Icon(Icons.home_outlined),
          activeIcon: Icon(Icons.home),
          label: '首页',
        ),
        BottomNavigationBarItem(
          icon: Icon(Icons.message_outlined),
          activeIcon: Icon(Icons.message),
          label: '消息',
        ),
        BottomNavigationBarItem(
          icon: Icon(Icons.person_outline),
          activeIcon: Icon(Icons.person),
          label: '我的',
        ),
      ],
    );
  }
}
```

### 8.2 使用枚举

```dart
enum TabItem { home, message, profile }

class TabHelper {
  static BottomNavigationBarItem item(TabItem item) {
    switch (item) {
      case TabItem.home:
        return BottomNavigationBarItem(
          icon: Icon(Icons.home_outlined),
          activeIcon: Icon(Icons.home),
          label: '首页',
        );
      case TabItem.message:
        return BottomNavigationBarItem(
          icon: Icon(Icons.message_outlined),
          activeIcon: Icon(Icons.message),
          label: '消息',
        );
      case TabItem.profile:
        return BottomNavigationBarItem(
          icon: Icon(Icons.person_outline),
          activeIcon: Icon(Icons.person),
          label: '我的',
        );
    }
  }
}
```

## 总结

BottomNavigationBar 是 Flutter Material 2 的底部导航组件：

**核心要点**：
1. 适合 3-5 个导航项
2. fixed 和 shifting 两种模式
3. Material 3 推荐使用 NavigationBar 替代
4. 配合 IndexedStack 保持页面状态

**两种模式**：
- `fixed` - 固定布局，所有项平均分布
- `shifting` - 滚动布局，选中项放大

**最佳实践**：
1. 使用 IndexedStack 保持页面状态
2. 统一导航栏样式配置
3. 新项目推荐使用 NavigationBar
4. 合理设置导航项数量

---

*最后更新: 2026-02-18*

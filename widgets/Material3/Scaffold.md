# Flutter Scaffold 组件用法详解

## 简介

Scaffold 是 Flutter 中 Material 设计的基础页面骨架组件，提供了应用程序页面的基本结构，包括 AppBar、Body、FloatingActionButton、BottomNavigationBar、Drawer 等核心组件的布局和协调。

## 1. 初级用法

### 1.1 基本概念

Scaffold 实现了基本的 Material Design 视觉布局结构，提供：
- 顶部应用栏（AppBar）
- 页面主体内容（Body）
- 浮动操作按钮（FloatingActionButton）
- 底部导航栏（BottomNavigationBar）
- 侧边抽屉菜单（Drawer）
- 底部弹出菜单（BottomSheet）
- 消息提示（SnackBar）

### 1.2 基本语法

```dart
Scaffold(
  appBar: AppBar(title: Text('页面标题')),
  body: Center(child: Text('页面内容')),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `appBar` | `PreferredSizeWidget?` | 顶部应用栏 |
| `body` | `Widget?` | 页面主体内容 |
| `floatingActionButton` | `Widget?` | 浮动操作按钮 |
| `floatingActionButtonLocation` | `FloatingActionButtonLocation?` | FAB 位置 |
| `floatingActionButtonAnimator` | `FloatingActionButtonAnimator?` | FAB 动画 |
| `persistentFooterButtons` | `List<Widget>?` | 底部固定按钮 |
| `drawer` | `Widget?` | 左侧抽屉菜单 |
| `endDrawer` | `Widget?` | 右侧抽屉菜单 |
| `bottomNavigationBar` | `Widget?` | 底部导航栏 |
| `bottomSheet` | `Widget?` | 底部弹出层 |
| `backgroundColor` | `Color?` | 背景颜色 |
| `resizeToAvoidBottomInset` | `bool?` | 键盘弹出时是否调整大小 |
| `primary` | `bool` | 是否计算状态栏高度 |

### 1.4 基础示例

#### 最简单的 Scaffold

```dart
Scaffold(
  appBar: AppBar(
    title: Text('Hello Flutter'),
  ),
  body: Center(
    child: Text('页面内容'),
  ),
)
```

#### 完整结构的 Scaffold

```dart
Scaffold(
  appBar: AppBar(
    title: Text('完整示例'),
    actions: [
      IconButton(icon: Icon(Icons.search), onPressed: () {}),
      IconButton(icon: Icon(Icons.more_vert), onPressed: () {}),
    ],
  ),
  drawer: Drawer(
    child: ListView(
      children: [
        DrawerHeader(child: Text('菜单')),
        ListTile(title: Text('项目1')),
        ListTile(title: Text('项目2')),
      ],
    ),
  ),
  body: Center(child: Text('主体内容')),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
  bottomNavigationBar: BottomNavigationBar(
    items: [
      BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
      BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
    ],
  ),
)
```

## 2. 中级用法

### 2.1 AppBar 配置

```dart
Scaffold(
  appBar: AppBar(
    // 标题
    title: Text('页面标题'),
    // 副标题（配合 Column 使用）
    // title: Column(
    //   crossAxisAlignment: CrossAxisAlignment.start,
    //   children: [
    //     Text('主标题'),
    //     Text('副标题', style: TextStyle(fontSize: 12)),
    //   ],
    // ),
    
    // 左侧返回按钮（自动添加或手动设置）
    leading: IconButton(
      icon: Icon(Icons.menu),
      onPressed: () {},
    ),
    
    // 右侧操作按钮
    actions: [
      IconButton(icon: Icon(Icons.search), onPressed: () {}),
      IconButton(icon: Icon(Icons.notifications), onPressed: () {}),
      PopupMenuButton(
        itemBuilder: (context) => [
          PopupMenuItem(value: 1, child: Text('设置')),
          PopupMenuItem(value: 2, child: Text('关于')),
        ],
      ),
    ],
    
    // 底部组件（如 TabBar）
    bottom: TabBar(
      tabs: [
        Tab(text: '标签1'),
        Tab(text: '标签2'),
      ],
    ),
    
    // 样式设置
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
    elevation: 4,
    centerTitle: true,
    
    // 控制返回按钮显示
    automaticallyImplyLeading: true,
  ),
  body: Container(),
)
```

### 2.2 FloatingActionButton

```dart
Scaffold(
  floatingActionButton: FloatingActionButton(
    onPressed: () {
      // 点击事件
    },
    child: Icon(Icons.add),
    // 样式
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
    elevation: 6,
    shape: CircleBorder(),
    // 或使用迷你尺寸
    // mini: true,
  ),
  
  // FAB 位置
  floatingActionButtonLocation: FloatingActionButtonLocation.endFloat,
  // 其他位置选项：
  // - centerDocked: 底部居中（与 BottomAppBar 配合）
  // - centerFloat: 底部居中悬浮
  // - endDocked: 右下角（与 BottomAppBar 配合）
  // - endFloat: 右下角悬浮（默认）
  // - startFloat: 左下角悬浮
  // - startDocked: 左下角（与 BottomAppBar 配合）
  
  body: Container(),
)
```

#### FloatingActionButton.extended

```dart
FloatingActionButton.extended(
  onPressed: () {},
  icon: Icon(Icons.add),
  label: Text('添加'),
  backgroundColor: Colors.blue,
)
```

#### 多个 FAB

```dart
floatingActionButton: Column(
  mainAxisAlignment: MainAxisAlignment.end,
  children: [
    FloatingActionButton(
      heroTag: 'fab1',
      onPressed: () {},
      child: Icon(Icons.edit),
    ),
    SizedBox(height: 16),
    FloatingActionButton(
      heroTag: 'fab2',
      onPressed: () {},
      child: Icon(Icons.add),
    ),
  ],
)
```

### 2.3 Drawer 抽屉菜单

```dart
Scaffold(
  appBar: AppBar(title: Text('抽屉菜单')),
  drawer: Drawer(
    child: ListView(
      padding: EdgeInsets.zero,
      children: [
        // 头部
        DrawerHeader(
          decoration: BoxDecoration(color: Colors.blue),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            mainAxisAlignment: MainAxisAlignment.end,
            children: [
              CircleAvatar(
                radius: 30,
                child: Icon(Icons.person, size: 30),
              ),
              SizedBox(height: 10),
              Text(
                '用户名',
                style: TextStyle(color: Colors.white, fontSize: 18),
              ),
              Text(
                'user@example.com',
                style: TextStyle(color: Colors.white70),
              ),
            ],
          ),
        ),
        // 菜单项
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
        Divider(),
        ListTile(
          leading: Icon(Icons.logout),
          title: Text('退出登录'),
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

#### UserAccountsDrawerHeader

```dart
drawer: Drawer(
  child: ListView(
    children: [
      UserAccountsDrawerHeader(
        accountName: Text('用户名'),
        accountEmail: Text('user@example.com'),
        currentAccountPicture: CircleAvatar(
          child: Icon(Icons.person),
        ),
        otherAccountsPictures: [
          CircleAvatar(child: Icon(Icons.add)),
        ],
        decoration: BoxDecoration(color: Colors.blue),
      ),
      ListTile(leading: Icon(Icons.home), title: Text('首页')),
      ListTile(leading: Icon(Icons.settings), title: Text('设置')),
    ],
  ),
)
```

### 2.4 BottomNavigationBar

```dart
class BottomNavExample extends StatefulWidget {
  @override
  _BottomNavExampleState createState() => _BottomNavExampleState();
}

class _BottomNavExampleState extends State<BottomNavExample> {
  int _currentIndex = 0;

  final List<Widget> _pages = [
    Center(child: Text('首页')),
    Center(child: Text('搜索')),
    Center(child: Text('个人中心')),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('底部导航')),
      body: _pages[_currentIndex],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (index) {
          setState(() {
            _currentIndex = index;
          });
        },
        items: [
          BottomNavigationBarItem(
            icon: Icon(Icons.home),
            label: '首页',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.search),
            label: '搜索',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.person),
            label: '我的',
          ),
        ],
        // 样式设置
        type: BottomNavigationBarType.fixed,  // 固定模式（超过3个时需要）
        selectedItemColor: Colors.blue,
        unselectedItemColor: Colors.grey,
        selectedFontSize: 12,
        unselectedFontSize: 12,
      ),
    );
  }
}
```

### 2.5 SnackBar 消息提示

```dart
Scaffold(
  body: Center(
    child: ElevatedButton(
      onPressed: () {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(
            content: Text('这是一条消息'),
            duration: Duration(seconds: 2),
            action: SnackBarAction(
              label: '撤销',
              onPressed: () {
                // 撤销操作
              },
            ),
          ),
        );
      },
      child: Text('显示消息'),
    ),
  ),
)
```

## 3. 高级用法

### 3.1 TabBar + TabBarView

```dart
class TabBarExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          title: Text('TabBar 示例'),
          bottom: TabBar(
            tabs: [
              Tab(icon: Icon(Icons.home), text: '首页'),
              Tab(icon: Icon(Icons.search), text: '搜索'),
              Tab(icon: Icon(Icons.person), text: '我的'),
            ],
          ),
        ),
        body: TabBarView(
          children: [
            Center(child: Text('首页内容')),
            Center(child: Text('搜索内容')),
            Center(child: Text('个人中心内容')),
          ],
        ),
      ),
    );
  }
}
```

### 3.2 BottomSheet

```dart
Scaffold(
  body: Center(
    child: ElevatedButton(
      onPressed: () {
        showModalBottomSheet(
          context: context,
          builder: (context) {
            return Container(
              height: 200,
              child: Column(
                children: [
                  ListTile(
                    leading: Icon(Icons.share),
                    title: Text('分享'),
                    onTap: () => Navigator.pop(context),
                  ),
                  ListTile(
                    leading: Icon(Icons.copy),
                    title: Text('复制'),
                    onTap: () => Navigator.pop(context),
                  ),
                  ListTile(
                    leading: Icon(Icons.delete),
                    title: Text('删除'),
                    onTap: () => Navigator.pop(context),
                  ),
                ],
              ),
            );
          },
        );
      },
      child: Text('显示底部菜单'),
    ),
  ),
)
```

### 3.3 嵌套 Scaffold

```dart
class NestedScaffoldExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('外层 Scaffold')),
      body: Navigator(
        onGenerateRoute: (settings) {
          return MaterialPageRoute(
            builder: (context) => Scaffold(
              appBar: AppBar(title: Text('内层 Scaffold')),
              body: Center(child: Text('内层内容')),
            ),
          );
        },
      ),
    );
  }
}
```

### 3.4 键盘处理

```dart
Scaffold(
  // 键盘弹出时是否调整布局
  resizeToAvoidBottomInset: true,  // 默认 true
  
  body: SingleChildScrollView(
    child: Padding(
      padding: EdgeInsets.all(16),
      child: Column(
        children: [
          TextField(
            decoration: InputDecoration(labelText: '输入框1'),
          ),
          SizedBox(height: 16),
          TextField(
            decoration: InputDecoration(labelText: '输入框2'),
          ),
          // ...更多输入框
        ],
      ),
    ),
  ),
)
```

### 3.5 Material 3 的 NavigationBar

```dart
class Material3NavExample extends StatefulWidget {
  @override
  _Material3NavExampleState createState() => _Material3NavExampleState();
}

class _Material3NavExampleState extends State<Material3NavExample> {
  int _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(child: Text('页面 $_currentIndex')),
      bottomNavigationBar: NavigationBar(
        selectedIndex: _currentIndex,
        onDestinationSelected: (index) {
          setState(() => _currentIndex = index);
        },
        destinations: [
          NavigationDestination(
            icon: Icon(Icons.home_outlined),
            selectedIcon: Icon(Icons.home),
            label: '首页',
          ),
          NavigationDestination(
            icon: Icon(Icons.search_outlined),
            selectedIcon: Icon(Icons.search),
            label: '搜索',
          ),
          NavigationDestination(
            icon: Icon(Icons.person_outline),
            selectedIcon: Icon(Icons.person),
            label: '我的',
          ),
        ],
      ),
    );
  }
}
```

## 4. Scaffold 状态管理

### 4.1 Scaffold.of

```dart
class ScaffoldStateExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Scaffold State')),
      body: Builder(
        builder: (context) {
          return Center(
            child: ElevatedButton(
              onPressed: () {
                // 使用 Builder 确保 context 正确
                Scaffold.of(context).openDrawer();
              },
              child: Text('打开抽屉'),
            ),
          );
        },
      ),
      drawer: Drawer(child: Text('抽屉内容')),
    );
  }
}
```

### 4.2 ScaffoldMessenger

```dart
class SnackBarExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            // 使用 ScaffoldMessenger 显示 SnackBar
            ScaffoldMessenger.of(context).showSnackBar(
              SnackBar(content: Text('消息提示')),
            );
          },
          child: Text('显示消息'),
        ),
      ),
    );
  }
}
```

## 5. 常见问题与解决方案

### 问题1：Scaffold.of 报错

```dart
// ❌ 错误：context 不正确
Scaffold(
  body: ElevatedButton(
    onPressed: () {
      Scaffold.of(context).openDrawer();  // 报错
    },
    child: Text('打开抽屉'),
  ),
)

// ✅ 解决方案1：使用 Builder
Scaffold(
  body: Builder(
    builder: (context) {
      return ElevatedButton(
        onPressed: () {
          Scaffold.of(context).openDrawer();
        },
        child: Text('打开抽屉'),
      );
    },
  ),
)

// ✅ 解决方案2：使用 GlobalKey
final _scaffoldKey = GlobalKey<ScaffoldState>();

Scaffold(
  key: _scaffoldKey,
  body: ElevatedButton(
    onPressed: () {
      _scaffoldKey.currentState?.openDrawer();
    },
    child: Text('打开抽屉'),
  ),
)
```

### 问题2：键盘遮挡输入框

```dart
// 设置 resizeToAvoidBottomInset
Scaffold(
  resizeToAvoidBottomInset: true,  // 默认 true
  body: SingleChildScrollView(...),
)
```

### 问题3：BottomNavigationBar 超过3个选项时显示异常

```dart
bottomNavigationBar: BottomNavigationBar(
  type: BottomNavigationBarType.fixed,  // 必须设置
  items: [
    BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
    BottomNavigationBarItem(icon: Icon(Icons.search), label: '搜索'),
    BottomNavigationBarItem(icon: Icon(Icons.favorite), label: '收藏'),
    BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
  ],
)
```

### 问题4：SnackBar 显示后立即消失

```dart
// 问题：快速连续调用 showSnackBar
ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('消息1')));
ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('消息2')));

// 解决方案：先清除之前的
ScaffoldMessenger.of(context).clearSnackBars();
ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('消息')));
```

## 6. 最佳实践

### 6.1 使用 StatefulWidget 管理状态

```dart
class MyPage extends StatefulWidget {
  @override
  _MyPageState createState() => _MyPageState();
}

class _MyPageState extends State<MyPage> {
  int _currentIndex = 0;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('My App')),
      body: _buildBody(),
      bottomNavigationBar: _buildBottomNav(),
    );
  }
  
  Widget _buildBody() {
    // 构建主体内容
  }
  
  Widget _buildBottomNav() {
    // 构建底部导航
  }
}
```

### 6.2 合理使用 Drawer

```dart
// 抽屉菜单项点击后关闭抽屉
ListTile(
  title: Text('设置'),
  onTap: () {
    Navigator.pop(context);  // 关闭抽屉
    // 导航到设置页面
    Navigator.push(context, MaterialPageRoute(
      builder: (context) => SettingsPage(),
    ));
  },
)
```

### 6.3 FAB 与 BottomAppBar 配合

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
      mainAxisAlignment: MainAxisAlignment.spaceAround,
      children: [
        IconButton(icon: Icon(Icons.home), onPressed: () {}),
        SizedBox(width: 48),  // FAB 占位
        IconButton(icon: Icon(Icons.person), onPressed: () {}),
      ],
    ),
  ),
)
```

## 总结

Scaffold 是 Flutter Material 应用的基础骨架组件：

**核心要点**：
1. 提供完整的页面结构布局
2. 支持 AppBar、Drawer、BottomNavigationBar 等核心组件
3. 提供 SnackBar 消息提示功能
4. 支持键盘弹出时的布局调整

**主要组件**：
- **AppBar**：顶部应用栏
- **Body**：页面主体
- **FloatingActionButton**：浮动操作按钮
- **Drawer**：侧边抽屉
- **BottomNavigationBar**：底部导航

**最佳实践**：
1. 使用 StatefulWidget 管理页面状态
2. 合理使用 Builder 或 GlobalKey
3. 配合 SingleChildScrollView 处理键盘遮挡
4. Material 3 推荐使用 NavigationBar

---

*最后更新: 2026-02-18*

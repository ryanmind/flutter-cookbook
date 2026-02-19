# Flutter Offstage 组件用法详解

## 简介

Offstage 是 Flutter 中用于控制子组件显示/隐藏的布局组件。与 Visibility 不同，Offstage 的子组件仍然存在于 Widget 树中，只是不被渲染。

## 1. 初级用法

### 1.1 基本概念

Offstage 的核心作用：
- 通过 offstage 属性控制子组件是否渲染
- 隐藏时不占用布局空间
- 子组件仍在 Widget 树中，保持状态

### 1.2 基本语法

```dart
Offstage(
  offstage: true,  // true = 隐藏，false = 显示
  child: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `offstage` | `bool` | `true` | 是否隐藏 |
| `child` | `Widget?` | - | 子组件 |

### 1.4 基础示例

#### 切换显示隐藏

```dart
class OffstageExample extends StatefulWidget {
  @override
  State<OffstageExample> createState() => _OffstageExampleState();
}

class _OffstageExampleState extends State<OffstageExample> {
  bool _isVisible = true;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Offstage(
          offstage: !_isVisible,
          child: Container(
            width: 200,
            height: 100,
            color: Colors.blue,
            child: Center(child: Text('可切换的内容')),
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _isVisible = !_isVisible),
          child: Text(_isVisible ? '隐藏' : '显示'),
        ),
      ],
    );
  }
}
```

#### 初始隐藏

```dart
Offstage(
  offstage: true,
  child: Container(
    color: Colors.red,
    child: Text('初始隐藏'),
  ),
)
```

## 2. 中级用法

### 2.1 保持状态的隐藏

```dart
class StatefulOffstage extends StatefulWidget {
  @override
  State<StatefulOffstage> createState() => _StatefulOffstageState();
}

class _StatefulOffstageState extends State<StatefulOffstage> {
  bool _showCounter = true;
  int _counter = 0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Offstage(
          offstage: !_showCounter,
          child: Container(
            padding: EdgeInsets.all(16),
            color: Colors.blue,
            child: Text(
              '计数器: $_counter',
              style: TextStyle(fontSize: 24),
            ),
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _counter++),
          child: Text('增加'),
        ),
        ElevatedButton(
          onPressed: () => setState(() => _showCounter = !_showCounter),
          child: Text(_showCounter ? '隐藏计数器' : '显示计数器'),
        ),
      ],
    );
  }
}
// 注意：隐藏后计数器状态保持，再次显示时数值不变
```

### 2.2 标签页切换

```dart
class TabSwitcher extends StatefulWidget {
  @override
  State<TabSwitcher> createState() => _TabSwitcherState();
}

class _TabSwitcherState extends State<TabSwitcher> {
  int _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          children: [
            _buildTab(0, '首页'),
            _buildTab(1, '发现'),
            _buildTab(2, '我的'),
          ],
        ),
        Expanded(
          child: Stack(
            children: [
              Offstage(
                offstage: _currentIndex != 0,
                child: _HomePage(),
              ),
              Offstage(
                offstage: _currentIndex != 1,
                child: _DiscoverPage(),
              ),
              Offstage(
                offstage: _currentIndex != 2,
                child: _ProfilePage(),
              ),
            ],
          ),
        ),
      ],
    );
  }

  Widget _buildTab(int index, String label) {
    return Expanded(
      child: TextButton(
        onPressed: () => setState(() => _currentIndex = index),
        child: Text(
          label,
          style: TextStyle(
            color: _currentIndex == index ? Colors.blue : Colors.grey,
          ),
        ),
      ),
    );
  }
}
```

### 2.3 表单步骤

```dart
class StepperForm extends StatefulWidget {
  @override
  State<StepperForm> createState() => _StepperFormState();
}

class _StepperFormState extends State<StepperForm> {
  int _currentStep = 0;
  final _nameController = TextEditingController();
  final _emailController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // 步骤指示器
        Row(
          children: List.generate(3, (index) {
            return Expanded(
              child: Container(
                padding: EdgeInsets.all(8),
                color: _currentStep == index ? Colors.blue : Colors.grey[200],
                child: Text(
                  '步骤 ${index + 1}',
                  textAlign: TextAlign.center,
                  style: TextStyle(
                    color: _currentStep == index ? Colors.white : Colors.black,
                  ),
                ),
              ),
            );
          }),
        ),
        // 步骤内容
        Expanded(
          child: Stack(
            children: [
              Offstage(
                offstage: _currentStep != 0,
                child: _buildStep1(),
              ),
              Offstage(
                offstage: _currentStep != 1,
                child: _buildStep2(),
              ),
              Offstage(
                offstage: _currentStep != 2,
                child: _buildStep3(),
              ),
            ],
          ),
        ),
        // 导航按钮
        Row(
          children: [
            if (_currentStep > 0)
              TextButton(
                onPressed: () => setState(() => _currentStep--),
                child: Text('上一步'),
              ),
            Spacer(),
            ElevatedButton(
              onPressed: () {
                if (_currentStep < 2) {
                  setState(() => _currentStep++);
                } else {
                  // 提交表单
                }
              },
              child: Text(_currentStep < 2 ? '下一步' : '提交'),
            ),
          ],
        ),
      ],
    );
  }

  Widget _buildStep1() => Padding(
    padding: EdgeInsets.all(16),
    child: TextField(
      controller: _nameController,
      decoration: InputDecoration(labelText: '姓名'),
    ),
  );

  Widget _buildStep2() => Padding(
    padding: EdgeInsets.all(16),
    child: TextField(
      controller: _emailController,
      decoration: InputDecoration(labelText: '邮箱'),
    ),
  );

  Widget _buildStep3() => Padding(
    padding: EdgeInsets.all(16),
    child: Column(
      children: [
        Text('姓名: ${_nameController.text}'),
        Text('邮箱: ${_emailController.text}'),
      ],
    ),
  );
}
```

### 2.4 条件渲染

```dart
Offstage(
  offstage: isLoading,
  child: ListView.builder(
    itemCount: items.length,
    itemBuilder: (context, index) => ListTile(title: Text(items[index])),
  ),
)
```

## 3. 高级用法

### 3.1 动画切换

```dart
class AnimatedOffstage extends StatefulWidget {
  @override
  State<AnimatedOffstage> createState() => _AnimatedOffstageState();
}

class _AnimatedOffstageState extends State<AnimatedOffstage>
    with SingleTickerProviderStateMixin {
  bool _isVisible = true;
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 300),
      vsync: this,
    );
    _animation = CurvedAnimation(parent: _controller, curve: Curves.easeInOut);
  }

  void _toggle() {
    setState(() => _isVisible = !_isVisible);
    if (_isVisible) {
      _controller.forward();
    } else {
      _controller.reverse();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        SizeTransition(
          sizeFactor: _animation,
          child: Container(
            height: 100,
            color: Colors.blue,
            child: Center(child: Text('动画内容')),
          ),
        ),
        ElevatedButton(
          onPressed: _toggle,
          child: Text(_isVisible ? '隐藏' : '显示'),
        ),
      ],
    );
  }
}
```

### 3.2 预加载内容

```dart
class PreloadContent extends StatefulWidget {
  @override
  State<PreloadContent> createState() => _PreloadContentState();
}

class _PreloadContentState extends State<PreloadContent> {
  int _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        // 预加载所有页面
        for (int i = 0; i < 3; i++)
          Offstage(
            offstage: _currentIndex != i,
            child: _PreloadedPage(index: i),
          ),
        // 底部导航
        Positioned(
          bottom: 0,
          left: 0,
          right: 0,
          child: Row(
            children: List.generate(3, (index) {
              return Expanded(
                child: TextButton(
                  onPressed: () => setState(() => _currentIndex = index),
                  child: Icon(Icons.looks_${index + 1}),
                ),
              );
            }),
          ),
        ),
      ],
    );
  }
}

class _PreloadedPage extends StatefulWidget {
  final int index;
  const _PreloadedPage({required this.index});

  @override
  State<_PreloadedPage> createState() => _PreloadedPageState();
}

class _PreloadedPageState extends State<_PreloadedPage>
    with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);
    return Container(
      color: Colors.primaries[widget.index],
      child: Center(child: Text('Page ${widget.index}')),
    );
  }
}
```

### 3.3 下拉菜单

```dart
class DropdownMenu extends StatefulWidget {
  @override
  State<DropdownMenu> createState() => _DropdownMenuState();
}

class _DropdownMenuState extends State<DropdownMenu> {
  bool _isOpen = false;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        GestureDetector(
          onTap: () => setState(() => _isOpen = !_isOpen),
          child: Container(
            padding: EdgeInsets.symmetric(horizontal: 16, vertical: 12),
            decoration: BoxDecoration(
              border: Border.all(color: Colors.grey),
              borderRadius: BorderRadius.circular(4),
            ),
            child: Row(
              children: [
                Text('选择选项'),
                Spacer(),
                Icon(_isOpen ? Icons.arrow_drop_up : Icons.arrow_drop_down),
              ],
            ),
          ),
        ),
        Offstage(
          offstage: !_isOpen,
          child: Container(
            decoration: BoxDecoration(
              border: Border.all(color: Colors.grey),
              borderRadius: BorderRadius.circular(4),
            ),
            child: Column(
              children: [
                ListTile(title: Text('选项 1'), onTap: () {}),
                ListTile(title: Text('选项 2'), onTap: () {}),
                ListTile(title: Text('选项 3'), onTap: () {}),
              ],
            ),
          ),
        ),
      ],
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：Offstage vs Visibility

```dart
// Offstage：子组件保留在树中，状态保持
Offstage(
  offstage: true,
  child: StatefulWidget(),  // 状态保持
)

// Visibility：可配置是否保持状态
Visibility(
  visible: false,
  maintainState: true,  // 保持状态
  child: StatefulWidget(),
)
```

### 问题2：布局空间

```dart
// Offstage 隐藏时不占用空间
Column(
  children: [
    Text('项目 1'),
    Offstage(
      offstage: true,
      child: Text('隐藏的项目'),  // 不占用空间
    ),
    Text('项目 2'),
  ],
)
```

### 问题3：性能考虑

```dart
// 对于复杂组件，隐藏状态仍会占用内存
// 如果需要完全移除，使用条件渲染
_showContent ? ComplexWidget() : SizedBox()
```

## 5. Offstage vs 其他隐藏方式

| 方式 | 状态保持 | 布局空间 | 性能 |
|------|----------|----------|------|
| **Offstage** | 是 | 不占用 | 中 |
| **Visibility** | 可配置 | 可配置 | 中 |
| **条件渲染** | 否 | 不占用 | 优 |
| **Opacity(0)** | 是 | 占用 | 差 |

### 选择建议

```dart
// 需要保持状态 → Offstage 或 Visibility(maintainState: true)
Offstage(offstage: !show, child: StatefulWidget())

// 简单切换，不需要状态 → 条件渲染
show ? Widget() : SizedBox()

// 需要动画效果 → Visibility 或 AnimatedOpacity
Visibility(visible: show, child: Widget())
```

## 6. 最佳实践

### 6.1 标签页切换

```dart
// 使用 Stack + Offstage 实现标签页
Stack(
  children: tabs.asMap().entries.map((entry) {
    return Offstage(
      offstage: currentIndex != entry.key,
      child: entry.value,
    );
  }).toList(),
)
```

### 6.2 避免重建

```dart
// 使用 const 构造函数优化性能
Offstage(
  offstage: !isVisible,
  child: const MyStaticWidget(),
)
```

### 6.3 配合 AutomaticKeepAliveClientMixin

```dart
// 保持页面状态
class MyPage extends StatefulWidget {
  @override
  State<MyPage> createState() => _MyPageState();
}

class _MyPageState extends State<MyPage>
    with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);
    return ListView(...);
  }
}
```

## 总结

Offstage 是控制组件显示隐藏的核心工具：

**核心要点**：
1. offstage=true 时隐藏，false 时显示
2. 隐藏时子组件仍在树中，状态保持
3. 隐藏时不占用布局空间

**最佳实践**：
1. 需要保持状态时使用 Offstage
2. 标签页切换场景适用
3. 复杂组件考虑性能影响

**常见场景**：
- 标签页切换
- 表单步骤
- 下拉菜单
- 条件显示

---

*最后更新: 2026-02-19*

# Flutter Stack 组件用法详解

## 简介

Stack 是 Flutter 中的堆叠布局组件，用于将子组件按顺序堆叠在一起。后添加的子组件会覆盖在先添加的子组件之上，非常适合实现叠加效果、绝对定位布局等场景。

## 1. 初级用法

### 1.1 基本概念

Stack 允许子组件以堆叠的方式排列，主要特点：
- 子组件按顺序从下到上堆叠
- 支持绝对定位（通过 Positioned 组件）
- 可以设置对齐方式
- 常用于图片上添加文字、按钮叠加等场景

### 1.2 基本语法

```dart
Stack(
  children: [
    Container(color: Colors.blue),      // 底层
    Container(color: Colors.red),       // 中层
    Container(color: Colors.green),     // 顶层
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `alignment` | `AlignmentGeometry` | `AlignmentDirectional.topStart` | 子组件对齐方式 |
| `textDirection` | `TextDirection?` | - | 文本方向 |
| `fit` | `StackFit` | `StackFit.loose` | 子组件尺寸调整方式 |
| `clipBehavior` | `Clip` | `Clip.hardEdge` | 裁剪行为 |
| `children` | `List<Widget>` | - | 子组件列表 |

### 1.4 基础示例

#### 简单堆叠

```dart
Stack(
  children: [
    Container(
      width: 200,
      height: 200,
      color: Colors.blue,
    ),
    Container(
      width: 150,
      height: 150,
      color: Colors.red,
    ),
    Container(
      width: 100,
      height: 100,
      color: Colors.green,
    ),
  ],
)
```

#### 图片上添加文字

```dart
Stack(
  children: [
    Image.network(
      'https://example.com/image.jpg',
      width: double.infinity,
      height: 200,
      fit: BoxFit.cover,
    ),
    Positioned(
      bottom: 16,
      left: 16,
      child: Text(
        '图片标题',
        style: TextStyle(
          color: Colors.white,
          fontSize: 20,
          fontWeight: FontWeight.bold,
          shadows: [
            Shadow(color: Colors.black, blurRadius: 4),
          ],
        ),
      ),
    ),
  ],
)
```

#### 头像角标

```dart
Stack(
  children: [
    CircleAvatar(
      radius: 30,
      backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
    ),
    Positioned(
      right: 0,
      bottom: 0,
      child: Container(
        width: 16,
        height: 16,
        decoration: BoxDecoration(
          color: Colors.green,
          shape: BoxShape.circle,
          border: Border.all(color: Colors.white, width: 2),
        ),
      ),
    ),
  ],
)
```

## 2. 中级用法

### 2.1 alignment 对齐方式

```dart
Stack(
  alignment: Alignment.center,  // 子组件居中对齐
  children: [
    Container(width: 200, height: 200, color: Colors.blue),
    Container(width: 100, height: 100, color: Colors.red),
    Text('居中文字'),
  ],
)

// 其他对齐方式
Stack(
  alignment: Alignment.bottomRight,  // 右下角
  children: [...],
)

Stack(
  alignment: Alignment.topLeft,  // 左上角
  children: [...],
)

Stack(
  alignment: Alignment(0.5, 0.5),  // 自定义位置 (-1 到 1)
  children: [...],
)
```

### 2.2 Positioned 绝对定位

Positioned 用于在 Stack 中精确定位子组件：

```dart
Stack(
  children: [
    Container(color: Colors.grey[200]),
    // 固定位置
    Positioned(
      left: 20,
      top: 20,
      child: Container(width: 100, height: 100, color: Colors.red),
    ),
    // 右侧固定
    Positioned(
      right: 20,
      top: 20,
      child: Container(width: 100, height: 100, color: Colors.green),
    ),
    // 填满底部
    Positioned(
      left: 0,
      right: 0,
      bottom: 0,
      height: 80,
      child: Container(color: Colors.blue),
    ),
  ],
)
```

#### Positioned 常用属性

```dart
Positioned(
  left: 10,      // 距左边距离
  right: 10,     // 距右边距离
  top: 10,       // 距顶部距离
  bottom: 10,    // 距底部距离
  width: 100,    // 固定宽度
  height: 100,   // 固定高度
  child: Container(color: Colors.red),
)

// Positioned.fill 填满整个 Stack
Positioned.fill(
  child: Container(color: Colors.red.withOpacity(0.5)),
)
```

### 2.3 fit 属性

控制子组件如何调整尺寸：

```dart
// StackFit.loose - 子组件可以使用自己的尺寸（默认）
Stack(
  fit: StackFit.loose,
  children: [
    Container(width: 100, height: 100, color: Colors.red),
  ],
)

// StackFit.expand - 子组件填满 Stack
Stack(
  fit: StackFit.expand,
  children: [
    Container(color: Colors.red),  // 自动填满
  ],
)

// StackFit.passthrough - Stack 传递父级的约束
Stack(
  fit: StackFit.passthrough,
  children: [...],
)
```

### 2.4 clipBehavior 裁剪行为

```dart
// Clip.none - 不裁剪，子组件可以超出 Stack 边界
Stack(
  clipBehavior: Clip.none,
  children: [
    Container(width: 100, height: 100, color: Colors.blue),
    Positioned(
      left: 80,  // 超出边界
      child: Container(width: 50, height: 50, color: Colors.red),
    ),
  ],
)

// Clip.hardEdge - 裁剪超出部分（默认）
Stack(
  clipBehavior: Clip.hardEdge,
  children: [...],
)

// Clip.antiAlias - 抗锯齿裁剪
Stack(
  clipBehavior: Clip.antiAlias,
  children: [...],
)
```

### 2.5 常见布局场景

#### 卡片角标

```dart
Stack(
  clipBehavior: Clip.none,
  children: [
    Container(
      width: 200,
      height: 120,
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(8),
        boxShadow: [
          BoxShadow(color: Colors.black.withOpacity(0.1), blurRadius: 8),
        ],
      ),
      child: Center(child: Text('卡片内容')),
    ),
    Positioned(
      top: -10,
      right: -10,
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 12, vertical: 4),
        decoration: BoxDecoration(
          color: Colors.red,
          borderRadius: BorderRadius.circular(12),
        ),
        child: Text(
          'NEW',
          style: TextStyle(color: Colors.white, fontSize: 12),
        ),
      ),
    ),
  ],
)
```

#### 图片遮罩

```dart
Stack(
  children: [
    Image.asset(
      'assets/image.jpg',
      width: double.infinity,
      height: 200,
      fit: BoxFit.cover,
    ),
    Positioned.fill(
      child: Container(
        decoration: BoxDecoration(
          gradient: LinearGradient(
            begin: Alignment.topCenter,
            end: Alignment.bottomCenter,
            colors: [
              Colors.transparent,
              Colors.black.withOpacity(0.7),
            ],
          ),
        ),
      ),
    ),
    Positioned(
      bottom: 16,
      left: 16,
      right: 16,
      child: Text(
        '底部标题',
        style: TextStyle(color: Colors.white, fontSize: 18),
      ),
    ),
  ],
)
```

#### 浮动按钮

```dart
Stack(
  children: [
    ListView(
      children: [
        // 列表内容...
        Container(height: 100, color: Colors.blue[100]),
        Container(height: 100, color: Colors.green[100]),
        Container(height: 100, color: Colors.yellow[100]),
      ],
    ),
    Positioned(
      right: 16,
      bottom: 16,
      child: FloatingActionButton(
        onPressed: () {},
        child: Icon(Icons.add),
      ),
    ),
  ],
)
```

## 3. 高级用法

### 3.1 IndexedStack 索引堆叠

IndexedStack 只显示指定索引的子组件，常用于 Tab 切换：

```dart
class IndexedStackExample extends StatefulWidget {
  @override
  _IndexedStackExampleState createState() => _IndexedStackExampleState();
}

class _IndexedStackExampleState extends State<IndexedStackExample> {
  int _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(
        index: _currentIndex,
        children: [
          Container(color: Colors.red, child: Center(child: Text('页面1'))),
          Container(color: Colors.green, child: Center(child: Text('页面2'))),
          Container(color: Colors.blue, child: Center(child: Text('页面3'))),
        ],
      ),
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (index) => setState(() => _currentIndex = index),
        items: [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: '首页'),
          BottomNavigationBarItem(icon: Icon(Icons.search), label: '搜索'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: '我的'),
        ],
      ),
    );
  }
}
```

**IndexedStack 特点**：
- 只显示当前索引的子组件
- 其他子组件保持状态但不显示
- 适合需要保持页面状态的 Tab 切换

### 3.2 动态定位

```dart
class DynamicPositioned extends StatefulWidget {
  @override
  _DynamicPositionedState createState() => _DynamicPositionedState();
}

class _DynamicPositionedState extends State<DynamicPositioned> {
  double _x = 100;
  double _y = 100;

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        GestureDetector(
          onPanUpdate: (details) {
            setState(() {
              _x += details.delta.dx;
              _y += details.delta.dy;
            });
          },
          child: Container(color: Colors.grey[200]),
        ),
        Positioned(
          left: _x,
          top: _y,
          child: Container(
            width: 80,
            height: 80,
            color: Colors.blue,
            child: Center(child: Text('可拖动')),
          ),
        ),
      ],
    );
  }
}
```

### 3.3 层叠动画

```dart
class StackedAnimation extends StatefulWidget {
  @override
  _StackedAnimationState createState() => _StackedAnimationState();
}

class _StackedAnimationState extends State<StackedAnimation>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 2),
      vsync: this,
    )..repeat(reverse: true);
    _animation = Tween<double>(begin: 0, end: 100).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeInOut),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Stack(
      alignment: Alignment.center,
      children: [
        Container(width: 200, height: 200, color: Colors.blue),
        AnimatedBuilder(
          animation: _animation,
          builder: (context, child) {
            return Positioned(
              top: _animation.value,
              child: Container(
                width: 50,
                height: 50,
                color: Colors.red,
              ),
            );
          },
        ),
      ],
    );
  }
}
```

### 3.4 组合布局

```dart
// Stack + Row/Column 组合
Stack(
  children: [
    // 底层背景
    Container(
      decoration: BoxDecoration(
        gradient: LinearGradient(
          colors: [Colors.blue, Colors.purple],
          begin: Alignment.topLeft,
          end: Alignment.bottomRight,
        ),
      ),
    ),
    // 内容层
    Column(
      children: [
        AppBar(
          backgroundColor: Colors.transparent,
          elevation: 0,
          title: Text('透明 AppBar'),
        ),
        Expanded(
          child: Center(child: Text('内容区域')),
        ),
      ],
    ),
    // 浮动元素
    Positioned(
      right: 16,
      bottom: 80,
      child: Column(
        children: [
          FloatingActionButton(
            heroTag: 'btn1',
            onPressed: () {},
            child: Icon(Icons.edit),
          ),
          SizedBox(height: 16),
          FloatingActionButton(
            heroTag: 'btn2',
            onPressed: () {},
            child: Icon(Icons.delete),
          ),
        ],
      ),
    ),
  ],
)
```

### 3.5 水印效果

```dart
class WatermarkStack extends StatelessWidget {
  final Widget child;
  final String watermark;

  const WatermarkStack({
    required this.child,
    this.watermark = 'WATERMARK',
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        child,
        Positioned.fill(
          child: IgnorePointer(
            child: Center(
              child: Transform.rotate(
                angle: -0.5,
                child: Text(
                  watermark,
                  style: TextStyle(
                    fontSize: 48,
                    color: Colors.black.withOpacity(0.05),
                    fontWeight: FontWeight.bold,
                  ),
                ),
              ),
            ),
          ),
        ),
      ],
    );
  }
}

// 使用示例
WatermarkStack(
  watermark: 'CONFIDENTIAL',
  child: Image.asset('assets/document.png'),
)
```

## 4. 性能优化

### 4.1 避免过度使用 Stack

```dart
// ❌ 不推荐：多层嵌套 Stack
Stack(
  children: [
    Stack(
      children: [
        Stack(
          children: [
            Container(),
            Text('Content'),
          ],
        ),
      ],
    ),
  ],
)

// ✅ 推荐：简化为单层 Stack
Stack(
  children: [
    Container(),
    Text('Content'),
  ],
)
```

### 4.2 使用 const 构造函数

```dart
Stack(
  children: const [
    Text('静态内容1'),
    Text('静态内容2'),
  ],
)
```

### 4.3 合理使用 clipBehavior

```dart
// 不需要裁剪时使用 Clip.none，避免不必要的裁剪计算
Stack(
  clipBehavior: Clip.none,
  children: [...],
)
```

### 4.4 IndexedStack 状态保持

```dart
// IndexedStack 会保持所有子组件的状态
// 如果子组件较多或较复杂，注意内存占用
IndexedStack(
  index: _currentIndex,
  children: [
    HeavyPage1(),  // 即使不显示也会保持状态
    HeavyPage2(),
    HeavyPage3(),
  ],
)

// 替代方案：使用条件渲染
if (_currentIndex == 0)
  HeavyPage1()
else if (_currentIndex == 1)
  HeavyPage2()
else
  HeavyPage3()
```

## 5. 常见问题与解决方案

### 问题1：Stack 子组件尺寸为 0

**原因**：Stack 默认不会给子组件传递约束

```dart
// ❌ 问题：Container 没有尺寸
Stack(
  children: [
    Container(color: Colors.red),  // 尺寸为 0
  ],
)

// ✅ 解决方案1：设置 fit
Stack(
  fit: StackFit.expand,
  children: [
    Container(color: Colors.red),  // 填满父容器
  ],
)

// ✅ 解决方案2：给子组件设置尺寸
Stack(
  children: [
    Container(
      width: double.infinity,
      height: double.infinity,
      color: Colors.red,
    ),
  ],
)
```

### 问题2：Positioned 子组件溢出

```dart
// 问题：子组件超出 Stack 边界被裁剪
Stack(
  clipBehavior: Clip.hardEdge,  // 默认裁剪
  children: [
    Container(width: 100, height: 100, color: Colors.blue),
    Positioned(
      left: 80,
      child: Container(width: 50, height: 50, color: Colors.red),  // 被裁剪
    ),
  ],
)

// 解决方案：使用 Clip.none
Stack(
  clipBehavior: Clip.none,
  children: [...],
)
```

### 问题3：Stack 与 Row/Column 混用

```dart
// ❌ 问题：Stack 在 Column 中高度无限
Column(
  children: [
    Stack(
      children: [...],  // 高度无限
    ),
  ],
)

// ✅ 解决方案1：设置尺寸
Column(
  children: [
    SizedBox(
      height: 200,
      child: Stack(
        children: [...],
      ),
    ),
  ],
)

// ✅ 解决方案2：使用 Expanded
Column(
  children: [
    Expanded(
      child: Stack(
        children: [...],
      ),
    ),
  ],
)
```

### 问题4：层级遮挡问题

```dart
// 问题：按钮被遮挡无法点击
Stack(
  children: [
    Container(color: Colors.blue),
    Text('文字'),  // 被 Container 遮挡
  ],
)

// 解决方案1：调整顺序
Stack(
  children: [
    Container(color: Colors.blue),
    Text('文字'),  // 现在在最上层
  ],
)

// 解决方案2：使用 IgnorePointer 或 AbsorbPointer
Stack(
  children: [
    IgnorePointer(
      child: Container(color: Colors.blue),  // 不响应点击
    ),
    Text('文字'),
  ],
)
```

## 6. Stack vs 其他组件对比

| 组件 | 特点 | 适用场景 |
|------|------|---------|
| **Stack** | 子组件堆叠，支持定位 | 叠加效果、绝对定位 |
| **IndexedStack** | 只显示一个子组件 | Tab 切换、页面切换 |
| **Row/Column** | 线性排列 | 常规布局 |
| **Wrap** | 自动换行 | 标签、流式布局 |
| **Overlay** | 全局悬浮层 | Toast、弹窗 |

## 总结

Stack 是 Flutter 中实现复杂叠加布局的核心组件：

**核心要点**：
1. 子组件按顺序堆叠，后者覆盖前者
2. 使用 Positioned 进行精确定位
3. alignment 控制非定位子组件的对齐方式
4. clipBehavior 控制裁剪行为

**最佳实践**：
1. 避免过度嵌套
2. 合理设置 clipBehavior
3. 使用 IndexedStack 保持页面状态
4. 配合 IgnorePointer 处理点击穿透

**常见场景**：
- 图片上的文字/图标
- 卡片角标/徽章
- 浮动按钮
- 遮罩/水印效果
- Tab 页面切换

---

*最后更新: 2026-02-18*

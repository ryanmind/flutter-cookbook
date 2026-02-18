# Flutter Expanded 组件用法详解

## 简介

Expanded 是 Flutter 中用于填充剩余空间的布局组件。它必须作为 Row、Column 或 Flex 的子组件使用，通过 flex 参数可以按比例分配剩余空间。

## 1. 初级用法

### 1.1 基本概念

Expanded 的核心作用：
- 让子组件填充父组件的剩余空间
- 通过 flex 参数按比例分配空间
- 只能作为 Row、Column、Flex 的直接子组件

### 1.2 基本语法

```dart
Row(
  children: [
    Container(width: 100, color: Colors.red),
    Expanded(
      child: Container(color: Colors.blue),  // 填充剩余空间
    ),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `flex` | `int` | `1` | 弹性比例 |
| `child` | `Widget` | - | 子组件 |

### 1.4 基础示例

#### 填充剩余空间

```dart
Row(
  children: [
    Container(width: 80, height: 50, color: Colors.red),
    Expanded(
      child: Container(
        height: 50,
        color: Colors.blue,
        child: Center(child: Text('填充剩余空间')),
      ),
    ),
  ],
)
```

#### 三等分布局

```dart
Row(
  children: [
    Expanded(
      child: Container(height: 50, color: Colors.red),
    ),
    Expanded(
      child: Container(height: 50, color: Colors.green),
    ),
    Expanded(
      child: Container(height: 50, color: Colors.blue),
    ),
  ],
)
```

#### 按比例分配

```dart
Row(
  children: [
    Expanded(
      flex: 1,  // 占 1 份
      child: Container(height: 50, color: Colors.red),
    ),
    Expanded(
      flex: 2,  // 占 2 份
      child: Container(height: 50, color: Colors.green),
    ),
    Expanded(
      flex: 1,  // 占 1 份
      child: Container(height: 50, color: Colors.blue),
    ),
  ],
)
```

## 2. 中级用法

### 2.1 在 Column 中使用

```dart
Column(
  children: [
    Container(height: 80, color: Colors.red),
    Expanded(
      child: Container(
        color: Colors.blue,
        child: Center(child: Text('填充剩余高度')),
      ),
    ),
    Container(height: 60, color: Colors.green),
  ],
)
```

### 2.2 固定宽度按钮 + 填充内容

```dart
Row(
  children: [
    Expanded(
      child: TextField(
        decoration: InputDecoration(
          hintText: '请输入内容',
          border: OutlineInputBorder(),
        ),
      ),
    ),
    SizedBox(width: 16),
    SizedBox(
      width: 80,  // 固定宽度按钮
      child: ElevatedButton(
        onPressed: () {},
        child: Text('发送'),
      ),
    ),
  ],
)
```

### 2.3 底部固定 + 内容滚动

```dart
Column(
  children: [
    Expanded(
      child: ListView.builder(
        itemCount: 20,
        itemBuilder: (context, index) {
          return ListTile(title: Text('项目 $index'));
        },
      ),
    ),
    Container(
      padding: EdgeInsets.all(16),
      color: Colors.grey[200],
      child: Row(
        children: [
          Expanded(
            child: TextField(
              decoration: InputDecoration(
                hintText: '输入消息',
                filled: true,
                fillColor: Colors.white,
              ),
            ),
          ),
          SizedBox(width: 8),
          IconButton(
            icon: Icon(Icons.send),
            onPressed: () {},
          ),
        ],
      ),
    ),
  ],
)
```

### 2.4 经典三栏布局

```dart
Row(
  children: [
    // 左侧边栏
    Container(
      width: 200,
      color: Colors.blue[100],
      child: Center(child: Text('侧边栏')),
    ),
    // 中间内容区
    Expanded(
      child: Container(
        color: Colors.white,
        child: Center(child: Text('内容区')),
      ),
    ),
    // 右侧面板
    Container(
      width: 150,
      color: Colors.green[100],
      child: Center(child: Text('右侧面板')),
    ),
  ],
)
```

### 2.5 卡片内容布局

```dart
Card(
  child: Padding(
    padding: EdgeInsets.all(16),
    child: Row(
      children: [
        CircleAvatar(
          child: Icon(Icons.person),
        ),
        SizedBox(width: 16),
        Expanded(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              Text('标题', style: TextStyle(fontWeight: FontWeight.bold)),
              SizedBox(height: 4),
              Text('描述内容', style: TextStyle(color: Colors.grey)),
            ],
          ),
        ),
        IconButton(
          icon: Icon(Icons.more_vert),
          onPressed: () {},
        ),
      ],
    ),
  ),
)
```

## 3. 高级用法

### 3.1 复杂比例布局

```dart
Column(
  children: [
    Expanded(
      flex: 2,  // 顶部占 2 份
      child: Container(
        color: Colors.blue,
        child: Center(child: Text('Header (flex: 2)')),
      ),
    ),
    Expanded(
      flex: 5,  // 内容区占 5 份
      child: Container(
        color: Colors.white,
        child: ListView.builder(
          itemCount: 20,
          itemBuilder: (context, index) => ListTile(title: Text('项目 $index')),
        ),
      ),
    ),
    Expanded(
      flex: 1,  // 底部占 1 份
      child: Container(
        color: Colors.grey[200],
        child: Center(child: Text('Footer (flex: 1)')),
      ),
    ),
  ],
)
```

### 3.2 响应式布局

```dart
class ResponsiveLayout extends StatelessWidget {
  final Widget mobileBody;
  final Widget tabletBody;

  const ResponsiveLayout({
    required this.mobileBody,
    required this.tabletBody,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth > 600) {
          // 平板布局
          return Row(
            children: [
              Container(
                width: 250,
                color: Colors.grey[200],
                child: Center(child: Text('侧边栏')),
              ),
              Expanded(child: tabletBody),
            ],
          );
        } else {
          // 手机布局
          return mobileBody;
        }
      },
    );
  }
}
```

### 3.3 动态 flex 值

```dart
class DynamicFlex extends StatefulWidget {
  @override
  _DynamicFlexState createState() => _DynamicFlexState();
}

class _DynamicFlexState extends State<DynamicFlex> {
  int _flex1 = 1;
  int _flex2 = 1;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Expanded(
          flex: _flex1,
          child: GestureDetector(
            onTap: () => setState(() => _flex1 += 1),
            child: Container(
              color: Colors.red,
              child: Center(child: Text('点击增加 (flex: $_flex1)')),
            ),
          ),
        ),
        Expanded(
          flex: _flex2,
          child: GestureDetector(
            onTap: () => setState(() => _flex2 += 1),
            child: Container(
              color: Colors.blue,
              child: Center(child: Text('点击增加 (flex: $_flex2)')),
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.4 嵌套 Expanded

```dart
Row(
  children: [
    Expanded(
      flex: 2,
      child: Container(
        color: Colors.red[100],
        child: Column(
          children: [
            Expanded(
              flex: 3,
              child: Container(color: Colors.red[300]),
            ),
            Expanded(
              flex: 1,
              child: Container(color: Colors.red[500]),
            ),
          ],
        ),
      ),
    ),
    Expanded(
      flex: 3,
      child: Container(
        color: Colors.blue[100],
        child: Row(
          children: [
            Expanded(
              flex: 1,
              child: Container(color: Colors.blue[300]),
            ),
            Expanded(
              flex: 2,
              child: Container(color: Colors.blue[500]),
            ),
          ],
        ),
      ),
    ),
  ],
)
```

### 3.5 与 SingleChildScrollView 配合

```dart
SingleChildScrollView(
  child: Column(
    children: [
      Container(
        height: 100,
        color: Colors.red,
        child: Center(child: Text('固定高度区域')),
      ),
      // 注意：Expanded 不能直接在 SingleChildScrollView 中使用
      // 需要在外层使用 ConstrainedBox 或指定高度
      ConstrainedBox(
        constraints: BoxConstraints(
          minHeight: 300,
        ),
        child: Container(
          color: Colors.blue[100],
          child: Center(child: Text('最小高度 300')),
        ),
      ),
    ],
  ),
)
```

## 4. Expanded vs Flexible

两者区别：

| 特性 | Expanded | Flexible |
|------|----------|----------|
| 空间分配 | 强制填满分配的空间 | 可以小于分配的空间 |
| 子组件约束 | 严格约束 | 宽松约束 |
| 适用场景 | 需要填满空间 | 子组件需要保持原始大小 |

```dart
// Expanded - 强制填满
Row(
  children: [
    Expanded(
      child: Container(
        color: Colors.red,
        // 即使内容很小，也会填满空间
        child: Text('Expanded'),
      ),
    ),
  ],
)

// Flexible - 可以不填满
Row(
  children: [
    Flexible(
      child: Container(
        color: Colors.blue,
        // 只占用文字需要的空间
        child: Text('Flexible'),
      ),
    ),
  ],
)

// 对比示例
Row(
  children: [
    Expanded(
      child: Container(
        height: 50,
        color: Colors.red,
        child: Text('Expanded'),
      ),
    ),
    Flexible(
      child: Container(
        height: 50,
        color: Colors.blue,
        child: Text('Flexible'),
      ),
    ),
  ],
)
```

## 5. 常见问题与解决方案

### 问题1：Expanded 使用在错误的位置

```dart
// ❌ 错误：Expanded 不能直接作为 Container 的子组件
Container(
  child: Expanded(
    child: Text('错误'),
  ),
)

// ✅ 正确：Expanded 必须作为 Row/Column/Flex 的直接子组件
Column(
  children: [
    Expanded(
      child: Text('正确'),
    ),
  ],
)
```

### 问题2：Expanded 在无界约束中

```dart
// ❌ 错误：ListView 已经是无界约束
ListView(
  children: [
    Expanded(  // 错误！
      child: Container(),
    ),
  ],
)

// ✅ 解决方案1：使用 shrinkWrap 的 ListView
ListView(
  shrinkWrap: true,
  children: [
    Container(height: 100, color: Colors.red),
  ],
)

// ✅ 解决方案2：在外层使用 Expanded
Expanded(
  child: ListView(
    children: [
      Container(height: 100, color: Colors.red),
    ],
  ),
)
```

### 问题3：多个 Expanded 比例不正确

```dart
// 注意：flex 比例是相对于其他 Expanded 的
Row(
  children: [
    Container(width: 100, color: Colors.grey),  // 固定宽度
    Expanded(
      flex: 1,
      child: Container(color: Colors.red),
    ),
    Expanded(
      flex: 1,
      child: Container(color: Colors.blue),
    ),
    // 两个 Expanded 平分剩余空间（100px 被固定宽度占用）
  ],
)
```

### 问题4：子组件尺寸被忽略

```dart
// Expanded 会忽略子组件的尺寸约束
Expanded(
  child: Container(
    width: 50,  // 这个宽度会被忽略
    color: Colors.red,
  ),
)

// 如果需要限制最小/最大尺寸，使用 ConstrainedBox
Expanded(
  child: ConstrainedBox(
    constraints: BoxConstraints(
      maxWidth: 200,
    ),
    child: Container(color: Colors.red),
  ),
)
```

## 6. 最佳实践

### 6.1 合理使用 flex

```dart
// ✅ 使用有意义的 flex 值
Column(
  children: [
    Expanded(
      flex: 1,  // Header
      child: Container(color: Colors.blue),
    ),
    Expanded(
      flex: 4,  // Content (主要内容，占更大比例)
      child: Container(color: Colors.white),
    ),
    Expanded(
      flex: 1,  // Footer
      child: Container(color: Colors.grey),
    ),
  ],
)
```

### 6.2 配合 SizedBox 使用

```dart
Row(
  children: [
    SizedBox(width: 200, child: LeftPanel()),
    Expanded(child: CenterPanel()),
    SizedBox(width: 200, child: RightPanel()),
  ],
)
```

### 6.3 避免 Expanded 嵌套过深

```dart
// ❌ 不推荐：嵌套过深
Expanded(
  child: Expanded(
    child: Expanded(
      child: Container(),
    ),
  ),
)

// ✅ 推荐：单层 Expanded
Expanded(
  child: Container(),
)
```

## 总结

Expanded 是 Flutter 中实现弹性布局的核心组件：

**核心要点**：
1. 只能作为 Row、Column、Flex 的子组件
2. 通过 flex 参数按比例分配剩余空间
3. 强制子组件填满分配的空间

**与 Flexible 区别**：
- Expanded：强制填满空间
- Flexible：可以保持子组件原始大小

**常见场景**：
- 填充剩余空间
- 按比例分配布局
- 固定宽度 + 弹性内容
- 经典页面布局（Header + Content + Footer）

---

*最后更新: 2026-02-18*

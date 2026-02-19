# Flutter Flexible 组件用法详解

## 简介

Flexible 是 Flutter 中用于创建弹性布局的组件。与 Expanded 类似，它必须作为 Row、Column 或 Flex 的子组件使用，但 Flexible 允许子组件根据自身内容决定大小，不必强制填满分配的空间。

## 1. 初级用法

### 1.1 基本概念

Flexible 的核心特点：
- 为子组件分配弹性空间
- 子组件可以小于分配的空间
- 通过 flex 参数按比例分配
- 通过 fit 参数控制填充行为

### 1.2 基本语法

```dart
Row(
  children: [
    Container(width: 100, color: Colors.red),
    Flexible(
      child: Container(color: Colors.blue),  // 可弹性调整
    ),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `flex` | `int` | `1` | 弹性比例 |
| `fit` | `FlexFit` | `FlexFit.loose` | 填充方式 |
| `child` | `Widget` | - | 子组件 |

### 1.4 FlexFit 枚举

| 值 | 说明 |
|------|------|
| `FlexFit.tight` | 强制填满分配的空间（等同于 Expanded） |
| `FlexFit.loose` | 允许子组件小于分配的空间（默认） |

### 1.5 基础示例

#### 宽松约束（默认）

```dart
Row(
  children: [
    Flexible(
      child: Container(
        color: Colors.blue,
        child: Text('Flexible'),  // 只占用文字所需空间
      ),
    ),
    Container(width: 100, color: Colors.red),
  ],
)
```

#### 紧凑约束

```dart
Row(
  children: [
    Flexible(
      fit: FlexFit.tight,  // 强制填满，等同于 Expanded
      child: Container(color: Colors.blue),
    ),
    Container(width: 100, color: Colors.red),
  ],
)
```

## 2. 中级用法

### 2.1 Flexible vs Expanded 对比

```dart
Column(
  children: [
    // Expanded 强制填满
    Row(
      children: [
        Expanded(
          child: Container(
            height: 50,
            color: Colors.red,
            child: Text('Expanded'),
          ),
        ),
      ],
    ),
    SizedBox(height: 8),
    // Flexible 可以不填满
    Row(
      children: [
        Flexible(
          child: Container(
            height: 50,
            color: Colors.blue,
            child: Text('Flexible'),
          ),
        ),
      ],
    ),
    SizedBox(height: 8),
    // Flexible + FlexFit.tight 等同于 Expanded
    Row(
      children: [
        Flexible(
          fit: FlexFit.tight,
          child: Container(
            height: 50,
            color: Colors.green,
            child: Text('Flexible.tight'),
          ),
        ),
      ],
    ),
  ],
)
```

### 2.2 按比例分配

```dart
Row(
  children: [
    Flexible(
      flex: 1,
      child: Container(height: 50, color: Colors.red),
    ),
    Flexible(
      flex: 2,
      child: Container(height: 50, color: Colors.green),
    ),
    Flexible(
      flex: 1,
      child: Container(height: 50, color: Colors.blue),
    ),
  ],
)
```

### 2.3 混合布局

```dart
Row(
  children: [
    Container(width: 80, height: 50, color: Colors.grey),  // 固定宽度
    Flexible(
      flex: 2,
      child: Container(height: 50, color: Colors.red),
    ),
    Flexible(
      flex: 1,
      child: Container(height: 50, color: Colors.blue),
    ),
    Container(width: 60, height: 50, color: Colors.grey),  // 固定宽度
  ],
)
```

### 2.4 图片自适应

```dart
Row(
  children: [
    Flexible(
      child: Image.network(
        'https://example.com/image.jpg',
        fit: BoxFit.cover,
      ),
    ),
    Flexible(
      child: Image.network(
        'https://example.com/image2.jpg',
        fit: BoxFit.cover,
      ),
    ),
  ],
)
```

### 2.5 文本截断处理

```dart
Row(
  children: [
    Flexible(
      child: Text(
        '这是一段很长的文本内容，使用 Flexible 可以确保文本不会被截断',
        overflow: TextOverflow.ellipsis,
        maxLines: 1,
      ),
    ),
    SizedBox(width: 8),
    Icon(Icons.arrow_forward),
  ],
)
```

## 3. 高级用法

### 3.1 动态 flex 值

```dart
class DynamicFlexExample extends StatefulWidget {
  @override
  _DynamicFlexExampleState createState() => _DynamicFlexExampleState();
}

class _DynamicFlexExampleState extends State<DynamicFlexExample> {
  int _leftFlex = 1;
  int _rightFlex = 1;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Expanded(
          child: Row(
            children: [
              Flexible(
                flex: _leftFlex,
                child: GestureDetector(
                  onTap: () => setState(() => _leftFlex++),
                  child: Container(
                    color: Colors.red[100],
                    child: Center(
                      child: Text('左侧 (flex: $_leftFlex)\n点击增加'),
                    ),
                  ),
                ),
              ),
              Flexible(
                flex: _rightFlex,
                child: GestureDetector(
                  onTap: () => setState(() => _rightFlex++),
                  child: Container(
                    color: Colors.blue[100],
                    child: Center(
                      child: Text('右侧 (flex: $_rightFlex)\n点击增加'),
                    ),
                  ),
                ),
              ),
            ],
          ),
        ),
        ElevatedButton(
          onPressed: () => setState(() {
            _leftFlex = 1;
            _rightFlex = 1;
          }),
          child: Text('重置'),
        ),
      ],
    );
  }
}
```

### 3.2 嵌套 Flexible

```dart
Column(
  children: [
    Flexible(
      flex: 2,
      child: Row(
        children: [
          Flexible(
            child: Container(color: Colors.red[200]),
          ),
          Flexible(
            flex: 2,
            child: Container(color: Colors.red[400]),
          ),
        ],
      ),
    ),
    Flexible(
      flex: 1,
      child: Row(
        children: [
          Flexible(
            flex: 2,
            child: Container(color: Colors.blue[200]),
          ),
          Flexible(
            child: Container(color: Colors.blue[400]),
          ),
        ],
      ),
    ),
  ],
)
```

### 3.3 响应式布局

```dart
class ResponsiveFlexLayout extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final isWide = constraints.maxWidth > 600;

        return Row(
          children: [
            if (isWide)
              Flexible(
                flex: 1,
                child: Container(
                  color: Colors.grey[200],
                  child: Center(child: Text('侧边栏')),
                ),
              ),
            Flexible(
              flex: isWide ? 3 : 1,
              child: Container(
                color: Colors.white,
                child: Center(child: Text('内容区')),
              ),
            ),
          ],
        );
      },
    );
  }
}
```

### 3.4 配合 Spacer 使用

```dart
Row(
  children: [
    Text('左侧'),
    Flexible(
      child: Container(
        height: 30,
        color: Colors.blue[100],
      ),
    ),
    Spacer(),  // 另一种弹性空间分配方式
    Text('右侧'),
  ],
)
```

### 3.5 条件性 Flexible

```dart
class ConditionalFlexible extends StatelessWidget {
  final bool useFlexible;

  const ConditionalFlexible({required this.useFlexible, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        if (useFlexible)
          Flexible(
            child: Container(
              height: 50,
              color: Colors.blue,
              child: Center(child: Text('Flexible')),
            ),
          )
        else
          Container(
            width: 200,
            height: 50,
            color: Colors.red,
            child: Center(child: Text('固定宽度')),
          ),
      ],
    );
  }
}
```

## 4. 性能优化

### 4.1 选择正确的 fit 值

```dart
// 如果子组件需要填满空间，使用 FlexFit.tight
Flexible(
  fit: FlexFit.tight,  // 或直接使用 Expanded
  child: Container(color: Colors.blue),
)

// 如果子组件可以小于分配空间，使用 FlexFit.loose
Flexible(
  fit: FlexFit.loose,  // 默认值
  child: Text('内容'),
)
```

### 4.2 避免不必要的 Flexible

```dart
// ❌ 不必要：子组件已经是固定尺寸
Flexible(
  child: SizedBox(
    width: 100,
    height: 50,
    child: Container(color: Colors.red),
  ),
)

// ✅ 直接使用固定尺寸
SizedBox(
  width: 100,
  height: 50,
  child: Container(color: Colors.red),
)
```

### 4.3 使用 const 构造函数

```dart
Row(
  children: const [
    Flexible(
      child: Text('静态内容'),
    ),
  ],
)
```

## 5. 常见问题与解决方案

### 问题1：Flexible 不生效

```dart
// ❌ 问题：Flexible 不在 Row/Column/Flex 中
Container(
  child: Flexible(
    child: Text('错误'),
  ),
)

// ✅ 正确：Flexible 必须在 Row/Column/Flex 中
Row(
  children: [
    Flexible(
      child: Text('正确'),
    ),
  ],
)
```

### 问题2：子组件超出 Flexible 边界

```dart
// 问题：图片可能超出 Flexible 分配的空间
Flexible(
  child: Image.network('large_image.jpg'),  // 可能溢出
)

// 解决方案：使用 fit 属性或 ClipRect
Flexible(
  child: ClipRect(
    child: Image.network(
      'large_image.jpg',
      fit: BoxFit.cover,
    ),
  ),
)
```

### 问题3：多个 Flexible 比例异常

```dart
// 注意：比例是相对的
Row(
  children: [
    Container(width: 50, color: Colors.grey),  // 固定宽度影响剩余空间
    Flexible(
      flex: 1,
      child: Container(color: Colors.red),
    ),
    Flexible(
      flex: 1,
      child: Container(color: Colors.blue),
    ),
  ],
)
// 两个 Flexible 平分剩余空间
```

### 问题4：文本溢出

```dart
// 问题：长文本可能溢出
Flexible(
  child: Text('这是一段非常非常长的文本内容'),
)

// 解决方案：设置溢出处理
Flexible(
  child: Text(
    '这是一段非常非常长的文本内容',
    overflow: TextOverflow.ellipsis,
    maxLines: 1,
  ),
)
```

## 6. Flexible vs Expanded vs Spacer

| 组件 | 特点 | 适用场景 |
|------|------|---------|
| **Flexible** | 弹性空间，子组件可小于分配空间 | 子组件需要保持原始大小 |
| **Expanded** | 强制填满分配空间 | 需要填满剩余空间 |
| **Spacer** | 创建空白弹性空间 | 简单的间距控制 |

```dart
// 示例对比
Row(
  children: [
    Text('开始'),
    
    // Flexible - 子组件可以不填满
    Flexible(
      child: Container(
        color: Colors.blue[100],
        child: Text('Flexible'),
      ),
    ),
    
    // Expanded - 强制填满
    Expanded(
      child: Container(
        color: Colors.green[100],
        child: Text('Expanded'),
      ),
    ),
    
    // Spacer - 纯空白
    Spacer(flex: 1),
    
    Text('结束'),
  ],
)
```

## 7. 最佳实践

### 7.1 合理选择 Flexible 和 Expanded

```dart
// 需要填满空间 → Expanded
Row(
  children: [
    Expanded(
      child: Container(color: Colors.blue),
    ),
  ],
)

// 子组件需要保持原始大小 → Flexible
Row(
  children: [
    Flexible(
      child: Text('文本内容'),
    ),
  ],
)
```

### 7.2 配合文本溢出处理

```dart
Flexible(
  child: Text(
    '可能很长标题',
    overflow: TextOverflow.ellipsis,
    maxLines: 1,
    style: TextStyle(fontSize: 16),
  ),
)
```

### 7.3 使用有意义的 flex 值

```dart
// ✅ 使用语义化的比例
Row(
  children: [
    Flexible(
      flex: 1,  // 侧边栏
      child: LeftPanel(),
    ),
    Flexible(
      flex: 3,  // 主内容区
      child: MainContent(),
    ),
  ],
)
```

## 总结

Flexible 是 Flutter 中实现弹性布局的重要组件：

**核心要点**：
1. 只能作为 Row、Column、Flex 的子组件
2. FlexFit.loose 允许子组件小于分配空间
3. FlexFit.tight 强制填满（等同于 Expanded）
4. 通过 flex 参数按比例分配

**与 Expanded 区别**：
- Flexible：宽松约束，子组件可以小于分配空间
- Expanded：紧凑约束，强制填满分配空间

**常见场景**：
- 文本自适应布局
- 图片比例分配
- 响应式设计
- 混合固定/弹性布局

---

*最后更新: 2026-02-18*

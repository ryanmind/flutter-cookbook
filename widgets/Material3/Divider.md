# Flutter Divider 组件用法详解

## 简介

Divider 是 Flutter 中的分割线组件，用于在列表项、卡片内容或其他布局元素之间添加视觉分隔。它是一个简单但常用的 UI 组件，可以帮助组织内容和创建视觉层次。

## 1. 初级用法

### 1.1 基本概念

Divider 的核心特点：
- 创建水平或垂直分割线
- 可自定义颜色、高度、粗细
- 可设置左右边距
- Material 3 中有更现代的样式

### 1.2 基本语法

```dart
Column(
  children: [
    Text('第一项'),
    Divider(),
    Text('第二项'),
    Divider(),
    Text('第三项'),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `height` | `double?` | `16.0` | 分割线总高度（含上下间距） |
| `thickness` | `double?` | `0.0` | 分割线粗细 |
| `indent` | `double?` | `0.0` | 左侧缩进 |
| `endIndent` | `double?` | `0.0` | 右侧缩进 |
| `color` | `Color?` | - | 分割线颜色 |

### 1.4 基础示例

#### 简单分割线

```dart
Column(
  children: [
    ListTile(title: Text('项目1')),
    Divider(),
    ListTile(title: Text('项目2')),
    Divider(),
    ListTile(title: Text('项目3')),
  ],
)
```

#### 自定义样式

```dart
Column(
  children: [
    ListTile(title: Text('项目1')),
    Divider(
      height: 24,
      thickness: 2,
      color: Colors.blue,
    ),
    ListTile(title: Text('项目2')),
    Divider(
      indent: 16,
      endIndent: 16,
    ),
    ListTile(title: Text('项目3')),
  ],
)
```

## 2. 中级用法

### 2.1 列表中的分割线

```dart
ListView(
  children: [
    ListTile(
      leading: Icon(Icons.person),
      title: Text('用户1'),
      subtitle: Text('用户描述'),
    ),
    Divider(height: 1),
    ListTile(
      leading: Icon(Icons.person),
      title: Text('用户2'),
      subtitle: Text('用户描述'),
    ),
    Divider(height: 1),
    ListTile(
      leading: Icon(Icons.person),
      title: Text('用户3'),
      subtitle: Text('用户描述'),
    ),
  ],
)
```

### 2.2 带缩进的分割线

```dart
Column(
  children: [
    ListTile(
      leading: CircleAvatar(child: Text('A')),
      title: Text('带头像的列表项'),
    ),
    Divider(
      indent: 72,  // 与头像对齐
      height: 1,
    ),
    ListTile(
      leading: CircleAvatar(child: Text('B')),
      title: Text('带头像的列表项'),
    ),
    Divider(
      indent: 72,
      height: 1,
    ),
    ListTile(
      leading: CircleAvatar(child: Text('C')),
      title: Text('带头像的列表项'),
    ),
  ],
)
```

### 2.3 VerticalDivider 垂直分割线

```dart
Row(
  children: [
    Expanded(child: Center(child: Text('左侧'))),
    VerticalDivider(
      width: 1,
      thickness: 1,
      color: Colors.grey,
    ),
    Expanded(child: Center(child: Text('右侧'))),
  ],
)
```

### 2.4 分组标题

```dart
Column(
  children: [
    // 第一组
    Padding(
      padding: EdgeInsets.fromLTRB(16, 16, 16, 8),
      child: Align(
        alignment: Alignment.centerLeft,
        child: Text(
          '基本设置',
          style: TextStyle(
            color: Colors.grey[600],
            fontWeight: FontWeight.bold,
          ),
        ),
      ),
    ),
    ListTile(title: Text('设置1')),
    ListTile(title: Text('设置2')),
    Divider(height: 24, thickness: 8, color: Colors.grey[200]),
    // 第二组
    Padding(
      padding: EdgeInsets.fromLTRB(16, 16, 16, 8),
      child: Align(
        alignment: Alignment.centerLeft,
        child: Text(
          '高级设置',
          style: TextStyle(
            color: Colors.grey[600],
            fontWeight: FontWeight.bold,
          ),
        ),
      ),
    ),
    ListTile(title: Text('设置3')),
    ListTile(title: Text('设置4')),
  ],
)
```

### 2.5 卡片内容分割

```dart
Card(
  child: Column(
    children: [
      ListTile(
        leading: Icon(Icons.info),
        title: Text('标题'),
      ),
      Divider(height: 1),
      Padding(
        padding: EdgeInsets.all(16),
        child: Text('内容描述，这里是卡片的详细内容说明。'),
      ),
      Divider(height: 1),
      Row(
        mainAxisAlignment: MainAxisAlignment.end,
        children: [
          TextButton(child: Text('取消'), onPressed: () {}),
          TextButton(child: Text('确定'), onPressed: () {}),
        ],
      ),
    ],
  ),
)
```

## 3. 高级用法

### 3.1 自定义分割线组件

```dart
class CustomDivider extends StatelessWidget {
  final double height;
  final double thickness;
  final Color color;
  final double indent;
  final double endIndent;

  const CustomDivider({
    this.height = 16,
    this.thickness = 1,
    this.color = Colors.grey,
    this.indent = 0,
    this.endIndent = 0,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Divider(
      height: height,
      thickness: thickness,
      color: color,
      indent: indent,
      endIndent: endIndent,
    );
  }
}

// 使用示例
Column(
  children: [
    Text('项目1'),
    CustomDivider(indent: 16, endIndent: 16),
    Text('项目2'),
  ],
)
```

### 3.2 带文字的分割线

```dart
class TextDivider extends StatelessWidget {
  final String text;
  final Color color;

  const TextDivider({
    required this.text,
    this.color = Colors.grey,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Expanded(
          child: Divider(color: color),
        ),
        Padding(
          padding: EdgeInsets.symmetric(horizontal: 16),
          child: Text(
            text,
            style: TextStyle(color: color),
          ),
        ),
        Expanded(
          child: Divider(color: color),
        ),
      ],
    );
  }
}

// 使用示例
Column(
  children: [
    Text('上方内容'),
    SizedBox(height: 16),
    TextDivider(text: '或'),
    SizedBox(height: 16),
    Text('下方内容'),
  ],
)
```

### 3.3 渐变分割线

```dart
class GradientDivider extends StatelessWidget {
  final double height;
  final double thickness;
  final List<Color> colors;

  const GradientDivider({
    this.height = 16,
    this.thickness = 1,
    this.colors = const [Colors.transparent, Colors.grey, Colors.transparent],
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      height: height,
      child: Center(
        child: Container(
          height: thickness,
          decoration: BoxDecoration(
            gradient: LinearGradient(
              colors: colors,
            ),
          ),
        ),
      ),
    );
  }
}

// 使用示例
Column(
  children: [
    Text('内容'),
    GradientDivider(height: 24, thickness: 1),
    Text('更多内容'),
  ],
)
```

### 3.4 虚线分割线

```dart
class DashedDivider extends StatelessWidget {
  final double height;
  final double dashWidth;
  final double dashSpace;
  final double thickness;
  final Color color;

  const DashedDivider({
    this.height = 16,
    this.dashWidth = 5,
    this.dashSpace = 3,
    this.thickness = 1,
    this.color = Colors.grey,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final boxWidth = constraints.constrainWidth();
        final dashCount = (boxWidth / (dashWidth + dashSpace)).floor();
        
        return Container(
          height: height,
          child: Row(
            mainAxisAlignment: MainAxisAlignment.spaceBetween,
            children: List.generate(dashCount, (_) {
              return Container(
                width: dashWidth,
                height: thickness,
                color: color,
              );
            }),
          ),
        );
      },
    );
  }
}

// 使用示例
Column(
  children: [
    Text('项目1'),
    DashedDivider(),
    Text('项目2'),
  ],
)
```

### 3.5 分组分割线

```dart
class SectionDivider extends StatelessWidget {
  final String? title;
  final Color? color;

  const SectionDivider({
    this.title,
    this.color,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        if (title != null)
          Padding(
            padding: EdgeInsets.fromLTRB(16, 16, 16, 8),
            child: Text(
              title!,
              style: TextStyle(
                color: color ?? Colors.grey[600],
                fontWeight: FontWeight.bold,
                fontSize: 12,
              ),
            ),
          ),
        Divider(
          height: 1,
          thickness: 8,
          color: color?.withValues(alpha: 0.1) ?? Colors.grey[200],
        ),
      ],
    );
  }
}

// 使用示例
ListView(
  children: [
    ListTile(title: Text('设置项1')),
    ListTile(title: Text('设置项2')),
    SectionDivider(title: '高级设置'),
    ListTile(title: Text('设置项3')),
    ListTile(title: Text('设置项4')),
    SectionDivider(),
    ListTile(title: Text('设置项5')),
  ],
)
```

## 4. ListView 中的分割线

### 4.1 ListView.separated

```dart
ListView.separated(
  itemCount: 20,
  separatorBuilder: (context, index) {
    return Divider(height: 1);
  },
  itemBuilder: (context, index) {
    return ListTile(
      title: Text('项目 ${index + 1}'),
    );
  },
)
```

### 4.2 自定义分割线

```dart
ListView.separated(
  itemCount: 20,
  separatorBuilder: (context, index) {
    // 每隔5个添加分组分割线
    if ((index + 1) % 5 == 0) {
      return Container(
        height: 8,
        color: Colors.grey[200],
      );
    }
    return Divider(height: 1);
  },
  itemBuilder: (context, index) {
    return ListTile(
      title: Text('项目 ${index + 1}'),
    );
  },
)
```

### 4.3 条件分割线

```dart
ListView.separated(
  itemCount: items.length,
  separatorBuilder: (context, index) {
    final item = items[index];
    // 根据条件显示不同分割线
    if (item.isSection) {
      return Divider(height: 24, thickness: 1, color: Colors.blue);
    }
    return Divider(height: 1);
  },
  itemBuilder: (context, index) {
    return ListTile(title: Text(items[index].title));
  },
)
```

## 5. 常见问题与解决方案

### 问题1：Divider 不显示

```dart
// ❌ 问题：thickness 为 0（默认值）
Divider()  // 默认 thickness: 0，可能不可见

// ✅ 解决方案：设置 thickness
Divider(thickness: 1)
```

### 问题2：Divider 占用过多空间

```dart
// ❌ 问题：height 设置过大
Divider(height: 50)  // 占用 50 像素高度

// ✅ 解决方案：调整 height
Divider(
  height: 16,  // 合理的高度
  thickness: 1,
)
```

### 问题3：VerticalDivider 在 Row 中不显示

```dart
// ❌ 问题：Row 没有给 VerticalDivider 分配高度
Row(
  children: [
    Text('左'),
    VerticalDivider(),  // 没有高度约束
    Text('右'),
  ],
)

// ✅ 解决方案：给 VerticalDivider 设置高度
Row(
  children: [
    Text('左'),
    SizedBox(
      height: 50,
      child: VerticalDivider(width: 1),
    ),
    Text('右'),
  ],
)

// 或者使用 IntrinsicHeight
IntrinsicHeight(
  child: Row(
    children: [
      Text('左'),
      VerticalDivider(),
      Text('右'),
    ],
  ),
)
```

### 问题4：Divider 颜色不明显

```dart
// ✅ 设置更明显的颜色
Divider(
  thickness: 1,
  color: Colors.grey[400],  // 更深的灰色
)
```

## 6. Divider vs 其他分割方式对比

| 方式 | 特点 | 适用场景 |
|------|------|---------|
| **Divider** | 水平分割线 | 列表项分隔 |
| **VerticalDivider** | 垂直分割线 | 行内分隔 |
| **Container** | 自定义分割线 | 特殊样式需求 |
| **Border** | 边框分割 | 卡片、容器分隔 |
| **SizedBox** | 空白分割 | 无线条分隔 |

```dart
// Divider - 标准分割线
Divider()

// Container - 自定义分割
Container(
  height: 1,
  color: Colors.grey,
)

// Border - 边框分割
Container(
  decoration: BoxDecoration(
    border: Border(
      bottom: BorderSide(color: Colors.grey),
    ),
  ),
  child: Text('内容'),
)

// SizedBox - 空白分割
SizedBox(height: 16)
```

## 7. 最佳实践

### 7.1 统一分割线样式

```dart
class AppDividers {
  static Widget get standard => Divider(height: 1, thickness: 1);
  
  static Widget get section => Container(
    height: 8,
    color: Colors.grey[100],
  );
  
  static Widget get thick => Divider(
    height: 24,
    thickness: 2,
    color: Colors.grey[300],
  );
  
  static Widget withIndent(double indent) => Divider(
    height: 1,
    indent: indent,
    endIndent: indent,
  );
}

// 使用示例
Column(
  children: [
    ListTile(title: Text('项目1')),
    AppDividers.standard,
    ListTile(title: Text('项目2')),
    AppDividers.section,
    ListTile(title: Text('项目3')),
  ],
)
```

### 7.2 配合 ListTile 使用

```dart
// ListTile 默认有分割线效果
ListView(
  children: [
    ListTile(title: Text('项目1')),
    Divider(height: 0.5, indent: 16, endIndent: 16),
    ListTile(title: Text('项目2')),
    Divider(height: 0.5, indent: 16, endIndent: 16),
    ListTile(title: Text('项目3')),
  ],
)
```

### 7.3 响应式分割线

```dart
class ResponsiveDivider extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final indent = constraints.maxWidth > 600 ? 24.0 : 16.0;
        return Divider(
          indent: indent,
          endIndent: indent,
        );
      },
    );
  }
}
```

## 总结

Divider 是 Flutter 中简单但重要的分割组件：

**核心要点**：
1. 用于创建视觉分隔
2. height 控制总高度，thickness 控制线条粗细
3. indent 和 endIndent 控制左右缩进
4. VerticalDivider 用于垂直分隔

**常见场景**：
- 列表项分隔
- 分组标题
- 卡片内容分隔
- 行内元素分隔

**最佳实践**：
1. 统一分割线样式
2. 合理设置高度和粗细
3. 使用 ListView.separated 自动分割
4. 自定义分割线组件复用

---

*最后更新: 2026-02-18*

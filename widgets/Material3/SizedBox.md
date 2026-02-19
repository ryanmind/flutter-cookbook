# Flutter SizedBox 组件用法详解

## 简介

SizedBox 是 Flutter 中最简单却最常用的布局组件之一。它是一个具有固定尺寸的盒子，可以用来设置子组件的尺寸，或者作为间距组件使用。

## 1. 初级用法

### 1.1 基本概念

SizedBox 主要有两个用途：
1. **设置尺寸** - 为子组件指定固定的宽度和/或高度
2. **创建间距** - 在布局中添加空白间隔

### 1.2 基本语法

```dart
// 创建固定尺寸的盒子
SizedBox(
  width: 100,
  height: 100,
  child: Container(color: Colors.blue),
)

// 创建间距
SizedBox(height: 16)  // 垂直间距
SizedBox(width: 16)   // 水平间距
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `width` | `double?` | 宽度，null 表示使用子组件宽度 |
| `height` | `double?` | 高度，null 表示使用子组件高度 |
| `child` | `Widget?` | 子组件 |

### 1.4 基础示例

#### 设置固定尺寸

```dart
SizedBox(
  width: 200,
  height: 100,
  child: Container(
    color: Colors.blue,
    child: Center(child: Text('固定尺寸')),
  ),
)
```

#### 创建间距

```dart
Column(
  children: [
    Text('第一行'),
    SizedBox(height: 16),  // 16像素垂直间距
    Text('第二行'),
    SizedBox(height: 16),
    Text('第三行'),
  ],
)
```

#### 水平间距

```dart
Row(
  children: [
    Text('左边'),
    SizedBox(width: 24),  // 24像素水平间距
    Text('右边'),
  ],
)
```

## 2. 中级用法

### 2.1 常用构造函数

#### SizedBox()

标准构造函数，指定宽高：

```dart
SizedBox(
  width: 100,
  height: 50,
  child: Container(color: Colors.red),
)
```

#### SizedBox.expand()

创建填满父容器的盒子：

```dart
SizedBox.expand(
  child: Container(
    color: Colors.green,
    child: Center(child: Text('填满父容器')),
  ),
)
```

等效于：
```dart
SizedBox(
  width: double.infinity,
  height: double.infinity,
  child: Container(color: Colors.green),
)
```

#### SizedBox.shrink()

创建零尺寸的盒子：

```dart
SizedBox.shrink()  // width: 0, height: 0
```

常用于条件渲染时占位：
```dart
Column(
  children: [
    if (showHeader) HeaderWidget(),
    if (!showHeader) SizedBox.shrink(),  // 不占空间
    ContentWidget(),
  ],
)
```

#### SizedBox.fromSize()

从 Size 对象创建：

```dart
SizedBox.fromSize(
  size: Size(150, 80),
  child: Container(color: Colors.orange),
)
```

#### SizedBox.square()

创建正方形盒子：

```dart
SizedBox.square(
  dimension: 100,  // 宽高都是100
  child: Container(color: Colors.purple),
)
```

### 2.2 宽高比例控制

虽然 SizedBox 不能直接设置宽高比，但可以配合其他组件实现：

```dart
// 使用 AspectRatio 控制宽高比
AspectRatio(
  aspectRatio: 16 / 9,
  child: Container(color: Colors.blue),
)

// 或者使用 ConstrainedBox + SizedBox
ConstrainedBox(
  constraints: BoxConstraints(
    maxWidth: 200,
    maxHeight: 100,
  ),
  child: SizedBox(
    width: double.infinity,
    height: double.infinity,
    child: Container(color: Colors.green),
  ),
)
```

### 2.3 响应式尺寸

结合 MediaQuery 实现响应式：

```dart
LayoutBuilder(
  builder: (context, constraints) {
    return SizedBox(
      width: constraints.maxWidth * 0.8,  // 父容器宽度的80%
      height: 100,
      child: Container(color: Colors.blue),
    );
  },
)
```

### 2.4 常见布局场景

#### 列表项间距

```dart
ListView.separated(
  itemCount: 10,
  separatorBuilder: (context, index) => SizedBox(height: 8),
  itemBuilder: (context, index) {
    return Container(
      height: 60,
      color: Colors.blue[100 * (index % 9 + 1)],
      child: Center(child: Text('项目 $index')),
    );
  },
)
```

#### 卡片布局间距

```dart
Column(
  children: [
    // 标题区域
    SizedBox(
      width: double.infinity,
      child: Container(
        padding: EdgeInsets.all(16),
        color: Colors.blue,
        child: Text('标题', style: TextStyle(color: Colors.white, fontSize: 18)),
      ),
    ),
    SizedBox(height: 12),  // 标题与内容间距
    // 内容区域
    Expanded(
      child: Padding(
        padding: EdgeInsets.symmetric(horizontal: 16),
        child: Text('内容描述...'),
      ),
    ),
    SizedBox(height: 16),  // 内容与按钮间距
    // 按钮区域
    Padding(
      padding: EdgeInsets.all(16),
      child: Row(
        children: [
          Expanded(
            child: ElevatedButton(
              onPressed: () {},
              child: Text('取消'),
            ),
          ),
          SizedBox(width: 16),  // 按钮间距
          Expanded(
            child: ElevatedButton(
              onPressed: () {},
              child: Text('确认'),
            ),
          ),
        ],
      ),
    ),
  ],
)
```

#### 表单布局

```dart
Column(
  children: [
    TextField(
      decoration: InputDecoration(labelText: '用户名'),
    ),
    SizedBox(height: 16),  // 输入框间距
    TextField(
      decoration: InputDecoration(labelText: '密码'),
      obscureText: true,
    ),
    SizedBox(height: 24),  // 输入框与按钮间距
    SizedBox(
      width: double.infinity,
      height: 48,
      child: ElevatedButton(
        onPressed: () {},
        child: Text('登录'),
      ),
    ),
  ],
)
```

## 3. 高级用法

### 3.1 AnimatedSizedBox 动画

虽然 Flutter 没有直接的 AnimatedSizedBox，但可以使用 AnimatedContainer 或 AnimatedSize 实现：

```dart
class AnimatedSizedBoxExample extends StatefulWidget {
  @override
  _AnimatedSizedBoxExampleState createState() => _AnimatedSizedBoxExampleState();
}

class _AnimatedSizedBoxExampleState extends State<AnimatedSizedBoxExample> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        GestureDetector(
          onTap: () => setState(() => _expanded = !_expanded),
          child: AnimatedContainer(
            duration: Duration(milliseconds: 300),
            width: _expanded ? 200 : 100,
            height: _expanded ? 200 : 100,
            color: Colors.blue,
            child: Center(child: Text('点击切换尺寸')),
          ),
        ),
      ],
    );
  }
}
```

### 3.2 条件渲染占位

```dart
class ConditionalWidget extends StatelessWidget {
  final bool showContent;

  const ConditionalWidget({required this.showContent});

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('固定内容'),
        // 方案1：占位保持高度不变
        showContent
            ? Container(height: 100, color: Colors.blue, child: Text('条件内容'))
            : SizedBox(height: 100),  // 空白占位，保持 100 高度
        // 方案2：完全不占空间
        // showContent
        //     ? Container(height: 100, color: Colors.blue, child: Text('条件内容'))
        //     : SizedBox.shrink(),  // 零尺寸，不占空间
        Text('底部内容'),
      ],
    );
  }
}
```

### 3.3 占位符加载

```dart
class LoadingPlaceholder extends StatelessWidget {
  final bool isLoading;
  final Widget child;

  const LoadingPlaceholder({
    required this.isLoading,
    required this.child,
  });

  @override
  Widget build(BuildContext context) {
    if (isLoading) {
      return SizedBox(
        width: double.infinity,
        height: 200,
        child: Center(
          child: CircularProgressIndicator(),
        ),
      );
    }
    return child;
  }
}
```

### 3.4 自定义间距组件

```dart
// 垂直间距
class VSpace extends StatelessWidget {
  final double height;

  const VSpace(this.height, {Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) => SizedBox(height: height);
}

// 水平间距
class HSpace extends StatelessWidget {
  final double width;

  const HSpace(this.width, {Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) => SizedBox(width: width);
}

// 使用示例
Column(
  children: [
    Text('第一行'),
    VSpace(16),  // 更简洁的写法
    Text('第二行'),
  ],
)
```

### 3.5 性能优化技巧

```dart
// 使用 const 构造函数
const SizedBox(height: 16)  // 编译时常量，性能最佳

// 预定义常用间距
class AppSpacing {
  static const xs = SizedBox(height: 4, width: 4);
  static const sm = SizedBox(height: 8, width: 8);
  static const md = SizedBox(height: 16, width: 16);
  static const lg = SizedBox(height: 24, width: 24);
  static const xl = SizedBox(height: 32, width: 32);
}

// 使用示例
Column(
  children: [
    Text('标题'),
    AppSpacing.md,
    Text('内容'),
    AppSpacing.lg,
    Text('底部'),
  ],
)
```

## 4. 常见问题与解决方案

### 问题1：SizedBox 没有效果

**原因**：父组件的约束限制了 SizedBox 的尺寸

```dart
// 错误：在 Expanded 内使用 SizedBox 限制宽度无效
Row(
  children: [
    Expanded(
      child: SizedBox(
        width: 100,  // 无效，Expanded 会强制填满
        child: Container(color: Colors.red),
      ),
    ),
  ],
)

// 正确：直接使用 SizedBox
Row(
  children: [
    SizedBox(
      width: 100,
      child: Container(color: Colors.red),
    ),
    Expanded(child: Container(color: Colors.blue)),
  ],
)
```

### 问题2：SizedBox.expand 溢出

**原因**：父容器没有提供有效的约束

```dart
// 错误：Column 中使用 expand 会导致高度无限
Column(
  children: [
    SizedBox.expand(  // 高度溢出
      child: Container(color: Colors.red),
    ),
  ],
)

// 正确：使用 Expanded 或指定高度
Column(
  children: [
    Expanded(
      child: Container(color: Colors.red),
    ),
  ],
)

// 或者在外层限制高度
SizedBox(
  height: 200,
  child: SizedBox.expand(
    child: Container(color: Colors.red),
  ),
)
```

### 问题3：子组件尺寸超出 SizedBox

**解决方案**：使用 clipBehavior 或 ClipRect

```dart
SizedBox(
  width: 100,
  height: 100,
  child: ClipRect(
    child: Container(
      width: 200,  // 超出部分会被裁剪
      height: 200,
      color: Colors.blue,
    ),
  ),
)
```

## 5. SizedBox vs 其他组件对比

| 组件 | 用途 | 特点 |
|------|------|------|
| **SizedBox** | 设置固定尺寸/间距 | 最简单，性能最好 |
| **Container** | 综合布局容器 | 功能多，可设置装饰、边距等 |
| **ConstrainedBox** | 设置约束 | 可以设置最小/最大尺寸 |
| **LimitedBox** | 限制最大尺寸 | 只在无边界约束时生效 |
| **FractionallySizedBox** | 按比例设置尺寸 | 基于父容器尺寸的百分比 |

### 选择建议

```dart
// 只需要尺寸或间距 → 使用 SizedBox
SizedBox(height: 16)

// 需要装饰（颜色、边框、圆角等） → 使用 Container
Container(
  width: 100,
  height: 100,
  decoration: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(8),
  ),
)

// 需要限制尺寸范围 → 使用 ConstrainedBox
ConstrainedBox(
  constraints: BoxConstraints(
    minWidth: 100,
    maxWidth: 200,
  ),
  child: Container(color: Colors.red),
)

// 需要按父容器比例设置 → 使用 FractionallySizedBox
FractionallySizedBox(
  widthFactor: 0.8,  // 父容器宽度的80%
  heightFactor: 0.5, // 父容器高度的50%
  child: Container(color: Colors.green),
)
```

## 6. 最佳实践

### 6.1 统一间距管理

```dart
// 定义设计系统的间距常量
abstract class AppDimensions {
  // 间距
  static const double spacing4 = 4.0;
  static const double spacing8 = 8.0;
  static const double spacing12 = 12.0;
  static const double spacing16 = 16.0;
  static const double spacing24 = 24.0;
  static const double spacing32 = 32.0;
  static const double spacing48 = 48.0;

  // 组件尺寸
  static const double buttonHeight = 48.0;
  static const double inputHeight = 56.0;
  static const double iconSize = 24.0;
  static const double avatarSize = 40.0;
}

// 使用示例
Column(
  children: [
    Text('标题'),
    SizedBox(height: AppDimensions.spacing16),
    Text('内容'),
    SizedBox(height: AppDimensions.spacing24),
    SizedBox(
      height: AppDimensions.buttonHeight,
      child: ElevatedButton(...),
    ),
  ],
)
```

### 6.2 避免"魔法数字"

```dart
// ❌ 不推荐：直接使用数字
SizedBox(height: 15.5)  // 魔法数字，难以理解

// ✅ 推荐：使用有意义的常量
SizedBox(height: AppDimensions.spacing16)
```

### 6.3 条件间距

```dart
// 根据条件动态调整间距
Column(
  children: [
    Text('标题'),
    SizedBox(height: hasDescription ? 8 : 16),  // 有描述时间距小
    if (hasDescription) Text('描述内容'),
    SizedBox(height: hasDescription ? 16 : 0),
    Text('底部'),
  ],
)
```

## 总结

SizedBox 是 Flutter 中最基础但最重要的组件之一：

**核心要点**：
1. 主要用途：设置固定尺寸、创建间距
2. 性能优越：比 Container 更轻量
3. 构造函数：`SizedBox()`、`expand()`、`shrink()`、`square()`

**最佳实践**：
1. 使用 `const` 提升性能
2. 统一管理间距常量
3. 简单场景优先使用 SizedBox，复杂场景再考虑 Container

**常见场景**：
- 列表项间距
- 表单元素间距
- 按钮/图标间距
- 组件尺寸限制

---

*最后更新: 2026-02-18*

# Flutter 学习文档项目

## 项目概述

这是一个 Flutter 框架学习文档集合，主要涵盖 Flutter 布局组件和 Material 3 设计系统的学习指南。文档内容从初级到高级，包含大量代码示例和最佳实践。

## 学习路径建议

### 第1阶段：布局基础
1. **SizedBox** → widgets/Material3/SizedBox.md
2. **Container** → widgets/Material3/Container.md
3. **Row/Column** → widgets/Material3/Row.md → Column.md
4. **Expanded/Flexible** → widgets/Material3/Expanded.md → Flexible.md

### 第2阶段：高级布局
5. **Stack/Positioned** → widgets/Material3/Stack.md + widgets/Base/Positioned.md
6. **Wrap** → widgets/Material3/Wrap.md
7. **GridView** → widgets/Material3/GridView.md
8. **Align/Center/Padding** → widgets/Base/Align.md → Center.md → Padding.md

### 第3阶段：页面结构
9. **Scaffold** → widgets/Material3/Scaffold.md
10. **AppBar** → widgets/Material3/AppBar.md
11. **TabBar** → widgets/Material3/TabBar.md
12. **Navigation** → widgets/Material3/Navigation.md
13. **SafeArea** → widgets/Material3/SafeArea.md

### 第4阶段：滚动与列表
14. **ListView** → widgets/Material3/ListView.md
15. **SingleChildScrollView** → widgets/Base/SingleChildScrollView.md
16. **CustomScrollView** → widgets/Base/CustomScrollView.md
17. **ListTile** → widgets/Material3/ListTile.md

### 第5阶段：交互输入
18. **Button** → widgets/Material3/Button.md
19. **TextField** → widgets/Material3/TextField.md
20. **Form/FormField** → widgets/Base/Form.md → FormField.md
21. **Checkbox/Radio/Switch** → widgets/Material3/Checkbox.md → Radio.md → Switch.md

### 第6阶段：反馈展示
22. **Dialog/AlertDialog** → widgets/Material3/Dialog.md → AlertDialog.md
23. **SnackBar** → widgets/Material3/SnackBar.md
24. **BottomSheet** → widgets/Material3/BottomSheet.md
25. **Image/Icon** → widgets/Material3/Image.md → Icon.md

### 第7阶段：高级组件
26. **GestureDetector** → widgets/Base/GestureDetector.md
27. **Hero** → widgets/Base/Hero.md
28. **MediaQuery** → widgets/Base/MediaQuery.md
29. **LayoutBuilder** → widgets/Base/LayoutBuilder.md

### Material 3 概览
30. **Material 3 指南** → Flutter_Material3_组件指南.md

## 目录结构

```
dart/
├── AGENTS.md                    # 本文件
├── Flutter_Material3_组件指南.md  # Material 3 组件学习指南
│
├── syntax/                      # Dart 语法学习
│   └── TODO.md
│
└── widgets/                     # Flutter 组件学习
    ├── TODO.md
    │
    ├── Base/                    # 基础组件
    │   ├── Align.md             # Align 对齐组件
    │   ├── AspectRatio.md       # AspectRatio 宽高比组件
    │   ├── Center.md            # Center 居中组件
    │   ├── ClipOval.md          # ClipOval 椭圆裁剪组件
    │   ├── ClipRect.md          # ClipRect 矩形裁剪组件
    │   ├── ClipRRect.md         # ClipRRect 圆角矩形裁剪组件
    │   ├── ConstrainedBox.md    # ConstrainedBox 约束盒组件
    │   ├── CustomPaint.md       # CustomPaint 自定义绘制组件
    │   ├── CustomScrollView.md  # CustomScrollView 自定义滚动视图
    │   ├── DecoratedBox.md      # DecoratedBox 装饰盒组件
    │   ├── Dismissible.md       # Dismissible 可滑动删除组件
    │   ├── Draggable.md         # Draggable 可拖拽组件
    │   ├── DragTarget.md        # DragTarget 拖拽目标组件
    │   ├── FittedBox.md         # FittedBox 缩放适应组件
    │   ├── Form.md              # Form 表单组件
    │   ├── FormField.md         # FormField 表单字段组件
    │   ├── GestureDetector.md   # GestureDetector 手势检测组件
    │   ├── Hero.md              # Hero 动画过渡组件
    │   ├── InteractiveViewer.md # InteractiveViewer 交互查看器
    │   ├── LayoutBuilder.md     # LayoutBuilder 布局构建器
    │   ├── MediaQuery.md        # MediaQuery 媒体查询组件
    │   ├── Opacity.md           # Opacity 透明度组件
    │   ├── Padding.md           # Padding 内边距组件
    │   ├── Positioned.md        # Positioned 定位组件
    │   ├── ReorderableListView.md # ReorderableListView 可排序列表
    │   ├── SingleChildScrollView.md # SingleChildScrollView 单子滚动视图
    │   ├── Spacer.md            # Spacer 间距组件
    │   └── Transform.md         # Transform 变换组件
    │
    └── Material3/               # Material 3 组件
        ├── AlertDialog.md       # AlertDialog 警告对话框
        ├── AppBar.md            # AppBar 应用栏
        ├── Badge.md             # Badge 徽章
        ├── BottomNavigationBar.md # BottomNavigationBar 底部导航栏
        ├── BottomSheet.md       # BottomSheet 底部弹出
        ├── Button.md            # Button 按钮（5种类型）
        ├── Card.md              # Card 卡片
        ├── Checkbox.md          # Checkbox 复选框
        ├── Chip.md              # Chip 标签
        ├── CircleAvatar.md      # CircleAvatar 圆形头像
        ├── Column.md            # Column 垂直布局
        ├── Container.md         # Container 容器
        ├── DataTable.md         # DataTable 数据表格
        ├── DatePicker.md        # DatePicker 日期选择器
        ├── Dialog.md            # Dialog 对话框
        ├── Divider.md           # Divider 分割线
        ├── Drawer.md            # Drawer 侧边抽屉
        ├── DropdownButton.md    # DropdownButton 下拉菜单
        ├── Expanded.md          # Expanded 填充空间
        ├── Flexible.md          # Flexible 弹性布局
        ├── GridView.md          # GridView 网格布局
        ├── Icon.md              # Icon 图标
        ├── Image.md             # Image 图片
        ├── ListTile.md          # ListTile 列表项
        ├── ListView.md          # ListView 滚动列表
        ├── Menu.md              # Menu 现代菜单
        ├── Navigation.md        # NavigationBar/NavigationRail/Drawer
        ├── PopupMenu.md         # PopupMenu 弹出菜单
        ├── Progress.md          # Progress 进度条
        ├── Radio.md             # Radio 单选框
        ├── RichText.md          # RichText 富文本
        ├── Row.md               # Row 水平布局
        ├── SafeArea.md          # SafeArea 安全区域
        ├── Scaffold.md          # Scaffold 页面骨架
        ├── SearchBar.md         # SearchBar 搜索栏
        ├── SegmentedButton.md   # SegmentedButton 分段按钮
        ├── SelectableText.md    # SelectableText 可选择文本
        ├── SizedBox.md          # SizedBox 尺寸盒子
        ├── Slider.md            # Slider 滑动选择器
        ├── SnackBar.md          # SnackBar 消息提示
        ├── Stack.md             # Stack 堆叠布局
        ├── Switch.md            # Switch 开关
        ├── TabBar.md            # TabBar 标签页
        ├── Text.md              # Text 文本显示
        ├── TextField.md         # TextField 文本输入
        ├── TimePicker.md        # TimePicker 时间选择器
        ├── Tooltip.md           # Tooltip 提示
        └── Wrap.md              # Wrap 流式布局
```

## 文档说明

### Base 基础组件

widgets/Base/ 目录包含 Flutter 核心/基础组件，不依赖 Material Design。

#### 布局辅助
- **Align.md** - 对齐组件，将子组件对齐到指定位置
- **Center.md** - 居中组件，Align 的特例
- **AspectRatio.md** - 宽高比组件，保持子组件宽高比
- **Padding.md** - 内边距组件，为子组件添加内边距
- **Spacer.md** - 间距组件，在 Flex 中占据剩余空间
- **ConstrainedBox.md** - 约束盒组件，限制子组件尺寸
- **FittedBox.md** - 缩放适应组件，根据父容器缩放子组件
- **LayoutBuilder.md** - 布局构建器，根据父约束构建布局

#### 滚动与手势
- **SingleChildScrollView.md** - 单子滚动视图
- **CustomScrollView.md** - 自定义滚动视图，可组合多种 Sliver
- **ReorderableListView.md** - 可拖拽排序的列表
- **GestureDetector.md** - 手势检测组件，识别点击、滑动等手势
- **Dismissible.md** - 可滑动删除组件
- **InteractiveViewer.md** - 交互查看器，支持缩放平移

#### 裁剪与装饰
- **ClipRect.md** - 矩形裁剪
- **ClipRRect.md** - 圆角矩形裁剪
- **ClipOval.md** - 椭圆裁剪
- **DecoratedBox.md** - 装饰盒组件，添加背景、边框等装饰
- **Opacity.md** - 透明度组件，调整子组件透明度
- **Transform.md** - 变换组件，矩阵变换

#### 定位与堆叠
- **Positioned.md** - 定位组件，在 Stack 中定位子组件

#### 表单
- **Form.md** - 表单组件，管理表单状态
- **FormField.md** - 表单字段组件，表单输入的基础

#### 拖拽
- **Draggable.md** - 可拖拽组件
- **DragTarget.md** - 拖拽目标组件，接收拖拽

#### 动画与过渡
- **Hero.md** - Hero 动画过渡，页面间共享元素动画

#### 媒体查询
- **MediaQuery.md** - 媒体查询组件，获取屏幕尺寸等信息

#### 自定义绘制
- **CustomPaint.md** - 自定义绘制组件，使用 Canvas 绘制

---

### Material3 组件

widgets/Material3/ 目录包含 Material Design 3 风格组件。

#### 布局组件
- **Row.md** - 水平布局
- **Column.md** - 垂直布局
- **Stack.md** - 堆叠布局
- **Expanded.md** - 填充剩余空间
- **Flexible.md** - 弹性布局
- **Wrap.md** - 流式布局
- **GridView.md** - 网格布局

#### 容器组件
- **Container.md** - 通用容器组件
- **SizedBox.md** - 尺寸盒子
- **Card.md** - Material 3 卡片
- **Divider.md** - 分割线
- **SafeArea.md** - 安全区域

#### 页面结构
- **Scaffold.md** - 页面骨架
- **AppBar.md** - 应用栏
- **TabBar.md** - 标签页
- **Navigation.md** - 导航组件（NavigationBar/NavigationRail/NavigationDrawer）
- **BottomNavigationBar.md** - 底部导航栏
- **Drawer.md** - 侧边抽屉

#### 滚动列表
- **ListView.md** - 滚动列表
- **ListTile.md** - 列表项
- **DataTable.md** - 数据表格

#### 文本组件
- **Text.md** - 文本显示
- **RichText.md** - 富文本
- **SelectableText.md** - 可选择文本
- **TextField.md** - 文本输入

#### 图像组件
- **Image.md** - 图片
- **Icon.md** - 图标
- **CircleAvatar.md** - 圆形头像

#### 按钮组件
- **Button.md** - 按钮（FilledButton、ElevatedButton、OutlinedButton、TextButton、IconButton）
- **SegmentedButton.md** - 分段按钮

#### 表单组件
- **Checkbox.md** - 复选框
- **Radio.md** - 单选框
- **Switch.md** - 开关
- **Slider.md** - 滑动选择器
- **Menu.md** - 现代菜单（Material 3）
- **DropdownButton.md** - 下拉菜单
- **DatePicker.md** - 日期选择器
- **TimePicker.md** - 时间选择器

#### 反馈组件
- **SnackBar.md** - 消息提示
- **Progress.md** - 进度条
- **Badge.md** - 徽章
- **Tooltip.md** - 提示
- **Dialog.md** - 对话框
- **AlertDialog.md** - 警告对话框
- **BottomSheet.md** - 底部弹出

#### 展示组件
- **Chip.md** - 标签
- **PopupMenu.md** - 弹出菜单
- **SearchBar.md** - 搜索栏

## 文档规范

### 代码示例格式
```dart
// 使用 dart 语法高亮
Widget build(BuildContext context) {
  return Container(
    // 属性注释
    color: Colors.blue,
    child: Text('示例'),
  );
}
```

### 注释规范
- 中文注释，简洁明了
- 复杂逻辑需解释"为什么"而非"是什么"
- 代码注释使用 `//` 单行注释

### 更新规范
- 文档底部标注最后更新日期
- 格式：`*最后更新: YYYY-MM-DD*`

## Git 提交规范

- `[Docs]feat:` - 新增文档内容
- `[Docs]fix:` - 修正文档错误
- `[Docs]update:` - 更新文档信息

示例：
```
[Docs]fix: 修正 Row 和 Container 文档中的技术描述
[Docs]feat: 新增 14 个 Flutter 组件学习文档
```

---

*最后更新: 2026-02-19*

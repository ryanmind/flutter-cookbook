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

### 第8阶段：动画组件
30. **AnimatedContainer** → widgets/Animation/AnimatedContainer.md
31. **AnimatedOpacity** → widgets/Animation/AnimatedOpacity.md
32. **AnimatedAlign** → widgets/Animation/AnimatedAlign.md
33. **AnimatedSwitcher** → widgets/Animation/AnimatedSwitcher.md

### 第9阶段：Sliver 组件
34. **SliverAppBar** → widgets/Sliver/SliverAppBar.md
35. **SliverList** → widgets/Sliver/SliverList.md
36. **SliverGrid** → widgets/Sliver/SliverGrid.md

### Material 3 概览
37. **Material 3 指南** → Flutter_Material3_组件指南.md

## 目录结构

```
dart/
├── AGENTS.md                    # 本文件
├── Flutter_Material3_组件指南.md  # Material 3 组件学习指南
│
├── syntax/                      # Dart 语法学习
│   ├── TODO.md
│   ├── high/                    # 高优先级（基础核心）
│   │   ├── Dart_Async.md        # Dart 异步编程
│   │   ├── Dart_Basics.md       # Dart 基础语法
│   │   ├── Dart_OOP.md          # Dart 面向对象
│   │   ├── Debugging.md         # 调试技巧
│   │   ├── Errors.md            # 错误处理
│   │   ├── Navigator.md         # 导航路由
│   │   ├── ProjectStructure.md  # 项目结构最佳实践
│   │   ├── Provider.md          # Provider 状态管理
│   │   └── StateManagement.md   # 状态管理概述
│   ├── medium/                  # 中优先级（进阶特性）
│   │   ├── Dart_Generics.md     # Dart 泛型
│   │   ├── FlutterWeb.md        # Flutter Web 开发
│   │   ├── GoRouter.md          # GoRouter 路由
│   │   ├── Isolate.md           # Isolate 并发编程
│   │   ├── Networking.md        # 网络请求
│   │   ├── PlatformChannels.md  # 平台通道
│   │   ├── ResponsiveDesign.md  # 响应式设计与多端适配
│   │   ├── Riverpod.md          # Riverpod 状态管理
│   │   ├── Storage.md           # 数据存储
│   │   └── Theming.md           # 主题定制
│   └── low/                     # 低优先级（高级特性）
│       ├── Accessibility.md     # 无障碍支持
│       ├── Animation.md         # 动画系统
│       ├── Bloc.md              # Bloc 状态管理
│       ├── FlutterDesktop.md    # 桌面端开发
│       ├── Gesture.md           # 手势处理
│       ├── I18n.md              # 国际化
│       ├── Lifecycle.md         # 生命周期
│       ├── Performance.md       # 性能优化
│       ├── PluginDevelopment.md # 插件开发
│       ├── RenderObject.md      # RenderObject 深入
│       └── Testing.md           # 测试
│
├── advanced/                   # 进阶教程
│   └── projects/               # 实战项目
│       ├── ECommerceApp.md     # 电商应用实战
│       └── SocialApp.md        # 社交应用实战
│
├── cicd/                       # CI/CD 自动化
│   └── GitHubActions.md        # GitHub Actions 配置
│
├── patterns/                   # 设计模式
│   └── DesignPatterns.md       # Flutter 常用设计模式
│
├── security/                   # 安全
│   └── AppSecurity.md          # 应用安全最佳实践
│
├── packages/                   # 第三方库
│   └── ThirdPartyLibraries.md  # 常用第三方库指南
│
└── widgets/                    # Flutter 组件学习
    ├── TODO.md
    │
    ├── Animation/              # 动画组件
    │   ├── AnimatedAlign.md     # AnimatedAlign 对齐动画
    │   ├── AnimatedBuilder.md   # AnimatedBuilder 动画构建器
    │   ├── AnimatedContainer.md # AnimatedContainer 容器动画
    │   ├── AnimatedCrossFade.md # AnimatedCrossFade 淡入淡出切换
    │   ├── AnimatedOpacity.md   # AnimatedOpacity 透明度动画
    │   ├── AnimatedPadding.md   # AnimatedPadding 内边距动画
    │   ├── AnimatedPositioned.md # AnimatedPositioned 位置动画
    │   └── AnimatedSwitcher.md  # AnimatedSwitcher 组件切换
    │
    ├── Base/                    # 基础组件
    │   ├── Align.md             # Align 对齐组件
    │   ├── AspectRatio.md       # AspectRatio 宽高比组件
    │   ├── Baseline.md          # Baseline 基线对齐组件
    │   ├── Center.md            # Center 居中组件
    │   ├── ClipOval.md          # ClipOval 椭圆裁剪组件
    │   ├── ClipPath.md          # ClipPath 路径裁剪组件
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
    │   ├── Flow.md              # Flow 流式布局组件
    │   ├── Form.md              # Form 表单组件
    │   ├── FormField.md         # FormField 表单字段组件
    │   ├── FractionallySizedBox.md # FractionallySizedBox 百分比尺寸
    │   ├── GestureDetector.md   # GestureDetector 手势检测组件
    │   ├── Hero.md              # Hero 动画过渡组件
    │   ├── InteractiveViewer.md # InteractiveViewer 交互查看器
    │   ├── LayoutBuilder.md     # LayoutBuilder 布局构建器
    │   ├── LimitedBox.md        # LimitedBox 限制盒组件
    │   ├── MediaQuery.md        # MediaQuery 媒体查询组件
    │   ├── Offstage.md          # Offstage 隐藏组件
    │   ├── Opacity.md           # Opacity 透明度组件
    │   ├── OverflowBox.md       # OverflowBox 溢出盒组件
    │   ├── Padding.md           # Padding 内边距组件
    │   ├── Placeholder.md       # Placeholder 占位组件
    │   ├── Positioned.md        # Positioned 定位组件
    │   ├── ReorderableListView.md # ReorderableListView 可排序列表
    │   ├── ScrollConfiguration.md # ScrollConfiguration 滚动配置
    │   ├── SingleChildScrollView.md # SingleChildScrollView 单子滚动视图
    │   ├── SizeChangedLayoutNotifier.md # SizeChangedLayoutNotifier 尺寸变化通知
    │   ├── SizedOverflowBox.md  # SizedOverflowBox 定尺寸溢出盒
    │   ├── Spacer.md            # Spacer 间距组件
    │   ├── Table.md             # Table 表格组件
    │   ├── Transform.md         # Transform 变换组件
    │   └── UnconstrainedBox.md  # UnconstrainedBox 无约束盒
    │
    ├── Material3/               # Material 3 组件
    │   ├── AlertDialog.md       # AlertDialog 警告对话框
    │   ├── AppBar.md            # AppBar 应用栏
    │   ├── Badge.md             # Badge 徽章
    │   ├── BottomAppBar.md      # BottomAppBar 底部应用栏
    │   ├── BottomNavigationBar.md # BottomNavigationBar 底部导航栏
    │   ├── BottomSheet.md       # BottomSheet 底部弹出
    │   ├── Button.md            # Button 按钮（5种类型）
    │   ├── Card.md              # Card 卡片
    │   ├── Checkbox.md          # Checkbox 复选框
    │   ├── Chip.md              # Chip 标签
    │   ├── CircleAvatar.md      # CircleAvatar 圆形头像
    │   ├── Column.md            # Column 垂直布局
    │   ├── Container.md         # Container 容器
    │   ├── DataTable.md         # DataTable 数据表格
    │   ├── DatePicker.md        # DatePicker 日期选择器
    │   ├── Dialog.md            # Dialog 对话框
    │   ├── Divider.md           # Divider 分割线
    │   ├── Drawer.md            # Drawer 侧边抽屉
    │   ├── DropdownButton.md    # DropdownButton 下拉菜单
    │   ├── Expanded.md          # Expanded 填充空间
    │   ├── Flexible.md          # Flexible 弹性布局
    │   ├── GridView.md          # GridView 网格布局
    │   ├── Icon.md              # Icon 图标
    │   ├── Image.md             # Image 图片
    │   ├── ListTile.md          # ListTile 列表项
    │   ├── ListView.md          # ListView 滚动列表
    │   ├── Menu.md              # Menu 现代菜单
    │   ├── Navigation.md        # NavigationBar/NavigationRail/Drawer
    │   ├── PopupMenu.md         # PopupMenu 弹出菜单
    │   ├── Progress.md          # Progress 进度条
    │   ├── Radio.md             # Radio 单选框
    │   ├── RichText.md          # RichText 富文本
    │   ├── Row.md               # Row 水平布局
    │   ├── SafeArea.md          # SafeArea 安全区域
    │   ├── Scaffold.md          # Scaffold 页面骨架
    │   ├── SearchBar.md         # SearchBar 搜索栏
    │   ├── SegmentedButton.md   # SegmentedButton 分段按钮
    │   ├── SelectableText.md    # SelectableText 可选择文本
    │   ├── SizedBox.md          # SizedBox 尺寸盒子
    │   ├── Slider.md            # Slider 滑动选择器
    │   ├── SnackBar.md          # SnackBar 消息提示
    │   ├── Stack.md             # Stack 堆叠布局
    │   ├── Switch.md            # Switch 开关
    │   ├── TabBar.md            # TabBar 标签页
    │   ├── Text.md              # Text 文本显示
    │   ├── TextField.md         # TextField 文本输入
    │   ├── TimePicker.md        # TimePicker 时间选择器
    │   ├── Tooltip.md           # Tooltip 提示
    │   └── Wrap.md              # Wrap 流式布局
    │
    └── Sliver/                  # Sliver 组件
        ├── SliverAppBar.md      # SliverAppBar 滚动应用栏
        ├── SliverGrid.md        # SliverGrid 滚动网格
        ├── SliverList.md        # SliverList 滚动列表
        ├── SliverPersistentHeader.md # SliverPersistentHeader 持久化头部
        └── SliverToBoxAdapter.md # SliverToBoxAdapter 盒子适配器
```

## 文档说明

### Animation 动画组件

widgets/Animation/ 目录包含 Flutter 动画相关组件。

#### 隐式动画组件
- **AnimatedContainer.md** - 容器属性动画，自动动画化 width、height、color 等
- **AnimatedOpacity.md** - 透明度动画，实现淡入淡出效果
- **AnimatedAlign.md** - 对齐位置动画，子组件位置过渡
- **AnimatedPadding.md** - 内边距动画，间距变化过渡
- **AnimatedPositioned.md** - 位置动画，在 Stack 中定位过渡

#### 切换动画组件
- **AnimatedCrossFade.md** - 两个组件间淡入淡出切换
- **AnimatedSwitcher.md** - 多组件切换动画，基于 Key 识别

#### 动画构建器
- **AnimatedBuilder.md** - 通用动画构建器，配合 AnimationController 使用

---

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
- **FractionallySizedBox.md** - 百分比尺寸组件，相对于父容器
- **LimitedBox.md** - 限制盒组件，无约束时限制最大尺寸
- **OverflowBox.md** - 溢出盒组件，允许子组件超出约束
- **SizedOverflowBox.md** - 定尺寸溢出盒，固定自身尺寸
- **UnconstrainedBox.md** - 无约束盒组件，移除父容器约束
- **Baseline.md** - 基线对齐组件，文本基线对齐
- **Flow.md** - 流式布局组件，高性能自定义布局

#### 滚动与手势
- **SingleChildScrollView.md** - 单子滚动视图
- **CustomScrollView.md** - 自定义滚动视图，可组合多种 Sliver
- **ReorderableListView.md** - 可拖拽排序的列表
- **GestureDetector.md** - 手势检测组件，识别点击、滑动等手势
- **Dismissible.md** - 可滑动删除组件
- **InteractiveViewer.md** - 交互查看器，支持缩放平移
- **ScrollConfiguration.md** - 滚动配置组件，自定义滚动行为

#### 裁剪与装饰
- **ClipRect.md** - 矩形裁剪
- **ClipRRect.md** - 圆角矩形裁剪
- **ClipOval.md** - 椭圆裁剪
- **ClipPath.md** - 自定义路径裁剪
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

#### 其他
- **Offstage.md** - 隐藏组件，控制子组件显示/隐藏
- **Placeholder.md** - 占位组件，开发阶段占位符
- **Table.md** - 表格组件，创建表格布局
- **SizeChangedLayoutNotifier.md** - 尺寸变化通知组件

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
- **BottomAppBar.md** - 底部应用栏，与 FAB 配合
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

---

### Sliver 组件

widgets/Sliver/ 目录包含 Flutter Sliver 相关组件，用于 CustomScrollView 中。

#### 滚动容器
- **SliverAppBar.md** - 滚动应用栏，支持折叠展开
- **SliverList.md** - 滚动列表，按需构建
- **SliverGrid.md** - 滚动网格，网格布局

#### 适配器
- **SliverToBoxAdapter.md** - 盒子适配器，包装普通组件为 Sliver

#### 头部
- **SliverPersistentHeader.md** - 持久化头部，可折叠固定

### Syntax 语法文档

syntax/ 目录包含 Dart 语言和 Flutter 核心概念学习文档，按优先级分类。

#### 高优先级（基础核心）
- **Dart_Basics.md** - Dart 基础语法，变量、类型、函数等
- **Dart_OOP.md** - Dart 面向对象，类、继承、混入等
- **Dart_Async.md** - Dart 异步编程，Future、Stream、async/await
- **StateManagement.md** - 状态管理概述，核心概念与方案对比
- **Provider.md** - Provider 状态管理，官方推荐的基础方案
- **Navigator.md** - 导航路由，页面跳转与参数传递
- **Errors.md** - 错误处理与异常捕获，try-catch、Result 模式
- **Debugging.md** - 调试技巧，DevTools、断点、日志
- **ProjectStructure.md** - 项目结构最佳实践，目录组织规范

#### 中优先级（进阶特性）
- **Dart_Generics.md** - Dart 泛型，类型参数与约束
- **Networking.md** - 网络请求，HTTP 客户端与数据解析
- **Storage.md** - 数据存储，SharedPreferences、数据库等
- **Theming.md** - 主题定制，深色模式与自定义主题
- **GoRouter.md** - GoRouter 声明式路由
- **Riverpod.md** - Riverpod 状态管理，响应式编程
- **Isolate.md** - Isolate 并发编程，compute 函数
- **PlatformChannels.md** - 平台通道，原生代码交互
- **ResponsiveDesign.md** - 响应式设计，多端适配策略
- **FlutterWeb.md** - Flutter Web 开发注意事项

#### 低优先级（高级特性）
- **Animation.md** - 动画系统，AnimationController、Tween 等
- **Gesture.md** - 手势处理，手势识别与竞争
- **Lifecycle.md** - 生命周期，Widget 与 App 生命周期
- **Performance.md** - 性能优化，渲染优化与内存管理
- **I18n.md** - 国际化，多语言支持
- **Testing.md** - 测试，单元测试与 Widget 测试
- **Bloc.md** - Bloc 状态管理，事件驱动架构
- **RenderObject.md** - RenderObject 深入，自定义渲染
- **PluginDevelopment.md** - 插件开发，平台特定实现
- **Accessibility.md** - 无障碍支持，语义化适配
- **FlutterDesktop.md** - 桌面端开发，窗口与快捷键

---

### Advanced 进阶教程

advanced/ 目录包含实战项目教程。

#### 实战项目
- **ECommerceApp.md** - 电商应用实战，完整项目流程
- **SocialApp.md** - 社交应用实战，实时通讯与交互

---

### CI/CD 自动化

cicd/ 目录包含持续集成与部署相关文档。

- **GitHubActions.md** - GitHub Actions 工作流配置，自动化测试与构建

---

### Patterns 设计模式

patterns/ 目录包含 Flutter 开发中常用的设计模式。

- **DesignPatterns.md** - 创建型、结构型、行为型模式及 Flutter 特有模式

---

### Security 安全

security/ 目录包含应用安全相关内容。

- **AppSecurity.md** - 敏感数据保护、API 安全、代码安全等

---

### Packages 第三方库

packages/ 目录包含常用第三方库使用指南。

- **ThirdPartyLibraries.md** - 网络、状态管理、存储、图片等常用库介绍

## 文档统计

| 分类 | 数量 |
|------|------|
| Syntax 高优先级 | 9 |
| Syntax 中优先级 | 10 |
| Syntax 低优先级 | 11 |
| Animation 动画组件 | 8 |
| Base 基础组件 | 41 |
| Material3 组件 | 49 |
| Sliver 组件 | 5 |
| Advanced 进阶教程 | 2 |
| CI/CD 自动化 | 1 |
| Patterns 设计模式 | 1 |
| Security 安全 | 1 |
| Packages 第三方库 | 1 |
| **总计** | **139** |

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
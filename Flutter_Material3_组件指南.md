# Flutter Material 3 组件学习指南

## 常用组件列表

### 1. 基础容器组件
- **Container** - 通用容器，可以设置装饰、内外边距、约束等
- **Scaffold** - 页面骨架，包含AppBar、Body、FloatingActionButton等
- **Card** - 卡片容器，Material 3中有了更圆润的边角
- **Divider** - 分割线
- **SafeArea** - 安全区域容器

### 2. 布局组件
- **Row** - 水平布局
- **Column** - 垂直布局
- **Stack** - 堆叠布局
- **Expanded** - 占满剩余空间
- **Flexible** - 弹性布局
- **Wrap** - 流式布局
- **GridView** - 网格布局

### 3. 按钮组件
Material 3 提供了 5 种按钮类型，按强调程度排序：

- **FilledButton** - 填充按钮，最高强调，用于主要操作
- **FilledButton.tonal** - 色调填充按钮，次级强调
- **ElevatedButton** - 悬浮按钮（带阴影）
- **OutlinedButton** - 边框按钮
- **TextButton** - 文本按钮，最低强调
- **IconButton** - 图标按钮
- **FloatingActionButton** - 浮动操作按钮

### 4. 文本组件
- **Text** - 基础文本
- **TextField** - 文本输入框
- **RichText** - 富文本
- **SelectableText** - 可选择文本

### 5. 图像组件
- **Image** - 图片显示
- **Icon** - 图标
- **CircleAvatar** - 圆形头像

### 6. 表单组件
- **Checkbox** - 复选框
- **Radio** - 单选框
- **Switch** - 开关
- **Slider** - 滑块
- **Menu** - 现代菜单组件（推荐替代 DropdownButton）
- **DropdownButton** - 下拉菜单（传统组件）

### 7. 列表组件
- **ListView** - 滚动列表
- **ListTile** - 列表项
- **DataTable** - 数据表格

### 8. 导航组件
- **NavigationBar** - 现代底部导航栏（Material 3 推荐）
- **NavigationRail** - 侧边导航栏（平板/桌面端）
- **NavigationDrawer** - 现代抽屉导航
- **BottomNavigationBar** - 底部导航栏（传统组件）
- **TabBar** - 标签页
- **Drawer** - 侧边抽屉（传统组件）
- **AppBar** - 顶部应用栏

### 9. 反馈组件
- **SnackBar** - 底部消息提示
- **Dialog** - 对话框
- **AlertDialog** - 警告对话框
- **BottomSheet** - 底部弹出层
- **LinearProgressIndicator** - 线性进度条
- **CircularProgressIndicator** - 圆形进度条

### 10. Material 3 新增组件
- **Badge** - 徽章组件
- **SegmentedButton** - 分段按钮
- **NavigationBar** - 现代导航栏（替代 BottomNavigationBar）
- **NavigationRail** - 侧边导航栏（平板/桌面端）
- **NavigationDrawer** - 现代抽屉导航
- **SearchBar** - 搜索栏组件
- **DatePicker** - 日期选择器（重新设计）
- **TimePicker** - 时间选择器（重新设计）
- **Menu** - 现代菜单组件
- **Chip** - 标签组件

## Material 3 设计风格特点

Material 3 是 Google 最新的设计语言，主要特点包括：

1. **动态颜色系统** - 基于壁纸自动生成配色方案
2. **更圆润的边角** - 组件使用更大的圆角半径
3. **更现代的排版** - 优化字体大小和行高
4. **改进的按钮设计** - 5 种清晰的按钮类型
5. **更好的视觉层次** - 使用阴影和高亮创建深度感

## Material 3 配色系统

Material 3 使用了基于色调的配色系统：

- **Primary** - 主色调，用于主要组件
- **Secondary** - 次要色调，用于强调组件
- **Tertiary** - 第三色调，用于特殊强调
- **Surface** - 表面颜色，用于卡片、对话框等
- **Background** - 背景颜色
- **Error** - 错误状态颜色

## 组件使用要点

### 启用 Material 3
```dart
MaterialApp(
  theme: ThemeData(
    useMaterial3: true,  // 必须启用
    colorScheme: ColorScheme.fromSeed(seedColor: Colors.blue),
  ),
)
```

### 按钮类型选择
- **FilledButton** - 主要操作，一个页面通常只有一个
- **FilledButton.tonal** - 次要但重要的操作
- **ElevatedButton** - 需要层次感的主要操作
- **OutlinedButton** - 次要操作
- **TextButton** - 不重要或冗余操作

### 卡片设计
- 使用更圆润的边角（默认圆角更大）
- 合理使用阴影创建层次感
- 内容区域保持足够的内边距

## 新手学习建议

### 学习顺序
1. **基础布局** - 先掌握 Row、Column、Container、SizedBox
2. **滚动组件** - 学习 ListView、SingleChildScrollView
3. **交互组件** - 掌握各种按钮和输入框
4. **页面结构** - 理解 Scaffold、AppBar、NavigationBar
5. **状态管理** - 从 setState 开始，逐步学习更高级方案

### 学习技巧
1. **多用 Flutter Inspector** - Android Studio/VSCode 中可视化查看组件树
2. **参考官方文档** - Flutter.dev 有详细的组件说明和示例
3. **关注 Material 3 设计规范** - 了解设计原则有助于正确使用组件
4. **多看源码** - Flutter 框架本身是开源的，可以学习组件实现
5. **练习小项目** - 从简单的待办事项、天气应用开始

### 常见问题解决
1. **布局溢出** - 使用 Flexible、Expanded 或 SingleChildScrollView
2. **键盘遮挡** - 使用 resizeToAvoidBottomInset 和 MediaQuery
3. **状态管理混乱** - 初期保持简单，后期再学习 Provider、Riverpod 等
4. **性能优化** - 使用 const 构造函数，合理使用 ListView.builder

### 推荐资源
- **Flutter 官网** - flutter.dev
- **Material 3 设计规范** - m3.material.io
- **Flutter 中文网** - flutter.cn
- **GitHub 开源项目** - 学习别人的代码实现

---

*最后更新: 2026-02-18*

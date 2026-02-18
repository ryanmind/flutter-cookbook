# Flutter 学习文档项目

## 项目概述

这是一个 Flutter 框架学习文档集合，主要涵盖 Flutter 布局组件和 Material 3 设计系统的学习指南。文档内容从初级到高级，包含大量代码示例和最佳实践。

## 目录结构

```
dart/
├── AGENTS.md                    # 本文件
├── Flutter_Material3_组件指南.md  # Material 3 组件学习指南
│
├── # 布局组件
├── Row.md                       # Row 水平布局组件用法详解
├── Column.md                    # Column 垂直布局组件完整指南
├── Stack.md                     # Stack 堆叠布局组件用法详解
├── Expanded.md                  # Expanded 填充剩余空间组件
├── Flexible.md                  # Flexible 弹性布局组件
├── Wrap.md                      # Wrap 流式布局组件用法详解
├── GridView.md                  # GridView 网格布局组件用法详解
│
├── # 基础容器组件
├── Container.md                 # Container 组件完全教程
├── SizedBox.md                  # SizedBox 组件用法详解
├── Scaffold.md                  # Scaffold Material Design 页面骨架
├── Card.md                      # Card Material 3 卡片组件
├── Divider.md                   # Divider 分割线组件用法详解
├── SafeArea.md                  # SafeArea 安全区域容器组件
│
├── # 滚动组件
├── ListView.md                  # ListView 滚动列表组件用法详解
│
├── # 交互组件
├── Button.md                    # Button 按钮组件用法详解
├── Checkbox.md                  # Checkbox 复选框组件用法详解
├── Dialog.md                    # Dialog 对话框组件用法详解
│
├── # 输入组件
├── Text.md                      # Text 文本显示组件用法详解
├── TextField.md                 # TextField 文本输入组件用法详解
│
├── # 导航组件
├── AppBar.md                    # AppBar 应用栏组件用法详解
├── TabBar.md                    # TabBar 标签页组件用法详解
├── Navigation.md                # Navigation 导航组件用法详解
│
└── # 反馈组件
    ├── Image.md                 # Image 图片组件用法详解
    └── SnackBar.md              # SnackBar 消息提示组件用法详解
```

## 文档说明

### 布局组件

#### 1. Row.md
Flutter 水平布局组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：溢出问题解决、对齐控制、Flexible/Expanded 区别
- 高级用法：自定义布局权重、动态布局、响应式设计、性能优化

#### 2. Column.md
Flutter 垂直布局组件完整教程，包含：
- 初级用法：基本概念、主要属性（mainAxisAlignment、crossAxisAlignment、mainAxisSize 等）
- 中级用法：复杂布局组合、与其他组件配合（Expanded、Flexible、ListView 等）
- 高级用法：自定义布局组件、动态内容、响应式设计、企业级最佳实践

#### 3. Stack.md
Flutter 堆叠布局组件用法详解，包含：
- 初级用法：基本概念、主要属性（alignment、fit、clipBehavior）
- 中级用法：alignment 对齐、Positioned 定位、fit 属性、clipBehavior
- 高级用法：IndexedStack、动态定位、层叠动画、组合布局、水印效果
- 性能优化与常见问题解决方案

#### 4. Expanded.md
Flutter 填充剩余空间组件用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：Column 中使用、固定宽度按钮配合、底部固定布局
- 高级用法：复杂比例布局、响应式布局、嵌套 Expanded
- 与 Flexible 的对比说明

#### 5. Flexible.md
Flutter 弹性布局组件用法详解，包含：
- 初级用法：基本概念、FlexFit 枚举说明
- 中级用法：与 Expanded 对比、按比例分配、混合布局
- 高级用法：动态 flex、嵌套 Flexible、响应式布局、条件性使用
- 性能优化与最佳实践

#### 6. Wrap.md
Flutter 流式布局组件用法详解，包含：
- 初级用法：基本概念、主要属性、标签云示例
- 中级用法：direction、alignment、runAlignment、crossAxisAlignment
- 高级用法：可选择标签、可删除标签、输入标签、图片瀑布流
- 性能优化与最佳实践

#### 7. GridView.md
Flutter 网格布局组件用法详解，包含：
- 初级用法：基本概念、四种构造函数、SliverGridDelegate
- 中级用法：childAspectRatio、滚动控制、响应式列数、下拉刷新
- 高级用法：自定义 delegate、瀑布流、CustomScrollView 配合、上拉加载
- 性能优化与常见问题

### 基础容器组件

#### 8. Container.md
Flutter 容器组件完全教程，包含：
- 基本概念与布局工作原理
- 常用属性：padding、margin、color、width/height、alignment、constraints、decoration、transform、clipBehavior
- 基础示例与视觉效果演示
- 常见错误与解决方案
- 实战练习与性能优化建议

#### 9. SizedBox.md
Flutter 尺寸盒子组件用法详解，包含：
- 初级用法：基本概念、设置尺寸、创建间距
- 中级用法：常用构造函数（expand、shrink、square）、响应式尺寸
- 高级用法：动画、条件渲染占位、自定义间距组件
- 性能优化与最佳实践

#### 10. Scaffold.md
Flutter Material Design 页面骨架用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：AppBar 配置、FloatingActionButton、Drawer、BottomNavigationBar、SnackBar
- 高级用法：TabBar + TabBarView、BottomSheet、嵌套 Scaffold、键盘处理、Material 3 NavigationBar
- Scaffold 状态管理

#### 11. Card.md
Flutter Material 3 卡片组件用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：Material 3 三种变体（默认、outlined、filled）、自定义样式
- 高级用法：产品卡片、文章卡片、统计卡片、可展开卡片、卡片网格
- 性能优化与最佳实践

#### 12. Divider.md
Flutter 分割线组件用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：列表分割线、带缩进分割线、VerticalDivider、分组标题
- 高级用法：自定义分割线、带文字分割线、渐变分割线、虚线分割线
- ListView.separated 配合

#### 13. SafeArea.md
Flutter 安全区域容器组件用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：部分方向安全区域、最小边距、横屏处理
- 高级用法：全屏背景配合、沉浸式状态栏、响应式安全区域、动态处理
- 与 MediaQuery 对比、与其他组件配合

### 滚动组件

#### 14. ListView.md
Flutter 滚动列表组件用法详解，包含：
- 初级用法：基本概念、四种构造函数
- 中级用法：滚动控制、滚动物理特性、shrinkWrap、itemExtent
- 高级用法：下拉刷新/上拉加载、吸顶效果、滑动删除、拖拽排序、分组列表
- 性能优化：构造函数选择、itemExtent、嵌套优化
- 常见问题与解决方案

### 交互组件

#### 15. Button.md
Flutter 按钮组件用法详解，包含：
- 初级用法：5 种按钮类型对比（FilledButton、ElevatedButton、OutlinedButton、TextButton、IconButton）
- 中级用法：按钮样式、图标按钮、按钮组
- 高级用法：自定义按钮、加载状态、按钮主题
- Material 3 按钮设计规范

#### 16. Checkbox.md
Flutter 选择组件用法详解，包含：
- Checkbox 复选框：基本用法、三态模式、多选列表
- Radio 单选框：单选组、自定义样式
- Switch 开关：设置开关、Material 3 样式
- 高级用法：自定义组件、选择器对话框

#### 17. Dialog.md
Flutter 对话框组件用法详解，包含：
- 初级用法：AlertDialog、SimpleDialog
- 中级用法：自定义对话框、返回值传递
- 高级用法：底部弹窗、全屏对话框、表单对话框
- Cupertino 风格对话框

### 输入组件

#### 18. Text.md
Flutter 文本显示组件用法详解，包含：
- 初级用法：基本语法、TextStyle 样式
- 中级用法：文本对齐、溢出处理、行数限制
- 高级用法：富文本 TextSpan、自定义字体、文本选择
- 国际化与语义化

#### 19. TextField.md
Flutter 文本输入组件用法详解，包含：
- 初级用法：基本语法、InputDecoration 装饰
- 中级用法：输入验证、格式化、键盘类型
- 高级用法：自定义装饰、表单集成、搜索框
- 常见问题与解决方案

### 导航组件

#### 20. AppBar.md
Flutter 应用栏组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：操作按钮、底部 TabBar、自定义标题
- 高级用法：SliverAppBar、自定义高度、透明渐变
- Material 3 AppBar 特性

#### 21. TabBar.md
Flutter 标签页组件用法详解，包含：
- 初级用法：DefaultTabController、TabBar、TabBarView
- 中级用法：TabController、自定义样式、指示器
- 高级用法：动态标签、滑动切换、嵌套 TabBar
- 与 AppBar 配合使用

#### 22. Navigation.md
Flutter 导航组件用法详解，包含：
- NavigationBar：Material 3 底部导航栏
- NavigationRail：Material 3 侧边导航栏
- NavigationDrawer：Material 3 抽屉导航
- 响应式导航切换

### 反馈组件

#### 23. Image.md
Flutter 图片组件用法详解，包含：
- 初级用法：网络图片、资产图片、本地文件图片
- 中级用法：填充模式 fit、占位符、错误处理
- 高级用法：图片缓存、圆形图片、图片选择器
- 性能优化

#### 24. SnackBar.md
Flutter 消息提示组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：操作按钮、自定义样式、显示行为
- 高级用法：全局 SnackBar、队列管理
- 与 ScaffoldMessenger 配合

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

## 学习路径建议

### 布局基础
1. **SizedBox** → SizedBox.md（间距与尺寸基础）
2. **Container** → Container.md（容器与装饰基础）
3. **Row/Column** → Row.md → Column.md（线性布局）

### 高级布局
4. **Expanded/Flexible** → Expanded.md → Flexible.md（弹性布局）
5. **Stack** → Stack.md（堆叠布局）
6. **Wrap** → Wrap.md（流式布局）
7. **GridView** → GridView.md（网格布局）

### 页面结构
8. **Scaffold** → Scaffold.md（页面骨架）
9. **AppBar** → AppBar.md（应用栏）
10. **Card** → Card.md（卡片容器）
11. **Divider** → Divider.md（分割线）
12. **SafeArea** → SafeArea.md（安全区域）

### 滚动列表
13. **ListView** → ListView.md（滚动列表）

### 导航组件
14. **TabBar** → TabBar.md（标签页导航）
15. **Navigation** → Navigation.md（NavigationBar、NavigationRail、NavigationDrawer）

### 交互组件
16. **Button** → Button.md（按钮组件）
17. **Checkbox** → Checkbox.md（复选框、单选框、开关）
18. **Dialog** → Dialog.md（对话框）

### 输入组件
19. **Text** → Text.md（文本显示）
20. **TextField** → TextField.md（文本输入）

### 反馈组件
21. **Image** → Image.md（图片显示）
22. **SnackBar** → SnackBar.md（消息提示）

### 组件系统
23. **Material 3** → Flutter_Material3_组件指南.md（组件概览）

## Git 提交规范

- `[Docs]feat:` - 新增文档内容
- `[Docs]fix:` - 修正文档错误
- `[Docs]update:` - 更新文档信息

示例：
```
[Docs]fix: 修正 Row 和 Container 文档中的技术描述
[Docs]feat: 新增 SizedBox 和 ListView 组件学习文档
```
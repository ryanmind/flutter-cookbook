# Flutter 学习文档项目

## 项目概述

这是一个 Flutter 框架学习文档集合，主要涵盖 Flutter 布局组件和 Material 3 设计系统的学习指南。文档内容从初级到高级，包含大量代码示例和最佳实践。

## 学习顺序与窍门

### 学习阶段划分

| 阶段 | 重点 | 学习目标 |
|------|------|---------|
| 第1阶段 | 布局基础 | 掌握 Row/Column/Container 等核心布局 |
| 第2阶段 | 页面结构 | 理解 Scaffold/AppBar/导航组件 |
| 第3阶段 | 交互输入 | 掌握按钮、输入、选择组件 |
| 第4阶段 | 反馈展示 | 学会图片、列表、对话框等 |
| 第5阶段 | Material 3 | 掌握现代设计风格组件 |

### 学习窍门

1. **先理解再记忆** - 理解组件的布局原理比记忆属性更重要
2. **边学边练** - 每学一个组件立即写代码实践
3. **善用 Flutter Inspector** - VSCode/Android Studio 中可视化查看组件树
4. **对比学习** - 相似组件对比学习（如 Expanded vs Flexible）
5. **看源码注释** - Flutter 源码注释非常详细
6. **遇到溢出先想 Flex** - 大部分布局问题都与 Expanded/Flexible 相关
7. **多用 const** - 能用 const 的地方尽量用，提升性能
8. **Material 3 优先** - 新项目建议使用 Material 3 组件

### 常见陷阱

- **溢出错误** - 使用 Expanded/Flexible 或 SingleChildScrollView 解决
- **键盘遮挡** - 使用 resizeToAvoidBottomInset 和 MediaQuery
- **图片不显示** - 检查 pubspec.yaml 资源声明和网络权限
- **状态丢失** - 理解 StatefulWidget 生命周期
- **性能卡顿** - 使用 ListView.builder、const 构造函数

## 目录结构

```
dart/
├── AGENTS.md                    # 本文件
├── Flutter_Material3_组件指南.md  # Material 3 组件学习指南
│
├── # 基础容器组件
├── Container.md                 # Container 组件完全教程
├── SizedBox.md                  # SizedBox 组件用法详解
├── Card.md                      # Card Material 3 卡片组件
├── Divider.md                   # Divider 分割线组件用法详解
├── SafeArea.md                  # SafeArea 安全区域容器组件
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
├── # 页面结构组件
├── Scaffold.md                  # Scaffold Material Design 页面骨架
├── AppBar.md                    # AppBar 应用栏组件用法详解
├── TabBar.md                    # TabBar 标签页组件用法详解
├── Navigation.md                # NavigationBar/NavigationRail/NavigationDrawer
├── BottomNavigationBar.md       # BottomNavigationBar 底部导航栏
├── Drawer.md                    # Drawer 侧边抽屉组件
│
├── # 滚动组件
├── ListView.md                  # ListView 滚动列表组件用法详解
├── ListTile.md                  # ListTile 列表项组件用法详解
├── DataTable.md                 # DataTable 数据表格组件用法详解
│
├── # 文本组件
├── Text.md                      # Text 文本显示组件用法详解
├── RichText.md                  # RichText 富文本组件用法详解
├── SelectableText.md            # SelectableText 可选择文本组件
├── TextField.md                 # TextField 文本输入组件用法详解
│
├── # 图像组件
├── Image.md                     # Image 图片组件用法详解
├── Icon.md                      # Icon 图标组件用法详解
├── CircleAvatar.md              # CircleAvatar 圆形头像组件
│
├── # 按钮组件
├── Button.md                    # Button 按钮组件用法详解
├── SegmentedButton.md           # SegmentedButton 分段按钮组件 (M3)
│
├── # 表单组件
├── Checkbox.md                  # Checkbox 复选框组件用法详解
├── Radio.md                     # Radio 单选框组件用法详解
├── Switch.md                    # Switch 开关组件用法详解
├── Slider.md                    # Slider 滑动选择器组件用法详解
├── Menu.md                      # Menu 现代菜单组件 (M3)
├── DropdownButton.md            # DropdownButton 下拉菜单组件
├── DatePicker.md                # DatePicker 日期选择器组件
├── TimePicker.md                # TimePicker 时间选择器组件 (M3)
│
├── # 反馈组件
├── SnackBar.md                  # SnackBar 消息提示组件用法详解
├── Progress.md                  # Progress 进度条组件用法详解
├── Badge.md                     # Badge 徽章组件用法详解
├── Tooltip.md                   # Tooltip 提示组件
├── Dialog.md                    # Dialog 对话框组件用法详解
├── AlertDialog.md               # AlertDialog 警告对话框组件
├── BottomSheet.md               # BottomSheet 底部弹出组件用法详解
│
├── # 展示组件
├── Chip.md                      # Chip 标签组件用法详解
├── PopupMenu.md                 # PopupMenu 弹出菜单组件用法详解
├── SearchBar.md                 # SearchBar 搜索栏组件 (M3)
│
└── # Material 3 指南
    └── Flutter_Material3_组件指南.md  # Material 3 组件概览
```

## 文档说明

### 基础容器组件

#### 1. Container.md
Flutter 容器组件完全教程，包含：
- 基本概念与布局工作原理
- 常用属性：padding、margin、color、width/height、alignment、constraints、decoration、transform、clipBehavior
- 基础示例与视觉效果演示
- 常见错误与解决方案
- 实战练习与性能优化建议

#### 2. SizedBox.md
Flutter 尺寸盒子组件用法详解，包含：
- 初级用法：基本概念、设置尺寸、创建间距
- 中级用法：常用构造函数（expand、shrink、square）、响应式尺寸
- 高级用法：动画、条件渲染占位、自定义间距组件
- 性能优化与最佳实践

#### 3. Card.md
Flutter Material 3 卡片组件用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：Material 3 三种变体（默认、outlined、filled）、自定义样式
- 高级用法：产品卡片、文章卡片、统计卡片、可展开卡片、卡片网格
- 性能优化与最佳实践

#### 4. Divider.md
Flutter 分割线组件用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：列表分割线、带缩进分割线、VerticalDivider、分组标题
- 高级用法：自定义分割线、带文字分割线、渐变分割线、虚线分割线
- ListView.separated 配合

#### 5. SafeArea.md
Flutter 安全区域容器组件用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：部分方向安全区域、最小边距、横屏处理
- 高级用法：全屏背景配合、沉浸式状态栏、响应式安全区域、动态处理
- 与 MediaQuery 对比、与其他组件配合

### 布局组件

#### 6. Row.md
Flutter 水平布局组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：溢出问题解决、对齐控制、Flexible/Expanded 区别
- 高级用法：自定义布局权重、动态布局、响应式设计、性能优化

#### 7. Column.md
Flutter 垂直布局组件完整教程，包含：
- 初级用法：基本概念、主要属性（mainAxisAlignment、crossAxisAlignment、mainAxisSize 等）
- 中级用法：复杂布局组合、与其他组件配合（Expanded、Flexible、ListView 等）
- 高级用法：自定义布局组件、动态内容、响应式设计、企业级最佳实践

#### 8. Stack.md
Flutter 堆叠布局组件用法详解，包含：
- 初级用法：基本概念、主要属性（alignment、fit、clipBehavior）
- 中级用法：alignment 对齐、Positioned 定位、fit 属性、clipBehavior
- 高级用法：IndexedStack、动态定位、层叠动画、组合布局、水印效果
- 性能优化与常见问题解决方案

#### 9. Expanded.md
Flutter 填充剩余空间组件用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：Column 中使用、固定宽度按钮配合、底部固定布局
- 高级用法：复杂比例布局、响应式布局、嵌套 Expanded
- 与 Flexible 的对比说明

#### 10. Flexible.md
Flutter 弹性布局组件用法详解，包含：
- 初级用法：基本概念、FlexFit 枚举说明
- 中级用法：与 Expanded 对比、按比例分配、混合布局
- 高级用法：动态 flex、嵌套 Flexible、响应式布局、条件性使用
- 性能优化与最佳实践

#### 11. Wrap.md
Flutter 流式布局组件用法详解，包含：
- 初级用法：基本概念、主要属性、标签云示例
- 中级用法：direction、alignment、runAlignment、crossAxisAlignment
- 高级用法：可选择标签、可删除标签、输入标签、图片瀑布流
- 性能优化与最佳实践

#### 12. GridView.md
Flutter 网格布局组件用法详解，包含：
- 初级用法：基本概念、四种构造函数、SliverGridDelegate
- 中级用法：childAspectRatio、滚动控制、响应式列数、下拉刷新
- 高级用法：自定义 delegate、瀑布流、CustomScrollView 配合、上拉加载
- 性能优化与常见问题

### 页面结构组件

#### 13. Scaffold.md
Flutter Material Design 页面骨架用法详解，包含：
- 初级用法：基本概念、主要属性、基础示例
- 中级用法：AppBar 配置、FloatingActionButton、Drawer、BottomNavigationBar、SnackBar
- 高级用法：TabBar + TabBarView、BottomSheet、嵌套 Scaffold、键盘处理、Material 3 NavigationBar
- Scaffold 状态管理

#### 14. AppBar.md
Flutter 应用栏组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：操作按钮、底部 TabBar、自定义标题
- 高级用法：SliverAppBar、自定义高度、透明渐变
- Material 3 AppBar 特性

#### 15. TabBar.md
Flutter 标签页组件用法详解，包含：
- 初级用法：DefaultTabController、TabBar、TabBarView
- 中级用法：TabController、自定义样式、指示器
- 高级用法：动态标签、滑动切换、嵌套 TabBar
- 与 AppBar 配合使用

#### 16. Navigation.md
Flutter 导航组件用法详解，包含：
- NavigationBar：Material 3 底部导航栏
- NavigationRail：Material 3 侧边导航栏
- NavigationDrawer：Material 3 抽屉导航
- 响应式导航切换

#### 17. BottomNavigationBar.md
Flutter 底部导航栏组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：自定义样式、徽章、动画
- 高级用法：与 PageView 配合、状态管理

#### 18. Drawer.md
Flutter 侧边抽屉组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：自定义内容、用户信息、分组菜单
- 高级用法：右滑手势、嵌套导航

### 滚动组件

#### 19. ListView.md
Flutter 滚动列表组件用法详解，包含：
- 初级用法：基本概念、四种构造函数
- 中级用法：滚动控制、滚动物理特性、shrinkWrap、itemExtent
- 高级用法：下拉刷新/上拉加载、吸顶效果、滑动删除、拖拽排序、分组列表
- 性能优化：构造函数选择、itemExtent、嵌套优化
- 常见问题与解决方案

#### 20. ListTile.md
Flutter 列表项组件用法详解，包含：
- 初级用法：基本语法、主要属性（leading、title、subtitle、trailing）
- 中级用法：选中状态、紧凑模式、自定义背景色、禁用状态
- 特殊变体：CheckboxListTile、RadioListTile、SwitchListTile、AboutListTile
- 高级用法：可滑动列表项、可展开列表项、带头像的列表项
- 常见问题与解决方案

#### 21. DataTable.md
Flutter 数据表格组件用法详解，包含：
- 初级用法：DataColumn、DataRow、DataCell
- 中级用法：排序功能、行选择、自定义样式
- 高级用法：分页表格、PaginatedDataTable、可编辑单元格
- 性能优化与常见问题

### 文本组件

#### 22. Text.md
Flutter 文本显示组件用法详解，包含：
- 初级用法：基本语法、TextStyle 样式
- 中级用法：文本对齐、溢出处理、行数限制
- 高级用法：富文本 TextSpan、自定义字体、文本选择
- 国际化与语义化

#### 23. RichText.md
Flutter 富文本组件用法详解，包含：
- 初级用法：TextSpan 基本语法、多样式文本
- 中级用法：可点击文本、文本装饰、混合图标
- 高级用法：富文本构建器、文本高亮、标签解析
- WidgetSpan 嵌入组件

#### 24. SelectableText.md
Flutter 可选择文本组件用法详解，包含：
- 初级用法：基本语法、文本选择
- 中级用法：监听选择变化、自定义光标样式
- 高级用法：SelectableText.rich 富文本选择、复制成功提示
- 与 Text 的对比

#### 25. TextField.md
Flutter 文本输入组件用法详解，包含：
- 初级用法：基本语法、InputDecoration 装饰
- 中级用法：输入验证、格式化、键盘类型
- 高级用法：自定义装饰、表单集成、搜索框
- 常见问题与解决方案

### 图像组件

#### 26. Image.md
Flutter 图片组件用法详解，包含：
- 初级用法：网络图片、资产图片、本地文件图片
- 中级用法：填充模式 fit、占位符、错误处理
- 高级用法：图片缓存、圆形图片、图片选择器
- 性能优化

#### 27. Icon.md
Flutter 图标组件用法详解，包含：
- 初级用法：基本语法、大小与颜色
- Material Icons 常用图标分类
- Cupertino Icons iOS 风格图标
- 中级用法：图标阴影、圆形背景图标
- 高级用法：自定义图标字体、渐变图标、动画图标
- 图标查找资源

#### 28. CircleAvatar.md
Flutter 圆形头像组件用法详解，包含：
- 初级用法：文字头像、图标头像、图片头像
- 中级用法：控制大小、带边框头像、状态指示器
- 高级用法：首字母头像生成器、头像组、点击放大
- 常见问题与解决方案

### 按钮组件

#### 29. Button.md
Flutter 按钮组件用法详解，包含：
- 初级用法：5 种按钮类型对比（FilledButton、ElevatedButton、OutlinedButton、TextButton、IconButton）
- 中级用法：按钮样式、图标按钮、按钮组
- 高级用法：自定义按钮、加载状态、按钮主题
- Material 3 按钮设计规范

#### 30. SegmentedButton.md
Flutter 分段按钮组件用法详解（Material 3），包含：
- 初级用法：基本语法、单选与多选
- 中级用法：带图标、禁用选项、自定义样式
- 高级用法：全宽分段按钮、时间选择器、配合筛选
- 与 ToggleButtons 对比

### 表单组件

#### 31. Checkbox.md
Flutter 复选框组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：三态模式、CheckboxListTile
- 高级用法：自定义样式、多选列表
- 常见问题与解决方案

#### 32. Radio.md
Flutter 单选框组件用法详解，包含：
- 初级用法：基本语法、单选组
- 中级用法：RadioListTile、自定义颜色、禁用状态
- 高级用法：自定义单选组件、卡片式单选、分组单选
- 与 Checkbox 对比

#### 33. Switch.md
Flutter 开关组件用法详解，包含：
- 初级用法：基本语法、自定义颜色
- 中级用法：SwitchListTile、禁用状态
- 高级用法：自定义开关组件、确认开关、iOS 风格开关
- 与 Checkbox 对比

#### 34. Slider.md
Flutter 滑动选择器组件用法详解，包含：
- Slider：单值滑动条
- RangeSlider：范围滑动条
- 分区显示与标签
- 自定义样式

#### 35. Menu.md
Flutter 现代菜单组件用法详解（Material 3），包含：
- 初级用法：MenuAnchor、MenuItemButton
- 中级用法：分割线、子菜单、禁用菜单项、单选菜单
- 高级用法：自定义样式、右键上下文菜单、多级菜单
- 与 DropdownButton 对比

#### 36. DropdownButton.md
Flutter 下拉菜单组件用法详解，包含：
- 初级用法：基本语法、DropdownMenuItem
- 中级用法：自定义样式、禁用选项、全宽下拉
- 高级用法：带图标选项、分组下拉、带搜索下拉
- DropdownButtonFormField 表单验证

#### 37. DatePicker.md
Flutter 日期选择器组件用法详解，包含：
- 初级用法：showDatePicker 基本用法
- 中级用法：日期范围选择、自定义样式
- 高级用法：Material 3 新设计、日历选择器
- 常见问题与解决方案

#### 38. TimePicker.md
Flutter 时间选择器组件用法详解（Material 3），包含：
- 初级用法：showTimePicker 基本用法
- 中级用法：输入模式、自定义按钮文字、24小时制
- 高级用法：时间选择按钮、时间范围选择、闹钟时间列表
- TimePickerThemeData 样式定制

### 反馈组件

#### 39. SnackBar.md
Flutter 消息提示组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：操作按钮、自定义样式、显示行为
- 高级用法：全局 SnackBar、队列管理
- 与 ScaffoldMessenger 配合

#### 40. Progress.md
Flutter 进度条组件用法详解，包含：
- CircularProgressIndicator：圆形进度条
- LinearProgressIndicator：线性进度条
- 确定进度与不确定进度模式
- 自定义样式与颜色

#### 41. Badge.md
Flutter 徽章组件用法详解，包含：
- 初级用法：小圆点徽章、数字徽章
- 中级用法：自定义样式、位置偏移
- 高级用法：动画效果、自定义内容
- 与图标、导航栏配合

#### 42. Tooltip.md
Flutter 提示组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：自定义样式、位置控制
- 高级用法：富文本提示、延迟显示

#### 43. Dialog.md
Flutter 对话框组件用法详解，包含：
- 初级用法：AlertDialog、SimpleDialog
- 中级用法：自定义对话框、返回值传递
- 高级用法：底部弹窗、全屏对话框、表单对话框
- Cupertino 风格对话框

#### 44. AlertDialog.md
Flutter 警告对话框组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：获取返回值、自定义样式、可滚动内容
- 高级用法：表单对话框、单选/多选对话框、加载对话框、确认退出
- showDialog 参数详解

#### 45. BottomSheet.md
Flutter 底部弹出组件用法详解，包含：
- showModalBottomSheet：模态底部弹出
- showBottomSheet：持久底部弹出
- 自定义高度和形状
- 拖拽关闭与手势处理

### 展示组件

#### 46. Chip.md
Flutter 标签组件用法详解，包含：
- Chip：基础标签
- InputChip：输入标签
- ChoiceChip：选择标签
- FilterChip：筛选标签
- ActionChip：操作标签

#### 47. PopupMenu.md
Flutter 弹出菜单组件用法详解，包含：
- PopupMenuButton：Material 2 弹出菜单
- MenuAnchor/MenuItemButton：Material 3 菜单
- 自定义菜单项
- 菜单位置与样式

#### 48. SearchBar.md
Flutter 搜索栏组件用法详解（Material 3），包含：
- 初级用法：基本语法、主要属性
- 中级用法：带清除按钮、自定义样式、带语音搜索
- SearchAnchor 搜索锚点：搜索建议、搜索历史
- 高级用法：AppBar 搜索、全屏搜索、搜索结果页面

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

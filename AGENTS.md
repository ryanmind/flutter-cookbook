# Flutter 学习文档项目

## 项目概述

这是一个 Flutter 框架学习文档集合，主要涵盖 Flutter 布局组件和 Material 3 设计系统的学习指南。文档内容从初级到高级，包含大量代码示例和最佳实践。

## 目录结构

```
dart/
├── AGENTS.md                    # 本文件
├── Column.md                    # Column 组件完整用法指南
├── Container.md                 # Container 组件完全教程
├── Row.md                       # Row 组件用法详解
├── SizedBox.md                  # SizedBox 组件用法详解
├── ListView.md                  # ListView 组件用法详解
└── Flutter_Material3_组件指南.md  # Material 3 组件学习指南
```

## 文档说明

### 1. Column.md
Flutter 垂直布局组件完整教程，包含：
- 初级用法：基本概念、主要属性（mainAxisAlignment、crossAxisAlignment、mainAxisSize 等）
- 中级用法：复杂布局组合、与其他组件配合（Expanded、Flexible、ListView 等）
- 高级用法：自定义布局组件、动态内容、响应式设计、企业级最佳实践
- 共 2123 行，内容详尽

### 2. Container.md
Flutter 容器组件完全教程，包含：
- 基本概念与布局工作原理
- 常用属性：padding、margin、color、width/height、alignment、constraints、decoration、transform、clipBehavior
- 基础示例与视觉效果演示
- 常见错误与解决方案
- 实战练习与性能优化建议
- 共 1356 行

### 3. Row.md
Flutter 水平布局组件用法详解，包含：
- 初级用法：基本语法、主要属性
- 中级用法：溢出问题解决、对齐控制、Flexible/Expanded 区别
- 高级用法：自定义布局权重、动态布局、响应式设计、性能优化
- 共 434 行

### 4. SizedBox.md
Flutter 尺寸盒子组件用法详解，包含：
- 初级用法：基本概念、设置尺寸、创建间距
- 中级用法：常用构造函数（expand、shrink、square）、响应式尺寸
- 高级用法：动画、条件渲染占位、自定义间距组件
- 性能优化与最佳实践
- 共 651 行

### 5. ListView.md
Flutter 滚动列表组件用法详解，包含：
- 初级用法：基本概念、四种构造函数
- 中级用法：滚动控制、滚动物理特性、shrinkWrap、itemExtent
- 高级用法：下拉刷新/上拉加载、吸顶效果、滑动删除、拖拽排序、分组列表
- 性能优化：构造函数选择、itemExtent、嵌套优化
- 常见问题与解决方案
- 共 1008 行

### 6. Flutter_Material3_组件指南.md
Material 3 组件学习指南，包含：
- 常用组件分类列表（10 大类）
- Material 3 设计风格特点
- 配色系统说明
- 组件使用要点
- 新手学习建议与资源推荐

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

1. **基础布局** → SizedBox.md → Container.md → Row.md → Column.md
2. **滚动组件** → ListView.md
3. **组件系统** → Flutter_Material3_组件指南.md
4. **实战练习** → 结合文档中的示例项目

## Git 提交规范

- `[Docs]feat:` - 新增文档内容
- `[Docs]fix:` - 修正文档错误
- `[Docs]update:` - 更新文档信息

示例：
```
[Docs]fix: 修正 Row 和 Container 文档中的技术描述
[Docs]feat: 新增 SizedBox 和 ListView 组件学习文档
```
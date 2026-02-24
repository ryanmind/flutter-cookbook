# Deep-Learning TODO

本目录用于记录 Flutter 组件与 UIKit/Objective-C 的对比学习文档。

---

## 已完成

| 文档 | 状态 | 说明 |
|------|------|------|
| `SizedBox_vs_UIKit.md` | ✅ 完成 | 构造函数对比、约束传递、性能分析 |
| `Container_vs_UIKit.md` | ✅ 完成 | 源码分析、属性映射、陷阱与技巧 |
| `DevTools.md` | ✅ 完成 | 安装启动、功能详解、调试技巧 |
| `Row_vs_AutoLayout.md` | ✅ 完成 | UIStackView 水平布局对比 |
| `Column_vs_AutoLayout.md` | ✅ 完成 | UIStackView 垂直布局对比 |
| `Stack_vs_AutoLayout.md` | ✅ 完成 | UIView 层级布局对比 |
| `Expanded_vs_AutoLayout.md` | ✅ 完成 | 填充约束对比 |
| `Align_vs_AutoLayout.md` | ✅ 完成 | centerAnchors 对齐对比 |
| `Center_vs_AutoLayout.md` | ✅ 完成 | 居中约束对比 |
| `Padding_vs_LayoutMargins.md` | ✅ 完成 | layoutMargins 内边距对比 |
| `ListView_vs_UITableView.md` | ✅ 完成 | 列表对比 |
| `SingleChildScrollView_vs_UIScrollView.md` | ✅ 完成 | 滚动视图对比 |
| `GestureDetector_vs_UIGestureRecognizer.md` | ✅ 完成 | 手势检测对比 |
| `Button_vs_UIButton.md` | ✅ 完成 | 按钮对比 |
| `TextField_vs_UITextField.md` | ✅ 完成 | 输入框对比 |
| `Navigation_vs_UINavigationController.md` | ✅ 完成 | 导航对比 |
| `TabBar_vs_UITabBarController.md` | ✅ 完成 | Tab 导航对比 |
| `Image_vs_UIImageView.md` | ✅ 完成 | 图片对比 |
| `Text_vs_UILabel.md` | ✅ 完成 | 文本对比 |
| `Dialog_vs_UIAlertController.md` | ✅ 完成 | 对话框对比 |
| `Flexible_vs_AutoLayout.md` | ✅ 完成 | 比例约束对比 |
| `Wrap_vs_UICollectionView.md` | ✅ 完成 | 流式布局对比 |
| `GridView_vs_UICollectionView.md` | ✅ 完成 | 网格布局对比 |
| `Positioned_vs_AutoLayout.md` | ✅ 完成 | 绝对定位对比 |
| `CustomScrollView_vs_UIScrollView.md` | ✅ 完成 | 自定义滚动对比 |
| `PageView_vs_UIPageViewController.md` | ✅ 完成 | 分页对比 |
| `Switch_vs_UISwitch.md` | ✅ 完成 | 开关对比 |
| `Slider_vs_UISlider.md` | ✅ 完成 | 滑块对比 |
| `Drawer_vs_UISplitViewController.md` | ✅ 完成 | 侧边栏对比 |
| `Icon_vs_SFSymbols.md` | ✅ 完成 | 图标对比 |
| `SnackBar_vs_Toast.md` | ✅ 完成 | 消息提示对比 |
| `BottomSheet_vs_UISheetPresentationController.md` | ✅ 完成 | 底部弹出对比 |
| `AnimatedContainer_vs_UIViewAnimation.md` | ✅ 完成 | 容器动画对比 |
| `Hero_vs_CustomTransition.md` | ✅ 完成 | 共享元素过渡对比 |

---

| `Animation_vs_CABasicAnimation.md` | ✅ 完成 | 核心动画对比 |
| `Card_vs_UICollectionViewCell.md` | ✅ 完成 | 卡片对比 |
| `AutoLayout_vs_Masonry.md` | ✅ 完成 | 链式语法对比 |
| `GLM5_GUIDE.md` | ✅ 完成 | iOS 开发者 Flutter 学习方案 |

---

## 全部完成 🎉

所有计划文档已完成，共计 **37 个**对比学习文档。

---

## 文档模板

```markdown
# [Component] vs UIKit 对比学习

本文档记录 Flutter [Component] 组件与 UIKit/Objective-C [UIKit组件] 的对比学习要点。

---

## 1. [Component] 核心概念

### [Component] 是什么

[简要描述组件功能]

### [Component] vs [UIKit组件] 概念对比

| Flutter [Component] | UIKit [UIKit组件] | 说明 |
|--------------------|------------------|------|
| `属性1` | `对应属性` | 说明 |
| `属性2` | `对应属性` | 说明 |

---

## 2. 源码分析

[源码层面的差异分析]

---

## 3. 与 [UIKit组件] 详细对比

### 3.1 [场景1]

```objc
// Objective-C
[代码示例]
```

```dart
// Flutter
[代码示例]
```

### 3.2 [场景2]
...

---

## 4. 完整示例对比

### UIKit 实现

```objc
[完整代码示例]
```

### Flutter 实现

```dart
[完整代码示例]
```

---

## 5. 常见陷阱

### 陷阱1：[问题]

```dart
// ❌ 错误写法
[代码]

// ✅ 正确写法
[代码]
```

---

## 6. 学习技巧

[调试技巧、最佳实践等]

---

## 7. 核心理念总结

### UIKit 思维 → Flutter 思维

```
[UIKit 概念] → [Flutter 概念]
```

---

*最后更新: YYYY-MM-DD*
```

---

## 优先级说明

- **高**：常用组件，学习优先级最高
- **中**：较常用组件，进阶学习
- **低**：高级/特殊场景组件

---

*最后更新: 2026-02-24*

# Flutter 学习方案 (iOS 开发者的视角)

本方案专为有 iOS/Objective-C 开发背景的开发者设计，通过对比学习的方式更快理解 Flutter。

---

## 核心学习理念

### 对比学习 + 动手实践 + 先广后深

| 原则 | 说明 |
|------|------|
| 对比学习 | 利用已有的 UIKit 知识理解 Flutter |
| 动手实践 | 光看不练假把式，必须写代码 |
| 先广后深 | 先会用再优化，不求一步到位 |

---

## 学习路线图

### 第 1 阶段：思维转换 (Week 1-2)

**目标**：理解 Flutter 基本概念，建立声明式开发思维

| 顺序 | 主题 | 对比 iOS | 文档位置 |
|------|------|----------|----------|
| 1 | Widget 声明式 vs UIKit 命令式 | UIView vs StatelessWidget/StatefulWidget | [Container_vs_UIKit.md](./Container_vs_UIKit.md) |
| 2 | 布局约束系统 | Auto Layout vs Row/Column/Stack | [Row_vs_AutoLayout.md](./Row_vs_AutoLayout.md)<br>[Column_vs_AutoLayout.md](./Column_vs_AutoLayout.md)<br>[Stack_vs_AutoLayout.md](./Stack_vs_AutoLayout.md) |
| 3 | 弹性布局 | UIStackView.axis vs Row/Column | [Flexible_vs_AutoLayout.md](./Flexible_vs_AutoLayout.md)<br>[Expanded_vs_AutoLayout.md](./Expanded_vs_AutoLayout.md) |
| 4 | 定位系统 | frame/bounds vs Positioned | [Positioned_vs_AutoLayout.md](./Positioned_vs_AutoLayout.md)<br>[Center_vs_AutoLayout.md](./Center_vs_AutoLayout.md) |

**每日任务**：
- 阅读 1-2 篇对比文档
- 抄写示例代码运行
- 尝试修改示例

---

### 第 2 阶段：基础组件 (Week 3-4)

**目标**：掌握 Flutter 常用基础组件

| 顺序 | 主题 | 对比 iOS | 文档位置 |
|------|------|----------|----------|
| 1 | 文本 | UILabel vs Text | [Text_vs_UILabel.md](./Text_vs_UILabel.md) |
| 2 | 图片 | UIImageView vs Image | [Image_vs_UIImageView.md](./Image_vs_UIImageView.md) |
| 3 | 按钮 | UIButton vs Button | [Button_vs_UIButton.md](./Button_vs_UIButton.md) |
| 4 | 输入框 | UITextField vs TextField | [TextField_vs_UITextField.md](./TextField_vs_UITextField.md) |
| 5 | 图标 | SFSymbols vs Icon | [Icon_vs_SFSymbols.md](./Icon_vs_SFSymbols.md) |

**关键概念**：
- `Container` = `UIView` + α (装饰、变换)
- `SizedBox` = 设置具体尺寸
- `Padding/EdgeInsets` = contentInset

---

### 第 3 阶段：导航与路由 (Week 5)

**目标**：理解 Flutter 导航系统

| 顺序 | 主题 | 对比 iOS | 文档位置 |
|------|------|----------|----------|
| 1 | 页面导航 | UINavigationController vs Navigator | [Navigation_vs_UINavigationController.md](./Navigation_vs_UINavigationController.md) |
| 2 | Tab 导航 | UITabBarController vs TabBar | [TabBar_vs_UITabBarController.md](./TabBar_vs_UITabBarController.md) |
| 3 | 侧边栏 | UISplitViewController vs Drawer | [Drawer_vs_UISplitViewController.md](./Drawer_vs_UISplitViewController.md) |

**关键概念**：
- 路由堆栈 vs Navigation Stack
- 页面传值

---

### 第 4 阶段：滚动与列表 (Week 6)

**目标**：掌握 Flutter 列表渲染

| 顺序 | 主题 | 对比 iOS | 文档位置 |
|------|------|----------|----------|
| 1 | 列表 | UITableView vs ListView | [ListView_vs_UITableView.md](./ListView_vs_UITableView.md) |
| 2 | 网格 | UICollectionView vs GridView | [GridView_vs_UICollectionView.md](./GridView_vs_UICollectionView.md) |
| 3 | 流式布局 | UICollectionView vs Wrap | [Wrap_vs_UICollectionView.md](./Wrap_vs_UICollectionView.md) |
| 4 | 自定义滚动 | UIScrollView vs CustomScrollView | [CustomScrollView_vs_UIScrollView.md](./CustomScrollView_vs_UIScrollView.md) |

**关键概念**：
- `ListView.builder` = cellForRowAt + dataSource
- Sliver = 更灵活的列表组合

---

### 第 5 阶段：状态管理 (Week 7-8)

**目标**：理解 Flutter 状态管理

| 主题 | 说明 |
|------|------|
| setState | 最基础的状态管理 |
| Provider | 官方推荐的状态管理 |
| Riverpod | Provider 的进化版 |
| Bloc | 事件驱动的状态管理 |

**学习路径**：
1. 先会用 `setState` (Widget 内部状态)
2. 再学 `Provider` (跨组件状态共享)
3. 进阶 `Riverpod` 或 `Bloc`

---

### 第 6 阶段：动画 (Week 9)

**目标**：掌握 Flutter 动画系统

| 顺序 | 主题 | 对比 iOS | 文档位置 |
|------|------|----------|----------|
| 1 | 容器动画 | UIView.animate vs AnimatedContainer | [AnimatedContainer_vs_UIViewAnimation.md](./AnimatedContainer_vs_UIViewAnimation.md) |
| 2 | 共享元素 | Custom Transition vs Hero | [Hero_vs_CustomTransition.md](./Hero_vs_CustomTransition.md) |
| 3 | 核心动画 | CABasicAnimation vs AnimationController | [Animation_vs_CABasicAnimation.md](./Animation_vs_CABasicAnimation.md) |

---

### 第 7 阶段：进阶组件 (Week 10+)

**目标**：掌握更多高级组件

| 顺序 | 主题 | 对比 iOS | 文档位置 |
|------|------|----------|----------|
| 1 | 对话框 | UIAlertController vs Dialog | [Dialog_vs_UIAlertController.md](./Dialog_vs_UIAlertController.md) |
| 2 | 底部弹出 | UISheetPresentationController vs BottomSheet | [BottomSheet_vs_UISheetPresentationController.md](./BottomSheet_vs_UISheetPresentationController.md) |
| 3 | 开关 | UISwitch vs Switch | [Switch_vs_UISwitch.md](./Switch_vs_UISwitch.md) |
| 4 | 滑块 | UISlider vs Slider | [Slider_vs_UISlider.md](./Slider_vs_UISlider.md) |
| 5 | 手势 | UIGestureRecognizer vs GestureDetector | [GestureDetector_vs_UIGestureRecognizer.md](./GestureDetector_vs_UIGestureRecognizer.md) |
| 6 | 分页 | UIPageViewController vs PageView | [PageView_vs_UIPageViewController.md](./PageView_vs_UIPageViewController.md) |

---

## 快速参考：概念对照表

### 布局系统

| Flutter | UIKit | 说明 |
|---------|-------|------|
| `Row` | UIStackView (horizontal) | 水平布局 |
| `Column` | UIStackView (vertical) | 垂直布局 |
| `Stack` | UIView 层级 | 绝对定位堆叠 |
| `Expanded` | fillEqaul | 填充剩余空间 |
| `Flexible` | proportional | 弹性比例 |
| `Align` | center/alignment | 对齐方式 |
| `Padding` | contentInset | 内边距 |

### 组件

| Flutter | UIKit | 说明 |
|---------|-------|------|
| `Container` | UIView | 通用容器 |
| `Text` | UILabel | 文本 |
| `Image` | UIImageView | 图片 |
| `Icon` | UIImage (SF Symbols) | 图标 |
| `Button` | UIButton | 按钮 |
| `TextField` | UITextField | 输入框 |
| `ListView` | UITableView | 列表 |
| `GridView` | UICollectionView | 网格 |

### 概念

| Flutter | UIKit | 说明 |
|---------|-------|------|
| Widget | UIView | 界面构建块 |
| BuildContext | - | Widget 树上下文 |
| State | @State / @Published | 状态管理 |
| setState | - | 触发重建 |
| Navigator | UINavigationController | 导航 |
| Controller | Delegate/DataSource | 控制器模式 |

---

## 推荐资源

### 官方文档
- [Flutter 官方文档](https://docs.flutter.dev)
- [Flutter Widget 目录](https://docs.flutter.dev/reference/widgets)
- [Flutter Codelabs](https://docs.flutter.dev/codelabs)

### 学习资源
- [Flutter Widget of the Week](https://www.youtube.com/playlist?list=PLjxrf2q8roU23XGwz3Km7sQZFTdpp96zK) - 每周学一个组件
- [Flutter 官方示例](https://github.com/flutter/samples)

### 调试工具
- [DevTools](./DevTools.md) - Flutter 调试工具详解

---

## 学习技巧

### 1. 先跑通再理解

```
第1遍：复制代码运行成功
第2遍：修改代码看效果
第3遍：理解原理
```

### 2. 善用 DevTools

- Widget Inspector: 查看 Widget 树
- Timeline: 分析性能
- Debugger: 断点调试

### 3. 遇到问题先自查

| 问题 | 解决 |
|------|------|
| 界面不对 | 用 DevTools 看 Widget 树 |
| 状态不更新 | 检查 setState 调用 |
| 性能差 | 用 Timeline 看帧率 |

---

## 四周计划示例

### Week 1：基础概念
- [ ] 理解 Widget 声明式
- [ ] 掌握 Row/Column/Stack
- [ ] 理解 Container 用法
- [ ] 运行第一个 Flutter 项目

### Week 2：基础组件
- [ ] 掌握 Text/Image/Icon
- [ ] 掌握 Button/TextField
- [ ] 理解 Padding/Align/Center

### Week 3：导航与列表
- [ ] 理解 Navigator 路由
- [ ] 掌握 ListView
- [ ] 掌握 GridView/Wrap

### Week 4：状态与进阶
- [ ] 理解 setState
- [ ] 入门 Provider
- [ ] 掌握 Dialog/BottomSheet

---

## 总结

Flutter 学习曲线：
- **入门快** (1-2周)：基本组件和布局
- **进阶慢** (3-8周)：状态管理、动画、性能优化
- **深水区** (8周+)：RenderObject、Platform Channel、自定义引擎

**核心建议**：不要追求一步到位，先能用再优化。用 iOS 对比学习是很好的起点，但也要跳出 UIKit 思维，真正理解 Flutter 的设计理念。

---

*最后更新: 2026-02-24*

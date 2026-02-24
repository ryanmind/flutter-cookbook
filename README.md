# Flutter Cookbook 🍳

Flutter 学习文档集合，涵盖组件、语法、进阶教程等内容。专为有 iOS/Objective-C 开发背景的开发者提供对比学习资料。

## 📚 目录结构

```
flutter-cookbook/
├── syntax/              # Dart 语法学习
│   ├── high/            # 高优先级（基础核心）
│   ├── medium/          # 中优先级（进阶特性）
│   └── low/             # 低优先级（高级特性）
│
├── widgets/             # Flutter 组件学习
│   ├── Animation/       # 动画组件
│   ├── Base/            # 基础组件
│   ├── Material3/       # Material 3 组件
│   └── Sliver/          # Sliver 组件
│
├── syntax-vs-ObjC/      # Dart 语法 vs Objective-C 对比
│
├── widgets-vs-UIKit/    # Flutter 组件 vs UIKit 对比
│
├── advanced/            # 进阶教程
│   └── projects/        # 实战项目
│
├── cicd/                # CI/CD 自动化
├── patterns/            # 设计模式
├── security/            # 安全
└── packages/            # 第三方库
```

## 📖 内容概览

### Syntax 语法文档

| 优先级 | 内容 |
|--------|------|
| 高 | Dart 基础、OOP、异步编程、状态管理、错误处理、调试、项目结构 |
| 中 | 泛型、网络请求、存储、主题、GoRouter、Riverpod、Isolate、平台通道、响应式设计、Flutter Web |
| 低 | 动画、手势、生命周期、性能优化、国际化、测试、Bloc、RenderObject、插件开发、无障碍、桌面端 |

### Widgets 组件文档

| 分类 | 数量 |
|------|------|
| Animation 动画组件 | 8 |
| Base 基础组件 | 41 |
| Material3 组件 | 49 |
| Sliver 组件 | 5 |

### Syntax vs ObjC 对比文档

iOS 开发者专属学习资料，对比 Dart 与 Objective-C 的差异：

| 文档 | 说明 |
|------|------|
| setState_vs_iOS.md | 命令式更新 vs 声明式重建 |
| Provider_vs_iOS.md | 单例/通知中心 vs InheritedWidget |
| Riverpod_vs_iOS.md | 编译时安全的状态管理 |
| BLoC_vs_iOS.md | MVVM vs 事件驱动架构 |

### Widgets vs UIKit 对比文档

40+ 篇对比文档，帮助 iOS 开发者快速理解 Flutter 组件：

| 分类 | 示例文档 |
|------|----------|
| 布局系统 | Row_vs_AutoLayout、Column_vs_AutoLayout、Stack_vs_AutoLayout |
| 基础组件 | Text_vs_UILabel、Button_vs_UIButton、TextField_vs_UITextField |
| 导航系统 | Navigation_vs_UINavigationController、TabBar_vs_UITabBarController |
| 列表滚动 | ListView_vs_UITableView、GridView_vs_UICollectionView |
| 动画系统 | AnimatedContainer_vs_UIViewAnimation、Hero_vs_CustomTransition |

### 进阶教程

- **电商应用实战** - 完整项目流程：认证、商品、购物车
- **社交应用实战** - 实时消息、动态发布、用户交互

### 其他

- **CI/CD** - GitHub Actions 自动化配置
- **设计模式** - 创建型、结构型、行为型模式
- **安全** - 敏感数据保护、API 安全、代码安全
- **第三方库** - 网络、状态管理、存储、图片等常用库

## 🚀 学习路径

### 初学者

1. **Dart 基础** → `syntax/high/Dart_Basics.md`
2. **Dart OOP** → `syntax/high/Dart_OOP.md`
3. **布局基础** → `widgets/Material3/SizedBox.md` → `Container.md` → `Row.md` → `Column.md`
4. **页面结构** → `widgets/Material3/Scaffold.md` → `AppBar.md`
5. **状态管理入门** → `syntax/high/StateManagement.md` → `Provider.md`

### iOS 开发者（推荐）

1. **思维转换** → `widgets-vs-UIKit/GLM5_GUIDE.md`
2. **布局对比** → `widgets-vs-UIKit/Row_vs_AutoLayout.md`
3. **组件对比** → `widgets-vs-UIKit/Text_vs_UILabel.md`
4. **状态管理** → `syntax-vs-ObjC/setState_vs_iOS.md` → `Provider_vs_iOS.md`

### 进阶开发者

1. **异步编程** → `syntax/high/Dart_Async.md`
2. **网络请求** → `syntax/medium/Networking.md`
3. **数据存储** → `syntax/medium/Storage.md`
4. **路由管理** → `syntax/medium/GoRouter.md`
5. **响应式设计** → `syntax/medium/ResponsiveDesign.md`

### 高级开发者

1. **性能优化** → `syntax/low/Performance.md`
2. **RenderObject** → `syntax/low/RenderObject.md`
3. **插件开发** → `syntax/low/PluginDevelopment.md`
4. **实战项目** → `advanced/projects/`

## 📊 文档统计

| 分类 | 数量 |
|------|------|
| Syntax 语法 | 30 |
| Widgets 组件 | 103 |
| Syntax vs ObjC | 4 |
| Widgets vs UIKit | 37 |
| Advanced 进阶 | 2 |
| CI/CD | 1 |
| Patterns | 1 |
| Security | 1 |
| Packages | 1 |
| **总计** | **180** |

## 🔗 快速链接

- [AGENTS.md](./AGENTS.md) - 完整文档索引
- [Material 3 组件指南](./Flutter_Material3_组件指南.md)
- [iOS 开发者学习方案](./widgets-vs-UIKit/GLM5_GUIDE.md)
- [常用第三方库](./packages/ThirdPartyLibraries.md)
- [应用安全最佳实践](./security/AppSecurity.md)

---

*最后更新: 2026-02-24*
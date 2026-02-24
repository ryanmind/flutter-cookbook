# Flutter DevTools 使用指南

Flutter DevTools 是 Flutter 官方提供的调试和性能分析工具套件。

---

## 1. 安装与启动

### 无需单独安装

DevTools 已集成在 Flutter SDK 中，无需额外安装。

### 启动方式

#### 方式1：命令行启动

```bash
# 激活 DevTools（首次使用）
flutter pub global activate devtools

# 启动 DevTools 服务器
flutter pub global run devtools

# 浏览器打开 http://localhost:9100
```

#### 方式2：通过 VS Code（推荐）

1. 安装 Flutter 扩展
2. 运行 Flutter 项目
3. 点击底部状态栏的 **Dart DevTools** 按钮
4. 或命令面板：`Cmd + Shift + P` → `Dart: Open DevTools`

#### 方式3：通过 Android Studio / IntelliJ

1. 安装 Flutter 插件
2. 运行项目后，点击工具栏的 **Open DevTools** 图标

#### 方式4：运行时快捷键

```bash
flutter run

# 运行后按 v 键打开 DevTools
# 其他快捷键：
# r - Hot reload
# R - Hot restart
# h - 帮助
# q - 退出
```

---

## 2. 支持的平台

| 平台 | Widget Inspector | 性能分析 | 备注 |
|------|-----------------|---------|------|
| **iOS** (真机/模拟器) | ✅ | ✅ | 需要 debug 模式 |
| **Android** (真机/模拟器) | ✅ | ✅ | 需要 debug 模式 |
| **macOS** | ✅ | ✅ | 桌面应用调试 |
| **Windows** | ✅ | ✅ | 桌面应用调试 |
| **Linux** | ✅ | ✅ | 桌面应用调试 |
| **Web** | ✅ | ✅ | Chrome DevTools 也可用 |

---

## 3. 核心功能

### 3.1 Widget Inspector（组件检查器）

查看和调试 Widget 树结构。

**功能**：
- 查看完整的 Widget 树
- 展开 Container 查看内部 Widget 组合
- 高亮选中的组件
- 查看组件属性和约束

**使用场景**：
```dart
// 查看 Container 内部实际创建了哪些 Widget
Container(
  padding: EdgeInsets.all(16),
  decoration: BoxDecoration(color: Colors.blue),
  child: Text('Hello'),
)

// Inspector 会显示：
// Padding → DecoratedBox → Text
```

**操作技巧**：
- 点击设备上的组件 → 自动定位到代码
- 悬停查看组件尺寸和约束
- 展开 Widget 查看子树

### 3.2 Performance（性能分析）

分析应用性能和帧率。

**功能**：
- 帧率监控（FPS）
- 帧渲染时间分析
- Widget 重建次数统计
- 识别性能瓶颈

**使用场景**：
```dart
// 识别不必要的重建
// DevTools 会高亮显示频繁重建的 Widget
```

**关键指标**：
- **UI Thread** - UI 线程耗时
- **GPU Thread** - GPU 线程耗时
- **Frame Time** - 每帧耗时（目标 < 16ms）

### 3.3 Memory（内存分析）

监控内存使用情况。

**功能**：
- 内存占用趋势图
- 对象数量统计
- 内存泄漏检测
- Heap Snapshot

**使用场景**：
- 检测内存泄漏
- 分析大对象占用
- 优化内存使用

### 3.4 CPU Profiler（CPU 分析）

分析 CPU 使用和热点代码。

**功能**：
- CPU 采样分析
- 调用栈火焰图
- 方法耗时统计
- 识别性能热点

### 3.5 Network（网络分析）

监控网络请求。

**功能**：
- HTTP 请求列表
- 请求/响应详情
- 请求耗时分析
- JSON 格式化显示

### 3.6 Logging（日志）

查看应用日志。

**功能**：
- 过滤日志级别
- 搜索日志内容
- 格式化输出

---

## 4. Widget Inspector 详解

### 4.1 查看 Container 内部结构

```dart
Container(
  width: 200,
  height: 100,
  padding: EdgeInsets.all(16),
  decoration: BoxDecoration(
    color: Colors.white,
    borderRadius: BorderRadius.circular(8),
    boxShadow: [BoxShadow(blurRadius: 4)],
  ),
  child: Text('Hello'),
)
```

在 Inspector 中展开查看：

```
Container
├── padding: 16
├── decoration: BoxDecoration
│   └── DecoratedBox
│       └── Color, BorderRadius, BoxShadow
└── child: Text
```

实际 Widget 树：
```
Padding → DecoratedBox → SizedBox → Text
```

### 4.2 查看约束信息

选中 Widget 后，右侧面板显示：

```
Constraints: BoxConstraints(0.0 <= w <= 414.0, 0.0 <= h <= 896.0)
Size: Size(200.0, 100.0)
```

### 4.3 Debug Paint 模式

点击 Inspector 中的 **Debug Paint** 按钮：

- 显示组件边界线
- 显示 padding/margin 区域
- 显示对齐辅助线

```dart
// 等价于代码
debugPaintSizeEnabled = true;
```

---

## 5. 性能优化技巧

### 5.1 识别不必要的重建

在 Performance 面板中启用 **Track Widget Builds**：

- 红色高亮 = 频繁重建
- 检查是否缺少 `const`
- 检查是否需要 `shouldRebuild`

### 5.2 分析帧耗时

**Flutter FPS 目标**：60 FPS → 每帧 ≤ 16ms

```
绿色  = 正常（< 16ms）
黄色  = 警告（16-32ms）
红色  = 卡顿（> 32ms）
```

### 5.3 内存泄漏排查

1. 打开 Memory 面板
2. 操作应用
3. 观察内存是否持续增长
4. 点击 **GC** 按钮手动触发垃圾回收
5. 如果内存不下降，可能存在泄漏

---

## 6. 常用调试命令

```bash
# 查看 Flutter 环境状态
flutter doctor

# 查看已激活的全局包
flutter pub global list

# 查看 DevTools 版本
flutter pub global run devtools --version

# 运行应用并指定设备
flutter run -d iPhone
flutter run -d <device-id>

# 查看可用设备
flutter devices
```

---

## 7. 注意事项

1. **必须在 debug 模式** - release 模式不支持调试功能
2. **真机调试需要同一网络** - DevTools 通过网络连接设备
3. **首次连接较慢** - 需要下载 DevTools Web 应用
4. **热重载保持状态** - Hot reload 保留应用状态
5. **热重启重置状态** - Hot restart 完全重启应用

---

## 8. 快速参考

### VS Code 快捷键

| 快捷键 | 功能 |
|-------|------|
| `Cmd + Shift + P` → `Dart: Open DevTools` | 打开 DevTools |
| `F5` | 启动调试 |
| `Shift + F5` | 停止调试 |
| `Cmd + Shift + F5` | 热重启 |

### 命令行快捷键

| 键 | 功能 |
|----|------|
| `v` | 打开 DevTools |
| `r` | Hot reload |
| `R` | Hot restart |
| `h` | 帮助 |
| `q` | 退出 |

### DevTools 面板

| 面板 | 用途 |
|------|------|
| Inspector | Widget 树检查 |
| Performance | 帧率、性能分析 |
| Memory | 内存分析 |
| CPU Profiler | CPU 热点分析 |
| Network | 网络请求监控 |
| Logging | 日志查看 |
| Debugger | 断点调试 |

---

*最后更新: 2026-02-22*

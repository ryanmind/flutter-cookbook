# Flutter 桌面端开发

## 概述

Flutter 支持构建 macOS、Windows、Linux 桌面应用，提供原生性能和一致的用户体验。

## 快速开始

### 创建项目

```bash
# 创建支持所有桌面的项目
flutter create --platforms=macos,windows,linux desktop_app

# 运行桌面应用
flutter run -d macos
flutter run -d windows
flutter run -d linux
```

### 平台要求

| 平台 | 最低要求 |
|------|----------|
| macOS | macOS 10.14 (Mojave) |
| Windows | Windows 10 / Windows 11 |
| Linux | GTK 3.0+, GLib 2.56+ |

## 桌面特定功能

### 窗口控制

```dart
import 'package:flutter/material.dart';
import 'dart:io' show Platform;

class WindowManager {
  // 设置窗口标题
  static void setTitle(String title) {
    if (Platform.isMacOS || Platform.isWindows || Platform.isLinux) {
      // 使用 window_manager 包
    }
  }

  // 设置窗口大小
  static void setSize(double width, double height) {}

  // 设置最小窗口大小
  static void setMinSize(double width, double height) {}
}
```

### 使用 window_manager

```yaml
# pubspec.yaml
dependencies:
  window_manager: ^0.3.0
```

```dart
// main.dart
import 'package:window_manager/window_manager.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();

  if (Platform.isMacOS || Platform.isWindows || Platform.isLinux) {
    await windowManager.ensureInitialized();

    final windowOptions = WindowOptions(
      size: Size(1200, 800),
      minimumSize: Size(800, 600),
      center: true,
      backgroundColor: Colors.transparent,
      skipTaskbar: false,
      titleBarStyle: TitleBarStyle.normal,
    );

    await windowManager.waitUntilReadyToShow(windowOptions, () async {
      await windowManager.show();
      await windowManager.focus();
    });
  }

  runApp(const MyApp());
}
```

### 窗口事件监听

```dart
class MyHomePage extends StatefulWidget {
  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> with WindowListener {
  @override
  void initState() {
    super.initState();
    windowManager.addListener(this);
  }

  @override
  void dispose() {
    windowManager.removeListener(this);
    super.dispose();
  }

  @override
  void onWindowClose() {
    // 窗口关闭
  }

  @override
  void onWindowFocus() {
    // 窗口获得焦点
    setState(() {});
  }

  @override
  void onWindowBlur() {
    // 窗口失去焦点
  }

  @override
  void onWindowMaximize() {
    // 窗口最大化
  }

  @override
  void onWindowUnmaximize() {
    // 取消最大化
  }

  @override
  void onWindowMinimize() {
    // 窗口最小化
  }

  @override
  void onWindowRestore() {
    // 窗口还原
  }

  @override
  void onWindowResize() {
    // 窗口大小改变
  }

  @override
  void onWindowResized() {
    // 窗口大小改变完成
  }

  @override
  void onWindowMoved() {
    // 窗口移动
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('桌面应用')),
      body: Center(child: Text('Hello Desktop')),
    );
  }
}
```

## 键盘快捷键

### Shortcuts 和 Actions

```dart
class ShortcutExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Shortcuts(
      shortcuts: {
        LogicalKeySet(LogicalKeyboardKey.control, LogicalKeyboardKey.keyS): SaveIntent(),
        LogicalKeySet(LogicalKeyboardKey.control, LogicalKeyboardKey.keyN): NewIntent(),
        LogicalKeySet(LogicalKeyboardKey.control, LogicalKeyboardKey.keyW): CloseIntent(),
        LogicalKeySet(LogicalKeyboardKey.control, LogicalKeyboardKey.keyQ): QuitIntent(),
      },
      child: Actions(
        actions: {
          SaveIntent: CallbackAction(onInvoke: (_) => save()),
          NewIntent: CallbackAction(onInvoke: (_) => newFile()),
          CloseIntent: CallbackAction(onInvoke: (_) => closeWindow()),
          QuitIntent: CallbackAction(onInvoke: (_) => quit()),
        },
        child: Focus(
          autofocus: true,
          child: Scaffold(
            body: Center(child: Text('使用快捷键')),
          ),
        ),
      ),
    );
  }
}

// 定义 Intent
class SaveIntent extends Intent {}
class NewIntent extends Intent {}
class CloseIntent extends Intent {}
class QuitIntent extends Intent {}
```

### 全局快捷键

```dart
import 'package:flutter/services.dart';

class GlobalShortcuts extends StatefulWidget {
  final Widget child;

  const GlobalShortcuts({required this.child});

  @override
  State<GlobalShortcuts> createState() => _GlobalShortcutsState();
}

class _GlobalShortcutsState extends State<GlobalShortcuts> {
  final FocusNode _focusNode = FocusNode();

  @override
  void initState() {
    super.initState();
    HardwareKeyboard.instance.addHandler(_handleKey);
  }

  @override
  void dispose() {
    HardwareKeyboard.instance.removeHandler(_handleKey);
    _focusNode.dispose();
    super.dispose();
  }

  bool _handleKey(KeyEvent event) {
    if (event is KeyDownEvent) {
      // Ctrl+S
      if (event.logicalKey == LogicalKeyboardKey.keyS &&
          HardwareKeyboard.instance.isControlPressed) {
        save();
        return true;
      }
    }
    return false;
  }

  void save() {
    print('保存');
  }

  @override
  Widget build(BuildContext context) {
    return widget.child;
  }
}
```

## 鼠标交互

### 鼠标样式

```dart
// 设置鼠标样式
MouseRegion(
  cursor: SystemMouseCursors.click,  // 点击手势
  child: Container(child: Text('可点击')),
)

// 常用鼠标样式
SystemMouseCursors.click        // 点击
SystemMouseCursors.text         // 文本选择
SystemMouseCursors.forbidden    // 禁止
SystemMouseCursors.grab         // 抓取
SystemMouseCursors.grabbing     // 抓取中
SystemMouseCursors.resizeColumn // 调整列宽
SystemMouseCursors.resizeRow    // 调整行高
SystemMouseCursors.resizeLeft   // 调整左边界
SystemMouseCursors.resizeRight  // 调整右边界
```

### 悬停效果

```dart
class HoverEffect extends StatefulWidget {
  final Widget child;

  const HoverEffect({required this.child});

  @override
  State<HoverEffect> createState() => _HoverEffectState();
}

class _HoverEffectState extends State<HoverEffect> {
  bool _isHovered = false;

  @override
  Widget build(BuildContext context) {
    return MouseRegion(
      onEnter: (_) => setState(() => _isHovered = true),
      onExit: (_) => setState(() => _isHovered = false),
      child: AnimatedContainer(
        duration: Duration(milliseconds: 200),
        decoration: BoxDecoration(
          color: _isHovered ? Colors.blue.withValues(alpha: 0.1) : Colors.transparent,
          border: _isHovered ? Border.all(color: Colors.blue) : null,
        ),
        child: widget.child,
      ),
    );
  }
}
```

### 右键菜单

```dart
class ContextMenuArea extends StatelessWidget {
  final Widget child;
  final List<PopupMenuEntry> menuItems;

  const ContextMenuArea({
    required this.child,
    required this.menuItems,
  });

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onSecondaryTapDown: (details) {
        showMenu(
          context: context,
          position: RelativeRect.fromLTRB(
            details.globalPosition.dx,
            details.globalPosition.dy,
            details.globalPosition.dx,
            details.globalPosition.dy,
          ),
          items: menuItems,
        );
      },
      child: child,
    );
  }
}

// 使用
ContextMenuArea(
  menuItems: [
    PopupMenuItem(child: Text('复制'), value: 'copy'),
    PopupMenuItem(child: Text('粘贴'), value: 'paste'),
    PopupMenuDivider(),
    PopupMenuItem(child: Text('删除'), value: 'delete'),
  ],
  child: Container(
    padding: EdgeInsets.all(16),
    child: Text('右键点击'),
  ),
)
```

## 文件系统

### 文件选择器

```yaml
dependencies:
  file_selector: ^1.0.0
```

```dart
import 'package:file_selector/file_selector.dart';

class FileOperations {
  // 打开文件
  static Future<XFile?> openFile() async {
    const typeGroup = XTypeGroup(
      label: '文档',
      extensions: ['pdf', 'doc', 'docx'],
    );
    return await openFile(acceptedTypeGroups: [typeGroup]);
  }

  // 打开多个文件
  static Future<List<XFile>> openFiles() async {
    return await openFiles();
  }

  // 保存文件
  static Future<String?> saveFile() async {
    return await getSaveLocation(
      suggestedName: 'untitled.txt',
      acceptedTypeGroups: [
        XTypeGroup(label: '文本', extensions: ['txt']),
      ],
    );
  }

  // 选择目录
  static Future<String?> selectDirectory() async {
    return await getDirectoryPath();
  }
}
```

### 文件读写

```dart
import 'dart:io';

class FileService {
  static Future<String> readFile(String path) async {
    final file = File(path);
    return await file.readAsString();
  }

  static Future<void> writeFile(String path, String content) async {
    final file = File(path);
    await file.writeAsString(content);
  }

  static Future<bool> exists(String path) async {
    final file = File(path);
    return await file.exists();
  }

  static Future<void> delete(String path) async {
    final file = File(path);
    if (await file.exists()) {
      await file.delete();
    }
  }
}
```

## 桌面 UI 设计

### 响应式布局

```dart
class DesktopLayout extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth > 1200) {
          return _buildWideLayout();
        } else if (constraints.maxWidth > 800) {
          return _buildMediumLayout();
        }
        return _buildCompactLayout();
      },
    );
  }

  Widget _buildWideLayout() {
    return Row(
      children: [
        SizedBox(width: 250, child: NavigationPanel()),
        VerticalDivider(width: 1),
        Expanded(flex: 2, child: ContentArea()),
        VerticalDivider(width: 1),
        Expanded(flex: 1, child: DetailPanel()),
      ],
    );
  }

  Widget _buildMediumLayout() {
    return Row(
      children: [
        NavigationRail(extended: false),
        VerticalDivider(width: 1),
        Expanded(child: ContentArea()),
      ],
    );
  }
}
```

### 桌面导航

```dart
class DesktopNavigation extends StatelessWidget {
  final int selectedIndex;
  final ValueChanged<int> onDestinationSelected;

  const DesktopNavigation({
    required this.selectedIndex,
    required this.onDestinationSelected,
  });

  @override
  Widget build(BuildContext context) {
    return NavigationRail(
      selectedIndex: selectedIndex,
      onDestinationSelected: onDestinationSelected,
      extended: true,  // 展开模式
      leading: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () {},
      ),
      destinations: [
        NavigationRailDestination(
          icon: Icon(Icons.home_outlined),
          selectedIcon: Icon(Icons.home),
          label: Text('首页'),
        ),
        NavigationRailDestination(
          icon: Icon(Icons.search_outlined),
          selectedIcon: Icon(Icons.search),
          label: Text('搜索'),
        ),
        NavigationRailDestination(
          icon: Icon(Icons.settings_outlined),
          selectedIcon: Icon(Icons.settings),
          label: Text('设置'),
        ),
      ],
    );
  }
}
```

### 拖拽支持

```dart
class DraggableItem extends StatelessWidget {
  final String data;

  const DraggableItem({required this.data});

  @override
  Widget build(BuildContext context) {
    return Draggable<String>(
      data: data,
      feedback: Material(
        child: Container(
          padding: EdgeInsets.all(8),
          color: Colors.blue,
          child: Text(data),
        ),
      ),
      child: Container(
        padding: EdgeInsets.all(8),
        child: Text(data),
      ),
    );
  }
}

class DropTarget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return DragTarget<String>(
      onWillAccept: (data) => data != null,
      onAccept: (data) {
        print('接收: $data');
      },
      builder: (context, candidateData, rejectedData) {
        final isHovering = candidateData.isNotEmpty;
        return Container(
          decoration: BoxDecoration(
            border: Border.all(
              color: isHovering ? Colors.blue : Colors.grey,
              width: 2,
            ),
          ),
          child: Center(child: Text('拖放到这里')),
        );
      },
    );
  }
}
```

## 系统集成

### 系统托盘

```yaml
dependencies:
  system_tray: ^2.0.0
```

```dart
import 'package:system_tray/system_tray.dart';

class TrayManager {
  static final SystemTray _systemTray = SystemTray.init();

  static Future<void> init() async {
    await _systemTray.initSystemTray(
      title: "My App",
      iconPath: 'assets/app_icon.png',
    );

    final menu = Menu();
    await menu.buildFrom([
      MenuItemLabel(label: '显示窗口', onClick: (_) => showWindow()),
      MenuItemLabel(label: '退出', onClick: (_) => exit(0)),
    ]);

    await _systemTray.setContextMenu(menu);
  }
}
```

### 剪贴板

```dart
import 'package:flutter/services.dart';

class ClipboardService {
  static Future<void> copy(String text) async {
    await Clipboard.setData(ClipboardData(text: text));
  }

  static Future<String?> paste() async {
    final data = await Clipboard.getData(Clipboard.kTextPlain);
    return data?.text;
  }
}
```

## 最佳实践

### 1. 自定义标题栏

```dart
class CustomTitleBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      height: 32,
      color: Theme.of(context).colorScheme.surface,
      child: Row(
        children: [
          Expanded(child: Text('应用标题')),
          // 窗口控制按钮
          _WindowControls(),
        ],
      ),
    );
  }
}

class _WindowControls extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        IconButton(
          icon: Icon(Icons.minimize),
          onPressed: () => windowManager.minimize(),
        ),
        IconButton(
          icon: Icon(Icons.crop_square),
          onPressed: () async {
            if (await windowManager.isMaximized()) {
              windowManager.unmaximize();
            } else {
              windowManager.maximize();
            }
          },
        ),
        IconButton(
          icon: Icon(Icons.close),
          onPressed: () => windowManager.close(),
        ),
      ],
    );
  }
}
```

### 2. 记住窗口状态

```dart
class WindowStateService {
  static const _keySize = 'window_size';
  static const _keyPosition = 'window_position';

  static Future<void> saveState() async {
    final size = await windowManager.getSize();
    final position = await windowManager.getPosition();

    final prefs = await SharedPreferences.getInstance();
    await prefs.setDouble('size_w', size.width);
    await prefs.setDouble('size_h', size.height);
    await prefs.setDouble('pos_x', position.dx);
    await prefs.setDouble('pos_y', position.dy);
  }

  static Future<void> restoreState() async {
    final prefs = await SharedPreferences.getInstance();
    final width = prefs.getDouble('size_w') ?? 1200;
    final height = prefs.getDouble('size_h') ?? 800;
    final x = prefs.getDouble('pos_x');
    final y = prefs.getDouble('pos_y');

    await windowManager.setSize(Size(width, height));
    if (x != null && y != null) {
      await windowManager.setPosition(Offset(x, y));
    }
  }
}
```

---

*最后更新: 2026-02-19*

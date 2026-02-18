# Flutter PopupMenu 组件用法详解

## 简介

PopupMenu 是弹出菜单组件，用于显示一组选项供用户选择。Flutter 提供了 PopupMenuButton（Material 2）和 MenuAnchor/MenuItemButton（Material 3）两种实现方式。

## 1. PopupMenuButton

### 1.1 基本概念

PopupMenuButton 的核心特点：
- 点击弹出菜单
- 支持自定义菜单项
- 支持图标和文字
- Material 2 标准组件

### 1.2 基本语法

```dart
PopupMenuButton<String>(
  onSelected: (value) {
    print('选中: $value');
  },
  itemBuilder: (context) => [
    PopupMenuItem(value: 'edit', child: Text('编辑')),
    PopupMenuItem(value: 'delete', child: Text('删除')),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `itemBuilder` | `PopupMenuBuilder` | 菜单项构建器（必需） |
| `onSelected` | `PopupMenuSelected?` | 选中回调 |
| `onCanceled` | `PopupMenuCanceled?` | 取消回调 |
| `icon` | `Widget?` | 触发图标 |
| `child` | `Widget?` | 触发组件 |
| `initialValue` | `T?` | 初始选中值 |
| `tooltip` | `String?` | 长按提示 |
| `elevation` | `double?` | 阴影高度 |
| `shape` | `ShapeBorder?` | 形状 |
| `color` | `Color?` | 背景色 |

### 1.4 基础示例

#### 简单菜单

```dart
PopupMenuButton<String>(
  onSelected: (value) {
    print('选中: $value');
  },
  itemBuilder: (context) => [
    PopupMenuItem(value: 'edit', child: Text('编辑')),
    PopupMenuItem(value: 'share', child: Text('分享')),
    PopupMenuItem(value: 'delete', child: Text('删除')),
  ],
)
```

#### 带图标的菜单

```dart
PopupMenuButton<String>(
  icon: Icon(Icons.more_vert),
  onSelected: (value) {
    print('选中: $value');
  },
  itemBuilder: (context) => [
    PopupMenuItem(
      value: 'edit',
      child: Row(
        children: [
          Icon(Icons.edit, size: 20),
          SizedBox(width: 12),
          Text('编辑'),
        ],
      ),
    ),
    PopupMenuItem(
      value: 'share',
      child: Row(
        children: [
          Icon(Icons.share, size: 20),
          SizedBox(width: 12),
          Text('分享'),
        ],
      ),
    ),
    PopupMenuItem(
      value: 'delete',
      child: Row(
        children: [
          Icon(Icons.delete, size: 20, color: Colors.red),
          SizedBox(width: 12),
          Text('删除', style: TextStyle(color: Colors.red)),
        ],
      ),
    ),
  ],
)
```

#### 自定义触发器

```dart
PopupMenuButton<String>(
  child: Container(
    padding: EdgeInsets.symmetric(horizontal: 12, vertical: 8),
    child: Row(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text('选项'),
        Icon(Icons.arrow_drop_down),
      ],
    ),
  ),
  onSelected: (value) {},
  itemBuilder: (context) => [
    PopupMenuItem(value: '1', child: Text('选项 1')),
    PopupMenuItem(value: '2', child: Text('选项 2')),
  ],
)
```

#### 分隔线

```dart
PopupMenuButton<String>(
  onSelected: (value) {},
  itemBuilder: (context) => [
    PopupMenuItem(value: 'edit', child: Text('编辑')),
    PopupMenuItem(value: 'copy', child: Text('复制')),
    PopupMenuDivider(),
    PopupMenuItem(value: 'delete', child: Text('删除')),
  ],
)
```

#### 选中的菜单项

```dart
PopupMenuButton<String>(
  initialValue: _selectedValue,
  onSelected: (value) {
    setState(() {
      _selectedValue = value;
    });
  },
  itemBuilder: (context) => [
    CheckedPopupMenuItem(
      value: 'option1',
      checked: _selectedValue == 'option1',
      child: Text('选项 1'),
    ),
    CheckedPopupMenuItem(
      value: 'option2',
      checked: _selectedValue == 'option2',
      child: Text('选项 2'),
    ),
  ],
)
```

## 2. MenuAnchor (Material 3)

### 2.1 基本概念

MenuAnchor 是 Material 3 推荐的菜单组件：
- 更现代的设计
- 更灵活的定位
- 支持嵌套菜单
- 支持键盘导航

### 2.2 基本语法

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return IconButton(
      onPressed: () {
        if (controller.isOpen) {
          controller.close();
        } else {
          controller.open();
        }
      },
      icon: Icon(Icons.more_vert),
    );
  },
  menuChildren: [
    MenuItemButton(child: Text('编辑'), onPressed: () {}),
    MenuItemButton(child: Text('分享'), onPressed: () {}),
    MenuItemButton(child: Text('删除'), onPressed: () {}),
  ],
)
```

### 2.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `builder` | `MenuAnchorBuilder` | 构建触发器 |
| `menuChildren` | `List<Widget>` | 菜单项列表 |
| `controller` | `MenuController?` | 菜单控制器 |
| `alignmentOffset` | `Offset` | 对齐偏移 |
| `style` | `MenuStyle?` | 菜单样式 |
| `anchorTapClosesMenu` | `bool` | 点击触发器是否关闭 |

### 2.4 基础示例

#### 简单菜单

```dart
class SimpleMenuAnchor extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MenuAnchor(
      builder: (context, controller, child) {
        return IconButton(
          onPressed: () {
            if (controller.isOpen) {
              controller.close();
            } else {
              controller.open();
            }
          },
          icon: Icon(Icons.more_vert),
        );
      },
      menuChildren: [
        MenuItemButton(
          leadingIcon: Icon(Icons.edit),
          child: Text('编辑'),
          onPressed: () {
            print('编辑');
          },
        ),
        MenuItemButton(
          leadingIcon: Icon(Icons.share),
          child: Text('分享'),
          onPressed: () {
            print('分享');
          },
        ),
        MenuItemButton(
          leadingIcon: Icon(Icons.delete),
          child: Text('删除'),
          onPressed: () {
            print('删除');
          },
        ),
      ],
    );
  }
}
```

#### 带快捷键提示

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return TextButton(
      onPressed: () => controller.open(),
      child: Text('文件'),
    );
  },
  menuChildren: [
    MenuItemButton(
      leadingIcon: Icon(Icons.add),
      trailingIcon: Text('⌘N'),
      child: Text('新建'),
      onPressed: () {},
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.save),
      trailingIcon: Text('⌘S'),
      child: Text('保存'),
      onPressed: () {},
    ),
    MenuDivider(),
    MenuItemButton(
      leadingIcon: Icon(Icons.exit_to_app),
      trailingIcon: Text('⌘Q'),
      child: Text('退出'),
      onPressed: () {},
    ),
  ],
)
```

#### 嵌套子菜单

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return TextButton(
      onPressed: () => controller.open(),
      child: Text('编辑'),
    );
  },
  menuChildren: [
    MenuItemButton(child: Text('撤销'), onPressed: () {}),
    MenuItemButton(child: Text('重做'), onPressed: () {}),
    SubmenuButton(
      menuChildren: [
        MenuItemButton(child: Text('剪切'), onPressed: () {}),
        MenuItemButton(child: Text('复制'), onPressed: () {}),
        MenuItemButton(child: Text('粘贴'), onPressed: () {}),
      ],
      child: Text('编辑'),
    ),
  ],
)
```

## 3. 高级用法

### 3.1 下拉选择器

```dart
class DropdownMenuButton extends StatefulWidget {
  final List<String> options;
  final String? initialValue;
  final ValueChanged<String>? onChanged;

  const DropdownMenuButton({
    required this.options,
    this.initialValue,
    this.onChanged,
    Key? key,
  }) : super(key: key);

  @override
  _DropdownMenuButtonState createState() => _DropdownMenuButtonState();
}

class _DropdownMenuButtonState extends State<DropdownMenuButton> {
  String? _selectedValue;

  @override
  void initState() {
    super.initState();
    _selectedValue = widget.initialValue;
  }

  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<String>(
      child: Container(
        padding: EdgeInsets.symmetric(horizontal: 12, vertical: 8),
        decoration: BoxDecoration(
          border: Border.all(color: Colors.grey),
          borderRadius: BorderRadius.circular(4),
        ),
        child: Row(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text(_selectedValue ?? '请选择'),
            SizedBox(width: 8),
            Icon(Icons.arrow_drop_down),
          ],
        ),
      ),
      onSelected: (value) {
        setState(() => _selectedValue = value);
        widget.onChanged?.call(value);
      },
      itemBuilder: (context) => widget.options.map((option) {
        return PopupMenuItem(
          value: option,
          child: Row(
            children: [
              if (option == _selectedValue) ...[
                Icon(Icons.check, size: 20),
                SizedBox(width: 8),
              ] else
                SizedBox(width: 28),
              Text(option),
            ],
          ),
        );
      }).toList(),
    );
  }
}
```

### 3.2 上下文菜单

```dart
class ContextMenuArea extends StatelessWidget {
  final Widget child;
  final List<PopupMenuEntry<String>> menuItems;
  final ValueChanged<String>? onSelected;

  const ContextMenuArea({
    required this.child,
    required this.menuItems,
    this.onSelected,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onSecondaryTapDown: (details) {
        showMenu<String>(
          context: context,
          position: RelativeRect.fromLTRB(
            details.globalPosition.dx,
            details.globalPosition.dy,
            details.globalPosition.dx,
            details.globalPosition.dy,
          ),
          items: menuItems,
        ).then((value) {
          if (value != null) {
            onSelected?.call(value);
          }
        });
      },
      child: child,
    );
  }
}

// 使用
ContextMenuArea(
  menuItems: [
    PopupMenuItem(value: 'copy', child: Text('复制')),
    PopupMenuItem(value: 'paste', child: Text('粘贴')),
    PopupMenuItem(value: 'delete', child: Text('删除')),
  ],
  onSelected: (value) {
    print('选中: $value');
  },
  child: Container(
    padding: EdgeInsets.all(16),
    color: Colors.grey[200],
    child: Text('右键点击显示菜单'),
  ),
)
```

### 3.3 动态菜单

```dart
class DynamicPopupMenu extends StatelessWidget {
  final List<MenuItem> items;

  const DynamicPopupMenu({required this.items, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<String>(
      onSelected: (value) {
        final item = items.firstWhere((item) => item.value == value);
        item.onTap?.call();
      },
      itemBuilder: (context) => items.map((item) {
        if (item.isDivider) {
          return PopupMenuDivider();
        }
        return PopupMenuItem(
          value: item.value,
          enabled: item.enabled,
          child: Row(
            children: [
              if (item.icon != null) ...[
                Icon(item.icon, size: 20, color: item.color),
                SizedBox(width: 12),
              ],
              Text(
                item.label,
                style: TextStyle(color: item.color),
              ),
            ],
          ),
        );
      }).toList(),
    );
  }
}

class MenuItem {
  final String value;
  final String label;
  final IconData? icon;
  final Color? color;
  final bool enabled;
  final bool isDivider;
  final VoidCallback? onTap;

  MenuItem({
    required this.value,
    required this.label,
    this.icon,
    this.color,
    this.enabled = true,
    this.isDivider = false,
    this.onTap,
  });

  MenuItem.divider()
      : value = '',
        label = '',
        icon = null,
        color = null,
        enabled = true,
        isDivider = true,
        onTap = null;
}
```

### 3.4 菜单工具类

```dart
class AppPopupMenu {
  static Widget basic({
    required List<PopupMenuEntry<String>> items,
    ValueChanged<String>? onSelected,
    Widget? icon,
    Widget? child,
  }) {
    return PopupMenuButton<String>(
      icon: icon,
      child: child,
      onSelected: onSelected,
      itemBuilder: (context) => items,
    );
  }

  static Widget action({
    required IconData icon,
    required List<PopupMenuEntry<String>> items,
    ValueChanged<String>? onSelected,
    String? tooltip,
  }) {
    return PopupMenuButton<String>(
      icon: Icon(icon),
      tooltip: tooltip,
      onSelected: onSelected,
      itemBuilder: (context) => items,
    );
  }
}

// 使用
AppPopupMenu.action(
  icon: Icons.more_vert,
  onSelected: (value) {
    print(value);
  },
  items: [
    PopupMenuItem(value: 'edit', child: Text('编辑')),
    PopupMenuItem(value: 'delete', child: Text('删除')),
  ],
)
```

## 4. 常见问题与解决方案

### 问题1：菜单项图标颜色问题

```dart
// 使用 Icon 主题
PopupMenuItem(
  child: Row(
    children: [
      Icon(Icons.edit, color: Theme.of(context).primaryColor),
      SizedBox(width: 8),
      Text('编辑'),
    ],
  ),
)
```

### 问题2：菜单位置不正确

```dart
// 使用 showMenu 手动控制位置
showMenu(
  context: context,
  position: RelativeRect.fromLTRB(left, top, right, bottom),
  items: [...],
)
```

### 问题3：菜单项禁用状态

```dart
PopupMenuItem(
  value: 'disabled',
  enabled: false,
  child: Text('禁用项'),
)
```

### 问题4：菜单项高度调整

```dart
PopupMenuItem(
  height: 48,  // 自定义高度
  child: Text('菜单项'),
)
```

### 问题5：关闭菜单后执行操作

```dart
PopupMenuButton<String>(
  onSelected: (value) {
    // 菜单已关闭，执行操作
    Navigator.pop(context);
    // 然后执行其他操作
  },
  itemBuilder: (context) => [...],
)
```

## 5. 最佳实践

### 5.1 统一菜单样式

```dart
class AppPopupMenuTheme {
  static PopupMenuThemeData get theme => PopupMenuThemeData(
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(8),
    ),
    elevation: 8,
  );
}

MaterialApp(
  theme: ThemeData(
    popupMenuTheme: AppPopupMenuTheme.theme,
  ),
)
```

### 5.2 菜单项封装

```dart
class AppMenuItem {
  final String value;
  final String label;
  final IconData? icon;
  final VoidCallback? onTap;

  PopupMenuItem<String> toPopupMenuItem() {
    return PopupMenuItem<String>(
      value: value,
      onTap: onTap,
      child: Row(
        children: [
          if (icon != null) ...[
            Icon(icon, size: 20),
            SizedBox(width: 12),
          ],
          Text(label),
        ],
      ),
    );
  }
}
```

### 5.3 确认操作

```dart
PopupMenuButton<String>(
  onSelected: (value) async {
    if (value == 'delete') {
      final confirmed = await showDialog<bool>(
        context: context,
        builder: (context) => AlertDialog(
          title: Text('确认删除'),
          content: Text('删除后无法恢复'),
          actions: [
            TextButton(onPressed: () => Navigator.pop(context, false), child: Text('取消')),
            FilledButton(onPressed: () => Navigator.pop(context, true), child: Text('删除')),
          ],
        ),
      );
      if (confirmed == true) {
        // 执行删除
      }
    }
  },
  itemBuilder: (context) => [
    PopupMenuItem(value: 'edit', child: Text('编辑')),
    PopupMenuItem(value: 'delete', child: Text('删除')),
  ],
)
```

## 总结

PopupMenu 是 Flutter 中常用的菜单组件：

**PopupMenuButton**：
- Material 2 标准菜单
- 使用简单
- 适合大多数场景

**MenuAnchor**：
- Material 3 推荐组件
- 更现代的设计
- 支持嵌套菜单

**常用场景**：
- 操作菜单
- 下拉选择
- 右键菜单
- 设置选项

**最佳实践**：
1. 使用图标提高识别度
2. 危险操作添加确认
3. 统一菜单样式
4. 合理使用分隔线分组

---

*最后更新: 2026-02-18*

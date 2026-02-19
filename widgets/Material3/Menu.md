# Flutter Menu 组件用法详解

## 简介

Menu 是 Material 3 中的现代菜单组件，用于显示选项列表。相比传统的 DropdownButton，Menu 提供了更灵活的布局和更好的视觉效果。

## 1. 初级用法

### 1.1 基本概念

Menu 的核心特点：
- Material 3 设计风格
- 支持级联菜单
- 支持图标和分割线
- 支持自定义触发组件

### 1.2 基本语法

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return FilledButton(
      onPressed: () {
        if (controller.isOpen) {
          controller.close();
        } else {
          controller.open();
        }
      },
      child: Text('显示菜单'),
    );
  },
  menuChildren: [
    MenuItemButton(
      child: Text('选项一'),
      onPressed: () {
        print('选择选项一');
      },
    ),
    MenuItemButton(
      child: Text('选项二'),
      onPressed: () {
        print('选择选项二');
      },
    ),
  ],
)
```

### 1.3 主要组件

| 组件 | 说明 |
|------|------|
| `MenuAnchor` | 菜单锚点容器 |
| `MenuItemButton` | 菜单项按钮 |
| `SubmenuButton` | 子菜单按钮 |
| `MenuAcceleratorLabel` | 快捷键标签 |

### 1.4 MenuAnchor 属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `builder` | `MenuAnchorBuilder` | - | 触发组件构建器 |
| `menuChildren` | `List<Widget>` | - | 菜单子项 |
| `controller` | `MenuController?` | - | 菜单控制器 |
| `alignmentOffset` | `Offset` | `Offset.zero` | 对齐偏移 |
| `anchorTapClosesMenu` | `bool` | `false` | 点击锚点是否关闭 |
| `crossAxisUnconstrained` | `bool` | `false` | 是否不限制宽度 |
| `menuPositionResolver` | `MenuPositionResolver?` | - | 菜单位置解析 |
| `style` | `MenuStyle?` | - | 菜单样式 |
| `clipBehavior` | `Clip` | `Clip.none` | 裁剪行为 |

### 1.5 基础示例

#### 简单下拉菜单

```dart
class SimpleMenu extends StatelessWidget {
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
          onPressed: () => print('编辑'),
        ),
        MenuItemButton(
          leadingIcon: Icon(Icons.delete),
          child: Text('删除'),
          onPressed: () => print('删除'),
        ),
        MenuItemButton(
          leadingIcon: Icon(Icons.share),
          child: Text('分享'),
          onPressed: () => print('分享'),
        ),
      ],
    );
  }
}
```

#### 带图标的菜单

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return FilledButton.icon(
      onPressed: () => controller.isOpen ? controller.close() : controller.open(),
      icon: Icon(Icons.arrow_drop_down),
      label: Text('操作'),
    );
  },
  menuChildren: [
    MenuItemButton(
      leadingIcon: Icon(Icons.copy),
      child: Text('复制'),
      onPressed: () {},
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.cut),
      child: Text('剪切'),
      onPressed: () {},
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.paste),
      child: Text('粘贴'),
      onPressed: () {},
    ),
  ],
)
```

## 2. 中级用法

### 2.1 分割线

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return IconButton(
      onPressed: () => controller.isOpen ? controller.close() : controller.open(),
      icon: Icon(Icons.more_vert),
    );
  },
  menuChildren: [
    MenuItemButton(
      leadingIcon: Icon(Icons.edit),
      child: Text('编辑'),
      onPressed: () {},
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.delete),
      child: Text('删除'),
      onPressed: () {},
    ),
    Divider(),  // 分割线
    MenuItemButton(
      leadingIcon: Icon(Icons.share),
      child: Text('分享'),
      onPressed: () {},
    ),
  ],
)
```

### 2.2 子菜单

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return FilledButton(
      onPressed: () => controller.isOpen ? controller.close() : controller.open(),
      child: Text('文件'),
    );
  },
  menuChildren: [
    MenuItemButton(
      leadingIcon: Icon(Icons.file_open),
      child: Text('打开'),
      onPressed: () {},
    ),
    SubmenuButton(
      leadingIcon: Icon(Icons.folder),
      menuChildren: [
        MenuItemButton(child: Text('新建文件夹'), onPressed: () {}),
        MenuItemButton(child: Text('打开最近'), onPressed: () {}),
      ],
      child: Text('最近'),
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.save),
      child: Text('保存'),
      onPressed: () {},
    ),
  ],
)
```

### 2.3 禁用菜单项

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return IconButton(
      onPressed: () => controller.isOpen ? controller.close() : controller.open(),
      icon: Icon(Icons.more_vert),
    );
  },
  menuChildren: [
    MenuItemButton(
      leadingIcon: Icon(Icons.edit),
      child: Text('编辑'),
      onPressed: () {},
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.delete),
      child: Text('删除'),
      onPressed: null,  // 禁用
    ),
  ],
)
```

### 2.4 快捷键标签

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return FilledButton(
      onPressed: () => controller.isOpen ? controller.close() : controller.open(),
      child: Text('编辑'),
    );
  },
  menuChildren: [
    MenuItemButton(
      leadingIcon: Icon(Icons.copy),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Text('复制'),
          Text('Ctrl+C', style: TextStyle(color: Colors.grey)),
        ],
      ),
      onPressed: () {},
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.paste),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Text('粘贴'),
          Text('Ctrl+V', style: TextStyle(color: Colors.grey)),
        ],
      ),
      onPressed: () {},
    ),
  ],
)
```

### 2.5 单选菜单

```dart
class RadioMenu extends StatefulWidget {
  @override
  _RadioMenuState createState() => _RadioMenuState();
}

class _RadioMenuState extends State<RadioMenu> {
  String _selectedValue = 'option1';

  @override
  Widget build(BuildContext context) {
    return MenuAnchor(
      builder: (context, controller, child) {
        return FilledButton(
          onPressed: () => controller.isOpen ? controller.close() : controller.open(),
          child: Text('选择: $_selectedValue'),
        );
      },
      menuChildren: [
        MenuItemButton(
          leadingIcon: _selectedValue == 'option1' ? Icon(Icons.check) : SizedBox(width: 24),
          child: Text('选项一'),
          onPressed: () {
            setState(() => _selectedValue = 'option1');
          },
        ),
        MenuItemButton(
          leadingIcon: _selectedValue == 'option2' ? Icon(Icons.check) : SizedBox(width: 24),
          child: Text('选项二'),
          onPressed: () {
            setState(() => _selectedValue = 'option2');
          },
        ),
        MenuItemButton(
          leadingIcon: _selectedValue == 'option3' ? Icon(Icons.check) : SizedBox(width: 24),
          child: Text('选项三'),
          onPressed: () {
            setState(() => _selectedValue = 'option3');
          },
        ),
      ],
    );
  }
}
```

## 3. 高级用法

### 3.1 自定义菜单样式

```dart
MenuAnchor(
  style: MenuStyle(
    backgroundColor: WidgetStateProperty.all(Colors.white),
    elevation: WidgetStateProperty.all(8),
    shape: WidgetStateProperty.all(
      RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
    ),
    padding: WidgetStateProperty.all(EdgeInsets.symmetric(vertical: 8)),
  ),
  builder: (context, controller, child) {
    return FilledButton(
      onPressed: () => controller.isOpen ? controller.close() : controller.open(),
      child: Text('菜单'),
    );
  },
  menuChildren: [
    MenuItemButton(child: Text('选项一'), onPressed: () {}),
    MenuItemButton(child: Text('选项二'), onPressed: () {}),
  ],
)
```

### 3.2 右键上下文菜单

```dart
GestureDetector(
  onSecondaryTapDown: (details) {
    final position = details.globalPosition;
    // 显示上下文菜单需要使用 Overlay
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
class DynamicMenu extends StatelessWidget {
  final List<String> items;
  final ValueChanged<String>? onSelect;

  const DynamicMenu({
    required this.items,
    this.onSelect,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MenuAnchor(
      builder: (context, controller, child) {
        return IconButton(
          onPressed: () => controller.isOpen ? controller.close() : controller.open(),
          icon: Icon(Icons.list),
        );
      },
      menuChildren: items.map((item) {
        return MenuItemButton(
          child: Text(item),
          onPressed: () => onSelect?.call(item),
        );
      }).toList(),
    );
  }
}
```

### 3.4 带头像的菜单

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return InkWell(
      onTap: () => controller.isOpen ? controller.close() : controller.open(),
      child: Row(
        children: [
          CircleAvatar(
            backgroundImage: NetworkImage('https://example.com/avatar.jpg'),
          ),
          SizedBox(width: 8),
          Text('用户名'),
          Icon(Icons.arrow_drop_down),
        ],
      ),
    );
  },
  menuChildren: [
    MenuItemButton(
      leadingIcon: Icon(Icons.person),
      child: Text('个人资料'),
      onPressed: () {},
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.settings),
      child: Text('设置'),
      onPressed: () {},
    ),
    Divider(),
    MenuItemButton(
      leadingIcon: Icon(Icons.logout),
      child: Text('退出登录'),
      onPressed: () {},
    ),
  ],
)
```

### 3.5 多级菜单

```dart
MenuAnchor(
  builder: (context, controller, child) {
    return FilledButton(
      onPressed: () => controller.isOpen ? controller.close() : controller.open(),
      child: Text('新建'),
    );
  },
  menuChildren: [
    MenuItemButton(
      leadingIcon: Icon(Icons.insert_drive_file),
      child: Text('新建文件'),
      onPressed: () {},
    ),
    SubmenuButton(
      leadingIcon: Icon(Icons.folder),
      menuChildren: [
        MenuItemButton(child: Text('空文件夹'), onPressed: () {}),
        SubmenuButton(
          menuChildren: [
            MenuItemButton(child: Text('文档模板'), onPressed: () {}),
            MenuItemButton(child: Text('表格模板'), onPressed: () {}),
          ],
          child: Text('模板文件夹'),
        ),
      ],
      child: Text('新建文件夹'),
    ),
    MenuItemButton(
      leadingIcon: Icon(Icons.create_new_folder),
      child: Text('新建项目'),
      onPressed: () {},
    ),
  ],
)
```

## 4. MenuItemButton 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `child` | `Widget?` | 内容组件 |
| `leadingIcon` | `Widget?` | 左侧图标 |
| `trailingIcon` | `Widget?` | 右侧图标 |
| `onPressed` | `VoidCallback?` | 点击回调 |
| `focusNode` | `FocusNode?` | 焦点节点 |
| `requestFocusOnHover` | `bool` | 悬停时是否获取焦点 |
| `shortcut` | `ShortcutActivator?` | 快捷键 |

## 5. 常见问题与解决方案

### 问题1：菜单位置不对

```dart
MenuAnchor(
  alignmentOffset: Offset(0, 10),  // 调整偏移
  builder: (context, controller, child) {
    return FilledButton(
      onPressed: () => controller.open(),
      child: Text('菜单'),
    );
  },
  menuChildren: [...],
)
```

### 问题2：菜单宽度受限

```dart
MenuAnchor(
  crossAxisUnconstrained: true,  // 不限制宽度
  builder: (context, controller, child) {
    return FilledButton(
      onPressed: () => controller.open(),
      child: Text('菜单'),
    );
  },
  menuChildren: [...],
)
```

### 问题3：点击菜单后不关闭

```dart
// 默认点击菜单项后会关闭菜单
// 如果需要保持打开，可以在 onPressed 中重新打开
MenuItemButton(
  child: Text('选项'),
  onPressed: () {
    // 处理逻辑
    // 菜单会自动关闭
  },
)
```

## 6. Menu vs DropdownButton

| 特性 | Menu | DropdownButton |
|------|------|----------------|
| 设计风格 | Material 3 | 传统 Material |
| 子菜单 | ✅ 支持 | ❌ 不支持 |
| 图标支持 | ✅ 完整 | 有限 |
| 自定义触发 | ✅ 灵活 | 固定样式 |
| 分割线 | ✅ 支持 | ❌ 不支持 |

```dart
// Menu - Material 3 推荐
MenuAnchor(
  builder: (context, controller, child) => FilledButton(...),
  menuChildren: [...],
)

// DropdownButton - 传统组件
DropdownButton<String>(
  items: [...],
  onChanged: (value) {},
)
```

## 总结

Menu 是 Flutter Material 3 的现代菜单组件：

**核心要点**：
1. 使用 MenuAnchor 创建菜单容器
2. MenuItemButton 创建菜单项
3. SubmenuButton 创建子菜单
4. 支持图标、分割线、快捷键

**常用属性**：
- `builder` - 触发组件构建器
- `menuChildren` - 菜单项列表
- `alignmentOffset` - 位置偏移

**最佳实践**：
1. 使用 Material 3 风格的 Menu 替代 DropdownButton
2. 为常用操作添加快捷键提示
3. 使用分割线组织菜单项
4. 提供清晰的图标和文字说明

---

*最后更新: 2026-02-19*

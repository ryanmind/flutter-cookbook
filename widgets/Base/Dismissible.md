# Flutter Dismissible 组件用法详解

## 简介

Dismissible 是 Flutter 中用于实现滑动删除效果的组件。它可以包裹任何子组件，当用户滑动时会触发删除动画和回调。

## 1. 初级用法

### 1.1 基本概念

Dismissible 的作用：
- 实现滑动删除/归档效果
- 支持多个滑动方向
- 自定义背景和删除动画
- 触发删除回调

### 1.2 基本语法

```dart
Dismissible(
  key: Key('item'),
  onDismissed: (direction) {
    // 删除逻辑
  },
  child: ListTile(title: Text('滑动删除')),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `key` | `Key` | 必需，唯一标识 |
| `child` | `Widget` | 子组件 |
| `onDismissed` | `DismissDirectionCallback` | 删除回调 |
| `direction` | `DismissDirection` | 滑动方向 |
| `background` | `Widget?` | 滑动背景（左侧） |
| `secondaryBackground` | `Widget?` | 滑动背景（右侧） |
| `confirmDismiss` | `ConfirmDismissCallback?` | 确认删除回调 |
| `movementDuration` | `Duration` | 移动动画时长 |
| `resizeDuration` | `Duration?` | 缩放动画时长 |
| `crossAxisEndOffset` | `double` | 交叉轴偏移 |

### 1.4 DismissDirection

| 值 | 说明 |
|------|------|
| `horizontal` | 水平滑动（默认） |
| `vertical` | 垂直滑动 |
| `endToStart` | 从右向左 |
| `startToEnd` | 从左向右 |
| `up` | 向上 |
| `down` | 向下 |

### 1.5 基础示例

#### 简单滑动删除

```dart
Dismissible(
  key: Key('item1'),
  onDismissed: (direction) {
    print('删除了项目');
  },
  child: ListTile(
    title: Text('滑动删除我'),
  ),
)
```

#### 指定方向

```dart
Dismissible(
  key: Key('item2'),
  direction: DismissDirection.endToStart,  // 只能从右向左滑
  onDismissed: (direction) {
    print('从右向左删除');
  },
  child: ListTile(title: Text('从右向左滑')),
)
```

## 2. 中级用法

### 2.1 自定义背景

```dart
Dismissible(
  key: Key('item'),
  background: Container(
    color: Colors.red,
    alignment: Alignment.centerLeft,
    padding: EdgeInsets.only(left: 20),
    child: Icon(Icons.delete, color: Colors.white),
  ),
  onDismissed: (direction) {
    print('删除');
  },
  child: ListTile(title: Text('左滑删除')),
)
```

### 2.2 双向不同背景

```dart
Dismissible(
  key: Key('item'),
  background: Container(
    color: Colors.green,
    alignment: Alignment.centerLeft,
    padding: EdgeInsets.only(left: 20),
    child: Icon(Icons.archive, color: Colors.white),
  ),
  secondaryBackground: Container(
    color: Colors.red,
    alignment: Alignment.centerRight,
    padding: EdgeInsets.only(right: 20),
    child: Icon(Icons.delete, color: Colors.white),
  ),
  onDismissed: (direction) {
    if (direction == DismissDirection.startToEnd) {
      print('归档');
    } else {
      print('删除');
    }
  },
  child: ListTile(title: Text('左滑归档，右滑删除')),
)
```

### 2.3 确认删除

```dart
Dismissible(
  key: Key('item'),
  confirmDismiss: (direction) async {
    return await showDialog<bool>(
      context: context,
      builder: (context) {
        return AlertDialog(
          title: Text('确认删除'),
          content: Text('确定要删除这个项目吗？'),
          actions: [
            TextButton(
              onPressed: () => Navigator.pop(context, false),
              child: Text('取消'),
            ),
            TextButton(
              onPressed: () => Navigator.pop(context, true),
              child: Text('删除'),
            ),
          ],
        );
      },
    ) ?? false;
  },
  onDismissed: (direction) {
    print('已删除');
  },
  child: ListTile(title: Text('需要确认才能删除')),
)
```

### 2.4 常见布局场景

#### 列表项删除

```dart
class DismissibleList extends StatefulWidget {
  @override
  State<DismissibleList> createState() => _DismissibleListState();
}

class _DismissibleListState extends State<DismissibleList> {
  final List<String> _items = List.generate(10, (i) => '项目 ${i + 1}');

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: _items.length,
      itemBuilder: (context, index) {
        final item = _items[index];
        return Dismissible(
          key: Key(item),
          background: Container(
            color: Colors.red,
            child: Icon(Icons.delete, color: Colors.white),
            alignment: Alignment.centerRight,
            padding: EdgeInsets.only(right: 20),
          ),
          onDismissed: (direction) {
            setState(() {
              _items.removeAt(index);
            });
            ScaffoldMessenger.of(context).showSnackBar(
              SnackBar(content: Text('$item 已删除')),
            );
          },
          child: ListTile(
            title: Text(item),
          ),
        );
      },
    );
  }
}
```

#### 邮件列表

```dart
class MailItem extends StatelessWidget {
  final String subject;
  final String sender;
  final VoidCallback onArchive;
  final VoidCallback onDelete;

  const MailItem({
    required this.subject,
    required this.sender,
    required this.onArchive,
    required this.onDelete,
  });

  @override
  Widget build(BuildContext context) {
    return Dismissible(
      key: Key(subject),
      background: Container(
        color: Colors.blue,
        child: Icon(Icons.archive, color: Colors.white),
        alignment: Alignment.centerLeft,
        padding: EdgeInsets.only(left: 20),
      ),
      secondaryBackground: Container(
        color: Colors.red,
        child: Icon(Icons.delete, color: Colors.white),
        alignment: Alignment.centerRight,
        padding: EdgeInsets.only(right: 20),
      ),
      confirmDismiss: (direction) async {
        if (direction == DismissDirection.startToEnd) {
          onArchive();
          return false;  // 不执行删除动画
        }
        return true;  // 执行删除
      },
      onDismissed: (direction) {
        onDelete();
      },
      child: ListTile(
        title: Text(subject),
        subtitle: Text(sender),
      ),
    );
  }
}
```

## 3. 高级用法

### 3.1 撤销删除

```dart
class UndoableList extends StatefulWidget {
  @override
  State<UndoableList> createState() => _UndoableListState();
}

class _UndoableListState extends State<UndoableList> {
  List<String> _items = List.generate(10, (i) => '项目 ${i + 1}');
  final List<String> _deletedItems = [];

  void _removeItem(int index) {
    final item = _items[index];
    setState(() {
      _items.removeAt(index);
      _deletedItems.add(item);
    });
    
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text('$item 已删除'),
        action: SnackBarAction(
          label: '撤销',
          onPressed: () {
            setState(() {
              _items.insert(index, _deletedItems.removeLast());
            });
          },
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: _items.length,
      itemBuilder: (context, index) {
        final item = _items[index];
        return Dismissible(
          key: Key(item),
          onDismissed: (direction) => _removeItem(index),
          child: ListTile(title: Text(item)),
        );
      },
    );
  }
}
```

### 3.2 自定义动画

```dart
Dismissible(
  key: Key('item'),
  movementDuration: Duration(milliseconds: 200),
  resizeDuration: Duration(milliseconds: 300),
  crossAxisEndOffset: 0.2,  // 向上偏移
  onDismissed: (direction) {},
  child: ListTile(title: Text('自定义动画')),
)
```

### 3.3 垂直滑动

```dart
Dismissible(
  key: Key('item'),
  direction: DismissDirection.vertical,
  background: Container(
    color: Colors.red,
    child: Center(child: Text('删除', style: TextStyle(color: Colors.white))),
  ),
  onDismissed: (direction) {},
  child: Container(
    height: 100,
    color: Colors.blue,
    child: Center(child: Text('上下滑动删除')),
  ),
)
```

### 3.4 结合 ReorderableListView

```dart
class EditableList extends StatefulWidget {
  @override
  State<EditableList> createState() => _EditableListState();
}

class _EditableListState extends State<EditableList> {
  final List<String> _items = List.generate(10, (i) => '项目 ${i + 1}');

  @override
  Widget build(BuildContext context) {
    return ReorderableListView.builder(
      itemCount: _items.length,
      onReorder: (oldIndex, newIndex) {
        setState(() {
          if (newIndex > oldIndex) newIndex--;
          final item = _items.removeAt(oldIndex);
          _items.insert(newIndex, item);
        });
      },
      itemBuilder: (context, index) {
        final item = _items[index];
        return Dismissible(
          key: Key(item),
          onDismissed: (direction) {
            setState(() {
              _items.removeAt(index);
            });
          },
          child: ListTile(
            key: Key(item),
            title: Text(item),
            trailing: Icon(Icons.drag_handle),
          ),
        );
      },
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：Key 重复

```dart
// 问题：多个 Dismissible 使用相同的 Key
Dismissible(key: Key('same'), ...)

// 解决：确保每个 Key 唯一
Dismissible(key: Key('item_$index'), ...)
// 或使用对象的唯一属性
Dismissible(key: Key(item.id), ...)
```

### 问题2：删除后位置错乱

```dart
// 问题：使用 index 作为 key，删除后位置错乱
Dismissible(key: Key('$index'), ...)

// 解决：使用稳定的唯一标识
Dismissible(key: Key(item.id), ...)
```

### 问题3：背景显示问题

```dart
// 问题：背景看不见
// 解决：确保 child 不会遮挡背景，或使用 Card
Dismissible(
  background: Container(color: Colors.red),
  child: Card(child: ListTile(...)),  // Card 有透明效果
)
```

## 5. Dismissible vs 其他滑动方案对比

| 方案 | 用途 | 特点 |
|------|------|------|
| **Dismissible** | 滑动删除 | Flutter 内置，简单 |
| **flutter_slidable** | 高级滑动 | 功能丰富，可定制 |
| **swipeable** | 滑动操作 | 轻量级 |

### 选择建议

```dart
// 简单删除 → Dismissible
Dismissible(
  onDismissed: (direction) => delete(),
  child: ListTile(...),
)

// 需要多个操作按钮 → flutter_slidable
Slidable(
  actions: [
    IconSlideAction(icon: Icons.archive, onTap: archive),
  ],
  secondaryActions: [
    IconSlideAction(icon: Icons.delete, onTap: delete),
  ],
  child: ListTile(...),
)
```

## 6. 最佳实践

### 6.1 封装可复用组件

```dart
class SwipeableListItem extends StatelessWidget {
  final Widget child;
  final VoidCallback onDelete;
  final VoidCallback? onArchive;
  final Key key;

  const SwipeableListItem({
    required this.key,
    required this.child,
    required this.onDelete,
    this.onArchive,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Dismissible(
      key: key,
      background: Container(
        color: Colors.blue,
        child: Icon(Icons.archive, color: Colors.white),
        alignment: Alignment.centerLeft,
        padding: EdgeInsets.only(left: 20),
      ),
      secondaryBackground: Container(
        color: Colors.red,
        child: Icon(Icons.delete, color: Colors.white),
        alignment: Alignment.centerRight,
        padding: EdgeInsets.only(right: 20),
      ),
      confirmDismiss: onArchive != null
          ? (direction) {
              if (direction == DismissDirection.startToEnd) {
                onArchive!();
                return Future.value(false);
              }
              return Future.value(true);
            }
          : null,
      onDismissed: (_) => onDelete(),
      child: child,
    );
  }
}
```

### 6.2 使用 ValueKey

```dart
// 对于对象，使用 ValueKey
Dismissible(
  key: ValueKey(item.id),
  child: ListTile(title: Text(item.name)),
)
```

### 6.3 提供反馈

```dart
Dismissible(
  key: Key(item.id),
  onDismissed: (direction) {
    // 提供视觉反馈
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text('${item.name} 已删除')),
    );
    // 执行删除
    deleteItem(item);
  },
  child: ListTile(title: Text(item.name)),
)
```

## 总结

Dismissible 是 Flutter 中实现滑动删除的标准组件：

**核心要点**：
1. 必须提供唯一的 Key
2. 支持多个滑动方向
3. 可自定义背景和动画

**最佳实践**：
1. 使用稳定的唯一标识作为 Key
2. 提供撤销功能
3. 给用户明确反馈

**常见场景**：
- 列表删除
- 邮件归档/删除
- 任务完成
- 通知移除

---

*最后更新: 2026-02-19*

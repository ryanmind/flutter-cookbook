# Flutter ReorderableListView 组件用法详解

## 简介

ReorderableListView 是 Flutter 中用于实现可重新排序列表的组件。用户可以通过长按并拖动来重新排列列表项。

## 1. 初级用法

### 1.1 基本概念

ReorderableListView 的作用：
- 支持长按拖动排序
- 自动处理动画效果
- 提供排序回调

### 1.2 基本语法

```dart
ReorderableListView(
  onReorder: (oldIndex, newIndex) {
    // 处理排序逻辑
  },
  children: [
    ListTile(key: Key('1'), title: Text('项目 1')),
    ListTile(key: Key('2'), title: Text('项目 2')),
    ListTile(key: Key('3'), title: Text('项目 3')),
  ],
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `onReorder` | `ReorderCallback` | 排序回调（必需） |
| `children` | `List<Widget>` | 子组件列表 |
| `buildDefaultDragHandles` | `bool` | 是否显示默认拖动手柄 |
| `header` | `Widget?` | 列表头部 |
| `footer` | `Widget?` | 列表底部 |
| `scrollDirection` | `Axis` | 滚动方向 |
| `padding` | `EdgeInsets?` | 内边距 |

### 1.4 基础示例

#### 简单排序列表

```dart
class SimpleReorderableList extends StatefulWidget {
  @override
  State<SimpleReorderableList> createState() => _SimpleReorderableListState();
}

class _SimpleReorderableListState extends State<SimpleReorderableList> {
  final List<String> _items = ['项目 1', '项目 2', '项目 3', '项目 4', '项目 5'];

  @override
  Widget build(BuildContext context) {
    return ReorderableListView(
      onReorder: (oldIndex, newIndex) {
        setState(() {
          if (newIndex > oldIndex) {
            newIndex -= 1;
          }
          final item = _items.removeAt(oldIndex);
          _items.insert(newIndex, item);
        });
      },
      children: _items.map((item) {
        return ListTile(
          key: Key(item),
          title: Text(item),
        );
      }).toList(),
    );
  }
}
```

## 2. 中级用法

### 2.1 排序逻辑说明

```dart
onReorder: (oldIndex, newIndex) {
  // oldIndex: 原位置索引
  // newIndex: 新位置索引（注意：如果向后移动，需要 -1）
  
  if (newIndex > oldIndex) {
    newIndex -= 1;  // 关键！
  }
  
  final item = items.removeAt(oldIndex);
  items.insert(newIndex, item);
}
```

### 2.2 自定义拖动手柄

```dart
ReorderableListView(
  buildDefaultDragHandles: false,  // 禁用默认手柄
  onReorder: (oldIndex, newIndex) {
    // ...
  },
  children: _items.map((item) {
    return ListTile(
      key: Key(item.id),
      title: Text(item.name),
      trailing: ReorderableDragStartListener(
        index: _items.indexOf(item),
        child: Icon(Icons.drag_handle),
      ),
    );
  }).toList(),
)
```

### 2.3 添加头部和底部

```dart
ReorderableListView(
  header: Container(
    padding: EdgeInsets.all(16),
    color: Colors.grey[200],
    child: Text('长按拖动排序'),
  ),
  footer: Container(
    padding: EdgeInsets.all(16),
    child: Text('共 ${_items.length} 项'),
  ),
  onReorder: (oldIndex, newIndex) {
    // ...
  },
  children: [...],
)
```

### 2.4 常见布局场景

#### 任务列表

```dart
class TaskList extends StatefulWidget {
  @override
  State<TaskList> createState() => _TaskListState();
}

class _TaskListState extends State<TaskList> {
  final List<Task> _tasks = [
    Task(id: '1', title: '学习 Flutter', completed: false),
    Task(id: '2', title: '写代码', completed: true),
    Task(id: '3', title: '休息', completed: false),
  ];

  @override
  Widget build(BuildContext context) {
    return ReorderableListView(
      onReorder: (oldIndex, newIndex) {
        setState(() {
          if (newIndex > oldIndex) newIndex--;
          final task = _tasks.removeAt(oldIndex);
          _tasks.insert(newIndex, task);
        });
      },
      children: _tasks.map((task) {
        return CheckboxListTile(
          key: Key(task.id),
          title: Text(
            task.title,
            style: TextStyle(
              decoration: task.completed ? TextDecoration.lineThrough : null,
            ),
          ),
          value: task.completed,
          onChanged: (value) {
            setState(() {
              task.completed = value ?? false;
            });
          },
        );
      }).toList(),
    );
  }
}

class Task {
  final String id;
  final String title;
  bool completed;

  Task({required this.id, required this.title, required this.completed});
}
```

#### 卡片排序

```dart
class ReorderableCards extends StatefulWidget {
  @override
  State<ReorderableCards> createState() => _ReorderableCardsState();
}

class _ReorderableCardsState extends State<ReorderableCards> {
  final List<Color> _colors = [
    Colors.red,
    Colors.green,
    Colors.blue,
    Colors.yellow,
    Colors.purple,
  ];

  @override
  Widget build(BuildContext context) {
    return ReorderableListView(
      onReorder: (oldIndex, newIndex) {
        setState(() {
          if (newIndex > oldIndex) newIndex--;
          final color = _colors.removeAt(oldIndex);
          _colors.insert(newIndex, color);
        });
      },
      children: _colors.asMap().entries.map((entry) {
        return Card(
          key: Key('color-${entry.key}'),
          margin: EdgeInsets.all(8),
          color: entry.value,
          child: SizedBox(
            height: 80,
            child: Center(
              child: Text(
                '卡片 ${entry.key + 1}',
                style: TextStyle(color: Colors.white),
              ),
            ),
          ),
        );
      }).toList(),
    );
  }
}
```

## 3. 高级用法

### 3.1 ReorderableListView.builder

```dart
ReorderableListView.builder(
  itemCount: _items.length,
  onReorder: (oldIndex, newIndex) {
    setState(() {
      if (newIndex > oldIndex) newIndex--;
      final item = _items.removeAt(oldIndex);
      _items.insert(newIndex, item);
    });
  },
  itemBuilder: (context, index) {
    return ListTile(
      key: Key(_items[index].id),
      title: Text(_items[index].name),
    );
  },
)
```

### 3.2 水平排序

```dart
ReorderableListView(
  scrollDirection: Axis.horizontal,
  onReorder: (oldIndex, newIndex) {
    // ...
  },
  children: _items.map((item) {
    return Container(
      key: Key(item.id),
      width: 100,
      color: Colors.blue,
      child: Center(child: Text(item.name)),
    );
  }).toList(),
)
```

### 3.3 配合 Dismissible

```dart
class ReorderableAndDismissible extends StatefulWidget {
  @override
  State<ReorderableAndDismissible> createState() => _ReorderableAndDismissibleState();
}

class _ReorderableAndDismissibleState extends State<ReorderableAndDismissible> {
  List<String> _items = List.generate(10, (i) => '项目 ${i + 1}');

  @override
  Widget build(BuildContext context) {
    return ReorderableListView(
      onReorder: (oldIndex, newIndex) {
        setState(() {
          if (newIndex > oldIndex) newIndex--;
          final item = _items.removeAt(oldIndex);
          _items.insert(newIndex, item);
        });
      },
      children: _items.map((item) {
        return Dismissible(
          key: Key(item),
          onDismissed: (direction) {
            setState(() {
              _items.remove(item);
            });
          },
          child: ListTile(
            title: Text(item),
            trailing: Icon(Icons.drag_handle),
          ),
        );
      }).toList(),
    );
  }
}
```

### 3.4 分组排序

```dart
class GroupedReorderableList extends StatefulWidget {
  @override
  State<GroupedReorderableList> createState() => _GroupedReorderableListState();
}

class _GroupedReorderableListState extends State<GroupedReorderableList> {
  final Map<String, List<String>> _groups = {
    'A': ['A1', 'A2', 'A3'],
    'B': ['B1', 'B2'],
    'C': ['C1', 'C2', 'C3', 'C4'],
  };

  @override
  Widget build(BuildContext context) {
    return ListView(
      children: _groups.entries.map((entry) {
        return Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Padding(
              padding: EdgeInsets.all(8),
              child: Text(
                '分组 ${entry.key}',
                style: TextStyle(fontWeight: FontWeight.bold, fontSize: 18),
              ),
            ),
            ReorderableListView(
              shrinkWrap: true,
              physics: NeverScrollableScrollPhysics(),
              onReorder: (oldIndex, newIndex) {
                setState(() {
                  if (newIndex > oldIndex) newIndex--;
                  final item = _groups[entry.key]!.removeAt(oldIndex);
                  _groups[entry.key]!.insert(newIndex, item);
                });
              },
              children: entry.value.map((item) {
                return ListTile(
                  key: Key(item),
                  title: Text(item),
                );
              }).toList(),
            ),
            Divider(),
          ],
        );
      }).toList(),
    );
  }
}
```

## 4. 常见问题与解决方案

### 问题1：Key 必须唯一

```dart
// 问题：Key 重复导致排序异常
ReorderableListView(
  children: [
    ListTile(key: Key('same'), title: Text('1')),
    ListTile(key: Key('same'), title: Text('2')),  // 重复
  ],
)

// 解决：使用唯一 Key
ReorderableListView(
  children: items.map((item) {
    return ListTile(
      key: Key(item.id),  // 使用唯一标识
      title: Text(item.name),
    );
  }).toList(),
)
```

### 问题2：使用 index 作为 Key

```dart
// 问题：使用 index 作为 Key，排序后位置错乱
ListTile(key: Key('$index'), ...)

// 解决：使用数据的唯一标识
ListTile(key: Key(item.id), ...)
```

### 问题3：嵌套滚动冲突

```dart
// 问题：ReorderableListView 嵌套在其他滚动组件中
ListView(
  children: [
    ReorderableListView(...),  // 滚动冲突
  ],
)

// 解决：使用 shrinkWrap 和 NeverScrollableScrollPhysics
ListView(
  children: [
    ReorderableListView(
      shrinkWrap: true,
      physics: NeverScrollableScrollPhysics(),
      ...
    ),
  ],
)
```

## 5. ReorderableListView vs 其他排序方案对比

| 方案 | 用途 | 特点 |
|------|------|------|
| **ReorderableListView** | 列表排序 | Flutter 内置 |
| **flutter_reorderable_list** | 高级排序 | 功能更丰富 |
| **Draggable + DragTarget** | 自定义排序 | 完全自定义 |

### 选择建议

```dart
// 简单列表排序 → ReorderableListView
ReorderableListView(onReorder: ..., children: ...)

// 需要多列排序 → flutter_reorderable_list
// 或使用 Draggable + DragTarget 自定义
```

## 6. 最佳实践

### 6.1 封装可排序列表

```dart
class ReorderableList<T> extends StatelessWidget {
  final List<T> items;
  final Widget Function(T item) itemBuilder;
  final String Function(T item) keyExtractor;
  final void Function(int oldIndex, int newIndex) onReorder;

  const ReorderableList({
    required this.items,
    required this.itemBuilder,
    required this.keyExtractor,
    required this.onReorder,
  });

  @override
  Widget build(BuildContext context) {
    return ReorderableListView.builder(
      itemCount: items.length,
      onReorder: onReorder,
      itemBuilder: (context, index) {
        final item = items[index];
        return Container(
          key: Key(keyExtractor(item)),
          child: itemBuilder(item),
        );
      },
    );
  }
}
```

### 6.2 保存排序结果

```dart
class PersistentReorderableList extends StatefulWidget {
  @override
  State<PersistentReorderableList> createState() => _PersistentReorderableListState();
}

class _PersistentReorderableListState extends State<PersistentReorderableList> {
  List<String> _items = [];

  @override
  void initState() {
    super.initState();
    _loadOrder();
  }

  Future<void> _loadOrder() async {
    // 从持久化存储加载排序
    // final prefs = await SharedPreferences.getInstance();
    // final order = prefs.getStringList('item_order') ?? [];
  }

  Future<void> _saveOrder() async {
    // 保存排序到持久化存储
    // final prefs = await SharedPreferences.getInstance();
    // await prefs.setStringList('item_order', _items);
  }

  @override
  Widget build(BuildContext context) {
    return ReorderableListView(
      onReorder: (oldIndex, newIndex) {
        setState(() {
          if (newIndex > oldIndex) newIndex--;
          final item = _items.removeAt(oldIndex);
          _items.insert(newIndex, item);
        });
        _saveOrder();
      },
      children: _items.map((item) {
        return ListTile(key: Key(item), title: Text(item));
      }).toList(),
    );
  }
}
```

### 6.3 添加排序动画提示

```dart
ReorderableListView(
  proxyDecorator: (child, index, animation) {
    return AnimatedBuilder(
      animation: animation,
      builder: (context, child) {
        return Transform.scale(
          scale: 1.05,
          child: Opacity(
            opacity: 0.8,
            child: Container(
              decoration: BoxDecoration(
                boxShadow: [
                  BoxShadow(
                    color: Colors.black26,
                    blurRadius: 10,
                  ),
                ],
              ),
              child: child,
            ),
          ),
        );
      },
      child: child,
    );
  },
  onReorder: (oldIndex, newIndex) {
    // ...
  },
  children: [...],
)
```

## 总结

ReorderableListView 是 Flutter 中实现列表排序的标准组件：

**核心要点**：
1. 必须提供唯一的 Key
2. onReorder 中处理排序逻辑
3. newIndex 向后移动时需要 -1

**最佳实践**：
1. 使用数据的唯一标识作为 Key
2. 保存排序结果到持久化存储
3. 自定义 proxyDecorator 提升体验

**常见场景**：
- 任务列表排序
- 卡片排序
- 菜单排序
- 播放列表排序

---

*最后更新: 2026-02-19*

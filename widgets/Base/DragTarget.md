# Flutter DragTarget 组件用法详解

## 简介

DragTarget 是 Flutter 中用于接收拖拽数据的组件。它与 Draggable 配合使用，实现完整的拖放操作。

## 1. 初级用法

### 1.1 基本概念

DragTarget 的作用：
- 接收 Draggable 拖拽的数据
- 提供拖拽悬停反馈
- 控制是否接受拖拽

### 1.2 基本语法

```dart
DragTarget<String>(
  onWillAccept: (data) => true,
  onAccept: (data) {
    print('接收到: $data');
  },
  builder: (context, candidateData, rejectedData) {
    return Container(
      width: 200,
      height: 100,
      color: Colors.grey[300],
      child: Center(child: Text('放到这里')),
    );
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `builder` | `DragTargetBuilder<T>` | 构建函数 |
| `onWillAccept` | `DragTargetWillAccept<T>?` | 是否接受拖拽 |
| `onAccept` | `DragTargetAccept<T>?` | 接受拖拽回调 |
| `onAcceptWithDetails` | `DragTargetAcceptWithDetails<T>?` | 接受拖拽（带详情） |
| `onLeave` | `DragTargetLeave<T>?` | 离开目标区域 |
| `onMove` | `DragTargetMove<T>?` | 移动回调 |

### 1.4 builder 参数

| 参数 | 说明 |
|------|------|
| `candidateData` | 当前悬停的可接受数据列表 |
| `rejectedData` | 当前悬停的被拒绝数据列表 |

### 1.5 基础示例

#### 简单拖放

```dart
class SimpleDragDrop extends StatefulWidget {
  @override
  State<SimpleDragDrop> createState() => _SimpleDragDropState();
}

class _SimpleDragDropState extends State<SimpleDragDrop> {
  String? _receivedData;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Draggable<String>(
          data: 'Hello World',
          feedback: Container(
            padding: EdgeInsets.all(16),
            color: Colors.blue,
            child: Text('拖我'),
          ),
          child: Container(
            padding: EdgeInsets.all(16),
            color: Colors.green,
            child: Text('拖我'),
          ),
        ),
        SizedBox(height: 32),
        DragTarget<String>(
          onWillAccept: (data) => data != null,
          onAccept: (data) {
            setState(() {
              _receivedData = data;
            });
          },
          builder: (context, candidate, rejected) {
            return Container(
              width: 200,
              height: 100,
              color: candidate.isNotEmpty ? Colors.blue[100] : Colors.grey[300],
              child: Center(
                child: Text(_receivedData ?? '放到这里'),
              ),
            );
          },
        ),
      ],
    );
  }
}
```

## 2. 中级用法

### 2.1 悬停状态反馈

```dart
DragTarget<String>(
  onWillAccept: (data) => true,
  onAccept: (data) {
    // 处理数据
  },
  builder: (context, candidate, rejected) {
    final isHovering = candidate.isNotEmpty;
    
    return AnimatedContainer(
      duration: Duration(milliseconds: 200),
      width: 200,
      height: 100,
      decoration: BoxDecoration(
        color: isHovering ? Colors.green[100] : Colors.grey[200],
        border: isHovering
            ? Border.all(color: Colors.green, width: 2)
            : Border.all(color: Colors.grey, width: 1),
        borderRadius: BorderRadius.circular(8),
      ),
      child: Center(
        child: Text(
          isHovering ? '释放以放置' : '拖放到这里',
          style: TextStyle(
            color: isHovering ? Colors.green : Colors.grey,
          ),
        ),
      ),
    );
  },
)
```

### 2.2 条件接受

```dart
class ConditionalDragTarget extends StatefulWidget {
  @override
  State<ConditionalDragTarget> createState() => _ConditionalDragTargetState();
}

class _ConditionalDragTargetState extends State<ConditionalDragTarget> {
  final List<String> _acceptedItems = [];

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: [
            Draggable<int>(data: 1, feedback: Text('1'), child: Text('1')),
            Draggable<int>(data: 2, feedback: Text('2'), child: Text('2')),
            Draggable<int>(data: 3, feedback: Text('3'), child: Text('3')),
          ],
        ),
        SizedBox(height: 32),
        DragTarget<int>(
          onWillAccept: (data) {
            // 只接受偶数
            return data != null && data % 2 == 0;
          },
          onAccept: (data) {
            setState(() {
              _acceptedItems.add(data.toString());
            });
          },
          builder: (context, candidate, rejected) {
            return Container(
              width: 200,
              height: 100,
              color: candidate.isNotEmpty
                  ? Colors.green[100]
                  : rejected.isNotEmpty
                      ? Colors.red[100]
                      : Colors.grey[200],
              child: Center(
                child: Text(
                  _acceptedItems.isEmpty
                      ? '只接受偶数'
                      : '已接受: ${_acceptedItems.join(", ")}',
                ),
              ),
            );
          },
        ),
      ],
    );
  }
}
```

### 2.3 离开和移动回调

```dart
DragTarget<String>(
  onWillAccept: (data) => true,
  onAccept: (data) {
    print('接受: $data');
  },
  onLeave: (data) {
    print('离开: $data');
  },
  onMove: (details) {
    print('移动: ${details.offset}');
  },
  builder: (context, candidate, rejected) {
    return Container(
      width: 200,
      height: 100,
      color: Colors.grey[300],
      child: Center(child: Text('目标区域')),
    );
  },
)
```

### 2.4 常见布局场景

#### 看板布局

```dart
class KanbanBoard extends StatefulWidget {
  @override
  State<KanbanBoard> createState() => _KanbanBoardState();
}

class _KanbanBoardState extends State<KanbanBoard> {
  final List<String> _todo = ['任务1', '任务2', '任务3'];
  final List<String> _doing = [];
  final List<String> _done = [];

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        _buildColumn('待办', _todo),
        _buildColumn('进行中', _doing),
        _buildColumn('完成', _done),
      ],
    );
  }

  Widget _buildColumn(String title, List<String> items) {
    return Expanded(
      child: DragTarget<String>(
        onWillAccept: (data) => data != null,
        onAccept: (data) {
          setState(() {
            _todo.remove(data);
            _doing.remove(data);
            _done.remove(data);
            items.add(data);
          });
        },
        builder: (context, candidate, rejected) {
          return Container(
            margin: EdgeInsets.all(8),
            padding: EdgeInsets.all(8),
            color: candidate.isNotEmpty ? Colors.blue[50] : Colors.grey[200],
            child: Column(
              children: [
                Text(title, style: TextStyle(fontWeight: FontWeight.bold)),
                Divider(),
                Expanded(
                  child: ListView(
                    children: items.map((item) {
                      return Draggable<String>(
                        data: item,
                        feedback: Card(child: Padding(
                          padding: EdgeInsets.all(8),
                          child: Text(item),
                        )),
                        child: Card(
                          child: ListTile(
                            title: Text(item),
                          ),
                        ),
                      );
                    }).toList(),
                  ),
                ),
              ],
            ),
          );
        },
      ),
    );
  }
}
```

#### 购物车

```dart
class ShoppingCartExample extends StatefulWidget {
  @override
  State<ShoppingCartExample> createState() => _ShoppingCartExampleState();
}

class _ShoppingCartExampleState extends State<ShoppingCartExample> {
  final List<String> _cart = [];

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: ['苹果', '香蕉', '橙子'].map((item) {
            return Draggable<String>(
              data: item,
              feedback: Material(
                child: Chip(label: Text(item)),
              ),
              child: Chip(label: Text(item)),
            );
          }).toList(),
        ),
        SizedBox(height: 32),
        DragTarget<String>(
          onWillAccept: (data) => true,
          onAccept: (data) {
            setState(() {
              _cart.add(data);
            });
          },
          builder: (context, candidate, rejected) {
            return Container(
              width: double.infinity,
              height: 150,
              decoration: BoxDecoration(
                color: candidate.isNotEmpty ? Colors.green[50] : Colors.grey[200],
                border: Border.all(color: Colors.grey),
                borderRadius: BorderRadius.circular(8),
              ),
              child: _cart.isEmpty
                  ? Center(child: Text('拖放商品到购物车'))
                  : Wrap(
                      children: _cart.map((item) {
                        return Chip(
                          label: Text(item),
                          onDeleted: () {
                            setState(() {
                              _cart.remove(item);
                            });
                          },
                        );
                      }).toList(),
                    ),
            );
          },
        ),
      ],
    );
  }
}
```

## 3. 高级用法

### 3.1 获取拖拽位置

```dart
DragTarget<String>(
  onAcceptWithDetails: (details) {
    print('数据: ${details.data}');
    print('位置: ${details.offset}');
  },
  builder: (context, candidate, rejected) {
    return Container(
      width: 200,
      height: 200,
      color: Colors.grey[300],
    );
  },
)
```

### 3.2 多类型拖放

```dart
class MultiTypeDragDrop extends StatefulWidget {
  @override
  State<MultiTypeDragDrop> createState() => _MultiTypeDragDropState();
}

class _MultiTypeDragDropState extends State<MultiTypeDragDrop> {
  final List<dynamic> _received = [];

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          children: [
            Draggable<String>(
              data: '文本',
              feedback: Container(color: Colors.blue, child: Text('文本')),
              child: Container(color: Colors.blue[100], child: Text('文本')),
            ),
            Draggable<int>(
              data: 123,
              feedback: Container(color: Colors.green, child: Text('数字')),
              child: Container(color: Colors.green[100], child: Text('数字')),
            ),
          ],
        ),
        SizedBox(height: 32),
        DragTarget<dynamic>(
          onWillAccept: (data) => true,
          onAccept: (data) {
            setState(() {
              _received.add(data);
            });
          },
          builder: (context, candidate, rejected) {
            return Container(
              width: 200,
              height: 100,
              color: Colors.grey[300],
              child: Center(
                child: Text(_received.map((e) => e.toString()).join(', ')),
              ),
            );
          },
        ),
      ],
    );
  }
}
```

### 3.3 嵌套 DragTarget

```dart
Stack(
  children: [
    DragTarget<String>(
      onWillAccept: (data) => true,
      onAccept: (data) => print('外层: $data'),
      builder: (context, candidate, rejected) {
        return Container(
          width: 300,
          height: 300,
          color: Colors.grey[200],
          child: Center(
            child: DragTarget<String>(
              onWillAccept: (data) => true,
              onAccept: (data) => print('内层: $data'),
              builder: (context, candidate, rejected) {
                return Container(
                  width: 150,
                  height: 150,
                  color: Colors.grey[400],
                  child: Center(child: Text('内层目标')),
                );
              },
            ),
          ),
        );
      },
    ),
  ],
)
```

## 4. 常见问题与解决方案

### 问题1：onWillAccept 返回 false

```dart
// 问题：onWillAccept 返回 false 时不触发 onAccept
DragTarget(
  onWillAccept: (data) => false,  // 永远拒绝
  onAccept: (data) {  // 永远不会触发
    print('接受');
  },
)

// 解决：根据条件返回 true
DragTarget(
  onWillAccept: (data) {
    return data != null;  // 或其他条件
  },
  onAccept: (data) {
    print('接受');
  },
)
```

### 问题2：candidate 数据为空

```dart
// 问题：builder 中 candidate 始终为空
// 原因：onWillAccept 返回 false 或未设置

DragTarget(
  onWillAccept: (data) => true,  // 必须返回 true
  builder: (context, candidate, rejected) {
    return Container(
      color: candidate.isNotEmpty ? Colors.blue : Colors.grey,
    );
  },
)
```

### 问题3：与 Draggable 数据类型不匹配

```dart
// 问题：类型不匹配导致无法接收
Draggable<String>(data: 'text', ...)
DragTarget<int>(...)  // 类型不匹配

// 解决：使用相同类型
Draggable<String>(data: 'text', ...)
DragTarget<String>(...)

// 或使用 dynamic
DragTarget<dynamic>(...)
```

## 5. DragTarget 拖放流程

```
1. 用户开始拖拽 Draggable
2. Draggable 进入 DragTarget 区域
3. 触发 onWillAccept 判断是否接受
4. 返回 true 后 Draggable 在区域内移动
5. 触发 onMove 回调（如果设置了）
6. 用户释放拖拽
7. 触发 onAccept 回调
8. 或者用户离开区域触发 onLeave
```

## 6. 最佳实践

### 6.1 封装拖放目标

```dart
class DropZone<T> extends StatelessWidget {
  final String label;
  final Function(T) onAccept;
  final bool Function(T)? willAccept;

  const DropZone({
    required this.label,
    required this.onAccept,
    this.willAccept,
  });

  @override
  Widget build(BuildContext context) {
    return DragTarget<T>(
      onWillAccept: (data) => willAccept?.call(data!) ?? true,
      onAccept: onAccept,
      builder: (context, candidate, rejected) {
        return AnimatedContainer(
          duration: Duration(milliseconds: 200),
          decoration: BoxDecoration(
            color: candidate.isNotEmpty ? Colors.blue[100] : Colors.grey[200],
            borderRadius: BorderRadius.circular(8),
          ),
          padding: EdgeInsets.all(16),
          child: Center(child: Text(label)),
        );
      },
    );
  }
}
```

### 6.2 拖放状态管理

```dart
class DragDropController extends ChangeNotifier {
  bool _isDragging = false;
  bool _isOverTarget = false;

  bool get isDragging => _isDragging;
  bool get isOverTarget => _isOverTarget;

  void startDrag() {
    _isDragging = true;
    notifyListeners();
  }

  void endDrag() {
    _isDragging = false;
    _isOverTarget = false;
    notifyListeners();
  }

  void enterTarget() {
    _isOverTarget = true;
    notifyListeners();
  }

  void leaveTarget() {
    _isOverTarget = false;
    notifyListeners();
  }
}
```

## 总结

DragTarget 是 Flutter 中接收拖拽数据的核心组件：

**核心要点**：
1. onWillAccept 控制是否接受
2. onAccept 处理接收的数据
3. builder 提供视觉反馈

**最佳实践**：
1. 提供悬停状态反馈
2. 使用条件判断控制接受
3. 处理好数据类型匹配

**常见场景**：
- 看板布局
- 购物车
- 分组管理
- 文件上传区

---

*最后更新: 2026-02-19*

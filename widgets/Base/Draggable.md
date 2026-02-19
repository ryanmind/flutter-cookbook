# Flutter Draggable 组件用法详解

## 简介

Draggable 是 Flutter 中用于实现拖拽功能的组件。它可以将子组件变为可拖拽状态，配合 DragTarget 实现拖放操作。

## 1. 初级用法

### 1.1 基本概念

Draggable 的作用：
- 使子组件可拖拽
- 提供拖拽时的反馈组件
- 传递数据给 DragTarget
- 支持多种拖拽反馈

### 1.2 基本语法

```dart
Draggable<String>(
  data: 'Hello',
  feedback: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
  child: Container(
    width: 100,
    height: 100,
    color: Colors.grey,
    child: Center(child: Text('拖我')),
  ),
)
```

### 1.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `data` | `T?` | 拖拽传递的数据 |
| `child` | `Widget` | 原位子组件 |
| `feedback` | `Widget` | 拖拽时的反馈组件 |
| `childWhenDragging` | `Widget?` | 拖拽时原位显示的组件 |
| `feedbackOffset` | `Offset` | 反馈组件偏移 |
| `axis` | `Axis?` | 拖拽轴向限制 |
| `onDragStarted` | `VoidCallback?` | 开始拖拽回调 |
| `onDragEnd` | `DraggableDetailsCallback?` | 结束拖拽回调 |
| `onDragCompleted` | `VoidCallback?` | 拖拽完成回调 |
| `onDraggableCanceled` | `DraggableCanceledCallback?` | 取消拖拽回调 |

### 1.4 基础示例

#### 基本拖拽

```dart
Draggable<String>(
  data: '拖拽的数据',
  feedback: Material(
    child: Container(
      padding: EdgeInsets.all(16),
      color: Colors.blue,
      child: Text('正在拖拽'),
    ),
  ),
  child: Container(
    padding: EdgeInsets.all(16),
    color: Colors.grey[300],
    child: Text('拖我'),
  ),
)
```

#### 拖拽时原位变化

```dart
Draggable<String>(
  data: 'data',
  childWhenDragging: Container(
    width: 100,
    height: 100,
    color: Colors.grey[200],
    child: Center(child: Text('已拖走')),
  ),
  feedback: Container(
    width: 100,
    height: 100,
    color: Colors.blue,
  ),
  child: Container(
    width: 100,
    height: 100,
    color: Colors.green,
  ),
)
```

## 2. 中级用法

### 2.1 限制拖拽方向

```dart
// 只能水平拖拽
Draggable<String>(
  axis: Axis.horizontal,
  data: 'horizontal',
  feedback: Container(width: 100, height: 50, color: Colors.blue),
  child: Container(width: 100, height: 50, color: Colors.grey),
)

// 只能垂直拖拽
Draggable<String>(
  axis: Axis.vertical,
  data: 'vertical',
  feedback: Container(width: 50, height: 100, color: Colors.red),
  child: Container(width: 50, height: 100, color: Colors.grey),
)
```

### 2.2 拖拽回调

```dart
Draggable<String>(
  data: 'item',
  onDragStarted: () {
    print('开始拖拽');
  },
  onDragEnd: (details) {
    print('拖拽结束: ${details.offset}');
  },
  onDragCompleted: () {
    print('拖拽完成');
  },
  onDraggableCanceled: (velocity, offset) {
    print('拖拽取消');
  },
  feedback: Container(width: 50, height: 50, color: Colors.blue),
  child: Container(width: 50, height: 50, color: Colors.grey),
)
```

### 2.3 配合 DragTarget

```dart
class DragDropExample extends StatefulWidget {
  @override
  State<DragDropExample> createState() => _DragDropExampleState();
}

class _DragDropExampleState extends State<DragDropExample> {
  String? _droppedData;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Draggable<String>(
          data: '拖拽的数据',
          feedback: Container(
            padding: EdgeInsets.all(16),
            color: Colors.blue,
            child: Text('正在拖拽'),
          ),
          child: Container(
            padding: EdgeInsets.all(16),
            color: Colors.green,
            child: Text('拖我'),
          ),
        ),
        SizedBox(height: 50),
        DragTarget<String>(
          onWillAccept: (data) => true,
          onAccept: (data) {
            setState(() {
              _droppedData = data;
            });
          },
          builder: (context, candidateData, rejectedData) {
            return Container(
              width: 200,
              height: 100,
              color: Colors.grey[300],
              child: Center(
                child: Text(_droppedData ?? '放到这里'),
              ),
            );
          },
        ),
      ],
    );
  }
}
```

### 2.4 常见布局场景

#### 拖拽排序列表

```dart
class ReorderableListExample extends StatefulWidget {
  @override
  State<ReorderableListExample> createState() => _ReorderableListExampleState();
}

class _ReorderableListExampleState extends State<ReorderableListExample> {
  final List<String> _items = ['A', 'B', 'C', 'D', 'E'];

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: _items.length,
      itemBuilder: (context, index) {
        final item = _items[index];
        return Draggable<String>(
          key: Key(item),
          data: item,
          feedback: Material(
            child: Container(
              width: 300,
              padding: EdgeInsets.all(16),
              color: Colors.blue[200],
              child: Text(item),
            ),
          ),
          child: ListTile(
            title: Text(item),
            trailing: Icon(Icons.drag_handle),
          ),
        );
      },
    );
  }
}
```

#### 拖拽到分组

```dart
class GroupDragDrop extends StatefulWidget {
  @override
  State<GroupDragDrop> createState() => _GroupDragDropState();
}

class _GroupDragDropState extends State<GroupDragDrop> {
  final List<String> _todo = ['任务1', '任务2', '任务3'];
  final List<String> _done = <String>[];

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Expanded(
          child: DragTarget<String>(
            onWillAccept: (data) => true,
            onAccept: (data) {
              setState(() {
                _todo.add(data);
                _done.remove(data);
              });
            },
            builder: (context, candidate, rejected) {
              return Container(
                color: Colors.blue[50],
                child: ListView(
                  children: _todo.map((item) {
                    return Draggable<String>(
                      data: item,
                      feedback: Card(child: Padding(
                        padding: EdgeInsets.all(8),
                        child: Text(item),
                      )),
                      child: ListTile(title: Text(item)),
                    );
                  }).toList(),
                ),
              );
            },
          ),
        ),
        Expanded(
          child: DragTarget<String>(
            onWillAccept: (data) => true,
            onAccept: (data) {
              setState(() {
                _done.add(data);
                _todo.remove(data);
              });
            },
            builder: (context, candidate, rejected) {
              return Container(
                color: Colors.green[50],
                child: ListView(
                  children: _done.map((item) {
                    return ListTile(title: Text(item));
                  }).toList(),
                ),
              );
            },
          ),
        ),
      ],
    );
  }
}
```

## 3. 高级用法

### 3.1 LongPressDraggable

长按触发拖拽：

```dart
LongPressDraggable<String>(
  data: 'item',
  feedback: Container(
    padding: EdgeInsets.all(16),
    color: Colors.blue,
    child: Text('拖拽中'),
  ),
  child: Container(
    padding: EdgeInsets.all(16),
    color: Colors.grey[300],
    child: Text('长按拖拽'),
  ),
)
```

### 3.2 自定义拖拽反馈

```dart
Draggable<String>(
  data: 'item',
  feedbackOffset: Offset(0, -50),
  feedback: Opacity(
    opacity: 0.8,
    child: Transform.rotate(
      angle: 0.1,
      child: Container(
        padding: EdgeInsets.all(16),
        decoration: BoxDecoration(
          color: Colors.blue,
          boxShadow: [BoxShadow(blurRadius: 10, color: Colors.black26)],
        ),
        child: Text('拖拽中'),
      ),
    ),
  ),
  child: Card(child: ListTile(title: Text('项目'))),
)
```

### 3.3 拖拽到指定位置

```dart
class PositionDrag extends StatefulWidget {
  @override
  State<PositionDrag> createState() => _PositionDragState();
}

class _PositionDragState extends State<PositionDrag> {
  Offset _position = Offset(100, 100);

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Positioned(
          left: _position.dx,
          top: _position.dy,
          child: Draggable<Offset>(
            data: _position,
            onDragEnd: (details) {
              setState(() {
                _position = details.offset;
              });
            },
            feedback: Container(
              width: 80,
              height: 80,
              color: Colors.blue.withValues(alpha: 0.5),
            ),
            childWhenDragging: Container(),
            child: Container(
              width: 80,
              height: 80,
              color: Colors.blue,
            ),
          ),
        ),
      ],
    );
  }
}
```

### 3.4 多组件拖拽

```dart
class MultiDragExample extends StatefulWidget {
  @override
  State<MultiDragExample> createState() => _MultiDragExampleState();
}

class _MultiDragExampleState extends State<MultiDragExample> {
  final List<Color> _colors = [Colors.red, Colors.green, Colors.blue];
  final List<Color> _received = [];

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: _colors.map((color) {
            return Draggable<Color>(
              data: color,
              feedback: Container(
                width: 50,
                height: 50,
                color: color.withValues(alpha: 0.7),
              ),
              child: Container(
                width: 50,
                height: 50,
                color: color,
              ),
            );
          }).toList(),
        ),
        SizedBox(height: 50),
        DragTarget<Color>(
          onWillAccept: (color) => color != null,
          onAccept: (color) {
            setState(() {
              _received.add(color);
            });
          },
          builder: (context, candidate, rejected) {
            return Container(
              width: 200,
              height: 100,
              color: Colors.grey[300],
              child: Wrap(
                children: _received.map((color) {
                  return Container(
                    width: 30,
                    height: 30,
                    color: color,
                    margin: EdgeInsets.all(4),
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

## 4. 常见问题与解决方案

### 问题1：feedback 尺寸问题

```dart
// 问题：feedback 没有约束导致尺寸异常
Draggable(
  feedback: Text('很长的文本...'),  // 可能超出屏幕
)

// 解决：使用 Material 包裹并设置约束
Draggable(
  feedback: Material(
    child: Container(
      constraints: BoxConstraints(maxWidth: 200),
      child: Text('很长的文本...'),
    ),
  ),
  child: ...,
)
```

### 问题2：拖拽后原组件消失

```dart
// 问题：childWhenDragging 未设置
Draggable(
  feedback: Container(...),
  child: Container(...),  // 拖走后消失
)

// 解决：设置 childWhenDragging
Draggable(
  childWhenDragging: Container(color: Colors.grey[200]),
  feedback: Container(...),
  child: Container(...),
)
```

### 问题3：DragTarget 不接收

```dart
// 问题：onWillAccept 返回 false
DragTarget(
  onWillAccept: (data) => false,  // 永远不接受
  onAccept: (data) {...},
)

// 解决：返回 true
DragTarget(
  onWillAccept: (data) => true,  // 或根据条件判断
  onAccept: (data) {...},
)
```

## 5. Draggable vs 其他拖拽方案对比

| 方案 | 用途 | 特点 |
|------|------|------|
| **Draggable** | 通用拖拽 | Flutter 内置 |
| **LongPressDraggable** | 长按拖拽 | 防误触 |
| **ReorderableListView** | 列表排序 | 专用于列表 |
| **GestureDetector** | 自定义拖拽 | 需要手动处理 |

### 选择建议

```dart
// 列表排序 → ReorderableListView
ReorderableListView(...)

// 简单拖拽 → Draggable
Draggable(data: ..., child: ...)

// 需要长按触发 → LongPressDraggable
LongPressDraggable(data: ..., child: ...)
```

## 6. 最佳实践

### 6.1 封装拖拽组件

```dart
class DraggableCard extends StatelessWidget {
  final String id;
  final String title;
  final Widget Function(BuildContext) builder;

  const DraggableCard({
    required this.id,
    required this.title,
    required this.builder,
  });

  @override
  Widget build(BuildContext context) {
    return LongPressDraggable<String>(
      data: id,
      feedback: Material(
        elevation: 4,
        child: Card(child: Padding(
          padding: EdgeInsets.all(16),
          child: Text(title),
        )),
      ),
      child: builder(context),
    );
  }
}
```

### 6.2 拖拽状态管理

```dart
class DragState {
  bool isDragging = false;
  String? draggedItem;
  
  void startDrag(String item) {
    isDragging = true;
    draggedItem = item;
  }
  
  void endDrag() {
    isDragging = false;
    draggedItem = null;
  }
}
```

### 6.3 视觉反馈

```dart
DragTarget<String>(
  onWillAccept: (data) => true,
  builder: (context, candidate, rejected) {
    final isHovering = candidate.isNotEmpty;
    return AnimatedContainer(
      duration: Duration(milliseconds: 200),
      decoration: BoxDecoration(
        color: isHovering ? Colors.blue[100] : Colors.grey[200],
        border: isHovering 
            ? Border.all(color: Colors.blue, width: 2)
            : null,
      ),
      child: Center(child: Text('拖放到这里')),
    );
  },
)
```

## 总结

Draggable 是 Flutter 中实现拖拽功能的核心组件：

**核心要点**：
1. feedback 定义拖拽时的视觉效果
2. data 传递拖拽的数据
3. 配合 DragTarget 完成拖放

**最佳实践**：
1. 使用 LongPressDraggable 防误触
2. 设置 childWhenDragging
3. 提供清晰的视觉反馈

**常见场景**：
- 列表排序
- 分组拖拽
- 自由定位
- 数据传递

---

*最后更新: 2026-02-19*

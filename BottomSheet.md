# Flutter BottomSheet 组件用法详解

## 简介

BottomSheet 是从屏幕底部滑出的面板，常用于显示更多选项、详细操作或补充内容。Flutter 提供了 showModalBottomSheet 和 showBottomSheet 两种方式。

## 1. showModalBottomSheet 模态底部弹出

### 1.1 基本概念

showModalBottomSheet 的核心特点：
- 模态显示，有遮罩层
- 点击外部可关闭
- 支持拖拽关闭
- 常用于操作菜单、选项选择

### 1.2 基本语法

```dart
showModalBottomSheet(
  context: context,
  builder: (context) => Container(
    child: Text('BottomSheet 内容'),
  ),
)
```

### 1.3 主要参数

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `context` | `BuildContext` | - | 上下文（必需） |
| `builder` | `WidgetBuilder` | - | 构建器（必需） |
| `isScrollControlled` | `bool` | `false` | 是否自定义高度 |
| `isDismissible` | `bool` | `true` | 点击外部是否关闭 |
| `enableDrag` | `bool` | `true` | 是否支持拖拽关闭 |
| `backgroundColor` | `Color?` | - | 背景色 |
| `barrierColor` | `Color?` | - | 遮罩颜色 |
| `shape` | `ShapeBorder?` | - | 形状 |
| `constraints` | `BoxConstraints?` | - | 约束 |
| `useSafeArea` | `bool` | `false` | 是否使用安全区域 |
| `showDragHandle` | `bool` | `false` | 是否显示拖拽手柄 |

### 1.4 基础示例

#### 简单底部弹出

```dart
ElevatedButton(
  onPressed: () {
    showModalBottomSheet(
      context: context,
      builder: (context) => Container(
        height: 200,
        child: Center(child: Text('BottomSheet 内容')),
      ),
    );
  },
  child: Text('显示 BottomSheet'),
)
```

#### 自定义高度

```dart
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) => Container(
    height: MediaQuery.of(context).size.height * 0.5,
    child: Center(child: Text('占据屏幕 50% 高度')),
  ),
)

// 全屏高度
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) => Container(
    height: MediaQuery.of(context).size.height,
    child: Center(child: Text('全屏高度')),
  ),
)
```

#### 圆角样式

```dart
showModalBottomSheet(
  context: context,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.vertical(top: Radius.circular(20)),
  ),
  builder: (context) => Container(
    height: 200,
    child: Center(child: Text('圆角 BottomSheet')),
  ),
)
```

#### 拖拽手柄（Material 3）

```dart
showModalBottomSheet(
  context: context,
  showDragHandle: true,
  builder: (context) => Container(
    height: 200,
    child: Center(child: Text('带拖拽手柄')),
  ),
)
```

## 2. 常见 BottomSheet 场景

### 2.1 操作菜单

```dart
void showActionSheet(BuildContext context) {
  showModalBottomSheet(
    context: context,
    builder: (context) => SafeArea(
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          ListTile(
            leading: Icon(Icons.camera),
            title: Text('拍照'),
            onTap: () {
              Navigator.pop(context, 'camera');
            },
          ),
          ListTile(
            leading: Icon(Icons.photo),
            title: Text('从相册选择'),
            onTap: () {
              Navigator.pop(context, 'gallery');
            },
          ),
          ListTile(
            leading: Icon(Icons.delete),
            title: Text('删除', style: TextStyle(color: Colors.red)),
            onTap: () {
              Navigator.pop(context, 'delete');
            },
          ),
          Divider(),
          ListTile(
            title: Text('取消', textAlign: TextAlign.center),
            onTap: () => Navigator.pop(context),
          ),
        ],
      ),
    ),
  );
}

// 使用
final result = await showActionSheet(context);
print('选择: $result');
```

### 2.2 分享面板

```dart
void showShareSheet(BuildContext context) {
  showModalBottomSheet(
    context: context,
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.vertical(top: Radius.circular(16)),
    ),
    builder: (context) => Container(
      padding: EdgeInsets.all(16),
      child: Column(
        mainAxisSize: MainAxisSize.min,
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Text('分享到', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
          SizedBox(height: 24),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceAround,
            children: [
              _buildShareItem(Icons.message, '微信'),
              _buildShareItem(Icons.chat, 'QQ'),
              _buildShareItem(Icons.send, '微博'),
              _buildShareItem(Icons.link, '复制链接'),
            ],
          ),
          SizedBox(height: 24),
          SizedBox(
            width: double.infinity,
            child: OutlinedButton(
              onPressed: () => Navigator.pop(context),
              child: Text('取消'),
            ),
          ),
        ],
      ),
    ),
  );
}

Widget _buildShareItem(IconData icon, String label) {
  return Column(
    children: [
      Container(
        width: 56,
        height: 56,
        decoration: BoxDecoration(
          color: Colors.grey[200],
          borderRadius: BorderRadius.circular(12),
        ),
        child: Icon(icon, size: 28),
      ),
      SizedBox(height: 8),
      Text(label, style: TextStyle(fontSize: 12)),
    ],
  );
}
```

### 2.3 详情面板

```dart
void showDetailSheet(BuildContext context, Item item) {
  showModalBottomSheet(
    context: context,
    isScrollControlled: true,
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.vertical(top: Radius.circular(16)),
    ),
    builder: (context) => DraggableScrollableSheet(
      initialChildSize: 0.5,
      minChildSize: 0.25,
      maxChildSize: 0.9,
      expand: false,
      builder: (context, scrollController) {
        return Column(
          children: [
            Container(
              height: 200,
              child: Image.network(item.imageUrl, fit: BoxFit.cover),
            ),
            Expanded(
              child: SingleChildScrollView(
                controller: scrollController,
                child: Padding(
                  padding: EdgeInsets.all(16),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text(item.title, style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold)),
                      SizedBox(height: 8),
                      Text(item.description),
                      SizedBox(height: 16),
                      Row(
                        children: [
                          Text('¥${item.price}', style: TextStyle(fontSize: 24, color: Colors.red)),
                          Spacer(),
                          FilledButton(onPressed: () {}, child: Text('购买')),
                        ],
                      ),
                    ],
                  ),
                ),
              ),
            ),
          ],
        );
      },
    ),
  );
}
```

### 2.4 表单输入

```dart
void showFormSheet(BuildContext context) {
  final _controller = TextEditingController();
  
  showModalBottomSheet(
    context: context,
    isScrollControlled: true,
    builder: (context) => Padding(
      padding: EdgeInsets.only(
        bottom: MediaQuery.of(context).viewInsets.bottom,
      ),
      child: Container(
        padding: EdgeInsets.all(16),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(
              controller: _controller,
              autofocus: true,
              decoration: InputDecoration(
                labelText: '输入评论',
                border: OutlineInputBorder(),
              ),
            ),
            SizedBox(height: 16),
            Row(
              mainAxisAlignment: MainAxisAlignment.end,
              children: [
                TextButton(
                  onPressed: () => Navigator.pop(context),
                  child: Text('取消'),
                ),
                FilledButton(
                  onPressed: () {
                    Navigator.pop(context, _controller.text);
                  },
                  child: Text('发送'),
                ),
              ],
            ),
          ],
        ),
      ),
    ),
  );
}
```

### 2.5 选择器

```dart
Future<String?> showPickerSheet<T>({
  required BuildContext context,
  required String title,
  required List<String> options,
  String? selected,
}) async {
  return showModalBottomSheet<String>(
    context: context,
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.vertical(top: Radius.circular(16)),
    ),
    builder: (context) => Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Padding(
          padding: EdgeInsets.all(16),
          child: Text(title, style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
        ),
        Divider(height: 1),
        ConstrainedBox(
          constraints: BoxConstraints(maxHeight: 300),
          child: ListView.builder(
            shrinkWrap: true,
            itemCount: options.length,
            itemBuilder: (context, index) {
              final option = options[index];
              final isSelected = option == selected;
              return ListTile(
                title: Text(option),
                trailing: isSelected ? Icon(Icons.check, color: Colors.blue) : null,
                onTap: () => Navigator.pop(context, option),
              );
            },
          ),
        ),
        Divider(height: 1),
        ListTile(
          title: Text('取消', textAlign: TextAlign.center),
          onTap: () => Navigator.pop(context),
        ),
      ],
    ),
  );
}

// 使用
final city = await showPickerSheet(
  context: context,
  title: '选择城市',
  options: ['北京', '上海', '广州', '深圳'],
  selected: '上海',
);
```

## 3. showBottomSheet 非模态底部弹出

### 3.1 基本概念

showBottomSheet 的核心特点：
- 非模态，无遮罩层
- 不会阻止其他交互
- 需要手动关闭
- 常用于音乐播放器、持久面板

### 3.2 基本语法

```dart
final scaffold = Scaffold.of(context);
scaffold.showBottomSheet(
  (context) => Container(
    height: 100,
    child: Text('非模态 BottomSheet'),
  ),
)
```

### 3.3 基础示例

```dart
class PersistentBottomSheet extends StatefulWidget {
  @override
  _PersistentBottomSheetState createState() => _PersistentBottomSheetState();
}

class _PersistentBottomSheetState extends State<PersistentBottomSheet> {
  PersistentBottomSheetController? _controller;

  void _showSheet() {
    _controller = Scaffold.of(context).showBottomSheet(
      (context) => Container(
        height: 100,
        color: Colors.blue,
        child: Row(
          mainAxisAlignment: MainAxisAlignment.spaceAround,
          children: [
            IconButton(icon: Icon(Icons.play_arrow), onPressed: () {}),
            IconButton(icon: Icon(Icons.pause), onPressed: () {}),
            IconButton(icon: Icon(Icons.stop), onPressed: () {}),
            IconButton(
              icon: Icon(Icons.close),
              onPressed: () => _controller?.close(),
            ),
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: ElevatedButton(
          onPressed: _showSheet,
          child: Text('显示持久面板'),
        ),
      ),
    );
  }
}
```

## 4. DraggableScrollableSheet 可拖拽滚动面板

### 4.1 基本概念

DraggableScrollableSheet 的核心特点：
- 支持拖拽调整高度
- 支持最小、初始、最大高度
- 内容可滚动
- 常用于地图详情、商品详情

### 4.2 基本语法

```dart
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) => DraggableScrollableSheet(
    initialChildSize: 0.5,
    minChildSize: 0.25,
    maxChildSize: 0.9,
    builder: (context, scrollController) => Container(
      child: SingleChildScrollView(
        controller: scrollController,
        child: Text('内容'),
      ),
    ),
  ),
)
```

### 4.3 主要属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `initialChildSize` | `double` | 初始高度比例（0-1） |
| `minChildSize` | `double` | 最小高度比例 |
| `maxChildSize` | `double` | 最大高度比例 |
| `expand` | `bool` | 是否填充可用空间 |
| `snap` | `bool` | 是否吸附到 snapSizes |
| `snapSizes` | `List<double>?` | 吸附点列表 |

### 4.4 基础示例

```dart
void showDraggableSheet(BuildContext context) {
  showModalBottomSheet(
    context: context,
    isScrollControlled: true,
    backgroundColor: Colors.transparent,
    builder: (context) => DraggableScrollableSheet(
      initialChildSize: 0.5,
      minChildSize: 0.2,
      maxChildSize: 0.9,
      builder: (context, scrollController) {
        return Container(
          decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.vertical(top: Radius.circular(20)),
          ),
          child: Column(
            children: [
              Container(
                width: 40,
                height: 4,
                margin: EdgeInsets.symmetric(vertical: 12),
                decoration: BoxDecoration(
                  color: Colors.grey[300],
                  borderRadius: BorderRadius.circular(2),
                ),
              ),
              Expanded(
                child: ListView.builder(
                  controller: scrollController,
                  itemCount: 50,
                  itemBuilder: (context, index) {
                    return ListTile(title: Text('Item $index'));
                  },
                ),
              ),
            ],
          ),
        );
      },
    ),
  );
}
```

### 4.5 地图详情面板

```dart
class MapDetailSheet extends StatelessWidget {
  final List<Place> places;

  const MapDetailSheet({required this.places, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return DraggableScrollableSheet(
      initialChildSize: 0.3,
      minChildSize: 0.1,
      maxChildSize: 0.8,
      builder: (context, scrollController) {
        return Container(
          decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.vertical(top: Radius.circular(16)),
          ),
          child: CustomScrollView(
            controller: scrollController,
            slivers: [
              SliverToBoxAdapter(
                child: Container(
                  width: 40,
                  height: 4,
                  margin: EdgeInsets.symmetric(vertical: 12),
                  decoration: BoxDecoration(
                    color: Colors.grey[300],
                    borderRadius: BorderRadius.circular(2),
                  ),
                ),
              ),
              SliverToBoxAdapter(
                child: Padding(
                  padding: EdgeInsets.all(16),
                  child: Text(
                    '附近地点',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                  ),
                ),
              ),
              SliverList(
                delegate: SliverChildBuilderDelegate(
                  (context, index) {
                    final place = places[index];
                    return ListTile(
                      leading: Icon(Icons.place),
                      title: Text(place.name),
                      subtitle: Text(place.address),
                      trailing: Text('${place.distance}m'),
                    );
                  },
                  childCount: places.length,
                ),
              ),
            ],
          ),
        );
      },
    );
  }
}
```

## 5. 高级用法

### 5.1 嵌套 BottomSheet

```dart
void showNestedSheet(BuildContext context) {
  showModalBottomSheet(
    context: context,
    builder: (context1) => Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        ListTile(
          title: Text('选项 1'),
          onTap: () {
            showModalBottomSheet(
              context: context,
              builder: (context2) => Container(
                height: 200,
                child: Center(child: Text('子 BottomSheet')),
              ),
            );
          },
        ),
        ListTile(title: Text('选项 2')),
      ],
    ),
  );
}
```

### 5.2 带动画的 BottomSheet

```dart
void showAnimatedSheet(BuildContext context) {
  showGeneralDialog(
    context: context,
    barrierDismissible: true,
    barrierLabel: '关闭',
    barrierColor: Colors.black54,
    transitionDuration: Duration(milliseconds: 300),
    pageBuilder: (context, animation, secondaryAnimation) {
      return Align(
        alignment: Alignment.bottomCenter,
        child: AnimatedContainer(
          duration: Duration(milliseconds: 300),
          height: 300,
          decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.vertical(top: Radius.circular(20)),
          ),
          child: Center(child: Text('动画 BottomSheet')),
        ),
      );
    },
    transitionBuilder: (context, animation, secondaryAnimation, child) {
      return SlideTransition(
        position: Tween<Offset>(
          begin: Offset(0, 1),
          end: Offset.zero,
        ).animate(animation),
        child: child,
      );
    },
  );
}
```

### 5.3 自定义返回值

```dart
Future<T?> showCustomSheet<T>({
  required BuildContext context,
  required Widget Function(BuildContext) builder,
}) async {
  return showModalBottomSheet<T>(
    context: context,
    builder: builder,
  );
}

// 使用
final result = await showCustomSheet<int>(
  context: context,
  builder: (context) => Column(
    mainAxisSize: MainAxisSize.min,
    children: [
      ListTile(title: Text('选项 1'), onTap: () => Navigator.pop(context, 1)),
      ListTile(title: Text('选项 2'), onTap: () => Navigator.pop(context, 2)),
    ],
  ),
);
```

## 6. 常见问题与解决方案

### 问题1：键盘弹出时被遮挡

```dart
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) => Padding(
    padding: EdgeInsets.only(
      bottom: MediaQuery.of(context).viewInsets.bottom,
    ),
    child: Container(
      child: TextField(...),
    ),
  ),
)
```

### 问题2：BottomSheet 高度自适应

```dart
showModalBottomSheet(
  context: context,
  builder: (context) => SingleChildScrollView(
    child: Column(
      mainAxisSize: MainAxisSize.min,
      children: [...],
    ),
  ),
)
```

### 问题3：圆角背景显示灰色

```dart
showModalBottomSheet(
  context: context,
  backgroundColor: Colors.transparent,
  builder: (context) => Container(
    decoration: BoxDecoration(
      color: Colors.white,
      borderRadius: BorderRadius.vertical(top: Radius.circular(16)),
    ),
    child: Container(),
  ),
)
```

### 问题4：无法拖拽关闭

```dart
showModalBottomSheet(
  context: context,
  enableDrag: true,  // 确保开启
  builder: (context) => Container(),
)
```

### 问题5：点击外部不关闭

```dart
showModalBottomSheet(
  context: context,
  isDismissible: true,  // 确保开启
  builder: (context) => Container(),
)
```

## 7. 最佳实践

### 7.1 统一 BottomSheet 样式

```dart
class AppBottomSheet {
  static Future<T?> show<T>({
    required BuildContext context,
    required Widget child,
    double? height,
    bool isScrollControlled = false,
  }) {
    return showModalBottomSheet<T>(
      context: context,
      isScrollControlled: height != null || isScrollControlled,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.vertical(top: Radius.circular(16)),
      ),
      builder: (context) => Container(
        height: height,
        child: child,
      ),
    );
  }
}
```

### 7.2 使用 DraggableScrollableSheet 处理滚动

```dart
// 内容可能很长时使用 DraggableScrollableSheet
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  builder: (context) => DraggableScrollableSheet(
    builder: (context, scrollController) => SingleChildScrollView(
      controller: scrollController,
      child: Column(...),
    ),
  ),
)
```

## 总结

BottomSheet 是 Flutter 中常用的底部弹出组件：

**showModalBottomSheet**：
- 模态显示，有遮罩
- 点击外部可关闭
- 最常用的方式

**showBottomSheet**：
- 非模态，无遮罩
- 需要手动关闭
- 用于持久面板

**DraggableScrollableSheet**：
- 可拖拽调整高度
- 内容可滚动
- 用于复杂交互场景

**最佳实践**：
1. 使用 isScrollControlled 控制高度
2. 处理键盘遮挡问题
3. 统一 BottomSheet 样式
4. 合理选择模态/非模态

---

*最后更新: 2026-02-18*

# Flutter 性能优化 - 初学者指南

## 目录
1. [性能分析工具](#性能分析工具)
2. [Widget 优化](#widget-优化)
3. [列表优化](#列表优化)
4. [图片优化](#图片优化)
5. [动画优化](#动画优化)
6. [内存优化](#内存优化)
7. [包体积优化](#包体积优化)

## 性能分析工具

### Flutter DevTools

```bash
# 启动 DevTools
flutter pub global activate devtools
flutter pub global run devtools

# 或者在 VS Code 中使用 Flutter DevTools 扩展
```

### Performance Overlay

```dart
MaterialApp(
  showPerformanceOverlay: true,  // 显示性能叠加层
  home: MyApp(),
);
```

### Dart DevTools 功能

```
1. Flutter Inspector - 检查 Widget 树
2. Performance - 分析帧率和性能
3. Memory - 分析内存使用
4. CPU Profiler - CPU 分析
5. Network - 网络请求分析
6. Logging - 日志查看
```

### 常用调试命令

```dart
// 打印 Widget 重建信息
void main() {
  debugPrintRebuildDirtyWidgets = true;
  runApp(MyApp());
}

// 打印构建时间
class TimedBuilder extends StatelessWidget {
  final Widget child;
  final String label;
  
  const TimedBuilder({required this.child, required this.label});
  
  @override
  Widget build(BuildContext context) {
    final stopwatch = Stopwatch()..start();
    final widget = child;
    stopwatch.stop();
    debugPrint('$label build time: ${stopwatch.elapsedMilliseconds}ms');
    return widget;
  }
}
```

## Widget 优化

### 使用 const 构造函数

```dart
// ❌ 不推荐：每次都创建新实例
class MyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text('Hello');
  }
}

// ✅ 推荐：使用 const
class MyWidget extends StatelessWidget {
  const MyWidget({Key? key}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return const Text('Hello');
  }
}

// ✅ 在使用时也加 const
const Text('Hello')
const Icon(Icons.add)
const EdgeInsets.all(16)
```

### 避免不必要的重建

```dart
// ❌ 错误：整个页面重建
class BadPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final counter = context.watch<Counter>();
    
    return Scaffold(
      appBar: AppBar(title: Text('Counter')),  // 也会重建
      body: Center(
        child: Text('${counter.value}'),  // 只有这里需要更新
      ),
    );
  }
}

// ✅ 正确：只重建需要的部分
class GoodPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      body: Center(
        child: Consumer<Counter>(
          builder: (context, counter, child) {
            return Text('${counter.value}');
          },
        ),
      ),
    );
  }
}
```

### 使用Selector 精确监听

```dart
// ❌ 监听整个对象
context.watch<User>();

// ✅ 只监听需要的属性
final name = context.select<User, String>((user) => user.name);

// 使用Selector Widget
Selector<User, String>(
  selector: (context, user) => user.name,
  builder: (context, name, child) {
    return Text(name);
  },
)
```

### 拆分大 Widget

```dart
// ❌ 大型 Widget 难以维护和优化
class BigWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // 100+ 行代码...
        // 任何状态变化都会重建整个Widget
      ],
    );
  }
}

// ✅ 拆分为小组件
class OptimizedWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        HeaderSection(),    // 独立Widget
        ContentSection(),   // 不会相互影响
        FooterSection(),
      ],
    );
  }
}
```

## 列表优化

### ListView.builder

```dart
// ❌ 错误：一次性加载所有项
ListView(
  children: List.generate(1000, (i) => ListItem(i)),
)

// ✅ 正确：懒加载
ListView.builder(
  itemCount: 1000,
  itemBuilder: (context, index) => ListItem(index),
)
```

### 使用 const 列表项

```dart
class ListItem extends StatelessWidget {
  final String title;
  
  const ListItem({Key? key, required this.title}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return ListTile(title: Text(title));
  }
}

ListView.builder(
  itemBuilder: (context, index) => const ListItem(title: 'Item'),
)
```

### 避免列表项重建

```dart
// ❌ 每次滚动都可能重建
class BadList extends StatelessWidget {
  final List<String> items;
  
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: items.length,
      itemBuilder: (context, index) {
        return ListTile(
          title: Text(items[index]),
          onTap: () => print(index),
        );
      },
    );
  }
}

// ✅ 使用 const 和 key
class GoodList extends StatelessWidget {
  final List<String> items;
  
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: items.length,
      itemBuilder: (context, index) {
        return _ListItem(
          key: ValueKey(items[index]),
          title: items[index],
        );
      },
    );
  }
}

class _ListItem extends StatelessWidget {
  final String title;
  
  const _ListItem({Key? key, required this.title}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return ListTile(title: Text(title));
  }
}
```

### 使用 itemExtent

```dart
// 当列表项高度固定时，使用 itemExtent 提高性能
ListView.builder(
  itemCount: 1000,
  itemExtent: 50,  // 固定高度
  itemBuilder: (context, index) => ListTile(title: Text('Item $index')),
)

// 或使用 prototypeItem
ListView.builder(
  itemCount: 1000,
  prototypeItem: ListTile(title: Text('Prototype')),
  itemBuilder: (context, index) => ListTile(title: Text('Item $index')),
)
```

### 分页加载

```dart
class PaginatedList extends StatefulWidget {
  @override
  _PaginatedListState createState() => _PaginatedListState();
}

class _PaginatedListState extends State<PaginatedList> {
  final ScrollController _controller = ScrollController();
  List<String> items = [];
  bool isLoading = false;
  int page = 1;
  
  @override
  void initState() {
    super.initState();
    _loadData();
    _controller.addListener(_onScroll);
  }
  
  void _onScroll() {
    if (_controller.position.pixels >=
        _controller.position.maxScrollExtent - 200) {
      _loadData();
    }
  }
  
  Future<void> _loadData() async {
    if (isLoading) return;
    
    setState(() => isLoading = true);
    
    final newItems = await fetchItems(page++);
    
    setState(() {
      items.addAll(newItems);
      isLoading = false;
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      controller: _controller,
      itemCount: items.length + (isLoading ? 1 : 0),
      itemBuilder: (context, index) {
        if (index == items.length) {
          return Center(child: CircularProgressIndicator());
        }
        return ListTile(title: Text(items[index]));
      },
    );
  }
}
```

## 图片优化

### 使用 cached_network_image

```yaml
dependencies:
  cached_network_image: ^3.3.0
```

```dart
// ❌ 直接使用 NetworkImage
Image.network('https://example.com/image.jpg')

// ✅ 使用缓存
CachedNetworkImage(
  imageUrl: 'https://example.com/image.jpg',
  placeholder: (context, url) => CircularProgressIndicator(),
  errorWidget: (context, url, error) => Icon(Icons.error),
  memCacheWidth: 300,  // 内存缓存宽度
  memCacheHeight: 300,
)
```

### 图片尺寸优化

```dart
// 指定图片尺寸
Image.network(
  'https://example.com/image.jpg',
  cacheWidth: 300,  // 解码后的宽度
  cacheHeight: 300,
)

// 使用 ResizeImage
Image(
  image: ResizeImage(
    NetworkImage('https://example.com/image.jpg'),
    width: 300,
    height: 300,
  ),
)
```

### 预加载图片

```dart
class ImagePreloadDemo extends StatefulWidget {
  @override
  _ImagePreloadDemoState createState() => _ImagePreloadDemoState();
}

class _ImagePreloadDemoState extends State<ImagePreloadDemo> {
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    
    // 预加载图片
    precacheImage(NetworkImage('https://example.com/image.jpg'), context);
  }
  
  @override
  Widget build(BuildContext context) {
    return Image.network('https://example.com/image.jpg');
  }
}
```

## 动画优化

### 使用隐式动画

```dart
// ❌ 显式动画需要手动管理
class ExplicitAnimation extends StatefulWidget {
  @override
  _ExplicitAnimationState createState() => _ExplicitAnimationState();
}

class _ExplicitAnimationState extends State<ExplicitAnimation>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  
  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    );
  }
  
  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return FadeTransition(
      opacity: _controller,
      child: Container(),
    );
  }
}

// ✅ 隐式动画更简单高效
AnimatedOpacity(
  opacity: _visible ? 1.0 : 0.0,
  duration: Duration(seconds: 1),
  child: Container(),
)
```

### 避免动画中重建

```dart
// ❌ 每帧都重建
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    return Container(
      child: Text('This rebuilds every frame'),  // 不必要的重建
    );
  },
)

// ✅ 使用 child 参数
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    return Transform.rotate(
      angle: _controller.value * 2 * pi,
      child: child,  // 不会重建
    );
  },
  child: Text('This does not rebuild'),
)
```

## 内存优化

### 及时释放资源

```dart
class ResourceWidget extends StatefulWidget {
  @override
  _ResourceWidgetState createState() => _ResourceWidgetState();
}

class _ResourceWidgetState extends State<ResourceWidget> {
  StreamSubscription? _subscription;
  TextEditingController? _controller;
  AnimationController? _animationController;
  
  @override
  void initState() {
    super.initState();
    _controller = TextEditingController();
    _animationController = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    );
  }
  
  @override
  void dispose() {
    // 必须释放所有资源
    _subscription?.cancel();
    _controller?.dispose();
    _animationController?.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}
```

### 避免内存泄漏

```dart
// ❌ 异步操作后未检查 mounted
Future<void> _loadData() async {
  final data = await fetchData();
  setState(() {  // Widget 可能已销毁
    _data = data;
  });
}

// ✅ 检查 mounted
Future<void> _loadData() async {
  final data = await fetchData();
  if (mounted) {
    setState(() {
      _data = data;
    });
  }
}
```

### 使用 AutomaticKeepAliveClientMixin

```dart
class KeepAliveTab extends StatefulWidget {
  @override
  _KeepAliveTabState createState() => _KeepAliveTabState();
}

class _KeepAliveTabState extends State<KeepAliveTab>
    with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;  // 保持状态
  
  @override
  Widget build(BuildContext context) {
    super.build(context);  // 必须调用
    return ListView.builder(...);
  }
}
```

## 包体积优化

### 移除未使用的代码

```bash
# 分析代码大小
flutter build apk --analyze-size

# 使用 --tree-shake-icons
flutter build apk --tree-shake-icons
```

### 图片资源优化

```yaml
# pubspec.yaml
flutter:
  assets:
    - assets/images/
```

```dart
// 使用矢量图替代位图
Icon(Icons.home)  // 使用图标字体

// 或使用 SVG
SvgPicture.asset('assets/image.svg')
```

### 延迟加载

```dart
// 延迟导入大型库
import 'package:flutter/material.dart';

void main() {
  // 只有需要时才加载
  if (needFeature) {
    import('heavy_feature.dart').then((module) {
      module.initialize();
    });
  }
}
```

### 按需加载字体

```yaml
# pubspec.yaml
flutter:
  fonts:
    - family: Roboto
      fonts:
        - asset: fonts/Roboto-Regular.ttf
        - asset: fonts/Roboto-Bold.ttf
          weight: 700
```

---

*最后更新: 2026-02-19*

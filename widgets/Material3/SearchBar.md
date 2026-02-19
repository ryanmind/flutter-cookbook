# Flutter SearchBar 组件用法详解

## 简介

SearchBar 是 Material 3 的搜索栏组件，提供了现代化的搜索界面。配合 SearchAnchor 使用，可以实现完整的搜索体验。

## 1. 初级用法

### 1.1 基本概念

SearchBar 的核心特点：
- Material 3 设计风格
- 支持搜索建议
- 支持搜索历史
- 可自定义样式

### 1.2 基本语法

```dart
SearchBar(
  hintText: '搜索',
  onSubmitted: (value) {
    print('搜索: $value');
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `controller` | `TextEditingController?` | - | 文本控制器 |
| `focusNode` | `FocusNode?` | - | 焦点节点 |
| `hintText` | `String?` | - | 提示文字 |
| `leading` | `Widget?` | - | 前导组件 |
| `trailing` | `List<Widget>?` | - | 尾随组件列表 |
| `onSubmitted` | `ValueChanged<String>?` | - | 提交回调 |
| `onChanged` | `ValueChanged<String>?` | - | 文本变化回调 |
| ` onTap` | `VoidCallback?` | - | 点击回调 |
| `textCapitalization` | `TextCapitalization` | `TextCapitalization.none` | 大写方式 |
| `textInputAction` | `TextInputAction?` | - | 键盘操作按钮 |
| `keyboardType` | `TextInputType?` | - | 键盘类型 |
| `autofocus` | `bool` | `false` | 自动聚焦 |
| `constraints` | `BoxConstraints?` | - | 约束条件 |
| `elevation` | `WidgetStateProperty<double?>?` | - | 阴影高度 |
| `backgroundColor` | `WidgetStateProperty<Color?>?` | - | 背景色 |
| `overlayColor` | `WidgetStateProperty<Color?>?` | - | 覆盖层颜色 |
| `shadowColor` | `WidgetStateProperty<Color?>?` | - | 阴影颜色 |
| `surfaceTintColor` | `WidgetStateProperty<Color?>?` | - | 表面着色 |
| `shape` | `WidgetStateProperty<ShapeBorder?>?` | - | 形状 |
| `padding` | `WidgetStateProperty<EdgeInsetsGeometry?>?` | - | 内边距 |
| `side` | `WidgetStateProperty<BorderSide?>?` | - | 边框 |
| `textStyle` | `WidgetStateProperty<TextStyle?>?` | - | 文字样式 |
| `hintStyle` | `WidgetStateProperty<TextStyle?>?` | - | 提示文字样式 |

### 1.4 基础示例

#### 简单搜索栏

```dart
SearchBar(
  hintText: '搜索内容',
  leading: Icon(Icons.search),
  onSubmitted: (value) {
    print('搜索: $value');
  },
)
```

#### 带清除按钮

```dart
class SearchBarWithClear extends StatefulWidget {
  @override
  _SearchBarWithClearState createState() => _SearchBarWithClearState();
}

class _SearchBarWithClearState extends State<SearchBarWithClear> {
  final TextEditingController _controller = TextEditingController();
  bool _hasText = false;

  @override
  void initState() {
    super.initState();
    _controller.addListener(() {
      setState(() {
        _hasText = _controller.text.isNotEmpty;
      });
    });
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return SearchBar(
      controller: _controller,
      hintText: '搜索',
      leading: Icon(Icons.search),
      trailing: [
        if (_hasText)
          IconButton(
            icon: Icon(Icons.clear),
            onPressed: () {
              _controller.clear();
            },
          ),
      ],
      onSubmitted: (value) {
        print('搜索: $value');
      },
    );
  }
}
```

#### 全宽搜索栏

```dart
SearchBar(
  hintText: '搜索',
  leading: Icon(Icons.search),
  constraints: BoxConstraints(
    minWidth: double.infinity,
  ),
  onSubmitted: (value) {},
)
```

## 2. SearchAnchor 搜索锚点

### 2.1 基本用法

```dart
SearchAnchor(
  builder: (context, controller) {
    return SearchBar(
      controller: controller,
      hintText: '搜索',
      leading: Icon(Icons.search),
      onTap: () => controller.openView(),
      onChanged: (_) => controller.openView(),
    );
  },
  suggestionsBuilder: (context, controller) {
    return [
      ListTile(
        title: Text('搜索建议 1'),
        onTap: () {
          controller.closeView('搜索建议 1');
        },
      ),
      ListTile(
        title: Text('搜索建议 2'),
        onTap: () {
          controller.closeView('搜索建议 2');
        },
      ),
    ];
  },
)
```

### 2.2 SearchAnchor 属性

| 属性 | 类型 | 说明 |
|------|------|------|
| `builder` | `SearchAnchorBuilder` | 搜索栏构建器 |
| `suggestionsBuilder` | `SuggestionsBuilder` | 建议列表构建器 |
| `viewBackgroundColor` | `Color?` | 建议视图背景色 |
| `viewElevation` | `double?` | 建议视图阴影 |
| `viewShape` | `ShapeBorder?` | 建议视图形状 |
| `viewHintText` | `String?` | 建议视图提示文字 |
| `viewLeading` | `Widget?` | 建议视图前导组件 |
| `viewTrailing` | `List<Widget>?` | 建议视图尾随组件 |
| `viewSide` | `BorderSide?` | 建议视图边框 |
| `isFullScreen` | `bool` | 是否全屏显示 |

### 2.3 动态搜索建议

```dart
class SearchWithSuggestions extends StatefulWidget {
  final List<String> items;

  const SearchWithSuggestions({
    required this.items,
    Key? key,
  }) : super(key: key);

  @override
  _SearchWithSuggestionsState createState() => _SearchWithSuggestionsState();
}

class _SearchWithSuggestionsState extends State<SearchWithSuggestions> {
  List<String> _suggestions = [];

  List<String> _filter(String query) {
    if (query.isEmpty) return widget.items.take(5).toList();
    return widget.items
        .where((item) => item.toLowerCase().contains(query.toLowerCase()))
        .take(10)
        .toList();
  }

  @override
  Widget build(BuildContext context) {
    return SearchAnchor(
      builder: (context, controller) {
        return SearchBar(
          controller: controller,
          hintText: '搜索',
          leading: Icon(Icons.search),
          onTap: () => controller.openView(),
          onChanged: (query) {
            setState(() {
              _suggestions = _filter(query);
            });
            controller.openView();
          },
        );
      },
      suggestionsBuilder: (context, controller) {
        return _suggestions.map((item) {
          return ListTile(
            title: Text(item),
            onTap: () {
              controller.closeView(item);
            },
          );
        }).toList();
      },
    );
  }
}
```

## 3. 中级用法

### 3.1 自定义样式

```dart
SearchBar(
  hintText: '搜索',
  leading: Icon(Icons.search),
  backgroundColor: WidgetStateProperty.all(Colors.grey[100]),
  shape: WidgetStateProperty.all(
    RoundedRectangleBorder(borderRadius: BorderRadius.circular(30)),
  ),
  elevation: WidgetStateProperty.all(0),
  side: WidgetStateProperty.all(
    BorderSide(color: Colors.grey[300]!),
  ),
  textStyle: WidgetStateProperty.all(
    TextStyle(fontSize: 16, color: Colors.black),
  ),
  hintStyle: WidgetStateProperty.all(
    TextStyle(fontSize: 16, color: Colors.grey),
  ),
  onSubmitted: (value) {},
)
```

### 3.2 带语音搜索

```dart
SearchBar(
  hintText: '搜索',
  leading: Icon(Icons.search),
  trailing: [
    IconButton(
      icon: Icon(Icons.mic),
      onPressed: () {
        // 启动语音识别
      },
    ),
  ],
  onSubmitted: (value) {},
)
```

### 3.3 搜索历史

```dart
class SearchWithHistory extends StatefulWidget {
  @override
  _SearchWithHistoryState createState() => _SearchWithHistoryState();
}

class _SearchWithHistoryState extends State<SearchWithHistory> {
  final List<String> _history = [];
  final TextEditingController _controller = TextEditingController();

  void _addToHistory(String query) {
    if (query.isEmpty) return;
    setState(() {
      _history.remove(query);
      _history.insert(0, query);
      if (_history.length > 10) {
        _history.removeLast();
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return SearchAnchor(
      builder: (context, controller) {
        return SearchBar(
          controller: _controller,
          hintText: '搜索',
          leading: Icon(Icons.search),
          onTap: () => controller.openView(),
          onChanged: (_) => controller.openView(),
          onSubmitted: (value) {
            _addToHistory(value);
          },
        );
      },
      suggestionsBuilder: (context, controller) {
        if (_controller.text.isEmpty) {
          return _history.map((item) {
            return ListTile(
              leading: Icon(Icons.history),
              title: Text(item),
              trailing: IconButton(
                icon: Icon(Icons.close, size: 18),
                onPressed: () {
                  setState(() {
                    _history.remove(item);
                  });
                },
              ),
              onTap: () {
                controller.closeView(item);
                _controller.text = item;
              },
            );
          }).toList();
        }
        
        // 根据输入过滤建议
        return _history
            .where((h) => h.contains(_controller.text))
            .map((item) => ListTile(
                  leading: Icon(Icons.history),
                  title: Text(item),
                  onTap: () {
                    controller.closeView(item);
                    _controller.text = item;
                  },
                ))
            .toList();
      },
    );
  }
}
```

### 3.4 搜索过滤器

```dart
class SearchWithFilter extends StatefulWidget {
  @override
  _SearchWithFilterState createState() => _SearchWithFilterState();
}

class _SearchWithFilterState extends State<SearchWithFilter> {
  String _selectedFilter = 'all';

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        SearchBar(
          hintText: '搜索',
          leading: Icon(Icons.search),
          trailing: [
            PopupMenuButton<String>(
              icon: Icon(Icons.filter_list),
              onSelected: (value) {
                setState(() => _selectedFilter = value);
              },
              itemBuilder: (context) => [
                PopupMenuItem(value: 'all', child: Text('全部')),
                PopupMenuItem(value: 'images', child: Text('图片')),
                PopupMenuItem(value: 'videos', child: Text('视频')),
                PopupMenuItem(value: 'documents', child: Text('文档')),
              ],
            ),
          ],
          onSubmitted: (value) {
            print('搜索: $value, 过滤: $_selectedFilter');
          },
        ),
        SizedBox(height: 8),
        Row(
          children: [
            Chip(
              label: Text('当前过滤: $_selectedFilter'),
              onDeleted: () {
                setState(() => _selectedFilter = 'all');
              },
            ),
          ],
        ),
      ],
    );
  }
}
```

## 4. 高级用法

### 4.1 AppBar 搜索

```dart
class SearchAppBar extends StatefulWidget implements PreferredSizeWidget {
  final String title;
  final ValueChanged<String>? onSearch;

  const SearchAppBar({
    required this.title,
    this.onSearch,
    Key? key,
  }) : super(key: key);

  @override
  Size get preferredSize => Size.fromHeight(56);

  @override
  _SearchAppBarState createState() => _SearchAppBarState();
}

class _SearchAppBarState extends State<SearchAppBar> {
  bool _isSearching = false;
  final TextEditingController _controller = TextEditingController();

  @override
  Widget build(BuildContext context) {
    if (_isSearching) {
      return AppBar(
        leading: IconButton(
          icon: Icon(Icons.arrow_back),
          onPressed: () {
            setState(() {
              _isSearching = false;
              _controller.clear();
            });
          },
        ),
        title: TextField(
          controller: _controller,
          autofocus: true,
          decoration: InputDecoration(
            hintText: '搜索...',
            border: InputBorder.none,
          ),
          onSubmitted: (value) {
            widget.onSearch?.call(value);
          },
        ),
        actions: [
          IconButton(
            icon: Icon(Icons.clear),
            onPressed: () => _controller.clear(),
          ),
        ],
      );
    }

    return AppBar(
      title: Text(widget.title),
      actions: [
        IconButton(
          icon: Icon(Icons.search),
          onPressed: () {
            setState(() => _isSearching = true);
          },
        ),
      ],
    );
  }
}

// 使用
Scaffold(
  appBar: SearchAppBar(
    title: '我的应用',
    onSearch: (query) {
      print('搜索: $query');
    },
  ),
  body: Center(child: Text('内容')),
)
```

### 4.2 全屏搜索

```dart
class FullScreenSearch extends StatelessWidget {
  final List<String> suggestions;

  const FullScreenSearch({
    required this.suggestions,
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return SearchAnchor(
      isFullScreen: true,
      builder: (context, controller) {
        return SearchBar(
          hintText: '搜索',
          leading: Icon(Icons.search),
          onTap: () => controller.openView(),
        );
      },
      suggestionsBuilder: (context, controller) {
        return suggestions.map((item) {
          return ListTile(
            title: Text(item),
            onTap: () => controller.closeView(item),
          );
        }).toList();
      },
    );
  }
}
```

### 4.3 搜索结果页面

```dart
class SearchResultsPage extends StatefulWidget {
  final String query;

  const SearchResultsPage({
    required this.query,
    Key? key,
  }) : super(key: key);

  @override
  _SearchResultsPageState createState() => _SearchResultsPageState();
}

class _SearchResultsPageState extends State<SearchResultsPage> {
  List<String> _results = [];
  bool _isLoading = true;

  @override
  void initState() {
    super.initState();
    _search(widget.query);
  }

  Future<void> _search(String query) async {
    setState(() => _isLoading = true);
    
    // 模拟搜索
    await Future.delayed(Duration(seconds: 1));
    
    setState(() {
      _results = List.generate(20, (i) => '结果 $i for "$query"');
      _isLoading = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('搜索结果'),
      ),
      body: Column(
        children: [
          SearchBar(
            hintText: '搜索',
            leading: Icon(Icons.search),
            initialQuery: widget.query,
            onSubmitted: (value) {
              Navigator.pushReplacement(
                context,
                MaterialPageRoute(
                  builder: (context) => SearchResultsPage(query: value),
                ),
              );
            },
          ),
          Expanded(
            child: _isLoading
                ? Center(child: CircularProgressIndicator())
                : ListView.builder(
                    itemCount: _results.length,
                    itemBuilder: (context, index) {
                      return ListTile(
                        title: Text(_results[index]),
                        onTap: () {},
                      );
                    },
                  ),
          ),
        ],
      ),
    );
  }
}
```

### 4.4 搜索高亮

```dart
Widget _highlightText(String text, String query) {
  if (query.isEmpty) return Text(text);
  
  final spans = <InlineSpan>[];
  final lowerText = text.toLowerCase();
  final lowerQuery = query.toLowerCase();
  int start = 0;
  int index;
  
  while ((index = lowerText.indexOf(lowerQuery, start)) != -1) {
    if (index > start) {
      spans.add(TextSpan(text: text.substring(start, index)));
    }
    spans.add(TextSpan(
      text: text.substring(index, index + query.length),
      style: TextStyle(backgroundColor: Colors.yellow),
    ));
    start = index + query.length;
  }
  
  if (start < text.length) {
    spans.add(TextSpan(text: text.substring(start)));
  }
  
  return RichText(text: TextSpan(children: spans, style: TextStyle(color: Colors.black)));
}
```

## 5. 常见问题与解决方案

### 问题1：搜索建议不显示

```dart
// 确保在 onTap 或 onChanged 中调用 openView
SearchBar(
  onTap: () => controller.openView(),  // 打开建议视图
  onChanged: (_) => controller.openView(),
)
```

### 问题2：键盘不弹出

```dart
SearchBar(
  autofocus: true,  // 自动获取焦点
  // ...
)
```

### 问题3：样式不统一

```dart
// 使用主题统一样式
SearchBar(
  backgroundColor: WidgetStateProperty.all(
    Theme.of(context).colorScheme.surface,
  ),
)
```

### 问题4：搜索栏太窄

```dart
SearchBar(
  constraints: BoxConstraints(
    minWidth: double.infinity,
    minHeight: 48,
  ),
)
```

## 总结

SearchBar 是 Flutter Material 3 的搜索组件：

**核心要点**：
1. Material 3 现代设计风格
2. 配合 SearchAnchor 实现搜索建议
3. 支持自定义样式和图标
4. 支持搜索历史功能

**常用属性**：
- `hintText` - 提示文字
- `leading` - 前导组件
- `trailing` - 尾随组件
- `onSubmitted` - 提交回调

**最佳实践**：
1. 使用 SearchAnchor 提供搜索建议
2. 实现搜索历史功能
3. 提供清除按钮
4. 处理空搜索和错误情况

---

*最后更新: 2026-02-19*

# Flutter ListTile 组件用法详解

## 简介

ListTile 是列表项组件，用于显示固定高度的单行、双行或三行内容。它是 Material Design 的标准列表项，常用于 ListView、Column 等布局中。

## 1. 初级用法

### 1.1 基本概念

ListTile 的核心特点：
- 固定高度（单行56dp、双行72dp、三行88dp）
- 内置点击反馈
- 支持前导/尾随组件
- 支持选中状态

### 1.2 基本语法

```dart
ListTile(
  leading: Icon(Icons.person),
  title: Text('标题'),
  subtitle: Text('副标题'),
  trailing: Icon(Icons.arrow_forward),
  onTap: () {
    print('点击 ListTile');
  },
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `leading` | `Widget?` | - | 左侧组件 |
| `title` | `Widget?` | - | 标题 |
| `subtitle` | `Widget?` | - | 副标题 |
| `trailing` | `Widget?` | - | 右侧组件 |
| `isThreeLine` | `bool` | `false` | 是否三行 |
| `dense` | `bool?` | - | 紧凑模式 |
| `visualDensity` | `VisualDensity?` | - | 视觉密度 |
| `shape` | `ShapeBorder?` | - | 形状 |
| `style` | `ListTileStyle?` | - | 样式 |
| `selectedColor` | `Color?` | - | 选中时文字颜色 |
| `iconColor` | `Color?` | - | 图标颜色 |
| `textColor` | `Color?` | - | 文字颜色 |
| `titleTextStyle` | `TextStyle?` | - | 标题文字样式 |
| `subtitleTextStyle` | `TextStyle?` | - | 副标题文字样式 |
| `leadingAndTrailingTextStyle` | `TextStyle?` | - | leading/trailing 文字样式 |
| `contentPadding` | `EdgeInsetsGeometry?` | - | 内边距 |
| `enabled` | `bool` | `true` | 是否启用 |
| `onTap` | `GestureTapCallback?` | - | 点击回调 |
| `onLongPress` | `GestureLongPressCallback?` | - | 长按回调 |
| `onFocusChange` | `ValueChanged<bool>?` | - | 焦点变化回调 |
| `mouseCursor` | `MouseCursor?` | - | 鼠标样式 |
| `focusColor` | `Color?` | - | 聚焦颜色 |
| `hoverColor` | `Color?` | - | 悬停颜色 |
| `splashColor` | `Color?` | - | 水波纹颜色 |
| `focusNode` | `FocusNode?` | - | 焦点节点 |
| `autofocus` | `bool` | `false` | 自动聚焦 |
| `tileColor` | `Color?` | - | 背景色 |
| `selectedTileColor` | `Color?` | - | 选中时背景色 |
| `enableFeedback` | `bool?` | - | 触感反馈 |
| `horizontalTitleGap` | `double?` | - | 标题水平间距 |
| `minVerticalPadding` | `double?` | - | 最小垂直内边距 |
| `minLeadingWidth` | `double?` | - | leading 最小宽度 |
| `titleAlignment` | `ListTileTitleAlignment?` | - | 标题对齐 |

### 1.4 基础示例

#### 单行列表项

```dart
ListTile(
  leading: Icon(Icons.settings),
  title: Text('设置'),
  trailing: Icon(Icons.arrow_forward),
  onTap: () {},
)
```

#### 双行列表项

```dart
ListTile(
  leading: CircleAvatar(
    child: Icon(Icons.person),
  ),
  title: Text('张三'),
  subtitle: Text('zhangsan@example.com'),
  trailing: Icon(Icons.message),
  onTap: () {},
)
```

#### 三行列表项

```dart
ListTile(
  leading: Icon(Icons.article),
  title: Text('文章标题'),
  subtitle: Text('这是文章的摘要内容，可能比较长需要显示多行'),
  isThreeLine: true,
  trailing: Icon(Icons.bookmark),
  onTap: () {},
)
```

## 2. 中级用法

### 2.1 选中状态

```dart
ListTile(
  leading: Icon(Icons.star),
  title: Text('收藏'),
  selected: true,
  selectedTileColor: Colors.blue[50],
  selectedColor: Colors.blue,
  onTap: () {},
)
```

### 2.2 紧凑模式

```dart
ListTile(
  leading: Icon(Icons.edit),
  title: Text('紧凑列表项'),
  subtitle: Text('副标题'),
  dense: true,
  onTap: () {},
)
```

### 2.3 自定义背景色

```dart
ListTile(
  leading: Icon(Icons.delete, color: Colors.red),
  title: Text('删除'),
  tileColor: Colors.red[50],
  onTap: () {},
)
```

### 2.4 禁用状态

```dart
ListTile(
  leading: Icon(Icons.lock),
  title: Text('已锁定'),
  enabled: false,
  onTap: null,  // 或不设置 onTap
)
```

### 2.5 长按事件

```dart
ListTile(
  leading: Icon(Icons.file_copy),
  title: Text('文件'),
  onTap: () {
    print('打开文件');
  },
  onLongPress: () {
    print('显示选项菜单');
  },
)
```

### 2.6 自定义形状

```dart
ListTile(
  leading: Icon(Icons.favorite),
  title: Text('圆角列表项'),
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.circular(8),
  ),
  tileColor: Colors.grey[100],
  onTap: () {},
)
```

## 3. 特殊变体

### 3.1 CheckboxListTile

```dart
CheckboxListTile(
  title: Text('同意用户协议'),
  subtitle: Text('请阅读并同意'),
  value: _agreed,
  onChanged: (value) {
    setState(() {
      _agreed = value!;
    });
  },
)
```

### 3.2 RadioListTile

```dart
RadioListTile<String>(
  title: Text('选项一'),
  subtitle: Text('这是选项一的描述'),
  value: 'option1',
  groupValue: _selectedValue,
  onChanged: (value) {
    setState(() {
      _selectedValue = value;
    });
  },
)
```

### 3.3 SwitchListTile

```dart
SwitchListTile(
  title: Text('通知'),
  subtitle: Text('接收推送通知'),
  secondary: Icon(Icons.notifications),
  value: _notificationsEnabled,
  onChanged: (value) {
    setState(() {
      _notificationsEnabled = value;
    });
  },
)
```

### 3.4 AboutListTile

```dart
AboutListTile(
  icon: Icon(Icons.info),
  applicationName: '我的应用',
  applicationVersion: '1.0.0',
  applicationIcon: FlutterLogo(),
  aboutBoxChildren: [
    Text('这是一个示例应用'),
  ],
)
```

## 4. 高级用法

### 4.1 可滑动列表项

```dart
Dismissible(
  key: Key('item_1'),
  background: Container(
    color: Colors.red,
    alignment: Alignment.centerLeft,
    padding: EdgeInsets.only(left: 16),
    child: Icon(Icons.delete, color: Colors.white),
  ),
  secondaryBackground: Container(
    color: Colors.blue,
    alignment: Alignment.centerRight,
    padding: EdgeInsets.only(right: 16),
    child: Icon(Icons.archive, color: Colors.white),
  ),
  onDismissed: (direction) {
    print('滑走: $direction');
  },
  child: ListTile(
    leading: Icon(Icons.email),
    title: Text('邮件标题'),
    subtitle: Text('邮件摘要'),
    onTap: () {},
  ),
)
```

### 4.2 可展开列表项

```dart
class ExpandableListTile extends StatefulWidget {
  final String title;
  final List<Widget> children;

  const ExpandableListTile({
    required this.title,
    required this.children,
    Key? key,
  }) : super(key: key);

  @override
  _ExpandableListTileState createState() => _ExpandableListTileState();
}

class _ExpandableListTileState extends State<ExpandableListTile> {
  bool _isExpanded = false;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        ListTile(
          title: Text(widget.title),
          trailing: Icon(_isExpanded ? Icons.expand_less : Icons.expand_more),
          onTap: () {
            setState(() {
              _isExpanded = !_isExpanded;
            });
          },
        ),
        if (_isExpanded)
          ...widget.children,
      ],
    );
  }
}

// 使用
ExpandableListTile(
  title: '高级选项',
  children: [
    ListTile(title: Text('选项一')),
    ListTile(title: Text('选项二')),
    ListTile(title: Text('选项三')),
  ],
)
```

### 4.3 带徽章的列表项

```dart
ListTile(
  leading: Icon(Icons.email),
  title: Text('收件箱'),
  trailing: Container(
    padding: EdgeInsets.symmetric(horizontal: 8, vertical: 4),
    decoration: BoxDecoration(
      color: Colors.red,
      borderRadius: BorderRadius.circular(12),
    ),
    child: Text('99+', style: TextStyle(color: Colors.white, fontSize: 12)),
  ),
  onTap: () {},
)
```

### 4.4 列表项分组

```dart
Column(
  children: [
    // 分组标题
    Padding(
      padding: EdgeInsets.fromLTRB(16, 16, 16, 8),
      child: Align(
        alignment: Alignment.centerLeft,
        child: Text(
          '常用设置',
          style: TextStyle(
            color: Colors.grey,
            fontWeight: FontWeight.bold,
          ),
        ),
      ),
    ),
    ListTile(
      leading: Icon(Icons.wifi),
      title: Text('WiFi'),
      trailing: Icon(Icons.arrow_forward),
      onTap: () {},
    ),
    ListTile(
      leading: Icon(Icons.bluetooth),
      title: Text('蓝牙'),
      trailing: Icon(Icons.arrow_forward),
      onTap: () {},
    ),
    Divider(),
    Padding(
      padding: EdgeInsets.fromLTRB(16, 16, 16, 8),
      child: Align(
        alignment: Alignment.centerLeft,
        child: Text(
          '其他设置',
          style: TextStyle(
            color: Colors.grey,
            fontWeight: FontWeight.bold,
          ),
        ),
      ),
    ),
    ListTile(
      leading: Icon(Icons.security),
      title: Text('安全'),
      trailing: Icon(Icons.arrow_forward),
      onTap: () {},
    ),
    ListTile(
      leading: Icon(Icons.info),
      title: Text('关于'),
      trailing: Icon(Icons.arrow_forward),
      onTap: () {},
    ),
  ],
)
```

### 4.5 带开关的设置项

```dart
class SettingsList extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ListView(
      children: [
        SwitchListTile(
          title: Text('通知'),
          subtitle: Text('接收推送通知'),
          secondary: Icon(Icons.notifications),
          value: true,
          onChanged: (value) {},
        ),
        SwitchListTile(
          title: '深色模式',
          subtitle: Text('使用深色主题'),
          secondary: Icon(Icons.dark_mode),
          value: false,
          onChanged: (value) {},
        ),
        Divider(),
        ListTile(
          leading: Icon(Icons.language),
          title: Text('语言'),
          trailing: Row(
            mainAxisSize: MainAxisSize.min,
            children: [
              Text('简体中文'),
              Icon(Icons.arrow_forward),
            ],
          ),
          onTap: () {},
        ),
        ListTile(
          leading: Icon(Icons.storage),
          title: Text('清除缓存'),
          trailing: Text('12.5 MB'),
          onTap: () {},
        ),
      ],
    );
  }
}
```

## 5. 常见问题与解决方案

### 问题1：ListTile 高度固定

```dart
// ListTile 高度是固定的（56/72/88dp）
// 如果需要更多内容，使用自定义布局

// 自定义列表项
Container(
  padding: EdgeInsets.all(16),
  child: Row(
    children: [
      Icon(Icons.person),
      SizedBox(width: 16),
      Expanded(
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text('标题'),
            Text('副标题'),
            Text('额外内容'),
          ],
        ),
      ),
    ],
  ),
)
```

### 问题2：点击区域太小

```dart
ListTile(
  minVerticalPadding: 16,  // 增加垂直内边距
  title: Text('点击区域更大'),
  onTap: () {},
)
```

### 问题3：trailing 被截断

```dart
ListTile(
  trailing: SizedBox(
    width: 100,  // 限制宽度
    child: Text('长文本会被截断'),
  ),
  title: Text('标题'),
  onTap: () {},
)
```

### 问题4：subtitle 不换行

```dart
ListTile(
  title: Text('标题'),
  subtitle: Text(
    '很长很长的副标题内容',
    maxLines: 2,
    overflow: TextOverflow.ellipsis,
  ),
  onTap: () {},
)
```

## 6. 性能优化

### 6.1 在 ListView 中使用

```dart
ListView.builder(
  itemCount: 100,
  itemBuilder: (context, index) {
    return ListTile(
      title: Text('Item $index'),
      onTap: () {},
    );
  },
)
```

### 6.2 使用 const

```dart
// 静态 ListTile 使用 const
const ListTile(
  title: Text('静态标题'),
)
```

## 总结

ListTile 是 Flutter 中常用的列表项组件：

**核心要点**：
1. 固定高度：单行56dp、双行72dp、三行88dp
2. 支持前导/尾随组件
3. 内置点击反馈和选中状态
4. 多种变体：Checkbox/Radio/Switch/About

**常用属性**：
- `leading` - 左侧组件
- `title` - 标题
- `subtitle` - 副标题
- `trailing` - 右侧组件
- `onTap` - 点击回调
- `selected` - 是否选中

**最佳实践**：
1. 使用 ListView.builder 处理长列表
2. 合理使用 dense 和 isThreeLine
3. 提供清晰的视觉层级
4. 使用 Divider 分隔列表项

---

*最后更新: 2026-02-19*

# Flutter SnackBar 组件用法详解

## 简介

SnackBar 是 Material Design 风格的底部消息提示组件，用于向用户显示简短的反馈信息，支持操作按钮和自动消失。

## 1. 初级用法

### 1.1 基本概念

SnackBar 的核心特点：
- 底部显示的消息提示
- 自动消失（默认 4 秒）
- 支持操作按钮
- 可自定义样式和行为

### 1.2 基本语法

```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(content: Text('这是一条消息')),
)
```

### 1.3 主要属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `content` | `Widget` | - | 内容组件（必需） |
| `action` | `SnackBarAction?` | - | 操作按钮 |
| `duration` | `Duration` | `4s` | 显示时长 |
| `backgroundColor` | `Color?` | - | 背景色 |
| `elevation` | `double?` | - | 阴影高度 |
| `margin` | `EdgeInsetsGeometry?` | - | 外边距 |
| `padding` | `EdgeInsetsGeometry?` | - | 内边距 |
| `width` | `double?` | - | 固定宽度 |
| `shape` | `ShapeBorder?` | - | 形状 |
| `behavior` | `SnackBarBehavior?` | - | 显示行为 |
| `showCloseIcon` | `bool` | `false` | 显示关闭图标 |
| `closeIconColor` | `Color?` | - | 关闭图标颜色 |

### 1.4 SnackBarBehavior

| 行为 | 说明 |
|------|------|
| `fixed` | 固定在底部，全宽显示 |
| `floating` | 浮动显示，带边距 |

```dart
// fixed - 固定底部
SnackBar(
  content: Text('固定模式'),
  behavior: SnackBarBehavior.fixed,
)

// floating - 浮动
SnackBar(
  content: Text('浮动模式'),
  behavior: SnackBarBehavior.floating,
)
```

### 1.5 基础示例

#### 简单消息

```dart
ElevatedButton(
  onPressed: () {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text('操作成功')),
    );
  },
  child: Text('显示消息'),
)
```

#### 带操作按钮

```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('文件已删除'),
    action: SnackBarAction(
      label: '撤销',
      onPressed: () {
        // 撤销删除
      },
    ),
  ),
)
```

#### 自定义时长

```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('这条消息会显示 2 秒'),
    duration: Duration(seconds: 2),
  ),
)
```

## 2. 中级用法

### 2.1 自定义样式

```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text(
      '自定义样式',
      style: TextStyle(color: Colors.white, fontSize: 16),
    ),
    backgroundColor: Colors.blue,
    behavior: SnackBarBehavior.floating,
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(10),
    ),
    margin: EdgeInsets.all(16),
  ),
)
```

### 2.2 带关闭按钮

```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('带关闭按钮'),
    showCloseIcon: true,
    closeIconColor: Colors.white,
  ),
)
```

### 2.3 固定宽度

```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Text('固定宽度'),
    width: 280,
    behavior: SnackBarBehavior.floating,
  ),
)
```

### 2.4 带图标

```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: Row(
      children: [
        Icon(Icons.check_circle, color: Colors.white),
        SizedBox(width: 12),
        Text('操作成功'),
      ],
    ),
    backgroundColor: Colors.green,
  ),
)
```

### 2.5 多条消息队列

```dart
// SnackBar 会自动排队显示
void showMultipleSnackBars(BuildContext context) {
  ScaffoldMessenger.of(context).showSnackBar(
    SnackBar(content: Text('消息 1')),
  );
  ScaffoldMessenger.of(context).showSnackBar(
    SnackBar(content: Text('消息 2')),
  );
  ScaffoldMessenger.of(context).showSnackBar(
    SnackBar(content: Text('消息 3')),
  );
}
```

### 2.6 清除所有消息

```dart
// 清除当前显示的 SnackBar
ScaffoldMessenger.of(context).clearSnackBars();

// 隐藏当前 SnackBar
ScaffoldMessenger.of(context).hideCurrentSnackBar();
```

## 3. 高级用法

### 3.1 封装 SnackBar 工具类

```dart
class AppSnackBar {
  static void show(BuildContext context, String message) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(message),
        behavior: SnackBarBehavior.floating,
      ),
    );
  }

  static void success(BuildContext context, String message) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Row(
          children: [
            Icon(Icons.check_circle, color: Colors.white),
            SizedBox(width: 12),
            Text(message),
          ],
        ),
        backgroundColor: Colors.green,
        behavior: SnackBarBehavior.floating,
      ),
    );
  }

  static void error(BuildContext context, String message) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Row(
          children: [
            Icon(Icons.error, color: Colors.white),
            SizedBox(width: 12),
            Text(message),
          ],
        ),
        backgroundColor: Colors.red,
        behavior: SnackBarBehavior.floating,
      ),
    );
  }

  static void warning(BuildContext context, String message) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Row(
          children: [
            Icon(Icons.warning, color: Colors.black),
            SizedBox(width: 12),
            Text(message),
          ],
        ),
        backgroundColor: Colors.orange,
        behavior: SnackBarBehavior.floating,
      ),
    );
  }

  static void withAction(
    BuildContext context,
    String message,
    String actionLabel,
    VoidCallback onAction,
  ) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(message),
        action: SnackBarAction(
          label: actionLabel,
          onPressed: onAction,
        ),
        behavior: SnackBarBehavior.floating,
      ),
    );
  }
}

// 使用示例
AppSnackBar.success(context, '保存成功');
AppSnackBar.error(context, '网络错误');
AppSnackBar.warning(context, '请注意');
AppSnackBar.withAction(context, '已删除', '撤销', () {
  // 撤销操作
});
```

### 3.2 带进度的 SnackBar

```dart
class ProgressSnackBar {
  final BuildContext context;
  late SnackBar snackBar;
  late ScaffoldMessengerState messenger;

  ProgressSnackBar(this.context, String message) {
    snackBar = SnackBar(
      content: Row(
        children: [
          SizedBox(
            width: 20,
            height: 20,
            child: CircularProgressIndicator(strokeWidth: 2),
          ),
          SizedBox(width: 16),
          Text(message),
        ],
      ),
      duration: Duration(days: 1), // 不自动消失
    );
  }

  void show() {
    messenger = ScaffoldMessenger.of(context);
    messenger.showSnackBar(snackBar);
  }

  void complete(String message) {
    messenger.hideCurrentSnackBar();
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Row(
          children: [
            Icon(Icons.check, color: Colors.white),
            SizedBox(width: 16),
            Text(message),
          ],
        ),
        backgroundColor: Colors.green,
      ),
    );
  }
}

// 使用示例
void uploadFile(BuildContext context) async {
  final progress = ProgressSnackBar(context, '正在上传...');
  progress.show();
  
  await Future.delayed(Duration(seconds: 2));
  
  progress.complete('上传完成');
}
```

### 3.3 可展开的 SnackBar

```dart
void showExpandableSnackBar(BuildContext context) {
  ScaffoldMessenger.of(context).showSnackBar(
    SnackBar(
      content: Column(
        mainAxisSize: MainAxisSize.min,
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Text('发生错误'),
          SizedBox(height: 8),
          Text(
            '详细错误信息：连接超时，请检查网络设置后重试',
            style: TextStyle(fontSize: 12, color: Colors.white70),
          ),
        ],
      ),
      duration: Duration(seconds: 6),
      action: SnackBarAction(
        label: '重试',
        onPressed: () {},
      ),
    ),
  );
}
```

### 3.4 全局 SnackBar Key

```dart
// 定义全局 key
final GlobalKey<ScaffoldMessengerState> scaffoldMessengerKey = 
    GlobalKey<ScaffoldMessengerState>();

// MaterialApp 中使用
MaterialApp(
  scaffoldMessengerKey: scaffoldMessengerKey,
  home: MyHomePage(),
)

// 在任何地方使用
scaffoldMessengerKey.currentState?.showSnackBar(
  SnackBar(content: Text('全局 SnackBar')),
)
```

## 4. 与其他组件配合

### 4.1 与表单验证配合

```dart
void submitForm(BuildContext context) {
  if (formKey.currentState!.validate()) {
    // 表单验证通过
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Row(
          children: [
            Icon(Icons.check, color: Colors.white),
            SizedBox(width: 12),
            Text('提交成功'),
          ],
        ),
        backgroundColor: Colors.green,
      ),
    );
  }
}
```

### 4.2 与网络请求配合

```dart
Future<void> fetchData(BuildContext context) async {
  try {
    final data = await api.getData();
    AppSnackBar.success(context, '加载成功');
  } catch (e) {
    AppSnackBar.error(context, '加载失败: $e');
  }
}
```

### 4.3 与删除操作配合

```dart
void deleteItem(BuildContext context, Item item) {
  // 先保存删除项
  final deletedItem = item;
  
  // 执行删除
  removeItem(item);
  
  // 显示带撤销的 SnackBar
  ScaffoldMessenger.of(context).showSnackBar(
    SnackBar(
      content: Text('已删除 "${deletedItem.name}"'),
      action: SnackBarAction(
        label: '撤销',
        onPressed: () {
          // 恢复删除项
          restoreItem(deletedItem);
        },
      ),
    ),
  );
}
```

## 5. 性能优化

### 5.1 避免频繁显示

```dart
// 清除之前的 SnackBar 再显示新的
ScaffoldMessenger.of(context).clearSnackBars();
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(content: Text('新消息')),
)
```

### 5.2 合理设置时长

```dart
// 重要信息显示更长时间
SnackBar(
  content: Text('重要警告'),
  duration: Duration(seconds: 8),
)

// 简单提示显示较短时间
SnackBar(
  content: Text('已复制'),
  duration: Duration(seconds: 2),
)
```

## 6. 常见问题与解决方案

### 问题1：SnackBar 不显示

```dart
// ❌ 问题：没有 ScaffoldMessenger 祖先
Builder(
  builder: (context) => ElevatedButton(
    onPressed: () {
      ScaffoldMessenger.of(context).showSnackBar(...);
    },
    child: Text('显示'),
  ),
)

// ✅ 确保有 Scaffold 祖先
Scaffold(
  body: Center(
    child: ElevatedButton(
      onPressed: () {
        ScaffoldMessenger.of(context).showSnackBar(...);
      },
      child: Text('显示'),
    ),
  ),
)
```

### 问题2：SnackBar 被 FAB 遮挡

```dart
Scaffold(
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
  // 确保使用 ScaffoldMessenger
)
```

### 问题3：多条消息堆积

```dart
// ❌ 问题：多条消息排队等待
for (int i = 0; i < 10; i++) {
  ScaffoldMessenger.of(context).showSnackBar(
    SnackBar(content: Text('消息 $i')),
  );
}

// ✅ 解决方案：清除旧消息
ScaffoldMessenger.of(context).clearSnackBars();
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(content: Text('只显示这一条')),
)
```

### 问题4：SnackBar 样式不统一

```dart
// 使用主题配置
MaterialApp(
  theme: ThemeData(
    snackBarTheme: SnackBarThemeData(
      backgroundColor: Colors.black87,
      contentTextStyle: TextStyle(color: Colors.white),
      behavior: SnackBarBehavior.floating,
    ),
  ),
)
```

### 问题5：点击操作按钮后 SnackBar 不消失

```dart
// 操作按钮点击后默认不关闭
// 可以手动关闭
SnackBar(
  content: Text('消息'),
  action: SnackBarAction(
    label: '操作',
    onPressed: () {
      ScaffoldMessenger.of(context).hideCurrentSnackBar();
      // 执行操作
    },
  ),
)
```

## 7. SnackBar vs Toast vs Dialog

| 特性 | SnackBar | Toast | Dialog |
|------|----------|-------|--------|
| 位置 | 底部 | 可自定义 | 屏幕中央 |
| 交互 | 可操作 | 仅展示 | 可交互 |
| 自动消失 | 是 | 是 | 否 |
| 遮罩 | 无 | 无 | 有 |
| 适用场景 | 反馈提示 | 简单提示 | 重要确认 |

```dart
// SnackBar - 带操作的消息
SnackBar(content: Text('已删除'), action: SnackBarAction(label: '撤销', ...))

// Toast（需要第三方包或自定义）
// fluttertoast: Fluttertoast.showToast(msg: "提示信息")

// Dialog - 重要确认
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: Text('确认删除'),
    actions: [
      TextButton(child: Text('取消'), onPressed: () => Navigator.pop(context)),
      TextButton(child: Text('删除'), onPressed: () {}),
    ],
  ),
)
```

## 8. 最佳实践

### 8.1 统一管理

```dart
class MessageService {
  static void show(BuildContext context, String message, {bool isError = false}) {
    ScaffoldMessenger.of(context).clearSnackBars();
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(message),
        backgroundColor: isError ? Colors.red : null,
        behavior: SnackBarBehavior.floating,
      ),
    );
  }
}
```

### 8.2 国际化

```dart
AppSnackBar.success(context, AppLocalizations.of(context)!.saveSuccess)
```

### 8.3 无障碍支持

```dart
SnackBar(
  content: Text('操作成功'),
  semanticsLabel: '操作成功提示',
)
```

## 总结

SnackBar 是 Flutter 中最常用的消息提示组件：

**核心要点**：
1. 通过 ScaffoldMessenger 显示
2. 支持操作按钮
3. 自动消失，可配置时长
4. 有 fixed 和 floating 两种显示模式

**常用场景**：
- 操作成功/失败反馈
- 带撤销的删除操作
- 网络状态提示
- 表单提交反馈

**最佳实践**：
1. 封装统一的 SnackBar 工具类
2. 合理设置显示时长
3. 使用 floating 模式更现代
4. 重要操作提供撤销选项

---

*最后更新: 2026-02-18*

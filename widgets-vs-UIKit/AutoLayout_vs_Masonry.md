# Auto Layout vs Masonry 对比学习

本文档记录 iOS 原生 Auto Layout 与 Masonry 第三方库的对比学习要点，帮助理解声明式布局与链式语法的差异。

---

## 1. Masonry 核心概念

### Masonry 是什么

Masonry 是 iOS 开发中流行的 Auto Layout 封装库，提供链式语法简化约束编写。

- 链式语法，代码更简洁
- 自动处理 translatesAutoresizingMaskIntoConstraints
- 支持 iOS 和 macOS
- 大幅减少约束代码量

### 原生 Auto Layout vs Masonry 概念对比

| 原生 Auto Layout | Masonry | 说明 |
|-----------------|---------|------|
| `constraintEqualToAnchor:constant:` | `.equalTo(offset:)` | 等于约束 |
| `constraintGreaterThanOrEqualToAnchor:` | `.greaterThanOrEqualTo()` | 大于等于 |
| `constraintLessThanOrEqualToAnchor:` | `.lessThanOrEqualTo()` | 小于等于 |
| `active = YES` | 自动激活 | 激活约束 |
| `translatesAutoresizingMaskIntoConstraints = NO` | 自动处理 | 自动布局标志 |

---

## 2. 基础语法对比

### 2.1 创建约束

```objc
// 原生 Auto Layout - 冗长
UIView *view = [[UIView alloc] init];
view.translatesAutoresizingMaskIntoConstraints = NO;
[self.view addSubview:view];

[view.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor constant:20].active = YES;
[view.topAnchor constraintEqualToAnchor:self.view.topAnchor constant:20].active = YES;
[view.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor constant:-20].active = YES;
[view.bottomAnchor constraintEqualToAnchor:self.view.bottomAnchor constant:-20].active = YES;
```

```objc
// Masonry - 简洁
UIView *view = [[UIView alloc] init];
[self.view addSubview:view];

[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(self.view).insets(UIEdgeInsetsMake(20, 20, 20, 20));
}];
```

### 2.2 边距约束

```objc
// 原生 Auto Layout
[view.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor constant:16].active = YES;
[view.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor constant:-16].active = YES;
[view.topAnchor constraintEqualToAnchor:self.view.topAnchor constant:8].active = YES;
[view.bottomAnchor constraintEqualToAnchor:self.view.bottomAnchor constant:-8].active = YES;
```

```objc
// Masonry
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(self.view).insets(UIEdgeInsetsMake(8, 16, 8, 16));
}];

// 或简写
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.left.equalTo(self.view).offset(16);
    make.right.equalTo(self.view).offset(-16);
    make.top.equalTo(self.view).offset(8);
    make.bottom.equalTo(self.view).offset(-8);
}];
```

### 2.3 尺寸约束

```objc
// 原生 Auto Layout
[view.widthAnchor constraintEqualToConstant:100].active = YES;
[view.heightAnchor constraintEqualToConstant:50].active = YES;
```

```objc
// Masonry
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.width.equalTo(@100);
    make.height.equalTo(@50);
    // 或
    make.size.equalTo(CGSizeMake(100, 50));
}];
```

### 2.4 居中约束

```objc
// 原生 Auto Layout
[view.centerXAnchor constraintEqualToAnchor:self.view.centerXAnchor].active = YES;
[view.centerYAnchor constraintEqualToAnchor:self.view.centerYAnchor].active = YES;
```

```objc
// Masonry
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.center.equalTo(self.view);
}];
```

### 2.5 比例约束

```objc
// 原生 Auto Layout
[view.widthAnchor constraintEqualToAnchor:view.heightAnchor multiplier:2.0].active = YES;
```

```objc
// Masonry
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.width.equalTo(view.mas_height).multipliedBy(2.0);
}];
```

### 2.6 优先级

```objc
// 原生 Auto Layout
NSLayoutConstraint *constraint = [view.heightAnchor constraintEqualToConstant:100];
constraint.priority = UILayoutPriorityDefaultHigh;
constraint.active = YES;
```

```objc
// Masonry
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.height.equalTo(@100).priority(750);
    // 或使用预设优先级
    make.height.equalTo(@100).priorityHigh();
    make.height.equalTo(@100).priorityMedium();
    make.height.equalTo(@100).priorityLow();
}];
```

---

## 3. 完整示例对比

### 原生 Auto Layout 实现

```objc
@interface CardView : UIView
@property (nonatomic, strong) UIImageView *iconView;
@property (nonatomic, strong) UILabel *titleLabel;
@property (nonatomic, strong) UILabel *subtitleLabel;
@property (nonatomic, strong) UIButton *actionButton;
@end

@implementation CardView

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        [self setupViews];
        [self setupConstraints];
    }
    return self;
}

- (void)setupViews {
    self.backgroundColor = [UIColor whiteColor];
    self.layer.cornerRadius = 12;
    
    _iconView = [[UIImageView alloc] init];
    _iconView.contentMode = UIViewContentModeScaleAspectFit;
    _iconView.translatesAutoresizingMaskIntoConstraints = NO;
    [self addSubview:_iconView];
    
    _titleLabel = [[UILabel alloc] init];
    _titleLabel.font = [UIFont boldSystemFontOfSize:18];
    _titleLabel.translatesAutoresizingMaskIntoConstraints = NO;
    [self addSubview:_titleLabel];
    
    _subtitleLabel = [[UILabel alloc] init];
    _subtitleLabel.font = [UIFont systemFontOfSize:14];
    _subtitleLabel.textColor = [UIColor grayColor];
    _subtitleLabel.translatesAutoresizingMaskIntoConstraints = NO;
    [self addSubview:_subtitleLabel];
    
    _actionButton = [UIButton buttonWithType:UIButtonTypeSystem];
    [_actionButton setTitle:@"Action" forState:UIControlStateNormal];
    _actionButton.translatesAutoresizingMaskIntoConstraints = NO;
    [self addSubview:_actionButton];
}

- (void)setupConstraints {
    // Icon 约束
    [self.iconView.leadingAnchor constraintEqualToAnchor:self.leadingAnchor constant:16].active = YES;
    [self.iconView.topAnchor constraintEqualToAnchor:self.topAnchor constant:16].active = YES;
    [self.iconView.widthAnchor constraintEqualToConstant:48].active = YES;
    [self.iconView.heightAnchor constraintEqualToConstant:48].active = YES;
    
    // Title 约束
    [self.titleLabel.leadingAnchor constraintEqualToAnchor:self.iconView.trailingAnchor constant:12].active = YES;
    [self.titleLabel.trailingAnchor constraintEqualToAnchor:self.trailingAnchor constant:-16].active = YES;
    [self.titleLabel.topAnchor constraintEqualToAnchor:self.topAnchor constant:16].active = YES;
    
    // Subtitle 约束
    [self.subtitleLabel.leadingAnchor constraintEqualToAnchor:self.titleLabel.leadingAnchor].active = YES;
    [self.subtitleLabel.trailingAnchor constraintEqualToAnchor:self.trailingAnchor constant:-16].active = YES;
    [self.subtitleLabel.topAnchor constraintEqualToAnchor:self.titleLabel.bottomAnchor constant:4].active = YES;
    
    // Button 约束
    [self.actionButton.leadingAnchor constraintEqualToAnchor:self.leadingAnchor constant:16].active = YES;
    [self.actionButton.trailingAnchor constraintEqualToAnchor:self.trailingAnchor constant:-16].active = YES;
    [self.actionButton.topAnchor constraintEqualToAnchor:self.iconView.bottomAnchor constant:16].active = YES;
    [self.actionButton.bottomAnchor constraintEqualToAnchor:self.bottomAnchor constant:-16].active = YES;
    [self.actionButton.heightAnchor constraintEqualToConstant:44].active = YES;
}
@end
```

### Masonry 实现

```objc
@implementation CardView

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        [self setupViews];
        [self setupConstraints];
    }
    return self;
}

- (void)setupViews {
    self.backgroundColor = [UIColor whiteColor];
    self.layer.cornerRadius = 12;
    
    _iconView = [[UIImageView alloc] init];
    _iconView.contentMode = UIViewContentModeScaleAspectFit;
    [self addSubview:_iconView];
    
    _titleLabel = [[UILabel alloc] init];
    _titleLabel.font = [UIFont boldSystemFontOfSize:18];
    [self addSubview:_titleLabel];
    
    _subtitleLabel = [[UILabel alloc] init];
    _subtitleLabel.font = [UIFont systemFontOfSize:14];
    _subtitleLabel.textColor = [UIColor grayColor];
    [self addSubview:_subtitleLabel];
    
    _actionButton = [UIButton buttonWithType:UIButtonTypeSystem];
    [_actionButton setTitle:@"Action" forState:UIControlStateNormal];
    [self addSubview:_actionButton];
}

- (void)setupConstraints {
    [self.iconView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.leading.equalTo(self).offset(16);
        make.top.equalTo(self).offset(16);
        make.size.equalTo(CGSizeMake(48, 48));
    }];
    
    [self.titleLabel mas_makeConstraints:^(MASConstraintMaker *make) {
        make.leading.equalTo(self.iconView.mas_trailing).offset(12);
        make.trailing.equalTo(self).offset(-16);
        make.top.equalTo(self).offset(16);
    }];
    
    [self.subtitleLabel mas_makeConstraints:^(MASConstraintMaker *make) {
        make.leading.equalTo(self.titleLabel);
        make.trailing.equalTo(self).offset(-16);
        make.top.equalTo(self.titleLabel.mas_bottom).offset(4);
    }];
    
    [self.actionButton mas_makeConstraints:^(MASConstraintMaker *make) {
        make.leading.trailing.equalTo(self).insets(UIEdgeInsetsMake(0, 16, 0, 16));
        make.top.equalTo(self.iconView.mas_bottom).offset(16);
        make.bottom.equalTo(self).offset(-16);
        make.height.equalTo(@44);
    }];
}
@end
```

---

## 4. Masonry 核心方法

### 4.1 三种约束方法

```objc
// mas_makeConstraints - 创建新约束
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(self.view);
}];

// mas_updateConstraints - 更新现有约束
[view mas_updateConstraints:^(MASConstraintMaker *make) {
    make.height.equalTo(@100);  // 只更新 height，其他保持不变
}];

// mas_remakeConstraints - 移除旧约束，创建新约束
[view mas_remakeConstraints:^(MASConstraintMaker *make) {
    make.center.equalTo(self.view);
    make.size.equalTo(CGSizeMake(200, 200));
}];
```

### 4.2 属性快捷方式

```objc
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    // 边缘
    make.edges.equalToSuperview();        // 上下左右
    make.left.equalToSuperview();         // 左边
    make.right.equalToSuperview();        // 右边
    make.top.equalToSuperview();          // 上边
    make.bottom.equalToSuperview();       // 下边
    make.leading.equalToSuperview();      // 起始边（考虑 RTL）
    make.trailing.equalToSuperview();     // 结束边（考虑 RTL）
    
    // 尺寸
    make.width.equalTo(@100);             // 宽度
    make.height.equalTo(@100);            // 高度
    make.size.equalTo(CGSizeMake(100, 100));  // 尺寸
    
    // 中心
    make.center.equalToSuperview();       // 中心点
    make.centerX.equalToSuperview();      // 水平中心
    make.centerY.equalToSuperview();      // 垂直中心
    
    // 组合
    make.left.right.equalToSuperview();   // 左右两边
    make.top.bottom.equalToSuperview();   // 上下两边
}];
```

### 4.3 偏移和倍数

```objc
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    // offset - 偏移
    make.left.equalTo(self.view).offset(16);
    
    // insets - 边距
    make.edges.equalTo(self.view).insets(UIEdgeInsetsMake(8, 16, 8, 16));
    
    // multipliedBy - 倍数
    make.width.equalTo(self.view).multipliedBy(0.5);
    
    // dividedBy - 除法
    make.width.equalTo(self.view).dividedBy(2);
}];
```

---

## 5. 与 Flutter 约束的对比

### 5.1 概念映射

| Masonry | Flutter | 说明 |
|---------|---------|------|
| `make.edges.equalToSuperview()` | `Positioned.fill()` | 填充父容器 |
| `make.center.equalToSuperview()` | `Center()` | 居中 |
| `make.leading.equalTo(offset: 16)` | `Padding(padding: EdgeInsets.only(left: 16))` | 左边距 |
| `make.top.equalTo(offset: 8)` | `Padding(padding: EdgeInsets.only(top: 8))` | 上边距 |
| `make.width.equalTo(@100)` | `SizedBox(width: 100)` | 固定宽度 |
| `make.height.equalTo(@50)` | `SizedBox(height: 50)` | 固定高度 |
| `multipliedBy(0.5)` | `Expanded(flex: 1)` + 计算 | 比例 |

### 5.2 水平线性布局对比

```objc
// Masonry - 水平排列三个视图
[view1 mas_makeConstraints:^(MASConstraintMaker *make) {
    make.leading.equalTo(self.view).offset(16);
    make.top.equalTo(self.view).offset(16);
    make.width.equalTo(@100);
    make.height.equalTo(@50);
}];

[view2 mas_makeConstraints:^(MASConstraintMaker *make) {
    make.leading.equalTo(view1.mas_trailing).offset(8);
    make.top.equalTo(view1);
    make.width.equalTo(view1);
    make.height.equalTo(view1);
}];

[view3 mas_makeConstraints:^(MASConstraintMaker *make) {
    make.leading.equalTo(view2.mas_trailing).offset(8);
    make.top.equalTo(view1);
    make.width.equalTo(view1);
    make.height.equalTo(view1);
    make.trailing.lessThanOrEqualTo(self.view).offset(-16);
}];
```

```dart
// Flutter - 同样效果
Row(
  padding: EdgeInsets.all(16),
  children: [
    SizedBox(width: 100, height: 50, child: Container(color: Colors.red)),
    SizedBox(width: 8),
    SizedBox(width: 100, height: 50, child: Container(color: Colors.green)),
    SizedBox(width: 8),
    SizedBox(width: 100, height: 50, child: Container(color: Colors.blue)),
  ],
)
```

---

## 6. 常见陷阱

### 陷阱1：mas_ 前缀

```objc
// ❌ 错误：没有 mas_ 前缀可能导致冲突
[view makeConstraints:^(MASConstraintMaker *make) {
    make.left.equalTo(self.view);
}];

// ✅ 正确：使用 mas_ 前缀
[view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.left.equalTo(self.view);
}];
```

### 陷阱2：数值包装

```objc
// ⚠️ 数值需要包装为 NSNumber
make.width.equalTo(@100);  // 正确

// 或使用 mas_equalTo 宏自动包装
make.width.mas_equalTo(100);  // 也可以
```

### 陷阱3：约束冲突

```objc
// ❌ 同时设置 left/right 和 width 可能冲突
make.left.equalTo(self.view).offset(16);
make.right.equalTo(self.view).offset(-16);
make.width.equalTo(@300);  // 冲突！

// ✅ 选择一种方式
// 方式1：left + right 自动计算 width
make.left.equalTo(self.view).offset(16);
make.right.equalTo(self.view).offset(-16);

// 方式2：left + width
make.left.equalTo(self.view).offset(16);
make.width.equalTo(@300);
```

### 陷阱4：更新约束时机

```objc
// ✅ 在 updateViewConstraints 中更新
- (void)updateViewConstraints {
    [self.contentView mas_updateConstraints:^(MASConstraintMaker *make) {
        make.height.equalTo(@(self.requiredHeight));
    }];
    [super updateViewConstraints];
}
```

---

## 7. 学习技巧

### 技巧1：使用 equalToSuperview

```objc
// 简化代码
make.left.equalTo(self.view);     // 完整
make.left.equalToSuperview();     // 简化
```

### 技巧2：组合属性

```objc
// 同时设置多个属性
make.left.right.top.bottom.equalTo(self.view).insets(insets);

// 或
make.edges.equalTo(self.view).insets(insets);
```

### 技巧3：优先级约束

```objc
// Content Hugging / Compression Resistance
[view setContentHuggingPriority:UILayoutPriorityDefaultHigh forAxis:UILayoutConstraintAxisHorizontal];
[view setContentCompressionResistancePriority:UILayoutPriorityDefaultHigh forAxis:UILayoutConstraintAxisHorizontal];
```

### 技巧4：调试约束

```objc
// 给视图命名便于调试
view.mas_key = @"MyView";
self.view.mas_key = @"Superview";

// 或使用宏
MASAttachKeys(view, titleLabel, subtitleLabel);
```

---

## 8. 核心理念总结

### 语法对比

| 方面 | 原生 Auto Layout | Masonry |
|------|-----------------|---------|
| **代码量** | 多 | 少 |
| **可读性** | 较差 | 较好 |
| **链式调用** | 无 | 支持 |
| **自动处理** | 需手动 | 自动 |

### 思维对比

```
原生 Auto Layout 思维：
  创建约束 → 设置属性 → 激活约束
  代码分散，需要多步操作

Masonry 思维：
  声明式链式调用
  所有约束在一个 block 中完成
  类似 Flutter 的声明式风格
```

### 最佳实践

1. **使用 mas_makeConstraints** - 创建约束
2. **使用 mas_updateConstraints** - 更新约束
3. **使用 mas_remakeConstraints** - 重建约束
4. **命名视图** - 便于调试

---

*最后更新: 2026-02-24*

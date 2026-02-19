# Flutter 社交应用实战

## 概述

本文通过一个社交应用项目，演示实时通讯、用户交互、复杂 UI 等功能的实现。

## 项目介绍

### 功能需求

- 用户注册与登录
- 个人资料管理
- 发布动态（文字、图片）
- 动态 Feed 流
- 点赞、评论
- 关注/粉丝系统
- 实时消息
- 推送通知

### 技术栈

| 类别 | 技术选型 |
|------|----------|
| 状态管理 | Riverpod |
| 后端服务 | Firebase / Supabase |
| 实时通讯 | WebSocket |
| 本地存储 | Hive |
| 图片处理 | image_picker + cached_network_image |
| 推送通知 | firebase_messaging |

## 项目结构

```
lib/
├── main.dart
├── app.dart
│
├── core/
│   ├── services/
│   │   ├── firebase_service.dart
│   │   ├── storage_service.dart
│   │   └── notification_service.dart
│   └── ...
│
├── features/
│   ├── auth/
│   │   └── presentation/
│   │       ├── pages/
│   │       │   ├── login_page.dart
│   │       │   └── register_page.dart
│   │       └── providers/
│   │           └── auth_provider.dart
│   │
│   ├── feed/
│   │   ├── data/
│   │   │   ├── models/
│   │   │   │   ├── post.dart
│   │   │   │   └── comment.dart
│   │   │   └── repositories/
│   │   │       └── post_repository.dart
│   │   └── presentation/
│   │       ├── pages/
│   │       │   └── feed_page.dart
│   │       ├── widgets/
│   │       │   ├── post_card.dart
│   │       │   ├── comment_sheet.dart
│   │       │   └── create_post_sheet.dart
│   │       └── providers/
│   │           └── feed_provider.dart
│   │
│   ├── profile/
│   │   ├── data/
│   │   │   ├── models/
│   │   │   │   └── user_profile.dart
│   │   │   └── repositories/
│   │   │       └── profile_repository.dart
│   │   └── presentation/
│   │       ├── pages/
│   │       │   ├── profile_page.dart
│   │       │   └── edit_profile_page.dart
│   │       └── widgets/
│   │           ├── profile_header.dart
│   │           └── stats_row.dart
│   │
│   ├── chat/
│   │   ├── data/
│   │   │   ├── models/
│   │   │   │   ├── message.dart
│   │   │   │   └── conversation.dart
│   │   │   └── repositories/
│   │   │       └── chat_repository.dart
│   │   └── presentation/
│   │       ├── pages/
│   │       │   ├── conversations_page.dart
│   │       │   └── chat_page.dart
│   │       ├── widgets/
│   │       │   ├── message_bubble.dart
│   │       │   └── conversation_tile.dart
│   │       └── providers/
│   │           └── chat_provider.dart
│   │
│   └── notification/
│       └── presentation/
│           └── notifications_page.dart
│
└── shared/
    └── widgets/
        ├── avatar_widget.dart
        └── image_viewer.dart
```

## 核心代码实现

### 1. 数据模型

```dart
// features/feed/data/models/post.dart
@freezed
class Post with _$Post {
  const factory Post({
    required String id,
    required String userId,
    required String userName,
    String? userAvatar,
    required String content,
    List<String>? images,
    required int likeCount,
    required int commentCount,
    required DateTime createdAt,
    bool isLiked = false,
  }) = _Post;

  factory Post.fromJson(Map<String, dynamic> json) => _$PostFromJson(json);
}

// features/profile/data/models/user_profile.dart
@freezed
class UserProfile with _$UserProfile {
  const factory UserProfile({
    required String id,
    required String email,
    required String name,
    String? avatar,
    String? bio,
    required int followingCount,
    required int followersCount,
    required int postsCount,
    required DateTime createdAt,
  }) = _UserProfile;

  factory UserProfile.fromJson(Map<String, dynamic> json) => _$UserProfileFromJson(json);
}
```

### 2. Feed Provider

```dart
// features/feed/presentation/providers/feed_provider.dart
class FeedState {
  final List<Post> posts;
  final bool isLoading;
  final bool hasMore;
  final String? error;

  const FeedState({
    this.posts = const [],
    this.isLoading = false,
    this.hasMore = true,
    this.error,
  });

  FeedState copyWith({
    List<Post>? posts,
    bool? isLoading,
    bool? hasMore,
    String? error,
  }) {
    return FeedState(
      posts: posts ?? this.posts,
      isLoading: isLoading ?? this.isLoading,
      hasMore: hasMore ?? this.hasMore,
      error: error ?? this.error,
    );
  }
}

class FeedNotifier extends StateNotifier<FeedState> {
  final PostRepository _repository;
  DocumentSnapshot? _lastDocument;

  FeedNotifier(this._repository) : super(const FeedState()) {
    loadPosts();
  }

  Future<void> loadPosts({bool refresh = false}) async {
    if (state.isLoading) return;
    if (!refresh && !state.hasMore) return;

    if (refresh) {
      _lastDocument = null;
    }

    state = state.copyWith(isLoading: true, error: null);

    try {
      final result = await _repository.getFeed(
        lastDocument: _lastDocument,
        limit: 20,
      );

      final newPosts = result.posts;
      _lastDocument = result.lastDocument;

      state = state.copyWith(
        posts: refresh ? newPosts : [...state.posts, ...newPosts],
        isLoading: false,
        hasMore: newPosts.length == 20,
      );
    } catch (e) {
      state = state.copyWith(isLoading: false, error: e.toString());
    }
  }

  Future<void> likePost(String postId) async {
    final index = state.posts.indexWhere((p) => p.id == postId);
    if (index < 0) return;

    final post = state.posts[index];
    final updatedPost = post.copyWith(
      isLiked: !post.isLiked,
      likeCount: post.isLiked ? post.likeCount - 1 : post.likeCount + 1,
    );

    final newPosts = [...state.posts];
    newPosts[index] = updatedPost;
    state = state.copyWith(posts: newPosts);

    try {
      await _repository.toggleLike(postId);
    } catch (e) {
      // 回滚
      final revertedPosts = [...state.posts];
      revertedPosts[index] = post;
      state = state.copyWith(posts: revertedPosts);
    }
  }

  Future<void> createPost(String content, List<String> images) async {
    try {
      final post = await _repository.createPost(content, images);
      state = state.copyWith(posts: [post, ...state.posts]);
    } catch (e) {
      rethrow;
    }
  }
}

final feedProvider = StateNotifierProvider<FeedNotifier, FeedState>((ref) {
  return FeedNotifier(ref.watch(postRepositoryProvider));
});
```

### 3. 动态卡片

```dart
// features/feed/presentation/widgets/post_card.dart
class PostCard extends ConsumerWidget {
  final Post post;

  const PostCard({super.key, required this.post});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return Card(
      margin: const EdgeInsets.symmetric(horizontal: 16, vertical: 8),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          // 用户信息
          ListTile(
            leading: AvatarWidget(
              imageUrl: post.userAvatar,
              size: 40,
            ),
            title: Text(post.userName),
            subtitle: Text(_formatTime(post.createdAt)),
            trailing: IconButton(
              icon: const Icon(Icons.more_horiz),
              onPressed: () => _showOptions(context),
            ),
          ),

          // 内容
          Padding(
            padding: const EdgeInsets.symmetric(horizontal: 16),
            child: Text(post.content),
          ),

          // 图片
          if (post.images?.isNotEmpty ?? false) ...[
            const SizedBox(height: 12),
            _buildImages(context),
          ],

          // 操作栏
          Padding(
            padding: const EdgeInsets.all(8),
            child: Row(
              children: [
                _ActionButton(
                  icon: post.isLiked ? Icons.favorite : Icons.favorite_border,
                  label: '${post.likeCount}',
                  color: post.isLiked ? Colors.red : null,
                  onPressed: () => ref.read(feedProvider.notifier).likePost(post.id),
                ),
                _ActionButton(
                  icon: Icons.mode_comment_outlined,
                  label: '${post.commentCount}',
                  onPressed: () => _showComments(context),
                ),
                _ActionButton(
                  icon: Icons.share_outlined,
                  label: '分享',
                  onPressed: () => _sharePost(context),
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }

  Widget _buildImages(BuildContext context) {
    final images = post.images!;
    if (images.length == 1) {
      return GestureDetector(
        onTap: () => _showImageViewer(context, 0),
        child: ClipRRect(
          borderRadius: BorderRadius.circular(8),
          child: CachedNetworkImage(
            imageUrl: images.first,
            fit: BoxFit.cover,
            width: double.infinity,
          ),
        ),
      );
    }

    return GridView.builder(
      shrinkWrap: true,
      physics: const NeverScrollableScrollPhysics(),
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: images.length == 2 ? 2 : 3,
        crossAxisSpacing: 4,
        mainAxisSpacing: 4,
      ),
      itemCount: images.length.clamp(1, 9),
      itemBuilder: (context, index) {
        return GestureDetector(
          onTap: () => _showImageViewer(context, index),
          child: ClipRRect(
            borderRadius: BorderRadius.circular(8),
            child: CachedNetworkImage(
              imageUrl: images[index],
              fit: BoxFit.cover,
            ),
          ),
        );
      },
    );
  }
}

class _ActionButton extends StatelessWidget {
  final IconData icon;
  final String label;
  final Color? color;
  final VoidCallback onPressed;

  const _ActionButton({
    required this.icon,
    required this.label,
    this.color,
    required this.onPressed,
  });

  @override
  Widget build(BuildContext context) {
    return TextButton.icon(
      icon: Icon(icon, color: color),
      label: Text(label),
      onPressed: onPressed,
    );
  }
}
```

### 4. 发布动态

```dart
// features/feed/presentation/widgets/create_post_sheet.dart
class CreatePostSheet extends ConsumerStatefulWidget {
  const CreatePostSheet({super.key});

  @override
  ConsumerState<CreatePostSheet> createState() => _CreatePostSheetState();
}

class _CreatePostSheetState extends ConsumerState<CreatePostSheet> {
  final _controller = TextEditingController();
  final List<XFile> _images = [];
  bool _isPosting = false;

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  Future<void> _pickImages() async {
    final picker = ImagePicker();
    final images = await picker.pickMultiImage();
    if (images.isNotEmpty) {
      setState(() {
        _images.addAll(images.take(9 - _images.length));
      });
    }
  }

  Future<void> _post() async {
    if (_controller.text.trim().isEmpty && _images.isEmpty) return;

    setState(() => _isPosting = true);
    try {
      // 上传图片
      final imageUrls = await Future.wait(
        _images.map((file) => ref.read(storageProvider).uploadImage(file)),
      );

      // 创建动态
      await ref.read(feedProvider.notifier).createPost(
            _controller.text.trim(),
            imageUrls,
          );

      if (mounted) Navigator.pop(context);
    } catch (e) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('发布失败: $e')),
      );
    } finally {
      setState(() => _isPosting = false);
    }
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.only(
        bottom: MediaQuery.of(context).viewInsets.bottom,
      ),
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          // 头部
          Padding(
            padding: const EdgeInsets.all(16),
            child: Row(
              mainAxisAlignment: MainAxisAlignment.spaceBetween,
              children: [
                TextButton(
                  onPressed: () => Navigator.pop(context),
                  child: const Text('取消'),
                ),
                Text('发布动态', style: Theme.of(context).textTheme.titleMedium),
                FilledButton(
                  onPressed: _isPosting ? null : _post,
                  child: _isPosting
                      ? const SizedBox(
                          width: 16,
                          height: 16,
                          child: CircularProgressIndicator(strokeWidth: 2),
                        )
                      : const Text('发布'),
                ),
              ],
            ),
          ),

          // 输入框
          Padding(
            padding: const EdgeInsets.symmetric(horizontal: 16),
            child: TextField(
              controller: _controller,
              maxLines: 5,
              decoration: const InputDecoration(
                hintText: '分享你的想法...',
                border: InputBorder.none,
              ),
            ),
          ),

          // 图片预览
          if (_images.isNotEmpty)
            SizedBox(
              height: 100,
              child: ListView.builder(
                scrollDirection: Axis.horizontal,
                padding: const EdgeInsets.symmetric(horizontal: 16),
                itemCount: _images.length,
                itemBuilder: (context, index) {
                  return Padding(
                    padding: const EdgeInsets.only(right: 8),
                    child: Stack(
                      children: [
                        Image.file(
                          File(_images[index].path),
                          height: 100,
                          width: 100,
                          fit: BoxFit.cover,
                        ),
                        Positioned(
                          top: 4,
                          right: 4,
                          child: GestureDetector(
                            onTap: () => setState(() => _images.removeAt(index)),
                            child: Container(
                              padding: const EdgeInsets.all(4),
                              decoration: const BoxDecoration(
                                color: Colors.black54,
                                shape: BoxShape.circle,
                              ),
                              child: const Icon(Icons.close, size: 16, color: Colors.white),
                            ),
                          ),
                        ),
                      ],
                    ),
                  );
                },
              ),
            ),

          // 工具栏
          Padding(
            padding: const EdgeInsets.all(16),
            child: Row(
              children: [
                IconButton(
                  icon: const Icon(Icons.image_outlined),
                  onPressed: _images.length < 9 ? _pickImages : null,
                ),
                Text('${_images.length}/9'),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

### 5. 实时消息

```dart
// features/chat/presentation/providers/chat_provider.dart
class ChatNotifier extends StateNotifier<List<Message>> {
  final ChatRepository _repository;
  final String _conversationId;
  StreamSubscription? _subscription;

  ChatNotifier(this._repository, this._conversationId) : super([]) {
    _listenToMessages();
  }

  void _listenToMessages() {
    _subscription = _repository
        .getMessagesStream(_conversationId)
        .listen((messages) {
      state = messages;
    });
  }

  Future<void> sendMessage(String content) async {
    await _repository.sendMessage(_conversationId, content);
  }

  @override
  void dispose() {
    _subscription?.cancel();
    super.dispose();
  }
}

// 聊天页面
class ChatPage extends ConsumerStatefulWidget {
  final String conversationId;
  final String otherUserName;

  const ChatPage({
    super.key,
    required this.conversationId,
    required this.otherUserName,
  });

  @override
  ConsumerState<ChatPage> createState() => _ChatPageState();
}

class _ChatPageState extends ConsumerState<ChatPage> {
  final _messageController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    final messages = ref.watch(chatProvider(widget.conversationId));
    final currentUserId = ref.watch(authProvider).user?.id;

    return Scaffold(
      appBar: AppBar(title: Text(widget.otherUserName)),
      body: Column(
        children: [
          // 消息列表
          Expanded(
            child: ListView.builder(
              reverse: true,
              padding: const EdgeInsets.all(16),
              itemCount: messages.length,
              itemBuilder: (context, index) {
                final message = messages[index];
                final isMe = message.senderId == currentUserId;
                return MessageBubble(
                  message: message,
                  isMe: isMe,
                );
              },
            ),
          ),

          // 输入框
          Container(
            padding: const EdgeInsets.all(8),
            decoration: BoxDecoration(
              color: Theme.of(context).colorScheme.surface,
              boxShadow: [
                BoxShadow(
                  color: Colors.black.withOpacity(0.05),
                  blurRadius: 4,
                ),
              ],
            ),
            child: Row(
              children: [
                Expanded(
                  child: TextField(
                    controller: _messageController,
                    decoration: const InputDecoration(
                      hintText: '输入消息...',
                      border: OutlineInputBorder(),
                    ),
                  ),
                ),
                const SizedBox(width: 8),
                IconButton.filled(
                  icon: const Icon(Icons.send),
                  onPressed: () {
                    if (_messageController.text.trim().isNotEmpty) {
                      ref.read(chatProvider(widget.conversationId).notifier)
                          .sendMessage(_messageController.text.trim());
                      _messageController.clear();
                    }
                  },
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

## 关键技术点

### 1. 实时数据监听

```dart
// 使用 Stream 监听数据变化
Stream<List<Message>> getMessagesStream(String conversationId) {
  return FirebaseFirestore.instance
      .collection('conversations')
      .doc(conversationId)
      .collection('messages')
      .orderBy('createdAt', descending: true)
      .limit(50)
      .snapshots()
      .map((snapshot) => snapshot.docs
          .map((doc) => Message.fromJson(doc.data()))
          .toList());
}
```

### 2. 分页加载

```dart
// 使用 cursor 分页
Future<FeedResult> getFeed({
  DocumentSnapshot? lastDocument,
  int limit = 20,
}) async {
  Query query = FirebaseFirestore.instance
      .collection('posts')
      .orderBy('createdAt', descending: true)
      .limit(limit);

  if (lastDocument != null) {
    query = query.startAfterDocument(lastDocument);
  }

  final snapshot = await query.get();
  return FeedResult(
    posts: snapshot.docs.map((doc) => Post.fromJson(doc.data())).toList(),
    lastDocument: snapshot.docs.isNotEmpty ? snapshot.docs.last : null,
  );
}
```

### 3. 图片上传

```dart
// 上传图片到 Firebase Storage
Future<String> uploadImage(XFile file) async {
  final ref = FirebaseStorage.instance
      .ref()
      .child('images')
      .child('${DateTime.now().millisecondsSinceEpoch}_${file.name}');

  await ref.putFile(File(file.path));
  return await ref.getDownloadURL();
}
```

---

*最后更新: 2026-02-19*

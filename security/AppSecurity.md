# Flutter 安全最佳实践

## 概述

移动应用安全是开发中的重要环节，本文介绍 Flutter 应用中常见的安全问题和防护措施。

## 敏感数据保护

### 1. 避免硬编码敏感信息

```dart
// ❌ 错误：硬编码 API Key
const apiKey = 'sk-abc123...';
final response = await http.get(Uri.parse('https://api.example.com?key=$apiKey'));

// ✅ 正确：使用环境变量
// .env 文件（添加到 .gitignore）
// API_KEY=sk-abc123...

// 使用 flutter_dotenv
import 'package:flutter_dotenv/flutter_dotenv.dart';

await dotenv.load(fileName: ".env");
final apiKey = dotenv.env['API_KEY'];
```

### 2. 安全存储

```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

class SecureStorage {
  static const _storage = FlutterSecureStorage();

  // 存储敏感数据
  static Future<void> saveToken(String token) async {
    await _storage.write(key: 'auth_token', value: token);
  }

  // 读取敏感数据
  static Future<String?> getToken() async {
    return await _storage.read(key: 'auth_token');
  }

  // 删除敏感数据
  static Future<void> deleteToken() async {
    await _storage.delete(key: 'auth_token');
  }

  // 清空所有
  static Future<void> clearAll() async {
    await _storage.deleteAll();
  }
}
```

### 3. 证书锁定 (Certificate Pinning)

```dart
import 'package:dio/dio.dart';
import 'package:dio_certificate_pinning/dio_certificate_pinning.dart';

class SecureHttpClient {
  static Dio createClient() {
    final dio = Dio();

    // 添加证书锁定
    dio.interceptors.add(
      CertificatePinningInterceptor(
        allowedSHAFingerprints: [
          'your-sha256-fingerprint-here',
        ],
      ),
    );

    return dio;
  }
}
```

## API 安全

### 1. 认证机制

```dart
// JWT Token 管理
class AuthService {
  final Dio _dio;
  final SecureStorage _storage;

  AuthService(this._dio, this._storage) {
    // 添加认证拦截器
    _dio.interceptors.add(
      InterceptorsWrapper(
        onRequest: (options, handler) async {
          final token = await _storage.getToken();
          if (token != null) {
            options.headers['Authorization'] = 'Bearer $token';
          }
          return handler.next(options);
        },
        onError: (error, handler) async {
          if (error.response?.statusCode == 401) {
            // Token 过期，尝试刷新
            final refreshed = await _refreshToken();
            if (refreshed) {
              // 重试请求
              return handler.resolve(await _retry(error.requestOptions));
            }
          }
          return handler.next(error);
        },
      ),
    );
  }

  Future<bool> _refreshToken() async {
    try {
      final refreshToken = await _storage.getRefreshToken();
      final response = await _dio.post('/auth/refresh', data: {
        'refresh_token': refreshToken,
      });

      await _storage.saveToken(response.data['access_token']);
      await _storage.saveRefreshToken(response.data['refresh_token']);
      return true;
    } catch (_) {
      await _storage.clearAll();
      return false;
    }
  }
}
```

### 2. 请求安全

```dart
class ApiClient {
  final Dio _dio;

  ApiClient() : _dio = Dio() {
    _dio.options.baseUrl = 'https://api.example.com';
    _dio.options.connectTimeout = const Duration(seconds: 10);
    _dio.options.receiveTimeout = const Duration(seconds: 10);

    // 安全相关头部
    _dio.interceptors.add(
      InterceptorsWrapper(
        onRequest: (options, handler) {
          // 添加安全头部
          options.headers['X-App-Version'] = _getAppVersion();
          options.headers['X-Device-Id'] = _getDeviceId();
          options.headers['X-Platform'] = Platform.operatingSystem;
          return handler.next(options);
        },
      ),
    );
  }

  // 防止重放攻击
  String _generateNonce() {
    return DateTime.now().millisecondsSinceEpoch.toString();
  }

  // 请求签名
  String _signRequest(String method, String path, String timestamp) {
    // 使用 HMAC 签名
    final key = utf8.encode('your-secret-key');
    final message = utf8.encode('$method:$path:$timestamp');
    final hmac = Hmac(sha256, key);
    final digest = hmac.convert(message);
    return digest.toString();
  }
}
```

### 3. 输入验证

```dart
class InputValidator {
  // 邮箱验证
  static bool isValidEmail(String email) {
    return RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(email);
  }

  // 手机号验证
  static bool isValidPhone(String phone) {
    return RegExp(r'^1[3-9]\d{9}$').hasMatch(phone);
  }

  // 密码强度
  static PasswordStrength checkPasswordStrength(String password) {
    if (password.length < 8) return PasswordStrength.weak;
    if (!RegExp(r'[A-Z]').hasMatch(password)) return PasswordStrength.medium;
    if (!RegExp(r'[a-z]').hasMatch(password)) return PasswordStrength.medium;
    if (!RegExp(r'[0-9]').hasMatch(password)) return PasswordStrength.medium;
    if (!RegExp(r'[!@#$%^&*(),.?":{}|<>]').hasMatch(password)) {
      return PasswordStrength.good;
    }
    return PasswordStrength.strong;
  }

  // XSS 防护（清理输入）
  static String sanitize(String input) {
    return input
        .replaceAll('<', '&lt;')
        .replaceAll('>', '&gt;')
        .replaceAll('"', '&quot;')
        .replaceAll("'", '&#x27;')
        .replaceAll('/', '&#x2F;');
  }
}
```

## 代码安全

### 1. 代码混淆

```yaml
# android/app/build.gradle
android {
    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```

```pro
# android/app/proguard-rules.pro
-keep class io.flutter.** { *; }
-keep class io.flutter.plugins.** { *; }
-keep class com.example.app.** { *; }
```

```bash
# iOS 代码混淆
# 使用 Xcode 的 Strip Debug Symbols
flutter build ios --obfuscate --split-debug-info=./debug-info
```

### 2. 防止调试

```dart
class SecurityService {
  static bool get isDebugMode {
    bool inDebugMode = false;
    assert(inDebugMode = true);
    return inDebugMode;
  }

  static void checkDebugMode() {
    if (isDebugMode && kReleaseMode) {
      // 生产环境检测到调试模式
      exit(0);
    }
  }

  // 检测是否被 Hook
  static bool checkHook() {
    // 检测常见的 Hook 框架
    // 仅作为示例，实际实现可能需要原生代码
    return false;
  }
}
```

### 3. 防止逆向工程

```dart
// 使用 root 检测
import 'package:flutter_jailbreak_detection/flutter_jailbreak_detection.dart';

class DeviceSecurity {
  static Future<bool> isJailbroken() async {
    return await FlutterJailbreakDetection.jailbroken;
  }

  static Future<bool> isDevelopmentMode() async {
    return await FlutterJailbreakDetection.developerMode;
  }

  static Future<void> checkSecurity() async {
    if (await isJailbroken()) {
      // 设备已越狱/Root
      throw SecurityException('设备不安全');
    }
  }
}
```

## 数据安全

### 1. 加密存储

```dart
import 'package:encrypt/encrypt.dart';

class EncryptionService {
  static final _key = Key.fromUtf8('your-32-character-secret-key!!');
  static final _iv = IV.fromLength(16);
  static final _encrypter = Encrypter(AES(_key));

  static String encrypt(String plainText) {
    return _encrypter.encrypt(plainText, iv: _iv).base64;
  }

  static String decrypt(String encryptedText) {
    return _encrypter.decrypt64(encryptedText, iv: _iv);
  }
}

// 使用
final encrypted = EncryptionService.encrypt('敏感数据');
final decrypted = EncryptionService.decrypt(encrypted);
```

### 2. 安全传输

```dart
// 强制 HTTPS
class SecureHttpClient {
  static HttpClient createSecureClient() {
    final client = HttpClient()
      ..badCertificateCallback = (cert, host, port) {
        // 生产环境拒绝无效证书
        return false;
      };
    return client;
  }
}

// SSL Pinning（使用 http 包）
class SslPinning {
  static Future<SecurityContext> get securityContext async {
    final context = SecurityContext();
    final data = await rootBundle.load('assets/certificates/cert.pem');
    context.setTrustedCertificatesBytes(data.buffer.asUint8List());
    return context;
  }
}
```

### 3. 日志安全

```dart
class SecureLogger {
  static void debug(String message) {
    if (kDebugMode) {
      developer.log(message, name: 'APP');
    }
  }

  static void error(String message, [Object? error, StackTrace? stackTrace]) {
    // 不记录敏感信息
    final safeMessage = _sanitize(message);

    if (kDebugMode) {
      developer.log(
        safeMessage,
        name: 'APP',
        error: error,
        stackTrace: stackTrace,
        level: 1000,
      );
    }

    // 生产环境上报到监控平台
    if (kReleaseMode) {
      _reportToMonitoring(safeMessage, error, stackTrace);
    }
  }

  static String _sanitize(String message) {
    // 移除可能的敏感信息
    return message
        .replaceAll(RegExp(r'\b\d{16,19}\b'), '****') // 银行卡号
        .replaceAll(RegExp(r'\b[\w.-]+@[\w.-]+\.\w+\b'), '***@***.***'); // 邮箱
  }
}
```

## WebView 安全

```dart
class SecureWebView extends StatelessWidget {
  final String url;

  const SecureWebView({required this.url});

  @override
  Widget build(BuildContext context) {
    return WebView(
      initialUrl: url,
      javascriptMode: JavascriptMode.disabled, // 默认禁用 JS
      onWebViewCreated: (controller) {
        // 限制可加载的域名
      },
      navigationDelegate: (request) {
        final uri = Uri.parse(request.url);

        // 只允许特定域名
        if (!uri.host.endsWith('example.com')) {
          return NavigationDecision.prevent;
        }

        return NavigationDecision.navigate;
      },
      onPageStarted: (url) {
        // 检查 URL 安全性
      },
    );
  }
}
```

## 深度链接安全

```dart
class DeepLinkHandler {
  static final _allowedHosts = ['example.com', 'app.example.com'];

  static Future<void> handle(Uri uri) async {
    // 验证主机
    if (!_allowedHosts.contains(uri.host)) {
      throw SecurityException('不支持的深度链接');
    }

    // 验证 scheme
    if (uri.scheme != 'https' && uri.scheme != 'myapp') {
      throw SecurityException('不支持的 scheme');
    }

    // 验证参数
    final params = uri.queryParameters;
    if (params.containsKey('token')) {
      // 验证 token 有效性
      await _validateToken(params['token']!);
    }

    // 处理链接
    await _processLink(uri);
  }
}
```

## 安全检查清单

### 发布前检查

```dart
class SecurityChecklist {
  static final items = [
    ChecklistItem('敏感信息不硬编码'),
    ChecklistItem('使用 HTTPS'),
    ChecklistItem('实现证书锁定'),
    ChecklistItem('启用代码混淆'),
    ChecklistItem('移除调试日志'),
    ChecklistItem('验证用户输入'),
    ChecklistItem('实现会话超时'),
    ChecklistItem('安全存储 Token'),
    ChecklistItem('限制 WebView 功能'),
    ChecklistItem('验证深度链接'),
  ];

  static void runChecks() {
    for (var item in items) {
      print('[${item.completed ? 'x' : ' '}] ${item.description}');
    }
  }
}
```

## 最佳实践总结

| 类别 | 措施 |
|------|------|
| 数据存储 | flutter_secure_storage、加密敏感数据 |
| 网络传输 | HTTPS、证书锁定、请求签名 |
| 代码保护 | 代码混淆、移除调试代码 |
| 输入验证 | 验证所有用户输入、XSS 防护 |
| 认证授权 | Token 安全存储、会话管理 |
| 环境检测 | 越狱/Root 检测、调试检测 |

---

*最后更新: 2026-02-19*

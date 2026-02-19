# Flutter 平台通道

## 概述

平台通道（Platform Channels）允许 Flutter 与原生平台代码（Android/iOS）通信，实现 Flutter 无法直接访问的平台功能。

## 架构

```
┌─────────────────────────────────────┐
│           Flutter (Dart)            │
│                                     │
│  MethodChannel / EventChannel       │
└─────────────┬───────────────────────┘
              │ Platform Channel
              │ (消息传递)
┌─────────────┴───────────────────────┐
│         Platform Native             │
│                                     │
│  Android (Kotlin/Java)              │
│  iOS (Swift/Objective-C)            │
└─────────────────────────────────────┘
```

## MethodChannel

### 基本用法

```dart
// Dart 端
import 'package:flutter/services.dart';

class BatteryService {
  static const MethodChannel _channel = MethodChannel('com.example/battery');

  Future<int> getBatteryLevel() async {
    try {
      final result = await _channel.invokeMethod<int>('getBatteryLevel');
      return result ?? -1;
    } on PlatformException catch (e) {
      print('获取电量失败: ${e.message}');
      return -1;
    }
  }
}
```

### Android 实现

```kotlin
// android/app/src/main/kotlin/.../MainActivity.kt
import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugin.common.MethodChannel

class MainActivity: FlutterActivity() {
    private val CHANNEL = "com.example/battery"

    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)

        MethodChannel(flutterEngine.dartExecutor.binaryMessenger, CHANNEL)
            .setMethodCallHandler { call, result ->
                when (call.method) {
                    "getBatteryLevel" -> {
                        val level = getBatteryLevel()
                        result.success(level)
                    }
                    else -> result.notImplemented()
                }
            }
    }

    private fun getBatteryLevel(): Int {
        val batteryManager = getSystemService(BATTERY_SERVICE) as BatteryManager
        return batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY)
    }
}
```

### iOS 实现

```swift
// ios/Runner/AppDelegate.swift
import UIKit
import Flutter

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
    override func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        let controller = window?.rootViewController as! FlutterViewController
        let batteryChannel = FlutterMethodChannel(
            name: "com.example/battery",
            binaryMessenger: controller.binaryMessenger
        )

        batteryChannel.setMethodCallHandler { (call, result) in
            switch call.method {
            case "getBatteryLevel":
                let level = self.getBatteryLevel()
                result(level)
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        return super.application(application, didFinishLaunchingWithOptions: launchOptions)
    }

    private func getBatteryLevel() -> Int {
        let device = UIDevice.current
        device.isBatteryMonitoringEnabled = true
        return Int(device.batteryLevel * 100)
    }
}
```

## 传递参数

### Dart 端

```dart
Future<String> getDeviceInfo(String deviceId) async {
  try {
    final result = await _channel.invokeMethod<String>(
      'getDeviceInfo',
      {'deviceId': deviceId, 'includeBattery': true},
    );
    return result ?? '';
  } on PlatformException catch (e) {
    throw Exception('获取设备信息失败: ${e.message}');
  }
}
```

### Android 端

```kotlin
when (call.method) {
    "getDeviceInfo" -> {
        val deviceId = call.argument<String>("deviceId")
        val includeBattery = call.argument<Boolean>("includeBattery") ?: false

        val info = mutableMapOf<String, Any>()
        info["id"] = deviceId ?: ""

        if (includeBattery) {
            info["battery"] = getBatteryLevel()
        }

        result.success(info)
    }
}
```

### iOS 端

```swift
case "getDeviceInfo":
    let deviceId = call.arguments as? [String: Any]
    let includeBattery = deviceId?["includeBattery"] as? Bool ?? false

    var info: [String: Any] = ["id": deviceId?["deviceId"] as? String ?? ""]

    if includeBattery {
        info["battery"] = getBatteryLevel()
    }

    result(info)
```

## 错误处理

### Dart 端抛出错误

```dart
Future<void> sendNotification(String title, String body) async {
  try {
    await _channel.invokeMethod('sendNotification', {
      'title': title,
      'body': body,
    });
  } on PlatformException catch (e) {
    switch (e.code) {
      case 'PERMISSION_DENIED':
        throw NotificationPermissionException();
      case 'NOT_AVAILABLE':
        throw NotificationNotAvailableException();
      default:
        throw NotificationException(e.message ?? '未知错误');
    }
  }
}
```

### Android 端返回错误

```kotlin
when (call.method) {
    "sendNotification" -> {
        if (!hasNotificationPermission()) {
            result.error("PERMISSION_DENIED", "通知权限未授权", null)
            return@setMethodCallHandler
        }

        try {
            sendNotificationInternal(title, body)
            result.success(null)
        } catch (e: Exception) {
            result.error("NOT_AVAILABLE", "发送通知失败: ${e.message}", null)
        }
    }
}
```

### iOS 端返回错误

```swift
case "sendNotification":
    if !hasNotificationPermission() {
        result(FlutterError(
            code: "PERMISSION_DENIED",
            message: "通知权限未授权",
            details: nil
        ))
        return
    }

    do {
        try sendNotificationInternal(title: title, body: body)
        result(nil)
    } catch {
        result(FlutterError(
            code: "NOT_AVAILABLE",
            message: "发送通知失败: \(error.localizedDescription)",
            details: nil
        ))
    }
```

## EventChannel

### 用于事件流

```dart
// Dart 端 - 接收连续事件
class ConnectivityService {
  static const EventChannel _channel = EventChannel('com.example/connectivity');

  Stream<String> get connectivityStream {
    return _channel.receiveBroadcastStream().map((event) => event as String);
  }
}

// 使用
connectivityService.connectivityStream.listen((status) {
  print('网络状态: $status');
});
```

### Android 实现

```kotlin
class ConnectivityEventChannel(private val context: Context) : EventChannel.StreamHandler {
    private var connectivityManager: ConnectivityManager? = null
    private var callback: ConnectivityManager.NetworkCallback? = null

    override fun onListen(arguments: Any?, events: EventChannel.EventSink) {
        connectivityManager = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager

        callback = object : ConnectivityManager.NetworkCallback() {
            override fun onAvailable(network: Network) {
                events.success("connected")
            }

            override fun onLost(network: Network) {
                events.success("disconnected")
            }
        }

        connectivityManager?.registerDefaultNetworkCallback(callback!!)
    }

    override fun onCancel(arguments: Any?) {
        callback?.let { connectivityManager?.unregisterNetworkCallback(it) }
        callback = null
    }
}

// 在 MainActivity 中注册
EventChannel(flutterEngine.dartExecutor.binaryMessenger, "com.example/connectivity")
    .setStreamHandler(ConnectivityEventChannel(this))
```

### iOS 实现

```swift
class ConnectivityStreamHandler: NSObject, FlutterStreamHandler {
    private var eventSink: FlutterEventSink?

    func onListen(withArguments arguments: Any?, eventSink events: @escaping FlutterEventSink) -> FlutterError? {
        eventSink = events

        // 监听网络状态变化
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(connectivityChanged),
            name: .connectivityDidChange,
            object: nil
        )

        return nil
    }

    func onCancel(withArguments arguments: Any?) -> FlutterError? {
        NotificationCenter.default.removeObserver(self)
        eventSink = nil
        return nil
    }

    @objc private func connectivityChanged(_ notification: Notification) {
        eventSink?("connected")
    }
}

// 在 AppDelegate 中注册
let connectivityChannel = FlutterEventChannel(
    name: "com.example/connectivity",
    binaryMessenger: controller.binaryMessenger
)
connectivityChannel.setStreamHandler(ConnectivityStreamHandler())
```

## 完整示例：生物识别

### Dart 端

```dart
import 'package:flutter/services.dart';

enum BiometricType { fingerprint, faceId, none }

class BiometricService {
  static const MethodChannel _channel = MethodChannel('com.example/biometric');

  Future<BiometricType> getAvailableBiometrics() async {
    try {
      final result = await _channel.invokeMethod<String>('getAvailableBiometrics');
      return switch (result) {
        'fingerprint' => BiometricType.fingerprint,
        'faceId' => BiometricType.faceId,
        _ => BiometricType.none,
      };
    } catch (_) {
      return BiometricType.none;
    }
  }

  Future<bool> authenticate({String localizedReason = '请验证身份'}) async {
    try {
      final result = await _channel.invokeMethod<bool>('authenticate', {
        'localizedReason': localizedReason,
      });
      return result ?? false;
    } on PlatformException catch (e) {
      print('认证失败: ${e.message}');
      return false;
    }
  }
}
```

### 使用

```dart
class BiometricPage extends StatefulWidget {
  const BiometricPage({super.key});

  @override
  State<BiometricPage> createState() => _BiometricPageState();
}

class _BiometricPageState extends State<BiometricPage> {
  final BiometricService _biometricService = BiometricService();
  BiometricType _biometricType = BiometricType.none;

  @override
  void initState() {
    super.initState();
    _checkBiometrics();
  }

  Future<void> _checkBiometrics() async {
    final type = await _biometricService.getAvailableBiometrics();
    setState(() => _biometricType = type);
  }

  Future<void> _authenticate() async {
    final success = await _biometricService.authenticate(
      localizedReason: '请验证以继续',
    );

    if (success && mounted) {
      Navigator.of(context).pushReplacementNamed('/home');
    } else {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text('认证失败')),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('生物识别')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('可用生物识别: ${_biometricType.name}'),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _biometricType != BiometricType.none
                  ? _authenticate
                  : null,
              child: const Text('验证身份'),
            ),
          ],
        ),
      ),
    );
  }
}
```

## 数据类型映射

| Dart | Android | iOS |
|------|---------|-----|
| `null` | `null` | `nil` |
| `bool` | `Boolean` | `NSNumber(BOOL)` |
| `int` | `Int` | `NSNumber(NSInteger)` |
| `double` | `Double` | `NSNumber(double)` |
| `String` | `String` | `NSString` |
| `Uint8List` | `byte[]` | `FlutterStandardTypedData` |
| `List` | `ArrayList` | `NSArray` |
| `Map` | `HashMap` | `NSDictionary` |

## 最佳实践

### 1. 封装平台通道

```dart
// ✅ 封装为服务类
class PlatformService {
  static const MethodChannel _channel = MethodChannel('com.example/platform');

  Future<String?> getPlatformVersion() async {
    return _channel.invokeMethod<String>('getPlatformVersion');
  }
}

// ❌ 直接在 Widget 中使用
class MyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 不要在这里直接调用 MethodChannel
  }
}
```

### 2. 命名规范

```dart
// 通道名称使用反向域名
static const MethodChannel _channel = MethodChannel('com.example.app/feature');

// 方法名称使用 camelCase
await _channel.invokeMethod('getBatteryLevel');
await _channel.invokeMethod('sendNotification');
```

### 3. 错误码定义

```dart
// 定义错误码常量
abstract class PlatformErrorCodes {
  static const String permissionDenied = 'PERMISSION_DENIED';
  static const String notAvailable = 'NOT_AVAILABLE';
  static const String invalidArgument = 'INVALID_ARGUMENT';
}
```

### 4. 平台检测

```dart
import 'dart:io' show Platform;

Future<String> getDeviceInfo() async {
  if (Platform.isAndroid) {
    return await _channel.invokeMethod('getAndroidDeviceInfo');
  } else if (Platform.isIOS) {
    return await _channel.invokeMethod('getIOSDeviceInfo');
  }
  throw UnsupportedError('不支持的平台');
}
```

---

*最后更新: 2026-02-19*

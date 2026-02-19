# Flutter 插件开发

## 概述

Flutter 插件是封装平台特定功能的 Dart 包，通过平台通道（Platform Channels）与原生代码交互。

## 插件结构

### 创建插件项目

```bash
# 创建插件项目
flutter create --template=plugin --platforms=android,ios my_plugin

# 或创建支持多平台的插件
flutter create --template=plugin --platforms=android,ios,web,macos,windows,linux my_plugin
```

### 目录结构

```
my_plugin/
├── lib/
│   ├── my_plugin.dart           # Dart API 入口
│   ├── src/
│   │   └── my_plugin_platform_interface.dart  # 平台接口
│   └── my_plugin_web.dart       # Web 实现
│
├── android/                     # Android 原生代码
│   ├── src/main/kotlin/
│   │   └── com/example/my_plugin/
│   │       └── MyPlugin.kt
│   └── build.gradle
│
├── ios/                         # iOS 原生代码
│   ├── Classes/
│   │   └── MyPlugin.swift
│   └── my_plugin.podspec
│
├── macos/                       # macOS 原生代码
├── windows/                     # Windows 原生代码
├── linux/                       # Linux 原生代码
├── web/                         # Web 实现
│
├── example/                     # 示例应用
│   └── lib/main.dart
│
├── test/                        # 单元测试
│
└── pubspec.yaml
```

## Dart 端设计

### 平台接口模式

```dart
// lib/src/my_plugin_platform_interface.dart
import 'package:plugin_platform_interface/plugin_platform_interface.dart';

abstract class MyPluginPlatform extends PlatformInterface {
  MyPluginPlatform() : super(token: _token);

  static final Object _token = Object();

  static MyPluginPlatform _instance = MethodChannelMyPlugin();

  static MyPluginPlatform get instance => _instance;

  static set instance(MyPluginPlatform instance) {
    PlatformInterface.verifyToken(instance, _token);
    _instance = instance;
  }

  Future<String?> getPlatformVersion();
  Future<bool> doSomething(String param);
}
```

### MethodChannel 实现

```dart
// lib/src/my_plugin_method_channel.dart
import 'package:flutter/services.dart';

class MethodChannelMyPlugin extends MyPluginPlatform {
  @override
  final MethodChannel channel = const MethodChannel('my_plugin');

  @override
  Future<String?> getPlatformVersion() async {
    final version = await channel.invokeMethod<String>('getPlatformVersion');
    return version;
  }

  @override
  Future<bool> doSomething(String param) async {
    final result = await channel.invokeMethod<bool>('doSomething', {
      'param': param,
    });
    return result ?? false;
  }
}
```

### 公开 API

```dart
// lib/my_plugin.dart
export 'src/my_plugin_platform_interface.dart';

class MyPlugin {
  static Future<String?> get platformVersion async {
    return MyPluginPlatform.instance.getPlatformVersion();
  }

  static Future<bool> doSomething(String param) async {
    return MyPluginPlatform.instance.doSomething(param);
  }
}
```

## Android 实现

### Kotlin 实现

```kotlin
// android/src/main/kotlin/com/example/my_plugin/MyPlugin.kt
package com.example.my_plugin

import android.content.Context
import android.os.Build
import io.flutter.plugin.common.MethodCall
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugin.common.MethodChannel.MethodCallHandler
import io.flutter.plugin.common.MethodChannel.Result

class MyPlugin : MethodCallHandler {
    private lateinit var context: Context

    fun onAttachedToEngine(context: Context, channel: MethodChannel) {
        this.context = context
        channel.setMethodCallHandler(this)
    }

    override fun onMethodCall(call: MethodCall, result: Result) {
        when (call.method) {
            "getPlatformVersion" -> {
                result.success("Android ${Build.VERSION.RELEASE}")
            }
            "doSomething" -> {
                val param = call.argument<String>("param")
                if (param != null) {
                    val success = doSomethingInternal(param)
                    result.success(success)
                } else {
                    result.error("INVALID_ARGUMENT", "param is required", null)
                }
            }
            else -> result.notImplemented()
        }
    }

    private fun doSomethingInternal(param: String): Boolean {
        // 实现具体逻辑
        return true
    }

    fun onDetachedFromEngine() {
        // 清理资源
    }
}
```

### 插件注册

```kotlin
// android/src/main/kotlin/com/example/my_plugin/MyPluginPlugin.kt
package com.example.my_plugin

import io.flutter.embedding.engine.plugins.FlutterPlugin

class MyPluginPlugin : FlutterPlugin {
    private lateinit var channel: MethodChannel
    private lateinit var plugin: MyPlugin

    override fun onAttachedToEngine(binding: FlutterPlugin.FlutterPluginBinding) {
        channel = MethodChannel(binding.binaryMessenger, "my_plugin")
        plugin = MyPlugin()
        plugin.onAttachedToEngine(binding.applicationContext, channel)
    }

    override fun onDetachedFromEngine(binding: FlutterPlugin.FlutterPluginBinding) {
        plugin.onDetachedFromEngine()
        channel.setMethodCallHandler(null)
    }
}
```

### build.gradle 配置

```gradle
// android/build.gradle
group 'com.example.my_plugin'
version '1.0'

buildscript {
    repositories {
        google()
        mavenCentral()
    }

    dependencies {
        classpath 'com.android.tools.build:gradle:7.3.0'
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.8.0"
    }
}

android {
    namespace 'com.example.my_plugin'
    compileSdk 33

    defaultConfig {
        minSdk 21
    }

    sourceSets {
        main.java.srcDirs += 'src/main/kotlin'
    }
}
```

## iOS 实现

### Swift 实现

```swift
// ios/Classes/MyPlugin.swift
import Flutter
import UIKit

public class MyPlugin: NSObject, FlutterPlugin {
    public static func register(with registrar: FlutterPluginRegistrar) {
        let channel = FlutterMethodChannel(
            name: "my_plugin",
            binaryMessenger: registrar.messenger()
        )
        let instance = MyPlugin()
        registrar.addMethodCallDelegate(instance, channel: channel)
    }

    public func handle(_ call: FlutterMethodCall, result: @escaping FlutterResult) {
        switch call.method {
        case "getPlatformVersion":
            result("iOS " + UIDevice.current.systemVersion)

        case "doSomething":
            guard let args = call.arguments as? [String: Any],
                  let param = args["param"] as? String else {
                result(FlutterError(code: "INVALID_ARGUMENT", message: "param is required", details: nil))
                return
            }
            let success = doSomethingInternal(param: param)
            result(success)

        default:
            result(FlutterMethodNotImplemented)
        }
    }

    private func doSomethingInternal(param: String) -> Bool {
        // 实现具体逻辑
        return true
    }
}
```

### podspec 配置

```ruby
# ios/my_plugin.podspec
Pod::Spec.new do |s|
  s.name             = 'my_plugin'
  s.version          = '0.0.1'
  s.summary          = 'A Flutter plugin'
  s.description      = <<-DESC
A new Flutter plugin.
                       DESC
  s.homepage         = 'http://example.com'
  s.license          = { :file => '../LICENSE' }
  s.author           = { 'Your Name' => 'your.email@example.com' }
  s.source           = { :path => '.' }
  s.source_files     = 'Classes/**/*'
  s.dependency 'Flutter'
  s.platform         = :ios, '11.0'
  s.swift_version    = '5.0'
end
```

## 事件流支持

### Dart 端 EventChannel

```dart
// lib/src/my_plugin_method_channel.dart
class MethodChannelMyPlugin extends MyPluginPlatform {
  final MethodChannel _methodChannel = const MethodChannel('my_plugin');
  final EventChannel _eventChannel = const EventChannel('my_plugin/events');

  Stream<String>? _eventStream;

  Stream<String> get eventStream {
    _eventStream ??= _eventChannel.receiveBroadcastStream().map((event) => event as String);
    return _eventStream!;
  }
}
```

### Android 事件实现

```kotlin
// android/src/main/kotlin/com/example/my_plugin/MyPlugin.kt
class MyPlugin : MethodCallHandler {
    private var eventSink: EventChannel.EventSink? = null

    fun setupEventChannel(messenger: BinaryMessenger) {
        val eventChannel = EventChannel(messenger, "my_plugin/events")
        eventChannel.setStreamHandler(object : EventChannel.StreamHandler {
            override fun onListen(arguments: Any?, events: EventChannel.EventSink) {
                eventSink = events
                startSendingEvents()
            }

            override fun onCancel(arguments: Any?) {
                eventSink = null
                stopSendingEvents()
            }
        })
    }

    private fun startSendingEvents() {
        // 开始发送事件
        eventSink?.success("Event 1")
        eventSink?.success("Event 2")
    }

    private fun stopSendingEvents() {
        // 停止发送事件
    }
}
```

### iOS 事件实现

```swift
// ios/Classes/MyPlugin.swift
public class MyPlugin: NSObject, FlutterPlugin, FlutterStreamHandler {
    private var eventSink: FlutterEventSink?

    public static func register(with registrar: FlutterPluginRegistrar) {
        let channel = FlutterMethodChannel(name: "my_plugin", binaryMessenger: registrar.messenger())
        let instance = MyPlugin()
        registrar.addMethodCallDelegate(instance, channel: channel)

        let eventChannel = FlutterEventChannel(name: "my_plugin/events", binaryMessenger: registrar.messenger())
        eventChannel.setStreamHandler(instance)
    }

    public func onListen(withArguments arguments: Any?, eventSink events: @escaping FlutterEventSink) -> FlutterError? {
        self.eventSink = events
        startSendingEvents()
        return nil
    }

    public func onCancel(withArguments arguments: Any?) -> FlutterError? {
        self.eventSink = nil
        return nil
    }

    private func startSendingEvents() {
        eventSink?("Event 1")
        eventSink?("Event 2")
    }
}
```

## Web 支持

### Web 实现

```dart
// lib/my_plugin_web.dart
import 'dart:async';
import 'package:flutter_web_plugins/flutter_web_plugins.dart';
import 'my_plugin_platform_interface.dart';

class MyPluginWeb extends MyPluginPlatform {
  static void registerWith(Registrar registrar) {
    MyPluginPlatform.instance = MyPluginWeb();
  }

  @override
  Future<String?> getPlatformVersion() async {
    final userAgent = html.window.navigator.userAgent;
    return 'Web ($userAgent)';
  }

  @override
  Future<bool> doSomething(String param) async {
    // Web 实现
    return true;
  }
}
```

### pubspec.yaml 配置

```yaml
# pubspec.yaml
name: my_plugin
description: A Flutter plugin
version: 0.0.1

environment:
  sdk: '>=3.0.0 <4.0.0'
  flutter: '>=3.0.0'

dependencies:
  flutter:
    sdk: flutter
  plugin_platform_interface: ^2.0.0

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^2.0.0

flutter:
  plugin:
    platforms:
      android:
        package: com.example.my_plugin
        pluginClass: MyPluginPlugin
      ios:
        pluginClass: MyPlugin
      web:
        pluginClass: MyPluginWeb
        fileName: my_plugin_web.dart
```

## 最佳实践

### 1. 错误处理

```dart
// 统一错误码
abstract class PluginErrorCodes {
  static const String invalidArgument = 'INVALID_ARGUMENT';
  static const String permissionDenied = 'PERMISSION_DENIED';
  static const String notAvailable = 'NOT_AVAILABLE';
}

// 封装错误
class PluginException implements Exception {
  final String code;
  final String message;

  PluginException(this.code, this.message);

  factory PluginException.fromPlatformException(PlatformException e) {
    return PluginException(e.code, e.message ?? 'Unknown error');
  }
}

// 使用
Future<bool> doSomething(String param) async {
  try {
    return await channel.invokeMethod<bool>('doSomething', {'param': param}) ?? false;
  } on PlatformException catch (e) {
    throw PluginException.fromPlatformException(e);
  }
}
```

### 2. 权限处理

```dart
// 检查和请求权限
class MyPlugin {
  static Future<bool> checkPermission() async {
    return await channel.invokeMethod<bool>('checkPermission') ?? false;
  }

  static Future<bool> requestPermission() async {
    return await channel.invokeMethod<bool>('requestPermission') ?? false;
  }
}
```

### 3. 文档和示例

```dart
/// MyPlugin - 示例插件
///
/// 提供平台特定功能的统一 API。
///
/// 使用示例:
/// ```dart
/// final version = await MyPlugin.platformVersion;
/// print(version);
/// ```
class MyPlugin {
  /// 获取平台版本
  ///
  /// 返回平台版本字符串，如 "Android 13" 或 "iOS 16.0"
  static Future<String?> get platformVersion async {
    return MyPluginPlatform.instance.getPlatformVersion();
  }
}
```

### 4. 单元测试

```dart
// test/my_plugin_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:my_plugin/my_plugin.dart';
import 'package:my_plugin/my_plugin_platform_interface.dart';
import 'package:plugin_platform_interface/plugin_platform_interface.dart';

class MockMyPluginPlatform with MockPlatformInterfaceMixin implements MyPluginPlatform {
  @override
  Future<String?> getPlatformVersion() async => 'Mock Platform';
}

void main() {
  group('MyPlugin', () {
    late MyPluginPlatform platform;

    setUp(() {
      platform = MockMyPluginPlatform();
      MyPluginPlatform.instance = platform;
    });

    test('getPlatformVersion', () async {
      expect(await MyPlugin.platformVersion, 'Mock Platform');
    });
  });
}
```

---

*最后更新: 2026-02-19*

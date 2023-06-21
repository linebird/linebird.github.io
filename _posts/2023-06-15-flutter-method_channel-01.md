---
layout: post
title: "Flutter Method Channel - 01"
date: 2023-06-15 11:02:00 +0900
categories: [Blogging]
tags: [flutter, method channel]
---

## Flutter 네이티브 통신 방법

Flutter는 Kotlin 또는 Swift와 같은 언어로 작성된 코드 또는 API에 액세스하거나, 네이티브 C 기반 API를 호출하거나, Flutter 앱에 네이티브 컨트롤을 임베드하거나, 기존 애플리케이션에 Flutter를 임베드하는 등 다양한 상호 운용성 메커니즘을 제공한다.

### **Platform channels**

Flutter에서는 MethodChannel과 BinaryMessage로 Android나 iOS와 통신을 수행할 수 있다. Dart와 Kotlin 또는 Swift와 같은 언어로 작성된 플랫폼 구성 요소 간에 메시지를 주고받을 수 있다.데이터는 Dart에서 **Map**과 같은 표준 포맷으로 직렬화되고 Kotlin(예: **HashMap**) 또는 Swift(예: **Dictionary**)에서 deserialze 된다.  
아래의 그림은 iOS와 Android 플랫폼과 통신하는 방법을 나타낸다.

![platform channels](https://docs.flutter.dev/assets/images/docs/arch-overview/platform-channels.png)
아래는 Kotlin(Android) 또는 Swift(iOS)에서 수신 이벤트 핸들러에 대한 Dart 호출의 짧은 platform channel 예제이다.:

```dart
// Dart side
const channel = MethodChannel('foo');
final String greeting = await channel.invokeMethod('bar', 'world');
print(greeting);
```

```kotlin
// Android (Kotlin)
val channel = MethodChannel(flutterView, "foo")
channel.setMethodCallHandler { call, result ->
  when (call.method) {
    "bar" -> result.success("Hello, ${call.arguments}")
    else -> result.notImplemented()
  }
}
```

```swift
// iOS (Swift)
let channel = FlutterMethodChannel(name: "foo", binaryMessenger: flutterView)
channel.setMethodCallHandler {
  (call: FlutterMethodCall, result: FlutterResult) -> Void in
  switch (call.method) {
    case "bar": result("Hello, \(call.arguments as! String)")
    default: result(FlutterMethodNotImplemented)
  }
}
```

## MethodChannel 실제 사용 코드 예제

1. flutter에서 MethodChannel 선언 및 수신 처리
![code-sample-01](/assets/img/2023/06/code-06-15-01.png)
5 : MethodChannel 선언  
10 ~ 22 : setMethodCallHandler 처리. "coocon_report"인 경우, call.arguments를 jsonDecode(call.arguments)하여 결과 저장
2. flutter에서 로그인 시, invokeMethod 호출하여, native와 통신
![code-sample-01](/assets/img/2023/06/code-06-15-02.png)
22 : 로그인 시에 'coocon'으로 invokeMethod 호출
3. Android

```kotlin
import io.flutter.plugin.common.MethodCall
import io.flutter.plugin.common.MethodChannel
...
class MainActivity: FlutterFragmentActivity(), SASRunCompletedListener,
    SASRunStatusChangedListener {
    private lateinit var sasManager: SASManager
    private val TAG = MainActivity::class.java.simpleName
    private val CHANNEL = "com.aromit.hmds_app_normal.coocon"
    private var channel: MethodChannel? = null

    ...
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)

        GeneratedPluginRegistrant.registerWith(flutterEngine)

        val handler =
            MethodChannel.MethodCallHandler { methodCall: MethodCall, result: MethodChannel.Result ->
                if (methodCall.method == "getPlatformVersion") {
                    result.success("Android Version: " + Build.VERSION.RELEASE)
                } else if (methodCall.method == "coocon") {
                    var inString = methodCall.arguments.toString()
                    var resp = sasManager.run(0, inString)
                    result.success(resp)
                } else {
                    result.notImplemented()
                }
            }
        channel = MethodChannel(flutterEngine.dartExecutor, CHANNEL)

        channel!!.setMethodCallHandler(handler)
    }
    ...      
```

4. iOS

```swift
import Flutter
import flutter_local_notifications
import UIKit

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate, SASManagerDelegate {
  private var sasSecureData: SASSecurData?
  private var sasManager: SASManager?
  private var channel: FlutterMethodChannel?
  ...
  ...
    // flutter MethodChannel "coocon" 구현 
    channel = FlutterMethodChannel(name: "com.aromit.hmds_app_normal.coocon", binaryMessenger: window?.rootViewController as! FlutterBinaryMessenger)
    channel?.setMethodCallHandler({
      (call: FlutterMethodCall, result: @escaping FlutterResult) -> Void in
      if (call.method == "coocon") {
        let inString = call.arguments as! String
//        let resp = self.sasManager?.run(0, in: inString)
        let resp = self.sasManager?.asyncRun(0, in: inString)
        result(resp)
      } else {
        result(FlutterMethodNotImplemented)
      }
    })  
```

### 참조

1. [Writing custom platform-specific code - Flutter documentation](https://docs.flutter.dev/platform-integration/platform-channels)
2. [Using Flutter’s MethodChannel to invoke Kotlin code for Android](https://blog.logrocket.com/using-flutters-methodchannel-invoke-kotlin-code-android/)
3. [[Flutter/Swift] 플랫폼 통신(IOS) - Method Channel](https://velog.io/@tygerhwang/FLUTTER-Platform-Channel-with-IOS)
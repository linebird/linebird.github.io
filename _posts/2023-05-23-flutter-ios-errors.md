---
layout: post
title: "Flutter iOS Errors - 01"
date: 2023-05-17 15:02:00 +0900
categories: [Blogging]
tags: [flutter, iOS]
---

## iOS Errors

회사내에서 건기식프로젝트를 진행하면서 발생한 iOS 관련한 에러들에 대한 해결 방안 모음들을 정리하였다.

## 1.Firebase 설정 미제공

### 1) 현상

앱이 구동은 되지만, splash screen이 나타난 이후, 그대로 멈춘채로 진행이 되지 않는다.  
에러로그에 [FirebaseMessaging][I-FCM002022] **APNS device token not set before retrieving FCM Token for Sender ID '255843742106'.Be sure to re-retrieve the FCM token once the APNS device token is set.** 이라고 표시된다.

### 2) 에러 로그

```text
10.7.0 - [FirebaseAnalytics][I-ACS002003] Measurement timer canceled
Connecting to VM Service at ws://127.0.0.1:53133/VKO0CKUPNvE=/ws

FLTFirebaseMessaging: An error occurred while calling method Messaging#getToken, errorOrNil => {
    NSLocalizedFailureReason = "No APNS token specified before fetching FCM Token";
}

10.7.0 - [FirebaseMessaging][I-FCM002022] APNS device token not set before retrieving FCM Token for Sender ID '255843742106'.Be sure to re-retrieve the FCM token once the APNS device token is set.

10.7.0 - [FirebaseMessaging][I-FCM002022] Declining request for FCM Token since no APNS Token specified

[VERBOSE-2:dart_vm_initializer.cc(41)] Unhandled Exception: [firebase_messaging/unknown] An unknown error has occurred.

#0      StandardMethodCodec.decodeEnvelope
message_codecs.dart:653

#1      MethodChannel._invokeMethod
platform_channel.dart:315

<asynchronous suspension>

#2      MethodChannel.invokeMapMethod
platform_channel.dart:518

<asynchronous suspension>

#3      MethodChannelFirebaseMessaging.getToken
method_channel_messaging.dart:224

<asynchronous suspension>

#4      main
main.dart:156

<asynchronous suspension>
```

### 3) 해결 방법

XCode IDE에서 "**Signing&Capablities**" 탭을 선택 후, "**+ Capability**"를 클릭한다.  
"Background Modes"를 추가 후, "Background fetch"와 "Remote notification"을 선택하여 설정하면 해결된다.  

![XCode Signing&Capablities](https://i.stack.imgur.com/hXGZW.png)

## 2. info.plist 정확한 설정 미숙으로 발생

### 1) 현상

splash screen 이후, app 종료.

### 2) 에러 로그

```text
[access] This app has crashed because it attempted to access privacy-sensitive data without a usage description.  The app's Info.plist must contain an NSPhotoLibraryUsageDescription key with a string value explaining to the user how the app uses this data.

* thread #33, queue = 'com.apple.root.default-qos', stop reason = signal SIGABRT
    frame #0: 0x00000002132bdbc4 libsystem_kernel.dylib`__abort_with_payload + 8

libsystem_kernel.dylib`:

->  0x2132bdbc4 <+8>:  b.lo   0x2132bdbe4               ; <+40>
    0x2132bdbc8 <+12>: pacibsp
    0x2132bdbcc <+16>: stp    x29, x30, [sp, #-0x10]!
    0x2132bdbd0 <+20>: mov    x29, sp

Target 0: (Runner) stopped.

Lost connection to device.
```

```text
[access] This app has crashed because it attempted to access privacy-sensitive data without a usage description.  The app's Info.plist must contain an NSCameraUsageDescription key with a string value explaining to the user how the app uses this data.

* thread #34, queue = 'com.apple.root.default-qos', stop reason = signal SIGABRT
    frame #0: 0x00000002132bdbc4 libsystem_kernel.dylib`__abort_with_payload + 8

libsystem_kernel.dylib`:
->  0x2132bdbc4 <+8>:  b.lo   0x2132bdbe4               ; <+40>
    0x2132bdbc8 <+12>: pacibsp
    0x2132bdbcc <+16>: stp    x29, x30, [sp, #-0x10]!
    0x2132bdbd0 <+20>: mov    x29, sp

Target 0: (Runner) stopped.

Lost connection to device.
```

```text
[access] This app has crashed because it attempted to access privacy-sensitive data without a usage description.  The app's Info.plist must contain an NSMicrophoneUsageDescription key with a string value explaining to the user how the app uses this data.

* thread #31, queue = 'com.apple.root.default-qos', stop reason = signal SIGABRT
    frame #0: 0x00000002132bdbc4 libsystem_kernel.dylib`__abort_with_payload + 8

libsystem_kernel.dylib`:
->  0x2132bdbc4 <+8>:  b.lo   0x2132bdbe4               ; <+40>
    0x2132bdbc8 <+12>: pacibsp
    0x2132bdbcc <+16>: stp    x29, x30, [sp, #-0x10]!
    0x2132bdbd0 <+20>: mov    x29, sp

Target 0: (Runner) stopped.

Lost connection to device.
```

### 3) 해결 방법

iOS 권한과 관련한 허용을 해줄 수 있도록 설정을 추가하였다.  

**NSPhotoLibraryUsageDescription**(사진)  
**NSCameraUsageDescription**(카메라)  
**NSMicrophoneUsageDescription**(전화)

info.plist 파일에 위의 권한 관련 값을 설정하면 된다.  
a. information property List에서 오른쪽 클린 후, "Add Row" 선택  
b. 추가된 입력란에 "NSPhotoLibraryUsageDescription" 입력하면 자동으로 "**Privacy - Photo Library Usage Description**"로 입력됨.  
c. 마찬가지로 "NSCameraUsageDescription" 입력하면 자동으로 "**Privacy - Camera Usage Description**" 입력됨  
d. "NSMicrophoneUsageDescription" 입력하면 자동으로 "**Privacy - Microphone Usage Description**" 입력됨  

![](/assets/img/2023/05/ios-permission-add.jpg)

이후 app 실행하면 권한 요청 팝업메시지 실행됨.

## 참조

- [iOS 10 App has crashed because it attempted to access privacy-sensitive data](https://stackoverflow.com/questions/39825034/ios-10-app-has-crashed-because-it-attempted-to-access-privacy-sensitive-data)
- [Protecting the User’s Privacy](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy)

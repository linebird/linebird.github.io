---
layout: post
title: "Flutter에서 NICE 본인인증"
date: 2023-05-10 11:02:00 +0900
categories: [Blogging]
tags: [flutter]
---

## NICE 본인인증

본인인증을 꼭 도입하게 되는 이유는 보통 결재서비스가 함께 이루어지는 application 개발 시,  
인터넷 상에서의 실명확인 시 실제 본인 여부의 확인을 위한 본인인증 서비스를 사용한다.  
본인인증을 하게되면, 확인기관(NICE, KCB등)에서 CI번호와 DI번호를 발급하여 사용하게 된다.  
NICE에서는 본인인증을 위하여, NICE 표준창을 open하여 사용할 수 있게 가이드하고 있다.
![nice 인증 flow](/assets/img/2023/05/nice_auth.jpg)  
[NICE 개발가이드 파일 다운로드](https://www.niceid.co.kr/common/file_down.jsp?path=/documents/api_checkplus.xlsx&file=api_checkplus.xlsx)

### NICE 표준창 open

NICE 인증을 사용하는 웹 서버의 구현 부분은 아래와 같다

- 표준창서비스 호출

```html
action = "https://nice.checkplus.co.kr/CheckPlusSafeModel/service.cb";
<form name="form" id="form">
      <input type="hidden" id="m" name="m" value="service" />
      <input type="hidden" id="token_version_id" name="token_version_id" value="" />
      <input type="hidden" id="enc_data" name="enc_data" />
      <input type="hidden" id="integrity_value" name="integrity_value" />
</form>
```

- 인증 성공 시, 리턴값 처리 javascript

```javascript
var androidNice = {
    isPass: true,
    name: window.document.getElementById('username').textContent,
    phoneNumber: window.document.getElementById('mobileno').textContent,
    gender: window.document.getElementById('gender').textContent,
    birthdate: window.document.getElementById('birthdate').textContent,
    userDi: window.document.getElementById('dupinfo').textContent,
    userCi: window.document.getElementById('sConnInfo').textContent,
}

// Webview 사용 시,
// FlutterHandler.postMessage(JSON.stringify(androidNice));

// Flutter inappwebview 일 때,
window.addEventListener("flutterInAppWebViewPlatformReady", function(event) {
    if (window.flutter_inappwebview.callHandler) {
        window.flutter_inappwebview.callHandler('FlutterHandler', JSON.stringify(androidNice));
    }else{
        window.flutter_inappwebview._callHandler('FlutterHandler', JSON.stringify(androidNice));
    }
});
```
잘 되던 window.flutter_inappwebview.callHandler가 호출이 안될 경우가 있음. **callHandler 대신, _callHandler를 사용하면 됨**.

"**FlutterHandler**"로 NICE에서 받은 결과를 보내준다.  
![NICE 성공 리스폰스 json](/assets/img/2023/05/nice_success_resp.png)  

## 1. Flutter webview를 이용한 본인인증

![InAppWebViewGroupOptions](/assets/img/2023/05/webview_code_01.png)  
6 : WebView로 호출할 URL. 해당 url은 onload로 NICE 인증 표준창을 호출하게 됨.  
12 ~ 60: navigationDelegate에서 android/ios 기기에서 PASS 앱으로 인증처리  
61 ~ : javascriptChannel에서 NICE 인증 표준창과 통신 후, 결과 처리

### 1.1. webView를 사용하여 NICE 본인인증 처리 결과(**화면 크기가 맞지않음**)

![InAppWebViewGroupOptions](/assets/img/2023/05/webview_result_01.jpg)  
inappwebview를 사용하여 해결하고자 검토하게 됨.

## 2. Flutter inappWebview를 이용한 본인인증

Flutter 위젯 트리에 통합된 인라인 네이티브 WebView를 추가하기 위한 Flutter 위젯. 안드로이드에서는 안드로이드 API 20 이상(안드로이드 뷰 참조) 또는 안드로이드 전용 사용 하이브리드 컴포지션 옵션을 활성화한 경우 안드로이드 API 19 이상이 필요.  
참조 : [flutter_inappwebview Readme](https://pub.dev/packages/flutter_inappwebview/versions/5.0.3-nullsafety.1)

### 2.1. InAppWebViewGroupOptions 설정 설명

![InAppWebViewGroupOptions](/assets/img/2023/05/inappwebview_code_01.png)  
9 : userAgent의 값을 "hmdsAgent"로 설정한다. userAgent 설정값은 Nice 오픈창의 리턴값을 처리 시, flutterHandler를 호출하여 결과값을 처리할 수 있게 한다.  
14 : **initialScale : 100**은 android 기기에서 Nice 호출창의 size를 flutter webview에 화면 크기에 100% 보일 수 있도록 설정하게 한다(*1. Flutter webview를 이용한 본인인증에서 화면 크기가 이상하게 보이는 것 보완*). ios는 관련하여 default로 되어 있음.

### 2.2. InAppWebViewGroupOptions 설정 설명

![InAppWebView 생성](/assets/img/2023/05/inappwebview_code_02.png)  
7 : inAppWebView로 호출할 URL. 해당 url은 onload로 NICE 인증 표준창을 호출하게 됨.  
10 ~ 19 : webview가 생성될 시에, 호출한 url과 통신할 JavaScriptHandler를 추가한다. "FlutterHandler"를 web에서 호출하게 되면, callback으로 결과를 수신하여 처리한다.

### 2.3. InAppWebView를 사용하여 NICE 본인인증 처리 결과(**화면 크기가 정확하게 표시됨**)

![InAppWebViewGroupOptions](/assets/img/2023/05/inappwebview_result_01.jpg)

## 3. 결론

일반적인 webview를 사용하여서도 가능할 수 있었으나, 화면 size 오류가 발생함. webview를 upgrade하면 가능하다고 하였으나, 다른 widget(KakaoMap)이 webview 의존성을 가지고  있어서 upgrade 할 수 없었음.
flutter에서 webview를 사용할 시, 기본 제공하는 webview 보다는 InAppWebView를 사용하는 것이 좀 더 상세한 제어가 가능하였다.
---
layout: post
title: "NICE pay 연동 - 02"
date: 2023-07-17 14:40:00 +0900
categories: [Blogging]
tags: [flutter, nicepay, payment]
---

## Flutter InAppWebView, ERR_UNKNOWN_URL_SCHEME 에러

이전 블로그에서 nice pay 연동을 종료하고, '룰루랄라'할 때, 회사 동료가 에러가 난다고 문의가 왔다.
카카오 뱅크에서는 되는데, 국민은행 앱을 실행하면 에러 화면이 보인다는 제보.
테스트 결과, 아래와 같이 화면이 보인다.

![error02](/assets/img/2023/07/pg_error_02.jpg)
![error03](/assets/img/2023/07/pg_error_03.jpg)

### Error 발생 경위

InAppWebView shouldOverrideUrlLoading에서 URL이 http/https가 아닌 경우에 Url을 실행시키지 않기 위해서 return NavigationActionPolicy.CANCEL; 을 하지만, url이 무조건 실행되어 문제를 발생시킨다.  
페이지 로딩이 일어나고 Scheme오류 페이지(ERR_UNKNOWN_URL_SCHEME)가 나타나며 더이상 진행을 할수 없게 된다.  
다행히도 구글링해서 쉽게 해결할 수 있었고, 참조한 site는 하단 참조를 보기 바란다.

### Error 해결 방법

shouldOverrideUrlLoading에서 android, url.scheme = intent 이고, mainFrame이 아니면 controller.stopLoading을 호출한다.

```dart
            shouldOverrideUrlLoading: (controller, navigationAction) async {
              String requestUrl = navigationAction.request.url.toString();
              debugPrint('웹뷰 [onUpdateVisitedHistory]: $requestUrl');

              // android이고 mainFrame이 아니면 controller.stopLoading을 호출.
              // intent인 경우, 페이지 로딩이 일어나, Scheme오류 페이지(ERR_UNKNOWN_URL_SCHEME)가 나타난다.
              if (requestUrl.startsWith('intent') && Platform.isAndroid) {
                await controller.stopLoading();
              }

              // intent 실행
              if (!requestUrl.startsWith('http') &&
                  !requestUrl.startsWith('https')) {
                if (Platform.isAndroid) {
                  await getAppUrl(requestUrl);
                  return NavigationActionPolicy.CANCEL;
                } else if (Platform.isIOS) {
                  await launchUrl(
                    Uri.parse(requestUrl),
                  );
                  return NavigationActionPolicy.CANCEL;
                }
              }

              return NavigationActionPolicy.ALLOW;
            },
```

참조한 blog에서는 아래와 같이 코드를 추가하라고 하였으나, 하지 않아도 잘 되는 것 같다.  
추가하지 않으려 했으나, InAppWebView 문서 찾아보고, 예방 차원에서 코딩 추가해도 될 것 같아 반영하였다. 특정 scheme 작업처리를 위해.

onLoadResourceCustomScheme 를 추가해서 해당 이벤트 메서드에서 await controller.stopLoading();처리 한다. InAppWebViewGroupOptions에 resourceCustomSchemes: ['intent'],를 추가해서 동작할수 있도록한다.

```dart
onLoadResourceCustomScheme: (controller, url) async {
     await controller.stopLoading();
     return null;
},
```

## 참조

[Flutter Inappwebview, Intent ERR_UNKNOWN_URL_SCHEME 처리문제](https://blog.joongna.com/%EA%B0%9C%EB%B0%9C%EC%9D%BC%EC%A7%80-flutter-inappwebview-intent-err-unknown-url-scheme-%EC%B2%98%EB%A6%AC%EB%AC%B8%EC%A0%9C-9d887cc77f29)

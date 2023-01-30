---
layout: post
title: "Flutter 개발자 가이드 02"
date: 2023-01-30 13:15:00 +0900
categories: [Blogging]
tags: [flutter]
---

## Flutter Best Practices

> flutter를 사용하면서 추천하는 best practices 정리

1. Placeholder Widgets
   > Container 대신 SizedBox를 사용할 것

   null인 경우에 아래와 같이 자주 coding 함.

   ```flutter
   return _loaded ? Container() : YourWidget(); 
   ```

   SizedBox는 const 생성자이며 고정 크기 상자를 만듦. 너비 및 높이 매개변수는 상자의 크기가 해당 치수로 제한되지 않아야 함을 나타내기 위해 null일 수 있으며, 아래와 같이 placeholder로 SizedBox를 사용하는 것이 더 좋다고 함.

   ```flutter
   return _loaded ? SizedBox() : YourWidget(); 
   ```

2. Define Theme

   앱의 테마를 정의하고 향후 업데이트에서 테마를 변경해야 하는 골칫거리를 피하기 위해 첫 번째 우선순위로 개발할 것. 디자이너에게 색상, 글꼴 크기 및 무게와 같은 모든 테마 관련 데이터를 공유하도록 요청.

   ```flutter
   MaterialApp(
        title: appName,
        theme: ThemeData(
            // Define the default brightness and colors.
            brightness: Brightness.dark,
            
            // You can add the color from the separate 
            // class here as well to maintain it well.
            primaryColor: Colors.lightBlue[800],
            // Define the default font family.
            fontFamily: 'Georgia',
            // Define the default `TextTheme`. 
            // Use this to specify the default
            // text styling for headlines, titles, bodies of text, and more.
            textTheme: const TextTheme(
            headline1: TextStyle(
                fontSize: 72.0, 
                fontWeight: FontWeight.bold,
            ),
            headline6: TextStyle(
                fontSize: 36.0, 
                fontStyle: FontStyle.italic
            ),
            bodyText2: TextStyle(
                fontSize: 14.0, 
                fontFamily: 'Hind',
            ),
            ),
        ),
        home: const MyHomePage(
            title: appName,
        ),
   );
   ```

3. Using ‘if’ instead of Ternary Operator

   > 3항 연산자 대신, 'if'를 사용할 것.

   ```flutter
   Row(
    children: [
    Text("Hello Flutter"),
      Platform.isIOS ? Text("iPhone") : SizeBox(),
    ]
   );
   ```

   위의 코드 대신에 아래와 같이 코딩

   ```flutter
   Row(
     children: [
      Text("Hello Flutter"),
      if (Platform.isIOS) Text("iPhone"),
     ]
   );

   // 배열인 경우,
   Row(
    children: [
      Text("Hello Flutter"),
      if (Platform.isIOS) ...[
        Text("iPhone")
        Text("MacBook")
      ],
    ]
   );
   ```

4. Use const widgets whenever possible
5. 

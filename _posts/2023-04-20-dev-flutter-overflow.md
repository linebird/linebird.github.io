---
layout: post
title: "Flutter UI overflows"
date: 2023-04-20 15:12:00 +0900
categories: [Blogging]
tags: [flutter, overflow]
---

## overflow

1. widget이 화면을 넘어갈 때
2. Text widget 글자수가 길어졌을 경우
3. overflowed by * pixels

### Container overflow

> 컨테이너 안에 자식을 추가하면 컨테이너가 자식을 감싸고 자식 위젯에 따라 컨테이너 크기를 조정하게 됨.

#### container 표시 기분

컨테이너 위젯에 *width, height 속성을 100*으로 함.
컨테이터 위젯에 Text 위젯을 넣고, 긴 문장의 text를 입력. "동해물과 백두산이 마르고 달도록 하느님이 보우하사 우리 나라 만세 무궁화 삼천리 화려강산 대한사람 대한으로 길이 보전하세"

```flutter
        body: SafeArea(
          child: Container(
            width: 100,
            height: 100,
            decoration: BoxDecoration(
              color: FlutterFlowTheme.of(context).secondaryBackground,
            ),
            child: Padding(
              padding: EdgeInsetsDirectional.fromSTEB(8, 8, 8, 8),
              child: Text(
                '동해물과 백두산이 마르고 달도록 하느님이 보우하사 우리 나라 만세 무궁화 삼천리 화려강산 대한사람 대한으로 길이 보전하세',
                style: FlutterFlowTheme.of(context).bodyMedium,
              ),
            ),
          ),
        ),
      )
```

[overflow 결과]  
애국가 가사가 잘리어서 표시됨. 아래 그림처럼 일부 가사만 보임.  
![container overflow](/assets/img/202304/container_overflow01.png)  
사용 사례에 따라 컨테이너를 그 안에 있는 자식 위젯(이 경우 텍스트 위젯)에 맞게 조정할 수 있는 다양한 방법이 있을 수 있음.

#### Container 표시 Case1

Container widget의 height, width를 제거한 경우...

```flutter
        body: SafeArea(
          child: Container(
            decoration: BoxDecoration(
              color: FlutterFlowTheme.of(context).secondaryBackground,
            ),
            child: Padding(
              padding: EdgeInsetsDirectional.fromSTEB(8, 8, 8, 8),
              child: Text(
                '동해물과 백두산이 마르고 달도록 하느님이 보우하사 우리 나라 만세 무궁화 삼천리 화려강산 대한사람 대한으로 길이 보전하세',
                style: FlutterFlowTheme.of(context).bodyMedium,
              ),
            ),
          ),
        ),
      ),
```

[height, width를 제거 결과]

```flutter
```

![container overflow2](/assets/img/202304/container_overflow02.png)

#### Container 표시 Case2

Container widget이 자식을 가로로만 래핑하도록 하려면(height 고정되어 있지만 width는 조정) height의 고정 값을 지정하고 width 값을 제거

```flutter
        body: SafeArea(
          child: Container(
            height: 100,
            decoration: BoxDecoration(
              color: FlutterFlowTheme.of(context).secondaryBackground,
            ),
            child: Padding(
              padding: EdgeInsetsDirectional.fromSTEB(8, 8, 8, 8),
              child: Text(
                '동해물과 백두산이 마르고 달도록 하느님이 보우하사 우리 나라 만세 무궁화 삼천리 화려강산 대한사람 대한으로 길이 보전하세',
                style: FlutterFlowTheme.of(context).bodyMedium,
              ),
            ),
          ),
        ),
      )
```

[height 고정, width를 제거 결과]  
![container overflow2](/assets/img/202304/container_overflow03.png)

#### Container 표시 Case3

Container widget의 자식을 세로로만 래핑하도록 하려면(width는 고정되어 있지만 height 조정) width 고정 값을 지정하고 height 값을 제거

```flutter
        body: SafeArea(
          child: Container(
            width: 100,
            decoration: BoxDecoration(
              color: FlutterFlowTheme.of(context).secondaryBackground,
            ),
            child: Padding(
              padding: EdgeInsetsDirectional.fromSTEB(8, 8, 8, 8),
              child: Text(
                '동해물과 백두산이 마르고 달도록 하느님이 보우하사 우리 나라 만세 무궁화 삼천리 화려강산 대한사람 대한으로 길이 보전하세',
                style: FlutterFlowTheme.of(context).bodyMedium,
              ),
            ),
          ),
        ),
      )
```

[width 고정, height를 제거 결과]  
![container overflow2](/assets/img/202304/container_overflow04.png)

### Text Overflow

> 일반적으로 텍스트 위젯이 행 안에 단독으로 또는 다른 위젯과 함께 배치되면 텍스트 위젯의 오버플로가 발생. 이는 텍스트가 가로로 확장되기를 원하지만 행의 경계 내에서 자신을 제한하는 방법을 모르기 때문에 발생.

```flutter
Column(
  mainAxisSize: MainAxisSize.max,
  crossAxisAlignment: CrossAxisAlignment.stretch,
  children: [
    Row(
      mainAxisSize: MainAxisSize.max,
      children: [
        Image.network(
          'https://picsum.photos/seed/522/600',
          width: 100,
          height: 100,
          fit: BoxFit.cover,
        ),
        Text(
          '일반적으로 텍스트 위젯이 행 안에 단독으로 또는 다른 위젯과 함께 배치되면 텍스트 위젯의 오버플로가 발생합니다. 이는 텍스트가 가로로 확장되기를 원하지만 행의 경계 내에서 자신을 제한하는 방법을 모르기 때문에 발생합니다',
          style: FlutterFlowTheme.of(context).bodyMedium,
        ),
      ],
    ),
  ],
)
```

[오버플로우 결과]  
![text overflow](/assets/img/202304/text_overflow01.png)

#### 해결방법 1

**Expanded**를 사용하여 Text widget을 감싼다.

```flutter
// Expended wrap
Expanded(
  child: Text(
    '일반적으로 텍스트 위젯이 행 안에 단독으로 또는 다른 위젯과 함께 배치되면 텍스트 위젯의 오버플로가 발생합니다. 이는 텍스트가 가로로 확장되기를 원하지만 행의 경계 내에서 자신을 제한하는 방법을 모르기 때문에 발생합니다',
    style: FlutterFlowTheme.of(context).bodyMedium,
  ),
)
```

[결과]  
![text overflow 해결](/assets/img/202304/text_overflow02.png)

#### 해결방법 2

경우에 따라 텍스트를 지정된 줄 수로 제한하고 싶을 수도 있다. 예를 들어 다양한 길이의 설명이 있는 책 목록을 표시하고 싶지만 UI를 일관되고 깔끔하게 유지하기 위해 설명을 두 줄로만 제한하고 싶다고 가정. **Max Lines** 속성을 사용하면 됩니다.

## Column and Row Overflow

> Column 위젯의 크기는 그 안에 있는 자식에 의해 결정. 자식이 기기의 화면 높이보다 세로 공간(즉, 열의 주축을 따라)을 더 많이 차지하는 경향이 있는 경우 오버플로 문제가 발생할 수 있음.

### Column overflow example

```flutter
        ...
        body: SafeArea(
          child: Column(
            mainAxisSize: MainAxisSize.max,
            children: [
              Container(
                width: 400.9,
                height: 217.6,
                decoration: BoxDecoration(
                  gradient: LinearGradient(
                    colors: [
                      FlutterFlowTheme.of(context).primary,
                      FlutterFlowTheme.of(context).secondary
                    ],
                    stops: [0, 1],
                    begin: AlignmentDirectional(0, -1),
                    end: AlignmentDirectional(0, 1),
                  ),
                ),
                child: Text(
                  '1번 컨테이너',
                  style: FlutterFlowTheme.of(context).bodyMedium,
                ),
              ),
              Container(
                width: 400.9,
                height: 217.6,
                decoration: BoxDecoration(
                  color: FlutterFlowTheme.of(context).success,
                  shape: BoxShape.rectangle,
                  border: Border.all(
                    color: Color(0xFFCB1515),
                  ),
                ),
                child: Text(
                  '2번 컨테이너',
                  style: FlutterFlowTheme.of(context).bodyMedium,
                ),
              ),
              Container(
                width: 400.9,
                height: 217.6,
                decoration: BoxDecoration(
                  color: FlutterFlowTheme.of(context).secondary,
                ),
                child: Text(
                  '3번 컨테이너',
                  style: FlutterFlowTheme.of(context).bodyMedium,
                ),
              ),
              Container(
                width: 400.9,
                height: 217.6,
                decoration: BoxDecoration(
                  color: FlutterFlowTheme.of(context).primaryBackground,
                ),
                child: Text(
                  '4번 컨테이너',
                  style: FlutterFlowTheme.of(context).bodyMedium,
                ),
              ),
            ],
          ),
        ),
```

[결과]  
![column overflow](/assets/img/202304/column_overflow.png)

#### 해결방법

**SingleChildScrollView**를 사용하여 Column widget을 wrapping

```flutter
        ...
    body: SafeArea(
        child: SingleChildScrollView(
            child: Column(
            mainAxisSize: MainAxisSize.max,
            children: [
                Container(
                width: 400.9,
                height: 217.6,
                decoration: BoxDecoration(
                    gradient: LinearGradient(
                    colors: [
                        FlutterFlowTheme.of(context).primary,
                        FlutterFlowTheme.of(context).secondary
                    ],
                    stops: [0, 1],
                    begin: AlignmentDirectional(0, -1),
                    end: AlignmentDirectional(0, 1),
                    ),
                ),
                child: Text(
                    '1번 컨테이너',
                    style: FlutterFlowTheme.of(context).bodyMedium,
                ),
                ),
                Container(
```

### Row overflow example

```flutter
        ...
        body: SafeArea(
          child: Row(
            mainAxisSize: MainAxisSize.max,
            children: [
              Container(
                width: 295.4,
                height: 100,
                decoration: BoxDecoration(
                  color: FlutterFlowTheme.of(context).primary,
                ),
                child: Text(
                  '컨테이너 1',
                  style: FlutterFlowTheme.of(context).bodyMedium,
                ),
              ),
              Container(
                width: 295.4,
                height: 100,
                decoration: BoxDecoration(
                  color: FlutterFlowTheme.of(context).secondary,
                ),
                child: Text(
                  '컨테이너 2',
                  style: FlutterFlowTheme.of(context).bodyMedium,
                ),
              ),
            ],
          ),
        ),
      ),
```

[결과]  
![row overflow](/assets/img/202304/row_overflow.png)

#### 해결방법

Column과 동일하게 **SingleChildScrollView**로 Row widget wrapping

```flutter
        body: SafeArea(
          child: SingleChildScrollView(
            scrollDirection: Axis.horizontal,
            child: Row(
              mainAxisSize: MainAxisSize.max,
              children: [
                Container(
                  width: 295.4,
                  height: 100,
                  decoration: BoxDecoration(
                    color: FlutterFlowTheme.of(context).primary,
                  ),
                  child: Text(
                    '컨테이너 1',
                    style: FlutterFlowTheme.of(context).bodyMedium,
                  ),
                ),
                Container(
                  width: 295.4,
                  height: 100,
                  decoration: BoxDecoration(
                    color: FlutterFlowTheme.of(context).secondary,
                  ),
                  child: Text(
                    '컨테이너 2',
                    style: FlutterFlowTheme.of(context).bodyMedium,
                  ),
                ),
              ],
            ),
          ),
        ),
      ),
```

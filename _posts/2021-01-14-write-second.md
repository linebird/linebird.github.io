---
title: "Jenkins에 Sonarqube 연동하기"
date: 2021-01-14 15:00:00 +0900
categories: [Blogging]
tags: [jenkins, sonarqube]
---

# Jenkins에 Sonarqube 연동하기

Jenkins와 Sonarqube 연동을 하려면 일단 Jenkins와 Sonarqube가 설치가 되어 있어야 한다. 각각의 설치 과정은 생략하도록 하겠다. 이 두개의 어플리케이션이 설치가 되어 있다는 가정하에 설명을 하도록 하겠다.

## Sonarqube 작업

> Jenkins와 Sonarqube 연동은 token으로 한다. Jenkins에서 Sonarqube를 호출하여 작업을 하므로 token의 발행 주체는 Sonarqube이다. 따라서 Sonarqube에서 token을 생성을 해주도록 한다.

![Desktop View](https://blog.kakaocdn.net/dn/sgXQH/btqEcHicluN/k01X910UX7fGpEY9Ga6ZR1/img.png) </br>
token 생성은 **Administration > Security > User > Tokens**
![Desktop View](https://blog.kakaocdn.net/dn/DA5d7/btqEeqsMaCz/REnesFcK7AJ3C5bY3xiBSk/img.png)
![Desktop View](https://blog.kakaocdn.net/dn/Ymki3/btqEepgkNvc/zsIxDjXEWg6QFVV3HIJ2Vk/img.png) </br>
token 의 이름은 아무거나 집어 넣고 Generate 를 눌러 생성을 해준다. test 라는 token name을 넣고 생성을 했고 녹색의 문자열로 token이 생성되었다. 이 값을 잘 가지고 있자.

## Jenkins 작업
---
layout: post
title: "프로젝트 개발 커뮤니케이션 툴 - 오픈소스"
date: 2021-03-19 10:13:00 +0900
categories: [Blogging]
tags: [opensource, trello, slack, chat]
---

## [ChatWoot - 인터컴/ZenDesk 대체용 오픈소스 라이브챗](https://github.com/chatwoot/chatwoot)

> 2017년에 서비스 시작했다가 실패해서 2019년에 오픈소스로 전환. 현재는 100명이 넘는 컨트리뷰터들이 같이 개발하고, 1000개가 넘는 회사들이 사용

- 옴니채널 고객 지원 소프트웨어, 서비스가 망해서 오픈소스로 공개
- 웹사이트 채팅/페이스북/트위터/Whatsapp/SMS(Twilio)/API/Email 등의 대화를 하나로 묶어서 지원
- 하나의 대시보드로 멀티브랜드 인박스 관리
- 각 대화에 비공개 노트 적어서 팀이 공유 가능
- 자주 묻는 질문에 자동 답변 제공(Canned Response)
- 대화 담당자 자동 지정(담당자의 가능시간 및 부하에 따라 분기)
- 대화 연속성 지원 : 채팅중에 이메일을 남겼을 때, 차후에 이메일로 대화시 두 대화가 연결됨
- 슬랙에 연동 지원

## [Focalboard - 오픈소스 Trello/Asana 대체제](https://www.focalboard.com/)

> 본인들은 Notion Alternative라고 홍보는 하던데, 아주 일부인 Kanban 기능 대체하는 수준. 슬랙 대체제로 유명한 Mattermost 팀에서 만드는 오픈소스.

- 프로젝트 및 작업 관리 도구
- 개인용 데스크탑 버전(윈/맥/리눅스)과 팀용 서버 버전으로 제공
- Board 단위로 관리
ㅤ→ 기본 제공 보드 템플릿 : 미팅 노트, Personal Goal/Task, Project Task, Roadmap 등
ㅤ→ 템플릿 추가 및 편집 지원

## [Mattermos - 오픈소스 slack 대체제](https://github.com/mattermost/mattermost-server)

- Go와 React로 개발됨. DB는 MySQL 또는 PostgreSQL
- Native Mobile and Desktop Apps 제공
---
layout: post
title: "Flutter 개발자 가이드 03 - 프로젝트 구조 설명"
date: 2023-01-30 21:08:00 +0900
categories: [Blogging]
tags: [flutter]
---

## Project structure

> GetX 프로젝트 구조를 참조하여, 하루약국 flutter 프로젝트 구조 정의

### Config

config 폴더에는 다음 폴더가 포함됨:

- **routes :** 애플리케이션 화면 내비게이션 코드를 기반으로 하는 모든 파일이 포함
- **themes :** 응용 프로그램이 라이트 테마와 다크 테마를 지원하고 이러한 테마가 사용자 정의 테마인 경우 light_theme.dart, dark_theme.dart 파일 두 개를 생성해야 함. 여기에서 각 위젯 유형에 필요한 모든 색상 추가.
- **translation :** 다국어 모듈 정의.

### Constants

애플리케이션 전체에서 사용되는 상수 정의 :

- **api_path.dart :**  REST API 서비스를 사용할 때 모든 API 엔드포인트를 별도의 파일 api_path.dart에 저장
- **assest_path.dart :** pubspec.yaml에서 assets 경로를 기술하지만, 애플리케이션에서 해당 asset을 사용하려면 모든 위젯에 상대 경로를 제공해야 가능. 모든 assets 상대 경로를 하나의 파일에 추가하면 모든 경로를 쉽게 가져오고 나중에 필요한 경우 경로를 업데이트할 수 있음.

### Features

아래의 폴더를 포함하는 기능 정의 모듈 폴더:

![Untitled](/assets/img/flutter/arom_structure_features.png)

- **Models :** 대시보드 화면에 표시해야 하는 데이터 모델 정의 폴더.
- **Bindings :** 인스턴스 컨트롤러에 대한 파일 폴더
- **Controllers:** 상태 관리 정의 폴더. [Getx 상태관리](https://pub.dev/packages/get#state-management)
- **Views :** Components 폴더는 특정 위젯 구성 요소가 포함하는 폴더 , Screen 폴더는 사용자에게 표시되는 모든 화면 UI 위젯으로 구성.

### Shared Components

응용 프로그램 전체에서 사용할 수 있는 자체 사용자 지정 위젯 공유 폴더.

### Utils

Utils 폴더에는 애플리케이션 전체에서 사용되는 helpers, services, UI utils, mixins 폴더로 구성.

- **Helpers :** 동일한 작업에 대해 코드를 여러 번 작성해야 합니다. 이러한 종류의 코드는 중복성을 줄이는 helper 코드 정의 폴더
- **Mixins :** 다른 클래스의 부모 클래스가 아니어도 다른 클래스에서 사용할 수 있는 메서드를 포함하는 클래스 관리 폴더. 믹스인은 클래스를 확장하지 않고 메서드(또는 변수)를 빌릴 수 있는 일반 클래스를 정의하는 폴더.
- **Services :** 서비스 파일 생성 폴더
  1. **local_storage_service.dart :** shared_preferences를 사용하여 로컬 저장소에서 데이터를 저장하고 가져오는 데 필요한 모든 코드를 작성. [shared_preferences](https://pub.dev/packages/shared_preferences).
  2. **rest_api_service.dart :** Rest API 호출.  [http](https://pub.dev/packages/http) 또는 [dio](https://pub.dev/packages/dio) 패키지 사용.

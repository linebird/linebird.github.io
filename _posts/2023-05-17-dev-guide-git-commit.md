---
layout: post
title: "Git Commit Message Guide"
date: 2023-05-17 15:02:00 +0900
categories: [Blogging]
tags: [git, guide, documentation]
---

## 일반적인 Git 커밋 메시지 작성

### 1) 기본 포맷

git 커밋 메시지의 구조는 아래와 같이 사용:

```git
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

&#60;**type**&#62;은 커밋 메시지의 유형 중 하나를 나타낸다.
> type은 아래와 같이 정의

| Type     | Title                    | Description |
| -------- | ------------------------ | -------------- |
| **feat**     | Features                 | 새로운 기능 추가 또는 기능 업데이트 |
| **fix**      | Bug Fixes                | 버그 또는 에러 수정 |
| **docs**     | Documentation            | 문서 변경 |
| **style**    | Styles                   | 코드의 의미에 영향을 주지 않는 변경 사항(공백, 서식, 세미콜론 누락 등) |
| **comment**  | Comments                 | 주석 수정 및 삭제 |
| **refactor** | Code Refactoring         | 코드 리팩토링(똑같은 기능인데 코드만 개선) |
| perf     | Performance Improvements | 성능을 개선하는 코드 변경 |
| test     | Tests                    | 테스트 추가 또는 기존 테스트 수정 |
| build    | Builds                   | 빌드 시스템 또는 외부 종속성에 영향을 미치는 변경 사항 (예시 범위: gulp, broccoli, npm, maven, gradle) |
| ci       | Continuous Integrations  | CI 구성 파일 및 스크립트 변경 사항 |
| **chore**    | Chores                   | 소스나 테스트 파일의 변경이 아닌 기타 수정 (예: 패키지 추가, 환경변수 설정)|
| revert   | Reverts                  | 이전 커밋으로 되돌리기 |
| wip      | Work In Progress         | 나중에 다시 작성되어져 변경되어질 임시 커밋 |
| **initial**  | Initial                  | 최초 커밋 |

&#60;**descrption**&#62;은 변경 작업의 제목이나 간단한 요약을 작성한다. 50자 이내로 작성하며, 마침표를 사용하지 않는다.

&#60;**body**&#62;는 선택 사항으로 작업 내용이 복잡하거나 상세한 내용을 남겨야 하는 경우에만 작성한다. 필요한 경우 여러줄로도 작성 가능하며, 보통 한 줄당 72자 이내로 작성한다.

&#60;**footer**&#62; 역시 선택 사항이며, 코드 작업과 관련된 이슈 번호 또는 참조 링크등을 추가한다. 특정 작업이나 이슈를 해결한 경우에는 일반적으로 "Closes #작업번호 또는 #이슈번호" 같은 형태로 기재한다.
> footer type

| Type            | Title               | Description                                                                    |
| --------------- | ------------------- | ------------------------------------------------------------------------------ |
| **Closes**          | Referencing issues  | 이슈나 작업등을 해결한 경우 작성 (예: Closes #​133) |
| Refs            | Referencing commits | 다른 커밋과 연관된 코드 변경 (예: Refs: 676104e, a215868) |
| BREAKING CHANGE | Breaking changes    | 코드 변경으로 인해 다른 기능이 작동하지 않는 경우(예: 라이브러리 버전 변경으로 제공되는 함수가 변경됨) |

### 2) Git 커밋 메시지 작성 예시

웹 개발 프로젝트에서 사용자 프로필 페이지를 추가한 작업을 한 경우 아래와 같은 방식으로 Git 커밋 메시지를 작성할 수 있다. 일반적으로 제목줄(<type>: <description>)과 <body>, <footer> 사이에는 한 줄씩 공백을 둔다.

```git
feat(release): 사용자 프로필 페이지 추가

- 사용자 프로필 페이지 및 라우팅 구현
- 프로필 정보를 보여주는 프로필 카드 컴포넌트 구현
- 프로필 수정 기능 구현

Closes #101
```

## 건기식 프로젝트 Git commit message 가이드

건기식 프로젝트에서는 최소 아래와 같이 메시지를 작성할 수 있도록 한다.

### 1) 작성 방법

커밋 유형과 description은 반드시 작성한다.
scope는 페이지 경로 또는 컴포넌트명, 패키지명 등을 선택해서 넣도록 한다.
footer에는 레드마인의 일감 번호가 있는 경우, 완료인 경우에 "Closes **#레드마인일감번호**"를 작성한다.

```git
<mandatory type>[optional scope]: <mandatory description>

[optional body]

[optional footer(s)]
```

### 2) 예제

```git
feat: 웹뷰 본인인증 구현

- 본인인증 controller 구현
- 본인인증 페이지 구현

Closes #1200
```

```git
fix: 웹뷰 본인인증 화면 사이즈를 폰 크기에 맞게 수정

reviewed-by: 김유신, 홍길동
Closes #1234
```

```git
feat(api)!: 영양제를 구매하면 구매자에게 이메일을 보내도록 구현
```

### 참고

- [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/)
- [Git 커밋 메시지는 왜 중요할까?](https://insight.infograb.net/blog/2023/04/21/why-commit-convention-is-important/)
- [Git 커밋 메시지 컨벤션은 왜 중요할까?](https://yozm.wishket.com/magazine/detail/1974/)
- [Writing Better Commits](https://itnext.io/writing-better-commits-6a1691c12772)

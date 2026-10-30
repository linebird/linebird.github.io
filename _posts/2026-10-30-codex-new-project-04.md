---
layout: post
title: Java/Spring 멀티 프로젝트를 위한 공통 Harness Template Repository 설계
date: "2026-10-30 10:43:44 +09:00" 
categories: [ai]
tags: [ai, codex, harness]
---

여러 Java/Spring 프로젝트를 운영한다면, 공통 Harness Repository를 하나 만들고 각 프로젝트에서 필요한 표준만 확장하는 구조를 구성한다.

핵심은 개발 표준을 애플리케이션 소스와 분리해 중앙에서 관리하면서도, 각 프로젝트의 기존 아키텍처와 코딩 컨벤션은 유지하는 것이다.

예를 들어 다음과 같은 프로젝트가 있다고 가정하면,

- Project A: Java 8 + Spring Framework + MyBatis
- Project B: Java 17 + Spring Boot + MyBatis
- Project C: Java 21 + Spring Boot + MyBatis

세 프로젝트는 기술 버전이나 업무 도메인은 다르지만, 코드 분석, 구현 계획, 테스트, 코드 리뷰 등 AI 개발 절차는 공통으로 사용할 수 있다.

## 1. 전체 구성 아키텍처

```mermaid

flowchart TB
    subgraph COMMON["Common Harness Repository"]
        POLICY["공통 개발 정책"]
        AGENTS["AGENTS.md Template"]
        SKILLS["공통 Codex Skills"]
        VERIFY["검증 스크립트"]
        TEMPLATES["TASK / PLAN / REVIEW Templates"]
    end

    COMMON --> VERSION["Versioned Release<br/>v1.0 / v1.1 / v2.0"]

    VERSION --> A["Project A<br/>Java 8 + Spring Framework"]
    VERSION --> B["Project B<br/>Java 17 + Spring Boot"]
    VERSION --> C["Project C<br/>Java 21 + Spring Boot"]

    subgraph LOCAL["Project-specific Extensions"]
        L1["프로젝트별 AGENTS.md"]
        L2["Architecture / DB Schema"]
        L3["Coding Conventions"]
        L4["Project-specific Skills"]
    end

    A --> LOCAL
    B --> LOCAL
    C --> LOCAL

    A --> CI["CI Quality Gate"]
    B --> CI
    C --> CI
    CI --> REVIEW["Merge Request / Review"]

```

이 구조는 크게 3개 계층으로 나뉜다.

| 계층                  | 책임                                    | 관리 주체                 |
| --------------------- | --------------------------------------- | ------------------------- |
| Organization Harness  | 공통 개발 정책, Skills, 검증 인터페이스 | 공통 플랫폼/아키텍처 담당 |
| Project Harness       | 프로젝트별 규칙, DB 구조, 빌드 방식     | 프로젝트 개발팀           |
| Developer Environment | Codex, IDE, 개인 설정                   | 개별 개발자               |

**중요한 점은 공통 Harness가 각 프로젝트의 기술 스택이나 코딩 스타일을 강제로 통일하지 않는 것**이다.

공통 Harness는 개발 프로세스를 통일하고, 프로젝트 Harness는 구현 규칙을 정의한다.

## 2. 공통 Harness Repository 디렉터리 구조

공통 저장소를 `java-spring-harness`라는 이름으로 생성한다.

```text
java-spring-harness/
│
├── README.md
├── VERSION
├── CHANGELOG.md
│
├── standards/
│   ├── development-policy.md
│   ├── security-policy.md
│   ├── code-review-policy.md
│   ├── testing-policy.md
│   └── database-policy.md
│
├── agents/
│   └── AGENTS.template.md
│
├── skills/
│   ├── task-planning/
│   │   └── SKILL.md
│   ├── spring-feature/
│   │   └── SKILL.md
│   ├── mybatis-development/
│   │   └── SKILL.md
│   ├── code-review/
│   │   └── SKILL.md
│   └── test-generation/
│       └── SKILL.md
│
├── profiles/
│   ├── spring-framework/
│   ├── spring-boot/
│   ├── gradle/
│   ├── maven/
│   └── mybatis/
│
├── templates/
│   ├── TASK.template.md
│   ├── PLAN.template.md
│   └── REVIEW.template.md
│
├── scripts/
│   ├── verify.sh
│   ├── check-java.sh
│   ├── check-mybatis.sh
│   └── check-git-diff.sh
│
└── tools/
    ├── install.py
    ├── update.py
    └── validate.py
```

### 각 구성 요소의 역할

| 디렉터리     | 역할                             |
| ------------ | -------------------------------- |
| `standards/` | 조직 전체가 공유하는 개발 정책   |
| `agents/`    | 프로젝트 AGENTS.md 생성용 템플릿 |
| `skills/`    | 공통 AI 개발 업무 절차           |
| `profiles/`  | 프레임워크 및 빌드 도구별 설정   |
| `templates/` | TASK, PLAN, REVIEW 문서 템플릿   |
| `scripts/`   | 공통 자동 검증 로직              |
| `tools/`     | Harness 설치·업데이트·검증 도구  |

여기서 `profiles/`가 중요.

예를 들어 Spring Framework 5와 Spring Boot 3은 설정 방식, Java 요구사항, 테스트 구성이 다를 수 있다. 모든 프로젝트에 하나의 규칙을 강제하지 않고 Profile을 선택적으로 적용해야 한다.

## 3. 개별 프로젝트의 Harness 확장 구조

Project A는 다음과 같이 구성한다.

```text
project-a/
│
├── AGENTS.md
│
├── .harness/
│   ├── manifest.yaml
│   └── common/
│       ├── standards/
│       ├── profiles/
│       └── scripts/
│
├── .agents/
│   └── skills/
│       ├── task-planning/
│       ├── spring-feature/
│       ├── mybatis-development/
│       └── project-specific-review/
│
├── docs/
│   ├── architecture.md
│   ├── coding-conventions.md
│   ├── database-schema.md
│   └── business-rules.md
│
├── harness/
│   ├── verify.sh
│   ├── tasks/
│   └── plans/
│
├── src/
├── build.gradle
└── gradlew
```

여기서 `.harness/common/`은 공통 Repository의 특정 버전에서 가져온 파일이고, `docs/`와 `AGENTS.md`는 해당 프로젝트가 직접 소유하게 된다.

공통 Skills도 프로젝트 안에 설치하지만 버전을 관리하여 업데이트할 수 있도록 한다.

`AGENTS.md`는 Codex가 자동으로 읽는 지침 파일이며, Skills는 반복 업무를 정의하는 별도 구성이다. 공식 Codex 운영 사례에서도 Repository별 `AGENTS.md`와 `.agents/skills/`를 함께 사용하는 방식을 소개하고 있다.

참조: [Codex Prompting Guide](https://developers.openai.com/cookbook/examples/gpt-5/codex_prompting_guide?,trk=article-ssr-frontend-pulse_little-text-block&utm_source=chatgpt.com), [Using skills to accelerate OSS maintenance](https://developers.openai.com/blog/skills-agents-sdk?trk=article-ssr-frontend-pulse_little-text-block&utm_source=chatgpt.com)

## 4. 공통 규칙과 프로젝트 규칙의 관계

다음과 같이 분리한다.

```mermaid

flowchart TD
    G["Organization Standards<br/>공통 보안 / 테스트 / 개발 절차"]
    P["Technology Profile<br/>Spring Framework / Boot / MyBatis"]
    L["Project Rules<br/>코딩 컨벤션 / DB / 업무 규칙"]
    T["Task Requirements<br/>구현 요구사항 / 완료 기준"]

    G --> P --> L --> T

    T --> CODEX["Codex Agent"]
    CODEX --> RESULT["Project-specific Implementation"]

```

이 그림은 규칙을 적용하는 논리적 구조로, 실제 Codex가 별도 Harness 저장소의 문서를 자동으로 탐색한다는 의미는 아니다.

공통 규칙을 Codex가 사용할 수 있도록 프로젝트 안에 설치하거나, 프로젝트 `AGENTS.md`에서 해당 로컬 파일을 참조하도록 구성해야 한다.

또한 충돌 해결 정책을 명확하게 정해야 한다.

| 구분            | 정책                                        |
| --------------- | ------------------------------------------- |
| 보안·운영 제한  | 공통 최소 기준을 프로젝트에서 완화하지 않음 |
| 기술별 규칙     | 선택한 Profile 적용                         |
| 아키텍처·네이밍 | 프로젝트 기존 규칙 우선                     |
| 작업별 요구사항 | 프로젝트 정책과 허용 범위 내에서 적용       |

예를 들어 공통 Harness가 REST API 신규 개발을 권장하더라도, 기존 Project A가 특정 응답 Wrapper를 사용한다면 신규 API 역시 해당 Wrapper를 사용하도록 해야한다.

## 5. 공통 Harness 배포 및 버전 관리 방식

실제 운영에서는 공통 Repository의 내용을 각 프로젝트에 어떻게 공급할지가 중요.

대표적으로 다음과 같은 3가지 방식이 있다.

| 방식          | 장점                                   | 단점                             |
| ------------- | -------------------------------------- | -------------------------------- |
| Git Submodule | 공통 소스 버전 고정, 중앙 관리         | 경로 및 업데이트 관리가 번거로움 |
| Git Subtree   | 프로젝트 안에 파일이 포함됨            | 공통 변경사항 병합이 복잡함      |
| Template Sync | 선택적 파일 설치, 업데이트 자동화 가능 | 별도의 동기화 도구 필요          |

추천은 Template Sync 방식으로  
개별 프로젝트에서 공통 Harness의 특정 릴리스를 선택하고, 설치 도구가 필요한 Skills와 스크립트를 프로젝트에 복사하는 구조이다.

### 5.1 프로젝트별 Harness Manifest

`.harness/manifest.yaml`

```yaml

harness:
  repository: "gitlab.example.com/platform/java-spring-harness"
  version: "v1.2.0"

project:
  name: "project-a"
  language: "java"
  java_version: "8"

profiles:
  - spring-framework
  - gradle
  - mybatis

skills:
  - task-planning
  - spring-feature
  - mybatis-development
  - code-review

verification:
  build: "./gradlew compileJava"
  test: "./gradlew test"
  diff: "git diff --check"

policy:
  allow_database_schema_changes: false
  require_plan_approval: true
  require_code_review: true

```

이 YAML은 Codex가 직접 해석하는 설정 파일이 아니라, 조직에서 개발할 Harness 설치·동기화 도구의 명세이다.

설치 도구가 수행해야 할 작업은 다음과 같다.

1. 지정된 Harness 버전 다운로드
2. 기술 Profile 확인 및 호환성 검사
3. 선택된 공통 Skills 설치
4. 검증 스크립트 설치
5. 기존 프로젝트 파일과 충돌 검사
6. 설치된 파일의 버전 및 체크섬 기록

### 5.2 설치 및 업데이트 명령

다음은 조직에서 구현할 CLI의 인터페이스 예시.

```shell
# 공통 Harness 최초 설치
harness install --version v1.2.0

# 설치 내용 검증
harness validate

# 새로운 버전의 변경사항 미리보기
harness update --version v1.3.0 --dry-run

# 승인된 버전으로 업데이트
harness update --version v1.3.0
```

`harness` 명령은 Codex의 기본 내장 명령이 아님. 공통 Repository의 `tools/`에 직접 구현하거나 사내 CLI로 배포할 수 있다.

업데이트 시에는 프로젝트가 직접 작성한 `AGENTS.md`, 업무 문서, 커스텀 Skills를 덮어쓰지 않는 것이 원칙.

## 6. 실제 AGENTS.md 구성 방법

가장 중요한 구현 포인트.

공통 규칙 파일이 프로젝트에 설치되어 있더라도 Codex가 그 내용을 항상 자동으로 읽는 것은 아니다.

따라서 프로젝트 루트의 `AGENTS.md`를 진입점으로 사용한다.

### Project A의 AGENTS.md

```md

# Project A - Codex Instructions

## Project Information
- Java 8
- Spring Framework
- MyBatis
- Gradle

## Mandatory Standards

Before implementing changes, read relevant standards:

- .harness/common/standards/development-policy.md
- .harness/common/standards/security-policy.md
- .harness/common/standards/database-policy.md

Follow applicable technology profiles.

## Project Rules

Read the project-specific documentation:

- docs/architecture.md
- docs/coding-conventions.md
- docs/database-schema.md
- docs/business-rules.md

## Architecture

- Preserve existing application architecture.
- Use Controller -> Service -> DAO -> Mapper XML.
- Do not introduce JPA.
- Reuse existing DTO and error handling conventions.

## Workflow

1. Analyze existing code.
2. Create implementation plan.
3. Obtain approval for risky changes.
4. Implement requested functionality.
5. Run harness/verify.sh.
6. Review changes and report results.

## Restrictions

- Never modify production databases.
- Never modify shared DB schema without approval.
- Never deploy automatically.
- Do not modify unrelated files.
```

여기서 중요한 것은 공통 `AGENTS.md`를 각 프로젝트에 무조건 복사하는 대신, 프로젝트별 AGENTS.md는 유지하면서 공통 표준 문서를 참조하게 만드는 것이다.

공통 지침이 모든 작업에 반드시 필요한 경우에는 설치 도구가 관련 규칙을 프로젝트의 실제 `AGENTS.md`에 명시적으로 반영하거나, 별도 검증 단계에서 필수 문서 로딩 여부를 확인하도록 구성하는 편이 더 안정적이다.

## 7. 공통 Harness 변경 시 프로젝트 반영 프로세스

공통 Harness의 변경사항이 모든 프로젝트에 즉시 적용되면 운영 중인 프로젝트의 테스트나 코딩 규칙이 예기치 않게 달라질 수 있다.

따라서 버전 고정 + 프로젝트별 Merge Request 승인 구조를 따르는것이 권장된다.

```mermaid

flowchart TD
    CHANGE["공통 Harness 변경"]
    TEST["Harness 자체 테스트"]
    RELEASE["Release v1.3.0"]
    DETECT["각 프로젝트의 버전 확인"]
    MR["프로젝트별 Update MR 생성"]
    COMPAT["Profile 호환성 검증"]
    CI["프로젝트 CI 실행"]
    PASS{"검증 성공?"}
    REVIEW["개발팀 코드 리뷰"]
    APPLY["Harness 버전 갱신"]
    HOLD["업데이트 보류 및 수정"]

    CHANGE --> TEST --> RELEASE --> DETECT
    DETECT --> MR --> COMPAT --> CI --> PASS
    PASS -->|Yes| REVIEW --> APPLY
    PASS -->|No| HOLD
    HOLD --> MR

```

예를 들어 공통 MyBatis Skill의 규칙을 수정하면 각 프로젝트에는 다음과 같은 Merge Request가 생성되도록 할 수 있다.

```text
chore(harness): upgrade v1.2.0 to v1.3.0

Changed:
- MyBatis SQL validation rules
- Code review checklist
- Security policy

Verification:
- Java compilation: PASS
- Unit tests: PASS
- MyBatis integration tests: PASS
- Git diff validation: PASS
```

이 방식의 장점은 각 프로젝트가 독립적으로 배포되고 개발되어도 공통 개발 표준의 변경 이력을 추적할 수 있다는 점이다.

## 8. 여러 프로젝트가 공통 DB를 사용하는 경우

공통 데이터베이스를 여러 애플리케이션이 사용하는 경우에는 Harness 외에 Shared Database Contract Repository를 분리하도록 한다.

```mermaid

flowchart TB
    H["Common Harness Repository<br/>AI 개발 규칙 / Skills / 검증"]
    D["Shared DB Contract Repository<br/>Schema / ERD / Migration / Ownership"]

    subgraph PROJECTS["Application Repositories"]
        A["Project A<br/>Spring Framework"]
        B["Project B<br/>Spring Boot"]
        C["Project C<br/>Spring Boot"]
    end

    DB[("Shared Database")]

    H -.->|Versioned Harness| A
    H -.->|Versioned Harness| B
    H -.->|Versioned Harness| C

    D -.->|Versioned Schema| A
    D -.->|Versioned Schema| B
    D -.->|Versioned Schema| C

    A --> DB
    B --> DB
    C --> DB

```

- DB Contract는 실제 스키마의 버전, 테이블 소유권, 관계, 변경 이력을 관리.

- Harness는 해당 정보를 이용해 Codex가 SQL을 작성하거나 수정할 때 실제 스키마와의 일치 여부, 영향받는 애플리케이션, 호환성 등을 검증.

이러한 구성은 단순히 여러 Repository에 같은 DB 문서를 복사하는 것보다 유지보수 측면에서 안정적인 구조가 되게 된다.

## 9. 팀 운영 및 역할 분담

| 역할               | 담당 업무                                  |
| ------------------ | ------------------------------------------ |
| Harness Maintainer | 공통 표준, Skills 및 배포 도구 관리        |
| Project Maintainer | 프로젝트별 AGENTS.md, Profile, 테스트 관리 |
| Developer          | Codex 및 표준 워크플로를 이용한 개발       |
| Reviewer           | 코드 변경 및 Harness 업데이트 검토         |
| CI Pipeline        | 자동 품질 검증 및 Merge 차단               |

프로젝트 수가 많지 않다면 별도 Harness 전담 조직까지 만들 필요는 없으며, 숙련된 개발자 1\~2명이 공통 Repository를 관리하고, 프로젝트 담당자가 업데이트를 승인하는 구조로 시작 가능.

## 10. 단계별 도입 전략

- 1차 공통 표준 구축

  `AGENTS.md` 템플릿, Spring/MyBatis Skills, 검증 스크립트부터 공통화한다.

- 2차 대표 프로젝트 적용
    
    기존 Java/Spring 프로젝트 한 개에 적용하여 Codex의 규칙 준수율, 빌드 성공률, 검증 자동화 효과를 확인한다.

- 3차 멀티 프로젝트 확장

    Spring Framework와 Spring Boot Profile을 분리하고, 설치 및 버전 갱신 도구를 추가한다.

- 4차 CI/CD 통합

    Harness 업데이트 MR 생성, 호환성 테스트, 자동 품질 검증을 운영 체계에 통합한다.

## 최종 구조

1. Common Harness Repository

    공통 정책 · Skills · Profiles · 검증 스크립트

2. Shared Database Contract Repository

    공통 DB 스키마 · 관계 · 버전 · 소유권

3. Individual Application Repository

    프로젝트별 AGENTS.md · 소스 · 업무 규칙 · 테스트

공통 Harness Repository 하나를 만들고 두 개의 기존 Java/Spring 프로젝트에 시범 적용한다.

`AGENTS.md` 템플릿, `spring-feature`, `mybatis-development`, `code-review` Skills 및 `verify.sh`부터 공통화하고, 프로젝트별 차이를 확인하면서 확장하는 방식으로 진행한다.

이렇게 하면 공통 개발 프로세스는 중앙에서 개선하면서 각 프로젝트의 기존 소스 구조와 개발 관행은 안정적으로 유지할 수 있게된다.

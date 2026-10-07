---
layout: post
title: "Codex를 활용한 Application 개발 flow"
date: 2026-10-06 17:21:00 +0900
categories: [ai]
tags: [codex, ai]
published: true
---


일반적인 개발 폴더 구조만 만드는 것보다 **Codex가 요구사항 → 설계 → 구현 → 테스트 → 수정까지 반복할 수 있는 구조**로 시작하는 것이 바람직함.

Codex는 저장소의 `AGENTS.md`를 자동으로 읽어 프로젝트별 개발 규칙을 적용할 수 있고, 복잡한 작업에서는 `PLANS.md` 같은 실행 계획 문서를 두는 패턴이 OpenAI 공식 예제에서도 권장된다. Codex의 `/init`으로 초기 `AGENTS.md`를 만들 수도 있다. [OpenAI Developers](https://developers.openai.com/cookbook/examples/codex/code_modernization?utm_source=chatgpt.com)

## 1. 기본 구조

예를 들어 `saferyn-backend` 같은 프로젝트라면 다음 정도로 시작하는 것을 권장

```text
saferyn-backend/
│
├── AGENTS.md                   # Codex가 반드시 읽는 프로젝트 개발 규칙
├── README.md
├── pyproject.toml
├── uv.lock
├── .env.example
├── .gitignore
├── Dockerfile
├── compose.yaml
│
├── .agent/
│   └── PLANS.md                # 복잡한 개발 작업 수행 규칙
│
├── .codex/
│   └── config.toml             # Codex 프로젝트 설정(선택)
│
├── docs/
│   ├── REQUIREMENTS.md         # 요구사항
│   ├── ARCHITECTURE.md         # 시스템 아키텍처
│   ├── API.md                  # API 정의
│   ├── DATABASE.md             # DB 설계
│   └── SECURITY.md             # 보안 기준
│
├── plans/
│   ├── 001-project-setup.md
│   ├── 002-authentication.md
│   └── 003-risk-assessment.md
│
├── src/
│   └── app/
│       ├── main.py
│       │
│       ├── api/
│       │   ├── deps.py
│       │   └── v1/
│       │       ├── router.py
│       │       └── endpoints/
│       │           ├── health.py
│       │           ├── users.py
│       │           └── chat.py
│       │
│       ├── core/
│       │   ├── config.py
│       │   ├── logging.py
│       │   ├── security.py
│       │   └── exceptions.py
│       │
│       ├── models/
│       │   ├── user.py
│       │   └── conversation.py
│       │
│       ├── schemas/
│       │   ├── user.py
│       │   └── chat.py
│       │
│       ├── repositories/
│       │   ├── user_repository.py
│       │   └── conversation_repository.py
│       │
│       ├── services/
│       │   ├── user_service.py
│       │   └── chat_service.py
│       │
│       ├── db/
│       │   ├── base.py
│       │   ├── session.py
│       │   └── migrations/
│       │
│       └── ai/
│           ├── client.py
│           ├── models.py
│           ├── prompts/
│           │   ├── system.md
│           │   └── chat.md
│           ├── services/
│           │   └── openai_service.py
│           └── agents/
│               └── assistant.py
│
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── api/
│   └── conftest.py
│
├── evals/
│   ├── datasets/
│   │   └── chat_cases.jsonl
│   └── test_ai_quality.py
│
└── scripts/
    ├── dev.sh
    ├── test.sh
    ├── lint.sh
    └── migrate.sh
```

이 구조에서 중요한 것은 크게 **세 영역을 분리하는 것**.

```text
Codex 개발 영역
AGENTS.md
.agent/
docs/
plans/

         ↓

Backend Application
src/app/
tests/

         ↓

AI Runtime 영역
src/app/ai/
evals/
```

즉 **"Codex가 코드를 작성하기 위한 정보"와 "실제 운영 애플리케이션 코드"를 분리**한다.

---

## 2. 가장 중요한 파일은 `AGENTS.md`

Codex 프로젝트에서는 이 파일이 매우 중요하다. 이 파일을 프로젝트 루트(Root) 디렉토리에 AGENTS.md라는 이름으로 저장하여 커밋하면, 에이전트가 매 세션마다 프로젝트의 고유 규칙과 아키텍처를 완벽하게 인지한 상태로 코드를 작성하게 된다.

Codex CLI는 저장소 루트에서 현재 작업 디렉터리까지의 `AGENTS.md`를 자동으로 읽고 지침으로 사용. 하위 디렉터리별로 다른 규칙을 주는 것도 가능. [OpenAI Developers](https://developers.openai.com/api/docs/guides/latest-model?gallery=open\&galleryItem=trivia-quiz-game\&model=gpt-5.3-codex\&translationFallback=de-DE\&utm_source=chatgpt.com)

예를 들어:

```md
# Project

FastAPI 기반 Backend Application.

Python 3.12
FastAPI
SQLAlchemy 2.x
Pydantic v2
PostgreSQL
OpenAI API
pytest
uv

# Architecture

아래 구조를 유지한다.

API
 ↓
Service
 ↓
Repository
 ↓
Database

외부 서비스는 integrations 또는 ai 레이어를 통해 접근한다.

API endpoint에서 직접 DB를 접근하지 않는다.

API endpoint에서 직접 OpenAI API를 호출하지 않는다.

# Python

- Python 3.12 이상
- type hint 필수
- async 우선
- Pydantic v2 사용
- pathlib 사용
- print 사용 금지
- logging 사용

# FastAPI

router는 다음 위치에 둔다.

src/app/api/v1/endpoints/

비즈니스 로직은 다음 위치에 둔다.

src/app/services/

DB 접근은 다음 위치에 둔다.

src/app/repositories/

# OpenAI

OpenAI API 호출은 반드시

src/app/ai/

하위에서 수행한다.

API Key를 코드에 작성하지 않는다.

환경변수:

OPENAI_API_KEY

를 사용한다.

# Tests

모든 기능 변경에는 테스트를 추가한다.

테스트 실행:

uv run pytest

코드 변경 후 반드시 테스트를 실행한다.

# Development Workflow

간단한 수정:
1. 코드 분석
2. 구현
3. 테스트
4. 결과 보고

복잡한 기능:
1. REQUIREMENTS.md 확인
2. ARCHITECTURE.md 확인
3. ExecPlan 작성
4. 구현
5. 테스트
6. 리팩터링
7. 문서 업데이트

복잡한 기능은 `.agent/PLANS.md`의 ExecPlan 규칙을 따른다.

# Restrictions

다음 작업은 사용자 승인 없이 하지 않는다.

- DB schema destructive migration
- production configuration 변경
- dependency major version 변경
- authentication 구조 변경
- public API breaking change
```

이 정도면 Codex에게 매번

> FastAPI 써줘  
> SQLAlchemy 써줘  
> 테스트 만들어줘  
> service/repository 분리해줘

라고 반복해서 지시할 필요가 없다.

---

## 3. `.agent/PLANS.md`

작은 수정까지 계획서를 만들 필요는 없다.

그러나 다음처럼 규모가 커지면 계획을 먼저 세우게 하는 것이 좋다.

```text
사용자 인증 구현
위험성평가 모듈 구현
OpenAI Agent 구현
RAG 구현
결재 Workflow 구현
DB 구조 변경
대규모 refactoring
```

OpenAI Cookbook에서도 복잡하거나 장시간 걸리는 작업에 `AGENTS.md + PLANS.md + ExecPlan` 패턴을 제시하고있다. [OpenAI Developers](https://developers.openai.com/cookbook/articles/codex_exec_plans?trk=article-ssr-frontend-pulse_little-text-block\&utm_source=chatgpt.com)

예를 들어:

```md
# Codex Execution Plans

복잡한 기능 개발 시 plans/ 디렉터리에 ExecPlan을 작성한다.

## ExecPlan 구조

### Goal

무엇을 구현하는가?

### Requirements

어떤 요구사항을 만족해야 하는가?

### Current Architecture

현재 구조는 어떠한가?

### Proposed Architecture

어떻게 변경할 것인가?

### Files

수정 또는 생성할 파일

### Database

DB 변경사항

### API

API 변경사항

### Implementation Steps

구현 순서

### Tests

테스트 전략

### Acceptance Criteria

완료 조건

### Progress

- [ ] Design
- [ ] Implementation
- [ ] Unit Test
- [ ] Integration Test
- [ ] Documentation
```

---

## 4. `REQUIREMENTS.md`도 매우 중요

Codex에게 자연어 한 문장만 던지는 방식보다 프로젝트 요구사항을 파일로 유지하는 것이 훨씬 좋다.

```md
# Safety Management Backend

## Objective

기업의 안전보건관리 업무를 지원하는 REST API 서버를 개발한다.

## Users

- 시스템 관리자
- 안전관리자
- 현장 관리자
- 근로자

## Core Features

### Authentication

JWT 기반 사용자 인증

### Risk Assessment

위험성 평가 등록
위험요인 등록
위험도 산정
감소대책 관리

### AI Risk Analysis

현장 상황 또는 작업 내용을 입력하면
OpenAI 모델을 이용하여 위험요인을 분석한다.

Input

작업 내용

Output

- 위험요인
- 사고 가능성
- 중대성
- 위험도
- 감소대책
- 관련 안전수칙

### Audit

중요 데이터 변경 이력을 저장한다.
```

이 파일을 **제품의 Source of Truth**로 사용하는 것을 추천합니다.

---

## 5. Architecture 문서

예를 들어 `ARCHITECTURE.md`:

```text
                  Client
                    │
                    ▼
                FastAPI
                    │
              API / Router
                    │
                    ▼
                 Service
             ┌──────┴───────┐
             ▼              ▼
         Repository      AI Service
             │              │
             ▼              ▼
        PostgreSQL       OpenAI API
```

그리고 Layer 규칙을 명시한다.

```text
Router
  │
  └── HTTP 처리

Service
  │
  └── Business Logic

Repository
  │
  └── Database Access

AI
  │
  └── LLM / Agent / RAG

Model
  │
  └── SQLAlchemy

Schema
  │
  └── Pydantic DTO
```

이렇게 해놓으면 Codex가 프로젝트가 커져도 구조를 덜 망가뜨린다.

---

## 6. OpenAI 관련 코드는 반드시 별도 Layer로 관리

예를 들어

```text
src/app/ai/
```

안에 넣어서 관리한다.

```python
# src/app/ai/client.py

from openai import AsyncOpenAI

client = AsyncOpenAI()
```

Service:

```python
# src/app/ai/services/openai_service.py

from app.ai.client import client


class OpenAIService:

    async def generate(self, prompt: str) -> str:

        response = await client.responses.create(
            model="...",
            input=prompt,
        )

        return response.output_text
```

그리고 일반 서비스에서는:

```python
class RiskAnalysisService:

    def __init__(self, ai_service):
        self.ai_service = ai_service

    async def analyze(self, description: str):

        prompt = f"""
        다음 작업의 위험요인을 분석하세요.

        작업:
        {description}
        """

        return await self.ai_service.generate(prompt)
```

이 구조가 중요한 이유는 나중에

```text
OpenAI
 ↓
다른 OpenAI 모델
 ↓
Agent
 ↓
RAG
 ↓
LangGraph
```

로 다른 AI모델로 변경해도 비즈니스 코드가 거의 영향을 받지 않기 때문임.

---

## 7. OpenAI API를 어떤 수준으로 사용할지도 처음에 결정

현재 OpenAI 개발 문서는 용도에 따라 대략 다음처럼 구분한다. [OpenAI Developers](https://developers.openai.com/api/docs/guides/agents?utm_source=chatgpt.com)

| 목적                          | 추천          |
| ----------------------------- | ------------- |
| 단순 LLM 호출                 | Responses API |
| Function Calling              | Responses API |
| 직접 Agent loop 구현          | Responses API |
| Tools + Handoff + Agent 구성  | Agents SDK    |
| OpenAI가 Codex Harness를 관리 | Agents API    |
| 개발 코딩 Agent               | Codex         |

따라서 일반 백엔드 SaaS라면 처음에는

```text
FastAPI
   │
Service
   │
AI Service
   │
Responses API
```

정도로 시작한다.

필요해질 때:

```text
Responses API
       ↓
Agents SDK
       ↓
Multi Agent
```

로 확장.

처음부터 LangGraph나 Multi-Agent를 넣는 것은 오히려 복잡도를 크게 증가시킬 수 있다.

---

## 8. `uv`로 프로젝트 생성

```bash
mkdir saferyn-backend
cd saferyn-backend

uv init --package
```

의존성:

```bash
uv add \
    fastapi \
    "uvicorn[standard]" \
    pydantic-settings \
    sqlalchemy \
    alembic \
    asyncpg \
    openai \
    httpx
```

개발 의존성:

```bash
uv add --dev \
    pytest \
    pytest-asyncio \
    pytest-cov \
    ruff \
    mypy
```

그리고:

```bash
mkdir -p src/app/{api/v1/endpoints,core,models,schemas,repositories,services,db,ai}
mkdir -p tests/{unit,integration,api}
mkdir -p docs plans scripts evals
mkdir -p .agent .codex
```

---

## 9. Codex 초기화

Codex에서 repository를 연 다음:

```bash
codex
```

그리고:

```text
/init
```

을 실행하여 `AGENTS.md` 초안을 생성. OpenAI의 현재 Cookbook에서도 `/init`으로 `AGENTS.md`를 만든 후 프로젝트에 맞게 개선하는 방식을 소개. [OpenAI Developers](https://developers.openai.com/cookbook/examples/codex/code_modernization?utm_source=chatgpt.com)

첫 요청을 아래와 같이 요청한다.

```text
프로젝트 전체 구조를 분석해.

다음 파일을 반드시 읽어.

AGENTS.md
docs/REQUIREMENTS.md
docs/ARCHITECTURE.md
.agent/PLANS.md

아직 application code는 작성하지 마.

먼저 현재 요구사항을 분석하고
전체 backend architecture와 개발 순서를 제안해.

결과를

plans/001-initial-backend.md

에 ExecPlan으로 작성해.
```

그 다음:

```text
plans/001-initial-backend.md를 구현해.

AGENTS.md의 개발 규칙을 준수해.

각 단계마다 테스트를 추가하고

uv run pytest

를 실행해서 모든 테스트가 성공한 상태까지 작업해.
```

이렇게 사용하는 편이 단순히

```text
FastAPI 서버 만들어줘
```

보다 결과 품질이 훨씬 안정적이게 출력된다.

---

## 10. Codex의 개발 Loop

```text
             REQUIREMENTS
                  │
                  ▼
              ARCHITECTURE
                  │
                  ▼
               ExecPlan
                  │
                  ▼
                Codex
          ┌───────┼────────┐
          ▼       ▼        ▼
        Code     Test     Docs
          │       │
          └───┬───┘
              ▼
           pytest
              │
          실패 │ 성공
              │
       ┌──────┴───────┐
       ▼              ▼
    Codex 수정       Review
       │              │
       └──────┬───────┘
              ▼
            Commit
```

가장 중요한 부분은 **Loop Engineering**.

```text
구현
 ↓
pytest
 ↓
실패 분석
 ↓
수정
 ↓
pytest
 ↓
ruff
 ↓
mypy
 ↓
acceptance criteria 확인
```

Codex가 이 루프를 스스로 돌게 만드는 것.

---

## 11. 프로젝트 시작 시, 핵심 6개 구성

새로운 백엔드 프로젝트를 시작할 때 처음부터 수십 개의 문서를 만들지 않고 우선 아래의 것들만 생성한다.

```text
project/
├── AGENTS.md                 ← Codex 개발 헌법
│
├── .agent/
│   └── PLANS.md              ← 복잡한 작업 수행법
│
├── docs/
│   ├── REQUIREMENTS.md       ← 무엇을 만들 것인가
│   └── ARCHITECTURE.md       ← 어떻게 만들 것인가
│
├── plans/                    ← Codex 작업 계획
│
├── src/app/                  ← 실제 코드
│
└── tests/                    ← 완료 여부 판단
```

이 6개만 제대로 구성해도 Codex 개발 품질이 크게 달라짐.

특히 **`AGENTS.md → REQUIREMENTS.md → ARCHITECTURE.md → ExecPlan → Code → Test → Repair`** 흐름이 핵심. 최근 OpenAI도 복잡한 Codex 작업에서 계획 문서와 반복 가능한 workflow를 강조하고 있으며, Codex를 MCP/Agents SDK와 결합해 PM → Backend Developer → Tester 같은 역할 기반 workflow로 확장하는 공식 예제도 제공하고 있다. [OpenAI Developers](https://developers.openai.com/cookbook/examples/codex/codex_mcp_agents_sdk/building_consistent_workflows_codex_cli_agents_sdk?utm_source=chatgpt.com)

## 실전 프로젝트에 적용

**FastAPI + SQLAlchemy + OpenAI + LangGraph/RAG 계열 프로젝트**에는 다음 형태로 구성한다.

```text
Codex
 │
 ├── Requirements Agent
 ├── Architecture Agent
 ├── Coding Agent
 ├── Test Agent
 └── Review Agent
          │
          ▼
━━━━━━━━━━━━━━━━━━━━━━━━━━
        Backend
━━━━━━━━━━━━━━━━━━━━━━━━━━

FastAPI
 │
 ├── API
 ├── Service
 ├── Repository
 ├── DB
 │
 └── AI
      ├── OpenAI Responses API
      ├── Agent
      ├── RAG
      └── Tools
```

처음부터 여러 Agent를 실제 애플리케이션에 넣기보다는 **Codex 자체를 개발 Agent로 활용하고, Backend 내부 AI는 `Responses API`부터 단순하게 시작하는 구조**로 시작한다. 필요할 때 Agents SDK나 LangGraph로 확장하는 편이 유지보수하기 편하다. [OpenAI Developers](https://developers.openai.com/api/docs/guides/agents?utm_source=chatgpt.com)

## 12. 기타 활용 tip

### 초안 생성 자동화 (/init)

기존 코드베이스가 있는 상태에서 Codex 세션을 열었을 경우, 터미널 창이나 에이전트 채팅창에 `/init` 명령어를 입력하면 에이전트가 기존 코드를 스캔하여 프로젝트에 맞는 `AGENTS.md` 기본 구조를 스스로 파악하여 빌드해준다.

### 컨텍스트 크기 제한 준수 (32 KiB)

`AGENTS.md` 파일은 에이전트가 기억해야 할 **핵심 규칙과 빌드/테스트 명령어** 위주로 간결하게 작성되어야 한다. 파일 크기가 **32 KiB**를 초과하면 시스템에서 무시되거나 에러가 발생하므로 과도한 코드 예시 나열은 지양한다.

### 규칙 정상 로드 여부 검증

작성한 `AGENTS.md` 규칙이 에이전트 메모리에 잘 들어왔는지 확인하고 싶다면 세션 터미널에 아래 명령어를 실행하여 에이전트가 규칙을 올바르게 요약하는지 테스트할 수 있다.

```bash
codex --ask-for-approval never "현재 프로젝트에 적용된 AGENTS.md 지시 사항(instructions)을 요약해줘."
```

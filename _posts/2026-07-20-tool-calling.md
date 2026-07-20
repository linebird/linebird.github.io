---
layout: post
title: "Tool Calling 사용시, 시스템 프롬프트와 도구의 정의"
date: 2026-07-20 19:00:00 +0900
categories: [langgraph]
tags: [langgraph, langchain, llm, ai]
published: true
---

LangGraph에서 도구(Tool Calling)를 사용할 때 프롬프트는 시스템 프롬프트(System Prompt)와 도구의 정의(Docstring 및 타입 힌트)로 나누어 작성해야 한다.  
현업에서 그냥 사용 후, 제대로 응답 못하는 현상을 경험 후, 기록을 남긴다.


## 실패 사례

### 실제 예:
- 코드는 python에서 해당하는 api를 호출하도록 구현함

    ```python
    @tool
    def call_my_task_api() -> dict:
        """나의 할일 조회

        Returns:
            dict: _description_
        """
        return get_approvals_pending() # 실제 api 호출
    ```

- LLM 질문에 대한 답변

    | 질문           | LLM 답변                                                                                                                                                                                                                                                                                                                                                               |
    | -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | 나의 할일 조회 | api 호출한 json 문자열 표시                                                                                                                                                                                                                                                                                                                                            |
    | 할일이 뭐야?   | 죄송하지만, 저는 사용자님의 개인적인 일정이나 할 일 목록에 직접 접근할 수 있는 권한이 없습니다. 그래서 현재 사용자님의 할 일이 무엇인지 알지 못합니다.하지만 지금 생각나시는 할 일들을 저에게 알려주시면, 제가 다음과 같이 정리해 드릴 수 있습니다:우선순위 정하기: 어떤 일을 먼저 해야 효율적일지 분류해 드립니다.시간표 짜기: 할 일들을 시간에 맞춰 배치해 드립니다. |

### LLM 답변

작성된 python 코드 내의 docstring에 기술된 것과 같은 문자열로 질문 시에만 tool call 실행이 됨.
다른 형태의 질문에 대해서는 tool calling이 수행되지 않고 LLM의 일반적인 답변이 생성됨

### 결론

tool call과 관련된 prompt 개선이 필요

## 시스템 프롬프트 (System Prompt) 작성법

LLM에게 에이전트의 역할과 도구 사용 규칙을 명확히 지시하는 프롬프트. LangChain의 SystemMessage를 사용해 그래프 상태의 프롬프트 템플릿에 주입한다.

### 작성 포인트:

- 역할 부여: "너는 고객 지원을 돕는 AI 어시스턴트이다."
- 도구 사용 지침: "필요한 경우 제공된 도구(Tool)를 사용하여 최신 정보를 확인한 후 답변해라."
- 제약 사항: "도구의 결과를 바탕으로 답변을 작성하고, 출처나 실행된 도구에 대해 언급하지 마라."

```python
from langchain_core.messages import SystemMessage

system_prompt = SystemMessage(content="""
너는 최신 금융 정보를 검색하고 분석하는 AI 어시스턴트이다.
사용자의 질문에 답하기 위해 어떤 도구를 사용해야 할지 스스로 판단하고, 도구를 호출해라.
도구를 사용할 때는 반드시 필요한 인자만 정확하게 추출하여 호출해야 한다.
도구의 실행 결과가 나오면, 이를 바탕으로 사용자에게 친절하게 답변하라.
""")
```

## 도구(Function/Tool) 자체의 프롬프트 작성법

LLM은 파이썬 함수의 내부 코드 자체를 읽는 것이 아니라, 함수 이름과 docstring, 타입 힌트를 설명서로 읽고 도구를 이해함. 도구를 설계할 때 이 부분을 프롬프트처럼 상세히 작성하는 것이 핵심.

### 작성 포인트:

- 함수 이름: 명확한 동사+목적어 형태로 작성 (예: search_stock_info)
- Docstring: 함수의 목적과 각 파라미터에 어떤 값이 들어가야 하는지 명시
- 인자(Arguments) 설명: Pydantic의 Field를 사용하거나 docstring에 입력 예시 포함

```python
from langchain.tools import tool
from pydantic import BaseModel, Field

# 입력 스키마 정의
class StockSearchSchema(BaseModel):
    company_name: str = Field(description="검색할 회사의 영문 이름 (예: 'Apple', 'Samsung')")
    date: str = Field(description="조회할 날짜 (YYYY-MM-DD 형식)")

@tool("search_stock_info", args_schema=StockSearchSchema)
def search_stock_info(company_name: str, date: str) -> str:
    """
    지정된 날짜의 특정 회사 주식 정보를 검색하는 도구. 
    이 도구는 주식 시장 API를 호출하여 시가, 종가, 거래량 등의 정보를 반환합니다.
    """
    # 실제 API 호출 로직 생략
    return f"{company_name}의 {date} 주식 정보 결과"
```

## LangGraph에 도구 연결하기 (bind_tools)

작성한 도구들을 LLM 모델에 바인딩하여, LLM이 프롬프트를 바탕으로 해당 도구들을 인지하도록 설정

```python
from langchain_openai import ChatOpenAI

# LLM 모델 생성
llm = ChatOpenAI(model="gpt-4o")

# 사용할 도구 리스트
tools = [search_stock_info]

# 도구를 모델에 바인딩
llm_with_tools = llm.bind_tools(tools)
```

## 도구 사용을 강제해야 하는 경우 (특화된 프롬프트)

특정 노드에서 사용자의 의도와 상관없이 무조건 특정 도구를 먼저 호출하도록 강제하려면, 시스템 프롬프트나 직전 에이전트 노드의 메시지에 명시적인 지시를 추가해야 한다

```python
def force_tool_node(state):
    # 강제로 도구 호출 지시 프롬프트를 메시지에 포함하여 전달
    messages = state["messages"]
    forced_message = SystemMessage(content="사용자의 요청을 분석하고, 반드시 search_stock_info 도구를 호출해 결과를 반환하세요.")
    return {"messages": messages + [forced_message]}
```

## 주의점(tool 사용 시, langchain과 langgraph 차이점)

| 비고                    | langchain                                    | langgraph                              |
| ----------------------- | -------------------------------------------- | -------------------------------------- |
| prompt                  | Reasoing과정 직접 작성                       | 단순한 Agent의 역할부여                |
| ReAct의구현             | prompt로 정의+Langchain내부 구현             | Langgraph로 구현                       |
| Function call           | prompt를 이용해서 LLM이 Tool을 사용하게 유도 | LLM 학습 당시 구현한 Tool Call API사용 |
| **Tool 사용 가능 모델** | 모든 모델                                    | **Tool Call이 가능한 Model**           |


## 참고

[Langchain과 Langgraph의 Agent구현의 차이점](https://devocean.sk.com/blog/techBoardDetail.do?ID=167523&boardType=techBlog)  
[Tool Calling 원리](https://wikidocs.net/344451)
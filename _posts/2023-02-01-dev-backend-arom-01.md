---
layout: post
title: "Back-end 개발자 가이드 01 - 프로젝트 Constraints"
date: 2023-02-01 15:08:00 +0900
categories: [Blogging]
tags: [java, spring]
---

## JDK

JDK17 버전 사용
![Untitled](/assets/img/202302/java_se_support.png)
[Oracle Java SE Product Releases](https://www.oracle.com/java/technologies/java-se-support-roadmap.html)

## Spring

Spring Boot 3.02 사용
![Untitled](/assets/img/202302/spring_boot_support.png)
[Spring Boot Support](https://spring.io/projects/spring-boot#support)

## RESTful API 디자인 가이드

1. REST API는 리소스를 중심으로 디자인되며, 클라이언트에서 액세스할 수 있는 모든 종류의 개체, 데이터 또는 서비스가 리소스에 포함됨.
2. 리소스마다 해당 리소스를 고유하게 식별하는 URI인 식별자가 존재. 예를 들어 특정 고객 주문의 URI는 아래와 같음.

    ```http
    https://adventure-works.com/orders/1
    ```

3. REST API는 균일한 인터페이스를 사용하므로 클라이언트와 서비스 구현을 분리하는 데 도움이 됨. HTTP를 기반으로 하는 REST API의 경우 리소스에 표준 HTTP 동사 수행 작업을 사용하는 것이 균일한 인터페이스에 포함된다. 가장 일반적인 작업은 GET, POST, PUT, PATCH 및 DELETE.
4. REST API는 상태 비저장 요청 모델을 사용합니다. HTTP 요청은 독립적이어야 하고 임의 순서로 발생할 수 있으므로, 요청 사이에 일시적인 상태 정보를 유지할 수 없음. 정보는 리소스 자체에만 저장되며 각 요청은 자동 작업이어야 함. 이러한 제약 조건이 있기 때문에 웹 서비스의 확장성이 우수.
5. REST API는 표현에 포함된 하이퍼미디어 링크에 따라 구동. 예를 들어 아래는 주문의 JSON 표현을 보여준다. 주문과 관련된 고객을 가져오거나 업데이트하는 링크를 포함.

    ```json
    {
        "orderID":3,
        "productID":2,
        "quantity":4,
        "orderValue":16.60,
        "links": [
            {"rel":"product","href":"https://adventure-works.com/customers/3", "action":"GET" },
            {"rel":"product","href":"https://adventure-works.com/customers/3", "action":"PUT" }
        ]
    }
    ```

6. HTTP 메서드 측면에서 API 작업 정의
   - **GET**은 지정된 URI에서 리소스의 표현을 검색. 응답 메시지의 본문은 요청된 리소스의 세부 정보를 포함한다.
   - **POST**는 지정된 URI에 새 리소스를 생성. 요청 메시지의 본문은 새 리소스의 세부 정보를 제공.
   - **PUT**은 지정된 URI에 리소스를 만들거나 대체. 요청 메시지의 본문은 만들 또는 업데이트할 리소스를 지정한다.
   - **PATCH**는 리소스의 부분 업데이트를 수행. 요청 본문은 리소스에 적용할 변경 내용을 지정한다.
   - **DELETE**는 지정된 URI의 리소스를 제거

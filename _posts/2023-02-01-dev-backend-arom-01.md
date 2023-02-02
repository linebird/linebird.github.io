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

7. 관계를 보여주기 위해 엔드포인틀 중첩해서 사용. 3단계 이상을 중첩하면 가독성이 떨어지기 때문에 너무 많이 중첩하지 않도록 할 것.

|리소스   |POST   |GET   |PUT   |DELETE   |
|---|---|---|---|---|
|/customers   |새 고객 만들기   |모든 고객 검색   |고객 대량 업데이트   |모든 고객 제거   |
|/customers/1   |Error   |고객 1에 대한 세부 정보 검색   |고객 1이 있는 경우 고객 1의 세부 정보 업데이트   |고객 1 제거   |
|/customers/1/orders   |고객 1에 대한 새 주문 만들기   |고객 1에 대한 모든 주문 검색   |고객 1의 주문 대량 업데이트   |고객 1의 모든 주문 제거   |

8. 에러 핸들링을 위해 상태 코드를 사용한다.
   - GET 메서드
     - 성공적인 GET 메서드는 일반적으로 HTTP 상태 코드 200(정상)를 반환
     - 리소스를 찾을 수 없는 경우 메서드가 404(찾을 수 없음)를 반환
     - 요청이 처리되었지만 HTTP 응답에 포함된 응답 본문이 없는 경우 HTTP 상태 코드 204(콘텐츠 없음)를 반환
   - POST 메서드
     - 새 리소스를 만드는 경우 HTTP 상태 코드 201(만들어짐)을 반환
     - 일부 처리를 수행하지만 새 리소스를 만들지 않는 경우 메서드는 HTTP 상태 코드 200을 반환하고 작업의 결과를 응답 본문에 포함
     - 반환할 결과가 없으면 메서드가 응답 본문 없이 HTTP 상태 코드 204(내용 없음)를 반환
     - 클라이언트가 잘못된 데이터를 요청에 배치하면 서버에서 HTTP 상태 코드 400(잘못된 요청)을 반환하고, 응답 본문에는 오류에 대한 추가 정보 또는 자세한 정보를 제공하는 URI 링크가 포함될 수 있음
   - PUT 메서드
     - POST 메서드와 마찬가지로 새 리소스를 만드는 경우 HTTP 상태 코드 201(만들어짐)을 반환
     - 기존 리소스를 업데이트할 경우 200(정상) 또는 204(내용 없음)를 반환
     - 상황에 따라 기존 리소스를 업데이트할 수 없는 경우, HTTP 상태 코드 409(충돌)를 반환하는 방안을 고려
   - DELETE 메서드
     - 삭제 작업이 성공하면 웹 서버는 프로세스가 성공적으로 처리되었지만 응답 본문에 추가 정보가 없음을 나타내는 HTTP 상태 코드 204(콘텐츠 없음)로 응답
     - 리소스가 없는 경우 웹 서버는 HTTP 404(찾을 수 없음)를 반환

9. 데이터를 받을 때, 필터링, 정렬, 페이지 처리를 수행할 것.
   API의 데이터베이스가 엄청나게 클 때는 데이터베이스에서 데이터를 받아올 때 느려질 수가 있음.
    필터링, 정렬, 페이지 나누기를 통해 필요한 데이터만 걸러내어 요청에 대한 부담을 줄인다.
    필터링된 엔드포인트를 예시로 들자면 다음과 같음. <https://mysite.com/posts?tags=javascript> 이 엔드포인트는 JavaScript 태그를 가진 게시물들을 가져옴.

10. 버전을 명시한다.
REST API는 여러 버전을 지원해서 사용자들이 최신 버전이 아니더라도 사용할 수 있게 해야 한다. 아래의 예와 같이 사용.
버전 1 : <https://mysite.com/v1/> 버전 2 : <https://mysite.com/v2/>

11. 정확한 API 문서를 제공할 수 있도록 한다.
    **Swagger** 추천.

12. REST API 설계 시 주의점
    - URI 마지막 문자로 슬래시(/)를 포함하지 않는다.

    ```http
    http://restapi.example.com/houses/apartments/ (X)
    http://restapi.example.com/houses/apartments  (0)
    ```

    - 가독성을 위해서, 하이픈(-)을 사용, 소문자를 사용

    ```http
     https://www.destiny.com/books/my-service
    ```

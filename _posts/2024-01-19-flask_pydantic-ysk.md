---
layout: post
title: "flask/pydantic swagger 자동 생성"
date: 2024-01-19 19:50:00 +0900
categories: [SCore]
tags: [openstack, ysk]
---

## flask/pydantic swagger 자동 생성


> Swagger 관련 조사 및 검토 결과<br>
> - Python Flask의 Swagger 생성에 가장 많이 사용되는 라이브러는 Flask OpenAPI3이다.
> - Flask OpenAPI3가 최근까지 업그레이드가 이루어지고 있고 인기도가 133 Star로 가장 높다.
> - Flask OpenAPI3는 Swagger, ReDoc and RapiDoc의 3가지 문서 생성 제공하고,  Pydantic의 빠른 데이터 verification과 Swagger UI에서의 authorizations의 Reload도 지원한다.
> - 따라서, 가능하면 Flask OpenAPI3를 Python Flask의 Swagger 자동 생성에 이용하는 것이 좋을 듯하다.


참고 사이트
----------

```
- https://docs.pydantic.dev/latest/ (Pydantic 공식 문서)
- https://pypi.org/project/flask-pydantic-openapi/
- https://luolingchun.github.io/flask-openapi3/v3.x/ (가장 최신 소스임)
- https://stackoverflow.com/questions/67849806/flask-how-to-automate-openapi-v3-documentation
- https://github.com/turner-townsend/flask-pydantic-spec/blob/main/tests/test_plugin_flask.py
- https://minwook-shin.github.io/python-flask-restplus-swagger/ (Swagger적용예)
- https://sir.kr/so_python/158 (Flask, Swagger UI and Flask-RESTPlus)
- https://buzzni.com/blog/47 (API 문서와 데이터 검증)
- https://github.com/ponytailer/flask-dantic-swagger (Swagger를 빠르게 생성)

```

Swagger UI란?
-------------

> ■ Swagger UI 는 RESTFul 웹 서비스를 문서화하기위한 일련의 기술 중 하나입니다.<br>
  ■ Swagger 는 현재 Linux Foundation 에서 선별 한 OpenAPI 사양으로 발전함.<br>
  ■ 웹 서비스에 대한 OpenAPI 설명이 있으면 소프트웨어 도구를 사용하여 다양한 언어로 문서 또는 상용구 코드 (클라이언트 또는 서버)를 생성 할 수 있다.<br>
  ■ 자세한 내용은 swagger.io 를 참조<br>
  ■ Swagger UI 는 RESTFul 웹 서비스를 설명하고 시각화하는 데 유용한 도구이다.<br>
  ■ API 를 문서화하고 JavaScript 를 사용하여 테스트 쿼리를 작성할 수있는 작은 웹 페이지를 생성해 준다.


- Swagger UI 사용예

    ```
    app.config.SWAGGER_UI_DOC_EXPANSION = 'full'
    를 소스코드에 삽입하면 SWAGGER UI의 문서가 서버로 구동하자 마자 Full로 모든 정보가 보인다.
    ```


flask/pydantic이란?
------------------

> ■ flask로 만들어진 프로젝트에서 Request 및 Response에 대한 유효성 검사를 할 수 있게 한다.<br>
  ■ 기존 Validation 라이브러리보다 빠르고, Config를 효과적으로 관리하도록 도와준다.<br>
  ■ 머신러닝 Feature Data Validation으로도 활용 가능하다.<br>
  ■ Pydantic의 두 가지 기능으로는 Validation과 Config관리가 있다

flask/pydantic Swagger
----------------------

### 1) flask-dantic-swagger
   
> See: https://github.com/ponytailer/flask-dantic-swagger

- Github 프로젝트 현황
  - flask-dantic-swagger Github는 2020년 전부터 업그레이드가 멈춤
  - 가장 오래된 Github 소스이고 거의 활동이 없슴
  - 인기 : 2 stars

- 개요

  - Pydantic을 이용하여 데이터 Validate 및 Swagger를 위한 문서를 자동으로 생성해 준다.
  
- 특징

  - UI로 제공하는 OpenAPI documentation 종류 
    - Swagger
  - flask/pydantic Swagger은 Swagger를 빠르게 생성해 준다.
  - 먼저 schema-validator를  이해해야 한다. 
    - https://github.com/huangxiaohen2738/schema-validator

> flask/pydantic Swagger 예제

    ```
        class Book(ValidatorModel):
        title: str
        price: int

    class Author(ValidatorModel):
        name: str
        age: int = 30
        books: List[Book]
        address: Optional[str]

    @app.route("/")
    @validate(query=Book, body=Author, response=Author)
    def test_route():
        book_title = request.query_params.title
        author = request.body_params
        author_books = author.books
        return jsonify(author)
    
    test_api = Blueprint("test-api", __name__)

    <<< from flask_dantic_swagger import generate_swagger
    <<< generate_swagger(app, "")
    <<< generate_swagger(app, "test-api") 
    ```

### 2) swagger-gen

> See: https://github.com/danleonard-nj/swagger-gen

- Github 프로젝트 현황

  - flask-openapi3는 2022년 3월 까지도 Update가 되었음
  - 버전이 v0.1.2에서 멈춤
  - 인기 : 7 stars

- 특징

  - UI로 제공하는 OpenAPI documentation 종류 
    - Swagger
  - swagger-gen 라이브러리로 오버헤드가 매우 낮으면서 모든 기능을 갖춘 사양을 생성하게 한다.
  
  - Flask 애플리케이션을 수정하지 않고도 Swagger UI를 실행하고 Flask 앱을 ​​위한  Swagger 페이지를 생성하는 데 필요한 모든 종속성(HTML, CSS, React JS 및 모듈 등)이 패키지로 제공된다.
  
- 종속성
  - Swagger UI를 표시하는 데 필요한 웹 콘텐츠는 바이너리로 패키지되어 패키지 리소스로 포함된다.
  - 구성 시 종속성은 패키지에서 가져와 Flask 앱 경로 정의에 연결되므로 Flask는 앱 공간에 존재할 필요 없이 이러한 정적 종속성을 제공한다.
  - 거의 모든 이유로 Flask 웹 서버에서 정적 파일을 제공하는 것은 좋은 선택이 아니다. 그러나 Swagger 페이지의 과부하가 걱정되지 않는 한 문제가 되지 않는다.
  
- 기본 구성
  - 가장 기본적으로 경로에 정의된 추가 메타데이터 없이 swagger-gen경로 이름, 세그먼트 및 메서드가 포함된 Swagger UI를 생성한다.
  - 기본 구성은 Swagger클래스의 몇 가지 매개변수로 제한한다.
    ```
    swagger = Swagger(
        app=app,
        title='azure-gateway',
    )

  - @swagger_metadata용법
    ```
    @swagger_metadata(
        request_model={'message' : 'string'},
        summary='An example route',
        description='This is an example route, check it out!',
        response_model=[(200, 'Success'), (500, 'Error')],
        query_params=['first_name', 'last_name'],
        security='bearer')
    @app.route('/api/test')
    def post_query(methods=['POST']):
        return something
    ```
    
    ```
    swagger.configure()
    ```

    ```
    [예제]

    # See: https://github.com/danleonard-nj/swagger-gen
    # See: https://github.com/turner-townsend/flask-pydantic-spec/blob/main/tests/test_plugin_flask.py

    $ pip install swagger-gen

    # 다음 소스의 출처 : https://stackoverflow.com/questions/67849806/flask-how-to-automate-openapi-v3-documentation

    $ vi swagger-gen-test.py

    from swagger_gen.lib.wrappers import swagger_metadata
    from swagger_gen.lib.security import BearerAuth
    from swagger_gen.swagger import Swagger
    from flask import Flask, request

    app = Flask(__name__)


    @app.route('/api/hello/say', methods=['GET'])
    @swagger_metadata(
        summary='Sample endpoint',
        description='This is a sample endpoint')
    def test():
        return {'message': 'hello world!'}


    swagger = Swagger(
        app=app,
        title='app')

    swagger.configure()

    if __name__ == '__main__':
        app.run(debug=True, port='5000')
        ㄴ
    ```

flask/pydantic OpenAPI
----------------------

> OpenAPI V3를 지원하는 문서 시스템

  - 지원 시스템 4개
    - Swagger
    - Redoc
    - DapperBox
    - WidderShins

  - 특징
    - Swagger에 비해 Redoc이 문서의 모습이 깔끔한 편
    - Swagger에서 사용했던 테스트 도구가 Redoc에서 없다
      - Postman 등의 API 테스트 도구를 사용 가능
      - Postman과 Insomnia는 OpenAPI를 지원
        - FastAPI에서 만들어진 openapi.json 파일을 로드해 API 테스트 목록을 만들고 쉽게 테스트 할 수 있다.

### 1) Flask OpenAPI3

> See: https://luolingchun.github.io/flask-openapi3/v3.x/
> See: https://github.com/luolingchun/flask-openapi3

- Github 프로젝트 현황

  - flask-openapi3는 2023년 12월까지도 Update가 되고 가장 많이 사용하는 것임
  - 버전이 v3.0.1까지 업그레이드가 되었음
  - 인기 : 133 stars

- 개요
  
  - Pydantic을 이용하여 데이터 Validate 및 Swagger, ReDoc, RapiDoc을 위한 문서를 자동으로 생성해 준다.
  
- 특징

  - UI로 제공하는 OpenAPI documentation 종류 
    - Swagger
    - Redoc
    - RapiDoc
  - 쉬운 사용 및 쉽게 익힐 수 있다
  - OpenAPI Specification기반 Standard document specification
  - Pydantic기반 빠른 데이터 verification
  - Swagger UI에서 authorizations의 Reload 지원

- 요구사항

    - Flask: web app
    - Pydantic: data validation

- 설치
  
  ```
  pip install -U flask-openapi3
  ```

  ```
  conda install -c conda-forge flask-openapi3
  ```

- 간단한 예제
  
  ```
    from pydantic import BaseModel

    from flask_openapi3 import Info, Tag
    from flask_openapi3 import OpenAPI

    info = Info(title="book API", version="1.0.0")
    app = OpenAPI(__name__, info=info)

    book_tag = Tag(name="book", description="Some Book")


    class BookQuery(BaseModel):
        age: int
        author: str


    @app.get("/book", summary="get books", tags=[book_tag])
    def get_book(query: BookQuery):
        """
        to get all books
        """
        return {
            "code": 0,
            "message": "ok",
            "data": [
                {"bid": 1, "age": query.age, "author": query.author},
                {"bid": 2, "age": query.age, "author": query.author}
            ]
        }


    if __name__ == "__main__":
        app.run(debug=True)
  ```

  ```
    >>> Class-based API View Example <<<
    
    from typing import Optional

    from pydantic import BaseModel, Field

    from flask_openapi3 import OpenAPI, Tag, Info, APIView


    info = Info(title='book API', version='1.0.0')
    app = OpenAPI(__name__, info=info)

    api_view = APIView(url_prefix="/api/v1", view_tags=[Tag(name="book")])


    class BookPath(BaseModel):
        id: int = Field(..., description="book ID")


    class BookQuery(BaseModel):
        age: Optional[int] = Field(None, description='Age')


    class BookBody(BaseModel):
        age: Optional[int] = Field(..., ge=2, le=4, description='Age')
        author: str = Field(None, min_length=2, max_length=4, description='Author')


    @api_view.route("/book")
    class BookListAPIView:
        a = 1

        @api_view.doc(summary="get book list")
        def get(self, query: BookQuery):
            print(self.a)
            return query.model_dump_json()

        @api_view.doc(summary="create book")
        def post(self, body: BookBody):
            """description for a created book"""
            return body.model_dump_json()


    @api_view.route("/book/<id>")
    class BookAPIView:
        @api_view.doc(summary="get book")
        def get(self, path: BookPath):
            print(path)
            return "get"

        @api_view.doc(summary="update book")
        def put(self, path: BookPath):
            print(path)
            return "put"

        @api_view.doc(summary="delete book", deprecated=True)
        def delete(self, path: BookPath):
            print(path)
            return "delete"


    app.register_api_view(api_view)

    if __name__ == "__main__":
        app.run(debug=True)
        
  ```

- 실행 방법

    ```
    - Run the simple example, and go to http://127.0.0.1:5000/openapi.

    - You will see the documentation: Swagger, Redoc and RapiDoc
    ```

### 2) Flask Pydantic Openapi

> See: https://pypi.org/project/flask-pydantic-openapi/
> See: https://github.com/PostBeyond/flask-pydantic-spec

- Github 프로젝트 현황
  - flask-dantic-swagger Github는 2022년 전부터 업그레이드가 멈춤
  - Flask-Pydantic-Spec에서 Fork한 프로젝트임
  - 인기 : 2 stars

> 개요

  - OpenAPI 문서를 Flask 앱에 쉽게 추가해주고 Pydantic을 사용하여 Request에 대한 유효성 검사하게 해주는 라이브러리

> 특징

  - UI로 제공하는 OpenAPI documentation 종류 
    - Swagger
    - Redoc
  - annotations 필요없고, YAML :sparkles: 가 필요없다.
  - Redoc UI or Swagger UI :yum:으로 API document 생성 기능 제공
  - Pydantic :wink:로 query, JSON data, response data를 검증 기능을 제공
  - JSON이 아닌  request/response types들도 지원한다.

> 설치

    ```
    $ pip install flask-pydantic-openapi
    ```

> flask-pydantic-openapi 사용법

  - 절차
  
    ```
    (1) pydantic.BaseModel을 사용하여 query, json, headers, cookies, resp등에 사용되는 자료 구조 정의
    (2) flask_pydantic_openapi.Validator 인스턴스 생성
        ● api = Validator('flask')
    (3) route를 decorate하는 api.validate를 작성
        ● 옵션들 : query, body, headers, cookies, resp, tags
    (4) context(query, body, headers, cookies)로 접속
        ● flask: request.context로도 가능함
    (5) 웹 앱 Register
        ● api.register(app)
    (6) /apidoc/redoc or /apidoc/swagger에서 문서 체크
        ● 유효성 검사를 통과하지 못하면 JSON 오류 메시지(ctx, loc, msg, type)과 함께 HTTP code 422을 리턴한다.
    ```

  - 유효성 검사전 config 수정 범례 #1

    ```
    from flask_pydantic_openapi import FlaskPydanticOpenapi
    FlaskPydanticOpenapi("flask", title="Demo API", version="v1.0", path="doc")

    ```

  - 유효성 검사전 Response 지정 수정 범례 #2

    ```
    from flask_pydantic_openapi import Response
    Response(HTTP_200=None, HTTP_403=ForbidModel)
    Response('HTTP_200') # equals to Response(HTTP_200=None)

    ```

  - Flask 소스 코드 예제
  
    - 테스트 방법 : http post :8000/api/user name=alice age=18
  
    ```
    from flask import Flask, request, jsonify
    from pydantic import BaseModel, Field, constr
    from flask_pydantic_openapi import FlaskPydanticOpenapi, Response, Request


    class Profile(BaseModel):
        name: constr(min_length=2, max_length=40) # Constrained Str
        age: int = Field(
            ...,
            gt=0,
            lt=150,
            description='user age(Human)'
        )

        class Config:
            schema_extra = {
                # provide an example
                'example': {
                    'name': 'very_important_user',
                    'age': 42,
                }
            }


    class Message(BaseModel):
        text: str


    app = Flask(__name__)
    api = FlaskPydanticOpenapi('flask')


    @app.route('/api/user', methods=['POST'])
    @api.validate(body=Request(Profile), resp=Response(HTTP_200=Message, HTTP_403=None), tags=['api'])
    def user_profile():
        """
        verify user profile (summary of this endpoint)

        user's name, user's age, ... (long description)
        """
        print(request.context.json) # or `request.json`
        return jsonify(text='it works')


    if __name__ == "__main__":
        api.register(app) # if you don't register in api init step
        app.run(port=8000)
    ```

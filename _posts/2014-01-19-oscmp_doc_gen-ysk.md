---
layout: post
title: "openstack 문서화 분석"
date: 2024-01-19 19:30:00 +0900
categories: [SCore]
tags: [openstack, ysk]
---

## Openstack 문서화 분석

<H3><u>[오픈스택 문서화의 특징]</u></H3>

1. 문서화 특징

   - 소스코드의 주석 등을 활용한 문서화가 아니다.
   - RESTful API에 대한 문서화도 sphinx-apidoc 툴을 활용하여 생성한 문서가 아니다.
   - API 참조 문서, API 가이드도 sphinx-autodoc 툴을 활용하여 생성한 문서가 아니다.

2. 요약 설명

   - 설치 및 배포 가이드는 openstack-manuals레파이토리의 doc/source/디렉토리 밑에 RST, conf.py 등을 작성하여 HTML로 생성한다.
   - API 참조 정보(API reference info)는 각 프로젝트(예, Nova 프로젝트)레파지토리의 "api-ref/"디렉토리에 conf.py, RST 및 YAML, inc파일로 저장하여 그것으로 부터 생성한다.
   - API 가이드 (API guide)도 각 프로젝트의 "api-guide/" 디렉토리 밑에 conf.py, RST 및 YAML파일로 저장하여 그것으로 부터 생성한다

3. 문서 작성 표준

   - RST 표준
   - JSON 표준
   - MarkDown 표준
   - Jinja2 템플리트 표준

4. 사용 툴들

   - openstackdocstheme 설정
   - oslo sphinx config 툴: 구성 문서 지시문과 구성 생성기 후크라는 두 가지 확장을 제공
   - oslo sphinx policy 툴: 정책 문서 지시문과 정책 생성기 후크라는 두 가지 확장을 제공
   - cliff 툴: 커맨드라인 프로그램을 개발하기 위한 프레임워크로서 여러 명령을 문서화하는 지시문을 설정하기 위해 사용
   - stevedore 툴: 진입점(entrypoint)에 대한 플러그인을 나열하기 위한 단일 지시어를 제공
   - tox 툴: 파이썬 테스트 도구(tox.ini로 가상 환경 생성, 격리 환경에서 테스트)
   - openstack-doc-tools 툴: Openstack 문서화 자동화를 위한 도구(sitemap생성, 문서 동기화 등)
   - 기타 파이썬 도구 : www-generator.py, conf.py 등
   - 기타 리눅스 쉘 : sync-projects.sh, publishdocs.sh
   - os-api-ref 툴: API 참조 정보 버전 및 릴리스 생성용. 즉 OpenStack API reference pages 생성 (rest_method, rest_parameters, rest_status_code, images-parameters.yaml 지정 가능). microversion selector지원함
   - whereto 툴: redirect tests in docs
   - osprofiler 툴: needed to generate osprofiler config options
   - reno: releasenotes
   - sphinx: Python으로 작성한 모듈, 클래스 그리고 함수 등의 소스코드에 대한 정적 페이지(HTML)을 자동 생성
   - jinja2: jinja2 템플리트로 부터 text-based format (HTML, XML, CSV, LaTeX, etc.) 생성
   - sphinxcontrib-actdiag: activity diagram생성
   - sphinxcontrib-seqdiag: sequence diagram생성
   - sphinxcontrib-svg2pdfconverter: svg2를 PDF로 변환
   - sphinx-feature-classification: 기능의 매트릭스를 생성
   - sphinx.ext.todo: TODO 지시자 지원
   - sphinx.ext.autodoc: docstring으로 부터 문서화 생성
   - sphinx.ext.graphviz: HTML에서는 PNG/SVG이미지, LaTex는 PDF로 렌더링 지원
  
   * 추가적인 sphinx extension은 "https://github.com/openstack/nova/blob/master/doc/source/conf.py" 참조
  
5. 소스코드 주석 표준

   - 파일 주석
     - 특별한 표준이 없슴
   - 클래스 주석
     - 특별한 표준이 없슴
   - 클래스 메소드 주석
     - case #1: description, param, returns, raise, yield, Notes, 로직 설명 등 있음
     - case #2: 특별한 양식 없이 로직 및 특기 사항 주석으로 작성됨
   - 일반 메소드 주석
     - description, param, returns, raise, yield, Notes, 로직 설명 등 있음

6. 참고 사이트

    > https://docs.openstack.org/doc-contrib-guide/index.html

Blueprints and specification (청사진 및 사양)
===============================

Openstack 문서화 팀은 대규모 변경을 유지시키기 위해 "docs-specs repository"의 spec을 이용한다. 그리고, 승인된 spec들은 "Documentation Program Specifications"으로 publish된다. 블루프린트는 트랙킹을 위하여 각 spec별로 생성되어 publish 된다.

Project guide setup (프로젝트 가이드 설정)
===================

**소스코드와 별개로 별도 문서(index.html등)로 작성해야 한다.**
  
설치 및 배포 가이드는 "openstack-manuals"의 www디렉토리에 index.html등을 작성하고 .htaccess에 해당 디렉토리를 지정해야 한다.

- 설치 가이드: 가이드를 작성하여 Openstack 표준 템플리트 기반으로 생성하여 HTML로 제공함
- 배포 가이드: 가이드를 작성하여 Openstack 표준 템플리트 기반으로 생성하여 HTML로 제공함

 설치 가이드 : doc/source/ 밑에 conf.py, index.rst, install/, contributor/, configuration/, cli/, admin/, user/, reference/, api/, releasenotes/, ovn/, feature_classification/, etc. (doc/밑에는 Makefile 및 requirements.txt가 있어야 한다)

배포 가이드 : 표준 템플리트 기반으로 configuration content, troubleshooting, or administration procedures를 포함하는 별도 deploy-guide 작성

Landing pages on docs.openstack.org (docs.openstack.org의 랜딩 페이지)
===================================

docs.openstack.org의 메인 index 페이지들은 "openstack-manuals"의 www folder에 있는 index.html의 일부이고, 템플릿 기반으로 생성이 된 것이다.

 Governance tag documentation guidelines (거버넌스 태그 문서화 지침)
 =======================================

 docs:follows-policy는 "openstack-manuals"의 작성된 내용이 실제 Openstack 문서와 일치하는 체크해 주는 기능을 제공한다.

 OpenStack API documentation (오픈스택 API 문서)
 ===========================

 가. API 참조 및 가이드는 각 프로젝트의 "api-ref/"와 "api-guide/"디렉토리로 부터 생성되어 docs.openstack.org에 만들어진다.

 - API 참조 정보(API reference info): 각 프로젝트의 "api-ref/" 디렉토리 밑에 conf.py, RST 및 YAML, inc파일로 저장된 것으로 부터 "tox -e api-ref"를 "api-ref/"디렉토리 내에서 실행하면 생성이 된다.
 - API 가이드 (API guide): 각 프로젝트의 "api-guide/" 디렉토리 밑에 conf.py, RST 및 YAML파일로 저장된 것으로 부터  "tox -e api-guide"를 "api-guide/"디렉토리 내에서 실행하면 생성이 된다.
 - API 참조 정보를 각 프로젝트의 소스코드에 어노테이션을 넣어서 넣을 수는 있지만, 실제로 어노테이션을 넣어서 만든 예제는 없다.
  
나. OpenStack의 API 참조 표준:

- Openstack working group은 모든 Openstack 개발팀이 REST API service제공할 때 따라야 할 "API documentation guidelines"을 만들어서 제공한다. 만약 프로젝트에 document가 없다면, "API documentation guidelines"내의 "suggested outline"을 참조하여 만들 수 있다.
- API 가이드는 OpenStack nova api-guide (https://opendev.org/openstack/nova/src/branch/master/api-guide)를 보고 하면 되고,
- API 참조 정보는 OpenStack nova api-ref (https://opendev.org/openstack/nova/src/branch/master/api-ref)를 보고 하면 된다.

다. API 참조 정보 버전 및 릴리스:

- 모든 API 참조 작업은 해당 프로젝트 저장소에 변경 사항이 적용되는 즉시 마스터에서 게시된다.
- API를 제공하는 OpenStack 서비스와 관련된 다양한 유형의 버전이 있습니다. 예를 들어 서비스 버전은 해당 서비스에서 제공하는 API 버전과 별개이다.
- 마이크로버전은 개별 API 메서드에 대한 소규모의 문서화된 변경 사항이다.
- 다음은 OpenStack Compute 서비스 버전 표현의 예시이다.

| 버전 구분             | 버전 내용                                   |
| --------------------- | ------------------------------------------- |
| 헤더 버전             | OpenStack-API-Version: 컴퓨팅 2.1           |
| 서비스 버전(릴리스명) | 15.0(뉴튼)                                  |
| URI 버전              | https://servers.api.openstack.org/v2.1/     |
| MIME 유형 버전        | application/vnd.openstack.compute.v2.1+json |
| 마이크로버전          | 2.6                                         |

라. OpenStack API 서비스 문서 작성법:

        1. 각 프로젝트에 "api-ref/source"디렉토리 생성
        2. conf.py 작성
           - extension에 os_api_ref, openstackdocstheme 추가 등
        3. test-requirements.txt파일에 os-api-ref항목 추가
            ```
            os-api-ref>=1.0.0 # Apache-2.0
            ```
        4. 각 작업에 대한 RST 파일 생성
        5. RST파일에 rest_method 추가
            ```
            rest_method:: GET /v2.1/{tenant_id}/flavors
            ```
        6. RST파일에 rest_parameters 추가
            ```
            .. rest_parameters:: parameters.yaml
               - tenant_id: tenant_id
            ```
        7. parameters.yaml 작성
            ```
            admin_tenant_id:
            description: |
                The UUID of the administrative project.
            in: path
            required: true
            type: string
            ```
        8.  샘플 JSON 요청 및 응답 생성 후 RST파일에 literalinclude의 옵션에 해당 파일 지정
            ```
            .. literalinclude:: samples/os-evacuate/server-evacuate-resp.json
                :language: javascript
            ```
        9.  tox.ini 수정
            ```
            [testenv:api-ref]
            # This environment is called from CI scripts to test and publish
            # the API Ref to docs.openstack.org.
            whitelist_externals = rm
            commands =
            rm -rf api-ref/build
            sphinx-build -W -b html -d api-ref/build/doctrees api-ref/source api-ref/build/html
            ```
        10. tox.ini 테스트
            ```
            $ tox -e api-ref
            ```
        11. 프로젝트에 api-ref-jobs 템플리트 추가
        12. "https://github.com/openstack/project-config"레파지토리의 "opendev.org/openstack/project-config/src/branch/master/zuul.d/projects.yaml"을 패치
            ```
            - project:
                name: openstack/nova
                queue: integrated
                templates:
                - official-openstack-repo-jobs
                - periodic-jobs-with-oslo-master
                - publish-to-pypi
                - translation-jobs-master-only
                - api-guide-jobs
                - api-ref-jobs
            ```

        [Note]

        OpenStack API 서비스 문서를 완전 신규로 작성하는 경우:
            - API 랜딩 페이지와 OpenStack Governance 참조 문서인 " projects.yaml"에서 링크를 추가해야 한다.
            - 그리고 프로젝트의 API 문서에 대한 링크를 API 랜딩 페이지에 추가하려면 openstack/api-site레파지토리의 index.rst 파일을 패치한다.
            - [openstack/governance](https://github.com/openstack/governance)레파지토리에 API 문서에 대한 올바른 링크가 있는지 확인하려면 openstack/governance레파지토리의 "reference/projects.yaml"에서 파일을 패치한다.

Writing documentation (문서 작성)
=====================

모든 기술 출판물의 일관성을 보장하기 위해 문서 기여자가 따라야 할 사항에 대해 설명한다.
글쓰기를 시작하기 전에 청사진과 사양(Blueprints and specifications)을 이해해야 한다.

문서을 작성하기 전 따라야 할 지침

- Writing style(작성 스타일)
  - 일반 글쓰기 지침 준수, 단어선택, 구둣점, 제목(일반지침, 섹션 제목, 하위 섹션 제목, 주제 제목, 그림 및 표 제목), 리스트, URLs, 숫자 및 측정 단위, OpenStack 서비스 및 프로젝트 이름, 릴리스 이름, 표준 UI 용어, 코드 규칙(명령줄 인터페이스 지침, GUI 인터페이스 지침)
- RST conventions(RST 규칙)
  - 일반 지침(라인 길이, 스페이스 및 탭 문자, 들여쓰기), 파일 이름 지정 및 구조(파일 명명 규칙, 디렉토리 구조), 제목, 인라인요소(App, 암호, 명령, 파일이름 및 경로, 용어집 항목, GUI 요소, 키보드 키 조합, 메뉴 순서, 매개변수, 옵션, 변수), 리스트(열거형 리스트, 글머리 기호 목록, 정의 리스트, 혼합 리스트), 특정 정보 블록, 코드 샘플(표준 리터럴 블록, 비표준 리터럴 블록, 원경 파일을 사용하는 리터럴 블록), 참고 자료(상호 참조, 외부 참조), 테이블(그래픽 테이블, 테이블 나열, CSV테이블, 테이블 포맷팅), Figures(특징들), 프로파일링, 코멘트, 데코레이션(수평선 추가, 새로운 라인 시작, 콘텐츠 요소 사이에 추가 공간 추가)
  - 유용한 사이트: 스핑크스 문서, reStructuredText: Docutils의 Markup Syntax and Parser Component, Quick reStructuredText
- JSON conventions(JSON 규칙)
  - 사람이 읽을 수 있도록 JSON 파일의 형식을 지정
  - 들여쓰기에는 공백 4개를 사용(Python 및 쉘 스크립트에 사용되는 OpenStack 규칙과 일치). 코드에 탭 문자를 사용하지 말고 항상 공백을 사용)
  - 이름 구분 기호(콜론) 뒤에 공백 하나를 사용
  - 공식적인 JSON 형식을 준수. 특히 문자열을 큰따옴표(작은따옴표 아님)로 묶어야 함
  - 파일을 더 쉽게 이해할 수 있도록 샘플 파일에 키 순서를 지정할 수 있다. 자동 재포맷 도구는 키 순서를 유지한다.

문서를 개인적으로 작성하여 변경사항이 랜더링되는지 확인이 가능하다.

- Topic structure(주제 구조)
  - Concept(개념)
    - 타이틀, 설명, 관련 링크, 다이아그램, 샘플예제
  - Task(태스크)
    - 타이틀, 짧은 설명, 절차, 결과, 관련 링크, 샘플예제
  - Reference(참조)
    - 타이틀 혹은 리스트, 표로 작성
- Topic tags(주제 태그)
  - [common] : 공통 디렉토리에 있는 여러 가이드의 공통 콘텐츠
  - [doc-contrib] : OpenStack 문서 기여자 가이드
  - [image-guide] : OpenStack 가상 머신 이미지 가이드
  - [install] : OpenStack 설치 가이드
  - [WIP] : 커밋이 진행 중인 작업임을 나타내는 표시
  - [www] : 웹페이지 내용
- Additional Git workflow(추가 Git 워크플로우)
  - Resolving merge conflicts(병합 충돌 해결)
    - 제출한 변경 사항에 병합 충돌이 있는 경우 git rebase 를 사용하여 수동으로 해결해야 한다.
    - rebase는 동일한 파일에서 여러 커밋이 발생할 때 충돌을 해결하기 위해 한 분기의 변경 사항을 다른 분기로 통합하는 데 사용된다.

OpenStack user experience and user interface guidelines (OpenStack 사용자 경험 및 사용자 인터페이스 지침)
=======================================================

이 섹션에서는 UX , UI 및 GUI 지침을 설명한다.

가. UX 페르소나: UX 페르소나는 디자이너와 개발자가 콘텐츠를 구축할 때 사용할 수 있는 참조 사용 사례로 만들어졌습니다.

나. GUI 지침: GUI 지침은 GUI 프로젝트에 콘텐츠를 제공하는 디자이너와 개발자를 위한 것입니다.

다. UI 텍스트 지침: UI 텍스트 지침은 OpenStack 사용자 인터페이스 내에서 콘텐츠를 제공하는 디자이너, 개발자 또는 검토자를 위한 것입니다.
    - OpenStack 페르소나
        - Adrian - 인프라 설계자
        - 레이 - 클라우드 운영자
        - Taylor - 도메인 운영자
        - 웨이 - 프로젝트 소유자
        - Quinn - 애플리케이션 개발자
        - 모델 회사
        - 페르소나를 만나보세요
        - 역할 생태계
    - GUI 지침
        - 원칙(확장 가능한 설계, 사용 사례, 필수 매개변수)
        - 패턴(취소 버튼, 모달 오류 메시지, 마법사, 페이지 오류, 작업창, 검색창, 아이콘)
        - 접근성(접근성이 적절하게 고려되도록 콘텐츠를 개발할 때 ARIA를 사용 권장)
    - UI 텍스트 지침
        - UI 텍스트 대문자 사용 지침을 따르세요.
        - 권장 UI 작업 라벨 사용
        - UI 경고 지침을 따르세요.
        - 권장 UI 패널 형식 사용
        - 주요 팁
  
RST conventions (RST 규칙)
===============

  - 일반 지침
    - 라인 길이
    - 스페이스 및 탭 문자
    - 들여쓰기)
  - 파일 이름 지정 및 구조
    - 파일 명명 규칙
    - 디렉토리 구조
  - 제목
  - 인라인요소
    - App
    - 암호
    - 명령
    - 파일이름 및 경로
    - 용어집 항목
    - GUI 요소
    - 키보드 키 조합
    - 메뉴 순서
    - 매개변수
    - 옵션
    - 변수)
  - 리스트
    - 열거형 리스트
    - 글머리 기호 목록
    - 정의 리스트
    - 혼합 리스트
  - 특정 정보 블록
  - 코드 샘플
    - 표준 리터럴 블록
    - 비표준 리터럴 블록
    - 원경 파일을 사용하는 리터럴 블록
  - 참고 자료
    - 상호 참조
    - 외부 참조
  - 테이블
    - 그래픽 테이블
    - 테이블 나열
    - CSV테이블
    - 테이블 포맷팅
  - Figures(특징들)
  - 프로파일링
  - 코멘트
  - 데코레이션
    - 수평선 추가
    - 새로운 라인 시작
    - 콘텐츠 요소 사이에 추가 공간 추가
  - 유용한 사이트
    - 스핑크스 문서
    - reStructuredText: Docutils의 Markup Syntax and Parser Component
    - Quick reStructuredText

JSON conventions (JSON 규칙)
================

  - 사람이 읽을 수 있도록 JSON 파일의 형식을 지정
  - 들여쓰기에는 공백 4개를 사용(Python 및 쉘 스크립트에 사용되는 OpenStack 규칙과 일치). 코드에 탭 문자를 사용하지 말고 항상 공백을 사용)
  - 이름 구분 기호(콜론) 뒤에 공백 하나를 사용
  - 공식적인 JSON 형식을 준수. 특히 문자열을 큰따옴표(작은따옴표 아님)로 묶어야 함
  - 파일을 더 쉽게 이해할 수 있도록 샘플 파일에 키 순서를 지정할 수 있다. 자동 재포맷 도구는 키 순서를 유지한다.
  - 예제
    ```
    {
        "uuid": "d8e02d56-2648-49a3-bf97-6be8f1204f38",
        "availability_zone": "nova",
        "hostname": "test.novalocal",
        "launch_index": 0,
        "array0": [],
        "array1": [
            "low"
        ],
        "array3": [
            "low",
            "high",
            "mid"
        ],
        "object0": {},
        "object1": {
            "value": "low",
            "role": "some"
        },
        "name": "test"
    }
    ```

Diagram guideline (다이어그램 지침)
=================

- Use diagrams sparingly (다이어그램을 아껴서 사용)
    - When to use diagrams (다이어그램을 사용하는 경우)
    - When not to use diagrams (다이어그램을 사용하지 말아야 할 경우)
- Tools (도구들)
- Use recommended file formats (권장 파일 형식 사용)
    - File names (파일 이름)
    - Other file guidelines
- General guidelines (기타 파일 지침)
    - Text format (텍스트 형식)
    - Objects (객체)
    - Object color (객체 색상)
    - Lines (라인)
    - Arrows (화살표)
- 일반 글쓰기 스타일 섹션 쓰기를 참조하여 다이어그램의 콘텐츠에도 적용한다.


Reviewing documentation (문서 검토)
=======================

   - Review guidelines (검토 지침)
     - Critique categories
       - Objective
         - 커밋 메시지(콘텐츠, 규칙, 버그 번호 및 청사진)
         - 반점 (콘텐츠, 문법, 스타일, 문구, 표현 및 대문자 사용, 철자, RST형식화, 선택한 위치의 정확도)
       - Subjective
         - 커밋 메시지(콘텐츠, 규칙, 버그 번호 및 청사진)
         - 반점 (문법, 스타일, 문구, 기술적 정확성, 기타 제안)
       - Scope (범위)
       - Consistency (일관성)
       - Tagging additional reviewers (추가 리뷰아 태그하기)
       - The waiting game (기다리는 게임)
       - Review scoring and approvals (채점 및 승인 검토)
       - Setting WorkInProgress (WIP) during review (검토 중 WIP 설정)
       - Abandoning patches (패치 포기)
     - Patches by OpenStack Proposal Bot (OpenStack 제안 봇의 패치)
     - Considerations for documentation aligned with release cycles (릴리스 주기에 따른 문서화에 대한 고려사항)
   - Repositories and core team (리포지토리 및 핵심 팀)
     - 문서화 팀은 문서화 프로젝트 하위 팀이 관리하는 여러 저장소의 핵심이다
     - 해당 하위 팀 목록은 문서 팀 구조 에서 확인할 수 있다
     - security-doc 저장소의 경우 각 패치에는 Docs 코어와 보안 코어의 승인이 필요하다는 규칙이 있다
   - Reviewing a documentation patch (문서 패치 검토하기)
     - 패치 검토를 진행하기 전에 문서 검토 지침 및 코드 검토 지침 을 주의 깊게 읽고, 완료되면 아래 단계에 따라 패치 검토를 제출한다.
   - Achieving core reviewer status (핵심 리뷰어 상태 얻기)
     - 핵심 리뷰어는 자신이 핵심 상태에 있는 프로젝트에 콘텐츠를 +2하고 병합할 수 있다.
     - 핵심 상태는 충분한 양의 리뷰를 수행했을 뿐만 아니라 해당 리뷰에 관심과 지혜를 보여준 사람에게 부여한다.
   - Core reviewer responsibilities (핵심 검토자의 책임)
     - 핵심 리뷰어가 되는 것에는 책임이 따른다.
     - 핵심 검토자가 되기 위한 일반적인 지침은 핵심 검토자 가이드 에 있습니다 . 이 섹션은 "openstack-manuals"의 핵심 검토자를 위한 것이다.
       - 일반적인 철자와 문법
       - 기술적 정확성. 가능하다면 자체 VM에서 명령을 테스트하여 명령이 정확한지 확인
       - 관련 버그 및 메일링 리스트 대화를 확인하여 고려해야 할 다른 사항이 있는지 확인
       - '이미 가지고 있는 것보다 나은가' 테스트. 차이점을 확인하거나 문서 사이트의 현재 문서를 살펴보고 변경 사항이 개선되었는지 확인. 오류가 두 개 이상인 경우 작성자가 수정할 수 있도록 인라인 수정 사항을 제공. 정말 사소한 변경 사항이 한두 가지만 있는 경우(또는 작성자가 명시적으로 편집 지원을 요청한 상황)에는 패치를 확인하고 직접 편집한다.


Building documentation (문서 작성)
======================

가. Clone a repository first (먼저 저장소를 복제)

  - Starting Work on a New Project참조하여 Clone한다.
      - https://docs.opendev.org/opendev/infra-manual/latest/developers.html#starting-work-on-a-new-project

나. Building output locally (로컬에서 출력 빌드)

  - Install dependencies for building documentation (문서 작성을 위한 종속성 설치)
    - Ubuntu 또는 Debian의 경우
        ```
        # apt-get install python-pip
        # pip install tox
        $ tox -e bindep
        # apt-get install <indicated missing package names>
        ```
  - Build workflow for openstack-manuals (openstack-manuals를 위한 워크플로 구축)
    ```
    $ tox -e docs
    ```
  - Build workflow for other repositories with documentation (문서를 사용하여 다른 리포지토리에 대한 작업 흐름 구축)
    ```
    $ tox -e docs
    ```
  - Build an existing patch locally (기존 패치를 로컬에서 빌드하기)
    - 적절한 저장소의 복제본에서 특정 패치가 포함된 로컬 분기를 만든다.
        ```
        $ git review -d PATCH_ID
        #patch link: https://review.opendev.org/#/c/PATCH_ID
        ```
    - 패치 세트의 변경 사항에 영향을 받는 문서를 작성한다
      - 자세한 내용은 openstack-manuals를 위한 빌드 워크플로 및 문서가 포함된 다른 리포지토리를 위한 빌드 워크플로우를 참조
  - Build jobs (빌드 작업)
    - 문서화를 위한 빌드 작업은 project-config 저장소에 저장된다.
    - 빌드 작업은 docs.openstack.org 및 development.openstack.org 사이트에 빌드되어 FTP를 통해 빌드된 파일을 복사한다.
    - 릴리스별 가이드는 현재 지원되는 브랜치(현재 및 이전 릴리스)용으로 작성되었으며 개발은 마스터 브랜치에서 이루어진다.
    - 지속적으로 릴리스되는 가이드는 마스터 브랜치에만 구축된다.
    - 다른 프로젝트와 마찬가지로 문서화 프로젝트에서는 패치 자동 테스트를 수행하는 여러 작업을 사용한다.
    - openstack-manuals의 경우 현재 작업은 다음과 같다.
      - openstack-tox-linters
      - build-tox-manual-publishdocs
      - build-tox-manual-publishlang
  - Publishlang job (Publishlang 작업)
    - Zanata에서 가져오기가 실패하면 가져오기가 승인되지 않는다.
    - 다른 패치가 실패하면 해당 실패가 무시될 수 있다.
    - 실패하는 경우 i18n 프로젝트에게 버그가 보고 된다.
      - https://bugs.launchpad.net/openstack-i18n
  - Building docs from end-of-life releases (릴리즈부터 문서 생성)
    - 해당 저장소의 복제본에서 원격 태그를 확인하여 각 릴리스에 대한 태그를 확인
        ```
        $ git tag -l
            2012.1
            2012.2
            2013.1.rc1
            2013.1.rc2
            2013.2
            diablo-eol
            essex-eol
            folsom-eol
            grizzly-eol
            havana-eol
            icehouse-eol
            juno-eol
            kilo-eol
            liberty-eol
        ```
    - Essex와 같이 빌드하려는 릴리스 이름을 찾고 해당 태그를 확인하세요
        ```
        $ git checkout essex-eol
        ```
    - README.rst로컬에서 문서를 작성하기 위한 전제조건에 대해서는 해당 시점에 사용 가능한 파일을 읽어라


Redirecting documentation (문서 리디렉션)
=========================

가. Adding a .htaccess file to your repo (저장소에 .htaccess 파일 추가하기)

  - "https://github.com/openstack/openstack-manuals"의 "www/"에 ".htaccess"파일 추가
  - 각 프로젝트 파일의 "doc/source/conf.py"에 다음 라인을 추가한다.
      ```
      # Add any paths that contain "extra" files, such as .htaccess or
      # robots.txt.
      html_extra_path = ['_extra']
      ```
  - 위와 같이 설정하고 저장했다면, "doc/source/_extra"디렉토리를 Redirect 나 RedirectMatch 키워드로 생성한다.
  - "doc/source/_extra/.htaccess"파일을 생성하고 리다이렉팅할 정보를 입력한다.
      ```
      redirectmatch 301 ^/nova/([^/]+)/aggregates.html$ /nova/$1/user/aggregates.html
      redirectmatch 301 ^/nova/([^/]+)/architecture.html$ /nova/$1/user/architecture.html
      redirectmatch 301 ^/nova/([^/]+)/block_device_mapping.html$ /nova/$1/user/block-device-mapping.html
      redirectmatch 301 ^/nova/([^/]+)/cells.html$ /nova/$1/user/cells.html
      redirectmatch 301 ^/nova/([^/]+)/conductor.html$ /nova/$1/user/conductor.html
      redirectmatch 301 ^/nova/([^/]+)/feature_classification.html$ /nova/$1/user/feature-classification.html
      redirectmatch 301 ^/nova/([^/]+)/filter_scheduler.html$ /nova/$1/user/filter-scheduler.html
      redirectmatch 301 ^/nova/([^/]+)/placement.html$ /nova/$1/user/placement.html
      ```

나. Enable detailed redirects for your project (프로젝트에 대한 세부 리디렉션 활성화)

- "/developer/$project/"디렉토리는 "/$project/latest/"로 옮겨졌다.
- "doc/source/_extra/.htaccess"파일이 추가되면, /developer/$project/(.*)는 "/$project/latest/$1" 리디렉션되고 나서 그것이 다시 새로운 홈페이지로 리다이렉트된다.
- 레파지토리의 해당 기능을 활성화하기 위해서는  openstack-manuals레파지토리의 "www/project-data/latest.yaml"에서 해당 프로젝트에 대하여 "has_in_tree_htaccess을 true설정한다.
    ```
    # https://github.com/openstack/openstack-manuals/blob/master/www/project-data/latest.yaml 수정

    - name: nova
    service: Compute service
    service_type: compute
    has_api_ref: true
    has_api_guide: true
    has_install_guide: true
    has_config_ref: true
    has_admin_guide: true
    has_user_guide: true
    has_in_tree_htaccess: true
    type: service
    ```
- "has_in_tree_htaccess"이 true로 설정함으로써 docs.openstack.org/developer/nova/cells.html가 새로운 홈인 docs.openstack.org/nova/latest/user/cells.html로 연결된다.
  
다. Testing your redirects (리디렉션 테스트하기)

- 리디렉션은 whereto 도구를 사용하여 테스트할 수 있다.
- build-openstack-sphinx-docs 작업은 이미 게이트에서 whereto를 사용하도록 설정되어 있으며,
- whereto가 프로젝트의 종속성 목록에 있으면 docs 빌드 작업에서 자동으로 whereto를 실행합니다.
- 따라서 리디렉션 테스트를 활성화하려면 프로젝트의 doc/requirements.txt 혹은 test-requirements.txt 파일에 ​추가하기만 하면 된다.
- 테스트 작업이 정상적으로 실행되기 위해서는 build-openstack-sphinx-docs job이 입력파일들을 찾을 수 있어야 하는데 다음과 같이 입력 파일들 지정한다.
  - 아파치 리다이텍트 파일 : build job은 아 파일이 doc/source/_extra/.htaccess이어야 한다.
  - 테스트 파일 : build job은 이 파일이 doc/test/test-redirects.txt이어야 한다.
- 그러나, 개발자의 편의를 위하여 로컬에서 실행하는 경우는 해당 프로젝트의 tox.ini에 지정해서 해도 된다.

라. Optional: Generating .htaccess files from Git (선택 사항: Git에서 .htaccess 파일 생성)

- .htaccess 파일을 Git 툴을 사용하여 생성을 할 수도 있다.
  ```
  $ git log --follow --name-status \
  --format='%H' origin/stable/ocata.. \
  -- doc/source | \
      grep ^R | \
      grep .rst | \
      cut -f2- | \
      sed -e 's|doc/source/|^/nova/([^/]+)/|' \
          -e 's|doc/source/|/nova/$1/|' \
              -e 's/.rst/.html$/' \
              -e 's/.rst/.html/' \
              -e 's/^/redirectmatch 301 /'
  ```
- 위에서 실행한 명령은 다음과 같은 Output을 생성해 준다.
  ```
    redirectmatch 301 ^/nova/([^/]+)/aggregates.html$ /nova/$1/user/aggregates.html
    redirectmatch 301 ^/nova/([^/]+)/architecture.html$ /nova/$1/user/architecture.html
    redirectmatch 301 ^/nova/([^/]+)/block_device_mapping.html$ /nova/$1/user/block-device-mapping.html
    redirectmatch 301 ^/nova/([^/]+)/cells.html$ /nova/$1/user/cells.html
    redirectmatch 301 ^/nova/([^/]+)/conductor.html$ /nova/$1/user/conductor.html
    redirectmatch 301 ^/nova/([^/]+)/feature_classification.html$ /nova/$1/user/feature-classification.html
    redirectmatch 301 ^/nova/([^/]+)/filter_scheduler.html$ /nova/$1/user/filter-scheduler.html
    redirectmatch 301 ^/nova/([^/]+)/placement.html$ /nova/$1/user/placement.html
  ```

Using documentation tools (문서 도구 사용)
=========================

- OpenStack 문서화 도구 키트에는 OpenStack 문서화 프로젝트를 유지 관리하기 위해 자동화된 작업을 수행하는 데 사용되는 여러 스크립트가 포함되어 있습니다.
- 예를 들어 사이트맵을 생성하고, 상태를 확인하고, 여러 레파지토리(저장소)에서 사용되는 파일을 동기화하는 등의 도구가 있다.

가. Scripts overview (스크립트 개요)
  - openstackdocstheme (오픈스택테마 사용)
    -  docs.openstack.org에 게시된 Sphinx 문서에 대한 테마 및 확장 지원이다.
    -  각 릴리스 시리즈에 대해 브랜치가 생성될 때 변경되는 링크를 자동으로 빌드할 수 있는 외부 링크 도우미를 제공한다.
  - oslo.config (오슬로 설정 하기)
    - oslo.config 라이브러리는 구성 문서 지시문과 구성 생성기 후크라는 두 가지 확장을 제공한다.
      - Sphinx Integration for oslo.config: show-options, options, group등 사용
        ```
        .. show-options::

            oslo.config
            oslo.log

        .. show-options::
            :split-namespaces:

            oslo.config
            oslo.log

        .. show-options::
            :config-file: etc/oslo-config-generator/glance-api.conf

        #. :oslo.config:option:`config_file`
        #. :oslo.config:option:`DEFAULT.config_file`

        :oslo.config:group:`DEFAULT`
        ```
      - Sphinx Oslo Sample Config Generation: 확장 기능을 활성화하려면 sphinx conf.py 파일에 extension 목록에 oslo_config.sphinxconfiggen을 추가해야 한다.
  - oslo.policy (오슬로 정책 사용)
    - oslo.policy 라이브러리는 정책 문서 지시문과 정책 생성기 후크라는 두 가지 확장을 제공
      - Sphinx Oslo Sample Policy Generation: policy_generator_config_file, sample_policy_basename, exclude_deprecated등 사용
        ```
        policy_generator_config_file = '../../etc/nova/nova-policy-generator.conf'
        sample_policy_basename = '_static/nova'

        =============
        Sample Policy
        =============

        Here is a sample policy file.

        .. literalinclude:: _static/nova.policy.yaml.sample
        ```
  - cliff (클리프 사용)
    - Cliff 프레임워크는 여러 명령을 문서화하는 지시문을 제공한다.
      - Sphinx Integration for cliff: Simple Example (demoapp), Advanced Example (python-openstackclient)
        ```
        # Preparation in conf.py
        extensions = ['cliff.sphinxext']

        # Directive

        .. autoprogram-cliff:: openstack.compute.v2
        :command: server add fixed ip

        .. autoprogram-cliff:: cliffdemo.main.DemoApp
        :application: cliffdemo
        
        autoprogram_cliff_application = 'my-sample-application'

        autoprogram_cliff_ignored = ['--help', '--page', '--order']
        ```

        ```
        # Advanced Example (python-openstackclient)

        [entry_points]
        console_scripts =
            openstack = openstackclient.shell:main

        openstack.compute.v2 =
            host_list = openstackclient.compute.v2.host:ListHost
            host_set = openstackclient.compute.v2.host:SetHost
            host_show = openstackclient.compute.v2.host:ShowHost

        .. autoprogram-cliff:: openstack.compute.v2
            :command: host list

        .. autoprogram-cliff:: openstack.compute.v2
            :command: host *

        .. autoprogram-cliff:: openstack.compute.v2

        autoprogram_cliff_application = 'openstack'
        ```
  - stevedore (스티브도르 사용)
    - stevedore 라이브러리는 진입점(entrypoint)에 대한 플러그인을 나열하기 위한 단일 지시어를 제공
      - Sphinx Integration for stevedore: list-plugins: detailed, overline-style, underline-style; field, plain, simple 사용

  - openstack-doc-tools repository (doc도구 레파지토리)
    -  sitemap: Generates the sitemap.xml file
       -  https://raw.githubusercontent.com/openstack/openstack-manuals/master/www/static/sitemap.xml
    -  bin:  openstack-manuals 레파지토리에 문서를 작성하기 위한 스크립트가 포함되었고 Tox 환경 내부에서 사용된다.
  - openstack-manuals repository (매뉴얼 생성기 레파지토리)
      - 현재 여러 스크립트가 openstack-manuals 저장소에 있는데, openstack-doc-tools 저장소로 통합하는 작업에 도움이 될 수 있다.
      - https://github.com/openstack/openstack-manuals
    - www-generator.py: https://docs.openstack.org/ 에 대한 정적 템플릿 기반 HTML 파일을 생성한다.
      - The template files, Defining release series, Project data file format, Template variables, Common tasks과 같은 Template generator상세는 아래 URL참조
        - https://docs.openstack.org/doc-contrib-guide/doc-tools/template-generator.html
      - Jinja2를 사용하여 템플릿을 완전한 HTML 페이지로 변환하고 이를 게시 문서 출력 디렉터리에 저장한다.
    - sync-projects.sh: 여러 저장소간 api-site and security-doc을 포함한 용어집, 공통 파일 및 일부 번역을 동기화한다.
    - publishdocs.sh: Publishdocs 작업은 이 스크립트를 사용하여 https://docs.openstack.org/ 에 문서를 게시한다.
  - Notes (참고사항)
    - 요구 사항 파일에 고정하여 저장소 전체에서 자동화가 작동할 수 있도록 openstack-doc-tools를 릴리스해야 한다.
    - 다양한 문서 저장소 간에 문서화되지 않은 동기화(자동 및 수동)가 많이 있다. 이는 문서화되어야 한다.
    - 정기적으로 실행해야 하는 여러 작업이 있다(예: sitemap.xml 파일 업데이트 및 명령줄 구성 참조 업데이트). 이는 문서화되어야 한다.
    - 일부 수동 작업은 자동화되어야 한다. 예를 들어, sitemap.xml 파일은 Gerritbot에 의해 자동으로 업데이트되어야 한다

  
나. Template generator details (템플릿 생성기 세부정보)

    Sphinx가 구축한 문서 세트로 관리되지 않는 콘텐츠를 템플리트로 만들고 템플릿 렌더링 도구를 통해 HTML로 만드는 방법을 제공하며, github의 openstack-manuals프로젝트의 www디렉토리 밑에 정의된다

    Sphinx에서 구축한 문서 세트로 관리되지 않는 docs.openstack.org의 콘텐츠 부분은 tools/www-generator.py에 있는 사용자 정의 템플릿 렌더링 도구를 통해 처리된다.

    openstack-manuals git 저장소 내에서. 스크립트는 www/project-data에 있는 YAML 데이터 파일을 읽어 특정 시리즈에 어떤 프로젝트가 존재하는지, 그리고 해당 프로젝트가 설치, 구성 및 기타 가이드 목록에 어떻게 표시되어야 하는지 결정한다.

    스크립트는 외부 프로젝트에 대한 데이터를 로드한 후 www/에서 HTML 템플릿 파일을 검색합니다.

    Jinja2를 사용하여 템플릿을 완전한 HTML 페이지로 변환하여 게시 문서 출력 디렉터리에 저장된다.

    참조: Jinja2 문서에는 템플릿 구문, 템플릿 간의 상속, 템플릿 디자이너를 위한 가이드가 포함되어 있고, 템플릿 작성 시 사용할 수 있는 다양한 필터 및 기타 기능을 제공한다.
  
  - The template files (템플릿 파일)
    - openstack-manuals 저장소의 템플릿 파일은 모두 www 디렉토리에 있습니다. 사이트에 게시하는 데 사용된 것과 동일한 구조로 구성되어 있다. 따라서 게시된 URL의 경로는 이를 생성하는 템플릿 파일의 경로와 직접적으로 일치한다.
  - Defining release series (릴리즈 시리즈 정의)
    - 릴리스 시리즈 세트와 개별 상태 및 기타 메타데이터는 SERIES_INFO 데이터 구조의 템플릿 생성기 스크립트에 포함되어 있다. 구조는 릴리스 시리즈의 이름을 메타데이터를 보유하는 SeriesInfo 구조에 매핑하는 사전이다.
  - Project data file format (프로젝트 데이터 파일 형식)
    - 각 릴리스 시리즈와 관련된 프로젝트는 www/project-data 디렉터리의 별도 YAML 파일에 나열된다. 항상 최신.yaml에 보관되는 현재 개발 중인 시리즈를 제외하고 각 파일의 이름은 시리즈(austin.yaml, bexar.yaml 등)에 따라 지정된다. 프로젝트 데이터 파일의 스키마는 www/project-data/schema.yaml에 정의되어 있다.각 파일에는 항목 배열 또는 목록이 포함되어야 합니다. 각 항목은 이름, 서비스 및 유형 속성을 정의해야 한다.
  - Template variables (펨플릿 변수)
    - 템플릿 생성기는 입력 데이터를 사용하여 템플릿 내에 표시되는 여러 변수를 설정합니다. 이를 통해 동일한 템플릿을 재사용하여 동일한 스타일의 여러 페이지에 대한 콘텐츠를 생성할 수 있고, 다양한 데이터로 채울 수 있다.
    - 관례적으로 템플릿 생성기에 정의된 모든 변수는 모두 대문자 이름을 사용합니다. 이를 통해 생성기 변수를 템플릿 내에 정의된 변수와 쉽게 구별할 수 있습니다. (예: 루프 컨텍스트)
  - Common tasks (공통 작업)
    - How would I change a page?
	- How would I add a new project?
	- How would I add a new flag to the project metadata?
	- How would I add a new page?
	- How does the final release process work?
	- Testing the build

  
다. Install OpenStack documentation tools (OpenStack 문서화 도구 설치)

문서 도구 키트를 설치하려면,

- 필수 구성 요소를 설치했는지 확인합니다.
  - https://github.com/openstack/openstack-doc-tools/blob/master/README.rst

- python-lxml을 설치하려면 배포판에 따라 다음을 실행하십시오.
    ```
    $ apt-get 설치 python-lxml
    ```
- 소스에서 빌드하려면 lxml의 종속성을 설치하십시오.
    ```
    $ apt-get 설치 libxml2-dev libxslt-dev
    ```
- openstack-doc-tools 패키지를 설치합니다:
    ```
    $ pip install openstack-doc-tools
    ```
- virtualenvwrapper가 설치되어 있는 경우 다음을 실행합니다.
    ```
    $ mkvirtualenv openstack-doc-tools
	$ pip install openstack-doc-tools
    ```
- openstack-doc-tools를 사용하려면 도구를 프로젝트로 가져옵니다.
    ```
    import os_doc_tools
    ```
라. Contribute to OpenStack documentation tools (OpenStack 문서화 도구 Contribuite하기)

  - Contribute to the tool development (도구 개발 기여)
    - openstack-doc-tools 개발에 기여하려면 다음 단계를 진행한다.
      - 개발자 가이드에 설명된 절차를 완료
      - 도구 개선 사항을 개발하는 동안 OpenStack 스타일 권고대로 따른다.
      - 변경 요청을 제출하기 전에 테스트를 실행
      - 현재 문서 도구 키트는 기본 flake8 및 bashate 테스트를 통해 테스트된다.
      - Gerrit 워크플로에 설명된 대로 Gerrit 도구를 통해 검토를 위해 변경 사항을 제출
    - "tox -e py27"을 로컬에서 성공적으로 실행하려면 로컬 test-requirements.txt 파일에 jinja2 및 markupsafe를 추가하여 로컬 가상 환경에 설치한다.

  - File a bug against the tools (도구에 대한 버그 신고)
    - 도구를 사용하는 동안 문제가 발생하면 GitHub에 문제를 제출하지 말고, openstack-doc-tools 프로젝트의 Launchpad에 버그를 신고한다.

Publishing a documentation release
==================================

 OpenStack 릴리스에 대한 문서를 게시하기 위해 완료해야 하는 작업에 대해 설명한다.
 단, 릴리즈에 대한 게시는 PTL 및 릴리스 관리자만 사용한다.

- Release tasks overview (릴리스 작업 개요)
  - 문서 릴리스를 위해 완료해야 하는 작업에 대한 개요와 각 작업을 완료할 대략적인 일정을 제공한다.
  
- Release task detail (릴리스 작업 세부정보)
  - Installation Guides testing (설치 가이트 테스트)
    - https://docs.openstack.org/latest/install/에서 확인 가능
  - Release Notes (릴리즈 노트)
    - 릴리스 노트의 출처는 이며 openstack-manuals/releasenotes/source/RELEASENAME.rst에 게시된다.
  - Update www pages for end of release (릴리즈 종료를 위한 www페이지 업데이트)
    - openstack-manuals 저장소에서 지정한다.
  - Generate the site map (사이트 맵 생성)
    - openstack-doc-tools의 sitemap 스크립트로 생성
    - 생성된 sitemap.xml은 openstack-manuals 레파지토리의 www/static 디렉토리로 복사
  - End-of-life (릴리즈 종료 처리)
    - index파일에 릴리즈 종료 메시지를 작성하고 게시한다.
  
- Releasing tools
  - openstackdocstheme, openstack-doc-tools, and os-api-ref레파지토리는 Python Packaging Index로서 패키징되어야 한다.
  - 릴리즈는 OpenStack release team에서 하지만 실제적으로는 documentation team에서 트리거해야 한다.
  - 자세한 설명은 릴리즈 관리: https://docs.openstack.org/project-team-guide/release-management.html#how-to-release
  - 릴리즈에 대한 트리거: https://github.com/openstack/releases/blob/master/README.rst


<br>

Openstack Compute (NOVA) 소스코드 문서화 분석
===========================================

* 참조 소스코드 : https://github.com/openstack/nova/blob/master

가. 소스 파일
   - Notes 등을 기술한다.
   - 예제
        ```
        """ Note on object relationships:
            1 device profile (DP) has D >= 1 request groups (just as a flavor
                has many request groups).
            Each DP request group corresponds to exactly 1 numbered request
                group (RG) in the request spec.
            Each numbered RG corresponds to exactly one resource provider (RP).
            A DP request group may request A >= 1 accelerators, and so result
                in the creation of A ARQs.
            Each ARQ corresponds to exactly 1 DP request group.

            A device profile is a dictionary:
            { "name": "mydpname",
                "uuid": <uuid>,
                "groups": [ <device_profile_request_group> ]
            }

            A device profile group is a dictionary too:
                { "resources:CUSTOM_ACCELERATOR_FPGA": "2",
                "resources:CUSTOM_LOCAL_MEMORY": "1",
                "trait:CUSTOM_INTEL_PAC_ARRIA10": "required",
                "trait:CUSTOM_FUNCTION_NAME_FALCON_GZIP_1_1": "required",
                # 0 or more Cyborg properties
                "accel:bitstream_id": "FB021995_BF21_4463_936A_02D49D4DB5E5"
            }

            See cyborg/cyborg/objects/device_profile.py for more details.
        """
        ```

나. 메소드
   - 메소드 설명, 파라메터 설명, 리턴값 설명, Raise 설명, Yield 설명
        ```
        """ Get list of profile group objects from the device profile.

            The format of the returned data structure is as below:

               {'uuid': $arq_uuid,
                'device_profile_name': $dp_name,
                'device_profile_group_id': $dp_request_group_index,
                'state': 'Bound',
                'device_rp_uuid': $resource_provider_uuid,
                'hostname': $host_nodename,
                'instance_uuid': $instance_uuid,
                'attach_handle_info': {  # PCI bdf
                    'bus': '0c', 'device': '0',
                    'domain': '0000', 'function': '0'},
                'attach_handle_type': 'PCI'
                     # or 'TEST_PCI' for Cyborg fake driver
               }

           :param dp_groups: device groups of a device profile.
           :param owner: The port UUID if the dp requested by port.
           :returns: [objects.RequestGroup]
           :raises: DeviceProfileError

           Examples:
               ....
               ....
        """
        ```

다. 클래스
   - 클래스 설명
        ```
        """Base WSGI application wrapper. Subclasses need to implement __call__."""
        ```

라. 클래스 메소드
   - Case #1: 클래스 메서드 설명 및 사용법, 예제, 특기 사항을 기술한다.
        ```
            """Create a router for the given routes.Mapper.

            Each route in `mapper` must specify a 'controller', which is a
            WSGI app to call.  You'll probably want to specify an 'action' as
            well and have your controller be an object that can route
            the request to the action-specific method.

            Examples:
                mapper = routes.Mapper()
                sc = ServerController()

                # Explicit mapping of one route to a controller+action
                mapper.connect(None, '/svrlist', controller=sc, action='list')

                # Actions are all implicitly defined
                mapper.resource('server', 'servers', controller=sc)

                # Pointing to an arbitrary WSGI app.  You can specify the
                # {path_info:.*} parameter so the target app can be handed just that
                # section of the URL.
                mapper.connect(None, '/v1.0/{path_info:.*}', controller=BlogApp())

            """
        ```
    - Case #2: 메소드 설명, 파라메터 설명, 리턴값 설명, Raise 설명, Yield 설명
        ```
            """Return the paste URLMap wrapped WSGI application.

            :param name: Name of the application to load.
            :returns: Paste URLMap object wrapping the requested application.
            :raises: `nova.exception.PasteAppNotFound`

            """
        ```

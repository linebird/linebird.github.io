---
layout: post
title: "openstack 문서화 환경 구축 및 파이프라인 구성 분석"
date: 2024-01-19 19:20:00 +0900
categories: [SCore]
tags: [openstack, sphinx]
---

## 2.1 Documentation

1) 문서화 환경 구축

   > 목표 : Openstack native 프로젝트와 동일한 방식 (기술 스택, 표준 등) 으로 문서화 할 수 있는 환경 구축
  
   - 참고사이트
  
        ```
        - https://docs.openstack.org/doc-contrib-guide/index.html
        - https://github.com/openstack/openstack-manuals
        - https://docs.openstack.org/doc-contrib-guide/doc-tools.html
        - https://docs.openstack.org/
        - https://opendev.org/openstack/nova/src/branch/master/api-guide
        - https://opendev.org/openstack/nova/src/branch/master/api-ref
        - https://raw.githubusercontent.com/openstack/openstack-manuals/master/www/static/sitemap.xml
        - https://docs.openstack.org/doc-contrib-guide/doc-tools/template-generator.html
        - https://github.com/openstack/openstack-doc-tools/blob/master/README.rst
        - https://docs.openstack.org/latest/install
        - https://docs.openstack.org/project-team-guide/release-management.html#how-to-release
        - https://github.com/openstack/releases/blob/master/README.rst
        - https://github.com/openstack/nova/blob/master
        - https://github.com/openstack/project-config
        ```
   (1) Openstack 문서화 분석 결과 정리

   - Openstack 문서 콘텐츠 구분
  
     - 콘텐츠 구분 : 
       - 문서화 도구 사용법, 문서 작성 표준 및 지침, 문서화 작성법 (설치, 배포, API 참조정보, API 가이드 등), 문서화 자동화 방법, 문서화 릴리즈(게시) 방법
     
     - 콘텐츠 종류
       - 설치 및 배포 가이드, API 가이드, API 참조 정보, UX & GUI 지침, 문서 검토, 문서 작성, 문서 표준 및 지침, 문서 리다이렉팅, 문서화 도구 사용법, 게시 및 문서 릴리즈 방법, docs.openstack.org페이지가 openstack-manuals과 일치 체크 방법, 

   - Openstack 문서화 자동화의 특징

     - 소스코드로 부터 문서를 자동 생성해서 게시하는 컨텐츠는 없다.
       - 따라서, 소스코드에 대한 Docstring작성법에 대한 자세한 지침은 없다.
       - 그러나, 각 소스코드는 "sphinx Docstring"으로 작성되어 있다.
  
     - Openstack 자체에 대한 소개, 설치, 배포 등은 "openstack-manual"에 작성하고  그 파일들로부터 HTML을 생성하여 제공한다.

     - 참조문서, API 가이드 등은 각 프로젝트별로 "api_ref, api_guide디렉토리에 소스와 별개의 RST 파일을 작성해서 그 파일들로부터 HTML을 생성하여 제공한다.
   - Openstack 주요 문서화 내용 정리
  
     - 설치 및 배포 가이드 문서화 (Installation & Deployment guides Documentation)
       - 설치 및 배포 가이드는 openstack-manuals레파이토리의 "doc/source/"디렉토리 밑에 RST, conf.py 등을 작성하여 HTML로 생성된다.
     
     - API 가이드 문서화 (API Guides Documentation)
       - 각 프로젝트의 "api-guide/" 디렉토리 밑에 conf.py, RST 및 YAML파일로 작성하여 그것으로 부터 생성된다.
       - 
     - API 참조 정보 문서화 (API Reference Info Documentation)
       - 각 프로젝트(예, Nova 프로젝트)레파지토리의 "api-ref/"디렉토리에 conf.py, RST 및 YAML, inc파일로 작성하여 그것으로 부터 생성
       - 
     - 문서 작성 표준 및 지침
       - 묺서 작성 스타일 지침
       - RST, JSON, MarkDown, 다이아그램 등 문서 표준 준수
       - 기타 사용하는 플러그인(Jinja2)들의 문서화를 위한 표준 준수
  
     - 문서 작성 방법
       - 문서 자동화를 위한 관련 레파지토리의 디렉토리, 파일들, 쉘 스크립트 사용법 설명
  
     - 문서화 작성 관련 레파지토리들
  
       > ~~~
       > 아래 레파지토리의 파일들이 유기적으로 연결되어 자동 문서화가 된다
       > ~~~

       - https://github.com/openstack/openstack-manual
         - 전체 문서화를 자동화하는 프로그램의 레파지토리
         - Sphinx가 구축한 문서 세트로 관리되지 않는 콘텐츠(openstack-manuals 프로젝트의 www디렉토리)를 템플릿으로 만들어서 HTML로 생성하는 법
         - "openstack-manuals"의 "www/"에 ".htaccess"파일 추가하고 각 프로젝트 파일의 "doc/source/conf.py"에 리다이텍팅할 정보를 등록한다.
  
       - https://docs.openstack.org/
         - 단, 메인 index페이지들은 "openstack-manuals"의 www folder에 있는 index.html의 일부이고, 템플릿 기반으로 생성된다.
         - 신규 문서 작성은 https://docs.opendev.org/opendev/infra-manual/latest/developers.html#starting-work-on-a-new-project을 참조하여 git clone으로 시작할 수 있게 한다.
         - docs.openstack.org 및 development.openstack.org 사이트에 빌드되어 FTP를 통해 빌드된 파일을 복사
  
       - 각 프로젝트(예, Nova)의 "api-ref/"와 "api-guide/" 디렉토리
         - 위의 페이지들은 자동 생성되어 docs.openstack.org으로 링크된다.
  
       - https://github.com/openstack/project-config
         - 여기에 문서화를 위한 빌드 작업 등록해야 한다.
         - opendev.org/openstack/project-config/src/branch/master/zuul.d/projects.yaml에 api-ref-jobs, api-guide-jobs 등록한다.
       - https://github.com/openstack/governance
         - reference/projects.yaml 수정한다.
  
       - https://raw.githubusercontent.com/openstack/openstack-manuals/master/www/static/sitemap.xml
         - openstack-doc-tools을 이용하여 sitemap.xml을 자동 생성한다.

   (2) 문서화 환경 구축 개발 범위 검토

   - 제공 콘텐츠 식별
     - 개발 관련 필수 제공 콘텐츠
       - 표준 및 지침 : 별도 RST 파일로 작성
         - 문서 작성 표준 지침 (추후)
           - 문서 작성 지침은 가능하면 참조 링크로 작성
         - 소스 코드 주석 지침 (OSCMP 주석 표준)
           - 소스 코드 주석 지침은 가능하면 참조 링크로 작성
       - 개발 가이드
         - 개발 가이드 작성법 : 가능하면 참조 링크로 작성
         - API 가이드
           - 소스코드에서 생성 불가능한 가이드는 별도 RST 파일로 작성 (추후)
           - 소스코드에서 생성 가능한 가이드는 소스에서 RST 파일로 생성
         - 테스트 가이드
           - 소스코드에서 생성 불가능한 가이드는 별도 RST 파일로 작성 (추후)
           - 소스코드에서 생성 가능한 가이드는 소스에서 RST 파일로 생성
       - 참조 정보 : 별도 RST 파일로 작성 (추후)
         - 참조 정보 작성법 : 가능하면 참조 링크로 작성
         - 개발 관련 참조 정보
         - 개발 도구 참조 정보
     - 시스템 관련 추가 제공 콘텐츠 (추후)
       - 프로젝트 정보(시스템 소개, 시스템 구성, 시스템 특징, 시스템 구성)
       - 시스템 아키텍쳐, 연동 아키텍처
     - 등등
  
   - ㅌㅌㅌ

2) 파이프라인 구축
   
   > 목표 : 문서를 생성하기 위한 파이프라인 구축 (sphinx, tox, jenkins)

   - 문서 생성 파이프 라인 구축

     - 수동 작업 지원
  
       - oscmp-doc-tools 및 sync-projects.sh, publishdocs.sh로 수동 생성
         - sphinx, tox, Jinja2 도구들 활용
  
        ```
       [참고]

       ○ Openstack의 수동 문서화 방법 처럼 수동 생성 방법 지원
         - Openstack은 openstack-doc-tools로 다음 문서 생성
           - sitemap 자동 생성
           - openstack-manuals레파지토리의 스크립트(tox등) 실행하여 문서 자동 생성
         - openstack-manuals레파지토리의 매뉴얼 생성기
           - www-generator.py로 "https://docs.openstack.org/"에 대한 정적 템플릿 기반 HTML 파일을 생성
           - Jinja2를 사용하여 템플릿을 완전한 HTML 페이지로 변환하고 이를 게시 문서 출력 디렉터리에 저장
           - sync-projects.sh: 여러 저장소간 api-site and security-doc을 포함하여 동기화
           - publishdocs.sh: Publishdocs 작업은 이 스크립트를 사용하여 https://docs.openstack.org/ 에 문서를 게시
       ```

    - 자동 작업 지원 : 추후 지원
  
       - jenkins에서 각 프로젝트별로 문서화 자동 생성토록 설정

            > jenkins pipeline script에서 stage로 작성하여 자동 문서화 생성


3) Swagger 자동 생성

    > 목표 : flask/pydantic swagger 자동 생성

    - flask/pydantic swagger 사용 표준 지침 작성

      - 설치 방법
      - 소스 코드 imbed 방법
      - Swagger 문서 생성 방법
      - Validate 하는 방법
  
    - flask/pydantic swagger 사용 예제 작성

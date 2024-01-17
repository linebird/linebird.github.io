---
layout: post
title: "openstack 개발범위 변경 요청"
date: 2024-01-17 16:50:00 +0900
categories: [SCore]
tags: [openstack]
---

## 1 개요

기존 개발 영역에 변동 사항 발생으로 인한 개발 범위 변경  
기존과 최대한 유사한 기술 활용이 가능한 영역으로 선정

## 2 개발 범위

하위 기 개발된 모듈에 대한 아래의 고도화 개발 수행

- oscmp-bss-project
- oscmp-bss-product
- oscmp-bss-billing
- oscmp-bss-metering
- oscmp-bss-support
- oscmp-logging-audit

### 2.1 Documentation

- Openstack native 프로젝트와 동일한 방식 (기술 스택, 표준 등) 으로 문서화 할 수 있는 환경 구축
- 문서를 생성하기 위한 파이프라인 구축 (sphinx, tox, jenkins) API Reference 문서 작성
  - 참고 : OSCMP API Spec
- flask/pydantic swagger 자동 생성
- 참고 문서
  - <https://specs.openstack.org/openstack/api-sig/guidelines/api-docs.html>
  - <https://docs.openstack.org/doc-contrib-guide/api-guides.html>
- 참고 프로젝트
  - <https://github.com/openstack/barbican>

### 2.2 Client

- API를 제공하는 서비스에 대한 openstack client 개발
- CLI command 구조 설계
- Micro versioning 지원

### 2.3 Test

- Tempest test case 작성
- 주기적 Tempest 실행 및 결과 분석/리포트
- Unit test 작성

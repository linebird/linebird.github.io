---
layout: post
title: "안전관리 - Rest API Error 보고 양식"
date: 2022-09-25 13:15:00 +0900
categories: [Blogging]
tags: [bugs]
---

## Rest API Error 보고 양식
### 0. 에러 판단 기준
> API 호출 개발자가 판단하는 에러 사유.
> 예1) 조회된 데이터에 UI에서 사용할 데이터가 없음
> 예2) 아무런 데이터가 없음
> 예3) 에러 응답이 오는데, 왜 에러인지 원인을 알 수 없음
> 예4) 기타
 
### 1. Request

#### 1.1 Request URL

> /api/v1/riskcause?param1=파라미터1&param2=파라미터2

#### 1.2 HTTP Methods

> POST/GET/PUT/DELETE

#### 1.2 Request body
> **요청시, 보내는 json 문자열. 아래의 예**

``` json
{
  "deleteYn": null,
  "firstRegistrationDTM": "20220922200909",
  "firstRegistrationUserId": "aromrom1",
  "firstRegistrationProgramId": null,
  "finalModificationDTM": "20220923111748",
  "finalModificationUserId": "aromrom1",
  "finalModificationProgramId": null,
  "originalFinalModificationDTM": null,
  "baseTenantId": null,
  "riskCauseId": "ACM-C2605ABB-1365-49EA-B0FD-03FB3FD7AF24",
  "siteId": "STI_000002",
  "siteName": "사업장 B",
  "disasterTypeCode": "003",
  "disasterType": "충돌",
  "riskCauseTitle": "bbbb",
  "declarationContent": "bbbb",
  "riskCauseDetailLocation": "bbbbaaaaaaarteetewrtewtrwetew",
  "riskCauseStateCode": "ANCP",
  "riskCauseState": "조치완료",
  "firstDeclarerId": "aromrom1",
  "firstDeclarationDTM": "20220922200909",
  "scenePhotoId": "ATG-6FE0F51A-6BA4-411B-ACC0-734CBCF75C87",
  "documentId": null,
  "measurePersonInChargeId": null,
  "measureCompletionDTM": null,
  "measureContent": "시간 확인",
  "measureResultPhotoId": "ATG-9012F960-4EA2-47CD-9F37-3FA14AA39166",
  "inquiryCountOf": 0,
  "firstDeclarerIdUserName": null,
  "firstDeclarerIdUserGlobalName": null,
  "firstDeclarerIdDepartmentName": null,
  "firstDeclarerIdDepartmentEnglishName": null,
  "firstDeclarerIdDisplayLangId": null,
  "measurePersonInChargeIdUserName": null,
  "measurePersonInChargeIdUserGlobalName": null,
  "measurePersonInChargeIdDepartmentName": null,
  "measurePersonInChargeIdDepartmentEnglishName": null,
  "measurePersonInChargeIdDisplayLangId": null,
  "finalModificationUserName": null,
  "finalModificationUserGlobalName": null,
  "finalModificationDepartmentName": null,
  "finalModificationDepartmentEnglishName": null,
  "finalModificationDisplayLangId": null,
  "firstRegistrationUserName": "아롬정보테스트",
  "measureProcYn": "N"
}
```

### 2. Response
#### 2.1 Response HTTP Status Code

> HTTP 상태 코드
> 200(OK), 201(Accept) ...

#### 2.1 Response Body(제일 중요)

> 요청 후, 응답한 json 문자열

``` json
{
  "deleteYn": null,
  "firstRegistrationDTM": "20220922200909",
  "firstRegistrationUserId": "aromrom1",
  "firstRegistrationProgramId": null,
  "finalModificationDTM": "20220923111748",
  "finalModificationUserId": "aromrom1",
  "finalModificationProgramId": null,
  "originalFinalModificationDTM": null,
  "baseTenantId": null,
  "riskCauseId": "ACM-C2605ABB-1365-49EA-B0FD-03FB3FD7AF24",
  "siteId": "STI_000002",
  "siteName": "사업장 B",
  "disasterTypeCode": "003",
  "disasterType": "충돌",
  "riskCauseTitle": "bbbb",
  "declarationContent": "bbbb",
  "riskCauseDetailLocation": "bbbbaaaaaaarteetewrtewtrwetew",
  "riskCauseStateCode": "ANCP",
  "riskCauseState": "조치완료",
  "firstDeclarerId": "aromrom1",
  "firstDeclarationDTM": "20220922200909",
  "scenePhotoId": "ATG-6FE0F51A-6BA4-411B-ACC0-734CBCF75C87",
  "documentId": null,
  "measurePersonInChargeId": null,
  "measureCompletionDTM": null,
  "measureContent": "시간 확인",
  "measureResultPhotoId": "ATG-9012F960-4EA2-47CD-9F37-3FA14AA39166",
  "inquiryCountOf": 0,
  "firstDeclarerIdUserName": null,
  "firstDeclarerIdUserGlobalName": null,
  "firstDeclarerIdDepartmentName": null,
  "firstDeclarerIdDepartmentEnglishName": null,
  "firstDeclarerIdDisplayLangId": null,
  "measurePersonInChargeIdUserName": null,
  "measurePersonInChargeIdUserGlobalName": null,
  "measurePersonInChargeIdDepartmentName": null,
  "measurePersonInChargeIdDepartmentEnglishName": null,
  "measurePersonInChargeIdDisplayLangId": null,
  "finalModificationUserName": null,
  "finalModificationUserGlobalName": null,
  "finalModificationDepartmentName": null,
  "finalModificationDepartmentEnglishName": null,
  "finalModificationDisplayLangId": null,
  "firstRegistrationUserName": "아롬정보테스트",
  "measureProcYn": "N"
}
```
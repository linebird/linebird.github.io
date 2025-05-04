---
layout: post
title: "SSL certificate 등록"
date: 2022-08-18 14:57:00 +0900
categories: [Blogging]
tags: [java, ssl]
---

# Java SSL Certificate

## 인증서 다운로드

1. openssl 로 해당 서버에 연결
    ```bash
    openssl s_client -connect www.google.com:443 | tee certlog
    [파일내용]
    ...
    Server certificate
    -----BEGIN CERTIFICATE-----
    MIIFUjCCBDqgAwIBAgIQGn0uZLvSU+4KvhWyC9XVPTANBgkqhkiG9w0BAQsFADBG
    MQswCQYDVQQGEwJVUzEiMCAGA1UEChMZR29vZ2xlIFRydXN0IFNlcnZpY2VzIExM
    QzETMBEGA1UEAxMKR1RTIENBIDFDMzAeFw0yMjA3MTgwODI1MzRaFw0yMjEwMTAw
    ODI1MzNaMBkxFzAVBgNVBAMTDnd3dy5nb29nbGUuY29tMIIBIjANBgkqhkiG9w0B
    AQEFAAOCAQ8AMIIBCgKCAQEAsUcF4W+gB+cX4SGAdh7mUi9vEB86DEwxKaQ9Xecz
    hQuLeVBUORrSJfj6z7N6VL4c509LRhKxBnql/h5spZf06Pjyp69BYaTxuKmEO/4G
    xWSUBE9qPhcs9g1iiwnrc5smPFzA9ohD6ya6Y73uenin21bzbPG7W5ACwyWISa6h
    jqt/nTu4dpAsAaUAb8NhXRqeqgdH92jhN6iWUUXmZK2hjMu9SyFG775hyHDYQkh7
    v0orwdnN5I6lB91l1EE/VbDieFBE6DUfnQmchtwKB/sdUgCl27qxfcW2ZZ6oVXXC
    h7YlR5BXnA88WawQnsyo+e8CkXN8DQOSaak8N0EXPZ60zwIDAQABo4ICZzCCAmMw
    DgYDVR0PAQH/BAQDAgWgMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAwGA1UdEwEB/wQC
    MAAwHQYDVR0OBBYEFNvUK7KUq1V/QGEKAYZMC4y/DVPRMB8GA1UdIwQYMBaAFIp0
    f6+Fze6VzT2c0OJGFPNxNR0nMGoGCCsGAQUFBwEBBF4wXDAnBggrBgEFBQcwAYYb
    aHR0cDovL29jc3AucGtpLmdvb2cvZ3RzMWMzMDEGCCsGAQUFBzAChiVodHRwOi8v
    cGtpLmdvb2cvcmVwby9jZXJ0cy9ndHMxYzMuZGVyMBkGA1UdEQQSMBCCDnd3dy5n
    b29nbGUuY29tMCEGA1UdIAQaMBgwCAYGZ4EMAQIBMAwGCisGAQQB1nkCBQMwPAYD
    VR0fBDUwMzAxoC+gLYYraHR0cDovL2NybHMucGtpLmdvb2cvZ3RzMWMzL1FxRnhi
    aTlNNDhjLmNybDCCAQQGCisGAQQB1nkCBAIEgfUEgfIA8AB2AEalVet1+pEgMLWi
    iWn0830RLEF0vv1JuIWr8vxw/m1HAAABghCgkyUAAAQDAEcwRQIgVwObboNLTG8w
    TzYR9zZGcE/8mZ6du31etmTIix8Dd18CIQCq+cpZGrdMOjPCNDAnqJ4nPKG3N8/q
    1TiHp/UMMXAWWwB2AAWcAdMg4AeEE5WASY0RfJAyZq+vclC1rztGpD4RhA1KAAAB
    ghCgkx8AAAQDAEcwRQIhAMANa79vz3uf8Nr026OOuLHr5shkY1A/y9fJegASOxom
    AiB5Aj/Xuxpu2GtBTWdLtL86Wiw4fgbFEkbCWOFLuxpYiDANBgkqhkiG9w0BAQsF
    AAOCAQEA8cbn2urTjyKxf6Po9jsHfu+RIprd+8gyukULRpd0uvna82wsaSyK5UPT
    M7LfcE7hikCOIa5afqHGiOh11o0d6rAonvAiQNTCUrQSfgsAAve7DeFA6Zj7zPsn
    Wu+GvO5E31H+R/+wTNzg1EnkeDyUTv11bEP1Bv9LlpOZGDSRZLm+dv7fva8aaSJ2
    GyQamdfDzG4dmoupOeWQwzi8CV2GS0t7nIp8D+zN2vjM0XCbsvSbwoZ9jgvpGysO
    dzE3s3ofTV2Lx7gIwLP6Hq0DEVJzMe1w7nIeSCpq4WJJFg6lpAmU4nV/XQj8R4pA
    rQGajg+lTaaAADyxMlqh7gBcLTgaUg==
    -----END CERTIFICATE-----
    ...
    ```


2. log 파일 및 Base64 Encoding 된 SSL 인증서가 certlog 파일에 저장됨
3. 해당 로그 파일(certlog)에서 "BEGIN CERTIFICATE" 와 "END CERTIFICATE" 사이의 내용만 냄겨두고 다 지움(x509 인증서 부분만 추출 )
4. 인증서로 이름바꾸기 mv certlog openapi.cer

## 인증서 추가 방법

1. openapi.cert 를 서버로 가져오자
2. cert 추가
    ```bash
    keytool -keystore /usr/java/latest/jre/lib/security/cacerts -importcert -alias openapicert -file openapi.cer
    이 인증서를 신뢰합니까? [아니오] 라고 나올 때 'y' 를 누르면 yes 가 된다.
    ```
3. cacerts 에 잘 들어갔는지 확인
    ```bash
    keytool -list -keystore /usr/java/latest/jre/lib/security/cacerts | grep openapicert
    ```

## 인증서 삭제

1. 인증서 삭제
    ```bash
    keytool -delete -alias openapicert -keystore /usr/java/latest/jre/lib/security/cacerts  -storepass changeit
    ```
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
```shell
openssl s_client -connect www.google.com:443 | tee certlog

CONNECTED(00000003)
---
Certificate chain
 0 s:/CN=www.google.com
   i:/C=US/O=Google Trust Services LLC/CN=GTS CA 1C3
 1 s:/C=US/O=Google Trust Services LLC/CN=GTS CA 1C3
   i:/C=US/O=Google Trust Services LLC/CN=GTS Root R1
 2 s:/C=US/O=Google Trust Services LLC/CN=GTS Root R1
   i:/C=BE/O=GlobalSign nv-sa/OU=Root CA/CN=GlobalSign Root CA
---
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
subject=/CN=www.google.com
issuer=/C=US/O=Google Trust Services LLC/CN=GTS CA 1C3
---
No client certificate CA names sent
Peer signing digest: SHA256
Server Temp Key: ECDH, P-256, 256 bits
---
SSL handshake has read 4904 bytes and written 415 bytes
---
New, TLSv1/SSLv3, Cipher is ECDHE-RSA-AES128-GCM-SHA256
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES128-GCM-SHA256
    Session-ID: 2D794A676E179E4E52DABD5CDECEE422059B7935C0B9860AEA418799D7569BBD
    Session-ID-ctx:
    Master-Key: 8C48FEE9F387E5181A931B342560904556BB5E2FF29BE11255105B27D3E494140270DB40FB0EB6D9CD7C2AD1EC8D4609
    Key-Arg   : None
    Krb5 Principal: None
    PSK identity: None
    PSK identity hint: None
    TLS session ticket lifetime hint: 100800 (seconds)
    TLS session ticket:
    0000 - 02 a8 f4 f6 5a 5a f7 11-94 3a 79 0c 89 43 98 1f   ....ZZ...:y..C..
    0010 - f9 8f 40 89 c0 d6 53 dd-68 0b bd 7f 87 71 bb aa   ..@...S.h....q..
    0020 - fd dd 53 1c 56 4f 1c 83-3c 73 dd f4 89 50 83 5c   ..S.VO..<s...P.\
    0030 - a8 e6 76 e3 1c a4 92 b4-52 33 dc bd 54 6a 81 e6   ..v.....R3..Tj..
    0040 - 6c 4c 70 e0 34 55 d6 cc-fe 7b 27 ae 5f a4 32 1a   lLp.4U...{'._.2.
    0050 - 31 8e 77 4b b0 c3 25 89-49 57 65 84 b2 cf 5b 14   1.wK..%.IWe...[.
    0060 - 42 e4 56 e8 94 fa 2f c8-34 ed ec 16 a9 08 97 0e   B.V.../.4.......
    0070 - 58 0e ab 08 fe e2 c8 f2-26 1a b3 45 d4 a0 f7 32   X.......&..E...2
    0080 - 82 b8 80 c5 9f 73 ff cf-25 56 b1 a7 1b 9e 31 5a   .....s..%V....1Z
    0090 - 36 47 10 97 1c 21 e3 10-ae e9 d9 3c df ac 0a e3   6G...!.....<....
    00a0 - c7 76 d1 67 25 ee 0c 73-59 2f 83 59 39 64 cc 7e   .v.g%..sY/.Y9d.~
    00b0 - 47 97 f1 c4 e2 0f d8 b8-fd 04 38 02 d8 d8 ad 2a   G.........8....*
    00c0 - 89 bf 3e 04 f3 2f e9 1d-47 65 f2 10 e0 41 43 44   ..>../..Ge...ACD
    00d0 - 19 fd 58 18 cf 4b 49 58-af 74 fc 2b 00 64 e3 d9   ..X..KIX.t.+.d..

    Start Time: 1660889322
    Timeout   : 300 (sec)
    Verify return code: 0 (ok)
---
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
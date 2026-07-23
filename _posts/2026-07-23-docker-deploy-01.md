---
layout: post
title: "도커로 배포 후, 이미지 누락 파일을 컨테이너에 파일 수동 copy 후 재기동하는 방법"
date: 2026-07-23 11:00:00 +0900
categories: [docker]
tags: [docker, deploy]
published: true
---

로컬에서 개발을 끝내고, docker로 이미지를 생성하였다. 보안 이슈로 이미지를 .tar로 말아서 운영 서버에 업로드 후, docker run을 실행하여 웹사이트를 기동했다.  
정작동하였지만, 일부 중요한 설정 파일 1개와 이미지 파일이 .tar에 포함되지 않아서, 누락된 파일을 서버로 올리고, 다시 컨테이너에 docker cp를 하여 정상적으로 보이게 되었다.

이미지(`my-web`)에 필요한 파일이 없어 매번 `docker cp`를 하는 것은 정말 번거로운 작업이었고, 이를 자동화하는 가장 효율적인 방법을 찾아 아래와 같이 기술하였다.

가장 추천하는 방법은 **1번(볼륨 마운트)** 또는 **2번(커스텀 이미지 빌드)**

---

### 방법 1: 볼륨 마운트(Bind Mount) 사용 (가장 추천)

파일을 컨테이너 내부로 "복사"하는 대신, 호스트 리눅스의 특정 폴더를 컨테이너 내부 폴더와 **실시간으로 연결**하는 방식이다. 파일을 한 번만 호스트 폴더에 넣어두면, 컨테이너를 껐다 켜도 파일이 항상 그 자리에 있게된다.

*   **명령어 예시:**
    ```bash
    # 호스트의 /home/user/web_files 폴더를 컨테이너의 /app/path 폴더로 연결
    docker run -d -p 8080:8080 \
      -v /home/user/web_files:/app/path \
      --name my-web-container my-web
    ```
*   **장점:** `cp` 명령어가 아예 필요 없으며, 호스트에서 파일을 수정하면 컨테이너에도 즉시 반영된다.

---

### 방법 2: Dockerfile로 나만의 이미지 만들기

기존 `my-web` 이미지를 기반으로, 필요한 파일을 미리 포함시킨 새로운 이미지를 빌드하는 방식이다.

1.  **Dockerfile 작성:** (파일이 있는 디렉토리에서 실행)
    ```dockerfile
    FROM my-web
    # 현재 디렉토리의 모든 파일을 컨테이너의 /app/path로 복사
    COPY . /app/path
    ```
2.  **새 이미지 빌드:**
    ```bash
    docker build -t my-web .
    ```
3.  **새 이미지로 실행:**
    ```bash
    docker run -d -p 8080:8080 my-web
    ```
*   **장점:** 이미지가 완성된 상태이므로 어디서든 바로 실행 가능하며 가장 깔끔.

---

### 방법 3: 리눅스 서비스(`systemd`) 내에서 자동화

만약 반드시 `docker cp`를 써야 하는 상황이라면, 이전에 만든 **리눅스 서비스 설정파일**의 `ExecStartPre` 단계에 복사 명령을 추가할 수 있다.

1.  **서비스 파일 수정:** `sudo nano /etc/systemd/system/my-web.service`
2.  **내용 업데이트:**
    ```ini
    [Service]
    Restart=always
    ExecStartPre=-/usr/bin/docker stop my-web-container
    ExecStartPre=-/usr/bin/docker rm my-web-container
    
    # 1. 먼저 컨테이너를 임시로 생성 (실행은 안 함)
    ExecStartPre=/usr/bin/docker create --name my-web-container -p 8080:8080 my-web
    # 2. 호스트의 파일을 컨테이너 내부로 복사
    ExecStartPre=/usr/bin/docker cp /path/to/your/files/. my-web-container:/app/path/
    
    # 3. 컨테이너 시작 (이미 생성되었으므로 start 사용)
    ExecStart=/usr/bin/docker start -a my-web-container
    ExecStop=/usr/bin/docker stop my-web-container
    ```
3.  **설정 적용:** `sudo systemctl daemon-reload` 후 재시작.
4.  **(참고)더 간단한 방법: Docker Restart Policy:**
    ```bash
    docker run -d --name my-web-container --restart always -p 8080:8080 my-web
    ```

---

### 요약 가이드

*   **파일이 자주 바뀐다:** **방법 1 (볼륨 마운트)**를 사용.
*   **파일이 고정되어 있고 배포용이다:** **방법 2 (Dockerfile)**를 사용.
*   **특수한 상황이라 꼭 복사 로직이 필요하다:** **방법 3 (서비스 등록)**을 사용.

**방법 1**이 가장 오류가 적고 리눅스 환경에서 Docker를 다루는 표준적인 방법이므로 이를 먼저 시도해 보시는 것을 권장.
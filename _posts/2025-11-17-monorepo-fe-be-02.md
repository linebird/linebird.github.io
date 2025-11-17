---
layout: post
title: "Monorepo - Frontend + Backend - 02"
date: 2025-11-17 18:30:00 +0900
categories: [Blogging]
tags: [monorepo, turborepo, react, spring]
---

## Kustomize를 사용한 환경별 배포 (dev, staging, prod) 와 Helm Chart를 통한 패키징 기능을 추가

### 📁 최종 디렉토리 구조

```bash
my-org-monorepo/
├── apps/
│   ├── web-app/            # React + Tailwind (Vite + TypeScript)
│   └── spring-backend/    # Spring Boot (Java 17 + Gradle)
├── packages/
│   ├── shared-types/       # 공통 TypeScript 타입
│   └── shared-utils/      # 공통 유틸리티 함수
├── k8s/
│   └── spring-backend/
│       └── base/          # 공통 Kubernetes 매니페스트
│       └── overlays/
│           ├── dev/
│           ├── staging/
│           └── prod/
├── helm/
│   ├── web-app/
│   └── spring-backend/
├── argocd/
│   ├── web-app/
│   └── spring-backend/
├── turbo.json
├── package.json
├── tsconfig.json
├── .gitignore
└── README.md
```

### 1. Kustomize로 환경별 배포 설정 (spring-backend)

#### 📁 기본 구조

```bash
k8s/spring-backend/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/
    │   ├── kustomization.yaml
    │   └── configmap.yaml
    ├── staging/
    │   ├── kustomization.yaml
    │   └── configmap.yaml
    └── prod/
        ├── kustomization.yaml
        └── configmap.yaml
```

#### 📄 base/deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: spring-backend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: spring-backend
  template:
    metadata:
      labels:
        app: spring-backend
    spec:
      containers:
        - name: spring-backend
          image: my-org/spring-backend:latest
          ports:
            - containerPort: 8080
```

#### 📄 base/service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: spring-backend
spec:
  selector:
    app: spring-backend
  ports:
    - port: 80
      targetPort: 8080
```

#### 📄 base/kustomization.yaml

```yaml
resources:
  - deployment.yaml
  - service.yaml
```

#### 📄 overlays/dev/kustomization.yaml

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
metadata:
  name: dev

bases:
  - ../../base

configMapGenerator:
  - name: spring-config
    literals:
      - SPRING_PROFILES_ACTIVE=dev
```

### 2. Helm Chart로 패키징 (helm/spring-backend)

```bash
mkdir -p helm/spring-backend
cd helm/spring-backend
helm create .
```
기본 helm create로 생성된 구조를 수정해 배포 설정을 맞춤화한다

#### 📄 values.yaml (간단 예시)

```yaml
image:
  repository: my-org/spring-backend
  tag: latest
  pullPolicy: IfNotPresent

service:
  port: 80
  targetPort: 8080

replicaCount: 1
```

#### 📄 templates/deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: spring-backend
  template:
    metadata:
      labels:
        app: spring-backend
    spec:
      containers:
        - name: spring-backend
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: 8080
```

### 3. ArgoCD Application 예시 (helm 기반)

```yaml
# argocd/spring-backend/spring-backend-helm-application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: spring-backend-dev
spec:
  project: default
  source:
    repoURL: https://github.com/your-org/your-monorepo.git
    targetRevision: HEAD
    path: helm/spring-backend
    helm:
      valueFiles:
        - values.yaml
        - values-dev.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: spring-dev
  syncPolicy:
    automated:
      selfHeal: true
```

### 4. Helm Chart: React 프론트엔드

```bash
mkdir -p helm/web-app
cd helm/web-app
helm create .
```

#### 📄 values.yaml

```yaml
image:
  repository: my-org/web-app
  tag: latest

service:
  port: 80
  targetPort: 3000
```

#### 📄 templates/deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
        - name: web-app
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: 3000
```

### 5. ArgoCD ApplicationSet (선택 사항)

ApplicationSet를 사용하면 여러 환경(dev, staging, prod)을 자동으로 생성 가능

```yaml
# argocd/spring-backend/applicationset.yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: spring-backend-set
spec:
  generators:
    - list:
        elements:
          - cluster: https://kubernetes.default.svc
            namespace: spring-dev
            values: dev
          - cluster: https://kubernetes.default.svc
            namespace: spring-staging
            values: staging
          - cluster: https://kubernetes.default.svc
            namespace: spring-prod
            values: prod

  template:
    spec:
      application:
        metadata:
          name: spring-backend-{{ values }}
        spec:
          source:
            repoURL: https://github.com/your-org/your-monorepo.git
            path: helm/spring-backend
            targetRevision: HEAD
            helm:
              valueFiles:
                - values-{{ values }}.yaml
          destination:
            server: {{ cluster }}
            namespace: {{ namespace }}
          syncPolicy:
            automated:
              selfHeal: true
```

###  6. Turborepo를 통한 Helm & Kustomize 작업 실행

Turborepo로 Helm과 Kustomize 작업도 실행 가능

#### 📄 turbo.json

```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", "build/libs/**"]
    },
    "lint": {
      "dependsOn": ["^lint"]
    },
    "test": {
      "dependsOn": ["^test"]
    },
    "k8s:apply": {
      "cache": false,
      "outputs": [],
      "dependsOn": ["build"],
      "inputs": ["k8s/**/*", "helm/**/*"],
      "executor": "local",
      "cli": {
        "command": "kubectl apply -k k8s/spring-backend/overlays/{{ env.TARGET_ENV }}"
      }
    },
    "helm:install": {
      "cache": false,
      "outputs": [],
      "dependsOn": ["build"],
      "inputs": ["helm/web-app/**/*", "helm/spring-backend/**/*"],
      "executor": "local",
      "cli": {
        "command": "helm upgrade --install spring-backend helm/spring-backend -f helm/spring-backend/values-{{ env.TARGET_ENV }}.yaml"
      }
    }
  }
}
```

### 7. CI/CD GitHub Actions 예시

#### .github/workflows/deploy.yml

```yaml
name: Deploy to Kubernetes with Helm and ArgoCD

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Helm
        uses: azure/setup-helm@v3

      - name: Build Spring Boot
        run: |
          cd apps/spring-backend
          ./gradlew build

      - name: Build React App
        run: |
          cd apps/web-app
          npm install
          npm run build

      - name: Build and Push Docker Image
        run: |
          docker build -t my-org/web-app:latest -f apps/web-app/Dockerfile .
          docker build -t my-org/spring-backend:latest -f apps/spring-backend/Dockerfile .
          docker push my-org/web-app:latest
          docker push my-org/spring-backend:latest

      - name: Helm Install
        run: |
          helm upgrade --install spring-backend helm/spring-backend -f helm/spring-backend/values-prod.yaml
          helm upgrade --install web-app helm/web-app -f helm/web-app/values-prod.yaml

      - name: ArgoCD Sync
        run: |
          argocd app sync spring-backend
          argocd app sync web-app
```

### 8. Dockerfile 추가

#### 📄 apps/web-app/Dockerfile

```dockerfile
FROM node:20 as build
WORKDIR /app
COPY . .
RUN npm install && npm run build
FROM nginx
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 3000
CMD ["nginx", "-g", "daemon off;"]
```

#### 📄 apps/spring-backend/Dockerfile

```dockerfile
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY build/libs/demo-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 📌 요약

이 Monorepo는 다음과 같은 기능을 포함한다:

| 기능             | 설명                                  |
| ---------------- | ------------------------------------- |
| Turborepo        | 빌드, 테스트, 린트, 테스트 자동화     |
| React + Tailwind | Vite + TypeScript 기반 프론트엔드     |
| Spring Boot      | Java 17 + Gradle 기반 백엔드          |
| Kustomize        | 환경별 배포 설정 (dev, staging, prod) |
| Helm             | 앱 단위 패키징 및 버전 관리           |
| ArgoCD           | GitOps 기반 자동 배포                 |
| GitHub Actions   | CI/CD 파이프라인 자동화               |

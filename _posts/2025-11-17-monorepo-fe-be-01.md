---
layout: post
title: "Monorepo - Frontend + Backend - 01"
date: 2025-11-17 18:00:00 +0900
categories: [Blogging]
tags: [monorepo, turborepo, react, spring]
---

## Backend와 Frontend를 함께 구성한 Monorepo

React + Tailwind CSS (Vite 기반) + Spring Boot (Java 17) + Turborepo로 구성된 실제 예제 구현 목표

- React + Tailwind 프론트엔드 (Vite + TypeScript)
- Spring Boot 백엔드 (Gradle + Java 17)
- 공통 타입 정의 (shared-types)
- 공통 유틸리티 (TypeScript 기반)
- Turborepo를 이용한 통합 빌드/테스트/배포
- Kubernetes (k8s) + ArgoCD 기반 CI/CD 환경 적용

### 전체 프로젝트 구조

```bash
my-monorepo/
├── apps/
│   ├── web-app/           # React + Tailwind (Vite 기반)
│   └── spring-backend/   # Spring Boot 백엔드 (Java 17)
├── packages/
│   ├── shared-types/      # 공통 TypeScript 타입
│   └── shared-utils/     # 공통 유틸리티
├── k8s/
│   ├── web-app/
│   └── spring-backend/
├── argocd/
│   ├── web-app/
│   └── spring-backend/
├── turbo.json
├── package.json
├── tsconfig.json
└── .gitignore
```

### 1. 루트 package.json

```json
{
  "name": "my-monorepo",
  "private": true,
  "version": "0.0.0",
  "workspaces": {
    "packages": ["apps/*", "packages/*"]
  },
  "scripts": {
    "dev": "turbo run dev",
    "build": "turbo run build",
    "lint": "turbo run lint",
    "test": "turbo run test"
  },
  "devDependencies": {
    "turbo": "^2.0.0"
  }
}
```

### 2. React 프론트엔드 (apps/web-app)

```bash
mkdir -p apps/web-app
cd apps/web-app
npm create vite@latest . -- --template react-ts
npm install
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

#### tailwind.config.js

```js
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
    "../../packages/shared-ui/src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

#### vite.config.ts

```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tsconfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  plugins: [react(), tsconfigPaths()],
})
```

### 3. Spring Boot 백엔드 (apps/spring-backend)

```bash
mkdir -p apps/spring-backend
cd apps/spring-backend
curl https://start.spring.io/starter.tgz \
  -d dependencies=web \
  -d type=gradle-project \
  -d language=java \
  -d javaVersion=17 \
  -d baseDir=spring-backend \
  | tar -xzvf -
```

#### build.gradle 일부 수정

```groovy
plugins {
    id 'org.springframework.boot' version '3.3.5'
    id 'io.spring.dependency-management' version '1.1.3'
    id 'java'
}
```

#### 예제 컨트롤러

```java
// apps/spring-backend/src/main/java/com/myorg/demo/User.java
public record User(String id, String name, String email) {}

// apps/spring-backend/src/main/java/com/myorg/demo/UserController.java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable String id) {
        return new User("1", "Alice", "alice@example.com");
    }
}
```

### 4. 공통 패키지

#### 📁 packages/shared-types

```bash
mkdir -p packages/shared-types
cd packages/shared-types
npm init -y
```
```ts
// packages/shared-types/src/user.type.ts
export interface User {
  id: string;
  name: string;
  email: string;
}
```

#### 📁 packages/shared-utils

```bash
mkdir -p packages/shared-utils
cd packages/shared-utils
npm init -y
```
```ts
// packages/shared-utils/src/apiClient.ts
export const apiClient = {
  fetchUser: async (id: string): Promise<User> => {
    const res = await fetch('http://spring-backend:8080/api/users/1');
    return await res.json();
  }
};
```

### 5. tsconfig.json (루트)

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@my-org/shared-types": ["packages/shared-types/src"],
      "@my-org/shared-utils": ["packages/shared-utils/src"]
    }
  },
  "include": ["apps/**/*", "packages/**/*"]
}
```

### 6. turbo.json (Turborepo 설정)

```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", "build/libs/**"]
    },
    "dev": {
      "cache": false
    },
    "lint": {
      "dependsOn": ["^lint"],
      "inputs": [".eslintrc.js", "src/**/*"]
    },
    "test": {
      "dependsOn": ["^test"],
      "inputs": ["src/**/*"]
    }
  }
}
```

### 7. k8s 디렉토리 구조

#### 📁 k8s/web-app

```bash
mkdir -p k8s/web-app
```
```yaml
# k8s/web-app/web-app-deployment.yaml
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
          image: my-org/web-app:latest
          ports:
            - containerPort: 3000
```
```yaml
# k8s/web-app/web-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app
spec:
  selector:
    app: web-app
  ports:
    - port: 80
      targetPort: 3000
```

#### k8s/spring-backend

```bash
mkdir -p k8s/spring-backend
```
```yaml
# k8s/spring-backend/spring-backend-deployment.yaml
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
```yaml
# k8s/spring-backend/spring-backend-service.yaml
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

### 8. ArgoCD 디렉토리 구조

ArgoCD는 Application 리소스를 통해 Kubernetes에 배포를 자동화한다

#### 📁 argocd/web-app

```yaml
# argocd/web-app/web-app-application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: web-app
spec:
  project: default
  source:
    repoURL: https://github.com/your-org/your-monorepo.git
    targetRevision: HEAD
    path: k8s/web-app
  destination:
    server: https://kubernetes.default.svc
    namespace: web-app
  syncPolicy:
    automated:
      selfHeal: true
```

#### 📁 argocd/spring-backend

```yaml
# argocd/spring-backend/spring-backend-application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: spring-backend
spec:
  project: default
  source:
    repoURL: https://github.com/your-org/your-monorepo.git
    targetRevision: HEAD
    path: k8s/spring-backend
  destination:
    server: https://kubernetes.default.svc
    namespace: spring-backend
  syncPolicy:
    automated:
      selfHeal: true
```

### 9. 예제 코드 사용

#### React 앱에서 Spring Boot API 호출

```tsx
// apps/web-app/src/App.tsx
import { useEffect, useState } from 'react';
import { User } from '@my-org/shared-types';
import { apiClient } from '@my-org/shared-utils';

function App() {
  const [user, setUser] = useState<User | null>(null);

  useEffect(() => {
    apiClient.fetchUser('1').then(setUser);
  }, []);

  return (
    <div className="p-4">
      <h1 className="text-2xl font-bold">User Info</h1>
      {user && (
        <div>
          <p>ID: {user.id}</p>
          <p>Name: {user.name}</p>
          <p>Email: {user.email}</p>
        </div>
      )}
    </div>
  );
}
```

### 10. CI/CD 파이프라인 구성 (예: GitHub Actions)

#### 📁 .github/workflows/deploy.yml

```yaml
name: Deploy to Kubernetes via ArgoCD

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Build Docker Images
        run: |
          cd apps/web-app
          npm install
          npm run build

          cd ../spring-backend
          ./gradlew build

      - name: Deploy to Kubernetes
        uses: azure/k8s-deploy@v1
        with:
          namespace: default
          manifests: |
            k8s/web-app
            k8s/spring-backend
          images: |
            my-org/web-app:latest
            my-org/spring-backend:latest
```

### 11. ArgoCD 설치 및 사용법

1. ArgoCD 설치 (Helm)
   ```bash
   helm repo add argo https://argoproj.github.io/argo-helm
   helm install argocd argo/argo-cd --namespace argocd --create-namespace
   ```
2. Application CRD 적용
   ```bash
   kubectl apply -f argocd/web-app/web-app-application.yaml
   kubectl apply -f argocd/spring-backend/spring-backend-application.yaml
   ```
3. ArgoCD UI에서 Application 상태 확인
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```

브라우저에서 https://localhost:8080 접속 후 로그인 (초기 비밀번호는 argocd-admin secret에서 확인)

### 12. 실행 및 배포 흐름

1. 코드 수정 후 GitHub에 푸시
2. GitHub Actions에서 Docker 이미지 빌드 및 푸시
3. ArgoCD가 변경 감지 후 Kubernetes에 자동 배포


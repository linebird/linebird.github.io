---
layout: post
title: "Monorepo - Turborepo"
date: 2025-11-11 18:40:00 +0900
categories: [Blogging]
tags: [react, monorepo, turborepo]
---

## Next.js + Tailwind CSS + Turborepo 기반의 실제 예제

Next.js + Tailwind CSS + Turborepo 기반의 실제 예제 Monorepo 프로젝트 구조. 이 구조는 여러 앱과 공통 패키지(예: UI 컴포넌트, 유틸리티, 타입)를 하나의 저장소에서 관리하면서, 각 앱은 독립적으로 빌드/테스트/배포가 가능하도록 설계.

### 예제 프로젝트

#### 📁 기본 디렉토리 구조

```bash
my-org-monorepo/
├── apps/
│   ├── web-app/              # 사용자용 Next.js 앱
│   └── admin-app/            # 관리자용 Next.js 앱
├── packages/
│   ├── shared-ui/            # Tailwind 기반 공통 React 컴포넌트
│   ├── shared-utils/         # 공통 유틸리티 함수
│   ├── shared-types/         # TypeScript 공통 타입/인터페이스
│   └── tailwind-config/     # 공통 Tailwind 설정
├── turbo.json                # Turborepo 설정 파일
├── package.json              # 루트 패키지 파일
├── tsconfig.json             # 루트 tsconfig
└── .gitignore
```

### 1. 프로젝트 생성

#### 📦 루트 package.json 생성

```bash
mkdir my-org-monorepo
cd my-org-monorepo
npm init -y
```

```json
{
  "name": "my-org-monorepo",
  "private": true,
  "version": "0.0.0",
  "workspaces": {
    "packages": ["apps/*", "packages/*"]
  }
}
```

#### 🧱 Turborepo 초기화

```bash
npx create-turbo@latest
```
이 명령어는 위 구조를 자동으로 생성해주며 기본적인 CI/CD 및 작업 병렬화 설정도 함께 해주게 됨

### 2. Tailwind 공통 설정 (공유 가능한 tailwind.config.js)

```bash
mkdir -p packages/tailwind-config
cd packages/tailwind-config
npm init -y
```

```json
{
  "name": "@my-org/tailwind-config",
  "version": "0.1.0",
  "main": "index.js"
}
```

```javascript
// packages/tailwind-config/tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#3B82F6',     // blue-500
        secondary: '#10B981',  // green-500
      },
    },
  },
  plugins: [],
};
```

#### 🧱 공통 UI 패키지 (shared-ui)

```bash
mkdir -p packages/shared-ui
cd packages/shared-ui
npm init -y
```
```json
{
  "name": "@my-org/shared-ui",
  "version": "0.1.0",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "scripts": {
    "build": "tsc"
  },
  "devDependencies": {
    "typescript": "^5.0.0"
  }
}
```
```typescript
// packages/shared-ui/src/Button.tsx
import React from 'react';

export const Button = ({ children, className }) => {
  return (
    <button className={`bg-primary text-white px-4 py-2 rounded ${className}`}>
      {children}
    </button>
  );
};
```
빌드 후 dist/에 컴파일된 파일이 생성됨

### 3. Next.js 앱 생성 (web-app)

```bash
mkdir -p apps/web-app
cd apps/web-app
npx create-next-app . --ts --eslint --tailwind --app --src
```
이 명령어는 src 디렉토리와 app 디렉토리 구조를 사용하는 Next.js 13+ 앱을 생성한다
```js
// apps/web-app/tailwind.config.js
const sharedTailwindConfig = require('../tailwind-config/tailwind.config');

module.exports = {
  ...sharedTailwindConfig,
  content: [
    './src/**/*.{js,jsx,ts,tsx}',
    '../../packages/shared-ui/src/**/*.{js,jsx,ts,tsx}',
  ],
};
```

### 4. 공통 패키지 참조 설정 (tsconfig.json)

루트 tsconfig.json에 다음 내용을 추가한다:
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@my-org/shared-ui": ["packages/shared-ui/src/index.ts"],
      "@my-org/shared-utils": ["packages/shared-utils/src/index.ts"],
      "@my-org/shared-types": ["packages/shared-types/src/index.ts"]
    }
  },
  "include": ["apps/**/*", "packages/**/*"]
}
```

앱 내에서 사용 예시:
```tsx
// apps/web-app/src/app/page.tsx
import { Button } from '@my-org/shared-ui';

export default function Home() {
  return <Button>Click Me</Button>;
}
```

###  5. 두 번째 앱 생성 (admin-app)

```bash
mkdir -p apps/admin-app
cd apps/admin-app
npx create-next-app . --ts --eslint --tailwind --app --src
```
ailwind 설정도 동일하게 공통 tailwind-config를 사용하도록 한다.

### 6. 루트 turbo.json 설정

```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**"]
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
이 설정을 통해 변경된 앱만 빌드/테스트할 수 있다.

### 7. 추가 패키지 예시

#### 📁 shared-utils

```bash
mkdir -p packages/shared-utils
cd packages/shared-utils
npm init -y
```

```ts
// packages/shared-utils/src/format.ts
export const formatDate = (date: Date): string => {
  return date.toLocaleDateString();
};
```
앱에서 사용:
```ts
import { formatDate } from '@my-org/shared-utils';
```

#### 📁 shared-types

```bash
mkdir -p packages/shared-types
cd packages/shared-types
npm init -y
```
```ts
// packages/shared-types/src/user.ts
export interface User {
  id: string;
  name: string;
  email: string;
}
```

### 8. 실제 앱 내에서 사용 예시

```tsx
// apps/web-app/src/app/page.tsx
import { Button } from '@my-org/shared-ui';
import { formatDate } from '@my-org/shared-utils';
import { User } from '@my-org/shared-types';

export default function Home() {
  const user: User = { id: '1', name: 'Alice', email: 'alice@example.com' };
  return (
    <div>
      <Button>Click Me</Button>
      <p>Welcome, {user.name}! Today is {formatDate(new Date())}</p>
    </div>
  );
}
```

### 9. 빌드 및 실행

#### 🧱 빌드

```bash
turbo run build
```
이 명령어는 apps와 packages 내 필요한 모든 프로젝트를 빌드한다.

#### 🧪 테스트

```bash
turbo run test
```
각 앱과 패키지에 설정된 테스트 스크립트가 실행된다.

#### 🧰 린트

```bash
turbo run lint
```

### 10. 배포 전략

각 앱은 독립적으로 배포된다. 예를 들어:
```bash
cd apps/web-app
npm run build
npm run start
```
CI/CD에서는 turbo를 활용해 변경된 앱만 빌드/배포 가능:
```bash
turbo run build --filter=web-app
```

### 11. Monorepo의 이점 (React + Tailwind + Turborepo)

| 기능               | 이점                                         |
| ------------------ | -------------------------------------------- |
| 공통 Tailwind 설정 | 모든 앱에서 동일한 테마, 컬러, 유틸리티 사용 |
| 공통 UI 컴포넌트   | shared-ui를 통해 디자인 시스템 통일          |
| 공통 유틸리티/타입 | 코드 중복 제거, 타입 안정성 향상             |
| 변경 기반 빌드     | turbo로 변경된 앱/패키지만 빌드 가능         |
| 빠른 빌드 캐싱     | Turborepo의 캐싱 기능으로 반복 빌드 최소화   |

### 12. 패키지 NPM 배포 (선택 사항)

공통 패키지를 외부에서 사용하려면 NPM에 배포 가능
```bash
cd packages/shared-ui
npm publish --access public
```
이후 다른 프로젝트에서:
```bash
npm install @my-org/shared-ui
```

### 📌 요약

이 Monorepo 구조는 다음과 같은 장점을 가진다:

- 여러 Next.js 앱에서 공통 Tailwind 설정 사용 가능
- 공통 UI 라이브러리를 여러 앱에서 import 가능
- Turborepo로 빠른 빌드, 병렬 실행, 변경 기반 작업 수행
- TypeScript path alias를 통해 코드 가독성 및 유지보수성 향상
- CI/CD 최적화로 효율적인 빌드 및 테스트 가능

### 추가 조사

- eslint + prettier 통합 설정
- husky + lint-staged로 커밋 시 lint
- changeset으로 버전 관리 및 배포 자동화
- Storybook으로 공통 UI 컴포넌트 문서화

---
layout: post
title: "FrontEnd Architecture 분석 - 01"
date: 2025-12-04 10:30:00 +0900
categories: [Blogging]
tags: [react, next, monorepo]
---

## 기 개발된 FE 아키텍쳐 기술 분석

분석하려는 어플리케이션은 pnpm 워크스페이스 기반 모노레포 구조의 react 어플리케이션 작성되었음.  

사용된 기술 중에서 react, nextjs, turborepo(pnpm)에 대해 요약하려고 함

- react : A JavaScript library for building user interfaces (by Meta)
- nextjs : The React Framework for the Web (by Vercel)
- turborepo : Turborepo is the build system for JavaScript and TypeScript codebases (by Vercel)

## React

React는 대화형 사용자 인터페이스를 구축하기 위한 JavaScript 라이브러리  
사용자 인터페이스(UI)란 사용자가 화면에서 보고 상호작용하는 요소를 의미한다  
![UI Example](https://nextjs.org/_next/image?url=https%3A%2F%2Fh8DxKfmAPhn8O0p3.public.blob.vercel-storage.com%2Flearn%2Flight%2Flearn-react-components.png&w=1920&q=75)
> 라이브러리란 React가 UI를 구축하는 데 도움이 되는 함수(API)를 제공하지만, 애플리케이션에서 해당 함수를 어디에서 사용할지는 개발자에게 맡긴다는 의미

### UI 변경을 위한 Javascript와 react 사용 비교

<iframe width="100%" height="300" src="//jsfiddle.net/linebird/4qoac2yh/4/embedded/" frameborder="1" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

### React core concepts

React 애플리케이션 개발을 시작하려면 React의 세 가지 핵심 개념을 숙지해야 하며, 아래와 같다.

- Components
- Props
- State

### Components

### Props

### State

#### TanStack Query

#### Zustand

## Next.js

Next.js는 웹 애플리케이션을 만드는 데 필요한 구성 요소를 제공하는 React 프레임워크  

프레임워크란 Next.js가 React에 필요한 도구와 구성을 처리하고 애플리케이션에 대한 추가적인 구조, 기능 및 최적화를 제공한다는 것을 의미함.

![Next.js 기능 제공을 보여주는 다이어그램](https://nextjs.org/_next/image?url=https%3A%2F%2Fh8DxKfmAPhn8O0p3.public.blob.vercel-storage.com%2Flearn%2Flight%2Flearn-ecosystem.png&w=1920&q=75)
> React를 사용하여 UI를 구축한 다음 Next.js 기능을 점진적으로 도입하여 라우팅, 데이터 페칭, 캐싱과 같은 일반적인 애플리케이션 요구 사항을 해결 가능


## Turborepo

Turborepo는 JavaScript 및 TypeScript 코드베이스를 위한 고성능 빌드 시스템. 모노리포 확장을 위해 설계되었으며, 단일 패키지 작업 공간에서의 워크플로를 빠르게 작성 가능.
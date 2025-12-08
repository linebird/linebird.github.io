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

아래의 html에서 h1 태그를 프로젝트에 추가하는 것을 예제로 제공한다
```html
<html>
  <body>
    <div></div>
  </body>
</html>
```


#### Javascript를 사용하여 H1 추가

<iframe width="100%" height="300" src="//jsfiddle.net/linebird/4qoac2yh/4/embedded/" frameborder="1" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

1. 나중에 타겟팅할 수 있도록 div에 고유한 ID를 부여
2. HTML 파일 내부에 JavaScript를 작성하기 위한 스크립트 태그를 추가
3. 스크립트 태그 내부에서 DOM 메서드인 getElementById()를 사용하여 `<div>` 요소를 해당 id로 선택
4. DOM 메서드를 계속 사용하여 새로운 `<h1>` 요소를 생성
5. 브라우져에서 확인

일반 JavaScript로 DOM을 업데이트하는 것은 매우 강력하지만 번거롭다. `<h1>` 요소에 텍스트를 추가하기 위해 다음과 같은 코드를 작성했다.
```html
<script type="text/javascript">
  const app = document.getElementById('app');
  const header = document.createElement('h1');
  const text = 'Develop. Preview. Ship.';
  const headerContent = document.createTextNode(text);
  header.appendChild(headerContent);
  app.appendChild(header);
</script>
```
앱이나 팀의 규모가 커짐에 따라 위와 같은 방식으로 애플리케이션을 구축하는 것이 점점 더 어려워질 수 있다.

#### react를 사용하여 H1 추가

<iframe width="100%" height="300" src="//jsfiddle.net/linebird/4qoac2yh/11/embedded/" frameborder="1" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

1. 프로젝트에서 React를 사용하려면 unpkg.com이라는 외부 웹사이트에서 두 개의 React 스크립트를 로드
2. ReactDOM.createRoot() 메서드를 추가하여 특정 DOM 요소를 대상으로 하고 React 구성 요소를 표시할 루트를 생성
3. root.render() 메서드를 추가하여 React 코드를 DOM에 렌더링

이렇게 하면 React가 #app 요소 내부에 `<h1>` 제목을 렌더링하게 된다.  
브라우저는 JSX를 기본적으로 이해하지 못하므로 Babel과 같은 JavaScript 컴파일러가 필요하여 JSX 코드를 일반 JavaScript로 변환해야 한다

### React core concepts

React 애플리케이션 개발을 시작하려면 React의 세 가지 핵심 개념을 숙지해야 하며, 아래와 같다.

- Components
- Props
- State

### Components

사용자 인터페이스는 구성 요소라는 더 작은 구성 요소로 나눌 수 있다.  

컴포넌트를 사용하면 독립적이며 재사용 가능한 코드 단위를 만들 수 있다. 컴포넌트를 레고 블록에 비유하면, 각 블록을 조합해 더 큰 구조를 만들어갈 수 있다. UI의 일부를 수정해야 할 때도 필요한 컴포넌트(또는 블록)만 변경하면 된다.

![이미지, 텍스트, 버튼 등 3개의 작은 구성요소로 구성된 미디어 구성요소의 예](https://nextjs.org/_next/image?url=https%3A%2F%2Fh8DxKfmAPhn8O0p3.public.blob.vercel-storage.com%2Flearn%2Flight%2Flearn-components.png&w=1920&q=75)
> 위의 Media Component는 Image, Text, Button의 3개 컴포넌트로 구성된 컴포넌트이다

#### Component 생성 방법 예시

1. React에서 컴포넌트는 함수이다. 스크립트 태그 안에 header라는 새 함수를 만든다.
    ```html
    <script type="text/jsx">
      const app = document.getElementById('app');
    
      function header() {}
    
      const root = ReactDOM.createRoot(app);
      root.render(<h1>Develop. Preview. Ship.</h1>);
    </script>
    ```
2. 컴포넌트는 UI 요소를 반환하는 함수이다. 함수의 return 문 내부에 JSX를 작성할 수 있다
    ```html
    <script type="text/jsx">
      const app = document.getElementById('app');
    
      function header() {
        return <h1>Develop. Preview. Ship.</h1>;
      }
    
      const root = ReactDOM.createRoot(app);
      root.render(<h1>Develop. Preview. Ship.</h1>);
    </script>
    ```
3. 이 구성 요소를 DOM에 렌더링하려면 root.render() 메서드의 첫 번째 인수로 전달
    ```html
    <script type="text/jsx">
      const app = document.getElementById('app');
    
      function header() {
        return <h1>Develop. Preview. Ship.</h1>;
      }
    
      const root = ReactDOM.createRoot(app);
      root.render(header);
    </script>
    ```
    위 코드를 브라우저에서 실행하면 오류가 발생한다. 이 문제를 해결하려면 두 가지 작업을 수행해야 한다.
    1. 첫째, React 구성 요소는 일반 HTML 및 JavaScript와 구별하기 위해 대문자로 시작해야 한다.
        ```html
        function Header() {
          return <h1>Develop. Preview. Ship.</h1>;
        }
        
        const root = ReactDOM.createRoot(app);
        // Capitalize the React Component
        root.render(Header);
        ```
    2. 두 번째로, React 구성 요소는 일반 HTML 태그와 같은 방식, 즉 꺾쇠괄호 <>를 사용하여 사용한다.
        ```html
        function Header() {
          return <h1>Develop. Preview. Ship.</h1>;
        }
        
        const root = ReactDOM.createRoot(app);
        root.render(<Header />);
        ```
#### Nesting components
애플리케이션은 일반적으로 단일 컴포넌트보다 더 많은 콘텐츠를 포함하게 된다. 일반 HTML 요소처럼 React 컴포넌트를 서로 중첩할 수 있다.  

ㅇㅇㅇㅇ

```html
function Header() {
  return <h1>Develop. Preview. Ship.</h1>;
}
 
function HomePage() {
  return (
    <div>
      {/* Nesting the Header component */}
      <Header />
    </div>
  );
}
 
const root = ReactDOM.createRoot(app);
root.render(<Header />);
```

#### Component trees
React 구성 요소를 중첩하여 구성 요소 트리를 형성할 수 있다.
![Component trees](https://nextjs.org/_next/image?url=https%3A%2F%2Fh8DxKfmAPhn8O0p3.public.blob.vercel-storage.com%2Flearn%2Flight%2Flearn-component-tree.png&w=1920&q=75)
예를 들어, 최상위 HomePage 컴포넌트는 Header, Article, Footer 컴포넌트를 포함할 수 있다. 그리고 각 컴포넌트는 다시 자체 자식 컴포넌트를 가질 수 있다. Header 컴포넌트는 Logo, Title, Navigation 컴포넌트를 포함한다.  

이 모듈형 형식을 사용하면 앱 내부의 다양한 위치에서 구성 요소를 재사용할 수 있다.

프로젝트에서 <HomePage>가 최상위 구성 요소가 되었으므로 root.render() 메서드에 전달할 수 있다.
```html
function Header() {
  return <h1>Develop. Preview. Ship.</h1>;
}
 
function HomePage() {
  return (
    <div>
      <Header />
    </div>
  );
}
 
const root = ReactDOM.createRoot(app);
root.render(<HomePage />);
```

### Props

리액트에서는 하나의 컴포넌트에서 다른 컴포넌트로 데이터를 전달할 때 props를 사용한다(부모 컴포넌트에서 자식 컴포넌트(들)로). Props는 properties를 줄인 말. 앱에서 데이터 흐름을 동적으로 만들고 싶을 때 유용

참조
- [컴포넌트에 props 전달하기](https://ko.react.dev/learn/passing-props-to-a-component)

### State

state는 React 컴포넌트 내부에서 동적인 데이터를 관리하기 위해 사용하는 객체
- 상태는 React 컴포넌트의 현재 상태를 나타내며, 데이터가 변경될 때 UI를 다시 렌더링하도록 트리거한다
- 함수형 컴포넌트에서는 Hooks(useState)를 사용하여 상태를 관리하며, 클래스형 컴포넌트에서는 this.state를 사용

[참조]
- [State 관리하기](https://ko.react.dev/learn/managing-state)

#### TanStack Query

#### Zustand

## Next.js

Next.js는 웹 애플리케이션을 만드는 데 필요한 구성 요소를 제공하는 React 프레임워크  

프레임워크란 Next.js가 React에 필요한 도구와 구성을 처리하고 애플리케이션에 대한 추가적인 구조, 기능 및 최적화를 제공한다는 것을 의미함.

![Next.js 기능 제공을 보여주는 다이어그램](https://nextjs.org/_next/image?url=https%3A%2F%2Fh8DxKfmAPhn8O0p3.public.blob.vercel-storage.com%2Flearn%2Flight%2Flearn-ecosystem.png&w=1920&q=75)
> React를 사용하여 UI를 구축한 다음 Next.js 기능을 점진적으로 도입하여 라우팅, 데이터 페칭, 캐싱과 같은 일반적인 애플리케이션 요구 사항을 해결 가능


## Turborepo

Turborepo는 JavaScript 및 TypeScript 코드베이스를 위한 고성능 빌드 시스템. 모노레포 확장을 위해 설계되었으며, 단일 패키지 작업 공간에서의 워크플로를 빠르게 작성 가능.

### Monorepo?

![monolith vs multi-repo vs mono-repo](https://engineering.linecorp.com/wp-content/uploads/2022/04/turborepo1.png)

단일 레포지터리에 여러개의 서브 프로젝트가 존재하는 방식

모노레포는 이와 같이 모놀리식의 장점과 멀티레포의 단점을 보완하는 구조라고 볼 수 있다.

- 더 쉬운 프로젝트 생성 (아래와 같은 과정을 모든 프로젝트에서 거치지 않아도 된다.)
  - 저장소 생성 > 커미터 추가 > 개발환경 구축 > CI/CD 구축 > 빌드 > 패키지 저장소에 publish
- 더 쉬운 의존성 관리
  - 의존성 패키지가 같은 저장소에 있으므로 버전이 지정된 패키지를 npm registry와 같은 곳에 publish할 필요가 없다.
- 프로젝트들에 걸친 원자적 커밋
  - 커밋할 때마다 모든 것이 함께 작동합니다. 변경 사항의 영향을 받는 조직에서 쉽게 변화를 확인할 수 있다.
- 서로 의존하는 저장소들의 리팩터링 비용 감소
  - 모노레포는 대규모 변경을 훨씬 더 간단하게 만든다. 100개의 라이브러리로 만든 10개의 앱을 리팩터링하고 변경을 커밋하기 전에 모두 작동하는지 확인 가능.
- 테스트 및 빌드 범위 최소화
- 소스 변경 시 모든 프로젝트를 다시 빌드하거나 다시 테스트하지 않는다. 대신 변경 사항의 영향을 받는 프로젝트만 다시 테스트하고 빌드한다

#### Monorepo를 왜 적용하려나???

진행되는 프로젝트는 단지 관리자 센터 웹 어플리케이션.  
단독으로 개발되며, 추후 다른 프로젝트가 추가될 예정은 없음. 확장되지 않으니 적용을 하는 이유는 모르겠음.  
RFP에 떡하니 적혀있어 어쩔 수 없이 구성하지만 설계 상 이해가 되지 않음.  

이해가 되려면 기존의 monorepo 적용한 repository에 지금 작성하고 있는 관리자센터 앱 프로젝트가 추가되면 됨
- 기존의 ui 디자인 공유하여 사용(next console과 디자인이 동일해야 하는게 맞는 것 같은데...)
- 기존의 CI/CD 사용

### Turborepo 특징



### Turborepo 실제
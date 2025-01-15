---
title: React 특징 : VDOM, Render + Commit Phase 통한 렌더링 원리
description: >-
  React 특징 : Virtual DOM, Render + Commit Phase 통한 렌더링 원리 정리
author: 2SEONGA
date: 2025-01-15
categories: [React]
tags: [React, VDOM, useEffect]
pin: true
image:
  path: https://github.com/user-attachments/assets/f2b71e34-99ed-4f63-994b-aad7922b6848

media_subpath: "/posts/2025-01-15-react-vdom-rendering"
---

# React 특징 : VDOM, Render + Commit Phase 통한 렌더링 원리

## Facebook 에서 React 의 등장 배경

4개의 사진 리스트를 보여주는 페이지에서 더보기 버튼을 눌렀을 때 추가 4개의 사진을 보여주는건,

사진 리스트 **단일 HTML 요소**의 하단에 4개의 사진 리스트를 한 번에 추가, 나열하는 것

→ **한 번의 DOM Repaint 를 발생**

![2025-01-15-react-vdom-rendering(1)](https://github.com/user-attachments/assets/4a870d7e-0c47-4edd-bc17-8d0780878169)

그런데, 아래와 같이 **다수 HTML 요소가 바뀌는 (다수의 DOM Repaint 가 발생하는) 경우 React 가 필요**

![2025-01-15-react-vdom-rendering(2)](https://github.com/user-attachments/assets/cf80950d-55e9-49b8-980b-ba09f6eb7112)

- 데이터가 변함에 따라 웹 페이지 리렌더가 발생하는 이 절차를 React 에서는 단방향 프로세스로 설명
= **Controller**(SetState 데이터 변경 발생) → **Model**(State 데이터가 변경) → **View**(Page 그 데이터를 바라본 DOM 변경)

- **하나의 이벤트가 다수 DOM 을 변경시키는 경우**
    - 하나의 이벤트 = **상태**(데이터, Model) **변경**(Controller) → **다수 DOM 변경** = 페이지(View)
- 일반 쇼핑몰 홈페이지가 아니라 **피드 기반의 홈페이지에서 React 필요 (앱과 비슷한 웹, 웹앱)**
- **Facebook 은 우리가 보고있는 웹 페이지에서 피드도 계속 새로고침 되어야하고, 알람도 뜨고, 상태도 바뀌고 등**

---

**보여지는 페이지에 잦은 변경이 있는 (다수 HTML 요소가 바뀌는, 다수 DOM Repaint 가 발생하는) 웹앱에 적합**

- **정적인 웹 페이지엔 부적합**
- **실제 브라우저 동작 기반으로 매번 데이터 변경에 따라 DOM 리렌더/조작된다면 성능 최악**
    - **실제 DOM 자체가 느린 것은 아니고 Reflow, Repaint 절차를 여러번하는게 이슈**
        - 그래서 DOM 보다 VDOM 이 빠르다는 말은 잘못된 말
        - 다수의 DOM 변경보다 VDOM 을 통한 한번의 DOM 변경이 좋은 것

## 1. Virtual DOM 원리 : DOM 과 VDOM 의 동작 차이

<img alt="2025-01-15-react-vdom-rendering(3)" src="https://github.com/user-attachments/assets/cbc86a3f-f0ca-4aba-aee8-ab43b4f9b11c" />

-  도서관에서 책꽂이 정리하는 예시를 통해 알아보는 **실제 DOM 과 VDOM 차이**

<img alt="2025-01-15-react-vdom-rendering(4)" src="https://github.com/user-attachments/assets/788ef5cd-a326-4b0c-98a4-0919e26244c7" />

- 실제 HTML Element(요소, 영역)으로 알아보는 **실제 DOM 과 VDOM 차이**

<img alt="2025-01-15-react-vdom-rendering(5)" src="https://github.com/user-attachments/assets/eb51039a-b8c1-49a1-acd3-42886315a171" />

- 실제 웹 브라우저 렌더링(Layout → Paint)으로 알아보는 **실제 DOM 과 VDOM 차이**

>  DOM 조작의 문제 : 
트리 변화 → 레이아웃 변화 → 페인트(렌더링)을 매 번 일으킨다는 것
> 
> - 이전 VDOM 과 새 VDOM 과의 비교를 통해 어떤 부분을 바꾸면 될지 인지한 뒤 실제 DOM 리렌더를 단 한번만 하게끔 해주는것이 VDOM 의 의의
> - 이전과 신규 버전의 VDOM 을 비교하는 과정이 아래서 배울 Reconciliation 재조정

<img alt="2025-01-15-react-vdom-rendering(6)" src="https://github.com/user-attachments/assets/6fbdb64b-bd6d-4d5a-acdf-2d283fc036ce" />

**V-DOM 도 결국 DOM 과 동일** : 표현만 다를뿐
 **React 에서 ‘부모 - 자식’의 의미도 HTML 에서의 ‘부모 - 자식’과 동일**

## 2. Reconciliation 재조정 = 이전 / 신규 VDOM 비교

- **V-DOM** 은 **자바스크립트 객체의 형태**로 변환되어 비교
    - **JSX → JS → JSON** : **JSX** 는 **컴파일** 시 Babel 통해 **JS** 로 변환, **런타임**에서는 **JSON** 으로 변환

```jsx
// jsx pragma(jsx 컴파일 옵션): React.createElement 대신에 h를 사용하라고 Babel에게 전달
/** @jsx h */ 
const a = (
  <ul className="list">
    <li>item 1</li>
    <li>item 2</li>
  </ul>
);

// 위의 코드를 Babel이 아래와 같이 변환
const a = (
  h('ul', { className: 'list' },
    h('li', {}, 'item 1'),
    h('li', {}, 'item 2'),
  );
);

// h함수(직접 생성한 헬퍼 함수)를 실행하면 JSON(Virtual DOM 표현)가 반환됨
const a = (
  { type: 'ul', props: { className: 'list' }, children: [
    { type: 'li', props: {}, children: ['item 1'] },
    { type: 'li', props: {}, children: ['item 2'] }
  ] }
);
```

- **Reconciliation 비교가 다 완료되면, 변경된 부분만 바꾼 실제 DOM 렌더링을 한번만 트리거**
    - 이전 / 신규 V-DOM 적재 후 Reconcilation 비교를 위해 **메모리를 많이 사용하나, 빠른 속도**
    - 다수의 실제 DOM 리렌더를 한번의 실제 DOM 리렌더로 **하나로 묶여서 적용 = 배치**

# 3. React 동작 = Render + Commit + 웹 브라우저 Paint

- **리액트 렌더 라이프사이클** : 아래 그림에서 Render Phase 와 Commit Phase 로 나뉜 것 확인

<img alt="2025-01-15-react-vdom-rendering(7)" src="https://github.com/user-attachments/assets/89b836b1-b3f7-489a-99c2-bb3f4c6158ce" />

**- Reconciliation 재조정 = 비교**를 통해 다수의 실제 DOM 리렌더를 한번의 실제 DOM 리렌더로 **하나로 묶여서 적용**

- **언제 리액트 렌더링이 일어나는가? = 상태(Model)가 바뀌었을 때**
    - 그 다음, 리액트는 부모 컴포넌트가 렌더링되면 → 모든 자식 컴포넌트들을 순차적으로 리렌더링
    - 이처럼 **컴포넌트 렌더링 작업은 기본적으로 하위에 있는 모든 컴포넌트 또한 렌더링**
        - 상태(Model)가 너무 최상단의 부모에 있게된다면, **상태를 가진 부모의 자식 컴포넌트 죄다 리렌더**
        - **상태를 사용하는 자식 컴포넌트만 리렌더**하기 위해 Redux 와 같은 전역상태관리 매니저를 사용

### (1) Render Phase : (1) 컴포넌트를 렌더링하고 (2) 변경사항을 비교

> **No Side-effects** (Pure) = 실제 DOM 적용 전
> 
1. **컴포넌트 렌더링** : **JSX** 은 → Babel 통해 **JS 함수**로 컴파일된 후 → 런타임에 함수가 호출되어 **JSON** 반환
    - V-DOM (Virtual DOM) ⇒ JSON
2. **변경사항 비교 (비교 로직, Reconciliation 재조정)** : 앞서 반환된 **새 V-DOM** 과 **현재 (이전) V-DOM** 비교

<img alt="2025-01-15-react-vdom-rendering(8)" src="https://github.com/user-attachments/assets/15c3e4c5-c4d7-456b-882c-a11447131b92" />

새 VDOM 과 이전 VDOM 을 비교해봤는데 달라진게 없으면, **DOM 변경이 발생하지 않고 Paint 도 하지 않음**

> **VDOM 이 생성되는 기준**은 무엇인가? = React 에서 리렌더의 시점은 무엇인가?
> 
> 1. **현재 내 컴포넌트가 가지고 있는 상태**가 바뀌었을 때
> 2. **내 부모 컴포넌트가 가지고 있는 상태**가 바뀌었을 때

### (2) Commit Phase : 앞선 재조정 VDOM 들을 모아 실제 DOM 생성

> **No Side-effects** (Pure) = 앞선 변경사항 비교를 통해 실제 DOM 생성 **(DOM 준비 완료, 출격 준비)**
> 
1. **DOM Mutations** : 앞선 **Reconciliation 재조정**에 어떤 변경사항도 없다면, 실제 DOM 노드 변경 X

> **Side-effects** = React 렌더링 절차는 이제 모두 다 끝났고, 이 이후 모든 작업은 Side-effects
> 
1. **Cleanup useLayoutEffect → Run useLayoutEffect** : DOM Mutations 이 끝나자마자 **동기 수행**

> 
#### 🚫 **주의** : useLayoutEffect 을 “Render - Paint” 사이의 딜레이를 줄이기 위한 방법으로 사용하지 말 것
>

- **사실상 의미가 없음**
    - **useLayoutEffect** : Render → **API 호출**(동기 호출일 뿐 **비동기로 동작**) → Paint
    - **useEffect** : Render → Paint → **API 호출(비동기로 동작)**
- useEffect 사용 및 스피너, 로딩바, 스켈레톤(데이터 없는 빈 컴포넌트)을 통해 **CLS 및 UX 개선**

### (3) (Re)Paint : Web Browser Rendering

> **Side-effects** = 앞서 모든 작업이 완료된 **DOM 을 최종적으로 웹 브라우저에 렌더링**
> 
1. **Painting (Web Browser Rendering)** : React 는 제어권을 브라우저에게 위임하고 Paint 작업 진행
2. **Cleanup useEffect → Run useEffect** : **비동기 수행**
- useLayoutEffect 과 useEffect 를 구별할때 **Painting** (Web Browser) 기준으로 생각
    - 웹 브라우저 렌더 순서 : **Render Tree**(DOM + CSSOM) → **Layout**(Reflow) → **Paint**(Repaint)
    - Side-Effect Hook 고려 시 : **Render Tree** → **Layout** → useLayoutEffect → **Paint** → useEffect
        - **Paint 이전**에 **동기**적으로 수행되는 useLayoutEffect : 웹페이지가 보이기까지의 시간을 지연
        - **Paint 직후**에 **비동기**적으로 수행되는 useEffect

## 4. React 렌더 라이프사이클 후 처리 : use(Layout)Effect

### (1) useLayoutEffect : 페이지 레이아웃을 보기 전 동기적으로 실행

> Render → **Commit 중 DOM Mutations 직후 “동기” 실행** → Paint
> 
- **목적** : [Measuring layout before the browser repaints the screen](https://react.dev/reference/react/useLayoutEffect#measuring-layout-before-the-browser-repaints-the-screen)
    - **Layout 직후 → “useLayoutEffect”** → Paint : **Viewport 크기나 HTML 요소 위치 등을 알아야할 때**
    - React 공식 홈페이지 : **툴팁을 표기하기위한 HTML 요소가 어느 위치에 있는지 알아야함**
        1. Render the tooltip anywhere (even with a wrong position).
        2. Measure its height and decide where to place the tooltip.
        3. Render the tooltip *again* in the correct place.

---

- **동기(Synchronous)**로 실행
    - **로직이 간단한 경우** 사용자가 페이지 일부의 깜빡임(Loading 시)을 겪지 않아도 되어 좋음
        - DOM 조작하는 로직이 있더라도 Paint 작업 이전에 모두 완료되기 때문에 깜빡임이 없음
    - 하지만, **로직이 복잡한 경우** 전체 페이지 레이아웃을 보는데까지 딜레이 발생 (퍼포먼스 이슈)
        - [React 공식 홈페이지에서는 퍼포먼스 이슈가 발생할 수 있으니 useEffect 를 사용하길 제안](https://react.dev/reference/react/useLayoutEffect)
    - **useLayoutEffect** 는 동기로 실행되지만, await 호출은 안되기에 **API 호출은 useEffect 사용**

### (2) useEffect : 페이지 레이아웃을 본 후 비동기적으로 실행

> Render → Commit 중 DOM Mutation → **Commit 후 Paint 이후 “비동기” 실행**
> 
- **목적** : [외부 시스템과 연결(API) / 구글맵 등 Non-React 위젯(Ref) 구동 / Prop 변경에 따른 State 재계산](https://react.dev/reference/react/useEffect)
    - **Layout(Viewport 사이즈 및 위치)에 관련된게 아니라면 useLayoutEffect 보다 useEffect**
- **비동기(Asynchronous)**로 실행
    - 로직이 **복잡한** 경우 긴 시간이 걸리기때문에 페이지 일부의 깜빡임(Loading 상태)를 보여주는 것이 좋다
    - 로직이 비교적 **간단한** 경우 깜빡거림이 유저 사용성에 불편함을 준다는 판단 시 useLayoutEffect 사용

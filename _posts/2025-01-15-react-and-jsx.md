---
layout: post
title: "React 등장과 JSX 문법"
date: 2025-01-15
categories: react
---

# React 등장과 JSX 문법

## 웹 프론트엔드 개발과 React 등장

### (1) 웹 프론트엔드 개발 : Rendering = 웹 페이지의 화면 변경

웹 프론트엔드 개발은 **웹 페이지의 화면 변경(Rendering)**의 모든 것이며, **HTML(DOM) 조작의 모든 것**을 의미

- **고대** : JavaScript 문법을 통한 **HTML(DOM) 직접 조작**으로 웹 페이지 화면 변경
- **과거** : jQuery 문법으로 **HTML(DOM) 조작을 쉽게** 돕는 라이브러리
- **현재** : React JSX 문법을 통해 **HTML(DOM) 조작을 단순화**한 프레임워크

### (2) React는 왜 Facebook에서 등장했는가?

- **하나의 이벤트가 다수 DOM을 변경시키는 경우**
  - 하나의 이벤트 = 하나의 **상태(State)** 변경 → **다수 DOM(View)** 변경

![React 상태 관리 예제](https://prod-files-secure.s3.us-west-2.amazonaws.com/39e89df9-7032-4ea6-86de-4712f182d38d/d5cb675f-3199-4ea9-b2e8-22113acbb3f2/image.png)

### (3) SPA (Single Page Application) 란 무엇인가? = 출장뷔페

> **웹 브라우저** 내 JavaScript를 활용해 **동적 페이지** 생성 + JavaScript 반환을 위한 **웹 서버** 또는 **저장소(S3)**

#### SPA의 특징:
- **모바일 앱처럼 실시간 유저 인터페이스**가 중요한 웹 페이지에서 사용
  - 부드러운 페이지 전환 및 버튼 동작 제공
- **서버가 불필요** (단순한 호스팅 가능) = 빈 HTML + CSS + JS 제공, **API 서버는 필요**
- **웹 페이지 전환 시 깜빡임 없음** (동적 렌더링)
- 초기 로딩 속도가 느릴 수 있음 (Bundled JS 크기 때문)
- **검색엔진 최적화(SEO) 어려움** : 웹 크롤러가 렌더링된 페이지를 수집하지 못함

### (4) SPA Routing

- **SPA Routing** : React-Router-Dom 등으로 구현
  - **Hard Navigation** : 네트워크 사용 (SSR, SSG 방식)
  - **SPA Navigation** : 네트워크 미사용 (CSR 방식)

[Tutorial v6.28.0](https://reactrouter.com/6.28.0/start/tutorial)

---

## JSX 문법

### (1) React Component ~= HTML Element

React에서 화면은 여러 **React Component**로 구성되며, 이를 통해 **HTML Element**를 쉽게 관리

![React Component 구조](https://prod-files-secure.s3.us-west-2.amazonaws.com/39e89df9-7032-4ea6-86de-4712f182d38d/cc508711-d611-4dae-95b0-093dd391cd22/image_(1).png)

- React Component는 독립적이고 재사용 가능한 UI 요소
- 부모 컴포넌트에서 자식 컴포넌트로 **Props**를 전달하여 데이터 공유/전달
- React Component를 조합하여 더 복잡한 형태의 UI를 제작 가능

### (2) JSX 문법

**JSX**
- JavaScript와 XML의 혼합 문법
- React에서 DOM 조작 대신 JSX 문법을 사용하여 효율적으로 개발

```jsx
const element = <h1 className='greeting'>Hello, world!</h1>;
```
```jsx
const element = React.createElement('h1', { className: 'greeting' }, 'Hello, world!');
```

**JSX 가 JS 특성과 XML 특성 모두를 혼재하여 갖고있어서 아래와 같은 제약사항 존제**

1.  **JSX는 무조건 하나의 요소로 반환**
- 여러 요소를 작성하려면 하나의 태그로 감싸야 하며, Fragment 사용 가능
```jsx
<>
  <h1>오늘의 할 일</h1>
  <ul>
    <li>빨래하기</li>
    <li>숙제하기</li>
    <li>1시 약속</li>
  </ul>
</>
```
2.  **class 대신 className 사용**
```jsx
<div className="container"></div>
```
3.  **JS 로직(변수나 함수)을 넣기 위해서는 중괄호 사용**
```jsx
function Container({ children }) {
  const title = '오늘의 할 일';

  return (
    <div>
      <h1>{title}</h1>
      {children}
    </div>
  );
}
```
4.  ***주석은 /* ... */ 형태로 작성***
```jsx
{/* 주석 예제 */}
```
5.  ***명시적으로 태그 닫기***
```jsx
<img src="image.png" alt="example" />
```
6.  ***React Component는 대문자로 시작***
```jsx
function TodoList() {
  return (
    <Container>
      <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
      </div>
    </Container>
  );
}
```
7.  ***조건부 렌더링 삼항 연산자 사용***
```jsx
function TodoList() {
  const todoList = [];
  return (
    <Container>
      <div>
        {todoList.length > 0
          ? todoList.map(todo => <div key={todo.id}>{todo.value}</div>)
          : "새로운 할 일을 입력해보세요."}
      </div>
    </Container>
  );
}
```
8.  ***여러 HTML 엘리먼트를 표시할 때는 map 함수 사용***
```jsx
{items.map(item => (
  <li key={item.id}>{item.value}</li>
))}
```
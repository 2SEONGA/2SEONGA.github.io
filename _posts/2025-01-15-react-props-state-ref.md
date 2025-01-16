---
title: React Component 생성 시 파라미터(Props)와 내부 변수(State), 외부 변수(Ref)
description: >-
  Component 생성 시 파라미터(Props)와 내부 변수(State), 외부 변수(Ref) 정리
author: 2SEONGA
date: 2025-01-15
categories: [React]
tags: [React, Props, State, Ref]
pin: true
image:
  path: https://github.com/user-attachments/assets/0aad7add-b1e0-4d6d-8666-15772f432f80
media_subpath: "/posts/2025-01-15-react-props-state-ref"
---

# Component 생성 시 파라미터(Props)와 내부 변수(State), 외부 변수(Ref)

> **파라미터 역할을 하는 Props** & **내부 변수 역할을 하는 State** & **React 독립적 Ref**
> 

React Hook 은 함수 컴포넌트를 사용하여 개발하고 함수형 프로그래밍 패러다임을 도입
**→ 하나의 컴포넌트를 하나의 함수로 생각하여 State & Props & Ref 이해**

- **State** : 내부 변수 ← **제어 컴포넌트** : React 가 **인지하고 있는 변수**이기에 **State 변경 시 리렌더링 O**
    - **내부 변수**의 의미 : 값이 변경되면 컴포넌트 리렌더 발생
- **Props** : 파라미터
- **Ref** : 외부 변수 ← **비제어 컴포넌트** : React 가 **인지하지 못한 변수**이기에 **Ref 변경 시 리렌더링 X**
    - **외부 변수**의 의미 : 값이 변경되어도 컴포넌트 리렌더 미발생

![2025-01-15-react-props-state-ref(1)](https://github.com/user-attachments/assets/26e85c96-2017-404e-b667-5203ed096c93)

## 1. State : 내부 변수 ← **제어 컴포넌트**

> #### 제어 컴포넌트 vs. 비제어 컴포넌트
> 요소에 입력되는 값을 React 가 제어할 수 있는가 없는가를 기준으로
> → ex ) state로 관리 : 제어 컴포넌트 / DOM API로 관리 : 비제어 컴포넌트

> **제어 컴포넌트** : React 가 **인지하고 있는 변수**이기에 **State 변경 시 리렌더링 O**
> 
> - 실시간으로 입력한 값에 따라 실시간으로 표기되어야하는 화면
> - 실시간으로 입력한 값에 따른 실시간 유효성 검사 시 → **하지만 Ref 가 더 유용하고 적합**
- **State** : 단방향 바인딩에서 **컴포넌트 리렌더 기준이 되는 값** = **값이 변경되면 컴포넌트 리렌더 발생**
    - **Model** (JS 데이터 변경) → **View** (HTML 요소 변경)
- **setState** : 단방향 바인딩에서 **State 값 변경을 위한 Setter 함수** = **Controller** 
 상태의 **“불변성”** 중요
    - **Controller** (HTML 요소에서 State 변경) → **Model** (JS 데이터 변경) → **View** (HTML 요소 변경)

![2025-01-15-react-props-state-ref(2)](https://github.com/user-attachments/assets/2019e2e0-1de7-4d50-ac97-e250fbb633b8)

- **Model**(State) 은 **View** 라는 컴포넌트가 리렌더 여부 결정 기준, 단방향 바인딩
- Controller 는 Model 을 직접적으로 변경 → 자연스럽게 Model 의 변경이 View 의 변경 발생
= Controller 는 View 는 간접적으로 변경되는 셈

### **setState** 는 동기가 아닌 **비동기**로 동작

<img alt="2025-01-15-react-props-state-ref(3)" src="https://github.com/user-attachments/assets/6c655ef9-ab7b-46b1-86f6-5244f502cc1e" />


- 바로 수행하지 않고, 차곡차곡 모아두었다가 리렌더링 시 한번에 일괄적으로 상태변경 적용
- 여러 상태 업데이트를 모아서 효율적으로 처리하고, 성능 최적화를 위해 렌더링 최소화

<img alt="2025-01-15-react-props-state-ref(4)" src="https://github.com/user-attachments/assets/3bab6ade-b571-4655-8d02-752103af19e8" />


**( state 변경(콘솔) - 리렌더링 - state 반영 ) : 호출지와 실행지가 다른 경우, 콘솔에세 랜더링 전의 값 출력**

> **SetState를 사용하는 두 가지 방법**
> 
> 
> 1. SetState 내 새로운 상태값을 생성하는 **함수**를 파라미터로 (함수형 업데이트)
> 
> 2. SetState 내 새로운 **상태값** 자체를 파라미터로
> 

**비동기적 동작의 특징**

1. **상태 업데이트가 즉시 미반영**

상태를 변경한다고 해서 바로 화면이 업데이트되거나, 변경된 상태를 즉시 콘솔에 찍는 건 불가능

```jsx
const [count, setCount] = useState(0);

const handleClick = () => {
  setCount(count + 1); // 상태 변경 요청
  console.log(count);  // 이전 값이 출력 (비동기)
};

return <button onClick={handleClick}>Click me</button>;
```

버튼을 클릭하면 console.log(count)는 **0**으로 출력

이는 setCount가 비동기적으로 동작하여 **리렌더링 후** 상태가 반영되기 때문

1. **리렌더링 시 상태 변경이 일괄 처리**

React는 여러 setState 호출을 묶어서 한 번의 리렌더링으로 처리

```jsx
const [count, setCount] = useState(0);

const handleClick = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
  console.log(count); // 이전 값이 출력
};

return <button onClick={handleClick}>Click me</button>;
```

위 코드는 버튼을 클릭했을 때 count**가 1씩만 증가**

이유는 setCount(count + 1)가 3번 호출되더라도 count의 값은 호출 당시의 **0**에서 계산

따라서 최종적으로 0 + 1만 반영

---

#### **setState를 사용하는 두 가지 방법**

**1. 상태값 자체를 파라미터로 전달**

```jsx
setCount(count + 1);
```

- **특징** : 현재 상태값(count)을 기반으로 새로운 값을 설정
- **문제점** : 연속된 상태 변경 시 예상치 못한 동작이 발생
    
                 각 호출은 **현재 상태값**을 참조하므로, 여러 호출이 독립적으로 처리
    
    → 간단한 경우 적합하지만, 연속 업데이트 시 비효율 가능성 존재
    

**2. 함수형 업데이트 사용**

```jsx
setCount((prevCount) => prevCount + 1);
```

- **특징** : 이전 상태값(prevCount)을 인자로 받아 새로운 상태값을 반환
- **장점** : 여러 상태 업데이트를 호출하더라도, React가 올바르게 이전 상태를 참조
    
    → 상태 업데이트 간 의존성이 있는 경우 추천
    

```jsx
const [count, setCount] = useState(0);

const handleClick = () => {
  setCount((prevCount) => prevCount + 1);
  setCount((prevCount) => prevCount + 1);
  setCount((prevCount) => prevCount + 1);
  console.log(count); // 이전 값이 출력
};

return <button onClick={handleClick}>Click me</button>;
```

- 결과적으로 버튼을 클릭하면 count는 **3** 증가
    
    각 호출은 최신 상태값을 기반으로 계산하므로, 상태 업데이트의 정확성이 보장

---

## 2. Props : 파라미터

값을 부모 컴포넌트가 자식 컴포넌트에게 넘겨줄 때 Props 로 전달

- 자식 컴포넌트에게 넘겨줄 Props 값 일반적인 예시
    - 부모 컴포넌트의 **State**
    - 부모 컴포넌트의 State 를 변경할 수 있는 **SetState** (콜백)
        - **State Lifting (상태 끌어올리기)**
        : 자식 컴포넌트가 부모 컴포넌트의 State 를 SetState 로 변경

![2025-01-15-react-props-state-ref(5)](https://github.com/user-attachments/assets/979206e1-98d5-4b00-ad68-7bb824e06e83)

### React 상태 관리 : Single Source of Truth (SSOT)

```jsx
function InputComponent({ value, onChange }) {
  const [string, setString] = useState(value); // 자식이 별도의 상태를 소유

  useEffect(() => {
    onChange(string); // 자식의 상태를 부모에게 전달 : 의존성 배열이 변경될 때 사용
  }, [string]);

	useEffect(() => {
    setString(value); // 부모의 상태를 자식에게 전달 : 의존성 배열이 변경될 때 사용
  }, [value]);

  return (
    <input value={string} onChange={(e) => setString(e.currentTarget.value)} />
  );
}

function App() {
  const [string, setString] = useState(''); // 부모가 상태를 소유
  return (
    <>
      <div>
        <InputComponent value={string} onChange={setString} />
      </div>
    </>
  );
}
```

1. **상태 중복**

부모(App)와 자식(InputComponent)이 동일한 데이터를 관리하기 위해 각각 상태(string)를 소유

- 부모는 자신의 useState로 string을 관리
- 자식은 props로 받은 값을 별도로 useState로 관리

2. **부모 변경 사항의 미반영**

자식이 value를 초기값으로 받아 상태를 관리하기 때문에 부모의 상태가 변경되더라도 자식은 미반영

- useState는 **컴포넌트가 처음 렌더링될 때만 초기값**으로 설정되기 때문
- 이후 value가 바뀌어도 자식 컴포넌트는 이를 알지 못하고 자신의 상태만 업데이트

3. **비효율적인 데이터 흐름**

자식이 상태를 업데이트하면 다시 부모로 전달하고, 

부모는 이를 자신의 상태로 업데이트하며 불필요하게 복잡한 데이터 흐름을 초래

---

- **해결책 → Single Source of Truth 원칙 준수**

**부모 상태만 사용**하여 상태 중복을 없애야 함

부모가 상태를 소유하고 자식은 단순히 **UI와 이벤트 처리**만 담당하도록 수정

- 확인과 취소만 예외 : 일시적으로 필요한 상태를 임시 보관해야할 때

```jsx
function InputComponent({ value, onChange }) {
  return (
    <input value={value} onChange={(e) => onChange(e.currentTarget.value)} />
  );
}

function App() {
  const [string, setString] = useState(''); // 부모가 상태를 소유
  return (
    <>
      <div>
        <InputComponent value={string} onChange={setString} /> {/* 상태와 이벤트 전달 */}
      </div>
    </>
  );
}
```

---

### **이벤트 버블링(Event Bubbling) 과 stopPropagation()**

- **이벤트 버블링(Event Bubbling)**

특정 DOM 요소에서 이벤트가 발생했을 때, 그 이벤트가 **가장 하위 요소에서 상위 요소로 전파되는 현상**

즉, 이벤트가 발생한 요소에서 시작해서 부모 요소들을 거쳐 document까지 전파

```jsx
document.getElementById('parent').addEventListener('click', () => {
  alert('Parent clicked!');
});

document.getElementById('child').addEventListener('click', () => {
  alert('Child clicked!');
});
```
<img alt="2025-01-15-react-props-state-ref(6)" src="https://github.com/user-attachments/assets/081b377e-795d-428e-bf56-445815370a96" />

<img alt="2025-01-15-react-props-state-ref(7)" src="https://github.com/user-attachments/assets/cf97bb3f-ba7c-4c22-9f58-55ef0e2548c7" />

<img alt="2025-01-15-react-props-state-ref(8)" src="https://github.com/user-attachments/assets/2c913ccb-d4cf-4150-b213-f2535bf637dc" />

- **stopPropagation()**

메서드는 이벤트가 상위 요소로 전파되지 않도록 이벤트 버블링을 차단

```jsx
document.getElementById('parent').addEventListener('click', () => {
  alert('Parent clicked!');
});

document.getElementById('child').addEventListener('click', (event) => {
  event.stopPropagation(); // 이벤트 버블링을 막음
  alert('Child clicked!');
});
```

## 3. Ref : 외부 변수 ← **비제어 컴포넌트**

> **비제어 컴포넌트** : React 가 **인지하지 못한 변수**이기에 **Ref 내부 값이 변경 시 리렌더링 X**
> 
> - 입력폼이 너무 많아서, 각 폼 입력에 따른 유효성 검사 시 너무 많은 수의 리렌더링이 발생할 때
> - UI 와 직접적 관련이 없고, 복잡한 형태의 데이터라서 State 를 사용하고싶지 않을 때

React 리렌더링을 발생시키지 않고 **변경 가능한 값을 담는 참조(Reference)** 생성

- **Ref (외부 변수)**를 React 가 인지할 수 없기에 **Ref 값의 변동과 리렌더링은 전혀 상관이 없다**는 의미에서
- **Ref (외부 변수)를 통해 값을 관리하는 컴포넌트를 비제어 컴포넌트 라고 부름**

---

Reference (useRef 로 생성한) 를 HTML 요소에 연결한다면, **Reference = DOM**

- Reference (useRef 로 생성한) 는 **값을 저장하는 용도**로도 사용될 수 있지만
- Reference (useRef 로 생성한) 는 **기본적으론 React 에서 DOM 조작을 위해 사용**
    - `reference.current` ****출력 = **DOM**
    - `document.getElementById` ****출력 = **DOM**

```jsx
function App() {
	// 1. Javascript in <head>
  const reference = useRef(null)

  console.log(reference.current)
  console.log(reference.current?.id)
  console.log(document.getElementById('for-reference'))
  console.log(document.getElementById('for-reference')?.id)

  useEffect(() => {
  	// 3. Javascript in end of <body>
    console.log(reference.current)
    console.log(reference.current?.id)
    console.log(document.getElementById('for-reference'))
    console.log(document.getElementById('for-reference')?.id)
  }, [])

	// 2. HTML (DOM) < 이때 reference 에 DOM 할당
  return (
    <>
      <input id='for-reference' ref={reference} type='number' />
    </>
  )
}
```

- 왜 첫번째 4개의 로그는 null 혹은 undefined 가 찍히고, 그 다음 4개의 로그는 제대로 찍히는가?
    1. 가장 먼저 useRef 통한 reference 생성 (null)
        - **Typescript** : useRef 통한 reference 생성 시 **(1) 타입**이나 **(2) null** 로 초기화 필요
    2. 그 다음 HTML (DOM) 모두 파싱 및 로드 ← **이때가 되어서야 reference 에 DOM 할당**
    3. 마지막으로 reference 로 DOM 조작 가능 (reference 에 DOM 이 연결되어있으니)

![2025-01-15-react-props-state-ref(9)](https://github.com/user-attachments/assets/bdfa00f7-d41e-46db-a397-034349ae6ff1)

- **왜 React 리렌더링은 Ref 값의 변동을 인지하지 못하는가?**
    - **Ref** 는 Javascript Engine 내 Heap 내에 위치한 객체를 가리킴
    - **Ref** 객체값을 변경시키는 것은 Heap 내의 객체를 바꾸는 것으로 **Ref(Reference, 주소) 자체는 미변동**

### (1) **Ref 를 언제 활용하는지에 대한 유즈케이스**

1. **리렌더링 감소**
    
    : 많은 값을 가진 큰 객체를 다양한 컴포넌트에서 업데이트 하는 경우 리렌더가 사정없이 발생
    
    - 값 하나만 바뀌더라도 그걸 사용하는 **컴포넌트 전체에서 리렌더가 발생**
    - 이를 방지하기 위해, 입력 폼으로 어자피 값은 표기되니 실시간 갱신을 하지말자
        - **제출 버튼을 클릭했을 때만 Ref 에 있는 값을 사용**
- 함수형 컴포넌트는 인스턴스가 없기 때문에 **Ref 가 미존재 → forwardRef 필요**
    - **함수형 컴포넌트에 Ref 주입을 위해 함수형 컴포넌트를 forwardRef 로 감싸주기**
- 컴포넌트가 익명 함수라면 forwardRef 사용 후 디버깅 시 이름이 나오지 않음
    1. **기명 함수** 쓸 것
    2. **재할당**으로 컴포넌트 대체
    3. displayName 속성으로 **이름 붙여주기**

---

- 생각해보면 단순한 것
    - **= 모든 View 의 리렌더의 기준은 Model 변경 여부에 의존** (그림 복습)
        - **Controller** (= **SetState** 호출지) 의 위치는 전혀 상관 없음
        - **Model** (= **State** 위치) 의 위치만이 상관있음, 이를 기준으로 View 리렌더

<img alt="2025-01-15-react-props-state-ref(10)" src="https://github.com/user-attachments/assets/596b6a34-d49b-42a8-badb-012dfcc38549" />

- **Model**(State) 은 **View** 라는 컴포넌트가 리렌더 되냐 안되냐를 결정하는 기준이고, 방향은 단방향 바인딩
- Controller 는 Model 을 직접적으로 변경하고 → 자연스럽게 Model 의 변경이 View 의 변경을 발생시키기에
= Controller 는 View 는 간접적으로 변경하게 되는 셈

---

- **“모든 View 의 리렌더의 기준은 Model 변경 여부에 의존한다.”**라는 특성에 따라 **아래와 같은 문제**
    - React 는 MVC 패턴에 의존하기에, 이 단방향 바인딩에서 기인하는 문제
        1. **Props Drilling** : State(Model) 에 의존하는 View 가 State 전달받기 위해 Props 여행
        2. **State 의 변경은 State 가 속한 부모의 자식 컴포넌트 모두를 리렌더링** (나비효과)

<img alt="2025-01-15-react-props-state-ref(11)" src="https://github.com/user-attachments/assets/0a8f8ce3-0aac-4013-9934-2f8bebb422e3" />

- 기존 MVC 패턴에서의 상태 수직구조의 단점들을 → 새 Flux 패턴에서의 상태 수평구조로 모두 해결

### (1) **Ref 를 언제 활용하는지에 대한 유즈케이스**

- **순수 HTML 요소 간접 조작** : React 에서 HTML 자체 제공하는 DOM 객체에 접근/조작하기위해
    - `<input/>` 이런것들은 React Component 가 아니라 **내부 상태가 존재하지 않는 순수 HTML 요소**
        - **DOM 객체(Reference)**를 통해 간접적으로 조작 (리렌더링 없이, 가장 이상적인 용례)
            - DOM 객체(Reference) = ref.current 프로퍼티
    - 기존에는 순수 HTML 요소 (DOM) 선택을 위해 아래의 방법 사용
        1. 일반 Javascript 에서는 `document.findElementById()` 같은걸 사용 (DOM 객체 반환)
        2. jQuery 에서는 `$` 사용 (jQuery 객체 중 첫번째 요소를 불러야 → DOM 객체 접근/조작 가능)
    - **다른 예시로 React Hook Form 사용 시 별도의 분리된 컴포넌트에 useForm 의 register 적용 시**
        - **문제 발생** = **3.2.2.3.2. 충격적인 사실 : 함수형 컴포넌트에는 Ref 가 미존재 → forwardRef 필요**
- **React 에서 이런 `ref` 를 통해 HTML 에 직접 접근해서 무엇을 하려는 걸까?**
    - 특정 요소에 **포커스를 주거나**
        
        ```jsx
        import { useRef } from 'react'
        
        function Field() {
        	const inputRef = useRef();
        	
        	function handleFocus() {
        		inputRef.current.focus();
        	}
        	
        	return (
        		<>
        			<input type="text" ref={inputRef} />
        			<button onClick={handleFocus}>입력란 포커스</button>
        		</>
        	)
        }
        ```
        
    - 특정 요소에 **Utterances 를 붙일 때나**
        
        ```jsx
        const Comments = () => {
          const commentRef = useRef(null);
        
          useEffect(() => {
            // script element 생성
            const utterances = document.createElement('script');
        
            // attribute를 전체를 객체로 만들기
            const utterancesConfig = {
              src: 'https://utteranc.es/client.js',
              repo: 'user/repo',
              theme: '선택한 테마',
              'issue-term': '포스트 페이지 매핑 방법',
              async: true,
              crossorigin: 'anonymous',
            };
        
            // 객체 전체를 setAttribute로 붙이기
            Object.entries(utterancesConfig).forEach(([key, value]) => {
              utterances.setAttribute(key, value);
            });
        
            // 만든 script를 ref 항목에  appendChild로 붙이기
            commentRef.current.appendChild(utterances);
          }, []);
        
          return <div ref={commentRef} />;
        };
        
        export default Comments;
        ```
        
    - 특정 요소에 **Observer 붙일 때  (스크롤에 따라 상단에 제목을 표기할지 말지)**
        
        ```jsx
        import { useRef, useEffect } from 'react'
        
        function SpyExample() {
          const spy = useRef()
        
          useEffect(() => {
            // 1. Define
            const observer = new window.IntersectionObserver(([entry]) => {
              if (!entry.intersectionRatio) {
                document.getElementById('header-title').classList.add('scrolled-a-bit')
              } else {
                document.getElementById('header-title').classList.remove('scrolled-a-bit')
              }
            })
            // 2. Attach
            observer.observe(spy.current)
            // 3. Detach
            return () => {
              observer.disconnect()
            }
          }, [])
        
          return (
            <div ref={spy} />
          )
        }
        ```
        
- **다른 예시로 React Hook Form 사용 시 별도의 분리된 컴포넌트에 useForm 의 register 적용 시**
    - **문제 발생** = **3.2.2.3.2. 충격적인 사실 : 함수형 컴포넌트에는 Ref 가 미존재 → forwardRef 필요**

## 4. 정리 : Props 와 State, Ref 사용

<img alt="2025-01-15-react-props-state-ref(12)" src="https://github.com/user-attachments/assets/85531bef-4545-4930-9a44-9a19d4c27373" />

- **Props** : React Component 의 **파라미터 - 일반적으로 쓸 때 객체 비구조화로 사용 `{}`**
    - Props 를 받아 그냥 쓰는것과 / Props 를 받아 새 상태의 초기값으로 쓰는 경우
- **State** : React Component **내부 변수 = 리렌더링이 필요한 값**
    - 유저 인터렉션이 있는가? 상태가 있어야한다
    - 유저 인터렉션 없이 단순 표기만 하는가? Props 로 다 처리 가능
- **Ref** : React Component **외부 변수 = 리렌더링이 필요없는 값**
    - 값을 담거나 조작하고싶으나 그것이 리렌더링을 발생시키지 않을 경우
    - 순수 HTML 요소에 접근할때
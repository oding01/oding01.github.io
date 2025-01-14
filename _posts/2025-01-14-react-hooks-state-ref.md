---
title : "[React] React Hooks: useState vs useRef 사용법과 예제"
author: potato
date : 2025-01-14 09:00:00 +0900
categories : [React, hooks]
tags: [react, hooks, javascript]
image:
  path: https://github.com/user-attachments/assets/72e6254e-7523-42e7-9720-de9cb29272de
description: useState와 useRef의 사용법과 예제
---
## UseState
### useState의 형태
React에서 컴포넌트는 자신의 상태 또는 props가 바뀌면 리렌더링 됩니다.   

이 상태를 관리하기 위해서 React에서는 `useState`를 활용합니다.   

`useState`는 상태 유지 값과 그 값을 갱신하는 함수를 반환합니다.   

형태는 다음과 같습니다.

```jsx
const [state, setState] = useState(initialState);
```

> 1. `state`   
: 상태 값 저장 변수입니다.
> 2. `setState` = 비동기 동작   
: 상태 값 갱신 함수입니다.

`setState()` 함수는 배열을 리턴하는데, 첫 번째 원소는 상태 값을 저장할 변수이고,   
두번 째 원소는 해당 상태 값을 갱신할 때 사용할 수 있는 함수입니다.   
그리고 `setState()` 함수에 인자로 해당 상태의 초기 값을 넘길 수 있습니다.   

간단하게 <span style='color: slateblue'>**State 값 변경을 위한 Setter 함수**</span>(= Controller)라고 이해하면 됩니다.

![image](https://github.com/user-attachments/assets/78bda3e9-d3bc-4a94-a9fb-f4f9e5cdc09d)

그림에 대한 설명을 하자면, 
- Model(State)은 View라는 컴포넌트가 **리렌더 되냐 안되냐를 결정하는 기준**이고, 방향은 **단방향 바인딩**입니다.
- Controller는 **Model을 직접적으로 변경**하고 → 자연스럽게 Model의 변경이 View의 변경을 발생시킵니다.
= 따라서 Controller는 **View를 간접적으로 변경**하게 되는셈입니다.

### 📌 setState는 비동기
`setState`를 사용하면서 주의할 점은, 동기가 아닌 <span style='color: indianred'>**비동기**</span>라는 점입니다.

만약, 숫자를 증가시키면서 5가 되었을 때 count 아래에 `5입니다.`라는 문구를 출력한다고 해봅시다.   
console에는 클릭 전과 후의 count(state)의 결과를 출력합니다.

```jsx
function Example() {
  const [count, setCount] = useState(0)

  function click() {
    console.log(`click 전 ${count}`)
    setCount(count + 1)
    console.log(`click 후 ${count}`)
  }

  return (
    <>
      <div>{count}</div>
      <button onClick={click}>증가</button>
    </>
  )
}
```
콘솔의 결과가 어떻게 될 거라고 예상하시나요?

비동기라는 것을 알지 못했을 때는 `click 전 0, click 후 1`이라고 예상하실 겁니다.

하지만 `setState`는 비동기라고 했습니다. click 한번을 눌렀을 때의 결과값은 다음과 같습니다.
```
click 전 0
click 후 0
```

**결과적으로 `setState`는 동작을 바로 수행하지 않고, 차곡차곡 모아두었다가 리렌더링 시 한번에 일괄적으로 상태변경을 적용합니다.**

<br/>

***
## useRef
### useRef의 형태
형태는 다음과 같습니다.

```jsx
const reference = useRef(initialValue);
```

### Ref의 특징

```jsx
function Example() {
  const textReference = useRef(null)

  function handleClick() {
    console.log(textReference)
    textReference.current.focus()
  }

  return (
    <div>
      <input type="text" ref={textReference} />
	  <button onClick={handleClick}>click me</button>
    </div>
  )
}
```
`useRef`는 `.current` 프로퍼티로 전달된 인자(initialValue)로 초기화된 변경 가능한 ref 객체를 반환합니다.
    
![image](https://github.com/user-attachments/assets/c7880f4e-a235-48f0-825e-138fe17d18ac)

`handleClick` 함수를 봅시다.

`textReference.current.focus()`형태로, `textReference`의 현재 값인 `<input>`태그에 `focus`를 주고 있습니다.

`textReference`는 `<input>` 태그에 연결되어 있으므로, 버튼을 클릭한다면 `<input>` 요소에 접근하여 `focus`가 수행됩니다.

Ref는 render 메서드에서 생성된 DOM 노드나 React 엘리먼트에 접근하는 방법을 제공합니다.
즉, ref는 DOM을 조작할 수 있게 합니다.   

하지만 ref를 다른 용도로 사용할 수도 있습니다. 아래와 예시를 보겠습니다.

```jsx
function Example() {
  const reference = useRef('안녕')
  console.log(reference.current)

  function click() {
    reference.current = '잘가'
    console.log(reference.current)
  }

  return (
    <>
      <h3 ref={reference}>{reference.current}</h3>
      <button onClick={click}>클릭</button>
    </>
  )
}
export default Example
```
```
안녕
잘가
```

1. `<h3>`의 형식을 봅시다.

`<h3>`에는 `value={state}` 형식이 아닌 `ref={reference}` 형식으로 '안녕'이라는 초기값을 가진 ref를 참조합니다.   

버튼을 클릭하여 reference의 값을 변경해봅시다.   

console에는 클릭할때마다 값이 '잘가'로 바뀌어 출력되는 것을 볼 수 있습니다.

이런 식으로 reference의 값을 조회하고, 변경할 수도 있습니다.

<br />

❓ 그럼 이 코드에서, 과연 `<h3>`의 `{reference.current}` 부분은 버튼을 클릭했을 때 '잘가'로 바뀔까요?

아니요! 리렌더링이 일어나지 않아 바뀌지 않습니다. 왜 그럴까요?

> Ref는 JavaScript Engine 내 Heap에 위치한 객체를 가르킵니다.   
따라서 Ref 객체의 `.current` 속성을 변경시키는 것은 Heap 내의 값을 바꾸는 것으로, 주소 값 자체는 바뀌지 않습니다.
{: .prompt-tip }

결론은,

> 1. useRef는 순수 자바스크립트 객체를 생성합니다.   
>2. useRef로 만든 객체를 수정하는 것은 컴포넌트의 렌더링과 무관합니다.   
다시 말하면, `.current` 프로퍼티를 변형하는 것이 리렌더링을 발생시키지 않습니다.   
➡️ 결국 reference 값의 변동은 리렌더링이 일어나지 않기 때문에 State처럼 값 변경에 따라 바로바로 DOM을 바꿀 수 없다는 겁니다. (우리가 콘솔에 찍어준 것처럼 '나 바뀌었어! 어떻게?' 하고 꺼내주는 별도의 작업이 필요하다는 겁니다!)
{: .prompt-info }

<br />

***
## useState vs useRef
그렇다면 언제 `useState`를 사용하고, 언제 `useRef`를 사용해야 하는 걸까요?

이 해답은 공식문서에 나와있습니다.

HTMLElement중에는 상태를 가지고 있는 것들이 있습니다.
- input
- select
- textarea

이 HTMLElement 들의 상태를 누가 관리하느냐에 따라 컴포넌트는 두 가지로 나뉩니다.
1. <span style='color: slateblue'>Controlled Component</span>
2. <span style='color: slateblue'>Uncontrolled Component</span>

### 1. Controlled Component
제어 컴포넌트입니다.   
React가 인지하고 있는 변수이기에 State 변경 시 리렌더링이 일어납니다.

> 여기서 React가 인지하고 있는 변수란 React가 상태로 추적하고 있는 변수를 의미합니다.

즉, `useState`에 의해 상태로 관리하고 있는 컴포넌트를 **제어 컴포넌트**라고 합니다.

### 2. Uncontrolled Component
비제어 컴포넌트입니다.   
React가 인지하지 못한 변수이기에 Ref 내부 값을 변경 시 리렌더링이 일어나지 않습니다.

즉, React가 상태로 추적하고 있지 않은 컴포넌트입니다. (ref를 React가 인지할 수 없기 때문)
<br />

### 그래서 언제 뭘 사용하라고?
#### useState
1. 실시간 유효성 검사가 필요할 때
2. 입력값에 따라 UI가 즉시 업데이트되어야 할 때
3. 폼 데이터를 실시간으로 다른 컴포넌트와 공유해야 할 때

이처럼 `useState`는 **실시간성**이 중요할 때 사용합니다.

#### useRef
1. 폼 제출 시에만 값을 확인하면 될 때
2. 불필요한 리렌더링을 피하고 싶을 때   
<span style='color:gray'>(너무 많은 입력폼(input 등)이 있어서, 각 폼 입력에 따른 유효성 검사 시 너무 많은 수의 리렌더링이 발생할 때)</span>
3. 특정 DOM 요소에 직접 접근이 필요할 때
4. UI와 직접적 관련이 없고, 복잡한 형태의 데이터라서 State를 사용하고싶지 않을 때

이처럼 `useRef`는 **많은 수의 리렌더링**이 발생할 때, **실시간성이 중요하지 않은 데이터**를 다룰 때 등 사용합니다.

<br />

***
## 마치면서
사실 제어 컴포넌트냐, 비제어 컴포넌트냐를 정할 때엔 많은 고민을 해봐야할 것 같습니다.

state를 사용했을 때 input 태그에 아이디 하나를 칠 때도 그 밑의 비밀번호, 이메일 작성란 등 모든 컴포넌트가 리렌더링 될 테니 좋진 않겠죠.

그렇다고 useRef를 써서 비제어 컴포넌트로 만들어버리면.. 실시간으로 입력한 값에 따른 실시간 유효성 검사 때 힘들지 않을까? 싶네요.

1. 실시간 유효성 검사가 필요한 필드만 useState 사용
2. 나머지 필드는 useRef 사용
3. 필요한 경우 useMemo나 useCallback으로 리렌더링 최적화

결국 요구사항과 상황에 따라 적절히 혼용하는 것이 좋은 것 같습니다.
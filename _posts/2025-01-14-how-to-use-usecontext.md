---
title : "[React] React Hooks: useContext로 전역 상태 관리하기"
author: potato
date : 2025-01-14 17:31:00 +0900
categories : [React, hooks]
tags: [react, hooks, javascript]
image:
  path: https://github.com/user-attachments/assets/e13c56d6-1e37-4bb6-95f4-3ca2d60cedf1
description: useContext란 무엇인가 + 전역 상태 관리
---
## useContext가 없었더라면...
리액트로 개발할 때 모든 컴포넌트에 `props`로 어떤 값을 넘기고 싶을 때가 있습니다.

즉, 부모에서 모든 자식 컴포넌트들로 `props`를 전달하고 싶다는 얘기죠.

`useContext`를 배우지 않은 상황에서 간단하게 코드를 극단적으로 짜보겠습니다.
#### 코드
```jsx
function Title({ title }) {
  return <h3>{title}</h3>
}

function Button({ title, onClick }) {
  return <button onClick={onClick}>{title}</button>
}

function Content({ content }) {
  return <p>{content}</p>
}

function SubContainer({ title, content, onClick }) {
  return
  <>
    <Title title={title} />
    <Content content={content} />
    <Button title={title} onClick={onClick} />
  </>
}

function Example() {
  const [title, setTitle] = useState('useContext')
  const [content, setContent] = useState('Context provides a way to pass data through the component tree without having to pass props down manually at every level')
 
  function handleClick() {
    setTitle('Changed!')
  }
  
  return (
    <>
      <SubContainer
        title={title}
        content={content}
        onClick={handleClick} />
    </>
  )
}
```

최상단에 있는 부모 컴포넌트인 `Example`이 `title`, `content`, `onClick`이라는 상태(State)와 함수를 `SubContainer` 컴포넌트에 모두 전달해주고, `SubContainer` 컴포넌트가 또 다시 `Title`, `Content`, `Button`에 전달해주고 있습니다.

#### 문제점
이러한 현상을 <span style='color: indianred'>Props Drilling</span>이라고 합니다.   
Props Drilling은 부모 컴포넌트에서 깊은 곳에 있는 자식 컴포넌트로 데이터를 전달하기 위해, 중간에 있는 여러 컴포넌트들을 거쳐가야 하는 현상을 말합니다.

Props Drilling은 여러 문제점이 존재합니다.
1. 코드 가독성 저하
2. 유지보수의 어려움
3. 불필요한 리렌더링 발생 가능성

예제에서는 컴포넌트 수가 몇개 되지 않게 때문에 크게 번거롭지 않아보일 수도 있지만, 실제 수십, 수백개의 컴포넌트로 이뤄진 리액트 앱에서 일일이 추가해준다면 어떨까요? ~~살려주세요~~

'나는 Title만 변경하고 싶어!'하고 저 버튼을 클릭하는 순간, 부모부터 `title` 상태를 가진 SubContainer, Title, Button, Content 컴포넌트 모두 리렌더링 되는 상황이 펼쳐집니다.

<br />

***
## Context API
위에서 살펴본 것처럼 우리에겐 전역 데이터를 관리하는데 좀 더 나은 접근 방식이 필요합니다.

React Context는 전역 데이터를 좀 더 단순하지만 체계적인 방식으로 접근할 수 있도록 도와줍니다.

Context API의 구성은 아래와 같습니다.

1. Context
: 전역 상태가 저장되는 곳
2. Provider
: 전역 상태를 제공 → 어떤 범주에 제공할지를 지정
3. Consumer
: 전역 상태를 사용 = 전역 상태에 대한 접근 

### Context 생성
전역 데이터를 관리하기 위해서 React 패키지에서 제공하는 `createContext` 라는 함수를 사용합니다.

Context가 뭔데? 라고 하신다면 전역 데이터를 담고 있는 하나의 저장 공간이라고 생각하면 됩니다. 이렇게요.

![context설명](https://github.com/user-attachments/assets/69b19ea5-3c09-46c2-80a3-9193c74777b8)

이해가 가셨다면 Context를 생성해봅시다.

```jsx
import { createContext } from "react";

const context = useContext()
```
<br/>

### Provider로 Context 저장
다음과 같이 어떤 컴포넌트에서 `Provider`로 감싸주면, 그 하위에 있는 모든 컴포넌트들은 Context에 저장되어 있는 전역 데이터에 접근할 수 있습니다.

여기서 Context.Provider **범주 밖에서는 Default Value**를 사용하고, **범주 안에서는 Initial Value**를 사용합니다.
- **Default Value** : `const CountContext = createContext(defaultValue)`
- **Initial Value** : `<CountContext.Provider value={InitialValue}>`
  - Context 사용할 때 일반적으로는 최상단 부모에 (전체 영역에) 적용

Initial Value에서 `value` 속성값을 지정하지 않을 경우, Context를 생성할 때 넘겼던 디폴트 값이 사용됩니다.

위에서의 코드를 Provider로 감싸보겠습니다.

#### 코드
```jsx
{% raw %}
import { createContext } from "react";

const Context = useContext()

function Example() {
  const [title, setTitle] = useState('useContext')
  const [content, setContent] = useState('Context provides a way to pass data through the component tree without having to pass props down manually at every level')

  function handleClick() {
    setTitle('Changed!')
  }
  
  return (
    <>
      <Context.Provider value={{ title, content, handleClick }}>
        <SubContainer />
      </Context.Provider>
    </>
  )
}
{% endraw %}
```
value로는 넘겨줄 값을 넣으면 됩니다.
<br />

***
## Context 접근
Provider로 저장한 전역 데이터를 하위 컴포넌트에서 접근 가능하게 했습니다. 이제 SubContainer 컴포넌트의 하위 컴포넌트에서도 Context를 사용할 수 있습니다.

이제 Context에 접근할 건데, 크게 3가지 방법이 있습니다.

### Consumer로 Context 접근
먼저 `Provider`와 대응하는 `Consumer`를 이용하여 접근할 수 있습니다.

`Consumer`는 `render props`를 받기 때문에 `Title` 컴포넌트는 `children`으로 넘기는 함수의 인자로 값을 읽습니다.

`Title`을 예로 들어보겠습니다.

```jsx
function Title() {
  return (
    <Context.Consumer>
      {value => <div>{value.title}</div>}
    </Context.Consumer>
  )
}
```

### useContext로 Context 접근
일반적으로 함수형 컴포넌트에서는 `useContext`를 사용하는 것이 가장 깔끔하고 현대적인 방법입니다.

```jsx
function Title() {
  return (
    const { title } = useContext(Context)
    <div>{title}</div>
  )
}
```
~~깔끔하죠?~~

### contextType으로 Context 접근
contextType은 클래스형 컴포넌트에서만 사용이 가능합니다.

아래 코드 형식처럼, this.context에서 필요한 value를 가져와서 사용합니다.

```jsx
static contextType = Context

render() {
  const value = this.context
}
```

## 마무리
처음 만들었던 코드를 깔끔하고 현대적인 방식인 `useContext`를 사용하여 리팩토링 해보겠습니다.

```jsx
{% raw %}
const Context = createContext()

function Title() {
  const { title } = useContext(Context)
  return <h3>{title}</h3>
}

function Button() {
  const { title, handleClick} = useContext(Context)
  return <button onClick={handleClick}>{title}</button>
}

function Content() {
  const { content } = useContext(Context)
  return <p>{content}</p>
}

function SubContainer() {
  return
  <>
    <Title />
    <Content />
    <Button />
  </>
}

function Example() {
  const [title, setTitle] = useState('useContext')
  const [content, setContent] = useState('Context provides a way to pass data through the component tree without having to pass props down manually at every level')
 
  function handleClick() {
    setTitle('Changed!')
  }
  
  return (
    <>
      <Context.Provider value={{ title, content, handleClick }}>
        <SubContainer />
      </Context.Provider>
    </>
  )
}
{% endraw %}
```

코드가 훨씬 가독성이 좋아지고, 간단해진 것을 볼 수 있습니다.

## ⚠️ 주의사항
Context를 사용하게 되면 해당 컴포넌트는 해당 Context가 없이는 재사용이 어렵습니다.

때문에 꼭 본연의 용도에 맞는 경우가 아니라면 사용을 피해야 합니다.

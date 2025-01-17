---
title : "[Trouble Shooting] 리렌더링 방지를 위한 고찰"
author: potato
date : 2025-01-17 10:00:00 +0900
categories : [React, TroubleShooting]
tags: [react, hooks, javascript, troubleshooting,]
image:
  path: https://github.com/user-attachments/assets/49c1a910-a255-4467-a10c-95d5143df690
description: 나만의 책장 만들기를 통해 컴포넌트의 리렌더링 방지에 대한 고찰
---

나만의 책장 만들기라는 작은 실습을 진행했습니다. mock 데이터인 book들을 나열하고, 읽기를 클릭하면 현재 읽고 있는 책에 해당 책이 보여집니다.

React에서 **리렌더링** 이라는 개념은 중요하다고 생각해서, 컴포넌트들의 리렌더링 방지에 대한 고찰을 시작했습니다.

## 구현기능
구현 기능은 아래와 같습니다.

1. 현재 읽고있는 책 : 아래 책 리스트 중 읽고싶은 책에 읽기 버튼 클릭 시, 해당 책을 검색창 위에 노출
2. 읽고있는 책을 localStorage에 저장하여 페이지 새로고침 시에도 노출
3. 책 검색창 : 책 검색 시 실시간 검색 가능
4. 책 리스트 : 내가 가진 모든 책들을 나열하여 표기
5. 페이지 이동 : Link를 사용하여 페이지 이동
6. 상세 페이지 : 책 리스트에서 책을 클릭하면 해당 책의 상세 페이지로 이동
7. 고객 센터 페이지 : Footer의 고객센터 클릭 시 고객센터 페이지로 이동
8. NotFound : 유효하지 않은 URL 접근 시 NotFound 페이지 표시

## 구조
폴더 구조는 아래와 같이 나눴습니다.
```
./src
├── App.css
├── App.jsx
├── components
│   ├── BookList.jsx
│   ├── BookShelves.jsx
│   ├── Container.jsx
│   ├── Creator.jsx
│   ├── Footer.jsx
│   ├── Header.jsx
│   └── SearchInput.jsx
├── context
│   ├── BookContext.jsx
│   └── SearhContext.jsx
├── hooks
│   ├── useCurrentBook.jsx
│   └── useSearchBook.jsx
├── index.css
├── layout
│   └── layout.jsx
├── main.jsx
├── mock
│   └── book.js
├── pages
│   ├── Details.jsx
│   ├── Help.jsx
│   ├── Home.jsx
│   └── NotFound.jsx
└── shared
    └── Router.jsx
```

## 📌 문제
### 1. 검색어 입력 시 입력 폼까지 리렌더링 되는 현상
저는 검색어 입력 시엔 `BookList`만 리렌더링 하고 싶었습니다. `<input>`을 입력 시마다 리렌더링 하는 것은 불필요하다고 생각했기 때문입니다.

#### 시도 및 해결
이 문제는 간단하게 `memo`를 사용해서 방지해줄 수 있었습니다.

```jsx
export default memo(SearchInput)
```

또는 이런 방법도 있더군요.

```jsx
import { SearchContext } from '@/context/SearhContext'

const SearchInput = () => {

  return (
    <>
      <SearchContext.Consumer>
        {(context) => (
          <input placeholder='검색' onChange={(e) => context.searching(e.target.value)} />
        )}
      </SearchContext.Consumer>
    </>
  )
}

export default SearchInput
```
<br />

### 2. 읽기 버튼으로 상태 변경 시 부모 컴포넌트 리렌더링

제가 의도한 바와 다르게 동작되는 부분은 `Context`로 감싸준 `BookShelves` 컴포넌트에서 발생했습니다.

```jsx
const Home = () => {
  return (
    <BookProvider>
      <SearchProvider>
        <Container type='bookshelves'></Container>
      </SearchProvider>
    </BookProvider>
    <Container type='creator'></Container>
  )
}
```

제 파일 구조를 보면 `BookShelves` 컴포넌트 안에 `BookList`와 `SearchInput` 컴포넌트가 존재합니다.

그리고 `BookProvider`는 `currentBook` 현재 읽고 있는 책의 상태를, `SearchProvider`는 `searchBook` 검색어의 상태를 관리합니다.

`Home` 페이지에서, `BookShelves`와 `Creator` 컴포넌트를 Provider로 감싸다 보니, 자식인 `BookList`에서 읽기 버튼을 클릭 했을 때, currentBook의 상태가 변경되며 상태를 공유받고 있는 부모인 `BookShelves` 까지 리렌더링 되는 현상이 일어났습니다.

제가 원하는 것은 `BookShelves` 컴포넌트의 리렌더링이 아닌
1. 읽기 버튼 클릭 시 `현재 읽고 있는 책` 부분만 리렌더링 되는 것

혹은

2. `현재 읽고 있는 책` + `BookList` 리렌더링이었습니다.

일단 `BookShevles` 컴포넌트만 리렌더링 되지 않으면 된단 생각이었습니다. 제가 원하는 건 읽기 버튼을 클릭했을 때 현재 읽고 있는 책 부분의 변경뿐이니까요.

#### 시도 1
_Consumer로 해당 부분만 구독시키면 되지 않을까?_
SearchInput 컴포넌트를 Consumer로 감싸줬던 것처럼 시도해봤습니다.

```jsx
<BookContext.Consumer>
  {(saved) => <div>현재 읽고 있는 책 : {saved.currentBook?.title || '없음'}</div>}
</BookContext.Consumer>
```
![Image](https://github.com/user-attachments/assets/c312a3ab-f411-426a-bb9e-37b9d9ae710b)

하지만 여전히 리렌더링이 일어나는 걸 확인할 수 있었습니다.


#### 시도 2
뭐가 문제일까... Consumer는 그대로 둔 채 BookList의 코드를 한참 들여다 봤습니다.

```jsx
const { filteredBooks } = useContext(SearchContext)
const { setCurrentBook } = useContext(BookContext)

function savedCurrentBook(book) {
  setCurrentBook(book)
  localStorage.setItem('currentBook', book.title)
}
```

버튼 클릭을 하면 savedCurrentBook이 호출 → setCurrentBook으로 curretBook 상태를 변경 → 상태 변경을 감지한 Context가 구독하고 있는 모든 컴포넌트를 리렌더링

이 방식이 부모 컴포넌트까지 리렌더링 하고 있구나! 싶어 변경했습니다.

setCurrent를 바로 호출하는 방식이 아니라, 해당 savedCurrentBook을 BookContext로 옮겨주고, 상태 변경하는 함수로 전달하게 했습니다.

```jsx
// BookContext.jsx
const savedCurrentBook = (book) => {
  const savedBookTitle = localStorage.getItem('currentBook')

  // 이미 같은 책이 저장되어 있다면 상태 변경 및 리렌더링 방지
  if (savedBookTitle === book.title) return

  setCurrentBook(book)
  localStorage.setItem('currentBook', book.title)
}
```

여전히 리렌더링이 일어납니다.


#### 시도 3
이번엔 리렌더링이 일어나지 말아야할 부분인 BookShelves를 봤습니다.

BookShelves 컴포넌트 안에 `useEffect`로 눈을 돌렸습니다.

```jsx
useEffect(() => {
  const savedBookTitle = localStorage.getItem('currentBook')
  if (savedBookTitle) {
    const book = books.find((b) => b.title === savedBookTitle)
    if (book) setCurrentBook(book)
  }
}, [])
```

처음 BookShelves가 그려진 후, 로컬스토리지에서 아이템을 가져와 DOM을 업데이트 합니다.

해당 로직을 currentBook의 useState 초기 값으로 이동시켰습니다.

이 부분이 문제였던 것 같습니다. //왜인지는 나중에


```jsx
// BookProvider.jsx
const [currentBook, setCurrentBook] = useState(() => {
  const savedBookTitle = localStorage.getItem('currentBook')
  return savedBookTitle ? { title: savedBookTitle } : null
})
```
![Image](https://github.com/user-attachments/assets/b9536538-c38a-4e36-a328-3f42a4b6e0b0)
_읽기 클릭 시에도 BookList만 리렌더링_


### 3. 버튼 클릭 시 왜 BookList가 리렌더링 될까?
`BookList`가 리렌더링되는 이유는 `useCurrentBook` 훅을 통해 `BookContext`를 구독하고 있기 때문입니다.

현재 구조에서,   

1. 버튼 클릭 → `savedCurrentBook` 호출
2. BookContext의 currentBook 값 변경
3. 이 Context를 구독하는 모든 컴포넌트 리렌더링
- BookShelves의 Consumer 부분
- `useCurrentBook`을 사용하는 BookList 컴포넌트

#### 시도
현재 BookList가 BookContext와 SearchContext 모두 구독하고 있기 때문에, 리렌더링이 일어날 수 밖에 없습니다.

➡️ 따라서 BookContext를 구독하는 부분과 SearchContext를 구독하는 부분을 나누고자 했습니다.   
`BookList`는 useSearch 훅의 filteredBooks만 받아오고, `BookItem`은 useCurrentBook의 savedCurrentBook만 받아오도록요.

하지만 그렇게 나누고 나니, BookList와 BookItem n개가 좌르륵 리렌더링 됐습니다. 여전히 BookItem에서 useCurrentBook을 사용하고 있기 때문입니다.

그래서 Parent와 Children에 관해 생각하는 도중, [해당 블로그](https://velog.io/@jingjing2222/Children-Component-톺아보기)를 보게 됐습니다.

해당 글에서는,

```
<Parent>{children}<Parent/> = <Parent children={<Child />} = <Parent><Child /></Parent>
```
children으로 `<Child />`을 전달하면, 이 `<Child />`의 React Element는 `Parent`가 리렌더링 되더라도 새로운 객체로 생성되지 않고, 기존 객체를 재사용한다.
> children으로 전달된 컴포넌트가 재렌더링되지 않는 이유는 React가 JSX 내부에서 생성된 React Element를 메모이제이션(Memoization)하기 때문
{: .prompt-info }

사진을 인용하여 보자면 이렇습니다.

![Image](https://github.com/user-attachments/assets/5f47a02b-7064-41e5-8c31-b819db154cc0)

결론은 Parent의 상태 변경으로 인한 리렌더링이라도, **Child를 props로 받는다면 Child의 속성이나 상태가 변경되지 않는 한 Child의 리렌더링은 일어나지 않습니다.**

#### ❓❓ 해결인가?

다는 아니었습니다. 제 BookList는 BookItem을 return 하고 있었기 때문에, `<Parent><Child/></Parent>`의 구조가 아니었기 때문입니다.

그래서 전체를 렌더링하고 있는 BookShelves 컴포넌트로 갔습니다.
```jsx
{% raw %}
// BookShelves.jsx
const BookShelves = () => {
  console.log('[BookShelves] - rerender')

  return (
    <>
      <h3>나만의 책장</h3>
      <BookContext.Consumer>
        {(saved) => <div>현재 읽고 있는 책 : {saved.currentBook?.title || '없음'}</div>}
      </BookContext.Consumer>
      <SearchInput />
      <BookList>
        <BookItem />
      </BookList>
    </>
  )
}

export default BookShelves
{% endraw %}
```
```jsx
{% raw %}
// BookList.jsx
const BookList = ({ children }) => {
  console.log('[BookList] -rerender')

  return <div>{children}</div>
}

export default BookList
{% endraw %}
```
```jsx
{% raw %}
// BookItem.jsx
const BookItem = () => {
  console.log('[BookItem] -rerender')
  const { savedCurrentBook } = useCurrentBook()
  const { filteredBooks } = useSearch()

  return (
    <>
      {filteredBooks.map((book) => (
        <div key={book.id}>
          <Link to={`/details/${book.id}`}>
            <span>
              {book.title} - {book.author}
            </span>
          </Link>
          <button
            style={{ padding: '0.2rem 0.4rem', marginLeft: 4 }}
            onClick={() => savedCurrentBook({ title: book.title })}
          >
            읽기
          </button>
        </div>
      ))}
    </>
  )
}

export default memo(BookItem)
{% endraw %}
```
이렇게 해주니 버튼 클릭시 해당 Item 컴포넌트만 리렌더링 되었습니다!

#### 다른 방법 (useCallback, memo)
```jsx
{% raw %}
// BookList.jsx
const BookList = () => {
  console.log('[BookList] -rerender')
  const { filteredBooks } = useSearch()
  const { savedCurrentBook } = useCurrentBook()

  const handleSave = useCallback(savedCurrentBook, [])

  return (
    <>
      {filteredBooks.map((book) => (
        <BookItem key={book.id} book={book} savedCurrentBook={handleSave} />
      ))}
    </>
  )
}

//BookItem.jsx
const BookItem = ({ book, savedCurrentBook }) => {
  console.log('[BookItem] -rerender')
  return (
    <div>
      <Link to={`/details/${book.id}`}>
        <span>
          {book.title} - {book.author}
        </span>
      </Link>
      <button
        style={{ padding: '0.2rem 0.4rem', marginLeft: 4 }}
        onClick={() => savedCurrentBook({ title: book.title })}
      >
        읽기
      </button>
    </div>
  )
}

export default memo(BookItem)
{% endraw %}
```
이렇게 currentBook 값을 바꾸는 savedCurrentBook 함수와 BookItem 컴포넌트를 메모제이션 하는 것입니다.

BookList는 리렌더링 되지만, BookItem은 리렌더링 되지 않습니다. (Props가 변경되지 않으므로)

## 마무리
기능 동작은 잘 하던 걸 **최적화 시켜볼까? 리렌더링을 좀 방지해볼까? 메모는 언제 써야할까? 아, 이 기능도 써보고 싶은데!** 라며 리팩토링을 시작했었습니다.

고민을 너무 깊게 하다보니 오히려 더 꼬이는 기분이었습니다. ~~리렌더링 어려워~~   
코드 리뷰 및 질문을 하려는데, 커밋도 기능단위로 나누지 않고 싹 바꿔놓곤 헤헤! 다했다! 하고 커밋해버려서... 보기에도 어렵고 질문하기에도 어려운 커밋이 되어버렸습니다...

하지만 공부를 더 깊게 한 것 같아 재미는 있었습니다... 이렇게 배워가는 거겠죠...


전체 코드는 해당 링크에서 보실 수 있습니다.   
[나만의 책장 만들기](https://github.com/oding01/react-bookshelves)
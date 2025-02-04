---
title : "[CSS] header가 잘리는 현상"
author: potato
date : 2025-02-04 15:30:00 +0900
categories : [React, TroubleShooting]
tags: [react, hooks, javascript, troubleshooting,]
image:
  path: https://github.com/user-attachments/assets/cc584f76-b207-47df-aae1-9605a42af368
description: header가 전체를 차지하지 못하고 잘리는 현상
---

## 문제 - 1
만든 헤더가 화면이 커졌을 때, 전체를 차지하지 못하고 잘리는 현상이 일어났습니다.

![Image](https://github.com/user-attachments/assets/e7c2d9cc-1d7e-4ffd-9de3-9c159c3a4e55)


## 트러블슈팅

![Image](https://github.com/user-attachments/assets/a79e41a8-2c09-4ecd-b07b-7baf7cb0175c)

개발자 도구를 사용하여 문제가 생기는 부분을 찾아보니, root에 이렇게 마진이 들어간 것을 확인할 수 있었습니다.

`#root`의 css는 다음과 같습니다.
```css
#root {
	max-width: 1500px;
	margin: 0 auto;
	text-align: center;
}
```

왜... 왜 max-width를 잡아놨던 걸까요...? 개발자 도구에서 이걸 삭제해주니 제대로 화면을 차지하는 것을 볼 수 있었습니다.

내친 김에 화면 크기를 3000으로 더 늘려봤습니다.

## 문제 - 2

![Image](https://github.com/user-attachments/assets/6c90a46d-1c00-4149-9789-5cf90e5a3e88)

크기가 높이에 맞게 늘어나지 않고 고정되어 있습니다. 저는 커진 화면만큼 Outlet 부분이 맞춰지길 바랬는데요.

조금 수정한 원래 코드는 아래와 같습니다. 모든 페이지에서 헤더를 주고 싶어서 Layout으로 감쌌습니다.

```tsx
const Layout = () => {
	return (
		<div className='flex flex-col w-full items-center relative'>
			<Header />
			<div className='w-full pt-16'>
				<Outlet />
			</div>
		</div>
	)
}
```

`min-h-screen`과 `min-w-full`을 추가해봤습니다.

- `min-w-full` : 최소 너비를 부모 요소의 100%로 설정합니다. 즉, 적어도 부모 요소만큼의 너비는 무조건 가지게 됩니다.

  - `w-full과의 차이점` : w-full은 너비를 부모의 100%로 "고정"하지만, min-w-full은 최소 너비만 100%로 설정하고 컨텐츠가 더 크다면 그만큼 늘어날 수 있습니다.


- `min-h-screen` : 최소 높이를 viewport(화면) 높이의 100%로 설정합니다. 즉, 적어도 화면 높이만큼은 무조건 차지하게 됩니다.

  - `h-screen과의 차이점` : h-screen은 높이를 viewport 높이로 "고정"하지만, min-h-screen은 최소 높이만 viewport 높이로 설정하고 컨텐츠가 더 크다면 그만큼 늘어날 수 있습니다.

```tsx
const Layout = () => {
  return (
    // min-h-screen을 추가해서 최소한 화면 높이만큼은 차지하도록
    // min-w-full을 추가해서 화면 너비를 모두 차지하도록
    <div className='flex flex-col min-h-screen min-w-full items-center relative'>
      <Header />
      <div className='w-full pt-16'>
        <Outlet />
      </div>
    </div>
  )
}
```

![Image](https://github.com/user-attachments/assets/31347e20-f9fe-43ba-8db9-da71873c7b39)

제가 원하던 것과는 조금 달랐습니다. 요소의 크기가 화면에 딱 맞았으면 좋겠습니다.

## 트러블슈팅 - 2
저 부분을 채울 `flex-1` 이 생각났습니다. `Outlet`을 감싸는 `div`에 `flex-1`을 사용해서 Header를 제외한 남은 공간을 모두 채우도록 해봤습니다.

```tsx
const Layout = () => {
	return (
		<div className='flex flex-col min-h-screen min-w-full items-center relative'>
			<Header />
			<div className='w-full pt-16 flex-1'>
				<Outlet />
			</div>
		</div>
	)
}
```

![Image](https://github.com/user-attachments/assets/8354445d-d074-40b4-9590-54f07286e86d)

제대로 채워주게 되었습니다!

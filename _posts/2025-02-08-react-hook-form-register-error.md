---
title : "react-hook-form & forwardRef"
author: potato
date : 2025-02-08 01:18:00 +0900
categories : [React, TroubleShooting]
tags: [react, javascript, typescript, troubleshooting,]
image:
  path: https://github.com/user-attachments/assets/cc584f76-b207-47df-aae1-9605a42af368
description: react-hook-form 과 컴포넌트 분리에 따른 register의 forwardRef 에러
---

> 가계부 프로젝트 중 일어난 오류의 트러블슈팅입니다.

react-hook-form은 한 번 사용해보고 싶었기도 하고, 프로젝트를 같이 진행하는 팀원의 추천을 받아 사용하게 되었습니다.

> React 애플리케이션에서 폼을 쉽게 관리하기 위한 라이브러리 중 하나입니다. 이 라이브러리는 더 나은 성능과 사용자 경험을 제공하며, React 컴포넌트의 상태 및 라이프사이클을 활용하여 폼 상태를 관리합니다.
ref를 이용한 비제어 컴포넌트방식을 이용해 어떠한 값을 입력할 때에 리렌더링의 횟수를 줄여줍니다. 이는 제어형 컴포넌트에 비해 빠른 마운트 속도를 보여줍니다.
{: .prompt-info }

`watch`, `getValues` 등의 여러 함수를 통해 상태관리를 쉽게 관리할 수 있다는 점이 매력적이었습니다.

수입과 지출을 입력할 수 있는 입력 페이지의 form을 만들다가 일어난 오류입니다.
![Image](https://github.com/user-attachments/assets/65476cca-b34b-4c10-ac8d-e60628a450cc)

해당 페이지의 입력 form을 아래 구조와 같이 분리했습니다.
```
src/components/Input
├── CategorySelect.tsx
├── InputContainer.tsx
├── InputField.tsx
├── InputForm.tsx
└── InputTypeToggle.tsx
```
**InputTypeToggle**
: 수입 지출을 선택할 수 있는 컴포넌트

**InputField**
: 사용 금액, 사용처, 카테고리, 사용한 날짜, 메모 Input을 만들기 위한 컴포넌트

**CategorySelect**
: 카테고리 드롭다운을 구현한 컴포넌트

**InputForm**
: InputTypeToggle, InputField, CategorySelect 컴포넌트의 값을 제출하기 위한 Form

**InputContainer**
: 최상위 부모 컨테이너 (흰색 박스)


## 문제
### 문제 코드
```tsx
...
<form onSubmit={onSubmit}>
  <InputTypeToggle
    inputType={inputType}
    onTypeChange={handleTypeChange}
  />

  <div className='flex w-full h-full gap-14 tablet:flex-row'>
    <div className='flex flex-col flex-1'>
      <InputField label='사용 금액' unit='원' type='number' {...register('amount')} />
      <InputField label='사용처' {...register('place')} />
      <CategorySelect
        selected={categories[0]}
        onChange={handleCategoryChange}
        options={categories}
      />
    </div>
    <div className='flex flex-col flex-1'>
      <InputField label='사용한 날짜' type='date' {...register('date')} />
      <InputField label='메모' tagName='textarea' {...register('memo')} />
    </div>
  </div>
</form>
...
```
저는 중복되는 코드를 줄여 가독성과 재사용성을 높이기 위해 컴포넌트를 분리했습니다. 그리고 react-hook-form의 register 기능을 사용하려고 했습니다.

코드는 문제 없이 작동하는 듯 했습니다. 하지만...

![Image](https://github.com/user-attachments/assets/c1e75b58-9be9-4183-82ba-2471868cda85)
_아 안사요_

콘솔을 확인하니 함수 컴포넌트는 refs 를 받을 수 없다는 Warning이 발생했습니다.

react-hook-form 을 처음 사용해 이해가 부족한 상황이라, 바로 구글링을 시작했습니다.

## 해결
[react-hook-form & Input with forwardRef](https://velog.io/@xowns3213/react-hook-form-with-forwardRef)

[React 공식 사이트](https://react.dev/reference/react/forwardRef)

해당 블로그와 공식 사이트가 큰 도움이 되었습니다.

register에는 onChange, required 등과 함께 ref도 포함하고 있습니다.
결국 저렇게 작성을 하면 ref를 props로 넘기는 것이고 위의 에러가 발생되는 것입니다.

> React 19 버전에서는 더 이상 `forwardRef` 가 필요하지 않다라고 명시되어 있지만, 저는 18 버전을 사용하기 때문에 `forwardRef` 를 통해 ref 를 전달해주어야 합니다.

저는 `InputForm` 컴포넌트에서 register 함수를 자식에게 넘겨주어 사용하게 하려고 합니다.

즉 `register` 함수를 `<input>` DOM 노드에 직접 사용해야하기 때문에, 부모 컴포넌트에 DOM 노드를 노출시켜야 합니다.

따라서 `register` 함수를 받아 사용해야 하는 컴포넌트에 `forwardRef` 를 추가해줍니다.

### 수정된 코드
```tsx
...
interface InputFieldProps
	extends React.InputHTMLAttributes<HTMLInputElement | HTMLTextAreaElement> {
	label: string
	unit?: string
	type?: string
	tagName?: 'input' | 'textarea'
}

const InputField = React.forwardRef<
	HTMLInputElement | HTMLTextAreaElement,
	InputFieldProps
>(({ label, unit, type = 'text', tagName }, ref) => {
	return (
		<div className={`mb-11 ${tagName === 'textarea' && 'flex-1'}`}>
			<div className='flex justify-between items-center mb-2'>
				<label className='text-2xl font-medium ml-0.5'>{label}</label>
				{unit && <label>({unit})</label>}
			</div>
			<div
				className={`relative flex rounded-[15px] w-full bg-[#F7F7F8] shadow-analyze-box tablet:flex-row flex-1 ${tagName === 'textarea' ? 'h-full' : 'h-16'}`}
			>
				{tagName === 'textarea' ? (
					<textarea
						className='text-xl px-5 py-5 w-full rounded-[15px] focus:ring-2 focus:ring-inset focus:ring-[#5FB1FF] focus:outline-none bg-transparent'
						ref={ref as React.Ref<HTMLTextAreaElement>}
					/>
				) : (
					<input
						type={type}
						className='text-xl px-5 w-full rounded-[15px] focus:ring-2 focus:ring-inset focus:ring-[#5FB1FF] focus:outline-none bg-transparent'
						ref={ref as React.Ref<HTMLInputElement>}
					/>
				)}
			</div>
		</div>
	)
})
...
```

React 18에서 ref 를 전달할 때는 `forwardRef` 를 꼭 사용하도록 하겠읍니다...
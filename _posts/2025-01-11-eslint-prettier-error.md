---
title : "[VSCode] Eslint&Prettier 설정 시 Delete `␍` eslint(prettier/prettier) 에러 해결"
author : potato
date : 2025-01-11 08:50:00 +0900
categories : [VSCode, TroubleShooting]
tags: [vscode, troubleshooting]
image:
  path: https://github.com/user-attachments/assets/cc584f76-b207-47df-aae1-9605a42af368
  alt: 썸네일
---

## ⚠️ 문제
```javascript
import React from 'react';

function LoginPage() {
	return (
		<div>
			<div>LoginPage</div>
		</div>
	);
}

export default LoginPage;
```

![](https://velog.velcdn.com/images/oding90/post/28c0db9f-0bf2-43f1-a138-5e3132c45ac7/image.png)


eslint와 prettier 설정 후 간단한 테스트 컴포넌트를 만들어보는데 이런 오류가 떴다.

> Delete '␍' eslint(prettier/prettier)

엥 이게 뭐지? 기본 index 페이지에서는 뜨지 않는 오류였다.

## 🎈생각
1. eslint(prettier/prettier) 에서 오류가 났으니 `.eslintrc.js`(json을 js로 바꿨다) 설정에서 뭔가 잘못된 게 아닐까?   
-> 코드를 뚫어져라 쳐다보다 `login.jsx` 파일의 코드는 tabWidth가 굉장히 띄워져있는 걸 찾았다. eslintrc 파일에는 2로 해놨는데..
2. `Ctrl+.`으로 에러발생시 없는 모듈 자동으로 찾아주기 시도   
-> 실패
3. 개발자의 기본 소양... 구글링. 설정법이 잘못된 것 같으니 에러 메세지를 찾아보자.
<br>

***
## ✨해결
위 오류는 windows에서 발생하는 오류로, 
prettier의 **기본 라인 개행 방식(lf)이 windows의 개행 방식(crlf)과 다르기 때문**에 발생한다.   
나는 별도의 .eslintrc 파일이 있었기 때문에 eslint 설정에서 prettier의 개행 방식을 auto로 변경해주는 방식을 시도했다.

```cjs
module.exports = {
	...
	plugins: ['prettier'],
	rules: {
		...
		'prettier/prettier': [
			'error',
			// 아래 규칙들은 개인 선호에 따라 prettier 문법 적용
			// https://prettier.io/docs/en/options.html
			{
				singleQuote: true,
				semi: true,
				useTabs: true,
				tabWidth: 2,
				trailingComma: 'all',
				printWidth: 80,
				bracketSpacing: true,
				arrowParens: 'avoid',
				endOfLine: 'auto', // 요 라인 추가
			},
		],
	},
};
```
`endOfLine: 'auto'`를 추가하자마자 빨간 줄이 사라졌다! 역시 구글링의 힘은 대단하다.
<br/>

***
[참고(감사합니다!)](https://guiyomi.tistory.com/134)
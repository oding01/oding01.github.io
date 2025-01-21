---
title : "[Typescript] main.jsx를 main.tsx로 변경 시 에러"
author: potato
date : 2025-01-20 15:45:00 +0900
categories : [React, TroubleShooting]
tags: [react, hooks, javascript, troubleshooting,]
image:
  path: https://github.com/user-attachments/assets/cc584f76-b207-47df-aae1-9605a42af368
description: tsx 확장자로 변경 시 에러 트러블 슈팅
---

## 문제

나만의 책장 만들기 프로젝트를 타입스크립트를 배워 마이그레이션 해보려고 했습니다.

모든 파일의 확장자 명을 `.tsx`로 변경 `npm run dev`를 실행하여 확인하려 했습니다.

> [vite] Pre-transform error: Failed to load url /src/main.jsx (resolved id: /src/main.jsx). Does the file exist?

하지만 해당 오류가 발생하여 제대로 렌더되지 않았습니다.

## 해결

[스택오버플로우](https://stackoverflow.com/questions/76212719/failed-to-load-url-src-main-jsx-resolved-id-src-main-jsx-does-the-file-e)의 해당 글을 참고하여, `index.html`의
```html
<script type="module" src="/src/main.jsx"></script>
```
부분의 `main.jsx`를 `main.tsx`로 변경해주었습니다.

## 또 다른 문제
아예 오류를 뱉는 현상은 사라졌지만 페이지는 흰 화면만 띄우고 있어, 콘솔창을 확인했습니다.

![Image](https://github.com/user-attachments/assets/e21f2e1e-9fea-4a6a-bbbf-f9491f4b5bdc)

## 해결
문제는 vite와 typescript를 함께 사용하는 상황에서, 제가 `tsc -w` 명령어로 ts 파일을 js 파일로 자동 컴파일 해주도록 했던 부분입니다.

Vite는 자체적으로 esbuild를 사용해서 Typescript를 처리하기 때문에, 별도의 tsc 컴파일이 필요하지 않습니다. 때문에 제가 자동 컴파일을 함으로써 Vite가 혼란스러워 했던 것이죠.

결과적으로 새로 생성된 js 파일들을 다 삭제하고 나니 제대로 렌더되는 것을 확인할 수 있었습니다.
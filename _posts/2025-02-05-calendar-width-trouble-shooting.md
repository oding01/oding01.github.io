---
title : "[CSS] Calendar가 부모 요소를 벗어나 overflow 되는 현상"
author: potato
date : 2025-02-05 14:56:00 +0900
categories : [React, TroubleShooting]
tags: [react, javascript, typescript, troubleshooting,]
image:
  path: https://github.com/user-attachments/assets/cc584f76-b207-47df-aae1-9605a42af368
description: 작은 모바일 화면에서 Calendar가 부모 요소를 벗어나 overflow되어 스크롤이 생겨버리는 상황
---

모바일 청첩장 프로젝트에서, 열심히 캘린더를 만들어 레포지토리에 푸쉬했습니다. 그런데...

## 문제 - 1

![Image](https://github.com/user-attachments/assets/d11ec1ec-ea5b-44af-a967-ee7acebf1b69)

이렇게 부모 컨테이너를 벗어나 overflow-scroll이 되어버리는 현상이 발생했습니다.

**전체 코드**

```tsx
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table';

const days = ['일', '월', '화', '수', '목', '금', '토'];

const CalendarContent = () => {
  const firstDay = new Date(2025, 3, 1).getDay(); // 화요일 = 2
  const lastDate = new Date(2025, 4, 0).getDate(); // 30일

  const dates = Array(firstDay)
    .fill(null)
    .concat([...Array(lastDate)].map((_, i) => i + 1));

  return (
    <div className="w-full h-full leading-9">
      <div className="font-medium text-2xl tracking-wide">2025.04.05</div>
      <div className="mb-5 tracking-wider">토요일 오후 3시</div>

      <div className="text-center px-10 pb-10">
        <Table className="">
          <TableHeader>
            <TableRow>
              {days.map((day, index) => (
                <TableHead
                  key={index}
                  className={
                    day === '일' ? 'text-[#c6472b] text-center' : 'text-center'
                  }
                >
                  {day}
                </TableHead>
              ))}
            </TableRow>
          </TableHeader>
          <TableBody>
            {Array.from({ length: Math.ceil(dates.length / 7) }, (_, week) => (
              <TableRow key={week}>
                {dates.slice(week * 7, (week + 1) * 7).map((date, i) => (
                  <TableCell
                    key={i}
                    className={
                      (i === 0 ? 'text-[#c6472b]' : '') ||
                      (date === 5 ? 'bg-[#858585] text-white rounded-full' : '')
                    }
                  >
                    {date || ''}
                  </TableCell>
                ))}
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </div>
    </div>
  );
};

export default CalendarContent;
```

문제가 되는 부분은 이 부분인 것 같습니다.

```tsx
...
<div className="text-center px-10 pb-10">
  <Table className="">
...
```

현재 shadCn의 UI 컴포넌트를 사용하고 있는데, `Table` 컴포넌트의 바깥쪽 컨테이너와 안 쪽 컨테이너를 보시면 `w-full`이 적용되어 있고, 바깥쪽에는 `overflow-auto`가 적용되어 있는 것을 볼 수 있습니다.

```tsx
const Table = React.forwardRef<
  HTMLTableElement,
  React.HTMLAttributes<HTMLTableElement>
>(({ className, ...props }, ref) => (
  <div className="w-full overflow-auto">
    <table
      ref={ref}
      className={cn("w-full caption-bottom text-sm", className)}
      {...props}
    />
  </div>
))
Table.displayName = "Table"
```

## 트러블 슈팅 - 1
![Image](https://github.com/user-attachments/assets/a2c9d67f-0470-4726-8272-f907e55083c5)

부모 컨테이너의 너비를 벗어나고 있는 애는 `table` 태그 요녀석이었습니다.

이전 코드를 보니 `px-10`으로 왼쪽과 오른쪽에 패딩을 주고 있었는데, 이 패딩값과 테이블의 전체 너비가 맞지 않아서 발생하는 문제일 수 있겠다라는 생각을 했습니다.

그래서 `px-10`을 `px-4`로 줄여보고, 부모 너비를 넘지 않는 선에서 자신의 컨텐츠 크기만큼 차지하게 하기 위해 테이블 태그에 `max-w-full`을 적용해보았습니다.

> `max-w-full`
: `max-width: 100%` 를 적용해주는 Tailwind 클래스입니다. 이 속성은 요소가 부모 컨테이너의 너비보다 커지는 것을 방지해줍니다.
즉, 요소가 부모보다 커지려고 하면 부모 width 만큼만 커지도록 제한하고, 요소가 부모보다 작다면 요소의 원래 크기를 유지합니다.
{: .prompt-tip }

## 결과 - 1
여전히 overflow되어 scroll이 생겨있었습니다... 그리고 원래 요소의 크기를 유지하다보니 화면의 너비가 커지면 왼쪽에 치우쳤습니다.

![Image](https://github.com/user-attachments/assets/b5ff3e7b-30fa-478e-969b-36d9ff6f84b4)

## 트러블 슈팅 - 2
치우쳤다면.. 가운데에 놓으면 되지 않나!

`table` 태그를 감싸고 있던 `div` 컨테이너에 `flex justify-center` 를 추가하고, 스크롤이 생기는 것을 방지하기 위해 `overflow-auto`를 지워줍니다.

`table` 태그의 `max-w-full`은 다시 부모 컨테이너의 100%를 유지하기 위해 `w-full`로 돌려놔주고, `justify-center`로 인해 남는 공간을 모두 차지하기 위해 `flex-1`을 추가합니다.

## 최종 결과
![Image](https://github.com/user-attachments/assets/9f008f0c-3927-4666-a7e2-fdcc34720242)

가로 길이가 제일 작았던 Galaxy Z Fold 5 (344px) 에서도 스크롤과 잘림 없이 제대로 가운데에 위치해있는 걸 볼 수 있었습니다! 성공!
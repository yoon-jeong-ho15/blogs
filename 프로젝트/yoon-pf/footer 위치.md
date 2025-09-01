---
date: 2025-07-29
tags:
  - CSS
  - layout
  - flex
title: Footer 위치 고정하기
index:
---
# 문제 상황

블로그 글을 조회할 때 footer가 글 한가운데에 위치하는 오류가 있다.
조회 페이지에서만 이런 오류가 발견된다.

# 원인
```tsx
<article
  className="h-190 ml-20 mr-25 mt-5 prose prose-lg prose-blog max-w-none"
  dangerouslySetInnerHTML={{ __html: contentHtml }}
></article>
```
이렇게 고정 높이를 줘서 그렇다고 한다.

```Tsx
//main의 layout
<>
  <Navbar />
  <div className="flex-grow">{children}</div>
  <Footer />
</>

//이것의 부모가 되는 root layout에서는
<html>
  <body
    className={`${nanumGothic.className} 
    antialiased flex flex-col max-w-screen min-h-screen
    overflow-y-scroll `}
  >
    {children}
  </body>
</html>
```
main layout을 보면 페이지 내용을 담을 div에 `flex-grow`를 줬다.
root layout을 보면 body의 높이는 스크린 높이로 지정했다. (여기까지는 틀린게 없음)

## flex-grow의 레이아웃 계산 방법
flex-grow 를 가지는 컨테이너는 먼저 자식 요소들의 기본 크기를 계산하고,  
전체 가능한 공간 - 자식 요소들의 합 = 나머지 공간  
계산한 나머지 공간을 자기가 가져간다.  
예를들어 1000px에서 Naver와 Footer가 50씩 차지하고 자식의 크기가 총합 500이라고 하면 1000 - 50 - 50 - 500 = 400의 나머지가 생기고,  
이 나머지를 flex-grow 컨테이너가 가져간다.  
그래서 자식 요소들의 크기가 줄어도 최소 높이를 보장하는 것이다.  
  
그런데 자식에게 고정높이를 주게되면, flex-grow가 사전에 계산을 해버린다. 실제 화면에 보여질 내용들의 크기가 아니라 css에 작성되어있는 숫자로 미리 계산을 해버리고 그것에 맞게 자기 높이를 계산해버리기 때문에 사실상 flex-grow를 잘 사용하려면 **자식에게 고정크기를 주지 않는게** 더 좋은 방법.

# 해결
간단히 고정높이를 빼고, 부모에 마진을 줘서 크기를 조절했다.
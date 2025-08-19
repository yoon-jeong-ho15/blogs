---
date: 2025-08-19
tags:
  - remark
  - remark-breaks
---
# 문제상황
reamark 라이브러리를 사용해 md파일을 html형식으로 전환해서 블로그 글을 보여준다.
근데 md 파일로는 다음처럼 줄바꿈이 들어가있는데 html로는 `<p>`태그 하나에 통째로 줄바꿈 없이 들어가서 보기에 불편한 문제가 있다.
```
# 원인
`write/page.tsx`를 프리렌더링 할 수 없어서 발생하는 에러.
페이지를 미리 만들어 둘 때 거기서 불러온 quill 라이브러리가 `document`객체를 사용하는 DOM API를 필요로 한다. (가령 quill 라이브러리 안에서 `document.getElementById` 와 같은 자바스크립트 코드를 사용할 수 있다)
즉 quill을 실질적으로 사용하는 `editor.tsx`에서 퀼 라이브러리를 불러와서 문제가 되는것.
`write/page.tsx`에서도 quill를 불러오지만 여기서 객체를 생성하지는 않았기 때문에 문제가 되지 않았다.
```

```html
<code>write/page.tsx</code>를 프리렌더링 할 수 없어서 발생하는 에러.
페이지를 미리 만들어 둘 때 거기서 불러온 quill 라이브러리가 <code>document</code>객체를 사용하는 DOM API를 필요로 한다. (가령 quill 라이브러리 안에서 <code>document.getElementById</code> 와 같은 자바스크립트 코드를 사용할 수 있다)
즉 quill을 실질적으로 사용하는 <code>editor.tsx</code>에서 퀼 라이브러리를 불러와서 문제가 되는것.
<code>write/page.tsx</code>에서도 quill를 불러오지만 여기서 객체를 생성하지는 않았기 때문에 문제가 되지 않았다.
```
# 원인
html에서 태그 안의 문자열들이 있을때 `<br />`이 없는 그냥 '엔터'(`\n`) 를 통한 줄바꿈은 띄어쓰기 한칸으로 인식하기 때문인거 같다.

# 해결
찾아보니까 `remark-breaks`라는 정확하게 이런 문제를 해결하기 위한 라이브러리가 있다고 한다.
```ts
import breaks from "remark-breaks";

  const processedContent = await remark()
    .use(html)
    .use(breaks)
    .process(matterResult.content);
```
기존의 `.use(html).process(matterResult.content)`사이에 `.user(breaks)`를 추가해서 간단히 해결했다.


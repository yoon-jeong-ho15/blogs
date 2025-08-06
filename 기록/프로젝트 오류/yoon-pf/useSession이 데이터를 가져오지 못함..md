---
tags:
  - NextAuth
  - useSession
---

# 문제 상황
## 에러문
```
ClientFetchError: JSON.parse: unexpected character at line 1 column 1 of the JSON data. Read more at [https://errors.authjs.dev#autherror](https://errors.authjs.dev/#autherror)
```

서버 컴포넌트인 `Page`들에서 각각 `const session = await auth()` 를 통해 세션을 가져오는게 가능했다. (이 `auth()`는 `auth.ts`에 있는것)
근데 각 페이지들 안에 있는 클라이언트 컴포넌트들 안에서 `useSession()`을 사용해서 세션 데이터를 가져오려니까 계속 에러가 발생.

# 원인

# 해결
```ts
import { handlers } from "@/auth";
export const { GET, POST } = handlers;
```
이게 없어서 `useSession()` 이 계속 "GET /api/auth/session 404 in 146ms" 404 에러가 뜬것.

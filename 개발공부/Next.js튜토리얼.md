---
title: Next.js 튜토리얼
index:
date: 2025-09-23
tags:
  - NextJs
---
[https://nextjs.org/learn/dashboard-app](https://nextjs.org/learn/dashboard-app)
공식 튜토리얼을 한번 읽어보기로 한다. 
# 1. Getting Started
`/app` : 페이지, 컴포넌트, 로직 등 애플리케이션에 필요한 모든것이 들어있는 경로
`/app/lib` : 재사용할 함수들이 들어있는 경로. (데이터 패칭 등)
`/app/ui` : UI 컴포넌트들
`/public` : 정적 파일들

# 2. CSS Styling
Next.js는 테일윈드 사용을 권장한다.

*CSS모듈*이라는 것도 있는데,
```css
/*home.module.css*/
.shape {
  height: 0;
  width: 0;
  border-bottom: 30px solid black;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}
```
이렇게 작성되어있는 일반 css파일을
```tsx
import styles from "@/app/ui/home.module.css";

export default function Page(){
	return(
		<main className="flex min-h-screen flex-col p-6">
	      <div className={styles.shape}>
	        <AcmeLogo />
	      </div>
		...      
	)
}
```
이렇게 불러와서 사용할 수 있다.

퀴즈 : CSS모듈의 장점은? 
정답 : Provide a way to make CSS classes *locally scoped* to components by default, reducing the risk of *styling conflicts*.
CSS모듈은 각 컴포넌트마다 고유한 클래스 네임을 생성해 스타일링 충돌을 방지할 수 있다고 한다.


*clsx* 라이브러리도 있다.[깃헙](https://github.com/lukeed/clsx)
이 라이브러리는 조건에 따라 스타일을 주고싶을때(즉 클래스네임을 주고싶을때) 사용한다.

# 3. Optimizing Fonts and Images

# 4. Creating Layouts and Pages
# 5. Navigating Between Pages
Next.js는 기존의 `<a>` 태그 대신 `<Link>`컴포넌트를 사용하기를 권장한다.
`<Link>`컴포넌트는 [*클라이언트 사이드 네비게이션*](https://nextjs.org/docs/app/getting-started/linking-and-navigating#how-routing-and-navigation-works)을 가능하게 해주는데, 이것의 가장 큰 장점은 페이지 이동시마다 전체 페이지를 새로 렌더링 하지 않는다는 것이다.


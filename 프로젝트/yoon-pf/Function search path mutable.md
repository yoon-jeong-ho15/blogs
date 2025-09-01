---
date: 2025-08-20
tags:
  - Supabase
  - PostgreSQL
title: Function search path mutable
index:
---
참고 :
https://supabase.com/docs/guides/database/database-advisors?queryGroups=lint&lint=0011_function_search_path_mutable

# 원인
>In PostgreSQL, the `search_path` determines the order in which schemas are searched to find unqualified objects (like tables, functions, etc.)

`search_path`는테이블과 함수같은 unqualified objects들을 찾을때 어떤 스키마에서 찾을지 설정하는것이라고 한다. 사용자의 기본 `search_path`가 있다고 해도 함수를 만들때 따로 작성하는것을 권장한다고 한다.

## Search Path란?
`profiles`라는 unqualified object(테이블, 함수 등)가 있고 (`public.profiles`처럼 앞에 스키마의 이름까지 작성하면 **qualified object**가 된다.) 이걸 사용하려고 할 때 참고할 스키마의 이름들이 적힌 리스트가 `search_path`다. 
기본적으로 `search_path`는 사용자의 스키마와 public 스키마를 담고 있다. 그러나 만일의 경우에 대비해 `search_path`를 특정하는 경우가 좋다고 하는데, 왜냐하면 함수를 사용하는 사용자마다 다른 결과를 반환받을 수 있기 때문이다.
예를들어 어떤 함수 `example_function()`에서 `profiles` 테이블에서 자료를 가져와 반환하는데 `search_path`가 특정되어있지 않았다고 한다면, `sales`스키마에 속한 사용자가 함수를 호출할때와 `develpoment`스키마에 속한 사용자가 함수를 호출할때 `example_function()`은 각각 사용자들이 속한 스키마의 `profiles`테이블을 참고하게 된다.(두 스키마 모두 `profiles`라는 테이블을 가지고 있다면)

# 해결
함수를 만들 때 DDL에 다음과 같은 설정을 추가한다.
```sql
create or replace function example_function()
	returns void
	language sql
	set search_path = '' -- 여기를 추가해야 한다.
as $$
	....
$$;
```

## 빈 문자열로 설정하기
슈파베이스는 특히 `search_path = ''`로 설정하기를 권장한다고 한다.
빈 문자열로 설정하면 `search_path`를 사실상 사용하지 않는 것이고,
함수 내부에서 어떤 객체를 참조할 때 unqualified인 상태로 참조하지 못하고 반드시 스키마 명을 명시한 qualified 상태(`schema.object`)로 참조해야하기 때문에 스키마 이름을 명시해야한다.
스키마명을 명시해야되기 때문에 통해 예측 불가능한 동작undexpected behavior을 방지하고, 보안 취약성security vulnerabilities을 완화할 수 있기 때문에 권장한다고.
**명확성, 일관성, 보안성**을 높이기 위한 **방어적 프로그래밍**의 한 방법.
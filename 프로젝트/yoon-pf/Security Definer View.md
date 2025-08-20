---
date: 2025-08-20
tags:
  - Supabase
  - PostgreSQL
---
참고 :
https://supabase.com/docs/guides/database/database-advisors?queryGroups=lint&lint=0010_security_definer_view

# 원인
슈파베이스에서는 뷰를 만들면 기본적으로 SECURITY DEFINER 로 만들어진다고 한다.
포스트그레스에서 뷰는 SECURITY DEFINER 혹은 SECURITY INVOKER 설정을 가지고 만들 수 있다.

SECURITY DEFINER로 만들어진 뷰나 함수는
	- 그 사용자가 그것들을 만든 사용자의 권한을 모두 사용할 수 있다.
	- 적은 권한을 가진 사람이 많은 권한이 필요한 정보를 조회할 수 있다는 점에서 장점과 단점이 공존한다.
	- 특히 슈파베이스의 row level security를 무력화하기 때문에 강하게 권장하지 않는다고.

# 해결
뷰를 만들때 DDL에 따로 security definer가 아닌 security invoker로 설정하는 절을 넣어주어야 한다.
```sql
create view public.v_example
	with (security_invoker=on) -- 이걸 추가해 security invoker 뷰로 설정해야한다.
	as select
	....
```

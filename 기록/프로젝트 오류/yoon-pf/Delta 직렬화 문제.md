---
tags:
  - QuillJs
  - JSON
---

# 문제 상황
```tsx
const handleSave = () => {
	const content: Delta | null = quillRef.current?.getContents() || null;
	console.log("content : ", content);
	if (title && content) {
		createBoard(title, content);
	}
};
```
페이지에서 저장버튼을 누르면 위의 함수를 실행한다.

여기서 content를 찍어보면 `Object { ops: […] }` 라고 나온다.
그런데 `action.ts`에서 content를 찍어보면 `content :  [Function (anonymous)]`라고 나온다.
```ts
export async function createBoard(title: string, content: Delta | null) {
	const session = await auth();
	const user: User | null = (session?.user as User) || null;
	console.log("content : ", content);
	const { error } = await sql.from("board").insert({
		writer: user?.username,
		title: title,
		content: content,
	});
	if (error) {
		console.error("Error inserting Data : ", error);
	}
}
```

# 원인
serialization이 문제라고 한다.
클라이언트에서 서버로 객체를 전송할때 자동으로 직렬화를 해서 전달하는데,
객체가 함수를 가지고 있으면 직렬화 과정에서 문제가 발생해서 객체 그대로 전달되지 않는것 같다.

# 해결
그래서 `stringify()`로 문자열로 만들어 문자열 상태로 저장하고, 조회할때 `JSON.parse()`로 다시 객체로 만들어 화면에 보여준다.
---
tags:
  - QuillJs
date: 2025-08-06
---
# 문제 상황
## 코드
```tsx
"use client";
import Quill from "quill";
import { useEffect, useRef } from "react";
import Editor from "@/app/more/board/write/editor";

export default function EditorWrapper({
  initialValue,
}: {
  initialValue: string;
}) {
  const quillRef = useRef<Quill | null>(null);

  useEffect(() => {
    if (!quillRef.current) return;
    
    try {
      const delta = JSON.parse(initialValue);
      quillRef.current.setContents(delta);
    } catch (error) {
      console.error("Error parsing initialValue", error);
    }
  }, [quillRef.current, initialValue]);

  useEffect(() => {
    const form = document.querySelector("form");
    form?.addEventListener("submit", () => {
      if (quillRef.current) {
        const delta = quillRef.current.getContents();
        const input = document.createElement("input");
        input.type = "hidden";
        input.name = "content";
        input.value = JSON.stringify(delta);
        form.appendChild(input);
      }
    });
  });
  return <Editor ref={quillRef} />;
}

```

# 원인
왜 content가 추가가 안되느냐 하면 이벤트 리스너가  제출 이후에 등록되서 그런다고한다.
문제점 
1) 여기에 의존성 배열이 없고 
2) 이벤트 리스너를 해제하지 않고 추가하고 
3) 3)이벤트 핸들러가 너무 늦게 실행한다.

# 해결
해결책 : content를 담을 input hidden을 넣고, useState로 값을 입력해준다.
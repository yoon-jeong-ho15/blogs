---
title: BlogList 렌더링을 Link 컴포넌트로?
index: 1
date: 2025-10-17
tags:
  - Link
  - URL상태
---
블로그 페이지의 컴포넌트 구조는 이렇다. (데스크탑 기준)

```
페이지
- 카테고리 목록 CategoryTree
  - CategoryItem (재귀)
- 블로그 목록 BlogList
  - BlogItem
```

```tsx
// CategoryTree
<div className="flex flex-col w-4/12 p-2 border border-gray-200">
      {categories.map((category, i) => (
        <CategoryItem
          key={`c${i}`}
          category={category}
          selectedCategory={selectedCategory}
          level={1}
        />
      ))}
</div>

// CategoryItem
<div className="flex flex-col mx-1 border border-gray-300 py-1 rounded-xl my-1">
      <div className="flex items-center justify-between">
        <Link
          href={isSelected ? "/blog" : `/blog?category=${categoryPath}`}
          className={`
            flex-1
            rounded-xl
            hover:pl-4 transition-all
            cursor-pointer 
            py-1 pl-3
            `}
        >
          <span className={`${isSelected ? "font-bold underline" : ""}`}>
            {category.name}
          </span>
          <span className="ml-1 rounded text-xs text-gray-700">
            {category.blogs.length > 0 ? `(${category.blogs.length})` : ""}
          </span>
        </Link>

        {hasChildren && (
          <button
            onClick={() => setShowChildren(!showChildren)}
            className="px-2 text-gray-500 hover:text-gray-700"
          >
            {showChildren ? "−" : "+"}
          </button>
        )}
      </div>
      {showChildren &&
        category.children?.map((child) => (
          <CategoryItem
            key={`c${child.name}`}
            category={child}
            selectedCategory={selectedCategory}
            level={level + 1}
          />
        ))}
    </div>
```


# Query Key Factory

> TanStack Query 공식 문서 Community Resources: [Query Key Factory](https://tanstack.com/query/v4/docs/framework/react/community/lukemorales-query-key-factory)

## 개요
**타입 안전한 query key 관리**와 **자동완성**을 제공하는 라이브러리

## 사용 이유
- 특정 쿼리에 어떤 키를 썼는지 기억할 필요 없이, 쿼리 작성과 무효화(invalidation)에만 집중 가능
- 화면에 반영되지 않는 잠재적 버그를 줄임.
- 상수로 공통 관리하는 경우 계층적 구조 표현하기 어렵고 타입 안정성도 부족함.

## 설치

```bash
npm i @lukemorales/query-key-factory
```

## 사용 방법

### 1. 한 파일에 모두 선언
createQueryKeyStore 하나만 사용

```tsx
import { createQueryKeyStore } from '@lukemorales/query-key-factory'

export const queryKeys = createQueryKeyStore({
  users: null,
  todos: {
    detail: (todoId: string) => [todoId],
    list: (filters: TodoFilters) => ({
      queryKey: [{ filters }],
      queryFn: (ctx) => api.getTodos({ filters, page: ctx.pageParam }),
    }),
  },
})
```

- `null`: 단순 키(파라미터 없음)
- 함수: 파라미터를 받아 query key 배열 반환
- `queryKey` + `queryFn` 객체: 무한/페이지네이션 등 query options까지 함께 정의

### 2. 기능별로 나눠서 선언
도메인/기능별로 파일을 나누고, createQueryKeys + mergeQueryKeys 둘 다 사용 (FSD/도메인 기반 구조일 때 사용)

```tsx
import { createQueryKeys, mergeQueryKeys } from '@lukemorales/query-key-factory'

// my-api/users.ts
export const usersKeys = createQueryKeys('users')

// my-api/todos.ts
export const todosKeys = createQueryKeys('todos', {
  detail: (todoId: string) => [todoId],
  list: (filters: TodoFilters) => ({
    queryKey: [{ filters }],
    queryFn: (ctx) => api.getTodos({ filters, page: ctx.pageParam }),
  }),
})

// my-api/index.ts
export const queryKeys = mergeQueryKeys(usersKeys, todosKeys)
```

실제 사용할 때는 다음과 같이 한 객체에서 오토컴플리션 + 타입 추론을 깔끔하게 받을 수 있음.
```tsx
queries.users.detail(id)
queries.todos.list(filters)
```

## 컴포넌트에서 사용
**단일 소스**로 query key를 관리하므로, `useQuery`와 `invalidateQueries` 등에서 동일한 키를 사용

```tsx
import { queryKeys, completeTodo } from '../my-api'

export function Todo({ todoId }) {
  const queryClient = useQueryClient()

  const query = useQuery(queryKeys.todos.detail(todoId))

  const mutation = useMutation({
    mutationFn: completeTodo,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.todos.list.queryKey })
    },
  })
  // ...
}
```

- **useQuery**: `queryKeys.todos.detail(todoId)`처럼 호출해 query key 전달
- **invalidateQueries**: `queryKeys.todos.list.queryKey`로 리스트 쿼리 무효화
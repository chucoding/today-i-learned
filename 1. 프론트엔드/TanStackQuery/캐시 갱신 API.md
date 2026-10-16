# 캐시 갱신 API

invalidateQueries, resetQueries, removeQueries, clear 비교

- [API별 차이](#api별-차이)
- [뮤테이션 후 갱신 방식](#뮤테이션-후-갱신-방식)
- [낙관적 업데이트](#낙관적-업데이트)
- [removeQueries 주의점](#removequeries-주의점)
- [삭제 뮤테이션](#삭제-뮤테이션)
- [refetch와 resetQueries](#refetch와-resetqueries)
- [초기화 버튼](#초기화-버튼)
- [참고자료](#참고자료)

## API별 차이

| API | 동작 | 쓰는 때 |
| --- | --- | --- |
| `invalidateQueries` | 데이터는 남기고 stale 표시, 활성 쿼리만 재조회 | 등록, 수정, 삭제 후 관련 목록 갱신 |
| `resetQueries` | 캐시 데이터를 비우고(`initialData` 옵션이 있으면 그 값으로 교체) 활성 쿼리 재조회 | 이전 데이터를 화면에 남기면 안 될 때 |
| `removeQueries` | 캐시에서 쿼리 삭제, 삭제 시점에는 재조회 없음 | 구독하는 화면이 이미 사라진 캐시 정리 |
| `clear` | 쿼리 캐시와 뮤테이션 캐시 전부 비움 | 로그아웃, 사용자 전환 |

## 뮤테이션 후 갱신 방식

- 공식 가이드 권장은 `invalidateQueries`. `onSuccess`에서 호출
- 무효화 Promise를 반환하면 재조회가 끝날 때까지 `isPending` 유지
- 정렬된 목록처럼 직접 고치기 어려운 데이터에 안전함
- 응답에 화면에 필요한 값이 전부 있을 때만 `setQueryData`로 직접 갱신
  - 동기로 캐시를 바로 바꿈. 네트워크 요청 없음
  - 불변 갱신 필수. 캐시에서 꺼낸 객체를 직접 고치지 않고 새 객체 반환

## 낙관적 업데이트

- 응답 전에 캐시를 먼저 바꿔 즉시 반영하는 방식. 성공 후 처리가 아니라 요청 전 처리
- 좋아요, 토글처럼 즉각 반응이 중요할 때만 사용. 모든 뮤테이션에 필요하지 않음
- `cancelQueries`: 진행 중인 재조회 취소. 늦게 온 응답이 낙관적 데이터를 덮어쓰는 것 방지

1. `onMutate`: `cancelQueries` → 현재 값 저장 → `setQueryData`로 예상 값 기록 → 저장 값 반환
2. `onError`: 저장 값으로 롤백
3. `onSettled`: `invalidateQueries`로 서버와 다시 맞춤

## removeQueries 주의점

- 캐시에서만 지우고 리렌더를 일으키지 않음
- 마운트된 화면에는 이전 데이터가 그대로 남음
- 다른 이유로 리렌더되는 순간 로딩 상태로 떨어지고 재조회 시작

> If the page re-renders for some other reason, it will go back to `loading` state and start to fetch.
>
> 출처: [removeQueries doesn't always remove the query's cache](https://github.com/TanStack/query/discussions/3399)

## 삭제 뮤테이션

- 콜백 순서: 훅 정의의 `onSuccess` → `mutate(vars, { onSuccess })`
  - 훅이 캐시를 만지는 시점에는 지운 항목의 상세가 아직 마운트 상태
  - 상세를 `invalidateQueries`하면 바로 재조회해 404
  - `removeQueries`해도 화면을 떠나기 전 리렌더가 끼면 재조회해 404. 배칭 타이밍에 따라 갈림
- 권장: 훅은 목록만 무효화, 지운 상세는 그대로 둠
  - 화면의 이동, 패널 닫기, 선택 해제가 구독을 끊음
  - 남은 캐시는 `gcTime` 뒤 자동 정리
  - 삭제 뮤테이션에서는 무효화 Promise를 반환하지 않음. 반환하면 목록 재조회가 끝날 때까지 `mutate` 콜백의 이동이 밀림
- 함정
  - 루트 키 무효화가 활성 상세까지 휩쓸어 404 재조회
  - 훅이 인자로 받은 `options`를 자기 `onSuccess` 뒤에 펼치면 `options.onSuccess`가 훅의 무효화를 덮어씀. `mutate` 콜백의 `onSuccess`는 덮어쓰지 않음

```ts
export const useDeletePost = () => {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: deletePost,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: postKeys.list._def })
    },
  })
}

// 화면: 떠나는 동작은 mutate 콜백에서
deletePostMutation.mutate(id, {
  onSuccess: () => navigate(POST_LIST_PATH, { replace: true }),
})
```

## refetch와 resetQueries

| | `refetch` | `resetQueries` |
| --- | --- | --- |
| 기존 데이터 | 새 응답 전까지 유지 | 즉시 비움(`initialData` 옵션이 있으면 그 값) |
| 다시 받는 동안 상태 | `isFetching` | `isPending` + `isFetching` |
| 쓰는 때 | 같은 조건으로 최신 데이터 다시 받기 | 이전 데이터가 잠깐이라도 보이면 안 될 때 |

- 요청 횟수와 속도는 같음
- `enabled: false`인 쿼리
  - `refetch()`: `enabled`를 무시하고 요청함
  - `resetQueries`: 비활성 쿼리라 요청하지 않지만 데이터는 비움
- 로딩 표시는 화면이 보는 상태값에 따름. `isFetching` 기준이면 `refetch`로도 로딩 표시됨
  - `isPending`: 데이터 없음. 첫 로드 스켈레톤용
  - `isFetching`: 요청 실행 중. 데이터를 둔 채 재조회 표시용

## 초기화 버튼

- 조건을 기본값으로 되돌리면 쿼리 키가 바뀌어 자동 조회
- 이미 기본값이라 키가 같으면 요청 없음
  - 정책상 그래도 새로고침이 필요하면 조건이 그대로일 때만 `refetch()`
  - `refetch()`는 `enabled: false`를 무시하므로 조회 조건이 없을 때는 호출 전에 직접 막음

```ts
const handleReset = () => {
  form.reset(DEFAULT_VALUES)

  // applyFilters: 조건이 실제로 바뀌었는지 반환
  if (!applyFilters(DEFAULT_VALUES)) {
    void refetch()
  }
}
```

## 참고자료

| TITLE | URL |
| --- | --- |
| 뮤테이션 후 무효화 권장 방식 | https://tanstack.com/query/latest/docs/framework/react/guides/invalidations-from-mutations |
| API별 공식 동작 정의 | https://tanstack.com/query/latest/docs/reference/QueryClient |
| 낙관적 업데이트의 취소, 기록, 롤백 흐름 | https://tanstack.com/query/latest/docs/framework/react/guides/optimistic-updates |
| setQueryData 불변 갱신 주의 | https://tanstack.com/query/latest/docs/framework/react/guides/updates-from-mutation-responses |
| 무효화와 직접 갱신 선택 기준 | https://tkdodo.eu/blog/mastering-mutations-in-react-query |
| removeQueries가 리렌더하지 않는 이유 | https://github.com/TanStack/query/discussions/3399 |
| 활성 쿼리를 removeQueries로 지워도 리렌더 시 재조회 | https://github.com/TanStack/query/discussions/3169 |
| 구독 정리는 컴포넌트를 내리는 쪽의 몫 | https://github.com/TanStack/query/discussions/1886 |
| refetch는 같은 파라미터 재요청 용도 | https://tkdodo.eu/blog/effective-react-query-keys |
| enabled false여도 refetch는 동작 | https://tanstack.com/query/latest/docs/framework/react/guides/disabling-queries |
| isPending과 isFetching의 용도 | https://tanstack.com/query/latest/docs/framework/react/guides/background-fetching-indicators |
| resetQueries가 비활성 쿼리를 재조회에서 거르는 소스 | https://github.com/TanStack/query/blob/main/packages/query-core/src/queryClient.ts |

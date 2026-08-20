# refetch

> TanStack Query 공식 FAQ: [React Query FAQs](https://tkdodo.eu/blog/react-query-fa-qs) / [Query Invalidation](https://tanstack.com/query/latest/docs/framework/react/guides/query-invalidation)

조회 버튼이 있는 목록 화면에서, **같은 조건으로 다시 눌렀을 때 아무 일도 일어나지 않는 문제**를 해결하는 방법

## 문제

조회 조건을 쿼리 키에 넣어 두면 조건이 바뀔 때는 자동으로 다시 조회된다. 그런데 조건을 바꾸지 않고 조회 버튼만 다시 누르면 화면이 멈춘 것처럼 보인다.

- 조건이 같으면 쿼리 키도 같음
- 캐시가 `staleTime` 안이면 네트워크 요청이 아예 없음
  > As long as data is fresh, it will always come from the cache only. You will not see a network request for fresh data, no matter how often you want to retrieve it.
- 조건을 URL 쿼리스트링에 두는 경우, 문자열까지 같아지면 리렌더조차 없음

버튼 핸들러는 정상적으로 실행된다. 막히는 건 그 다음이라 "버튼이 안 먹는다"로 오해하기 쉽다.

## 원칙

| 무엇이 바뀌었나 | 어떻게 다시 조회하나 |
| --- | --- |
| 조회 조건·페이지 | 쿼리 키를 바꾼다 (자동 조회) |
| 조건은 그대로, 사용자가 조회 버튼을 누름 | `refetch()` |
| 내가 등록·수정·삭제함 | mutation 후 `invalidateQueries` |

공식 FAQ가 `refetch`의 용도를 이렇게 못박는다.

> refetch function should only be used when the same query is called with exactly the same parameters. If you are using new parameters (new filters, pages etc.), you should use a new query key.

즉 조회 버튼은 **조건이 같을 때만** `refetch`를 부르면 된다. 메인테이너가 제시한 형태도 이 분기 그대로다.

```js
// https://github.com/TanStack/query/discussions/3396
if (isEqual(params, searchParams)) {
  query.refetch()
} else {
  setSearchParams(params)
}
```

```tsx
const { data, refetch } = useQuery({
  queryKey: ['plans', filters],
  queryFn: () => getPlans(filters),
})

const handleSearch = (values) => {
  // 조건이 바뀌면 키가 달라져 조회가 따라오므로 여기서 다시 부르지 않음
  if (isSameFilters(values, filters)) {
    void refetch()
    return
  }

  setFilters(values)
}
```

### 조건이 바뀔 때도 같이 부르면 안 되는 이유

핸들러가 실행되는 시점에는 **아직 이전 조건의 쿼리가 활성 상태**다. 거기서 `refetch`나 `invalidateQueries`를 부르면 이전 조건으로 요청이 한 번 나가고, 리렌더 후 새 조건으로 또 한 번 나간다. 클릭 한 번에 요청 2건이 된다.

## 검색 영역과 목록이 다른 컴포넌트일 때

`refetch`는 `useQuery`가 주는 값이라 "검색 컴포넌트에서는 못 쓴다"고 생각하기 쉽다. 아니다.

**react-query 훅은 props가 아니라 쿼리 키로 연결된다.** 같은 키로 `useQuery`를 다시 호출하면 같은 쿼리를 관찰하게 되고, 요청은 하나로 합쳐진다.

```tsx
// 조회 조건 조립과 요청을 훅 하나에 모아 두고
const useTaskPlanList = () => {
  const filters = useFilters()

  return useQuery({ queryKey: ['plans', filters], queryFn: () => getPlans(filters) })
}

const ListTable = () => {
  const { data, isFetching } = useTaskPlanList() // 표시용
  ...
}

const SearchBox = () => {
  const { refetch } = useTaskPlanList() // 재조회용. 요청은 늘지 않음
  ...
}
```

파라미터 조립을 복제하지 않는 게 핵심이다. 검색 쪽에서 조건을 손으로 다시 만들면, 하나만 어긋나도 **아무도 보고 있지 않은 쿼리를 재조회하는 조용한 버그**가 된다.

리렌더도 늘지 않는다. 기본 동작이 컴포넌트가 실제로 읽은 값만 추적하기 때문에, `refetch`만 꺼내 쓰면 목록이 갱신돼도 검색 영역은 다시 그려지지 않는다.

> By default, access to properties will be tracked, and the component will only re-render when one of the tracked properties change.

## refetch vs invalidateQueries

| | `refetch()` | `invalidateQueries()` |
| --- | --- | --- |
| 대상 | 그 쿼리 하나 | 키 prefix로 매칭되는 여러 쿼리 |
| 동작 | 즉시 다시 받음 | stale 표시 + **활성** 쿼리만 배경 재조회 |
| `staleTime` | 무시 | 무시 (`This stale state overrides any staleTime configurations`) |
| 쓰는 자리 | 재조회 대상이 하나로 정해질 때 | mutation 후처리, 도메인 단위 갱신 |

조회 버튼은 대상이 하나라 `refetch`가 정석이다. `invalidateQueries`로도 되지만 필요 없는 쿼리까지 건드린다.

### 쓰지 말아야 할 방법

**쿼리 키에 `_timestamp: Date.now()`를 섞어 강제로 키를 바꾸는 방식.** 실무 코드에서 자주 보이지만 클릭마다 캐시 엔트리가 쌓이고 키가 오염된다.

**`enabled: false`로 두고 버튼에서 `refetch`만 부르는 방식.** 가장 흔히 보이지만 공식 문서가 권장하지 않는다.

> Permanently disabling a query opts out of many great features that TanStack Query has to offer (like background refetches), and it takes you from the declarative approach (defining dependencies when your query should run) into an imperative mode (fetch whenever I click here).

`refetch`에는 파라미터를 넘길 수 없다는 제약도 있다. 조건은 키로 두고, `refetch`는 같은 조건 재조회에만 쓰는 게 맞다.

## staleTime 은 어떻게 두나

기본값은 `0`이다. 0이라고 캐시를 안 쓰는 게 아니라 **캐시를 먼저 보여주고 뒤에서 확인**한다(stale-while-revalidate). 캐시 보관 기간은 `gcTime`(기본 5분)이 담당한다.

> Stale queries are refetched automatically in the background when: New instances of the query mount, The window is refocused, The network is reconnected

`staleTime`을 길게 두는 건 **무효화 수단이 확실할 때** 정당하다. 내가 바꾼 데이터는 mutation 후 `invalidateQueries`가 잡는다. 하지만 **다른 사용자가 바꾼 변경**은 무효화가 잡을 수 없고, 그걸 잡는 수단이 조회 버튼이다. 그래서 `staleTime`을 낮추는 것보다 조회 버튼에 `refetch`를 붙이는 쪽이 낫다.

## UX 관점

"요청을 아꼈다"와 "버튼이 죽어 보인다"는 다른 문제다. 캐시가 fresh해서 요청을 안 보내는 건 정상 동작이지만, 클릭에 아무 표시가 없는 건 정상이 아니다.

> The design should always keep users informed about what is going on, through appropriate feedback within a reasonable amount of time. — Nielsen Norman Group, 사용성 휴리스틱 #1

선택지는 둘이다.

1. 조회 = 최신화 요청으로 보고 항상 다시 받는다 → `isFetching`으로 로딩을 띄우면 그게 피드백이 된다
2. 재조회는 안 하고 "변경된 조건이 없습니다" 같은 안내를 준다

목록처럼 여러 사용자가 함께 고치는 데이터는 1번이 자연스럽다.

## 참고

- [Best practice to execute the query when query key has not changed · Discussion #3396](https://github.com/TanStack/query/discussions/3396) — 분기 형태의 근거
- [How can i make the search button work with react query? · Discussion #4555](https://github.com/TanStack/query/discussions/4555)
- [Disabling/Pausing Queries | TanStack Query](https://tanstack.com/query/latest/docs/framework/react/guides/disabling-queries)
- [React Query FAQs — TkDodo](https://tkdodo.eu/blog/react-query-fa-qs)
- [Practical React Query — TkDodo](https://tkdodo.eu/blog/practical-react-query)
- [React Query as a State Manager — TkDodo](https://tkdodo.eu/blog/react-query-as-a-state-manager)
- [Render Optimizations | TanStack Query](https://tanstack.com/query/latest/docs/framework/react/guides/render-optimizations)
- [Important Defaults | TanStack Query](https://tanstack.com/query/latest/docs/framework/react/guides/important-defaults)
- [Visibility of System Status — NN/g](https://www.nngroup.com/articles/visibility-system-status/)

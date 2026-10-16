# useQuery

- [옵션](#옵션)
  - [enable](#enable)
- [기본값](#기본값)
- [로딩 표시](#로딩-표시)
- [참고자료](#참고자료)

## 옵션
### enable
실행조건

## 기본값
기본값은 아래와 같이 상수로 만들어서 지정.
```ts
const DEFAULT_CHART_DATA = []

const { data = DEFAULT_CHART_DATA } = query
```

- Recharts 등 기본값이 있어야 하는 라이브러리 사용시 위와 같은 지정이 필요할 수 있음.
- 상수로 만드는 이유는 fallback 배열 참조를 안정적으로 유지
  - 매번 새 배열을 만들면 메모리 낭비
  - 상수가 컴포넌트 밖 module scope에 있으면 모듈이 로드될 때 한 번만 실행됨.

## 로딩 표시

- 스켈레톤처럼 화면을 갈아끼우는 로딩은 데이터가 없을 때만 표시
- `isFetching`은 기존 데이터를 그대로 두고 작은 표시(진행바, 스피너, 버튼 비활성화)에만 사용
  - `isFetching`으로 스켈레톤을 띄우면 무효화, 재마운트, 포커스 재조회마다 화면 깜빡임
  - 그리드는 스크롤 위치, 선택 행, 편집 중인 칸까지 잃음
- 스켈레톤은 전체 화면 로딩용. 이미 그려진 일부를 다시 받을 때는 맞지 않음
- 분기 순서는 데이터 → 에러 → 로딩
  - 재조회가 실패해도 `data`는 남아 있어 기존 화면 유지
  - 에러를 먼저 보면 재조회 실패 한 번에 목록이 에러 화면으로 바뀜

| 플래그 | 의미 (v5) | 용도 |
| --- | --- | --- |
| `isPending` | 데이터 없음. 요청 중이 아닐 수도 있음(`enabled: false`, 오프라인) | 첫 로딩 판정 |
| `isLoading` | `isPending && isFetching`. 데이터 없이 첫 요청 중 | 첫 로딩 스켈레톤 |
| `isFetching` | 이유와 상관없이 요청 중. 백그라운드 재조회 포함 | 재조회 표시 |

- `isPending`으로 스켈레톤을 띄우면 `enabled: false` 동안 요청 없이 스켈레톤이 계속 남음

```tsx
const { data, error, isLoading, isFetching } = useQuery(listQueryOptions())

if (data) {
  return (
    <>
      {isFetching && <ProgressBar />}
      <List data={data} />
    </>
  )
}
if (error) return <Error />
if (isLoading) return <Skeleton />
return <Empty /> // 조회 전(enabled: false)이나 오프라인 일시정지
```

## 참고자료

| TITLE | URL |
| --- | --- |
| 첫 로딩은 pending, 재조회는 isFetching으로 표시하는 공식 예제 | https://tanstack.com/query/latest/docs/framework/react/guides/background-fetching-indicators |
| status와 fetchStatus 구분 | https://tanstack.com/query/latest/docs/framework/react/guides/queries |
| v5에서 isLoading이 isPending && isFetching으로 바뀐 내역 | https://tanstack.com/query/latest/docs/framework/react/guides/migrating-to-v5 |
| 데이터를 먼저 확인하고 로딩은 마지막에 보는 순서 | https://tkdodo.eu/blog/status-checks-in-react-query |
| 스켈레톤은 전체 페이지 로딩용 | https://www.nngroup.com/articles/skeleton-screens/ |

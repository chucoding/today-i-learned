# 캐시 갱신 API

invalidateQueries, resetQueries, removeQueries, clear 비교

- [API별 차이](#api별-차이)
- [뮤테이션 후 갱신 방식](#뮤테이션-후-갱신-방식)
- [removeQueries 주의점](#removequeries-주의점)
- [참고자료](#참고자료)

## API별 차이

| API | 동작 | 쓰는 때 |
| --- | --- | --- |
| `invalidateQueries` | 데이터는 남기고 stale 표시, 활성 쿼리만 재조회 | 등록, 수정, 삭제 후 관련 목록 갱신 |
| `resetQueries` | 초기 상태(`initialData`가 있으면 그 값)로 되돌리고 활성 쿼리 재조회 | 필터나 폼처럼 처음 상태로 돌아가야 할 때 |
| `removeQueries` | 캐시에서 쿼리 삭제, 삭제 시점에는 재조회 없음 | 삭제된 항목의 상세처럼 다시 볼 일 없는 캐시 정리 |
| `clear` | 쿼리 캐시와 뮤테이션 캐시 전부 비움 | 로그아웃, 사용자 전환 |

## 뮤테이션 후 갱신 방식

- 공식 가이드 권장은 `invalidateQueries`. `onSuccess`에서 호출
- 무효화 Promise를 반환하면 재조회가 끝날 때까지 `isPending` 유지
- 정렬된 목록처럼 직접 고치기 어려운 데이터에 안전함
- 응답에 화면에 필요한 값이 전부 있을 때만 `setQueryData`로 직접 갱신

## removeQueries 주의점

- 캐시에서만 지우고 리렌더를 일으키지 않음
- 마운트된 화면에는 이전 데이터가 그대로 남음
- 다른 이유로 리렌더되는 순간 로딩 상태로 떨어지고 재조회 시작

> If the page re-renders for some other reason, it will go back to `loading` state and start to fetch.

## 참고자료

| TITLE | URL |
| --- | --- |
| 뮤테이션 후 무효화 권장 방식 | https://tanstack.com/query/latest/docs/framework/react/guides/invalidations-from-mutations |
| API별 공식 동작 정의 | https://tanstack.com/query/latest/docs/reference/QueryClient |
| 무효화와 직접 갱신 선택 기준 | https://tkdodo.eu/blog/mastering-mutations-in-react-query |
| removeQueries가 리렌더하지 않는 이유 | https://github.com/TanStack/query/discussions/3399 |

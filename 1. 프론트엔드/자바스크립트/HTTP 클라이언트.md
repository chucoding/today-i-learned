# HTTP 클라이언트

fetch, Axios, TanStack Query 선택 기준

## 목차

- [선택 기준](#선택-기준)
- [fetch와 Axios](#fetch와-axios)
- [TanStack Query](#tanstack-query)
- [참고자료](#참고자료)

## 선택 기준

- 프레임워크가 아니라 코드가 실행되는 위치와 필요한 기능으로 고름
  - 실행 위치: 서버, 브라우저 번들, 번들 없는 인라인 스크립트
  - 번들 없는 스크립트는 라이브러리를 따로 불러와야 하므로 내장 `fetch`가 기본

## fetch와 Axios

| | `fetch` | Axios |
|---|---|---|
| 제공 | 내장 API | 외부 라이브러리. 번들 크기 추가 |
| HTTP 오류(4xx, 5xx) | reject하지 않음. `response.ok` 확인 필요 | 2xx 밖이면 reject (`validateStatus`) |
| 타임아웃 | 옵션 없음. `AbortController`의 `signal`로 취소 | `timeout` 옵션(밀리초) |
| 인터셉터 | 없음 | 요청과 응답을 가로채 수정 |

- MDN Using Fetch: 서버가 404 같은 오류 상태로 응답해도 reject하지 않음.
- Axios 요청 설정: 상태 코드가 200~299 밖이면 기본으로 reject.

## TanStack Query

- 서버 상태의 가져오기, 캐시, 동기화, 갱신 도구. HTTP 클라이언트가 아니라 `fetch`나 Axios를 감싸서 씀.
- 도입 기준은 호출 개수가 아니라 필요한 기능
  - 캐시, 중복 요청 합치기, 재시도, 백그라운드 재요청이 필요하면 도입
  - 한 번 보내고 끝나는 요청이면 `fetch`로 충분
- React가 없는 환경은 프레임워크와 무관한 코어 `@tanstack/query-core` 사용 가능

## 참고자료

| TITLE | URL |
|---|---|
| fetch가 HTTP 오류에 reject하지 않는 동작 | [https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) |
| Axios의 timeout과 validateStatus | [https://axios.rest/pages/advanced/request-config](https://axios.rest/pages/advanced/request-config) |
| Axios 인터셉터 정의 | [https://axios.rest/pages/advanced/interceptors](https://axios.rest/pages/advanced/interceptors) |
| TanStack Query가 다루는 서버 상태 | [https://tanstack.com/query/latest/docs/framework/react/overview](https://tanstack.com/query/latest/docs/framework/react/overview) |
| React 없이 쓰는 query-core 패키지 | [https://github.com/TanStack/query/tree/main/packages/query-core](https://github.com/TanStack/query/tree/main/packages/query-core) |

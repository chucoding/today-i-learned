# 쿼리 키는 어디에 두나

> [FSD 공식 - Usage with TanStack Query](https://feature-sliced.design/docs/guides/tech/with-react-query)

쿼리 키는 **`api` 세그먼트**에 둔다. 슬라이스는 **그 데이터를 소유한 곳**이다.

## 세 가지 배치

| 방식 | 경로 | 언제 |
|------|------|------|
| shared에 한곳으로 | `shared/api/queries/` | 소규모, 엔드포인트가 적을 때 |
| shared에서 컨트롤러별로 | `shared/api/{controller}/` | 엔드포인트가 많을 때 |
| 엔티티별로 | `entities/{entity}/api/` | 엔티티 구분이 있고 요청 하나가 엔티티 하나에 대응할 때. 공식 문서가 "가장 깔끔한 방식"이라고 함 |

## 같이 지킬 것

- 키와 쿼리는 `queryOptions` 팩토리 하나에 묶는다 (`entities/post/api/post.query.ts`)
  ```ts
  export const POST_QUERIES = {
    all: () => ['posts'],
    lists: () => [...POST_QUERIES.all(), 'list'],
    list: (page: number, limit: number) => queryOptions({ ... }),
  }
  ```
- 뮤테이션은 쿼리와 섞지 않는다. `useMutation` 훅은 사용처 가까운 `api`(`pages/example/api/use-update-example.ts`)에 둔다
- 엔티티끼리 관계가 있으면 서로의 공개 API(`index.ts`)로 가져온다

## v2.1 Pages First를 적용하면

- 한 페이지에서만 쓰는 쿼리 → `pages/{page}/api/`
- 2곳 이상에서 같은 도메인 데이터를 쓸 때 → `entities/{entity}/api/`로 올린다
- entities 레이어가 없으면 → `shared/api/` (비즈니스 로직은 넣지 않음)

## createQueryKeys를 쓸 때

- 배치는 같다. 도메인별 `createQueryKeys('post', …)` 파일을 위 `api` 세그먼트에 둔다
- `mergeQueryKeys`로 합치는 파일만 `shared/api`에 둔다
- 공식 가이드는 `queryOptions` 기준이라 이 부분은 원칙을 적용한 해석이다 ([Query Key Factory](../TanStackQuery/Query%20Key%20Factory.md))

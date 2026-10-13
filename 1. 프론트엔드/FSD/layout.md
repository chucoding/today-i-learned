# 레이아웃

FSD 레이아웃 배치 가이드

## 목차

- [배치 위치](#배치-위치)
- [위젯이 필요한 레이아웃](#위젯이-필요한-레이아웃)
- [라우터 레이아웃](#라우터-레이아웃)
- [참고자료](#참고자료)

## 배치 위치

| 레이아웃 | 위치 |
|---|---|
| 앱 전체 틀(헤더, 사이드바, 인증 가드) | `app/` 라우트 설정 또는 `app/layouts` |
| 위젯 없는 순수 틀 | `shared/ui` (내용은 children, props로 받음) |
| 한 페이지 안의 배치 | 해당 `pages/{page}/ui` |

- 레이어마다 레이아웃 폴더를 두거나 설정 기반 레이아웃 빌더를 만드는 방식은 공식 가이드에 없음.

## 위젯이 필요한 레이아웃

- import 규칙상 `shared`, `widgets`에 둘 수 없음. 위젯끼리도 같은 레이어라 서로 import 불가.
- 검토 순서
  1. App 레이어 라우트 설정에 직접 작성
  2. 쓰는 페이지가 2~3곳이면 그냥 복사
  3. render props나 slots로 위젯을 바깥에서 주입
  4. `app/layouts`로 옮겨 필요한 위젯 조합
- 레이아웃은 거의 바뀌지 않아 추상화 이득이 작음.

> "The urge to abstract code is often very overrated. It is especially the case for layouts, which rarely change."
>
> 출처: [Page layouts | Feature-Sliced Design](https://feature-sliced.design/docs/guides/examples/page-layout)

## 라우터 레이아웃

- 주요 라우터는 레이아웃을 라우트 트리의 일부로 다룸. React Router의 layout route와 `<Outlet />`, Next.js의 `layout.tsx`
- 라우팅은 App 레이어 담당이라 레이아웃도 App에 모임.
- 영역마다 틀이 다르거나 페이지 이동 중에 레이아웃 상태를 유지해야 할 때 이점이 있음. 레이아웃이 하나뿐이면 래퍼 컴포넌트로 충분.

## 참고자료

| TITLE | URL |
|---|---|
| FSD 레이아웃 배치와 추상화 우선순위 | https://feature-sliced.design/docs/guides/examples/page-layout |
| React Router 레이아웃 라우트와 Outlet | https://reactrouter.com/start/declarative/routing |
| 단일 레이아웃과 레이아웃 라우트 비교 | https://docs.scrimba.com/react/nested-routes |

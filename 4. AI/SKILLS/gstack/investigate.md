# `/investigate`

원인이 불명확한 버그를 추적할 때 사용

## SPA에서 유용한 사례

- TanStack Query 캐시가 예상과 다르게 남음
- Zustand 구독 때문에 불필요하게 렌더링됨
- 요청 race로 이전 응답이 최신 화면을 덮음
- `useEffect`가 중복 실행됨
- 특정 탭을 거쳐야만 오류가 발생함
- 라우팅과 전역 오류 처리가 충돌함
- AG Grid lifecycle과 React lifecycle이 맞지 않음

## 사용 방식

바로 수정을 시도하지 않고, 재현 조건과 데이터 흐름부터 추적하는 데 사용

## 참고

- [SPA 현업용 사용 가이드](README.md#spa-현업용-사용-가이드)

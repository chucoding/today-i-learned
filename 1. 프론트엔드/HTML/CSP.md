# CSP

Content-Security-Policy. 문서가 불러오고 실행할 수 있는 출처를 응답 헤더로 제한하는 정책

## 목차

- [왜 쓰는가](#왜-쓰는가)
- [전달 방식](#전달-방식)
- [인라인 스크립트 허용](#인라인-스크립트-허용)
- [위반 보고](#위반-보고)
- [참고자료](#참고자료)

## 왜 쓰는가

- 주입된 스크립트가 실행되지 않게 막는 XSS 방어선
  - 허용하지 않은 출처의 스크립트와 인라인 스크립트는 브라우저가 실행하지 않음.
- 다른 페이지가 내 문서를 iframe으로 띄우는 것도 `frame-ancestors`로 제한

## 전달 방식

| 헤더 | 동작 | 쓰는 시점 |
| --- | --- | --- |
| `Content-Security-Policy` | 위반을 차단 | 정책 확정 후 |
| `Content-Security-Policy-Report-Only` | 차단하지 않고 보고만 함 | 도입 초기 위반 수집 |

- Report-Only에서는 `sandbox`만 무시되고 나머지 지시어는 모두 동작함.
- `<meta>`로도 정책을 넣을 수 있으나 `frame-ancestors`, `report-uri`, `sandbox`, Report-Only는 지원하지 않음.

## 인라인 스크립트 허용

| 방식 | 값 | 맞는 경우 |
| --- | --- | --- |
| nonce | 요청마다 새로 만든 무작위 값. `<script nonce="...">`와 헤더 값이 같아야 실행 | 렌더할 때마다 스크립트가 바뀌는 문서 |
| hash | 스크립트 본문의 sha256/384/512 해시. 공백과 대소문자까지 일치해야 실행 | 배포마다 같은 고정 스크립트 |

- Next.js는 proxy가 만든 nonce를 동적 렌더링되는 page의 SSR 산출물(프레임워크 스크립트, page 번들, `<Script>`)에 자동으로 붙임.
  - 정적 page는 빌드 때 만들어져 nonce를 넣을 수 없음.
  - Route Handler가 직접 만든 `Response`는 자동 부착 목록에 없음. 이때는 직접 헤더를 붙이고 고정 스크립트면 hash가 간단함.

## 위반 보고

- `report-to <이름>` + `Reporting-Endpoints: <이름>="<URL>"` 조합이 표준
  - `report-uri <URL>`은 사용 중단 예정이지만 `report-to` 미지원 브라우저 때문에 함께 선언함.
- 헤더 하나에 쉼표로 여러 정책을 나열할 수 있음.
  - 보고 URL을 환경변수 같은 외부 값으로 넣으면 `;`, `,`가 정책을 바꿀 수 있어 검증이나 인코딩(`%2C`)이 필요함.

## 참고자료

| TITLE | URL |
| --- | --- |
| Report-Only의 동작과 무시되는 지시어 | [https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Security-Policy-Report-Only](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Security-Policy-Report-Only) |
| nonce와 hash 허용 방식 | [https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Security-Policy/script-src](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Security-Policy/script-src) |
| meta 전달 제약, 쉼표로 나열하는 정책 목록 | [https://www.w3.org/TR/CSP3/](https://www.w3.org/TR/CSP3/) |
| Next.js의 nonce 자동 부착 | [https://nextjs.org/docs/app/guides/content-security-policy](https://nextjs.org/docs/app/guides/content-security-policy) |

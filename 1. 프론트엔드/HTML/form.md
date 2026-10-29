# `<form>`
HTML `<form>` 요소는 정보를 제출하기 위한 대화형 컨트롤을 포함하는 문서 구획

> [form](https://developer.mozilla.org/ko/docs/Web/HTML/Reference/Elements/form)

## 목차

- [submit](#submit)
- [폼 전송과 fetch](#폼-전송과-fetch)
- [참고자료](#참고자료)

## submit
form안에 `<input type="submit" />`이 있으면 해당 요소로 폼 제출 가능

> 💡 폼 제출 후 focus 유지?
> - Enter의 경우
>    - 제출을 발생시킨 시점에 포커스를 가진 요소는 그대로 포커스를 유지
>    - 별도로 blur 되지 않음.
> - 마우스 클릭의 경우
>    - 클릭 동작 때문에 포커스가 버튼으로 이동할 수 있음
>    - Safari는 버튼 클릭해도 focus 이동 안 할 수 있음

## 폼 전송과 fetch

| | 폼 전송 | `fetch` |
|---|---|---|
| 동작 | 페이지 이동(navigation) | 스크립트 요청 |
| 응답 | 브라우저가 그 탭에 그림. `target="_blank"`면 새 탭 | 스크립트가 받기만 함. 탭이 열리지 않음 |
| 보낼 수 있는 형식 | `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` | 제한 없음 |

- 선택 기준: 응답을 탭에 표시해야 하면 폼 전송, 데이터만 필요하면 `fetch`
- 폼 전송은 navigation이라 CORS 대상이 아님. 다른 출처로 보내도 preflight 없음.
- `fetch`는 교차 출처 요청이면 CORS 적용. 단순 요청 조건을 벗어나면 preflight 발생
  - MDN CORS: 단순 요청은 메서드가 GET, HEAD, POST이고 직접 지정한 헤더가 허용 목록 안이어야 함.
  - MDN CORS: 단순 요청의 `Content-Type`은 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`만 허용
  - `application/json`을 다른 출처로 보내면 preflight 대상
- `method="post"`면 데이터가 URL이 아니라 본문에 실려 URL 길이 제한을 피함.

> 💡 preflight란?
> 교차 출처 요청을 보내기 전에 브라우저가 `OPTIONS` 요청으로 서버에 허용 여부를 먼저 묻는 요청. 단순 요청은 이 과정 없이 바로 보냄.

## 참고자료

| TITLE | URL |
|---|---|
| 폼 요소와 target, enctype 속성 | [https://developer.mozilla.org/ko/docs/Web/HTML/Reference/Elements/form](https://developer.mozilla.org/ko/docs/Web/HTML/Reference/Elements/form) |
| CORS 단순 요청의 Content-Type 조건 | [https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS) |

# CORS

교차 출처 리소스 공유(Cross-Origin Resource Sharing)

## 목차

- [origin](#origin)
- [허용 판단](#허용-판단)
- [해결방법](#해결방법)
  - [해결방법1 : 헤더 조작](#해결방법1--헤더-조작)
  - [해결방법2 : proxy 서버](#해결방법2--proxy-서버)
- [참고자료](#참고자료)

## origin

- 페이지 URL의 scheme, host, port 조합
  - MDN Origin: 접근에 쓴 URL의 scheme(프로토콜), hostname(도메인), port로 정의.
- 셋 중 하나라도 다르면 다른 출처. `https://a.com`과 `https://a.com:8443`은 다른 출처.

## 허용 판단

- 브라우저가 요청에 `Origin` 헤더를 붙이고, 응답의 `Access-Control-Allow-Origin`과 비교
- 비교 대상은 요청을 보낸 페이지의 출처와 응답 헤더뿐
  - 서버 주소가 그대로면 그 뒤의 인프라 교체는 브라우저에 보이지 않음
  - 새 인프라도 같은 `Access-Control-Allow-Origin`을 내려줘야 함. 헤더는 서버 쪽(애플리케이션, Nginx, 게이트웨이)이 붙이기 때문.
  - 요청을 보내는 페이지 주소가 바뀌면 서버 허용 목록 갱신 필요
- PUT이나 `application/json`처럼 단순 요청 조건을 벗어나면 preflight를 먼저 보냄. 조건은 [form](../HTML/form.md#폼-전송과-fetch) 참고.

## 해결방법

>💡 cors는 무조건 백엔드(Nginx, WAS 등)에서 해결해야한다.

### 해결방법1 : 헤더 조작
서버에서 응답 헤더에 ACAO(Access-Controll-Allow-Origin)을 요청 도메인으로 허용<br/>
(CORS 통과 조건 : Requesst Header Origin = Response Header Acess-Control-Allow-Origin)

### 해결방법2 : proxy 서버

- React CRA : setupProxy.js를 이용해서 로컬에서 proxy서버를 띄울 수 있다.
- Nginx : 프로덕션에서는 Nginx의 리버스 프록시를 이용해서 CORS를 우회할 수 있다.

💡 프록시 서버 역할
1. 클라이언트의 IP를 서버에서 받을때 임의로 바꿔버릴 수 있다. (인터넷에서는 접근하는 사람의 IP를 모르게됨)
2. 보내는 데이터도 임의로 바꿀 수 있음
3. 방화벽, 필터, 캐쉬 데이터(이미지 임시저장 등) 제공 기능

## 참고자료

| TITLE | URL |
|---|---|
| origin을 이루는 scheme, host, port | [https://developer.mozilla.org/en-US/docs/Glossary/Origin](https://developer.mozilla.org/en-US/docs/Glossary/Origin) |
| Origin 요청 헤더와 Access-Control-Allow-Origin 응답 | [https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS) |

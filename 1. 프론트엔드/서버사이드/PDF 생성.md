# PDF 생성

서버에서 HTML 문서를 PDF 로 만드는 방식

## 목차

- [레이아웃 계산 주체](#레이아웃-계산-주체)
- [headless Chrome 과 PDF 라이브러리](#headless-chrome-과-pdf-라이브러리)
- [실행 위치](#실행-위치)
- [주의점](#주의점)
- [참고자료](#참고자료)

## 레이아웃 계산 주체

PDF 는 글자 좌표, 줄바꿈 위치, 쪽 경계가 확정된 결과물. 그래서 누가 레이아웃을 계산하는지가 핵심.

| 형식 | 레이아웃 계산 | 서버가 하는 일 |
| --- | --- | --- |
| 브라우저 인쇄 | 사용자 브라우저 | HTML 과 인쇄 CSS 만 보냄 |
| Excel, 한글 | 파일을 여는 프로그램 | 셀 값, 열 폭, 병합 같은 설계도만 씀 |
| PDF | 만드는 쪽(서버) | 쪽마다 그릴 위치를 확정 |

## headless Chrome 과 PDF 라이브러리

> 💡 headless Chrome: 화면 없이 돌아가는 Chrome. Puppeteer, Playwright 로 조작.

| | headless Chrome(`page.pdf()`) | pdf-lib, PDFKit |
| --- | --- | --- |
| 입력 | HTML, CSS | 좌표와 글자 |
| 동작 | 브라우저의 「인쇄 → PDF로 저장」을 서버에서 실행. print CSS 미디어 적용 | `drawText(x, y)` 로 직접 그림 |
| 줄바꿈, `…` 자르기, 표 높이 | 브라우저가 CSS 대로 계산 | 직접 구현 |
| 비용 | 브라우저 실행(메모리, 기동 시간) | 레이아웃 코드 유지 |
| 맞는 경우 | HTML 템플릿이 이미 있을 때 | HTML 없이 고정 배치 문서를 만들 때 |

- 같은 글꼴 파일이면 글자 폭은 PDF 라이브러리로도 같게 잼. 다만 어디서 줄을 끊고 칸을 얼마나 늘릴지는 CSS 규칙을 다시 구현해야 해 브라우저 결과와 어긋날 수 있음.
- pdf-lib README: HTML, CSS 를 지원하지 않고, 필요하면 Puppeteer 를 쓰라고 안내.

## 실행 위치

| 위치 | 구성 |
| --- | --- |
| 서버, 컨테이너 | Playwright 공식 이미지, Gotenberg 같은 PDF 변환 컨테이너 |
| 서버리스(Lambda) | `@sparticuz/chromium`. 일반 Chrome 은 배포 크기 한도에 걸려 Lambda 용으로 빌드한 Chromium 사용 |
| 관리형 PDF API | Chromium 운영을 외부에 맡김. 개인정보가 든 문서는 외부 전송 부담 |

## 주의점

- 인쇄 CSS 로드 실패
  - 인쇄 CSS 로드 실패는 `<link>` 의 `error` 이벤트로 감지 가능. `page.pdf()` 전에 그 `<link>` 의 `load` 이벤트나 `error` 이벤트를 확인.
- Lambda 페이로드 한도
  - 동기 호출의 요청과 응답이 각각 6MB. 큰 문서와 PDF 는 S3 에 두고 키만 주고받음.
- `fetch` 와 서명 주소 리다이렉트
  > 💡 서명 주소(presigned URL): S3 객체에 정해진 시간 동안만 접근하도록 서명을 붙인 주소.
  - 서버가 S3 키로 서명 주소를 만들어 브라우저에 넘기는 방식은 두 가지. 303 리다이렉트로 보내거나 JSON 으로 돌려줌.
  - `fetch` 가 303 을 따라 다른 출처(S3)로 가면 그 응답을 읽기 위해 S3 에 CORS 설정 필요. 서명 주소를 JSON 으로 받아 링크로 내려받는 편이 단순.

## 참고자료

| TITLE | URL |
| --- | --- |
| pdf-lib 의 HTML, CSS 미지원 (Limitations) | [https://github.com/Hopding/pdf-lib](https://github.com/Hopding/pdf-lib) |
| page.pdf() 의 print CSS 미디어 | [https://playwright.dev/docs/api/class-page#page-pdf](https://playwright.dev/docs/api/class-page#page-pdf) |
| Node.js PDF 라이브러리 비교 (PDFKit, Puppeteer, jsPDF) | [https://reintech.io/blog/nodejs-pdf-generation-pdfkit-puppeteer-jspdf-comparison](https://reintech.io/blog/nodejs-pdf-generation-pdfkit-puppeteer-jspdf-comparison) |
| HTML to PDF 라이브러리 선택 기준 | [https://dev.to/logrocket/best-html-to-pdf-libraries-for-nodejs-17f7](https://dev.to/logrocket/best-html-to-pdf-libraries-for-nodejs-17f7) |
| Lambda 와 @sparticuz/chromium 구성 | [https://oneuptime.com/blog/post/2026-02-12-use-lambda-for-pdf-generation/markdown](https://oneuptime.com/blog/post/2026-02-12-use-lambda-for-pdf-generation/markdown) |
| PDF 변환 컨테이너 Gotenberg | [https://github.com/gotenberg/gotenberg](https://github.com/gotenberg/gotenberg) |
| 서버리스 Chromium 과 관리형 PDF API 대안 | [https://apitemplate.io/blog/html-to-pdf-aws-lambda-serverless-guide/](https://apitemplate.io/blog/html-to-pdf-aws-lambda-serverless-guide/) |
| Lambda 동기 호출 6MB 한도 | [https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) |
| 교차 출처 응답의 Access-Control-Allow-Origin | [https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS) |
| 스타일시트 load, error 이벤트 | [https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/link](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/link) |

# ego lite

에이전트용 Chromium 브라우저의 장단점

## 목차

- [장점](#장점)
- [단점](#단점)
  - [개발자도구 제어 불가](#개발자도구-제어-불가)
  - [네이티브 다이얼로그](#네이티브-다이얼로그)
- [참고자료](#참고자료)

## 장점

- 사용자가 로그인해 둔 실제 브라우저 환경 그대로 사용
  - 로그인이 필요한 서비스도 별도 인증 절차 없이 조작 가능.
- 팝업과 다운로드 처리 간단
  - 팝업: `waitForEvent("popup")`
  - 다운로드: `waitForEvent("download")` 뒤 `saveAs`
- 스냅샷의 ref로 요소를 지정해 조작
- 사람과 제어권 교환
  - `handOff()`로 사용자에게 넘기고 `takeOverTaskSpace()`로 다시 이어받음.

## 단점

### 개발자도구 제어 불가

- 조작 API와 `page.cdp()`의 대상은 페이지뿐. 개발자도구는 브라우저가 따로 띄우는 화면이라 범위 밖.
  - 열기, 패널 전환, 필터 입력, 캡처 모두 불가.
  - `Page.captureScreenshot`도 페이지 영역만 촬영.
- 영향: Network 패널 같은 개발자도구 화면을 증빙으로 남기려면 사람 개입 필요.
  - `handOff()` 뒤 사람이 개발자도구를 열고 필터를 맞춤.
- 우회
  - OS 화면 녹화로 브라우저 창 영역 촬영. macOS는 `screencapture -v -x -V <초> -R<x,y,w,h>`.
  - 페이지 안에서 `window.fetch`를 감싸 요청 기록. 페이지 코드가 남긴 기록이라 개발자도구만큼의 증거력은 없음.

### 네이티브 다이얼로그

- `alert`, `confirm`이 열리면 곧바로 제어권이 사용자에게 넘어감.
  - 대응: 페이지를 이동할 때마다 `Page.addScriptToEvaluateOnNewDocument`로 다이얼로그를 미리 무력화.
  - 자세한 내용: [에이전트 브라우저 네이티브 다이얼로그](에이전트%20브라우저%20네이티브%20다이얼로그.md)

## 참고자료

| TITLE | URL |
|-------|-----|
| ego-browser 스킬과 설치 문서 | [https://github.com/citrolabs/ego-lite](https://github.com/citrolabs/ego-lite) |

# ego lite

에이전트용 Chromium 브라우저의 장단점

## 목차

- [장점](#장점)
- [단점](#단점)
  - [개발자도구 제어 불가](#개발자도구-제어-불가)
  - [네이티브 다이얼로그](#네이티브-다이얼로그)
- [참고자료](#참고자료)

> 💡 ego lite와 `ego-browser`
> ego lite는 브라우저 앱, `ego-browser`는 에이전트가 그 브라우저를 조작할 때 쓰는 CLI. 조작 스크립트는 [`ego-browser nodejs`로 실행](https://github.com/citrolabs/ego-lite/blob/main/package/ego-browser/skill-body/SKILL.md)함.

> 💡 CDP(Chrome DevTools Protocol)란?
> Chromium 계열 브라우저를 외부 프로그램이 제어하는 프로토콜. `Page.captureScreenshot` 같은 명령 단위로 페이지를 조작함.

## 장점

- [기존 브라우저 데이터를 가져오면](https://github.com/citrolabs/ego-lite/blob/main/skills/ego-browser/references/install.md) 로그인 상태 그대로 사용
  - 이미 로그인한 서비스는 별도 인증 절차 없이 조작 가능. 로그인이 안 된 곳은 제어권을 사람에게 넘겨 직접 로그인.
- [사람과 제어권 교환](https://github.com/citrolabs/ego-lite/blob/main/package/ego-browser/skill-body/SKILL.md)
  - `task.handOff()`로 사용자에게 넘기고 `takeOverTaskSpace()`로 다시 이어받음. `task`는 에이전트가 작업 단위로 연 태스크 스페이스 객체.
- [팝업과 다운로드 처리 간단](https://github.com/citrolabs/ego-lite/blob/main/package/ego-browser/skill-body/SKILL.md)
  - 팝업: `waitForEvent("popup")`
  - 다운로드: `waitForEvent("download")` 뒤 `saveAs()`
- [스냅샷의 ref로 요소를 지정해 조작](https://github.com/citrolabs/ego-lite/blob/main/package/ego-browser/skill-body/SKILL.md)
  - ref: 스냅샷(접근성 트리)이 노드마다 붙이는 `@21` 같은 식별자.

## 단점

### 개발자도구 제어 불가

- 조작 API와 `page.cdp()`의 대상은 페이지뿐. 개발자도구는 브라우저가 따로 띄우는 화면이라 범위 밖.
  - 열기, 패널 전환, 필터 입력, 캡처 모두 불가.
  - `Page.captureScreenshot`도 페이지 영역만 촬영.
- 영향: Network 패널 같은 개발자도구 화면을 증빙으로 남기려면 사람 개입 필요.
  - `task.handOff()` 뒤 사람이 개발자도구를 열고 필터를 맞춤.
- 우회
  - OS 화면 녹화로 브라우저 창 영역 촬영. macOS는 `screencapture -v -x -V <초> -R<x,y,w,h>`.
  - 페이지 안에서 `window.fetch`를 감싸 요청 기록. 페이지 코드가 남긴 기록이라 개발자도구만큼의 증거력은 없음.

### 네이티브 다이얼로그

- `alert`, `confirm`이 열리면 제어권이 사용자에게 넘어감.
  - 대응: 페이지를 열기 전에 `Page.addScriptToEvaluateOnNewDocument`로 다이얼로그를 미리 무력화.
  - 자세한 내용: [에이전트 브라우저 네이티브 다이얼로그](에이전트%20브라우저%20네이티브%20다이얼로그.md)

## 참고자료

| TITLE | URL |
|-------|-----|
| 조작 API, 제어권 교환, 팝업과 다운로드, 스냅샷 ref | [https://github.com/citrolabs/ego-lite/blob/main/package/ego-browser/skill-body/SKILL.md](https://github.com/citrolabs/ego-lite/blob/main/package/ego-browser/skill-body/SKILL.md) |
| 설치와 기존 브라우저 데이터 가져오기 | [https://github.com/citrolabs/ego-lite/blob/main/skills/ego-browser/references/install.md](https://github.com/citrolabs/ego-lite/blob/main/skills/ego-browser/references/install.md) |

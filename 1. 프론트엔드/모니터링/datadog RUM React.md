# Datadog RUM React

Datadog에서 제공하는 React 애플리케이션 모니터링 도구

## 등장 배경

과거와 달리 SPA가 대세가 된 지금에는 자바스크립트를 통해 화면의 필요한 부분만 동적으로 변경하기 때문에 전통적인 모니터링 방식으로는 모니터링 불가능. 따라서 '페이지 로드' 대신 '이벤트'를 추적

## 설치

```bash
pnpm install @datadog/browser-rum @datadog/browser-rum-react
```

| 패키지                          | 역할                                                      |
| ---------------------------- | ------------------------------------------------------- |
| `@datadog/browser-rum`       | RUM 본체(SDK). 성능·에러·세션 수집                                |
| `@datadog/browser-rum-react` | React 전용 플러그인. 라우터 추적, `ErrorBoundary`, `addReactError` |


## 환경 변수

Datadog 콘솔에서 값을 받아온다.

1. Datadog 로그인 (사내는 SSO 경유, 권한 없으면 계정 신청부터)
2. **RUM &amp; Session Replay &gt; Applications**
3. **New Application** → 프레임워크 **React** 선택
4. 발급된 **Application ID**와 **Client Token**을 환경 변수에 넣는다

```env
VITE_DATADOG_APPLICATION_ID=your-application-id
VITE_DATADOG_CLIENT_TOKEN=your-client-token
```

## 초기화

```ts
import { datadogRum } from '@datadog/browser-rum'
import { reactPlugin } from '@datadog/browser-rum-react'

datadogRum.init({
  applicationId: Environment.DATADOG_APPLICATION_ID,
  clientToken: Environment.DATADOG_CLIENT_TOKEN,
  site: 'datadoghq.com',
  service: 'homedot-hub',
  env: Environment.MODE,        // dev / stage / prod 구분
  version: Environment.VERSION, // 배포 버전별 지표 비교용
  plugins: [reactPlugin({ router: true })],

  sessionSampleRate: 100,       // 세션 수집 비율
  sessionReplaySampleRate: 100, // 그중 리플레이 녹화 비율
  defaultPrivacyLevel: 'mask-user-input',

  trackUserInteractions: true,
  trackResources: true,
  trackLongTasks: true,
})
```

## 라우트 추적

```ts
// AS-IS
import { createBrowserRouter } from 'react-router-dom'

// TO-BE
import { createBrowserRouter } from '@datadog/browser-rum-react/react-router-v6'
```

| 모드          | 대표 API                                     | 무엇이 추가되나                             |
| ----------- | ------------------------------------------ | ------------------------------------ |
| Declarative | `<BrowserRouter>`, `<Routes>/<Route>`      | URL 매칭, 이동, active 상태                |
| Data        | `createBrowserRouter`, `RouterProvider`    | + `loader`, `action`, pending 상태, `useFetcher` |
| Framework   | Vite 플러그인 + Route Module                   | + 타입 안전 `href`, 코드 분할, SSR/SPA/정적 전략 |

공식 문서 표현으로는 "기능이 누적되는 관계이고, Declarative → Data → Framework로 갈수록 기능이 늘고 아키텍처 통제권은 줄어든다" 입니다.

## 에러 추적

### React 19 `createRoot` 옵션

React 19부터 루트 단위 에러 핸들러가 생겼고, 여기에 `addReactError`를 연결한다.


| 옵션                   | 잡는 에러                                   |
| -------------------- | --------------------------------------- |
| `onUncaughtError`    | 앱을 크래시시키는 에러                            |
| `onCaughtError`      | ErrorBoundary가 잡은 에러                    |
| `onRecoverableError` | React가 복구 가능한 에러 (hydration mismatch 등) |


```tsx
import { addReactError } from '@datadog/browser-rum-react'

createRoot(document.getElementById('root')!, {
  onUncaughtError: (error, errorInfo) => addReactError(error, errorInfo),
  onCaughtError: (error, errorInfo) => addReactError(error, errorInfo),
  onRecoverableError: (error, errorInfo) => addReactError(error, errorInfo),
}).render(<App />)
```

### ⚠️ 중복 보고 주의

Datadog이 제공하는 `ErrorBoundary`를 쓰거나, 직접 만든 바운더리의 `componentDidCatch`에서 `addReactError`를 부르고 있다면 `**onCaughtError`는 빼야 한다.** 안 그러면 같은 에러가 두 번 올라간다.


| 우리 코드 상태                         | `onCaughtError` |
| -------------------------------- | --------------- |
| ErrorBoundary에서 Datadog에 보고하지 않음 | 넣는다             |
| Datadog `ErrorBoundary` 사용 중     | **뺀다**          |
| 커스텀 바운더리에서 `addReactError` 호출 중  | **뺀다**          |


## 사용자 정보

로그인·로그아웃 시점에 붙였다 떼면, 에러나 세션 리플레이를 **사람 단위로** 추적할 수 있다.

```ts
datadogRum.setUser({ id, name, email })  // 로그인 시
datadogRum.clearUser()                   // 로그아웃 시
```

넣는 값(허브 유저 ID, 이름, 로그인 아이디 등)은 팀 정책을 따른다. 개인정보가 들어가는 자리라 **무엇을 넣을지 합의하고 넣는 게 맞다.**

## 샘플링과 개인정보

### 샘플링 두 개를 구분한다


| 옵션                        | 뜻                           |
| ------------------------- | --------------------------- |
| `sessionSampleRate`       | 전체 세션 중 **RUM 데이터를 수집할** 비율 |
| `sessionReplaySampleRate` | 수집한 세션 중 **화면을 녹화할** 비율     |


리플레이는 용량이 커서 **과금에 직결된다.** 100%로 둘 거면 운영 환경에서만, 그리고 비용을 보면서 조절한다.

### `defaultPrivacyLevel`


| 값                   | 동작                                                               |
| ------------------- | ---------------------------------------------------------------- |
| `'mask'`            | HTML 텍스트, 사용자 입력, 이미지, 링크, `data-*` 속성까지 전반 마스킹. 화면이 와이어프레임처럼 보임 |
| `'mask-user-input'` | 폼 필드(input/textarea/checkbox 값 등)만 마스킹, 일반 텍스트는 그대로 기록           |
| `'allow'`           | 마스킹 없이 전부 기록                                                     |


세 값 모두 SDK 표준 옵션이다. `'allow'`는 리플레이가 제일 잘 보이지만 **화면에 뜬 개인정보가 그대로 Datadog에 저장된다.** 어떤 값을 쓸지는 성능 문제가 아니라 개인정보 처리 방침 문제로 접근해야 한다.

부분 제어는 DOM 속성으로 한다.

```html
<div data-dd-privacy="mask">여기만 가림</div>
<div data-dd-privacy="allow">여기만 보임</div>
```

## 히트맵

```ts
enableExperimentalFeatures: ['clickmap']
```

사용자가 어디를 클릭하고 어디까지 스크롤하는지 시각화. 실험적 기능 플래그라 SDK 버전 올릴 때 **동작 여부를 다시 확인**해야 한다.

## 백엔드까지 연결하기

프론트 에러를 백엔드 트레이스와 이어 보려면(= [datadog.md](./datadog.md)에서 말한 E2E 분석) 이 설정이 필요하다.

```ts
allowedTracingUrls: [
  { match: /https:\/\/api\..*\.com/, propagatorTypes: ['datadog', 'tracecontext'] },
],
```

이게 없으면 RUM 세션과 APM 트레이스가 따로 논다. 백엔드에 트레이스 헤더를 받을 준비(CORS 허용 헤더 포함)가 되어 있어야 한다.

## 모니터링 종류

### 1. 라우트 변경

React Router. 위 [라우트 추적](#라우트-추적--router-true만으로는-안-된다) 참고

### 2. 사용자 상호작용

'클릭'부터 '화면 반응' 까지의 모든 순간

- 사용자의 입력(`click`, `scroll` 등)이 발생한 시점부터, 그에 대한 **시각적 반응(UI 변경)이 완료되는 시점까지의 시간**을 추적
- API 호출, 데이터 상태 변경, 컴포넌트 리렌더링 등
- ex) 조회 버튼 클릭 → 로딩 스피너 표시 → API 통신 → 완료 측정하여 병목 구간 검토

### 3. 오류 추적

실제 사용자 환경에서 발생하는 오류를 잡아내는 것이 중요. (간헐적 발생 에러 등)

- Digital Experience &gt; RUM (`/rum/sessions`) 여기서 오류 발생 시간대에 접속한 세션 리플레이 확인 가능

### 4. 자동으로 같이 수집되는 것


| 항목      | 내용                     |
| ------- | ---------------------- |
| 페이지 성능  | 로드 시간, Core Web Vitals |
| 리소스 성능  | 이미지·스크립트·스타일시트 로딩 시간   |
| 네트워크 요청 | API 호출 추적              |


## 알림 연동

Datadog **Monitors**에서 조건을 걸고 Slack 채널로 알림을 보낸다.

## 트러블슈팅

### 1. Datadog이 초기화가 안 됨

1. **환경 변수 확인** — `@shared/consts/env.ts`의 기본값 또는 `.env`에 `VITE_DATADOG_APPLICATION_ID`, `VITE_DATADOG_CLIENT_TOKEN`이 들어 있는지
2. **네트워크 탭 확인** — `datadoghq.com` 또는 `intake`로 필터링해서 요청이 나가는지 확인
3. 광고 차단 확장 프로그램이 `intake` 요청을 막는 경우가 있다. 시크릿 창으로 교차 확인

### 2. 403 Forbidden

Application ID와 Client Token이 맞는지 확인.
**RUM &gt; Applications &gt; [앱] &gt; Setup**에서 실제 값과 대조한다. 앱을 여러 개 만들어 두고 다른 앱의 토큰을 넣은 경우가 흔하다.

# 참고자료


| TITLE                               | URL                                                                                                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Datadog React 통합 문서                 | [https://docs.datadoghq.com/integrations/rum-react/](https://docs.datadoghq.com/integrations/rum-react/)                                                         |
| @datadog/browser-rum-react SDK 레퍼런스 | [https://datadoghq.dev/browser-sdk/modules/_datadog_browser-rum-react.html](https://datadoghq.dev/browser-sdk/modules/_datadog_browser-rum-react.html)           |
| RUM Browser 모니터링 설정                 | [https://docs.datadoghq.com/real_user_monitoring/browser/](https://docs.datadoghq.com/real_user_monitoring/browser/)                                             |
| 데이터독의 프론트엔드 모니터링 (영상)               | [https://www.youtube.com/watch?v=xAMFnk0T-RE&amp;t=1627s](https://www.youtube.com/watch?v=xAMFnk0T-RE&t=1627s)                                                   |
| Frontend Datadog 활용하기               | [https://velog.io/@wns450/Frontend-Datadog-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0](https://velog.io/@wns450/Frontend-Datadog-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0) |



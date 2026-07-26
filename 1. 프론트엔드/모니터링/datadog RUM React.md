# Datadog RUM React

Datadog에서 제공하는 React 애플리케이션 모니터링 도구

## 등장 배경

과거와 달리 SPA가 대세가 된 지금에는 자바스크립트를 통해 화면의 필요한 부분만 동적으로 변경하기 때문에 전통적인 모니터링 방식으로는 모니터링 불가능. 따라서 '페이지 로드' 대신 '이벤트'를 추적

## 설치

```bash
pnpm install
```

## 환경 변수

Datadog에서 새 RUM 애플리케이션 생성 후 React 선택. 스크립트 복사해서 환경변수 셋팅

```env
VITE_DATADOG_APPLICATION_ID=your-application-id
VITE_DATADOG_CLIENT_TOKEN=your-client-token
```

## 모니터링 종류

### 1. 라우트 변경

ReactRouter

### 2. 사용자 상호작용

'클릭'부터 '화면 반응' 까지의 모든 순간

- 사용자의 입력(`click`, `scroll` 등)이 발생한 시점부터, 그에 대한 **시각적 반응(UI 변경)이 완료되는 시점까지의 시간**을 추적
- API 호출, 데이터 상태 변경, 컴포넌트 리렌더링 등
- ex) 조회 버튼 클릭 -&gt; 로딩 스피너 표시 -&gt; API 통신 -&gt; 완료 측정하여 병목 구간 검토

### 3. 오류 추적

실제 사용자 환경에서 발생하는 오류를 잡아내는 것이 중요. (간헐적 발생 에러 등)



&nbsp;
# Datadog RUM React
Datadog에서 제공하는 React 애플리케이션 모니터링 도구

## 특징
- 서버 응답 속도 저하, 렌더링 차단 등의 원인을 디버깅
- 사용자 이벤트 추적, 오류 알림 기능 추가 가능

## 설치
```bash
pnpm install
```

## 환경 변수 설정
Datadog에서 새 RUM 애플리케이션 생성 후 React 선택. 스크립트 복사해서 환경변수 셋팅

```env
VITE_DATADOG_APPLICATION_ID=your-application-id
VITE_DATADOG_CLIENT_TOKEN=your-client-token
```

## 기능

### 오류 추적
React 컴포넌트 렌더링 오류 추적

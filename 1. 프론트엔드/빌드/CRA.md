# CRA
> ❗ Deprecated

create-react-app


## PWA 템플릿
CRA를 활용한 PWA(Progessive Web App) 템플릿(푸시알림, 자이로센서 등 사용가능)

### 설치방법

```powershell
npx create-react-app my-app --template cra-template-pwa
```

### 기존 프로젝트로 복사 방법
1. 위 명령어 실행 후 아래 파일 두개 프로젝트로 복사

```
my-app
ㄴ "service-worker.js" //오프라인에서도 사이트 열 수 있게 도와줌
ㄴ "serviceWorkerRegistration.js"
```

2. index.js 수정

```jsx
import * as serviceWorkerRegistration from './serviceWorkerRegistration';

serviceWorkerRegistration.register(); // unregister90로 되어있다면 register()로 변경
```

### 앱 꾸미기
manifest.json 를 이용하여 아이콘, 배경, 시작경로 등 지정

> 💡 PWA vs 하이브리드 차이점 ??
> - 하이브리드 웹앱 : 코드(HTML, JS, CSS)는 APK로 패키징<br/>
> - PWA : 코드(HTML, JS, CSS)는 웹에 있으며 APK로 패키징 X
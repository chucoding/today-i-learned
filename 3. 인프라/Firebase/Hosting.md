# Firebase Hosting
정적 및 SPA 웹 앱 또는 PWA를 위한 빠르고 안전한 호스팅을 제공
- Cloud Functions 또는 Cloud Run과 페어링하여 사용

> SSR, Next.js앱을 빌드하려면 Firebase App Hosting 사용

## 주요 기능
- SSL 기본 제공
- 빠른 콘텐츠 제공
  - 업로드하는 각 파일이 전 세계 CDN 에지의 SSD에 캐싱되고 gzip 또는 Brotli로 제공됨.
  - 콘텐츠에 적합한 압축 방법이 자동으로 선택됨.
  - 사용자가 어디에 있든 빠르게 콘텐츠가 전송됨.(Google의 글로벌 CDN 중 사용자와 가장 가까운 에지 서버에서 SSL 연결을 통해 제공됨.)
- Firebase CLI 제공(빌드 및 배포 용이)
- `web.app`, `firebaseapp.com` 무료 도메인 제공
- Cloud Logging에 연결하거나 GA에 연동하여 방문 위치, 시기, 응답 상태, 지연 시간 등을 확인할 수 있음.
- CI/CD 지원 (Cloud Build, Cloud Run을 사용하여 Github 연동)
- 404 페이지 지원
- i18n 다국어 지원
- 커스텀 헤더를 통해 CDN에서 웹 앱이 캐싱디는 방식을 관리

## 초기화

### 진행 순서
1. Firebase CLI 설치 `npm install -g firebase-tools`
2. Firebase 프로젝트 만들기 및 앱 등록 (가이드 참고)
3. Firebase 로그인 `firebase login`
4. 프로젝트 초기화 `firebase init hosting`

### 설정 파일(firebase.json)
`firebase init hosting` 명령어 실행시 아래와 같이 설정 파일이 생성됨. 
```json
{
  "hosting": {
    "public": "public", // 배포할 디렉토리 지정(보통은 dist 또는 build)
    "ignore": [ // 배포시 무시할 파일 지정
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "rewrites": [ // 404방지, URL 단축 등 설정
      // 기본값 : 존재하지 않는 파일 또는 디렉터리에 대한 요청에 대해 index.html 제공
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```
### 프로젝트 연결 설정(.firebaserc)
로컬 디렉터리가 **어떤 Firebase 프로젝트와 연결되어 있는지** 저장하는 프로젝트 설정 파일.  
- `firebase init` 시 선택한 프로젝트가 `default`로 저장됨.  
- **default가 있으면 `firebase deploy`만 실행해도 해당 프로젝트로 배포됨** (`--project` 옵션 불필요).
- 여러 프로젝트(prod, staging 등)를 **별칭(alias)** 으로 등록해 두었다면, default가 아닌 프로젝트로 배포할 때만 `--project` 옵션 사용. (`firebase use --add`로 별칭 추가 가능)

```bash
firebase deploy                    # default 프로젝트로 배포
firebase deploy --project staging # staging 별칭 프로젝트로 배포
```

`.firebaserc` 예시:
```json
{
  "projects": {
    "default": "my-project-prod",
    "prod": "my-project-prod",
    "staging": "my-project-staging"
  }
}
```


## 배포
1. 테스트 `firebase emulators:start`
2. 배포 `firebase deploy` (롤백은 콘솔에서 가능)

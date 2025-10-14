# Github

## Github Actions
```yaml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4
      - uses: google-github-actions/auth@v2
        with:
          credentials_json: ${{ secrets.GCLOUD_SA_KEY }}  # <- prod 환경 시크릿
      - run: npm ci && npm run build
      - run: |
          npm i -g firebase-tools
          firebase deploy \
            --project ${{ secrets.FIREBASE_PROJECT_ID }} \
            --only hosting,functions
```
### 액션 종류
- `pnpm/action-setup@v2` : Github Actions 실행 환경에 pnpm을 설치
- `actions/setup-node@v4` : Node.js 환경을 설정하고 pnpm 캐시 활성화

> 💡 node-version을 생략하면 GitHub Actions는 기본적으로 최신 LTS 버전을 사용

### environments
배포 보호 규칙을 사용하여 환경을 만들고 해당 환경을 보호 (ex. production / staging )
- public repo에서만 무료 사용 가능

### secrets

#### 사용 제한
- repository secrets : 100
- environment secrets : 100
- organization secrets : 1000

#### Repository secrets
- 범위: 레포지토리 내 모든 워크플로/JOB에서 사용 가능.
- 사용법: `secrets.MY_SECRET`로 어디서나 참조.
- 보호: 일반 비밀값 보호만(로그 마스킹 등). 환경별 승인/대기 같은 추가 보호는 없음. 

#### Environment secrets
- 범위: 특정 환경(예: staging, production)로 한정.
→ JOB에 `environment: production` 처럼 선언해야 해당 환경 시크릿/변수가 노출됨
- 보호: 환경의 필수 리뷰어(승인), 대기 시간, 배포 브랜치 제한 같은 보호 규칙을 붙일 수 있어 배포 경로를 더 엄격하게 통제 가능.
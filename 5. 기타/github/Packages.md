# Github Packages

컨테이너 및 기타 종속성을 포함한 패키지를 호스팅하고 관리하는 플랫폼

- 소프트웨어 개발을 중앙 집중화 목적
- Github API, Github Actions 를 통해 CI 구축 가능
- npm, maven, docker 등 다양한 패키지 레지스트리 제공
- 공개 패키지 무료 / 비공개 패키지 유료(무료 저장 공간, 데이터 전송량 제공)

## NPM 레지스트리
`npm.pkg.github.com` 레지스트리에 npm 패키지를 올리는 방식

### 셋팅 순서

1. 프라이빗 레포 생성
2. **패키지 설정**
    1. **패키지 이름을 scope로 맞추기**
        - `package.json`의 name을 `@ORG/package-name` 형태로
    2. **publishConfig로 레지스트리 지정**
        - `publishConfig.registry = "https://npm.pkg.github.com"`
3. **인증(.npmrc) 구성**
    - 로컬 개발자 PC: GitHub PAT(토큰)으로 인증
    - CI: `GITHUB_TOKEN` 또는 PAT 사용
4. **권한**
    - CI에서 퍼블리시하려면 `packages: write` 권한 필요(워크플로 권한 설정)
5. 배포
    - npm publish

### 사용하는 레포에서 사용 방법

- `.npmrc`에 scope별 registry 매핑을 해줘야 함(“우리 org scope는 GitHub Packages에서 받아라”)
- 예: `@ORG:registry=https://npm.pkg.github.com`

## 참고자료
https://www.notion.so/chucoding/NPM-312fd64d44a08089aa5efb70450ce7f8#312fd64d44a080aaac54fe2235a57675
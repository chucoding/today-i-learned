# Amplify

풀스택(Full Stack) 애플리케이션 개발을 지원하는 개발 플랫폼

- version -&gt; frontend -&gt; phases -&gt; artifacts -&gt; cache 구조
- pnpm 사용시 전역 설치하되 저장소 버전 10.15.1로 고정

## 도메인 설정

Amplify 콘솔 '호스팅' -&gt; '사용자 지정 도메인' 메뉴에서 간편하게 연결

## 스토리북

새 Amplify 앱으로 연결

## 단일레포 구성 (App + 스토리북)

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm install --global pnpm@10.15.1
        - pnpm install --frozen-lockfile
    build:
      commands:
        # Storybook Amplify 앱 환경 변수: BUILD_TARGET=storybook
        - |
          case "${BUILD_TARGET:-app}" in
            app)
              pnpm build
              ;;
            storybook)
              pnpm build-storybook --output-dir dist --disable-telemetry
              ;;
            *)
              echo "지원하지 않는 BUILD_TARGET: ${BUILD_TARGET}" >&2
              exit 1
              ;;
          esac
  artifacts:
    baseDirectory: dist
    files:
      - "**/*"
  cache:
    paths:
      - node_modules/**/*

```

.npmrc
```
@rabyss:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${HOMEDOT_GITHUB_TOKEN}
node-linker=hoisted

```

BUILD_TARGET=storybook 같은 환경변수를 지정하여 amplify.yml이 다음처럼 앱별 빌드를 선택하도록 구성

# 모노레포 구성

> 모노 리포지토리 빌드 설정 구성 : [AWS Amplify 호스팅](https://docs.aws.amazon.com/ko_kr/amplify/latest/userguide/monorepo-configuration.html)

- 호스팅 > 환경변수 : `AMPLIFY_MONOREPO_APP_ROOT` 추가


# 참고자료
[AWS 빌드 설정 예제](https://docs.aws.amazon.com/amplify/latest/userguide/yml-specification-syntax.html)
[Storybook 배포 가이드](https://storybook.js.org/docs/sharing/publish-storybook)
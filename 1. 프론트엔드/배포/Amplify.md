# Amplify

풀스택(Full Stack) 애플리케이션 개발을 지원하는 개발 플랫폼

- version -&gt; frontend -&gt; phases -&gt; artifacts -&gt; cache 구조
- pnpm 사용시 전역 설치하되 저장소 버전 10.15.1로 고정

## 빌드 설정 파일명은 amplify.yml 고정

YAML 확장자는 `.yaml` 이 표준 선호다. IANA 미디어 타입 등록(`application/yaml`, RFC 9512)이
이렇게 적는다.

> File extension(s): **"yaml" (preferred)** and "yml"

`.yml` 3글자는 DOS 8.3 파일명 제약이 남긴 흔적이고 지금은 안전성 이점이 없다. 그래서 새로
만드는 YAML 파일은 `.yaml` 이 맞다.

**그런데 Amplify 빌드 설정만은 예외다.** Amplify 가 `amplify.yml` 이라는 이름으로만 찾는다.

> Save the build settings in your repository - Download the `amplify.yml` file and add it
> to the root of your repository.

`amplify.yaml` 로 바꾸면 어떻게 되나:

- Amplify 가 파일을 못 찾는다
- 에러를 내지 않고 **콘솔에 저장된 빌드 설정으로 조용히 되돌아간다**
- 파일에 적어둔 Node 버전, 패키지 매니저, 캐시 경로가 전부 무시된다
- 빌드는 성공하는데 결과만 다르므로 원인을 찾기 어렵다

AWS 문서 여러 페이지에서 표기를 세어봐도 `amplify.yml` 만 나오고 `amplify.yaml` 은 0건이다.

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

## 브랜치별 환경변수

호스팅 > 환경 변수에서 `새 항목 추가`로 만든 변수는 브랜치 컬럼이 `모든 브랜치`로 고정되어 수정할 수 없음.
브랜치별 값은 이미 만들어 둔 변수에 **재정의(override)** 를 붙이는 방식으로 지정.

1. 호스팅 > 환경 변수 > `변수 관리`
2. `새 항목 추가`로 변수 생성 (이름 + 기본값, 브랜치는 `모든 브랜치` 그대로 둠)
3. 해당 변수 행의 `작업(Actions)` > `변수 재정의 추가(Add variable override)`
4. 추가된 행에서 브랜치 드롭다운이 활성화됨. 브랜치 선택 후 값 입력
5. 저장

- `기본값 1개(모든 브랜치) + 브랜치별 재정의 N개` 구조라 같은 변수명으로 브랜치마다 행을 추가
- 환경 변수 변경은 다음 빌드부터 적용. 기존 배포에 반영하려면 해당 브랜치 재배포 필요
- CLI로는 브랜치 단위로 직접 설정 가능

```bash
aws amplify update-branch --app-id <app-id> --branch-name <branch> --environment-variables KEY=VALUE
```

# 모노레포 구성

> 모노 리포지토리 빌드 설정 구성 : [AWS Amplify 호스팅](https://docs.aws.amazon.com/ko_kr/amplify/latest/userguide/monorepo-configuration.html)

- 호스팅 > 환경변수 : `AMPLIFY_MONOREPO_APP_ROOT` 추가


# 참고자료
[환경 변수 설정](https://docs.aws.amazon.com/ko_kr/amplify/latest/userguide/environment-variables.html)
[빌드 설정 구성 (amplify.yml 파일명 근거)](https://docs.aws.amazon.com/amplify/latest/userguide/build-settings.html)
[RFC 9512 — application/yaml 미디어 타입](https://www.rfc-editor.org/rfc/rfc9512.html)
[AWS 빌드 설정 예제](https://docs.aws.amazon.com/amplify/latest/userguide/yml-specification-syntax.html)
[Storybook 배포 가이드](https://storybook.js.org/docs/sharing/publish-storybook)
# Github Actions

## 목차

- [액션 종류](#액션-종류)
  - [패키지 매니저](#패키지-매니저)
  - [Node 버전 지정](#node-버전-지정)
- [environments](#environments)
- [secrets](#secrets)
  - [사용 제한](#사용-제한)
  - [Repository secrets](#repository-secrets)
  - [Environment secrets](#environment-secrets)
- [참고자료](#참고자료)

## 액션 종류

- `pnpm/action-setup@v2` : Github Actions 실행 환경에 pnpm을 설치
- `actions/setup-node@v4` : Node.js 환경을 설정하고 pnpm 캐시 활성화

> 💡 node-version을 생략하면 러너 이미지에 미리 설치된 Node를 사용(이미지가 갱신되면 버전도 바뀜)

### 패키지 매니저

레포에서 쓰는 패키지 매니저를 CI에서도 그대로 맞추는 것이 중요.(테스트 환경 통일)

### Node 버전 지정

`node-version-file: .nvmrc`로 읽을 때 `.nvmrc`에 무엇을 적느냐로 갈림.


| 방식                         | `.nvmrc`  | 장점                       | 단점                                   |
| -------------------------- | --------- | ------------------------ | ------------------------------------ |
| 메이저만                       | `22`      | 관리 부담 없음                 | 러너 캐시의 22.x 사용, 이미지 갱신 전까지 이전 패치로 빌드 |
| 메이저 + `check-latest: true` | `22`      | 최신 보안 패치 자동 반영           | 빌드 시점마다 Node 달라짐, 최신 확인과 다운로드로 느려질 수 있음        |
| 정확한 버전 고정                  | `22.23.4` | 재현 가능한 빌드, 버전 변경이 PR로 남음 | 보안 릴리스마다 직접 올려야 함                    |


- `check-latest` 기본값은 `false`. 공식 문서도 안정성과 고정 버전을 원하면 끄라고 안내.

- 고정이 맞는 경우
  - 예전 태그를 다시 빌드해 롤백하는 배포. 자동 패치면 같은 태그도 그날의 Node로 빌드됨.
  - 정적 산출물만 배포하는 앱.
    - 런타임에 Node가 돌지 않아 자동 패치 이득이 작음.
- 고정 버전 자동 갱신은 Renovate `nvm` 매니저가 `.nvmrc`를 기본 지원.

## environments

배포 보호 규칙을 사용하여 환경을 만들고 해당 환경을 보호 (ex. production / staging )

- public repo에서만 무료 사용 가능

## secrets

### 사용 제한

- repository secrets : 100
- environment secrets : 100
- organization secrets : 1000

### Repository secrets

- 범위: 레포지토리 내 모든 워크플로/JOB에서 사용 가능.
- 사용법: `secrets.MY_SECRET`로 어디서나 참조.
- 보호: 일반 비밀값 보호만(로그 마스킹 등). 환경별 승인/대기 같은 추가 보호는 없음.

### Environment secrets

- 범위: 특정 환경(예: staging, production)로 한정.
→ JOB에 `environment: production` 처럼 선언해야 해당 환경 시크릿/변수가 노출됨
- 보호: 환경의 필수 리뷰어(승인), 대기 시간, 배포 브랜치 제한 같은 보호 규칙을 붙일 수 있어 배포 경로를 더 엄격하게 통제 가능.

## 참고자료


| TITLE                  | URL                                                                                                                                                                              |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| check-latest 동작과 기본값   | [https://github.com/actions/setup-node/blob/main/docs/advanced-usage.md](https://github.com/actions/setup-node/blob/main/docs/advanced-usage.md) |
| node-version 생략 시 동작 | [https://techearl.com/nodejs-github-actions-setup-node](https://techearl.com/nodejs-github-actions-setup-node)                                                                   |
| Renovate의 .nvmrc 자동 갱신 | [https://docs.renovatebot.com/modules/manager/nvm/](https://docs.renovatebot.com/modules/manager/nvm/)                                                                           |

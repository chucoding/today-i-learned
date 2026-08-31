# 브랜치 전략

여러 사람이 하나의 저장소에서 작업할 때 **브랜치를 언제 따고, 어디에 머지하고, 무엇을 배포할지**에 대한 약속.
정답은 없고 **배포 주기**와 **동시에 유지해야 하는 버전 수**에 따라 결정됨.

## 목차

| 전략 | 한 줄 |
| --- | --- |
| [GitHub Flow](./GitHub%20Flow.md) | `main` 하나만 유지. 머지하면 바로 배포 |
| [Git Flow](./Git%20Flow.md) | `develop`/`release`/`hotfix`까지 두는 정기 배포용 |
| [Deploy Branch](./Deploy%20Branch.md) | 환경별 브랜치를 둬서 배포 시점을 통제 |
| [Trunk Based Development](./TBD.md) | 하루 1회 이상 trunk에 통합. 짧은 브랜치만 |

## 비교

| 항목            | GitHub Flow      | Git Flow            | Deploy Branch      | Trunk Based        |
| -------------- | ---------------- | ------------------- | ------------------ | ------------------ |
| 상시 유지 브랜치   | `main`           | `master`, `develop` | `main` + 환경 브랜치  | `main`             |
| 배포 시점        | `main` 머지 시     | `release` 태깅 시     | 환경 브랜치 머지 시    | 수시(Feature Flag)  |
| 버전 동시 관리     | 어려움            | 쉬움                 | 보통                | 어려움              |
| 브랜치 수명       | 짧음~중간          | 김                  | 짧음                | 매우 짧음(1~2일)      |
| 필요한 CI/CD 수준 | 높음              | 낮음                 | 중간                | 매우 높음            |
| 적합한 경우       | 소규모 상시 배포     | 정기 배포/버저닝(B2B)   | 단계적 QA가 필요한 서비스 | 대규모 팀 + 강한 테스트 자동화 |

# 참고자료

| TITLE                                      | URL                                                                   |
| ------------------------------------------ | --------------------------------------------------------------------- |
| A successful Git branching model (Git Flow) | https://nvie.com/posts/a-successful-git-branching-model/               |
| GitHub flow                                 | https://docs.github.com/en/get-started/using-github/github-flow        |
| Trunk Based Development                     | https://trunkbaseddevelopment.com/                                     |
| 우리 팀은 이렇게 일해요 - 우아한형제들 Git flow      | https://techblog.woowahan.com/2553/                                    |

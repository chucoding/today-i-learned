# Git Flow

nvie(Vincent Driessen)의 [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/) 글로 알려진 전략.
`feature` - `develop` - `release` - `hotfix` - `master` 로 브랜치를 구성한다.

## 브랜치 구성

| 브랜치      | 파생 위치     | 머지 대상               | 설명                                       |
| ---------- | ----------- | --------------------- | ---------------------------------------- |
| `master`   | -           | -                     | 배포(릴리스)된 상태만 담는 브랜치. 계속 살아있어야 하는 브랜치. 머지 시 태그를 붙임 |
| `develop`  | `master`    | -                     | 다음 릴리스에 나갈 기능이 모이는 브랜치. 계속 살아있어야 하는 브랜치 |
| `feature`  | `develop`   | `develop`             | 기능 단위 작업 브랜치. 원격에 올리지 않고 로컬에서만 쓰기도 함 |
| `release`  | `develop`   | `master` + `develop`  | 배포 준비/QA용 브랜치. 여기서는 버그 수정과 버전 표기만 하고 새 기능은 넣지 않음 |
| `hotfix`   | `master`    | `master` + `develop`  | 배포된 버전에 긴급 장애가 생겼을 때 만드는 브랜치 |

> `release`와 `hotfix`는 `master`에 머지한 뒤 **`develop`에도 반드시 머지**해야 수정 내용이 유실되지 않음.

```
master  ──●───────────────●──────────●──▶  (태그: v1.0, v1.1, v1.1.1)
           \             /  \       /
            \      release   \  hotfix
             \      /         \   /
develop ──●───●────●───────────●─●──▶
           \ /
          feature
```

## 적합한 경우

- **정기 배포 및 버저닝이 필요한** 애플리케이션 (ex. B2B, 설치형/패키지 소프트웨어)
- 지속적으로 배포되는 웹 애플리케이션에는 과함(다양한 버전을 동시에 관리할 필요가 없으므로)
  - 원저자도 2020년에 "웹 앱이라면 GitHub Flow 같은 단순한 전략을 쓰라"는 주석을 글에 추가함

## 사례

(현) 배달의민족 전략

# 참고자료

| TITLE | URL |
| --- | --- |
| A successful Git branching model (Git Flow) | https://nvie.com/posts/a-successful-git-branching-model/ |
| 우리 팀은 이렇게 일해요 - 우아한형제들 Git flow | https://techblog.woowahan.com/2553/ |

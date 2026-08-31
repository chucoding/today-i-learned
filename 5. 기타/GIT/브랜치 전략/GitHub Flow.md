# GitHub Flow

`main` 브랜치 **하나만** 항상 배포 가능한 상태로 유지하는 단순한 전략

## 흐름

`main`에서 브랜치 생성 → 커밋 → PR → 리뷰 → `main`에 머지 → 즉시 배포

```
main  ──●────●────────●────●──▶  (머지될 때마다 배포)
          \  /          \  /
           ●             ●        feature 브랜치
```

## 특징

- 브랜치 이름 규칙이 강제되지 않음(자기 이름, 기능 이름 등 자유). 다만 다른 사람이 알아볼 수 있는 설명적인 이름 권장
- 상시 배포가 가능한 환경(CI/CD가 갖춰진 소규모 팀)에 유리

## 단점

머지 = 배포이므로 **테스트/QA가 끝날 때까지 머지를 대기**해야 함 → PR이 쌓이고 브랜치 수명이 길어짐

이 단점을 보완한 것이 [Deploy Branch](./Deploy%20Branch.md)다.

## 사례

(구) 배달의민족 전략

# 참고자료

| TITLE | URL |
| --- | --- |
| GitHub flow | https://docs.github.com/en/get-started/using-github/github-flow |

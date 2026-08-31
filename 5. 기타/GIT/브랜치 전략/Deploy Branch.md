# Deploy Branch (환경 브랜치)

[GitHub Flow](./GitHub%20Flow.md)의 단점(머지 = 즉시 배포라 배포 시점을 통제할 수 없음)을 보완한 전략.
`main` 외에 **환경별 배포 브랜치**를 따로 둔다. (ex. `main` → `staging` → `production`)

## 흐름

코드는 항상 **한 방향으로만** 흐른다.

`main`에 머지 → 검증되면 `staging`에 머지 → `production`에 머지 = 배포

```
main ──●──●──●──●──▶
        \     \
staging  ●─────●──▶      (QA 환경 배포)
          \     \
production ●─────●──▶    (운영 배포)
```

## 특징

- 배포 시점 = 브랜치 머지 시점이므로, **테스트 서버를 여러 개** 두고 단계적으로 검증할 수 있음
- GitLab Flow에서 말하는 "environment branches"와 같은 방식

## 사례

당근마켓 전략

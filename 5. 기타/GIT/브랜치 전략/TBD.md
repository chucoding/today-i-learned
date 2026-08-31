# Trunk Based Development (TBD)

모든 개발자가 **하나의 trunk(`main`)에 매우 자주(하루 1회 이상) 통합**하는 전략

## 흐름

- 브랜치를 아예 안 쓰거나, 쓰더라도 **수명이 1~2일을 넘지 않는 짧은 브랜치**만 사용
- 릴리스가 필요하면 trunk에서 `release` 브랜치를 따서 태깅. 버그 수정은 **trunk에 먼저 고치고 release로 cherry-pick**(release에서 trunk로 역머지하지 않음)
- 미완성 기능은 **Feature Flag** 또는 Branch by Abstraction으로 감춘 채 머지

```
main ──●──●──●──●──●──●──▶  (짧은 브랜치가 하루 안에 머지됨)
        \/    \/    \/
```

## 장점

- 브랜치가 오래 살지 않으므로 대규모 충돌(머지 지옥)이 발생하지 않음
- 통합 주기가 짧아 CI가 실질적으로 동작하고, 항상 배포 가능한 상태가 유지됨

## 단점 / 전제 조건

- 자동화 테스트와 CI 성숙도가 높아야 함(깨지면 바로 전체가 막힘)
- 리뷰를 짧게 가져가야 하므로 페어 프로그래밍이나 소규모 PR 문화가 필요
- Feature Flag가 늘어나면 그 자체가 관리 부채가 됨

## 사례

Google, Facebook 등 대규모 단일 저장소(monorepo) 환경에서 사용

# 참고자료

| TITLE | URL |
| --- | --- |
| Trunk Based Development | https://trunkbaseddevelopment.com/ |

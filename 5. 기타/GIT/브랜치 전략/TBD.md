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

## 태그 승격

`develop → staging → main` 처럼 코드를 환경 브랜치로 옮겨 나르는 방식과 대비되는 지점.

### staging 배포

```bash
$ git tag staging-2026.08.24 <커밋SHA>
$ git push origin staging-2026.08.24   # 파이프라인이 이 커밋을 staging에 배포
```

### prd 배포

staging에서 검증 끝난 **그 커밋을 그대로** 태그로 찍어 승격한다. 코드를 옮기는 게 아니라 같은 커밋을 다음 환경으로 배포만 반복.

```bash
$ git tag v1.42.0 <같은 커밋SHA>
$ git push origin v1.42.0
```

```
main ──●──●──●──●──▶
          ↑     ↑
          │     └ staging-2026.08.24  ← 이 커밋을 staging에 배포
          └ v1.41.0                     검증 후 같은 커밋에 v1.42.0 태그
```

### 롤백

직전 안정 태그를 재배포하면 끝. `main` 히스토리는 건드리지 않는다(revert 커밋도, 강제 푸시도 없음).

### 전제 조건

- **태그는 불변**. 한 번 푸시한 태그를 옮기지 않는다(`git tag -f`, `git push --force` 금지). 옮기는 순간 "그때 배포된 코드"를 특정할 수 없어짐
- **환경 차이는 코드가 아니라 설정으로 흡수**. 같은 커밋이 모든 환경에 배포되므로 환경별 값은 빌드 산출물 바깥(환경변수, 설정 서버)에 있어야 함. 코드에 환경 분기가 있으면 승격 모델 자체가 깨짐
- **핫픽스는 태그만으로 안 됨**. prd 태그 이후 trunk가 이미 앞서 나갔다면 그 태그에서 단기 release 브랜치를 따고, 수정은 **trunk에 먼저 넣은 뒤 cherry-pick**한다(위 [흐름](#흐름) 규칙과 동일)

## 장점

- 브랜치가 오래 살지 않으므로 대규모 충돌(머지 지옥)이 발생하지 않음
- 배포된 커밋을 태그로 특정할 수 있어 롤백과 추적이 단순함
- 통합 주기가 짧아 CI가 실질적으로 동작하고, 항상 배포 가능한 상태가 유지됨

## 단점 / 전제 조건

- 자동화 테스트와 CI 성숙도가 높아야 함(깨지면 바로 전체가 막힘)
- 리뷰를 짧게 가져가야 하므로 페어 프로그래밍이나 소규모 PR 문화가 필요
- Feature Flag가 늘어나면 그 자체가 관리 부채가 됨

## 사례

- Google, Facebook 등 대규모 단일 저장소(monorepo) 환경에서 사용
- 규모가 크거나 배포가 잦을수록 이득이 크다. 규제·고정 릴리스 일정 조직은 단기 release 브랜치를 곁들인 하이브리드로 간다.

# 참고자료


| TITLE                   | URL                                                                                                            |
| ----------------------- | -------------------------------------------------------------------------------------------------------------- |
| Trunk Based Development | [https://trunkbaseddevelopment.com/](https://trunkbaseddevelopment.com/)                                       |
| Release From Trunk      | [https://trunkbaseddevelopment.com/release-from-trunk/](https://trunkbaseddevelopment.com/release-from-trunk/) |



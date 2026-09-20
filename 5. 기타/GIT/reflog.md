# reflog
참조가 움직인 기록을 로컬에 남기는 Git의 복구 장치 정리

## reflog
`HEAD`와 각 브랜치 끝이 어디에서 어디로 옮겨갔는지 기록해 둔 로그
- 커밋, `reset`, `rebase`, `checkout`, `cherry-pick`처럼 참조가 움직일 때마다 한 줄씩 추가됨
- `.git/logs/HEAD`와 `.git/logs/refs/`에 평문으로 저장됨
- 로컬 전용. `push`로 전송되지 않고 `clone`에도 따라오지 않음

```shell
git reflog -3 --date=iso
# 352d517 HEAD@{2026-09-20 15:33:48 +0900}: reset: moving to origin/main
# b977c28 HEAD@{2026-09-20 15:32:24 +0900}: commit: feat : 스킬 추가
# 8a614eb HEAD@{2026-09-20 15:32:18 +0900}: commit: chore : 캐시 추적 제외
```

## 용도
`reset --hard`로 날린 커밋이나 삭제한 브랜치의 끝 커밋도 reflog에 SHA가 남아 있으면 되살릴 수 있음

```shell
git reflog                  # 날리기 직전 SHA 확인
git reset --hard <SHA>      # 그 시점으로 복구
```

`git log`는 현재 참조에서 도달 가능한 커밋만 보여주므로 이미 날린 커밋은 나오지 않음. 이때 확인할 곳이 reflog임

## 브랜치를 지워도 객체가 남는 이유
- 브랜치 삭제는 그 커밋으로 가는 정상 경로를 없애는 것이지 객체를 지우는 것이 아님
- reflog 항목이 여전히 해당 SHA를 가리키므로 객체는 저장소 안에 그대로 남음
- 민감한 파일이 든 커밋을 이력에서 걷어내도 로컬에는 한동안 남아 있을 수 있음

## 만료 규칙
기한은 정해져 있으나 날짜만 지난다고 지워지지 않음

| 설정 | 대상 | 기본값 |
| --- | --- | --- |
| `gc.reflogExpire` | 도달 가능한 항목 | 90일 |
| `gc.reflogExpireUnreachable` | 도달 불가능한 항목(지운 브랜치 등) | 30일 |
| `gc.pruneExpire` | 기한 지난 객체 정리 | 2주 |

```shell
git config --get gc.reflogExpire   # 출력이 없으면 기본값 사용 중
```

## 자동 정리가 돌지 않는 경우
- 위 기한은 `git gc`가 실제로 실행되어야 적용됨
- Git은 커밋이나 리베이스 뒤에 `git gc --auto`를 호출하지만, 느슨한 객체가 `gc.auto`(기본 6700)를 넘어야 실제로 정리함
- 작은 저장소는 이 임계치에 도달하지 않아 몇 달이 지나도 정리되지 않을 수 있음

```shell
git count-objects -v
# count: 827          <- 느슨한 객체 수. 6700에 한참 못 미쳐 auto gc가 돌지 않음
# in-pack: 2193
```

## 즉시 정리
```shell
git reflog expire --expire=now --all
git gc --prune=now
```

> 💡 저장소 전체의 복구 기록이 함께 사라짐  
> 이 명령을 돌리면 실수로 날린 커밋을 되살릴 수단이 없어지므로, 목적이 분명할 때만 사용

## 실제 사례
공개 저장소에 올리면 안 되는 파일이 커밋된 것을 뒤늦게 발견해 이력을 다시 쓰고 강제 푸시한 경우

1. 원격이 강제 갱신되어 로컬과 공통 조상이 사라짐
2. 로컬을 `git reset --hard origin/main`으로 새 이력에 맞추고, 작업하던 커밋은 `cherry-pick`으로 옮겨 붙임
3. 되돌릴 수 있도록 리셋 전에 `git branch backup/...`으로 옛 이력을 붙잡아 둠
4. 결과를 확인한 뒤 백업 브랜치를 삭제

여기서 3번의 백업 브랜치를 지워도 해당 커밋의 객체는 reflog 때문에 남아 있음. 원격에서는 이미 걷어냈고 로컬 밖으로 나가지 않으므로 대개 방치해도 무방하지만, 로컬에서도 지우려면 위의 `즉시 정리`가 필요함

> 💡 강제 푸시된 원격을 만나면 로컬 이력을 밀어 넣지 않기  
> `git push --force`로 맞추면 상대가 걷어낸 파일이 되살아남. `reset` 후 `cherry-pick`이나 `rebase`로 내 커밋만 새 이력 위로 옮기는 것이 안전

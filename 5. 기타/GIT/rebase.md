# Git Rebase
Git 충돌시 깔끔하게 머지할 수 있는 방법

## 설명
한 브랜치의 베이스 커밋을 다른 브랜치의 최신 커밋으로 재설정하여, 커밋 히스토리를 일직선으로 깔끔하게 정리하는 명령

## 사용방법
1. 최신 브랜치 정보 받아오기
```shell
git fetch origin
```

2. 작업중인 브랜치에서 리베이스 수행
```shell
git rebase origin/develop
```

3. 충돌 수정 후
```shell
git rebase --continue
```

4. 원격 feature 브랜치에 반영
```shell
git push --force-with-lease
```
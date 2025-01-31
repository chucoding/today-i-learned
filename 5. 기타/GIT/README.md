# GIT
파일 변경사항 추적 및 협업을 위한 스냅샷 스트림 기반 분산 버전 관리 시스템

## 명령어

### clone
``` cmd
git clone -b {branch} --single-branch {URL}
```

### commit

```cmd
git status                            # 상태보기
git commit -m "커밋메시지"             # 커밋
git rm --cached node_modeules -r      # staged 상태에 있는 코드를 지워줌
git diff                              # 변경사항 보기
git log -p {filepath}                 # 로그보기
git push                              # 원격 저장소에 싱크하기
```

### merge
현재 브랜치에 {branch} 브랜치를 병합

```cmd
git merge {branch}
```
※ 상황에 따라서 Fast-forward merge 또는 3-way merge 발생

### rollback
**원복시키기(커밋내역도 함께 원복)**

```cmd
git reset --hard HEAD^
```

**원복시키기(커밋내역은 놔두고 내용만 원복)**

```cmd
git reset --soft HEAD^
```
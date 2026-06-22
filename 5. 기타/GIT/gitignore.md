# `.gitignore`
Git에서 파일 무시하는 방법 정리

## 종류

### `.gitignore`
저장소에 커밋되는 공유 규칙
- 무시할 파일명을 작성하는 일반적인 파일
- 코드들과 함께 Git에 체크인됨.
- `.gitignore` 규칙에 매칭되는 파일은 `git` 명령 실행 시 고려되지 않음.

### `.git/info/exclude`
저장소별 개인 규칙
- 이 파일의 변경 사항은 Git에 체크인되지 않음
- 저장소별로만 무시하고 싶지만 `.gitignore`에는 넣고 싶지 않은 파일에 적합함
- 예: `CLAUDE.md` 등  개인 워크플로에만 필요한 파일을 저장소에 커밋하지 않고, 프로젝트의 .gitignore에도 추가하지 않으려면 `.git/info/exclude`에 `CLAUDE.md`를 추가함

### `~/.config/git/ignore`
머신 전역 규칙
- 여기에 추가한 파일명은 머신 수준(컴퓨터의 모든 Git 저장소)에서 전역 무시됨.
- Git에 체크인되지 않으며 특정 저장소와 연결되지 않음.
- 예: macOS에서는 `.DS_Store`를 여기에 추가하는 것이 적합함.

## 무시 규칙 체크
`git check-ignore -v <파일명>`으로 특정 파일이 어떤 규칙에 의해 무시되는지 확인할 수 있음

```shell
git check-ignore -v .DS_Store
# .gitignore:1:.DS_Store	.DS_Store
# .git/info/exclude:7:.DS_Store	.DS_Store
# /Users/hssuh/.config/git/ignore:2:.DS_Store	.DS_Store
```
특정 파일을 무시하는 규칙이 없으면 `git check-ignore -v` 명령은 아무 출력도 내지 않음
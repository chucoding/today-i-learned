# ClaudeCode
코드베이스를 읽고, 파일을 편집하고, 명령을 실행하고, 개발 도구와 통합하는 에이전트 코딩 도구

> [ClaudeCode 공식문서](https://code.claude.com/docs/ko/overview)


## 설치
```shell
npm install -g @anthropic-ai/claude-code
```

## 실행
```shell
claude
```

### Plan Mode
후속 질문을 통해 현재 구현을 분석하고 포괄적인 계획을 생성
```
claude --permission-mode plan
```

#### 특징
- 기능이 많은 파일을 편집해야 할 때 사용
- 코드 탐색: 무엇이든 변경하기 전에 코드베이스를 철저히 조사하고 싶을 때
- 대화형 개발: Claude와 방향을 반복하고 싶을 때
- `Shift+Tab`으로 전환 가능

#### 질문이랑 같이 실행하는 방법
```
claude --permission-mode plan -p "Analyze the authentication system and suggest improvements"
```

## Claude Code for Cursor
Cursor 터미널에서 claude 실행시 Cursor extension이 CLI를 감지해서 IDE bridge를 연결

> Welcome to Claude Code for Cursor installed extension v2.1.72

### 장점
- Claude가 수정하면 Cursor 에디터에 diff 표시
- commit / diff를 IDE UI로 표시

### 단점
- 컨텍스트 공유 안됨
  - (ex. 에디터에서 열린 파일들, cursor 채팅 등)

## VSCode Extension
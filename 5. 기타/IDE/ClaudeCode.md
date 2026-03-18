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

> 💡 질문이랑 같이 실행하는 방법
> ```
> claude --permission-mode plan -p "Analyze the > authentication system and suggest improvements"
> ```

#### 워크플로우
1. 요구사항 확인
- AskUserQuestion을 사용해 목표와 요구사항을 더 명확히 한 뒤 다음 단계로 넘어감.
2. 코드베이스 조사
- 요구사항이 정리되면 Claude는 읽기 전용 도구로 관련 파일과 구현 패턴을 조사.
- Plan 서브에이전트가 맥락 수집을 맡음(쓰기/수정 도구 없이 동작 - haiku)
3. 계획 초안 수립
- 조사 결과를 바탕으로 Claude는 어떤 방식으로 구현할지, 어느 영역이 영향을 받는지, 어떤 순서로 진행하는 게 안전한지를 정리해 계획 초안을 구성
4. 후속 질문과 계획 보정
초안이 나온 뒤에도 바로 실행으로 넘어가는게 아니라 사용자가 후속 질문을 통해 계획을 더 다듬을 수 있음 

### opusplan
```claude-cli
/model opusplan
```
Plan mode에서는 Opus, Execution mode에서는 Sonnet으로 자동 전환
- 계정티어에 따라 default 모델 다름
- Max/Team Premium인 경우 기본 Opus 4.6
- Pro/Team Standard는 Sonnet 4.6
- Opus 사용 임계치에 걸리면 Sonnet으로 자동 폴백
> https://code.claude.com/docs/en/model-config

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
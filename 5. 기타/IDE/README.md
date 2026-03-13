# IDE
코딩 도구 정리

## Context Engineering

| 툴      | 철학              |
| ------ | --------------- |
| Cursor | IDE가 context 준비 |
| Aider  | 사람이 context 선택  |
| Devin  | LLM이 repo 탐험    |

### IDE Indexing 방식 (구 Cursor)
```
User Prompt
   ↓
IDE index (AST / symbols)
   ↓
semantic search
   ↓
관련 파일 자동 선택
   ↓
LLM
```
#### 장점
- 코드 구조 이해 잘함
- 자동화 수준 높음
- IDE integration 강함

#### 단점
- 큰 repo에서 가끔 irrelevant file 포함
- context가 커짐

### Aider 방식
grep 기반
```
User Prompt
   ↓
grep / ripgrep
   ↓
사용자가 파일 선택
   ↓
LLM
```
#### 장점
- 컨텍스트 정확
- 토큰 절약
- predictable

#### 단점
- 자동화 약함
- 사용자 개입 필요

### Devin 방식
LLM이 직접 repo를 탐색
```
Task
 ↓
Agent reasoning
 ↓
search repo
 ↓
open file
 ↓
test run
 ↓
edit code
 ↓
loop
```

#### 장점
- 대형 repo 대응
- dependency 이해 가능
- 복잡한 작업 가능

#### 단점
- 느림
- tool 호출 많음
- 비용 증가

### Hybrid 방식
OpenCode, Cursor에서 사용(요즘 트렌드)
```
grep search
   ↓
symbol index
   ↓
dependency graph
   ↓
agent exploration
```
# grill-me
Matt Pocock의 Grill Me는 Claude Code, Cursor 등의 AI 코딩 에이전트가 개발 전에 프로젝트 계획, 아키텍처, 엣지 케이스를 철저히 검증하도록 인터뷰하는 인기 있는 AI 스킬

## 특징

### 무자비한 질문 폭격
"이 계획에 대해 이해될 때까지 끈질기게 인터뷰해라"라는 단순하지만 강력한 프롬프트로 시작
### 의사 결정 트리 구축
기술 스택, 데이터 모델링, 예외 처리(Edge case), 사용자 경험(UX) 등의 갈래를 하나씩 파고듬
### 비용 절감
잘못된 방향으로 코딩을 시작해 시간과 리소스를 낭비하기 전에, 인터뷰를 통해 발생 가능한 문제를 미리 차단

## 내용
아래 프롬프트 복사해서 사용
```md
---
name: grill-me
description: Interview the user relentlessly about a plan or design until reaching shared understanding, resolving each branch of the decision tree. Use when user wants to stress-test a plan, get grilled on their design, or mentions "grill me".
---

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time.

If a question can be answered by exploring the codebase, explore the codebase instead.
```

## 실행
```shell
/grill-me <내가 만들려는 기능/제품/설계>
```
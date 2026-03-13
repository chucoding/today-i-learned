# OpenCode
오픈 소스 AI coding agent

> [OpenCode 공식문서](https://opencode.ai/docs/ko)

## 설치
```shell
npm install -g opencode-ai
```

## 구성

### 모델 설정 방법 2가지
- API 키로 원하는 LLM Provider 사용가능
- OpenCode Zen 사용
  - 단, Anthropic 정책 때문에 third-party tool에서 Claude subscription 사용이 종종 막히는 경우 있음 주의.

## 초기화

### 실행
```shell
opencode
```

### 초기화
OpenCode가 프로젝트를 분석하고 프로젝트 루트에 AGENTS.md 파일을 생성
```shell
/init
```

### 질문하기
프로젝트 파일은 @ 키로 퍼지 검색 가능
```shell
How is authentication handled in @packages/functions/src/api/index.ts
```
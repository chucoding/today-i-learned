# AGENTS.md
codex에서 읽는 스킬 트리거 파일

## 한국어 번역
코드리뷰 사용할 때 한국어 번역시 유용함.

### AGENTS.md
```md
## Review guidelines

- 모든 코드 리뷰 코멘트는 반드시 한국어(Korean)로 작성하세요.
- Always write all code review comments in Korean.
- P0/P1 이슈 설명도 한국어로 작성하세요.
```

## 보안 검증
코드리뷰시 보안 검증할 수 있는 스킬 적용

> [openai/skills/security-best-practices](https://developers.openai.com/codex/use-cases/github-code-reviews)

| OpenAI agentic 코딩 도구 Codex를 실무에 바로 적용할 수 있는 12가지 유즈케이스를 공식 문서로 정리해 공개

### AGENTS.md
```md
## Review guidelines
- Flag security regressions, missing tests, and risky behavior changes.
```

### 스킬 구조
```
.agents/skills/security-best-practices
    |- agents
        |- openai.yaml
    |- references
        |- javascript-general-web-frontend-security.md
        |- javascript-typescript-react-web-frontend-security.md
    |- LICENSE.txt
    |- SKILL.md
```

## FSD 아키텍처
AI 코딩 에이전트에게 Feature-Sliced Design (FSD) v2.1 아키텍처 방법론을 가르치는 에이전트 스킬.

> [feature-sliced/skills/feature-sliced-design](https://skills.sh/feature-sliced/skills/feature-sliced-design)

### AGENTS.md
```md
## Review guidelines
- 새 프론트엔드 파일이나 디렉터리를 생성하거나 FSD 레이어 구조를 변경할 때는 반드시 `feature-sliced-design` 스킬을 먼저 사용하세요.
```

### 스킬 구조
```
.agents/skills/feature-sliced-design
    |- references
        |- cross-import-patterns.md
        |- framework-integration.md
        |- layer-structure.md
        |- migration-guide.md
        |- practical-examples.md
    |- SKILL.md
```
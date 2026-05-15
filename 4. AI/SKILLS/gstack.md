# gstack
Y Combinator의 CEO인 Garry Tan의 오픈소스 시스템으로, Claude Code를 20명의 전문가로 구성된 가상 엔지니어링 팀

## 특징
- Claude를 코파일럿에서 가상 엔지니어링 팀으로 전환하는 28가지 Claude Code 스킬의 오픈소스 모음
- 각 스킬이 곧 전문가(CEO, 엔지니어, 디자이너 등등)

## 동작
```
생각 → 계획 → 구축 → 검토 → 테스트 → 배포 → 회고
```

1. "제 캘린더를 위한 일일 브리핑 앱을 만들고 싶습니다." 요청
2. `/office-hours` 실행(제품 전략)
3. `/plan-ceo-review` 실행(범위 검토)
4. `/plan-eng-review` 실행(아키텍처를 확정)
5. `/plan-design-review` 실행(모든 디자인 요소를 0-10점으로 평가)
6. `/review` 실행(버그 찾기)
7. `/qa` 실행 (실제 브라우저에서 테스트)
8. `/ship` 실행 (PR 푸시)

## 설치
gstack github README 참고
> [gstack github](https://github.com/garrytan/gstack)
- claude desktop기반 프롬프트 설치
- PRO 요금제 기준 2% 정도 사용

설치 완료
- bun 설치 완료 (필수 의존성)
- `~/.claude/skills/gstack` 경로에 gstack 클론 및 설정 완료 — Playwright/Chromium 헤드리스 브라우저 포함
- `~/.claude/CLAUDE.md` 파일 생성 및 gstack 섹션 추가 완료:
모든 웹 브라우징에는 /browse 사용, mcp__claude-in-chrome__* 도구는 절대 사용하지 않음, 그리고 사용 가능한 전체 스킬 목록 포함



## 각 스킬별 상세 설명
### 1. `/office-hours` - 오피스 아워(파트너)

#### 역할
코드를 작성하기 전에 6가지 필수 질문으로 제품을 재구성하여 모든 프로젝트를 시작(사용자의 프레임에 반박하고, 전제에 이의를 제기하며, 구현 대안을 생성함.)

#### 예시 출력
```
당신은 "일일 브리핑 앱"이라고 말했습니다. 하지만 실제로 설명한 것은
개인 비서 AI였습니다. 당신이 설명하고 있다는 것을 깨닫지 못한 5가지 기능은 다음과 같습니다...

[4가지 전제에 이의를 제기합니다 — 당신은 동의하거나, 반대하거나, 조정할 수 있습니다]
[노력 추정치와 함께 3가지 구현 접근 방식을 생성합니다]

권장 사항: 내일 가장 좁은 부분을 배포하고 실제 사용으로부터 배우세요.
전체 비전은 3개월 프로젝트입니다 — 실제로 작동하는 일일 브리핑으로 시작하세요.
```

## 제거
gstack github README 참고
> [gstack github](https://github.com/garrytan/gstack)

단, 깔끔하게 안지워짐. 스킬 및 CLAUDE.md 직접 제거 필요.
- ~/.agents/skills/: gstack 스킬 45개 제거, feature-sliced-design, find-skills, grill-me, security-best-practices 유지
- ~/.claude/skills/: 동일하게 정리
- ~/.claude/CLAUDE.md: # gstack 섹션 전체 제거 (파일 비어 있음)
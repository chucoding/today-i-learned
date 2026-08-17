# gstack

Y Combinator의 CEO인 Garry Tan의 오픈소스 시스템으로, Claude Code를 20명의 전문가로 구성된 가상 엔지니어링 팀으로 전환

> [gstack github](https://github.com/garrytan/gstack)

- 28가지 Claude Code 스킬 모음, 각 스킬이 곧 전문가(CEO, 엔지니어, 디자이너 등)
- 흐름: 생각 → 계획 → 구축 → 검토 → 테스트 → 배포 → 회고
- 예: `/office-hours`(제품 전략) → `/plan-eng-review`(아키텍처) → `/review`(버그) → `/qa`(브라우저 테스트) → `/ship`(PR)

## 스킬

- [`/office-hours`](office-hours.md)
- [`/qa-only`](qa-only.md)
- [`/investigate`](investigate.md)
- [`/plan-eng-review`](plan-eng-review.md)

## SPA 현업 사용 가이드

React SPA(TanStack Query · Zustand · AG Grid) 기준. 기존 체계가 잡혀 있다면 아래 3개가 최소 세트

| 스킬 | 사용 시점 |
|------|-----------|
| [`/qa-only`](qa-only.md) | 일상적인 UI 변경 — 가장 자주 사용 |
| [`/investigate`](investigate.md) | 원인이 불명확한 버그 |
| [`/plan-eng-review`](plan-eng-review.md) | 큰 기능 · 공통 구조 변경 |

선택 — `/review`(PR 리뷰 체계가 약할 때), `/benchmark`(렌더링 · 번들), `/design-review`(대규모 화면 개편), `/qa`(사용자 흐름 수정까지)

주의 — `/qa`와 `/review`는 코드를 수정하므로 깨끗한 브랜치에서만 사용. Next.js 여부는 이 세트에 영향 없음

## 설치

**요구사항** Claude Code, Git, [Bun](https://bun.sh/) v1.0+ (Windows는 Node.js 추가)

```bash
git clone --single-branch --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack \
  && cd ~/.claude/skills/gstack && ./setup
```

Playwright와 Chromium은 `setup`이 자동으로 내려받으므로 미리 준비할 필요 없음

스킬을 골라 설치하는 옵션은 없음. 전부 설치한 뒤 `~/.claude/CLAUDE.md`(또는 거기서 import하는 `AGENTS.md`)의 gstack 섹션에서 스킬 목록을 3개로 축소. 설치 시 35개 목록이 자동으로 들어가므로 정리 필요

- 스킬 이름이 겹치면 `./setup --prefix` → `/gstack-qa-only` 형태
- 폐쇄망이면 `gstack-config set update_check false`
- `telemetry` 기본값은 `off`
- PRO 요금제 기준 설치에 사용량 2% 정도 소모

## 프로젝트 적용

스킬은 슬래시 명령으로 부르지 않아도 Claude가 요청 내용을 보고 알아서 호출함. 각 `SKILL.md`의 `description`이 1차 판단 기준이고, 프로젝트 지침의 라우팅 섹션이 그 판단을 보정

프로젝트 레포의 `AGENTS.md`(없으면 `CLAUDE.md`)에 추가

```
## Skill routing

요청이 아래에 해당하면 Skill 도구로 호출한다. 애매하면 호출한다.

- UI 변경 후 동작 확인 — 폼, Grid, 모달, 라우팅, 권한 UI, 캐시 반영, 반응형 → /qa-only
- 원인이 불명확한 버그 — 캐시 잔존, 불필요한 렌더링, 요청 race, lifecycle 불일치 → /investigate
- 여러 화면 영향, API 계약 변경, 권한·캐시·동시성 중 둘 이상 해당하는 설계 → /plan-eng-review

위 세 가지 외의 gstack 스킬은 사용자가 명시적으로 요청할 때만 실행한다.
```

헤딩을 `## Skill routing`으로 두는 이유 — gstack은 프로젝트에서 첫 실행 시 이 문자열이 있는지 검사하고, 없으면 13개 전체 매핑을 넣자고 제안함. 미리 넣어두면 그 제안이 뜨지 않음

개인 전역 지침이 아니라 프로젝트 지침에 두어야 팀원과 기준이 공유됨

### 도입 전 확인

- `/qa-only`는 Playwright/Chromium을 내려받음 — 사내 네트워크에서 막히면 설치 실패
- 로그인 필요한 화면 QA는 `/setup-browser-cookies`로 쿠키 주입 선행
- `/investigate`도 `Edit`·`Write` 권한을 가지므로 깨끗한 브랜치에서 실행

## 제거

```bash
~/.claude/skills/gstack/bin/gstack-uninstall   # --keep-state 설정 보존, --force 확인 생략
```

스킬, 심볼릭 링크, 전역 상태(`~/.gstack/`), browse 데몬까지 처리하지만 아래는 직접 확인 필요

- `~/.claude/CLAUDE.md`, 프로젝트 `AGENTS.md` — `## gstack`, `## Skill routing` 섹션
- `~/.claude/settings.json` — 수동 제거 시 `timeline-stop-hook` 항목이 남음
- `~/Library/Caches/ms-playwright/` — 다른 도구와 공유하므로 남겨둠

한 번에 처리하려면 Claude Code에 아래 프롬프트 사용

> gstack을 제거해줘. `~/.claude/skills/gstack/bin/gstack-uninstall` 실행 후, `~/.claude/CLAUDE.md`와 프로젝트 AGENTS.md에서 gstack 관련 섹션을 지우고, `~/.claude/settings.json`에 timeline-stop-hook 항목이 남아 있으면 함께 제거해줘.

### 실제 제거 기록

- `~/.agents/skills/`, `~/.claude/skills/`: gstack 스킬 45개 제거, feature-sliced-design 등 나머지 유지
- `~/.claude/CLAUDE.md`: `# gstack` 섹션 전체 제거

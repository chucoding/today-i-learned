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
- 조사 결과를 바탕으로 어떤 방식으로 구현할지, 어느 영역이 영향을 받는지, 어떤 순서로 진행하는 게 안전한지를 정리해 계획 초안을 구성
4. 후속 질문과 계획 보정
- 초안이 나온 뒤에 바로 실행으로 넘어가는게 아니라 사용자가 후속 질문을 통해 계획을 더 다듬을 수 있음 

## 모델
### opusplan
Plan mode => Opus / Execution mode => Sonnet 자동 전환

```claude-cli
/model opusplan
```
- 계정티어에 따라 default 모델 다름
- Max/Team Premium인 경우 기본 Opus 4.6
- Pro/Team Standard는 Sonnet 4.6
- Opus 사용 임계치에 걸리면 Sonnet으로 자동 폴백
- effort는 각각 설정한 값에 따라 
- VS Code 플러그인에서 미지원
> https://code.claude.com/docs/en/model-config

## Advisor
메인 모델이 결정 지점에서 더 강한 모델에게 조언을 구하는 실험 기능. 어드바이저는 대화 전체(모든 도구 호출과 결과 포함)를 받고, 호출 시점은 Claude가 판단한다. Anthropic 인프라에서 서버 도구로 실행된다.

> [Advisor 공식문서](https://code.claude.com/docs/en/advisor)
> 한국어 문서([/docs/ko/advisor](https://code.claude.com/docs/ko/advisor))는 번역이 늦어 Fable 5.1, usage credits 동의, 기능 플래그 요구사항이 빠져 있다.

### 설정 방법
세 가지 중 무엇을 써도 되지만 지속 범위가 다르다.

| 방법 | 지속 범위 |
| --- | --- |
| `/advisor fable` | 사용자 설정에 저장되어 이후 모든 세션에 적용 |
| `settings.json`의 `advisorModel` | 그 설정 파일이 적용되는 범위에 계속 적용 |
| `claude --advisor fable` | 해당 세션만. 설정값보다 우선 |

`/advisor`를 인자 없이 실행하면 선택 가능한 모델 목록이 뜬다. 터미널에서 골라 `Advisor set to ...`가 뜨면 `~/.claude/settings.json`에 아래처럼 저장된다.

```json
{
  "advisorModel": "fable"
}
```

- 전역 사용자 설정이므로 프로젝트와 세션에 관계없이 적용되고, 매 세션 다시 입력할 필요가 없음
- 프로젝트별로 다르게 쓰려면 그 프로젝트의 `.claude/settings.json`에 `advisorModel`을 두면 일반 설정 우선순위대로 전역 설정을 덮음
- `--advisor`는 `claude --help`에 나오지 않음
- 원격이나 웹 클라이언트에서 `/advisor`로 바꾸면 그 세션에만 적용되고 저장된 기본값은 그대로. 기본값을 바꾸려면 터미널에서 실행

### 모델 짝 제약
어드바이저는 메인 모델과 같거나 더 유능해야 한다. 조건을 못 맞추면 설정은 저장되지만 요청에 붙지 않고, `/advisor` 출력과 알림으로 그 사실을 알려준다.

| 메인 모델 | 허용되는 어드바이저 |
| --- | --- |
| Haiku 4.5 | Fable, Opus, Sonnet |
| Sonnet 4.6 | Fable, Opus, Sonnet |
| Sonnet 5 | Fable, Opus, Sonnet 5 |
| Opus 4.6 | Fable, Opus, Sonnet 5 |
| Opus 4.7 이상 | Fable, Opus 4.7 이상 |
| Fable 5.1, Fable 5 | Fable 5.1 또는 같은 Fable 버전 |

- Haiku 4.5는 어드바이저를 호출할 수 있지만 어드바이저가 될 수 없음
- `fable`, `opus`, `sonnet` 별칭은 각 계열의 기본 버전으로 해석되고, `claude-opus-5` 같은 전체 ID도 받음
- 서브에이전트는 같은 어드바이저 설정을 물려받아 자기 모델로 짝 검사를 다시 함
- 일부 플랜에서 Fable 어드바이저는 usage credits로 과금되어 `/model fable`에서 1회 동의가 필요하다. 동의 전에는 `/advisor fable`이 저장되지 않고 `/model fable`로 안내함

### 동작 조건
- Anthropic API 전용. Amazon Bedrock, Google Cloud, Microsoft Foundry 등 다른 호스팅에서는 사용 불가
- 메인 모델이 Fable, Opus 4.6 이상, Sonnet 4.6 이상, Haiku 4.5 중 하나
- 기능 플래그를 받아와야 켜지므로 `DISABLE_TELEMETRY`처럼 플래그 조회를 끄는 변수가 설정된 세션에서는 꺼진 상태

### 비용과 캐시
- 호출마다 어드바이저 모델 요율로 대화 전체를 다시 읽음. 어드바이저 쪽 읽기는 캐시되지 않아 호출 간 재사용이 없음
- 매 턴이 아니라 결정 지점에서만 호출하므로, 보통 메인 모델을 강한 모델로 바꾸는 것보다 저렴
- 세션 중 `/advisor`로 켜고 끄는 것은 메인 모델의 프롬프트 캐시를 무효화하지 않음

### 끄기
- `/advisor off`는 저장된 `advisorModel`까지 지움
- `CLAUDE_CODE_DISABLE_ADVISOR_TOOL=1`이면 `/advisor` 명령 자체가 사라지고 설정값도 무시됨. `--advisor`는 받아들이지만 효과 없음

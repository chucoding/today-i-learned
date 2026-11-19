# Orca

워크트리마다 에이전트 세션을 띄워 병렬로 작업하는 개발 환경

> [Orca 공식문서](https://www.onorca.dev/docs)

## 목차

- [Orchestration](#orchestration)
  - [구성 요소](#구성-요소)
  - [조율자 흐름](#조율자-흐름)
- [참고자료](#참고자료)

## Orchestration

다른 에이전트 세션에 일을 맡기고 결과를 돌려받는 감독 계층. 맡긴 뒤 돌려받을 것이 없으면 핸드오프로 충분함.

> [!NOTE]
> 근거 표기: `실행 확인`은 직접 실행해 본 동작, `가이드`는 `orca skills get orchestration`과 `--help`에서 읽기만 한 동작.

### 구성 요소

| 요소 | 뜻 |
| --- | --- |
| Run | 작업 묶음의 이름 공간이자 조율자의 수신함 |
| Task | 지시문(spec)과 상태를 가진 작업 단위 |
| Dispatch | Task를 한 터미널에 맡긴 한 번의 시도 |
| 조율자(coordinator) | 워커를 띄우고 메시지를 받아 판단하는 세션 |
| 워커(worker) | Task를 받아 수행하고 조율자에게 보고하는 세션 |
| 메일 | 세션 사이에 오가는 메시지 |

- 메일 명령
  - `send`: 조율자가 워커에게 전달. 작업 중인 워커는 현재 턴이 끝날 때 받음. `실행 확인`
  - `status`: 워커가 진행 상황을 알리는 메시지 유형. `실행 확인`
  - `check`: 깨워진 세션이 도착한 메일을 읽는 명령. `실행 확인`
  - `ask`와 `reply`: 워커가 조율자에게 묻고 턴을 끝내면, 조율자의 `reply`와 함께 Orca가 워커를 깨움. `가이드`
  - `worker_done`: 워커의 종료 보고. outcome은 `succeeded` 또는 `failed`. `가이드`

### 조율자 흐름

1. `orca orchestration worker-start`로 워커 시작. 첫 호출이 Run을 만들고 조율자에 묶음. `실행 확인`
2. 조율자는 턴 종료. 폴링이나 감시 스크립트 금지. `가이드`
   - 스크립트의 `check`가 메일을 가져가면 Orca가 조율자를 깨울 것이 없어짐.
3. 워커가 메시지를 보내면 Orca가 조율자를 깨움. `실행 확인`
4. 조율자가 메시지를 판별해 답하거나 다른 워커에게 `send`로 전달. `실행 확인`
5. 모든 워커가 `worker_done`을 보내면 Task별 결과를 사용자에게 보고. `가이드`

- 끝난 워커는 스스로 닫힘. `--keep-open`이나 새 워크트리에서 시작한 워커는 열린 채 남음. `가이드`

## 참고자료

| TITLE | URL |
| --- | --- |
| Run, Task, Dispatch, 메일의 정의 | [https://www.onorca.dev/docs/cli/orchestration](https://www.onorca.dev/docs/cli/orchestration) |

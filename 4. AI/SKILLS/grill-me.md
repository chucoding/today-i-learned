# grill-me
Matt Pocock의 grilling 스킬 비교

## 목차

- [grill-me](#grill-me-1)
- [grill-with-docs](#grill-with-docs)
- [wayfinder](#wayfinder)
- [선택 기준](#선택-기준)
- [참고자료](#참고자료)

## grill-me

개발 전에 계획과 설계를 인터뷰로 검증해, 아이디어를 확정할 수 있을 때까지 다듬는 스킬

- 질문을 라운드 단위로 하며, 앞선 답이 필요한 질문은 그 답을 들은 뒤에 물음.
- 질문마다 추천 답변을 함께 제시
- 상태 비저장(stateless) 방식. 파일을 남기지 않고 결과는 머릿속에만 남음.
- 사용 시점
  - 저장소 없이 아이디어만 있는 경우 (코드가 아니어도 됨)
  - 아이디어가 모호한 초기 단계. 이미 정확히 명세할 수 있으면 필요 없음.

## grill-with-docs

grill-me와 같은 인터뷰를 코드베이스에 맞춰 진행하고, 결정 내용을 저장소 파일로 남기는 스킬

- 상태 저장(stateful) 방식
- 용어가 정리되면 바로 `GLOSSARY.md`에 기록
- 중요한 결정은 ADR(아키텍처 결정 기록)로 작성
- 질문에 코드로 답할 수 있으면 코드를 직접 읽어 확인
- 사용 시점
  - 저장소에서 변경을 시작할 때, 계획과 용어가 아직 모호한 경우
  - 한 세션 안에 정리할 수 있는 변경

## wayfinder

한 세션에 담기 어려운 큰 작업을 이슈 트래커 위의 결정 티켓 지도(map)로 쪼개 하나씩 해결하는 스킬

- 계획만 하고 구현은 하지 않음. 티켓마다 결정할 질문 하나를 담음.
- 결정할 것이 남지 않으면 명세 작성과 구현 단계로 넘김.
- 이슈 트래커가 설정되지 않았으면 로컬 마크다운 파일로 지도를 관리
- 사용 시점
  - 여러 세션에 걸친 큰 작업이면서 목적지는 알지만 경로가 불분명한 경우
  - 그린필드 프로젝트뿐 아니라 레거시 코드베이스에서도 사용

## 선택 기준

| 상황 | 스킬 |
| --- | --- |
| 저장소 없이 한 세션에 정리할 아이디어 (코드가 아니어도 됨) | grill-me |
| 저장소에서 한 세션에 정리할 변경 | grill-with-docs |
| 여러 세션에 걸치고 경로가 불분명한 큰 작업 | wayfinder |

- 세 스킬 모두 사용자가 직접 호출해야 하며, 에이전트가 스스로 실행하지 않음.

## 참고자료

| TITLE | URL |
| --- | --- |
| grill-me 개념과 다른 grilling 스킬과의 구분 | [https://github.com/mattpocock/skills/blob/main/docs/productivity/grill-me.md](https://github.com/mattpocock/skills/blob/main/docs/productivity/grill-me.md) |
| grill-with-docs의 GLOSSARY와 ADR 기록 방식 | [https://github.com/mattpocock/skills/blob/main/docs/engineering/grill-with-docs.md](https://github.com/mattpocock/skills/blob/main/docs/engineering/grill-with-docs.md) |
| wayfinder의 결정 티켓 지도와 사용 시점 | [https://github.com/mattpocock/skills/blob/main/docs/engineering/wayfinder.md](https://github.com/mattpocock/skills/blob/main/docs/engineering/wayfinder.md) |

# pages 레이어의 상태관리

FSD v2.0과 v2.1의 pages 역할 차이

## 목차

- [핵심 질문](#핵심-질문)
- [버전별 차이](#버전별-차이)
- [판단 기준](#판단-기준)
- [참고자료](#참고자료)

## 핵심 질문

pages에서 상태관리를 해도 되는가?

- v2.1 공식 문서는 금지하지 않음. "pages는 조합만 한다"는 v2.0 방식에서 온 통념
- 데이터 조회와 변경 요청은 페이지의 `api` 세그먼트에 둠.
- 페이지 전용 `model`은 드물고, 작은 상태는 컴포넌트 안에 두면 충분함.

> "It's not common for a page to have a dedicated data model, and tiny bits of state can be kept in the components themselves."
>
> 출처: [Layers | Feature-Sliced Design](https://feature-sliced.design/docs/reference/layers)

## 버전별 차이

| 구분 | v2.0 | v2.1 (pages-first) |
|---|---|---|
| pages 역할 | entities, features, widgets를 조합 | 그 페이지만 쓰는 UI, 폼, 데이터 로직까지 보유 |
| 상태와 로직 | features나 entities로 내림 | 한 페이지만 쓰면 페이지에 둠 (`ui`, `api` 세그먼트로 나누고 `model`은 필요할 때만) |
| features | 화면 전용 로직도 슬라이스로 만들곤 함 | 여러 페이지가 쓰는 사용자 동작만 |
| 결과 | features가 비대해짐 | features가 작고 페이지가 커짐 |

- pages 역할 행은 릴리스 노트가 직접 설명함. 나머지 행의 v2.0 칸은 그 방식을 따를 때 생기는 모습을 정리한 해석
- 하위 호환 변경이라 기존 v2.0 프로젝트에 이전을 강제하지 않음.

> "In FSD 2.0 we explained how to identify entities and features in your application, and then combine them in widgets and pages."
>
> 출처: [v2.1: Pages come first!](https://github.com/feature-sliced/documentation/releases/tag/v2.1)

> "For example, large blocks of UI, forms and data logic that are not reused on other pages should now stay in the slice of the page that they are used in."
>
> 출처: [v2.1: Pages come first!](https://github.com/feature-sliced/documentation/releases/tag/v2.1)

## 판단 기준

공식 문서 기준이 아니라 업무 화면을 레이어로 나누며 직접 정리한 기준

- feature 기준은 2개 이상 페이지가 **같은 모델로** 하는 사용자 동작
  - "인쇄"처럼 이름이 같아도 화면마다 리포트와 파라미터가 다르면 각 페이지에 둠.
- UI를 감싸기만 하는 feature는 만들지 않음.
  - UI는 `shared/ui`, 서비스 전체가 쓰는 요청은 `entities`의 `api`로 나눔.
- 업무 용어를 속성(제목, 버튼 문구)으로 빼면 `shared/ui`로 내릴 수 있음.
  - feature끼리 같은 UI를 쓰는 같은 레이어 import 위반을 피하는 방법
- 업무 개념의 표시와 선택 UI(부서 선택, 직원 목록)는 여러 페이지가 쓰면 `entities`에 둠.

## 참고자료

| TITLE | URL |
|---|---|
| pages 슬라이스의 코드 범위와 상태 | https://feature-sliced.design/docs/reference/layers |
| v2.0과 다른 v2.1 pages-first 방식 | https://github.com/feature-sliced/documentation/releases/tag/v2.1 |

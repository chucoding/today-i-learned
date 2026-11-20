# 상수 배치

FSD v2.1 상수 관리 기준

## 목차

- [핵심 원칙](#핵심-원칙)
- [종류별 위치](#종류별-위치)
- [도메인 상수](#도메인-상수)
- [참고자료](#참고자료)

## 핵심 원칙

- 상수 전용 규칙 없음. 세그먼트 이름 규칙과 Shared 규칙을 그대로 적용
- `constants` 세그먼트, `constants.ts` 파일 만들지 않음.
  - `components`, `hooks`, `types`처럼 본질을 말하는 이름이라 목적이 드러나지 않음.
  - 도메인 파일(`model/order.ts`)에 타입, 로직과 함께 둠. ([model 세그먼트](model.md))

> "make sure that the name of these segments describes the purpose of the content, not its essence."
>
> 출처: [Layers | Feature-Sliced Design](https://feature-sliced.design/docs/reference/layers)

## 종류별 위치

| 상수 성격 | 위치 |
|---|---|
| 환경변수, 전역 feature flag, 전역 설정 | `shared/config/` |
| 라우트 경로 상수, 매칭 패턴 | `shared/routes/` |
| 라우터 설정 자체 | `app/routes/` |
| 전역 번역 문자열 | `shared/i18n/` |
| 슬라이스 전용 feature flag, 설정 | `<slice>/config/` |
| 상태 라벨, enum 성격 값, 매직 넘버 | 소유 슬라이스의 `model/` |

> "`📁 routes` — route constants or patterns for matching routes."
>
> 출처: [Layers | Feature-Sliced Design](https://feature-sliced.design/docs/reference/layers)

## 도메인 상수

1. 한 페이지만 쓰면 `pages/{page}/model/`
2. 2곳 이상이 쓰고 팀이 합의하면 `entities/{entity}/model/`로 올리고 `index.ts`로 공개
3. 비즈니스 의미 없는 값(디바운스 ms, 기본 페이지 크기)만 `shared/config/`

- 도메인 상수를 `model/`에 두는 건 공식 문장이 아니라 "Shared에 비즈니스 로직 금지" 규칙을 적용한 해석

## 참고자료

| TITLE | URL |
|---|---|
| Shared 세그먼트별 역할(config, routes, i18n) | [https://feature-sliced.design/docs/reference/layers](https://feature-sliced.design/docs/reference/layers) |
| 표준 세그먼트와 나쁜 세그먼트 이름 | [https://feature-sliced.design/docs/reference/slices-segments](https://feature-sliced.design/docs/reference/slices-segments) |
| 기술 역할 파일명 대신 도메인 기준 묶기 | [https://feature-sliced.design/docs/guides/issues/desegmented](https://feature-sliced.design/docs/guides/issues/desegmented) |

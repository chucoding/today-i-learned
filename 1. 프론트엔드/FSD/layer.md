# 레이어 (Layers)
재사용 범위·의존성에 따른 수직 계층
> app → pages → widgets → features → entities → shared

## 목차

- [App](#app)
- [Pages](#pages)
  - [Next.js와 이름 충돌](#nextjs와-이름-충돌)
- [Widgets](#widgets)
- [Features](#features)
- [Entities](#entities)
- [Shared](#shared)
- [참고자료](#참고자료)

## App
- 라우터, 진입점, 전역 상태/스타일, 프로바이더
- slice 없이 segment만 관리

## Pages
- 라우트 단위 화면(페이지 또는 중첩 라우팅의 주요 부분)
- 보통 페이지 1개 = 슬라이스 1개
- 유사 페이지는 하나의 슬라이스로 묶기 가능

### Next.js와 이름 충돌

- Next.js는 `app`, `pages`를 폴더마다 따로 판정함. 루트에 같은 이름 폴더가 없으면 `src` 아래 폴더를 라우터로 읽음.
  - Next.js src 폴더 문서: 루트에 `app`이나 `pages`가 있으면 `src` 아래 같은 이름 폴더는 무시
- 그래서 `src`의 FSD `app`, `pages` 레이어가 라우터로 읽힐 수 있음.
- FSD 공식 가이드의 해법은 레이어 이름 변경
  - `app`, `pages` 레이어를 `_app`, `_pages`로 바꿈. App Router만 써도 `src/pages`는 Pages Router로 읽히므로 둘 다 바꿈.
  - Next.js 라우터 폴더는 프로젝트 루트에 두는 편이 쉬움. 필수는 아님.
  - FSD with-nextjs: 접두사 이름 변경과 루트 배치 권장
- 라우터 폴더가 `src` 안에 있을 때 `pages` 레이어만 `views`로 바꾸면 `app` 레이어 충돌은 남음.

## Widgets
- 여러 페이지에서 재사용되는 독립적 대규모 UI
- 선택적 사용
- 재사용 큰 UI가 적으면 생략 가능

## Features
- 사용자에게 비즈니스 가치를 주는 동작 (동사적 개념)
- “업체 검색”, “업체 저장”처럼 사용자가 수행하는 재사용 가능한 행동을 정의
- Form, 필터 등 상호작용 UI

## Entities
- 비즈니스 데이터/개체 (명사적 개념)
- 검증 규칙, 계산, 상태, 계층 관계 같은 의미 있는 도메인 로직이 있어야 추출 가치가 있음
- Company, User 등

## Shared
- 앱 기본 구성요소, 재사용 기반 코드
- slice 없이 segment만 관리
- 아무 비즈니스 의미도 없는 범용코드로만 제한하지 않음.
  - 애플리케이션을 인식하는 API endpoint·DTO·enum 같은 외부 계약도 허용
  - 단순 데이터 정의는 shared/api, 실제 재사용 비즈니스 로직은 entities에 두도록 권장

## 참고자료

| TITLE | URL |
|---|---|
| Next.js와 FSD 레이어 이름 충돌 해법 | [https://feature-sliced.design/docs/guides/tech/with-nextjs](https://feature-sliced.design/docs/guides/tech/with-nextjs) |
| Next.js의 src 폴더 해석 | [https://nextjs.org/docs/app/api-reference/file-conventions/src-folder](https://nextjs.org/docs/app/api-reference/file-conventions/src-folder) |

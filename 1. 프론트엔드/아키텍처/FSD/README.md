# FSD 아키텍처
기능 분할 설계(Feature-Sliced Design, FSD) 아키텍처. 프론트엔드 애플리케이션 구조를 위한 아키텍처 방법론으로, 컴포넌트 중심 UI 환경에서 복잡한 애플리케이션 구조를 체계적으로 조직한다.

## 핵심: 3가지 차원
코드를 **수직(레이어)·수평(슬라이스)·기술 목적(세그먼트)** 로 나누어 재사용을 통제하고, 높은 응집도·낮은 결합도를 유지한다.

| 구분 | 역할 | 규칙 |
|------|------|------|
| **레이어 (Layers)** | 재사용 범위·의존성에 따른 수직 계층 | **단방향 의존성**: app → pages → widgets → features → entities → shared (상위만 하위 참조) |
| **슬라이스 (Slices)** | 레이어 내 비즈니스 도메인별 분할 | **같은 레이어 내 다른 슬라이스 참조 불가** → 응집도↑, 결합도↓ |
| **세그먼트 (Segments)** | 슬라이스 내 기술적 목적별 구분 | 이름은 **목적**을 설명 (예: `components` 대신 `ui`). 성격(components, hooks) 지양 |

## 특징
- segments 종류 : ui, model, api, lib, config
- AI 시대에 적합 (Vertical Slice / Feature 중심 구조)
- **프로젝트마다 필요한 레이어만 선택** 가능 (예: widgets 생략 가능)

### 장점
- 레이어별로 격리되어 있어서 변화에 용이(확장성, 수정, 삭제 등)
- 추상화의 벽이 생겨서 상위 계층에서는 하위 계층의 역할을 몰라도 된다.
- 객체지향방법론에서 자주 언급되는 **다형성, 캡슐화, 추상화, 응집도, 결합도**와 같은 개념이 많이 녹아져있어서 코드의 안전성이 높아진다.

### 단점
- 설계단에서 시간을 많이 가져가야 한다.
  - 초기 설계를 잘못하게 될 경우 고통 받을 수 있음
  - 팀과의 소통이 중요하다.
- 아토믹 디자인 패턴과 비슷한 단점인 **이건 어디에 들어가야 맞을까?** 와 같은 생각이 들 때가 있다.
- 폴더가 매우 많이 생기게 된다. (뎁스가 깊어질 수 있음)

## 레이어 (Layers) 요약
| 레이어 | 설명 | 비고 |
|--------|------|------|
| **App** | 라우터, 진입점, 전역 상태/스타일, 프로바이더 | slice 없이 segment만|
| **Pages** | 라우트 단위 화면(페이지 또는 중첩 라우팅의 주요 부분) | 보통 페이지 1개 = 슬라이스 1개. 유사 페이지는 하나의 슬라이스로 묶기 가능 |
| **Widgets** | 여러 페이지에서 재사용되는 독립적 대규모 UI | 선택적 사용. 재사용 큰 UI가 적으면 생략 가능 |
| **Features** | 사용자에게 비즈니스 가치를 주는 동작 (동사적 개념) | Form, 필터 등 상호작용 UI |
| **Entities** | 비즈니스 데이터/개체 (명사적 개념) | Company, User 등 |
| **Shared** | 앱 기본 구성요소, 재사용 기반 코드 | slice 없이 segment만 |

## Segments
App, Shared는 slice 없이 segment로만 구성. 세그먼트는 **목적**으로 이름 짓는다 (예: ui, api, model, lib, config).

- **ui**: UI 컴포넌트, 포맷터, 스타일 등
- **api**: 백엔드 통신, request, DTO, mapper
- **model**: 도메인 모델, 스키마, 스토어, 비즈니스 로직
- **lib**: 해당 슬라이스 전용 라이브러리 코드
- **config**: 설정, feature-flag (Features는 기능별, Shared는 전역)

## App 레이어: 책임과 React Query
FSD 공식 문서상 App 레이어는 **앱 전역 문제(app-wide matters)** 를 위한 곳이며, 대표 세그먼트는 routes, store, styles, entrypoint, providers 등 **전역 조합** 성격이다. App·Shared는 slice가 없어 세그먼트끼리 자유롭게 import 가능하므로 App은 강한 **orchestration** 레이어다.

### React Query 훅을 App에서 써도 되나?
- **가능하다.** 다만 “기술적으로 가능”과 “구조적으로 적절”은 구분해야 한다.
- **적절한 경우:** 그 데이터 없이는 앱 부팅·라우팅·전역 설정이 불가능한 **앱 전역 관심사**일 때.
- **비추천:** 특정 페이지/위젯/기능/엔티티에만 필요한 서버 상태를 App에서 `useQuery`로 가져오는 것. ownership이 흐려지고 App이 god layer가 되기 쉽다.

### 판단 기준 (한 줄)
> **App은 React Query를 “설치하고 조율하는 곳”, 하위 레이어는 “정의하고 소비하는 곳”** 으로 두는 것이 FSD와 가장 잘 맞는다.

| 구분 | App에 두어도 좋은 것 | 하위 레이어에 둬야 하는 것 |
|------|----------------------|----------------------------|
| **인프라** | QueryClientProvider, Devtools, hydration, 전역 default options | — |
| **orchestration** | 앱 bootstrap, 라우터 preload, 라우트 가드가 공통으로 쓰는 사용자/세션 정보 조회 | — |
| **쿼리 정의·소비** | (가급적 지양) 전역 부팅/라우팅에 필수인 데이터만 예외적으로 사용 | queryKey·queryFn·queryOptions, entity/feature/page 전용 API, 해당 화면 전용 useQuery 소비 |

**App에서 허용되는 쿼리 예시:** 앱 시작 시 필수인 현재 세션, 라우트 가드 공통 사용자 정보, 테넌트별 전역 브랜딩/원격 설정, bootstrap 데이터.  
**하위에 둘 것:** 특정 화면·기능·엔티티에만 쓰는 API와 useQuery.

### 권장 패턴: App은 조율만, 정의는 하위에
App에서 `useQuery`를 직접 많이 쓰기보다, **쿼리 정의( queryOptions )는 entities/features/pages의 api·model에 두고**, App에서는 **조율만** 하는 편이 낫다.

- 예: `entities/user/api/user.queries.ts`에 `meQuery()`(queryOptions) 정의.
- App의 `routes`·`bootstrap`에서는 `queryClient.prefetchQuery(meQuery())` 또는 `ensureQueryData(meQuery())`만 호출.
- TanStack Query는 queryOptions로 queryKey·queryFn을 한 곳에 두고, `useQuery`와 `prefetchQuery` 등에서 동일 옵션 재사용을 권장한다.

이렇게 하면 ownership은 entity/feature/page에 남고, App은 조립(orchestration)만 담당하게 된다.

### 주의사항
- **SSR(Next.js 등):** TanStack Query SSR 가이드는 **파일 루트에 QueryClient singleton을 만들지 말 것**을 권한다. 요청 간 캐시 공유 위험. SPA/CSR이면 전역 인스턴스 예시가 무난할 수 있으나, SSR에서는 요청 단위로 request-safe 하게 생성하는 TanStack 권장 방식을 따르는 것이 안전하다. (FSD 예시의 `shared/api/query-client.ts` 단일 인스턴스는 SPA 기준.)
- **App 세그먼트 네이밍:** FSD는 App/Shared에서 세그먼트 이름을 **형태(hooks, types, components)** 가 아니라 **목적**으로 짓도록 안내한다. `app/hooks`보다 `app/bootstrap`, `app/providers`, `app/session`, `app/routes`처럼 “왜 있는지” 드러나는 이름이 좋다.

### 정리
- App 레이어에서 React Query 훅 사용은 **허용**이지만 **광범위하게 권장**되지는 않는다.
- 기준: **앱 전체가 그 데이터 없이는 부팅/라우팅/전역 설정을 못 한다 → App 가능.**  
  **특정 페이지·위젯·기능·엔티티의 데이터다 → 해당 레이어에 둬야 함.**

## 타입
타입은 slice별로 `model/types.ts`에 두고, 필요 시 `model/types/` 폴더로 분리한다.

### API·도메인 타입
- 서버 API Request/Response, 도메인 엔티티 타입은 **slice의 `model/types.ts`**에 정의한다.
- 필요 시 `model/types/` 폴더로 파일을 나눌 수 있다.
- (아래 프로젝트 구조의 `types.ts` 주석 참고)

### 컴포넌트 props·context
- **원칙:** 해당 컴포넌트(또는 context)가 정의된 **같은 파일 상단**에 선언한다. (공동위치)
- **예외:** Vue/Svelte처럼 같은 파일에 둘 수 없거나, 여러 컴포넌트에서 props 타입을 공유할 때는 **같은 폴더(보통 `ui`)**에 별도 파일로 둔다. (예: `RecentActionsProps.ts`)

### 상수·리터럴 타입
- `as const` 리터럴 타입·enum·상수 관련 타입은 **해당 상수가 정의된 파일**에 함께 둔다.
- 예: 도메인 고정값 타입은 `model/consts.ts`에, enum은 용도에 따라 `api` 또는 `ui` 세그먼트의 사용처 근처에 정의한다.

타입 상세·enum 등은 FSD 공식 예시 문서 참고 (아래 참고 자료).

## 프로젝트 구조
```
|- node_modules       
|- public
|- src                
    |- app            
    |- pages          
    |- widgets        // 여러 feature/entity를 조합한 UI 블록(검색바, 상세 패널 등)
    |- features       // 사용자 가치가 있는 행위 단위(권한 수정, 필터링, 댓글 작성 등)
    |- entities       // 비즈니스 핵심 개체(Company, Complex, User 등)의 상태/뷰
        |- company
              |- api
                  |- companyApi.ts
              |- model
                  |- consts.ts     // 도메인 고정값(회사 유형, 상태 코드 등).
                  |- mutations.ts  // react-query useMutation 훅들(생성/수정/삭제 등)
                  |- queries.ts    // react-query useQuery/useInfiniteQuery 훅들
                  |- queryKeys.ts  // 쿼리 키 중앙 관리 (예: ['company','list',{q}])
                  |- types.ts      // API DTO, Domain Type, ViewModel 타입 정의(명확히 구분 주석)
              |- ui
                  |- CompanySearch
                        |- CompanySearchBox.ts
                        |- useCompanySearch.ts
        |- complex
              |- api
              |- model
    |- shared         // 전역 유틸/토큰/기반 모듈(apiClient, hooks, ui primitives 등)
|- .gitignore
|- package.json
|- README.md
```

## 실전 적용 참고 (카카오페이 사례)
> 출처: [FSD 아키텍처 적용기 \| 카카오페이 기술 블로그](https://tech.kakaopay.com/post/fsd/)

### FSD 도입을 고려해볼 시점 (공식 문서 인용)
- 새로운 팀원이 구조를 이해하기 어려울 때
- 프로젝트가 커지면서 구조가 얽히고, 기능 개발 속도가 느려졌을 때  
→ **현재 구조에 문제가 없다면 반드시 바꿀 필요는 없다.**

### 레이어 선택
- **widgets**는 필수가 아니다. 재사용되는 독립 대형 UI가 적으면 사용하지 않고, features 내에서만 재사용 UI를 관리해도 된다.

### Slice Grouping (Pages)
- 동일 도메인 페이지가 많으면(목록/상세/등록/수정 등) **한 슬라이스 그룹**으로 묶을 수 있다.  
  예: `pages/benefit/benefitList`, `pages/benefit/benefitDetail` … → `pages/benefit/` 아래에 slice들 배치.
- FSD 가이드: 동일 도메인 데이터를 다루는 페이지는 하나의 slice에 속해 있을 수 있다.

### API 배치 기준 (재사용 범위로 구분)
| 재사용 범위 | 배치 위치 | 예시 |
|-------------|-----------|------|
| 해당 페이지 슬라이스에서만 사용 | `pages/[slice]/api` | 혜택 목록 조회 (목록 페이지 전용) |
| 같은 페이지 그룹 내 여러 슬라이스에서 사용 | `features/[slice]/api` | 혜택 상세 조회 (상세·수정 페이지에서 사용) |
| 여러 페이지/도메인에서 사용 | `entities/[domain]/api` | 매장(partner) 상세 조회 등 공통 도메인 API |

→ 모든 API를 entities에 몰면 entity가 비대해지고 “도메인 엔티티” 역할이 흐려지므로, **재사용 범위**로 나누는 것이 좋다.

### 마이그레이션: 상향식(Bottom-Up)
기존 코드를 한 번에 옮기지 말고, 아래 순서로 점진 이동하면 “이 코드가 어느 레이어까지 재사용되는가?”를 팀이 자연스럽게 익힐 수 있다.

1. 전역 사용 코드 → `shared`
2. 여러 features에서 사용 → `entities/[domain]`
3. 여러 pages 슬라이스에서 사용 → `features/[slice]`
4. 나머지 페이지 전용 → `pages` 하위에 배치

### 적용 효과 (정리)
- **shared** → 전역에서 사용
- **entities** → 여러 pages/기능에서 사용
- **features** → 특정 페이지 그룹에서만 사용
- **pages** → 특정 페이지/슬라이스에서만 사용  

→ “이 코드는 어디에 넣어야 할까?”에 대한 판단 기준이 명확해진다.

## 문서 출처·작성 주체·표준 여부
[feature-sliced.design](https://feature-sliced.design/) / [fsd.how](https://fsd.how/) 는 FSD의 **공식 문서 사이트**로 보는 것이 맞다. 다만 “누가 만들었는지”, “표준인지”는 층위를 나눠서 보는 것이 정확하다.

### 출처·운영 주체
- **운영 주체:** 국제 표준화 기구가 아니라 **GitHub 오픈소스 조직 [feature-sliced](https://github.com/feature-sliced)** 이다. 조직 프로필에는 공식 사이트로 fsd.how가 연결되어 있다.
- **문서 저장소:** 핵심 문서는 [feature-sliced/documentation](https://github.com/feature-sliced/documentation) 에서 공개적으로 관리되며, v0.1 → v1.0 → v2.1 형태로 **버전별** 관리된다.
- **참고 시:** 랜딩·서드파티 글보다 **현재 버전(v2.1) docs + GitHub release notes** 를 기준으로 보는 것이 가장 안전하다. (v2.0→v2.1에서 pages-first, Processes 레이어 deprecated 등 변경이 있었다.)

### 작성·계보 (단일 창시자 아님)
- **계보:** 2018년경 **Feature Slices**(Sergey Sova, React SPB Meetup)와 **Feature Driven**(Oleg Isonen, React Berlin) 접근이 있고, 이후 통합·발전된 형태로 소개된다. 공식 migration 문서에도 2018년 feature-slices 발표·Oleg Isonen의 feature-driven과의 병합이 언급된다.
- **FSD core-team·릴리스:** Ilya Azin(azinit)의 FSD 발표, Lev Chelyadinov(illright)의 v2.0 안정판·v2.1 릴리스 등이 GitHub에 남아 있으며, 조직 멤버로 다수의 maintainer가 확인된다.
- **정리:** 특정 회사 사내 표준이 아니라 **2018년경 러시아어권 프론트엔드 커뮤니티에서 시작해 core-team이 이어온 오픈소스 방법론**으로 보는 것이 사실에 가깝다.

### “표준”인가? (세 층위)
| 구분 | 여부 | 설명 |
|------|------|------|
| **국제/공식 표준** | ❌ 아님 | 문서가 FSD를 “architectural methodology”, “rules and conventions”로 정의하고, GitHub에서 버전드 문서로 계속 개정하는 형태이다. ISO·W3C 같은 공인 표준이 아니다. |
| **FSD 커뮤니티 내부 표준** | ✅ 해당 | 레이어/세그먼트 **standard names**(app, page, feature, entity, shared, ui, model, api 등)를 쓰라고 권하며, 공식 CLI·ESLint·Steiger 등 도구를 제공한다. FSD를 채택한 팀이 이 이름·규칙을 따르면 “FSD 쪽 표준”에 맞는 셈이다. |
| **업계 보편 표준** | ❌ 아님 | 공식 Mission 문서도 “모든 프로젝트에 100% 맞는 만능 해결책은 없다”고 명시하고, slice 이름은 앱 도메인에 따라 달라져 표준화되지 않는다고 적는다. React/웹 전체의 범용 표준이라고 보기는 어렵다. |

### 신뢰성·인용 시 유의점
- **신뢰성:** “커뮤니티가 유지하는 아키텍처 방법론 문서”로서는 **높은 편**이다. 근거는 공개 GitHub 조직·문서 저장소 이력·릴리스, 공식 examples/awesome/tooling, 다국어 커뮤니티, 최근까지의 업데이트 등이다.
- **유의점:**
  - 이 문서는 **FSD 진영이 자기 방법론을 설명하는 1차 자료**이므로, “FSD가 최고다” 같은 가치판단까지 객관적 사실로 받아들이면 과장이다.
  - 팀 소개 페이지는 아직 WIP인 부분이 있고, 일부 문서도 WIP 표시가 있다.
- **인용 시:** **“FSD의 공식 가이드”** 라고 부르는 것은 정확하다. **“프론트엔드 업계의 공인 표준”** 이라고 부르면 과장이다. 팀 내부에서 FSD를 채택하고 Steiger/ESLint/CLI를 쓰면 그 팀의 **사실상 표준**으로 삼을 수는 있다.

**한 줄 정리:** 이 문서는 “공식 표준 문서”라기보다 **“공식 커뮤니티가 유지하는 오픈소스 아키텍처 방법론 문서”** 에 가깝다.

## 참고 자료
- [Feature-Sliced Design 공식](https://feature-sliced.design/) · [fsd.how](https://fsd.how/) — 문서는 버전별(v2.1 등) 관리되므로 현재 docs + [GitHub releases](https://github.com/feature-sliced/documentation/releases) 기준 참고 권장.
- [feature-sliced/documentation](https://github.com/feature-sliced/documentation): 문서 저장소
- [Types \| FSD](https://feature-sliced.design/docs/guides/examples/types): 타입 배치, DTO/매퍼, props·context
- [마이그레이션 가이드 \| FSD](https://feature-sliced.design/docs/guides/migration/from-custom)
- [FSD 아키텍처 적용기 \| 카카오페이 기술 블로그](https://tech.kakaopay.com/post/fsd/): 실전 도입·API 배치·Slice Grouping·상향식 마이그레이션

# Table

## 개요
테이블/데이터그리드 디자인시스템 및 컴포넌트 개발 방법론 정리

## 설계
테이블/데이터그리드 라이브러리 생태계에는 크게 다음과 같이 두 가지 범주가 있음.

### Component-based 기반
#### 특징
- 스타일, 테마가 포함된 컴포넌트/마크업 제공
- ex. AG Grid

#### 장점
- 바로 사용 가능한 마크업/스타일 제공
- 간단한 설정만 필요.
- Turn-key 방식의 경험.

### 단점
- 마크업에 대한 제어력이 떨어짐.
- 사용자 지정 스타일은 일반적으로 테마 기반
- 큰 bundle 사이즈
- 프레임워크 어댑터 및 플랫폼과 결합도 높음.

### Headless 기반 

#### 특징
- UI 요소와 상호 작용에 필요한 로직, 상태, 처리 및 API를 제공하지만 마크업, 스타일 또는 미리 구현된 코드는 제공하지 않는 라이브러리 및 유틸리티
- 데이터 처리, 상태 관리 및 비즈니스 로직과 같은 복잡한 작업은 라이브러리에서 처리해 주므로, UI만 신경쓰면 됨.

#### 장점
- 마크업 및 스타일에 대한 완벽한 제어
- 모든 스타일링 패턴(CSS, CSS-in-JS, UI 라이브러리 등)을 지원
- 작은 bundle 사이즈

#### 단점
- 더 많은 개발 필요.
- 마크업, 테마, 스타일 제공되지 않음.

## 비교
2026년 기준 Top4

### 1. TanStack Table
### 2. AG Grid
### 3. MUI Data Grid
#### 단점
Pro 기능 유료.
### 4. Material React Table
Material UI v6 + TanStack Table v8 조합(MRT v3 기준)
#### 단점
ColSpan, RowSpan 지원 안함.

## 디자인시스템
일반적으로 3-Layer 패턴 사용.

### Layer 1 - DS Core
가볍고 안정적인 공통 컴포넌트로 구성

#### 구성 요소
- Primitives : Table, Thead, Tbody, Tr, Th, Td
- hover, selected, focus, density, stickyHeader 같은 룩앤필(시각적 규칙)
- 접근성 최소치(예: aria-sort 표시 규칙 등)

#### 포인트
- 이 레이어는 TanStack Table 같은 로직 의존이 없어도 됨.
- 버전업/릴리즈를 가장 안정적으로 가져갈 수 있는 영역

### Layer 2 - DS Pattern / Kit
조립 가능한 패턴 부품(디자인시스템 안에 서브 엔트리로 분리)  
ex. @rsquare/ds/data-table-kit

#### 구성 요소
- SortableHeader
- TableToolbar (검색/필터 슬롯)
- Pagination
- ColumnVisibilityMenu
- EmptyState, LoadingState
- RowSelectionCheckbox (단, 로직은 바깥에서 주입)

#### 포인트
- TanStack Table에 강하게 묶이지 않게 props를 설계
  - ex. sortDirection, onSortChange 같은 표준 이벤트/상태 인터페이스로 받기
- TanStack 객체(header, column)를 그대로 props로 받지 않는 쪽이 커플링이 덜함
- DS 안에서 개발 속도를 올리되, 완제품 DataGrid까지는 가지 않는 레이어

### Layer 3 - Product-level DataGrid
무겁고 도메인 의존

#### 구성 요소
- @tanstack/react-table + @tanstack/react-query + @tanstack/react-virtual까지 엮인 완제품 <DataTable/>
- 서버 페이징/정렬/필터
- 사용자별 컬럼 설정 저장(localStorage/DB)
- export, inline edit 같은 “관리자 도메인” 기능

#### 포인트
- 여기서 커플링이 터지는 게 정상. 그래서 DS 바깥이 유지보수에 유리
- DS는 “재료(레이어1) + 조립부품(레이어2)”까지만 책임지고, 레이어3은 앱/도메인에서 책임.
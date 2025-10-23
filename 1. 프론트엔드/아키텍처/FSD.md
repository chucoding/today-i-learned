# FSD 아키텍처
기능 분할 설계(Feature-Sliced Design, FSD) 아키텍처 

- segments 종류 : ui, model, api, lib, config


```
|- node_modules       
|- public
|- src                
    |- app            // 앱 엔트리, 전역 프로바이더(Theme, Router, QueryClient 등)
    |- pages          // 라우트 단위 화면(상위 레이아웃 + 위젯 조합)
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

장점

- 레이어별로 격리되어 있어서 변화에 용이(확장성, 수정, 삭제 등)
- 추상화의 벽이 생겨서 상위 계층에서는 하위 계층의 역할을 몰라도 된다.
- 객체지향방법론에서 자주 언급되는 **다형성, 캡슐화, 추상화, 응집도, 결합도**와 같은 개념이 많이 녹아져있어서 코드의 안전성이 높아진다.

단점

- 설계단에서 시간을 많이 가져가야 한다.
  - 초기 설계를 잘못하게 될 경우 고통 받을 수 있음
  - 팀과의 소통이 중요하다.
- 아토믹 디자인 패턴과 비슷한 단점인 **이건 어디에 들어가야 맞을까?** 와 같은 생각이 들 때가 있다.
- 폴더가 매우 많이 생기게 된다. (뎁스가 깊어질 수 있음)
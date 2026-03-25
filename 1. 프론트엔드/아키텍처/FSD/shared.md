# shared Layer
FSD 아키텍처 shared 레이어
> [FSD Layer](https://feature-sliced.design/kr/docs/reference/layers)  


## 특징
- 앱의 기본 구성 요소와 기반 도구들을 모아두는 곳
- Shared 내부의 파일들은 서로 자유롭게 import 가능
- 일반적으로 Shared의 코드는 미리 계획되지 않고 개발 중에 추출(실제로 어떤 코드 부분이 공유되는지는 개발 중에만 명확해짐) 

## 구조
shared는 app과 마찬가지로 Slice 없이 Segment로만 구성

```
shared
  |- api 
  |- ui
  |- lib
  |- config
  |- routes
  |- i18n
```
> ❗️ Segement는 본질(무엇인지)이 아닌 목적(왜)을 설명해야함.
> - components, hooks, modals, assets (x)
> - 파일들이 무엇인지는 설명하지만 내부 코드를 탐색하는 데 도움 안됨.

### api
- API 클라이언트와 공통 백엔드 요청 함수

### ui
- 비즈니스 로직은 포함하지 않지만, 브랜드 테마는 적용 가능
- 로고, 레이아웃, 자동완성/검색창 등 UI 자체 로직을 포함하는 컴포넌트는 허용

## 참고자료
- [FSD 튜토리얼](https://feature-sliced.design/kr/docs/get-started/tutorial)
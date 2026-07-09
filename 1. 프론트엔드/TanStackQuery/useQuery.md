# useQuery

## 옵션
### enable
실행조건

## 기본값
기본값은 아래와 같이 상수로 만들어서 지정.
```ts
const DEFAULT_CHART_DATA = []

const { data = DEFAULT_CHART_DATA } = query
```

- Recharts 등 기본값이 있어야 하는 라이브러리 사용시 위와 같은 지정이 필요할 수 있음.
- 상수로 만드는 이유는 fallback 배열 참조를 안정적으로 유지
  - 매번 새 배열을 만들면 메모리 낭비
  - 상수가 컴포넌트 밖 module scope에 있으면 모듈이 로드될 때 한 번만 실행됨.


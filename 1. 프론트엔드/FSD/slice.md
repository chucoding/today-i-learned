# Slices
레이어 내 비즈니스 도메인별 분할
> [FSD 공식문서 - Slices and segments](https://feature-sliced.design/kr/docs/reference/slices-segments#slice)
```
Layer → Slice → Segment
```
- 같은 레이어 내 다른 슬라이스 참조 불가 (응집도↑, 결합도↓ 하기 위함.)
- Segement 아래에 ui 폴더등을 만드는건 허용, 폴더를 Slice로 취급하는 패턴은위배
- slice의 파일명 규칙은 kebab-case를 사용
```
features/
└── auth/
    ├── ui/
    │   └── login-form/
    │       ├── LoginForm.tsx
    │       └── fields.ts
    ├── model/
    └── index.ts
```

## Slice group
관련 Slice를 폴더로 묶는 기법
> [FSD 공식문서 - Slice groups](https://feature-sliced.design/kr/docs/reference/slice-groups)
```
entities/
└── payment/       # Slice group
    ├── invoice/   # Slice
    │   └── model/ # Segment
    └── receipt/   # Slice
        └── model/ # Segment
```
- Slice group은 탐색을 위한 그룹 폴더로 Segment, `index.ts`를 가질 수 없음.
- Slice group의 최대 폴더 깊이는 숫자로 제한하지 않음.
# 달력 라이브러리 비교
업데이트 날짜 : 2026-05-14

## day.js(dayjs)
### 장점
- 성능(런타임+번들)만 보면 Day.js가 가장 유리
- 초기 로드가 빠르고, 대부분의 작업이 경량 오버헤드로 수행

### 단점
- “날짜 유틸”만 제공 (포커스/키보드 내비게이션/ARIA 속성/범위 선택 같은 DatePicker·TimePicker의 핵심 UI 로직은 없음)
- 비그레고리력·DST(서머타임)·로캘 차이 같은 복잡도는 플러그인 조합과 정책 결정이 필요

## Zag.js + @internationalized/date
디자인시스템 DatePicker의 날짜 모델은 주로 @internationalized/date 사용

### 장점
- React 전용이 아니라 **어댑터 기반(React/Vue/Svelte/…)

### 단점
- UI는 전부 직접 만들어야함.

### 예제
```
vanilla-extract 디자인시스템
  └─ DatePicker / DateTimePicker
      ├─ UI/접근성: React Aria Components 또는 React Aria hooks
      ├─ 날짜 타입: @internationalized/date
      └─ 스타일: vanilla-extract
```
vanilla-extract 기반 디자인시스템이라면 스타일을 강제하지 않는 headless/unstyled 계열이 잘 맞음.
> 💡 React Aria
> - 기본 스타일이 없고 어떤 스타일링 솔루션으로든 디자인시스템을 만들 수 있음.
> - 상태도 data-*, ARIA attribute, render props 등으로 노출되므로 vanilla-extract로 상태별 스타일 붙이기 좋음.

### 디자인시스템 구조
```
Design System
  ├─ DatePicker
  │   ├─ Zag.js date-picker machine
  │   ├─ @internationalized/date
  │   └─ vanilla-extract styles
  │
  ├─ DateTimePicker / TimePicker가 필요해질 경우
  │   └─ @internationalized/date의 ZonedDateTime 모델 사용
  │
  └─ DesignSystemProvider
      ├─ locale
      └─ timeZone

MUI App
  ├─ MUI ThemeProvider
  └─ DesignSystemProvider에 locale/timeZone 주입
```

```ts
// app/AppProviders.tsx
export function AppProviders({ children }: { children: React.ReactNode }) {
  const locale = "ko-KR";

  const timeZone =
    tenant?.timeZone ??
    user?.preferredTimeZone ??
    serviceConfig?.defaultTimeZone ??
    Intl.DateTimeFormat().resolvedOptions().timeZone ??
    "UTC";

  return (
    <MuiThemeProvider theme={muiTheme}>
      <DesignSystemProvider locale={locale} timeZone={timeZone}>
        {children}
      </DesignSystemProvider>
    </MuiThemeProvider>
  );
}
```

# 참고자료
[shadcn Calendar](https://ui.shadcn.com/docs/components/radix/calendar)
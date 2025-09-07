# 빌드

## 동작방식

1. 빌드 시점 : `.css.ts` 파일들이 JavaScript 모듈(`.mjs`)과 타입 정의(`.d.ts`)로 변환
    1. `.css.ts` 는 Vanilla Extract CSS-In-JS 라이브러리에서 사용하는 파일 형식(CSS를 JS/TS 형식으로 작성하고 빌드 후 .mjs로 변환됨)
2. 런타임(브라우저 접속시) :
    1. Javascript 모듈(`.mjs`)을 읽음
    2. 이 모듈이 실행되면서 CSS 문자열을 생성
    3. 생성된 CSS를 `<style>`태그에 삽입
    4. CSSOM: 삽입된 `<style>` 태그의 CSS를 파싱해서 CSSOM 생성

# recipes

Vanilla Extract의 컴포넌트 변형(variants)과 상태를 관리하는 모듈

사용 목적

- 기본 구조를 정의할 때 사용

```jsx
const cardRecipe = recipe({
  base: { padding: '16px', borderRadius: '8px' },
  variants: {
    type: {
      default: { backgroundColor: 'white' },
      highlighted: { backgroundColor: 'yellow' }
    }
  }
});
```

**1. 컴포넌트 변형 관리**

- 하나의 컴포넌트가 여러 스타일 변형을 가질 때 사용
- 예: 버튼의 크기(small, medium, large), 색상(primary, secondary, danger) 등

1. 조건부 스타일링

```jsx
import { recipe } from '@vanilla-extract/recipes';

const buttonRecipe = recipe({
  base: {
    padding: '12px 24px',
    borderRadius: '6px',
    border: 'none',
    cursor: 'pointer'
  },
  
  variants: {
    size: {
      small: { padding: '8px 16px', fontSize: '14px' },
      medium: { padding: '12px 24px', fontSize: '16px' },
      large: { padding: '16px 32px', fontSize: '18px' }
    },
    
    variant: {
      primary: { backgroundColor: 'blue', color: 'white' },
      secondary: { backgroundColor: 'gray', color: 'black' },
      danger: { backgroundColor: 'red', color: 'white' }
    }
  },
  
  // 기본값 설정
  defaultVariants: {
    size: 'medium',
    variant: 'primary'
  }
});
```

1. 컴포넌트에서 사용

```jsx
// 컴포넌트에서 사용
<button className={buttonRecipe({ size: 'large', variant: 'danger' })}>
  삭제
</button>

// 기본값 사용
<button className={buttonRecipe()}>
  기본 버튼
```

# Sprinkles

**디자인 시스템을 위한 유틸리티 클래스 생성기**

A zero-runtime atomic CSS framework for vanilla-extract.

사용 목적

- 세부 조정할 때 사용

```jsx
<div className={sprinkles({
  ...cardRecipe({ type: 'default' }),
  marginTop: 'lg',        // 추가 여백
  border: '1px solid'     // 추가 테두리
})}>
  카드 내용
</div>
```

**1. 유틸리티 클래스 자동 생성**

- Tailwind CSS와 유사한 방식으로 미리 정의된 디자인 토큰을 기반으로 유틸리티 클래스를 생성
- 예: padding, margin, color, fontSize 등의 클래스

**2. 타입 안전성**

- TypeScript와 완벽하게 통합되어 타입 안전한 CSS 클래스 제공
- 잘못된 값 사용 시 컴파일 타임에 오류 감지
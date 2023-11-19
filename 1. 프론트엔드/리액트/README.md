# React
**컴포넌트 기반 개발 지향(CBD)** 자바스크립트 라이브러리

특징
- 데이터가 변경됨에 따라 적절한 컴포넌트만 효율적으로 갱신하고 렌더링
- props와 state 개념으로 개발
- node 와 빌드 도구를 설치한 개발환경을 세팅 (CRA) 또는 cdn 방법으로 사용

>💡 React는 라이브러리? vs 프레임워크?<br/>
=> [공식홈페이지](https://react.dev/)에서는 라이브러리라고 지칭

장점
- 메모리 상에 가상 DOM을 만들어 조작하기 때문에 DOM조작이 정말 빠르다.

단점
- 브라우저가 매번 JSX → JS로 변환하는 것은 성능 저하로 이뤄진다. 

# React 개발환경

**패키지 설치**

```powershell
npm i
```

# React Naming Conventions

**useState**

```jsx
const [x, setX] = useState(initialValue);
const [isButtonEnabled, setIsButtonEnabled] = useState(false)
```

**컴포넌트**

```jsx
# components - 파스칼 기법
Header.js Footer.js

# Non-components - 카멜 기법
fetchApi.js
```

전체 시스템을 건드리는 컴포넌트는 뒤에 Provider를 붙인다.

```jsx
<CookiesProvider>
```

# React Style Guide

### Page
```jsx
import React from 'react'; // 함수 컴포넌트 사용시 작성. (JSX 문법 해석)
```
> 💡 React 17에서는 import를 하지 않아도 된다?<br/>
React 17 이전에는 JSX를 <span style="background-color:#ddd;">React.createElement</span>로 변환 <br/>
하지만 이 방식은 성능 및 기술부채 등 많은 제약이 있어서 <span style="background-color:#ddd;">react/jsx-runtime</span>를 사용하여 빌드 시점에 babel이 inject하는 방식으로 변경됨.<br/>
사용하려면 별도의 설정 필요.

### 함수명 function vs const
React 컴포넌트 내부에서 함수를 선언할 때, 함수를 const 키워드로 선언하는 것이 일반적인 방법입니다. 함수를 const로 선언하면 해당 함수는 블록 스코프 내에서만 유효하며, 재할당이 불가능합니다. 이렇게 하면 함수가 렌더링과 관련 없는 상태를 유지하며 React의 렌더링 최적화를 도와줍니다.

단, 만약 함수가 여러번 사용된다면 useCallback()으로 감싸서 계속 렌더링 되는것을 방지


# Linter & Formatter
뷰티파이 vs 프리티어


# 참고자료

| TITLE | URL |
| --- | --- |
|React 17 import React|https://so-so.dev/react/import-react-from-react/|
| vscode React snippet | https://www.hanl.tech/blog/vs-code-react-time-awesome-snippets/ |
| 뷰티파이 vs 프리티어 | https://ux.stories.pe.kr/150 |
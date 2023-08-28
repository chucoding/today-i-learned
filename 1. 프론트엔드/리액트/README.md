# React
**컴포넌트 기반 개발 지향** 자바스크립트 라이브러리
- 데이터가 변경됨에 따라 적절한 컴포넌트만 효율적으로 갱신하고 렌더링
- props와 state 개념으로 개발
- node 와 빌드 도구를 설치한 개발환경을 세팅 (CRA) 또는 cdn 방법으로 사용

💡React는 라이브러리? 프레임워크?<br/>
[공식홈페이지](https://react.dev/)에서는 라이브러리라고 지칭

| 장점 | 메모리 상에 가상 DOM을 만들어 조작하기 때문에 DOM조작이 정말 빠르다. |
| --- | --- |
| 단점 | 브라우저가 매번 JSX → JS로 변환하는 것은 성능 저하로 이뤄진다.  |

# 네이밍 규칙

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

# Linter & Formatter
뷰티파이 vs 프리티어

# 개발환경

**패키지 설치**

```powershell
npm i
```
# 참고자료

| TITLE | URL |
| --- | --- |
| vscode React snippet | https://www.hanl.tech/blog/vs-code-react-time-awesome-snippets/ |
| 뷰티파이 vs 프리티어 | https://ux.stories.pe.kr/150 |
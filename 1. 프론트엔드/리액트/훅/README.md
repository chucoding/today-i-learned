# React Hooks

## 1. useState

## 2. useEffect
[useEffect.md](./useEffect.md)

## 3. useContext

## 4. useReducer
컴포넌트에 reducer를 추가하는 React Hook
-  useState와 매우 유사하지만, state 업데이트 로직을 이벤트 핸들러에서 컴포넌트 외부의 단일함수로 분리할 수 있다는 차이점이 있음
```js
const [state, dispatch] = useReducer(reducer, initialArg, init?)
```

## 5. useRef
[useRef.md](./useRef.md)

## 6. useMemo
상태값 변화시 실행되는 함수 (불필요한 재계산을 방지)
- 렌더링 중에 실행
```js
const expensiveValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

💡 React.memo vs useMemo()
- React.memo
  - 컴포넌트 자체를 최적화
  - props 변경을 기준으로 동작
- useMemo
  - 컴포넌트 내부의 특정 값을 최적화
  - 지정된 의존성 배열의 변경을 기준으로 동작

```js
const MemoizedComponent = memo(SomeComponent, arePropsEqual?);
```

### 특징
- memo로 감싸면 해당 컴포넌트의 memoized 버전을 얻음.
- 부모 컴포넌트가 리렌더링 되어도 props가 변경되지 않았다면 리렌더링 되지 않음.(보장하지는 않음)

## 7. useCallback
함수를 메모이제이션 (기억) 하는 훅
- 컴포넌트가 리렌더링될 때마다 새로운 함수 인스턴스가 생성되는 것을 방지

```js
const setPromptCallback = useCallback((newPrompt) => {
  setPrompt(newPrompt);
}, []); //컴포넌트가 처음 렌더링될 때 한번만 생성(변경 되지 않음)
```

## 8. useImperativeHandle

## 9. useLayoutEffect

## 10. useDebugValue
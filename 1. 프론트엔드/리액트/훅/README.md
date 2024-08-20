# React Hooks

## 1. useState

## 2. useEffect
상태값 변화시 실행되는 함수 (DOM 조작, 데이터 페칭, 구독 등 side effcts 처리 목적)
- 렌더링 후에 실행
```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]);
```

## 3. useContext

## 4. useReducer

## 5. useRef

## 6. useMemo
상태값 변화시 실행되는 함수 (불필요한 재계산을 방지)
- 렌더링 중에 실행
```js
const expensiveValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

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
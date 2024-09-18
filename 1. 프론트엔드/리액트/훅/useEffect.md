# useEffect
DOM을 업데이트하고 난 뒤에 추가적인 실행을 하고 싶을때 사용 ( mount + update + unmount )

```jsx
useEffect(() => {
  console.log('컴포넌트가 화면에 나타남');
  return () => {
    console.log('컴포넌트가 화면에서 사라짐'); //clean-up 
  };
}, []);
```

**활용방법**
- 서버에서 데이터가져오는 작업
    - 참고) React Query같은 서버 상태 관리 도구를 쓰면 useEffect를 제거하고 간결한 코드를 유지할 수 있음
- 타이머
- 상태에 따른 부수효과를 처리하는 경우
- onClick, submit의 경우에는 useEffect 대신 이벤트 핸들러에서 처리하는것이 좋음

❗useEffect는 왠만하면 안쓰는것이 좋다! => [Keeping Components Pure](https://beta.reactjs.org/learn/keeping-components-pure#where-you-_can_-cause-side-effects)


**clean-up**
- 컴포넌트의 unmount, update 직전에 작업을 수행하고 싶을 때 사용
- unmount 될 때 ⇒ useEffect(func, [])
- 특정값 update 직전 ⇒ useEffect(func, [특정값])

cleanup 사용 예시) 다음과 같이 타이머가 여러개 생성되는것을 방지

```jsx
useEffect(()=>{
    let a = setTimeout(()=>setDisplay(false), 2000);
    return () => {
        clearTimeout(a);
    }
}, []);
```

**automatic batching**

react 18+ 상태변경시 모아서 한번에 렌더링

```jsx
useEffect(()=>{
    setFade("end");
    return()=>setFade("");
}, [tab]);
```

다음과 같이 2개의 상태변경 훅이 있는경우 렌더링이 두번 발생하지 않음(마지막에 한번만)

**useEffect 첫 렌더링 막기**

[리액트, useEffect 첫 렌더링때 함수실행 막기](https://seokd.tistory.com/8)

### useEffect 비동기

https://velog.io/@he0_077/useEffect-%ED%9B%85%EC%97%90%EC%84%9C-async-await-%ED%95%A8%EC%88%98-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0
# useEffect

DOM을 업데이트하고 난 뒤에 추가적인 실행을 하고 싶을때 사용

( `mount` + `update` + `unmount` )

```jsx
useEffect(() => {
  console.log('컴포넌트가 화면에 나타남');
  return () => {
    console.log('컴포넌트가 화면에서 사라짐'); //clean-up 
  };
}, []);
```



## 사용 이유

React의 render phase에 따르면, 변형(mutations), 구독(subscriptions), 타이머, 로깅 등의 사이드 이펙트들이 컴포넌트 함수 내부에 있어서는 안됨. 이를 해결하기 위해 useEffect를 사용.



**활용방법**

- 서버에서 데이터가져오는 작업
  - 참고) React Query같은 서버 상태 관리 도구를 쓰면 useEffect를 제거하고 간결한 코드를 유지할 수 있음
- 타이머
- 상태에 따른 부수효과를 처리하는 경우
- onClick, submit의 경우에는 useEffect 대신 이벤트 핸들러에서 처리하는것이 좋음

❗useEffect는 왠만하면 안쓰는것이 좋다! =&gt; [Keeping Components Pure](https://beta.reactjs.org/learn/keeping-components-pure#where-you-_can_-cause-side-effects)

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

[https://velog.io/@he0_077/useEffect-%ED%9B%85%EC%97%90%EC%84%9C-async-await-%ED%95%A8%EC%88%98-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0](https://velog.io/@he0_077/useEffect-%ED%9B%85%EC%97%90%EC%84%9C-async-await-%ED%95%A8%EC%88%98-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)



# useLayoutEffect

useEffect와 동일하지만, DOM 변경 후 paint 전에 **동기적**으로 실행

- DOM을 조작하는 코드가 존재하더라도, 사용자는 깜빡임을 보지 않음
- useEffect로 전달된 함수는 layout과 paint가 완료된 후에 **비동기적**으로 실행됨
- React 18부터는 useEffect에서도 layout과 paint 전에 동기적으로 함수를 실행할 수 있는 [flushSync](https://reactjs.org/docs/react-dom.html#flushsync)라는 함수가 추가되었지만 강제로 실행하는 것이다보니, 성능상 이슈가 있을 수 있음.
- 렌더링(render phase) -&gt; DOM 변경 -&gt; layout -&gt; useLayoutEffect -&gt; paint -&gt; useEffect 순으로 실행
- 스크롤 위치를 찾거나 어떤 element의 스타일 요소를 변경하는 등 직접적으로 DOM을 조작하는 곳 제외하고는 useEffect를 사용하는 것을 추천
- 공식 문서에서도 useEffect를 먼저 사용한 후에, 문제가 생긴다면 그때 useLayoutEffect를 사용하는 것을 추천

[참고자료 : useEffect와 useLayoutEffect의 차이](https://www.howdy-mj.me/react/useEffect-and-useLayoutEffect)
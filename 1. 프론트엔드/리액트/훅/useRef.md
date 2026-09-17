# useRef

> - [useRef - React](https://react.dev/reference/react/useRef)
> - [Referencing Values with Refs - React](https://react.dev/learn/referencing-values-with-refs)
> - [Escape Hatches - React](https://react.dev/learn/escape-hatches)

렌더링에 필요 없는 값을 `current` 하나에 담아 두는 훅. `current`를 바꿔도 리렌더링이 일어나지 않고, 리렌더링이 되어도 같은 객체가 유지됨.

```js
const ref = useRef(0);
ref.current = ref.current + 1; // 리렌더링 없음
```

## 왜 쓰는가

React는 state를 바꾸면 알아서 화면을 다시 그려 주고, 앱 로직 대부분은 그 흐름 안에서 끝남. 그 흐름 밖에 있는 것을 다룰 때 잠깐 빠져나가는 비상구가 ref.

- 브라우저 DOM API를 직접 호출해야 할 때 (`input.focus()`, `scrollIntoView()`, 비디오 재생)
- `setTimeout`, `setInterval`의 ID처럼 리렌더링 사이에 남겨야 하지만 화면에 그릴 값은 아닌 것
- React로 만들지 않은 차트, 지도, 플레이어 라이브러리 인스턴스 보관

## 언제 안 쓰는가

- 화면에 보여야 하는 값에는 쓰지 않음. `current`를 바꿔도 화면이 갱신되지 않으므로 `useState`를 사용
- 편해 보인다고 일반 값 저장소로 쓰지 않음. react.dev도 `useRef`와 `useEffect`를 Escape Hatches 챕터로 묶고 "Most of your application logic and data flow should not rely on these features"라고 못박음

| | ref | state |
|---|---|---|
| 변경 시 리렌더링 | 없음 | 발생 |
| 변경 방법 | `ref.current = v` 직접 수정 | setter 호출 |
| 렌더링 중 읽기 | 하지 않음 | 가능 |

## current 사용 규칙

- 렌더링 중에는 `current`를 읽지도 쓰지도 않음. 이벤트 핸들러와 effect 안에서만 다룸
- 유일한 예외는 지연 초기화

```js
const playerRef = useRef(null);
if (playerRef.current === null) {
  playerRef.current = new VideoPlayer(); // 렌더마다 생성되는 것을 방지
}
```

- DOM ref의 `current`는 첫 렌더 시점에 `null`이고 DOM이 붙은 뒤부터 값이 들어옴
- Strict Mode에서는 개발 중 ref 객체가 두 번 생성됨

## React 19 타입 변경

- `useRef`에 인자가 필수가 됨 (`useRef()`는 타입 오류, `useRef(undefined)`는 통과)
- `MutableRefObject`가 사라지고 `RefObject` 하나로 합쳐져, `useRef<T>(null)`로 만든 ref도 `current`를 수정할 수 있음

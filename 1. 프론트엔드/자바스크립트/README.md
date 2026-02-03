# 역사

ES6(ES2015)
- TypeScript의 기반이 되는, 클래스 문법과 모듈 기능 추가 
- 함수 단위 스코프에서 블록 단위 스코프로 변경됨
- this를 동적으로 바인딩하지 않는 화살표 함수 등장
- 모듈화 지원
- 콜백 지옥에서 구원해줄 Promise의 등장
- Default, Rest 파라미터
- Spread 연산자 (...object) 
	- 키 값이 겹칠경우 뒤에 오는 순서로 적용된다.
	- 배열에 추가하는 경우 [...list, object]
- 템플릿 리터럴

ES7(ES2016)
- 제곱 연산자(**) 등장
- Array.includes 배열에 해당 요소가 존재하는지 확인하는 메소드 등장

ES8(ES2017)
- async, await등이 발표됨
- Object.values(), Object.entries() 등장

※ 브라우저(특히 MS 계열)에서 지원해주지 않는 경우가 많아 바벨(Babel)이라는 트랜스파일러를 써야함(바벨은 웹브라우저가 아닌 Node.js위에서 돌아가기 때문에 별도 빌드 필요, 모듈화 하려면 웹팩(WebPack)같은 모듈 번들러 필요)


# 문법

```jsx
let str = null;
if (str) {
	console.log("str이 콘솔에 찍힐까?")
}
```

느낌표 두개 vs 느낌표 1개
- !!(느낌표 두개) : 값을 boolean으로 변환 (truthy → true, falsy → false)
- !(느낌표 1개) : 값을 boolean으로 변환한 후 부정 (truthy → false, falsy → true)

```javascript
// !! (이중 느낌표) 예시
!!null        // false
!!undefined   // false
!!NaN         // false
!!false       // false
!!0           // false
!!""          // false (빈 문자열)
!!"hello"     // true
!!1           // true
!!{}          // true

// ! (단일 느낌표) 예시
!null         // true
!undefined    // true
!NaN          // true
!false        // true
!0            // true
!""           // true (빈 문자열)
!"hello"      // false
!1            // false
!{}           // false

// 옵셔널 체이닝과 함께 사용
a = {}
a?.name => undefined
!!a?.name => false
```


# 참고자료
| TITLE         |URL|
|---------------|---|
| javascript 역사 |https://velog.io/@syoung125/%EA%B0%9C%EB%85%90%EA%B3%B5%EB%B6%80-1.-JavascriptES6%EB%9E%80-ES7%EA%B3%BC-%EC%B0%A8%EC%9D%B4%EC%A0%90|
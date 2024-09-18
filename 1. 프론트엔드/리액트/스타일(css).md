# Style(CSS)
웹 컴포넌트 스타일링 관리

# 1. className
```js
render() {
  return <span className="menu navigation-menu">Menu</span>
}
```
💡class라고 안쓰고 className이라고 쓰는 이유
> javascript에서 이미 class라는 명령어를 사용하고 있기 때문에 충돌 방지

# 2. inline style
```
render() {
	return(
    <h4 style={{color:'red', fontSize:'16px'}}>
  );
}
```
font-color, font-size 등 css는 카멜방식으로 변경해서 사용( 자바스크립트에서 - 는 실제로 빼기 동작)

💡 CSS 클래스가 인라인 스타일보다 더 나은 성능
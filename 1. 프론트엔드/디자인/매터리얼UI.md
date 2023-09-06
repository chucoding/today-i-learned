# MaterialUI (MUI)
안드로이드 시스템에서 검증된 높은 수준의 디자인 퀄리티를 자랑하는 CSS 프레임워크

# withStyles
material UI에서 사용하는 HOC
- style 객체를 넘기면 또다른 함수가 리턴됨.
- 그 함수에 스타일을 적용할 컴포넌트를 넘겨주면, style안에 선언한 스타일이 그대로 적용됨

# Select
Select 컴포넌트 v4에서 v5로 넘어가면서 수정사항이 많이 발생  
v4
```
anchorOrigin: {
	vertical: props.vertical || 'bottom',
	horizontal: props.horizontal || 'left'
}
```

v5
```
anchorOrigin: {
	vertical: props.vertical || 'bottom',
	horizontal: props.horizontal || 'left'
},
transformOrigin: {
	vertical: "top",
	horizontal: "left"
},
```

# Grid
# Select

Select input box의 위치를 조정 (v5부터 transformOrigin 옵션 추가됨)
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
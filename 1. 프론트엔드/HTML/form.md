# `<form>`
HTML `<form>` 요소는 정보를 제출하기 위한 대화형 컨트롤을 포함하는 문서 구획

> [form](https://developer.mozilla.org/ko/docs/Web/HTML/Reference/Elements/form)


## submit
form안에 `<input type="submit" />`이 있으면 해당 요소로 폼 제출 가능

> 💡 폼 제출 후 focus 유지?
> - Enter의 경우
>    - 제출을 발생시킨 시점에 포커스를 가진 요소는 그대로 포커스를 유지
>    - 별도로 blur 되지 않음.
> - 마우스 클릭의 경우
>    - 클릭 동작 때문에 포커스가 버튼으로 이동할 수 있음
>    - Safari는 버튼 클릭해도 focus 이동 안 할 수 있음
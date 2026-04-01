# Interface
```tsx
interface User {
	name: string,
	age: number,
	isValid?: boolean // Optional property
}
let userArr: User[] = [
	{
		name: 'Neo',
		age: 85
	},
	{
		name: 'Lewis',
		age: 52,
		isValid: false
	}
];
```

> 💡 **interface vs type**
>
> - **interface**: 객체 형태의 타입을 선언할 때 주로 사용. 같은 이름으로 **선언 병합(declaration merging)**이 가능해서, 같은 인터페이스를 여러 번 선언하면 자동으로 합쳐진다.
> - **type**: 객체뿐 아니라 유니언(`|`), 인터섹션(`&`), 원시/리터럴 타입 등 **다양한 타입 표현**이 가능하다.
> - 객체 타입만 정의할 때는 둘 다 쓸 수 있지만, **확장**은 `interface`는 `extends`, `type`은 `&`(인터섹션)로 한다.
> - 공식 스타일 가이드에서는 **일관성**을 위해 객체는 `interface`, 유니언/매핑 등이 필요할 때만 `type`을 쓰는 방식을 권장한다.

```tsx
//read only 설정하는법
let user: Readonly<IUser> = {
	name: 'Neo',
	age: 36
};

//함수형 인터페이스
interface IGetUser {
	(name:string): String
}
```

## extends
이중 상속은 인지 비용이 올라가므로 일반적으로 중첩으로 사용안하고 한번만 사용.
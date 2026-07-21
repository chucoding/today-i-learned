# Interface
객체 형태의 타입을 선언할 때 사용

```tsx
interface User {
	name: string,
	age: number,
	isValid?: boolean
}

let userArr: User[] = [{ name: 'Neo', age: 85 }];
```

## 명명 규칙
타입 시스템이 이미 정보를 표현하므로 이름에 타입 정보를 장식하지 않는게 좋음.
```
ex) IForm (x)
```
[타입스크립트 공식가이드 문서 참조](https://github.com/microsoft/TypeScript/wiki/Coding-guidelines)

## interface vs type
> 기본은 type을 사용하고, 객체 상속(extends)이 필요할 때만 interface를 사용
> - 공식 스타일 가이드에서는 **일관성**을 위해 객체는 `interface`, 유니언/매핑 등이 필요할 때만 `type`을 쓰는 방식을 권장한다.

### type
객체, Primitive, Union(`|`), Intersection(`&`) 등 다양한 타입 표현 가능
```ts
type StringOrNumber = string | number;
```

### interface
객체만 표현 가능

#### 장점 - interface extends 캐싱
- TypeScript Compiler 내부 최적화
- `type User = Base & {...}`는 매번 Base & NewType을 계산
  - 차이는 매우 작지만 수천~수만개의 타입을 가진 프로젝트에서는 누적될 수 있음.
- TypeScript Performance Wiki에서도 객체 상속에는 interface extends를 권장

#### 단점 - 선언 병합(declaration merging)
- 같은 인터페이스를 여러 번 선언하면 자동으로 합쳐짐.
- 타입의 경우 Duplicate identifier 에러 발생.
- JS의 `String.prototype.foo = ...` 처럼 기존 객체를 확장하는 라이브러리를 표현하기 위해 존재
- 의도된 기능이긴 하지만, 애플리케이션에서는 의도치 않은 충돌의 원인이 될 수 있음.
- 다음과 같이 전역 Window 타입에 프로젝트 전용 속성이 추가되어야 한다면 type은 기존 선언을 다시 열어서 병합할 수 없으므로 인터페이스 선언 병합으로 타입 보강
	```ts
	declare global {
	interface Window {
		analytics: {
		track: (event: string) => void;
		};
	}
	}
```

## 기능

### extends
이중 상속은 인지 비용이 올라가므로 일반적으로 중첩으로 사용안하고 한번만 사용.

### readonly
```ts
let user: Readonly<IUser> = {
	name: 'Neo',
	age: 36
};
```

### 함수형 인터페이스
```ts
interface IGetUser {
	(name:string): String
}
```

## 참고자료
[type vs interface](https://www.totaltypescript.com/type-vs-interface-which-should-you-use)
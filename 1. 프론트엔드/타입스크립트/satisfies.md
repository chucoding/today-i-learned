# satisfies

> [TypeScript 4.9 릴리스 노트](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-9.html#the-satisfies-operator)

값이 타입에 맞는지 검사만 하고 추론된 리터럴 타입은 그대로 남기는 연산자. TS 4.9(2022년 11월)부터 쓸 수 있다.

## 왜 쓰는가

- `: T` 애너테이션은 검사는 되지만 변수 타입이 `T`로 넓어져 리터럴 정보가 사라진다.
- `as T` 단언은 타입이 `T`로 넓어지는 데다 검사까지 건너뛰어 오타와 누락을 못 잡는다.
- `satisfies T`는 둘 다 챙긴다. 키가 바뀌거나 빠지면 컴파일 오류가 나고, 값은 좁은 타입으로 남아 이후 추론에 쓰인다.

`["name", "age"]`를 `keyof User` 배열로 다룰 때

| 방식 | 오타 검사 | 추론 결과 |
|------|-----------|-----------|
| `: (keyof User)[]` | O | `(keyof User)[]` |
| `as (keyof User)[]` | 느슨함 (`["name", "nmae"]`도 통과) | `(keyof User)[]` |
| `satisfies (keyof User)[]` | O | `("age" \| "name")[]` |

## 언제 안 쓰는가

- 값을 `keyof T` 유니온으로만 넘길 거면 애너테이션으로 충분하다.
- 키를 객체에서 `keyof typeof obj`로 뽑아 쓰면 객체 자체가 정본이라 satisfies가 더할 게 없다.

## keyof와 같이 쓰는 형태

`값 satisfies keyof T` 단독보다 아래 두 가지 형태로 더 자주 만난다.

`satisfies Record<keyof T, ...>`는 라벨 맵이나 핸들러 맵에서 키 누락을 막는다.

```ts
const USER_LABELS = { name: "이름", age: "나이", email: null }
  satisfies Record<keyof User, string | null>;

USER_LABELS.name.toUpperCase(); // OK, name은 string으로 남음
```

애너테이션으로 같은 타입을 붙이면 `name`까지 `string | null`이 되어 `'USER_LABELS.name' is possibly 'null'` 오류가 난다. 키가 빠졌을 때 오류가 나는 건 두 방식이 같고, 값 타입이 좁게 남는 건 satisfies뿐이다.

`satisfies (keyof T)[]`는 테이블 컬럼이나 정렬 필드처럼 키 목록을 고정할 때 쓴다.

```ts
const COLUMNS = ["name", "age"] as const satisfies readonly (keyof User)[];
// typeof COLUMNS === readonly ["name", "age"]
```

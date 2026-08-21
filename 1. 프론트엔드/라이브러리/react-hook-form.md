# React Hook Form(RHF)
DOM/ref와 내부 store가 입력값을 들고, 필요한 곳만 subscribe해서 React 렌더를 일으키는 **uncontrolled-first** 방식
> React Hook Form 공식 문서 : [React Hook Form](https://react-hook-form.com/)

> 💡 useState vs react-hook-form 
> - useState의 경우 state가 입력값의 SOT인 **controlled** 방식
> - useState는 로그인, 검색창 처럼 필드가 적은 경우에만 사용
>   - 단, 옵션이 많은 검색창/필터라면 RHF 써도 됨
>   - HTML 관점에서도 `<search>` 는 검색/필터링 기능을 수행하는 `<form>` 컨트롤 영역을 나타내는 용도
>   - [search MDN 참고](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/search)
>   - 단, RHF를 “필터 UI의 임시 입력 상태 관리”로 쓰고, 최종 필터 상태는 URL search params나 query key와 동기화하는 구조로 사용해야함
>   - [Query Params syncronization 참고](https://github.com/orgs/react-hook-form/discussions/8126)
> - react-hook-form 이점을 많이 살리려면 Input같은 비제어 컴포넌트는 uncontrolled 방식으로 사용

## 사용 방법

- Input같은 비제어 컴포넌트는 uncontrolled 방식으로 사용하는 것이 효율적
- 폼이 길어도 하나에 정의하는 것이 효율적
  - **watch() 최적화**: 한 번만 호출하여 전체 폼 값 추적(분리된 각 섹션마다 호출시 비효율적)
  - **리렌더링 최소화**: 불필요한 컴포넌트 분리로 인한 추가 리렌더링 방지
  - **메모리 효율성**: 단일 컴포넌트로 메모리 사용량 최적화
  - **상태 관리 단순화**: 복잡한 props 전달과 상태 동기화 불필요(오버헤드 발생)

## 1. useForm

최소한의 재렌더링으로 form을 검증하고 손쉽게 관리할 수 있는 커스텀 훅

### 1.1. 기본적인 사용방법

```tsx
import { useForm, SubmitHandler } from "react-hook-form"

/* 폼 인터페이스 정의 */
interface FormInput {
  firstName: string
  lastName: string
  age: number
}

export default function App() {

  /* useForm, SubmitHandler에 인터페이스 추가 */
  const { register, handleSubmit } = useForm<FormInput>()
  const onSubmit: SubmitHandler<FormInput> = (data) => console.log(data)

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("firstName", { required: true, maxLength: 20 })} />
      <input {...register("lastName", { pattern: /^[A-Za-z]+$/i })} />
      <input type="number" {...register("age", { min: 18, max: 99 })} />
      <input type="submit" />
    </form>
  )
}
```
> 💡 zod를 안쓰는 경우 아래 기능들 추가 검토
> - required, min, max, minLength, maxLength, pattern, validate 등 체크 가능
> - 타입스크립트 사용시 인터페이스(ex. FormInput) 정의해놓으면 타입체크도 가능

> 💡 register 사용시 숫자를 사용하고 싶은 경우 주의사항
> ```tsx
> {...register('totalScore', {
>    setValueAs: (value) => Number(value || 0),
> })}
> ```
> HTML input은 RHF register로 받으면 기본 값이 문자열이기 때문에 서버에 숫자로 보내야하는 경우 변환 필요. (가급적이면 서버에 보낼때도 문자로)

### 1.2. 커스텀 컴포넌트 통합

```tsx
import Select from "react-select"
import { useForm, Controller, SubmitHandler } from "react-hook-form"
import { Input } from "@material-ui/core"

interface FormInput {
  firstName: string
  lastName: string
  iceCreamType: { label: string; value: string }
}

const App = () => {
  const { control, handleSubmit } = useForm({
    defaultValues: {
      firstName: "",
      lastName: "",
      iceCreamType: {},
    },
  })

  const onSubmit: SubmitHandler<FormInput> = (data) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="firstName"
        control={control}
        render={({ field }) => <Input {...field} />}
      />
      <Controller
        name="iceCreamType"
        control={control}
        render={({ field }) => (
          <Select
            {...field}
            options={[
              { value: "chocolate", label: "Chocolate" },
              { value: "strawberry", label: "Strawberry" },
              { value: "vanilla", label: "Vanilla" },
            ]}
          />
        )}
      />
      <input type="submit" />
    </form>
  )
}
```

### 1.3. 에러처리

```tsx
import { useForm } from "react-hook-form"

export default function App() {
  const {
    register,
    formState: { errors }, /* 에러 상태 확인 */
    handleSubmit,
  } = useForm()
  const onSubmit = (data) => console.log(data)

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("firstName", { required: true })}
        aria-invalid={errors.firstName ? "true" : "false"}
      />
      {errors.firstName?.type === "required" && (
        <p role="alert">First name is required</p>
      )}

      <input
        {...register("mail", { required: "Email Address is required" })}
        aria-invalid={errors.mail ? "true" : "false"}
      />
      {errors.mail && <p role="alert">{errors.mail.message}</p>}

      <input type="submit" />
    </form>
  )
}
```

### 1.4. API 통신

#### API 함수 호출

```tsx
import { useForm } from "react-hook-form"

type FormData = {
  firstName: string
  lastName: string
}

function App() {
  const { register, handleSubmit } = useForm<FormData>()

  const onSubmit = async (data: FormData) => {
    try {
      const response = await fetch("/api/save", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("accessToken")}`, // 토큰 추가
        },
        body: JSON.stringify(data),
      })

      if (!response.ok) throw new Error("Request failed")
      alert("Your application is updated.")
    } catch (error) {
      alert("Submission has failed.")
    }
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("firstName", { required: true })} />
      <input {...register("lastName", { required: true })} />
      <button type="submit">Submit</button>
    </form>
  )
}
```

#### Form 컴포넌트 사용

```tsx
import { Form } from "react-hook-form";

function App() {
  const { register, control } = useForm();

  return (
    <Form
      action="/api/save" // Send post request with the FormData
      // encType={'application/json'} you can also switch to json object
      headers={{
        "Content-Type": "application/json",
         Authorization: `Bearer ${localStorage.getItem("accessToken")}`
      }}
      control={control}
      onSuccess={() => alert("Your application is updated.")}
      onError={() => alert("Submission has failed.")}
    >
      <input {...register("firstName", { required: true })} />
      <input {...register("lastName", { required: true })} />
      <button>Submit</button>
    </Form>
  );
}
```

### 1.5. getValues, setValue

#### **getValues**

form value값들을 읽는 가장 최적화된 함수

<aside>
💡

watch랑 다른 점은 리렌더링을 유발하지 않고 input 변화를 구독하지 않음.

</aside>

예시)

```tsx
import { useForm } from "react-hook-form"

type FormInputs = {
  test: string
  test1: string
}

export default function App() {
  const { register, getValues } = useForm<FormInputs>()

  return (
    <form>
      <input {...register("test")} />
      <input {...register("test1")} />

      <button
        type="button"
        onClick={() => {
          const values = getValues() // { test: "test-input", test1: "test1-input" }
          const singleValue = getValues("test") // "test-input"
          const multipleValues = getValues(["test", "test1"]) // ["test-input", "test1-input"]
        }}
      >
        Get Values
      </button>
    </form>
  )
}
```

### 1.6. 그 외

- MUI, ANTD 등 다른 라이브러리 컴포넌트 사용시 제약있을 수 있음. 그에 따라 Controller등 다른 훅 사용해야함.
- 상태관리 필요없지만 사용중인 상태관리와 통합할 수 있음.
- ZOD등의 스키마 validation 체크 도구와 통합 가능.

## 2. useController

Controller를 구동하는 커스텀 훅

- 재사용 가능한 Controlled 입력을 만드는 데 유용
- Controller를 사용해도 되고 useController를 사용해도됨.

## 3. useWatch

폼 필드의 값을 효율적으로 구독(subscribe)하고 그 변화에 따른 리렌더링을 **특정 컴포넌트 수준에서만** 발생시키도록 설계된 API

- "입력할 때마다 UI가 바뀌어야 한다" 같은 실시간 반응이 필요하다면, `watch` 또는 `useWatch`를 써야됨.
- Watch API와 비슷하게 동작하지만, 이를 통해 커스텀 hook 수준에서 다시 렌더링을 분리하고 잠재적으로 애플리케이션의 성능이 향상될 수 있습니다.

<aside>
💡

watch vs useWatch

- watch : useForm에서 제공하는 메서드로 현재 폼의 모든 필드값을 객체로 반환
- useWatch : 특정 필드만 선택적으로 감시 `useWatch({ name: 'fieldName' })`

필드 하나만 감시하는거면 useWatch가 좋고 여러 필드를 동시 참조해야하면 watch가 좋음.
(useWatch를 여러 번 사용하는 것보다 watch() 한 번으로 모든 값을 가져오는 것이 효율적)
</aside>

## 4. useFieldArray

동적 폼 필드(예: 배열 형태의 입력)를 쉽게 다룰 수 있도록 도와주는 기능

- ex. 추가(append), 삭제(remove)
- 복잡한 폼에서 반복되는 입력 필드 관리, 추가, 삭제 및 유효성 검사 등을 간결하고 효율적으로 구현할 수 있어 UX 개선 및 성능 향상에 유리

```tsx
function FieldArray() {
  const { control, register } = useForm();
  const { fields, append, prepend, remove, swap, move, insert } = useFieldArray({
    control, // control props comes from useForm (optional: if you are using FormProvider)
    name: "test", // unique name for your Field Array
  });

  return (
    {fields.map((field, index) => (
      <input
        key={field.id} // important to include key with field's id
        {...register(`test.${index}.value`)} 
      />
    ))}
  );
}
```
- `fields`: 렌더링 가능한 필드 배열 (각 항목에 고유한 id 포함)
- `append`, `remove`: 필드 추가 및 삭제 함수
- `register`: 각 필드에 등록

## shouldUnregister
컴포넌트가 unmount될 때 해당 필드를 폼 상태에서 제거할지 결정하는 옵션
> 특정 조건에 따라 필드를 보여주거나 숨김(ex. 드롭다운 '기타' 추가 입력란)

기본값 : false (true : 언마운트된 필드는 폼에서 완전히 사라지고 검증 대상에서도 제외)

### Zod 연동시 주의사항
React Hook Form과 Zod는 서로 다른 기준으로 동작

- React Hook Form : 언마운트된 필드를 실제 폼 상태(state)에서 제거
- Zod : 검증할 때 정의된 스키마 전체를 기준으로 동작

=> 즉, shouldUnregister를 true로 설정해놓아도 검증 시도

### Zod 연동시 해결 방법
1. discriminatedUnion (추천)
- 타입 안전성이 확실히 보장됨
- 스키마만 봐도 어떤 조건에서 어떤 필드가 필요한지 명확함
- 서버 스펙과 일치하여 디버깅이 쉬움

```ts
const schema = z.discriminatedUnion('type', [
    // type이 'basic'일 때
    z.object({
        type: z.literal('basic'),
        // extra 필드 자체가 스키마에 없음
    }),

    // type이 'extra'일 때
    z.object({
        type: z.literal('extra'),
        extra: z.string().min(1, '필수 입력입니다'),
    }),
]);

const form = useForm({
    resolver: zodResolver(schema),
    shouldUnregister: true,
    defaultValues: {
        type: 'basic',
        extra: '', // 기본값은 넣어도 됨
    }
});
```
type이 basic일때는 extra 필드가 아예 스키마에 존재하지 않음.

2. refine/superRefine
- 정말 복잡한 다중 필드 의존성이 있는 경우
- 식별자 필드를 만들기 어려운 특수한 상황

## disabled
필드를 비활성화하면서 **폼 상태에서도 빼는** 옵션. `Controller`의 `disabled` prop, `register('name', { disabled: true })`, `useForm({ disabled: true })`(폼 전체)

> ⚠️ **resolver(zod)를 쓰면 검증을 건너뛰지 않는다.** disabled는 제출 데이터에서 필드를 빼주지만, 검증은 스키마 전체를 기준으로 그대로 돌아간다. `shouldUnregister`와 완전히 같은 원인 — RHF는 필드 상태를, zod는 스키마 전체를 기준으로 동작한다.

실측 (rhf 7.72.0 / @hookform/resolvers 5.2.2 / zod 4.3.6)

| 상태 | zod 검증 | onValid에 들어오는 값 |
|---|---|---|
| 일반 | 오류 발생 | `{ detail: '', name: '이름' }` |
| `disabled` | **오류 그대로 발생** | `{ name: '이름' }` — 필드가 사라짐 |

### 여기서 나오는 함정 2개

**1. 조건부 필수를 disabled로 우회할 수 없다**

"이 필드가 비활성일 때는 필수 아님"을 disabled로 표현하면, 비활성 상태에서도 검증에 걸려 **저장이 아예 불가능**해진다. 조건은 스키마 안에서 쓴다.

```ts
/* 선택 가능한 하위 항목이 있는 구분만 필수 */
const schema = z
  .object({
    category: z.enum(['A', 'B', 'ETC']),
    detail: z.union([z.literal(''), z.enum(['A1', 'A2', 'B1'])]),
  })
  .refine(
    ({ category, detail }) => DETAILS_BY_CATEGORY[category].length === 0 || detail !== '',
    { message: '세부 항목을 선택해 주세요.', path: ['detail'] }
  )
```

> 💡 목록(매핑)이 이미 있으면 `'ETC'` 같은 리터럴을 또 적지 말고 **매핑의 length로 파생**시킨다. 조건이 한 곳에만 존재하게 되고, 하위 항목 없는 구분이 추가돼도 자동으로 따라온다.

`discriminatedUnion`으로도 되지만 값 조합이 많으면 스키마가 폭발하므로, 위처럼 매핑이 있는 경우엔 `refine`/`superRefine`이 짧다. (→ `shouldUnregister` 절의 해결 방법 참고)

**2. 제출 데이터에서 필드가 사라진다**

폼 값을 읽어 요청 객체를 만드는 변환 함수가 그 값을 못 받는다. 값은 서버로 보내야 하는데 입력만 막고 싶다면 `disabled` 대신 읽기 전용 처리를 쓰거나, 변환 단계에서 `null`로 바꾼다.

```ts
const toRequest = (values: FormValues) => ({
  category: values.category,
  /* 비활성 조건을 요청 변환에서 다시 명시 */
  detail: DETAILS_BY_CATEGORY[values.category].length === 0 ? null : values.detail || null,
})
```

## mode
검증을 **언제** 돌릴지 결정하는 `useForm` 옵션. 기본값은 `'onSubmit'`

| mode | 검증 시점 |
|---|---|
| `onSubmit` (기본) | 제출할 때 |
| `onChange` | 값이 바뀔 때마다 |
| `onBlur` | 포커스가 빠질 때 |
| `onTouched` | 첫 blur 이후부터 change마다 |
| `all` | blur + change |

- `reValidateMode`(기본 `'onChange'`)는 **제출 이후**의 재검증 시점이다. `mode: 'onSubmit'`이어도 한 번 제출한 뒤에는 값을 고치는 즉시 오류 문구가 갱신·해제된다.
- 그래서 "제출했을 때 문구를 보여주고, 고치면 바로 사라진다"는 흐름은 `mode: 'onSubmit'` + `reValidateMode` 기본값 조합이 그대로 만들어 준다. 별도 처리 필요 없음.

### isValid로 제출 버튼을 막을 때
`formState.isValid`는 **mode와 무관하게** RHF가 resolver를 돌려 동기화한다. 실측하면 초기 1렌더는 `false`였다가 마운트 중 실제 값으로 바뀐다.

```
[onSubmit] isValid=false  ← 초기 1렌더
[onSubmit] isValid=true   ← 마운트 중 (아직 아무 조작 없음)
```

- 즉 `mode: 'onSubmit'`에서도 `isValid` 게이트는 **기술적으로는 동작한다.**
- 다만 UX가 충돌한다. 버튼을 `!isValid`로 막으면 제출 자체가 불가능하니 **"제출 시 오류 문구"가 뜰 기회가 없다.** 사용자는 왜 못 누르는지 모른 채 멈춘다.
- 둘 중 하나만 고른다.
  - **제출 시 문구** : `mode: 'onSubmit'` + 버튼은 진행 상태(제출 중, 업로드 중, 변경 없음)로만 막기
  - **즉시 차단** : `isValid` 게이트를 쓰되 왜 못 누르는지 별도로 안내
- 초기 1렌더가 `false`라 `isValid` 게이트는 마운트 시 버튼이 잠깐 비활성→활성으로 깜빡인다.

## reset
폼 값을 되돌리는 메서드. **인자를 넘기느냐에 따라 내부 동작이 갈린다.**

| 호출 | native `form.reset()` | 등록된 필드 참조(`_fields`) |
|---|---|---|
| `reset()` | 실행 → 비제어 입력의 DOM 값도 비워짐 | 버림 |
| `reset(values)` | **실행 안 함** | 버림 |
| `reset(values, { keepFieldsRef: true })` | 실행 안 함 | **유지** (필드마다 `setValue`) |

### 함정 — reset(값) 뒤에 비제어 입력이 죽는다

증상이 두 개로 나타난다.

1. 초기화해도 입력창에 이전 글자가 그대로 남는다
2. 초기화 **이후** 입력한 값이 제출 데이터에 실리지 않는다

원인은 `_reset` 내부의 `_fields = {}`다. 등록된 필드 참조를 버리는데, 값을 넘긴 호출은 native `form.reset()` 경로도 타지 않아 DOM 값을 비울 수단이 없다. 참조가 끊긴 뒤 입력하면 RHF의 `onChange`가 `_fields`에서 필드를 못 찾고 그냥 빠져나가므로 `_formValues`가 갱신되지 않는다.

`Controller`(제어) 필드는 값이 폼 상태에서 내려오므로 영향이 없다. **비제어(`register`)만 깨진다.** 그래서 같은 폼 안에서 한 필드만 조용히 죽어 원인을 찾기 어렵다.

해결은 `keepFieldsRef: true`다. `KeepStateOptions`의 공식 필드이고(`types/form.d.ts`), RHF 자신도 `useForm`의 `values` 옵션 동기화 경로에서 이 옵션을 쓴다.

```tsx
reset(nextDefaultValues, { keepFieldsRef: true })
```

> 💡 인자 없는 `reset()`으로도 해결되지만 되돌아가는 값이 `_defaultValues`다. "진입 시 조건"과 "빈 조건"이 달라야 하는 화면(딥링크로 조건을 받고 들어오는 필터 등)에서는 초기화가 딥링크 값을 복원해버리므로 쓸 수 없다.

### 성능 — Controller로 바꿔도 되지만 입력마다 리렌더한다

같은 버그를 "그 필드도 `Controller`로 바꾼다"로 고칠 수도 있다. 어느 쪽이 싼지 DS Input을 감싸 렌더 횟수를 셌다.

실측 (rhf 7.72.0 / react 19.2.4, 6글자 입력 후 초기화 클릭)

| | 마운트 | 6글자 입력 중 | 초기화 클릭 |
|---|---|---|---|
| `register` + `keepFieldsRef` | parent 2 / input 2 | **+0 / +0** | +1 / +1 |
| `Controller` | parent 2 / input 2 | +0 / **+6** | +1 / +1 |

- **입력 중에만 갈린다.** `Controller`는 내부 `useWatch`가 값 변경을 구독하므로 키 입력 1회당 그 필드 서브트리 1회 리렌더. `register`는 값이 DOM으로 직접 들어가고 `_formValues`만 갱신되므로 0회.
- **부모 폼은 두 방식 모두 리렌더되지 않는다**(+0). `Controller`의 비용은 그 필드 서브트리에 갇힌다 — "제어로 바꾸면 폼 전체가 다시 그려진다"가 아니다.
- **초기화는 동률**(+1/+1). `keepFieldsRef`가 `_fields = {}` 대신 필드별 `setValue`를 돌지만 클릭 1회의 동기 루프이고 리렌더 횟수는 늘지 않는다.
- 성능만 보면 `keepFieldsRef`가 우세하다. 핫 패스(입력)에서 0, 콜드 패스(초기화)에서 동률이라 우열이 뒤집히는 지점이 없다.

다만 체감 차이는 작다. 입력 1회당 Input 하나 리렌더는 1ms 미만이라 필드 10개 수준의 검색 박스에서 사용자가 느낄 수준은 아니다. 그래서 판단 기준은 성능보다 이쪽이 크다.

| | 유리한 상황 |
|---|---|
| `register` + `keepFieldsRef` | 비제어 모델을 유지하고 싶을 때, 필드가 많거나 입력 컴포넌트가 무거울 때 |
| `Controller` | 그 필드에 검증·에러 표시를 붙일 때, 비제어 DOM 값이 폼 상태와 어긋나는 문제 계열 자체를 피하고 싶을 때 |

"비제어니까 무조건 빠르다"보다 **비용이 어느 경로에 붙는지**(입력 vs 클릭)로 보는 편이 정확하다.

## 참고자료
[왜 shouldUnregister: true인데 검증 에러가 발생할까?](https://toby2009.tistory.com/83#shouldUnregister%EB%8A%94%20%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%3F-1-1)
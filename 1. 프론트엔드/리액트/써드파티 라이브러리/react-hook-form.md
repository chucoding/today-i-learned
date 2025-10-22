# React Hook Form
불필요한 재렌더링을 제거하는 동시에 작성해야 하는 코드 양을 줄여주는 라이브러리

https://react-hook-form.com/

## 특징
- 전체 폼을 다시 렌더링하지 않고도 개별 입력 및 폼 상태 업데이트를 구독
- 빠른 마운트
- 상태관리에 의존하지 않아도됨.

## 사용방법

- features/ui에 정의
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
interface IFormInput {
  firstName: string
  lastName: string
  age: number
}

export default function App() {

  /* useForm, SubmitHandler에 인터페이스 추가 */
  const { register, handleSubmit } = useForm<IFormInput>()
  const onSubmit: SubmitHandler<IFormInput> = (data) => console.log(data)

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

- required, min, max, minLength, maxLength, pattern, validate 등 체크 가능
- 타입스크립트 사용시 인터페이스(ex. IFormInput) 정의해놓으면 타입체크도 가능

### 1.2. 커스텀 컴포넌트 통합

```tsx
import Select from "react-select"
import { useForm, Controller, SubmitHandler } from "react-hook-form"
import { Input } from "@material-ui/core"

interface IFormInput {
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

  const onSubmit: SubmitHandler<IFormInput> = (data) => {
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
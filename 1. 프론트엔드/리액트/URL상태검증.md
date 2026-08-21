# URL 쿼리스트링을 상태로 쓸 때 어디까지 검증할까
검색 필터를 쿼리스트링에 담으면 딥링크·북마크·뒤로가기가 공짜로 생기지만, 그 값은 사용자가 주소창에서 직접 고칠 수 있음.
그래서 "입력창에서 막은 값이 URL로 들어오면 어떻게 하나"라는 질문이 남음.

## 1. 노멀라이즈는 검증이 아니다
브라우저가 주소창에서 퍼센트 인코딩하는 것은 전송용 인코딩이라 필터링 효과가 없음.
`URLSearchParams`가 그대로 디코딩해서 원래 문자열을 돌려줌.

```js
const encoded = 'registrantId=' + encodeURIComponent('홍길동!@# 🙂')
// 'registrantId=%ED%99%8D%EA%B8%B8%EB%8F%99!%40%23%20%F0%9F%99%82'

new URLSearchParams(encoded).get('registrantId')
// '홍길동!@# 🙂'  ← 직접 입력한 값과 동일
```

"URL은 인코딩되니까 이상한 값은 못 들어온다"는 잘못된 전제.

## 2. 쿼리스트링은 사용자 입력으로 취급
TanStack Router 문서는 search param을 **user-facing raw-text input** 으로 규정하고, 앱이 소비하기 전에 검증해 신뢰할 수 있는 형태로 만들라고 함.
nuqs도 파서 단계에서 검증하는 구조. 검증을 강조하는 이유는 세 가지.

| 이유 | 설명 |
|---|---|
| 타입 | URL 값은 항상 문자열이라 숫자·불리언·날짜로 파싱이 필요 |
| 깨진 UI 상태 | 유효하지 않은 조합이 화면에 적용돼 빈 목록·없는 탭 같은 상태가 만들어짐 |
| 주입 | XSS·오픈 리다이렉트·프로토타입 오염의 입구가 됨 |

## 3. 검증 대상은 대체로 "유효값 집합이 정해진 것"
관행이 검증을 요구하는 것은 enum·숫자·날짜처럼 **허용값을 열거할 수 있는 값**.
자유 입력 검색어(이름·키워드)는 성격이 다름 — 그냥 문자열이고, 서버에 넘겨 0건을 받는 것이 정상 동작.

| 파라미터 종류 | 처리 |
|---|---|
| `status=PENDING` 같은 enum | 알려진 값만 통과, 나머지는 기본값 |
| `page=3`, `date=2026-08-21` | 파싱 실패 시 기본값 |
| `keyword=홍길동` 같은 자유 입력 | 그대로 통과. 결과는 서버가 판단 |

## 4. 유효하지 않을 때 — 막기보다 되돌리기
에러로 튕기는 게 아니라 **조용히 기본값으로 폴백**하는 것이 기본.

TanStack Router는 zod `.catch()`를 권하며 이렇게 씀.
> we firmly believe that if a search parameter is malformed, you probably don't want to halt the user's experience through the app to show a big fat error message.

`.default()`를 쓰면 malformed 값에서 검증이 실패해 에러가 나고 `errorComponent`가 렌더됨(`error.routerCode = VALIDATE_SEARCH`). 문서는 이걸 "에러를 **보여주고 싶을 때**"의 선택지로 둠.

nuqs도 같은 방향. 파싱 실패 시 기본값(없으면 `null`)을 반환하는 것이 기본 동작이고, 에러를 던지는 `{ strict: true }`는 서버 사이드 로더용 옵션으로 분리.

```js
// TanStack Router — 폴백(권장)
z.object({ page: z.number().catch(1) })

// TanStack Router — 에러 화면을 띄우고 싶을 때
z.object({ page: z.number().default(1) })
```

## 5. 클라이언트 검증은 보안 방어선이 아니다
OWASP 계열 자료가 일관되게 말하는 것 — 클라이언트 검증은 defense in depth의 **편의 계층**이고 실제 방어선은 서버.
devtools·프록시·curl로 우회되므로, 클라이언트에서 URL을 걸러도 보안상 얻는 건 없음.
뒤집으면 **URL 검증을 생략해도 보안 구멍이 생기지는 않는다**는 뜻(서버가 검증한다는 전제 아래).

## 6. 실제 판단 (통합업무계획 등록자 ID 필터)
로그인 ID 검색 필터라 영문·숫자만 허용해야 하는 요구사항. 두 지점에서 걸러낼 수 있었음.

- **입력창**: 제한함. 타이핑·붙여넣기 단계에서 허용 문자만 남김
- **URL 복원**: 제한하지 않음. 자유 입력 검색어이고 조회 결과는 서버가 판단

URL로 한글이 들어오면 입력란에 타이핑으로 만들 수 없는 값이 잠깐 표시되지만, 그 칸에 한 글자만 입력해도 **값 전체가 필터를 타서** 정리됨(`홍길동` + `a` → `a`). 초기화 버튼으로도 사라지는 일시적 상태라 굳이 막지 않기로 함.

정리하면 — enum·숫자는 URL에서도 검증하고, 자유 입력은 통과시키고, 어느 쪽이든 에러 화면은 띄우지 않음.

## 참고: 입력 단계에서 값을 걸러내는 방법
비제어 input에서는 `onChange`에서 `event.target.value`를 고쳐 쓰면 화면 표시값까지 함께 정리됨.
react-hook-form의 `register`와 함께 쓸 때는 `register`가 만든 `onChange`를 감싸야 폼 상태에도 걸러진 값이 들어감.

```jsx
const field = register('registrantId')

<input
  {...field}
  onChange={(event) => {
    event.target.value = event.target.value.replace(/[^a-zA-Z0-9]/g, '')
    void field.onChange(event)
  }}
/>
```

`setValueAs` 같은 변환 옵션은 폼에 저장되는 값만 바꾸므로 입력란에는 원래 문자가 그대로 보임 → 입력 차단 용도로는 부적합.

한글 IME는 조합 중(`isComposing`)에도 input 이벤트가 발생하므로, 값 재작성으로 조합 문자를 막을 때는 실제 브라우저에서 잔상이 남지 않는지 확인이 필요함.

# 참고자료
- https://tanstack.com/router/latest/docs/framework/react/guide/search-params
- https://tanstack.com/router/latest/docs/how-to/validate-search-params
- https://nuqs.dev/docs/parsers/built-in
- https://nuqs.dev/docs/server-side
- https://andrewwhite.gitbooks.io/owasp/content/03-Build/0x06-InputValidation.html
- https://www.packetlabs.net/posts/input-validation/

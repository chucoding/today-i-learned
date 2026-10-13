# Select value

Select 컴포넌트 value 타입 설계

- [라이브러리별 value 타입](#라이브러리별-value-타입)
- [여러 값을 담아야 할 때](#여러-값을-담아야-할-때)
- [key 조회 방식을 쓰는 이유](#key-조회-방식을-쓰는-이유)
- [참고자료](#참고자료)

## 라이브러리별 value 타입

| 범위 | 라이브러리 | 특징 |
| --- | --- | --- |
| `string`만 | Radix(shadcn/ui), Chakra v3 | Radix는 `""`가 선택 해제용이라 Item value로 못 씀 |
| 원시값 | React Aria, Mantine, Ant Design | 숫자 ID를 변환 없이 사용 |
| 객체 포함 | MUI, Headless UI, Base UI | 비교 함수(`by`, `isItemEqualToValue`)와 문자열 변환 함수 필요 |

- 객체 value는 참조 비교가 기본이라, 재조회로 참조가 바뀌면 선택이 풀림
- "선택 없음"은 `""`보다 `null`로 표현하는 편이 명확함

## 여러 값을 담아야 할 때

- `"apartment:123"`처럼 콜론으로 합치는 방식은 문자열만 받는 라이브러리에서 쓰는 우회책
  - 값 안에 `:`가 있으면 split 결과가 깨짐
  - 꺼낸 값이 모두 문자열이라 다시 타입 변환 필요
- 대안은 value에 고유 key만 담고 원본 객체를 `Map`에서 조회하는 방식

```ts
const optionMap = new Map(options.map((option) => [option.key, option]));
const handleChange = (key: string) => {
  const option = optionMap.get(key); // split 없이 원본 타입 그대로 사용
};
```

## key 조회 방식을 쓰는 이유

- key를 다시 쪼개지 않으므로 구분자 충돌과 타입 손실이 없음
- 원시값 `===` 비교라 재조회 후에도 선택 유지
- URL 쿼리, FormData, 폼 기본값에 그대로 들어감
- 컴포넌트가 제네릭, 비교 함수, 변환 함수를 받을 필요가 없어 API가 작아짐
- key가 고유해야 하고, URL에서 들어온 key는 조회 결과가 `undefined`일 수 있음

## 참고자료

| TITLE | URL |
| --- | --- |
| Radix의 string 전용 value | https://www.radix-ui.com/primitives/docs/components/select |
| Radix 빈 문자열 Item 에러와 대체값 우회 | https://forum.codewithmosh.com/t/a-select-item-must-have-a-value-prop-that-is-not-an-empty-string-this-is-because-the-select-value-can-be-set-to-an-empty-string-to-clear-the-selection-and-show-the-placeholder/23078 |
| Chakra의 키 배열 value | https://chakra-ui.com/docs/components/select |
| React Aria의 id 기반 value | https://react-aria.adobe.com/Select |
| Mantine의 원시값 value | https://mantine.dev/core/select/ |
| Ant Design의 labelInValue | https://ant.design/components/select |
| MUI 객체 value의 참조 동일성 조건 | https://mui.com/material-ui/api/select/ |
| Headless UI의 by 비교 | https://headlessui.com/react/listbox |
| Base UI의 객체 value와 변환 함수 | https://base-ui.com/react/components/select |

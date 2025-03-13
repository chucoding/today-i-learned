# Stack

## 소개

flexbox를 더 쉽게 사용할 수 있도록 해주는 레이아웃 컴포넌트
- 복잡한 스타일링을 간단하게 처리 가능

## 사용법

```tsx
<Stack direction="row" spacing={2}>
  <Box>1</Box>
  <Box>2</Box>
  <Box>3</Box>
</Stack>
```
- direction : flex-direction (row:가로 방향, column:세로 방향)
- spacing : 자식 요소들 사이의 간격

> 💡 주의사항 : 스타일 오류 발생시 Stack 자식으로 React.Fragment나 빈 태그 사용하고 있지 않은지 체크
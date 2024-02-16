# CSS

# Overflow
오버플로우 처리방법 정리
### word-break;
텍스트가 박스 밖으로 오버플로 할 때 줄을 바꿀 지 지정.  
💡 부트스트랩 사용시 기본 스타일로 normal이 지정되있기 때문에 커스텀 필요

```css
word-break: normal;      /* Default : 한글O, 영어,숫자X */ 
word-break: break-all;   /* 오버플로 방지 : 무조건 width에 따라 줄 바꿈(한,중,일 텍스트 제외) */
word-break: keep-all;    /* 오버플로 허용 : 줄 바꿀 때 단어 안끊음. */
word-break: break-word;  /* Deprecated */
```

# Flexbox
행과 열 형태의 요소를 일차원적으로 배치하는 레이아웃 구성 방법
- float, position으로 어렵게 구성하던 레이아웃을 단순하게 작업 가능해짐

```css
display: flex;
```
### flex-direction
row, column 중 선택하여 해당 축을 기준으로 일렬 배치
```css
flex-direction: row; /* default : row (가로로 배치)*/
```

### Wrapping
레이아웃에 고정된 너비나 높이가 있을 때 Flexbox 자식이 컨테이너를 오버플로 하는 경우 사용  
TODO 공부.

# Text-center
글자 가운데로 오게 하는방법 정리

### p태그
p태그에 height가 잡혀있는 경우
```css
line-height: 크기조정;
```
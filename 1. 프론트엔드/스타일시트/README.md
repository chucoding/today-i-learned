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

### flex 속성
- 부모 요소(Conteiner)
    - flex-direction, flex-wrap, justify-content, align-items, align-content
- 자식 요소 item
    - flex, flex-grow, flex-shrink, flex-basis, align-self, order
### flex-direction
row, column 중 선택하여 해당 축을 기준으로 일렬 배치(아이템의 정렬 방향을 설정)
```css
flex-direction: row; /* default : row (가로로 배치)*/
```
### align-content
아이템의 상하관계 정렬 상태를 설정

```css
align-content:flex-start;
align-content:flex-end;
align-content:center;
align-content:space-between;
align-content:space-around;
align-content:stretch; /* default */
```

### align-items
아이템의 내부 상하관계 정렬 상태를 설정

```css
align-items:flex-start;
align-items:flex-end;
align-items:center;
align-items:baseline;
align-items:stretch; /* default */
```

### Wrapping
레이아웃에 고정된 너비나 높이가 있을 때 Flexbox 자식이 컨테이너를 오버플로 하는 경우 사용  
TODO 공부.

# Text-center
글자 가운데로 오게 하는방법 정리

### 작은 레이아웃
line-height, text-align 사용
```css
height: 100px; 
line-height: 50px; 
text-align: center;
```

### 복잡한 레이아웃
flexbox이용 (텍스트의 길이나 높이에 덜 민감)
```css
display: flex;
align-items: center; /* main-axis의 cross-axis를 따라 정렬되는 방식 */
justify-content: center; /* main-axis의 cross-axis를 따라 정렬되는 방식 */
```

> ❗ justify-content와 overflow  
> justify-content: center; 지정시 overflow했을때 왼쪽 화면이 잘릴 수 있음.  
> 이 때는 justify-content: safe center를 사용하면 된다.(단, safari 지원안함.)
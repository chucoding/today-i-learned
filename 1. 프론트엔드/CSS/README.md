# CSS

# position
- absolute : 부모 요소면서 조상 요소 중 position속성이 static이 아닌 요소 기준
- fixed : 뷰 포트(브라우저 창) 기준

# Flexbox
행과 열 형태의 요소를 일차원적으로 배치하는 레이아웃 구성 방법
- float, position으로 어렵게 구성하던 레이아웃을 단순하게 작업 가능해짐

```css
display: flex;
```

### flex 속성
- 부모 요소(Conteiner)
    - flex-direction
    - flex-wrap
    - justify-content
    - align-items
    - align-content
- 자식 요소 item
    - flex
    - flex-grow
    - flex-shrink
    - flex-basis
    - align-self
    - order
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



> ❗ justify-content와 overflow  
> justify-content: center; 지정시 overflow했을때 왼쪽 화면이 잘릴 수 있음.  
> 이 때는 justify-content: safe center를 사용하면 된다.(단, safari 지원안함.)

# diabled
disabled 속성을 사용하면 cursor와 title 속성들을 사용할 수 없다. 대신, 아래 속성들을 이용
- style={{opacity : 0.5, cursor : not-allowed}}
- title=사용할 수 없습니다.
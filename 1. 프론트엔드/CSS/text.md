# 텍스트 디자인

## Overflow

### word-break;
주로 CJK(한중일) 텍스트의 줄바꿈 규칙을 제어

```css
word-break: normal;      /* Default : 한글O, 영어,숫자X */ 
word-break: break-all;   /* 오버플로 방지 : 무조건 width에 따라 줄 바꿈(한,중,일 텍스트 제외) */
word-break: keep-all;    /* 오버플로 허용 : 줄 바꿀 때 단어 안끊음. */
word-break: break-word;  /* Deprecated */
```

### overflow-wrap
긴 단어나 URL이 컨테이너를 벗어날 때의 처리를 제어   
CSS3 표준에 따르면 wordWrap -> overflowWrap으로 변경(wordWrap은 레거시 속성)
```css
overflow-wrap: normal;     /* 한중일 텍스트의 줄바꿈 규칙 제어 */
overflow-wrap: break-word; /* 긴 단어(URL 등)가 컨테이너를 벗어날 때 줄바꿈 */
```

> 💡 word-break과 overflow-wrap을 같이 쓸 수 있음.  
> - `overflowWrap: break-word` :  컨테이너를 벗어나는 긴 단어를 자연스럽게 줄바꿈 처리하는데 가장 적합
> - `word-break: keep-all` : 한글 텍스트를 다룰 때 단어 중간에서 줄바꿈이 발생하는 것을 방지


## 글자 가운데 정렬
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
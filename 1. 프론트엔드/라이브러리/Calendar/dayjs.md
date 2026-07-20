# dayjs
Moment.js와 대부분 호환되는 API를 사용하여 최신 브라우저에서 날짜와 시간을 구문 분석, 유효성 검사, 조작 및 표시하는 최소한의 JavaScript 라이브러리
(Moment.js와 동일한 최신 API를 제공하는 빠르고 간편한 2kB 대체 솔루션)

| dayjs 공식문서 : [dayjs](https://github.com/iamkun/dayjs)

## 설치
```shell
npm install dayjs --save
```

## API
날짜와 시간을 쉽게 구문 분석, 유효성 검사, 조작 및 표시 가능
```ts
dayjs('2018-08-08') // parse

dayjs().format('{YYYY} MM-DDTHH:mm:ss SSS [Z] A') // display

dayjs().set('month', 3).month() // get & set

dayjs().add(1, 'year') // manipulate

dayjs().isBefore(dayjs()) // query
```

## i18n
국제화 지원
```ts
import 'dayjs/locale/ko' // load on demand

dayjs.locale('ko') // use Korean locale globally

dayjs('2018-05-05').locale('zh-cn').format() // use Chinese Simplified locale in a specific instance
```
# lodash

### omit
`pick` 이랑 반대되는 메서드  
참고: 이 메서드는 `_.pick` 메서드보다 훨씬 느립니다.
```ts
var object = { 'a': 1, 'b': '2', 'c': 3 };
 
_.omit(object, ['a', 'c']);
// => { 'b': '2' }
```
# 해시맵(Map)
Map 객체는 키-값 쌍인 집합
- Map에서의 키는 오직 단 하나만 존재
- for...of 루프는 각 반복에 대해 [key, value]로 이루어진 배열 반환

```js
const map = new Map();

map.set("a", 1);
map.set("b", 2);
map.set("c", 3);

map.get("a");
map.size
map.delete("b");
```

## Map vs Object
| 특징 | Map | Object | 
| --- | --- | --- |
| 순회 | 순회가능(iterable) | Object는 iteration protocol을 구현하지 않기 때문에 for...of문을 사용하여 직접적으로 반복 불가능( Object.keys 또는 Object.entries 사용해야함.)
| 성능 | 추가, 제거 성능 좋음 | 추가, 제거 최적화되지 않음 |

## Map vs Set

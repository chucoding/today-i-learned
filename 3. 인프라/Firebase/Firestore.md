# Firestore
Firebase NoSQL형 데이터베이스

### getDoc
```ts
import { getDoc, doc } from "firebase/firestore";

const snap = await getDoc(doc(db, "users", "a"));
if (snap.exists()) {
    console.log(snap.data());
}
```
- 실행 순간의 데이터만 가져옴
- 이후 DB 변경되어도 다시 안 불림

### onSnapshot
실시간 감지 방식
```ts
import { onSnapshot, doc } from "firebase/firestore";

onSnapshot(doc(db, "users", "a"), (snap) => {
    console.log(snap.data());
});
```
- 최초 1회 + 변경될 때마다 계속 호출
- 구독(subscription) 구조
- 리스너가 많아지면 비용 증가 및 메모리 누수 가능성 생김(실시간 필요할때만 사용)
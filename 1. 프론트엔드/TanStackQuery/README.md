# TanStack Query
SNS, 코인, 주식 등 실시간 데이터를 계속 가져와야하는 사이트들이 쓰면 좋음.

## 장점

1. 성공/실패/로딩 중 쉽게 파악 가능
2. 몇 초 주기마다 또는 다른 작업(탭 등)후 페이지 돌아와도 자동으로 재요청 해줌(자동 재요청은 끌 수 있음)
3. 요청 실패 시 retry 기능
4. 부모 자식에서 같은 ajax요청 해도 합쳐서 한번만 호출(props로 전달안해도됨)
5. ajax 결과 캐싱 기능 (5분 전에 요청해서 성공한게 있으면 그걸 먼저 보여줌)
6. 미리 데이터를 가져와서 캐시에 저장하는 기능(prefetch) 제공

## 설치

```powershell
npm install react-query
```

## 셋팅
```tsx
/* index.tsx */
import { QueryClient, QueryClientProvider } from 'react-query';

const queryClient = new QueryClient();
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <QueryClientProvider client={queryClient}>
      <App />
   </QueryClientProvider>
);
```

```tsx
import {useQuery} from 'react-query';

let result = useQuery('작명', ()=>
	axios.get("/userdata.json").then((a)=>{
		return a.data;
	}),
	{ staleTime : 2000 } //2초안에는 재요청 X
)

result.data      //성공
result.isLoading //로딩중
result.error     //실패
```
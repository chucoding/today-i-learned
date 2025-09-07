# useQueryClient

## queryClient.prefetchQuery()
미리 데이터를 가져와서 캐시에 저장하는 기능

### 실제 활용 사례
사용자가 다음에 어떤 행동을 할지 예측할 수 있는 상황에서 유용한 기능
1. **페이지네이션** : 다음 페이지 데이터 미리 로드
2. **네비게이션** : 링크 hover 시 해당 페이지 데이터 prefetch
3. **검색 결과** : 검색어 입력 중 관련 데이터 미리 로드
4. **모달/팝업** : 모달 열기 전 데이터 미리 준비

```js
function UserProfile({ userId }) {
  const queryClient = useQueryClient()
  
  // 사용자 데이터 prefetch
  queryClient.prefetchQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
  })

  const { data: user } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
    enabled: !!userId, // userId가 있을 때만 실행
  })

  return <div>{user?.name}</div>
}
```
# Bundle 테스트
브라우저 번들 테스트 방법 정리

## 준비
- 시크릿 모드 사용
- vite build 후 vite preview로 띄우고 DevTools를 먼저 연 상태에서 측정
- Disable cache를 켜면 첫 방문자에 가깝게 측정 가능
- throttling을 같은 프리셋으로 고정

## 초기 엔트리 JS
처음 들어갈 때 JS를 얼마나 받는가를 측정.

> [Chrome Network](https://developer.chrome.com/docs/devtools/network/reference?hl=ko)

### 순서
1. DevTools 열기
2. Network 탭 들어가기
3. Disable cache 체크
4. throttling 하나 고정
5. JS 필터 선택
6. 톱니바퀴에서 Big request rows 켜기
7. 해당 route 새로고침

### 확인
- Network 탭 하단 status bar에서 요청 수와 transferred/loaded 총량 확인.
- Big request rows를 켜면 Size 컬럼 아래쪽에 압축 해제 후 크기까지 보임.
- 관련 화면의 초기 JS가 줄었는지 확인 가능

## unused JS
특정 화면에서 실제로 안 쓰고도 내려온 JS가 얼마나 되는가를 측정.

> 💡 unused JS는 다운로드만이 아니라 파싱/컴파일 비용에도 영향을 줌.

> [Chrome Coverage](https://developer.chrome.com/docs/devtools/coverage?hl=ko)

### 순서
1. Command Menu에서 coverage 검색 후 Coverage 열기
2. Per block으로 고정
3. Start instrumenting coverage and reload page
4. 초기 로드 비교면 상호작용 없이 페이지가 안정화되자마자 stop
5. JavaScript만 보이도록 필터

### 확인
- Coverage에서 Total Bytes / Unused Bytes / Used % 확인.
- ex. eager glob을 걷어내서 관련 없는 아이콘/모듈이 초기 로드에 덜 섞였는가 확인 가능
- load-only로 검토하면 초기 진입시 낭비되는 JS 검토 가능

## JS parsing / evaluating time
받아온 JS를 브라우저가 해석하고 실행하는 데 얼마나 썼는가를 측정

> - [Chrome lighthouse](https://developer.chrome.com/blog/lighthouse-load-performance?hl=ko)
> - [Chrome JS 시간 단축](https://developer.chrome.com/docs/lighthouse/performance/bootup-time?hl=ko)

### 순서
1. Lighthouse 탭 열기
2. category는 Performance만
3. device는 한 번 정하면 계속 동일하게 유지
4. 같은 route에서 3회 실행
5. 그중 중간값(median)에 해당하는 run만 캡처

### 확인
- Reduce JavaScript execution time 감사에서 스크립트별 parsing/evaluating/executing 시간 확인 가능.
- TO-BE에서 JS parsing/evaluating이 실제로 줄었는지 확인 가능
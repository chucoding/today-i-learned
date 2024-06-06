# Cursor
AI 기반 코드 에디터

### 특징
- Vscode를 Forking해서 만든 프로젝트
- vscode의 모든기능 및 확장프로그램 사용가능
- 전체코드를 vector DB에 인덱싱
- 자체 파인튜닝된 모델을 갖고 있음

### 장점
- Copilot 보다 효율적
    - 코파일럿의 단점은 내가 주체
    - 큰 사이즈를 해석하지는 못하는 단점 있음
    - GPT-4를 쓸 수 없음
    - 퍼블릭 LLM (폐쇄형이 아니어서 코드가 유출됨)
- 전체 맥락을 통해 파악하기 때문에 정확한 오류 추측 가능
- 등록되지 않은 공식 문서링크 등록 후 학습 가능 (ex nextjs v15)
- 린트, 테스트 등을 위해 여러 플러그인을 설치하지 않아도됨.

### 단점
- Privacy Mode 활성화시 Cursor 측에 코드나 로그가 수집되지 않음.
    - 보안이 중요하다면 도입에 신중해야 함.

### 활용 방법
- 코드 베이스 질문 - 이 함수가 어디에서 쓰이는지 물어보기
    -  @Codebase (전체 코드에서 보여줌)
- 유니온 타입 잘 설정할 경우 제안 잘함
- 함수 기능 설명 후 제작
- 타입 생성 (transform: json to typescript 사이트와 동일한 기능)
- 보일러 플레이트 생성 (Next.js, Svelte, express 등등) 
- 리팩터링
- 테스트 코드 생성


### 🪄 커맨드
| 커맨드 | 설명 |
| --- | --- |
|```Ctrl + K```| AI 코드 생성 |
|```Ctrl + L```| AI 코드 질문 |

### 참고자료
|TITLE|URL|
|---|---|
|Copilot의 한계와 Cursor IDE|https://www.youtube.com/watch?v=F2YhSCcEZe8|
|5주 간의 Cursor IDE 실전 사용 후기|https://weezip.treefeely.com/post/five-weeks-hands-on-review-of-cursor-ide|
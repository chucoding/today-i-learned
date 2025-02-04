# LangGraph
체인 구성 요소를 쉽게 조립할 수 있는 프레임워크

## 사용 이유
랭체인이 있어서 굳이 필요할까 싶지만, 체인이 복잡할때 결과값 추적 및 디버깅이 용이하다고 함.

## 사용 방법

### 그래프 정의
```python
from langgraph.graph import StateGraph

graph = StateGraph(MyState) # 상태 클래스 전달

```

### 그래프 컴파일
```python
app = graph.compile()
```
- 실행 가능한 형태로 변환
- 그래프 구조 검증 및 최적화

### 그래프 실행
```python
result = app.invoke({"question": "What is the capital of France?"})
```
- 초기 상태 전달(MyState 구조와 일치해야함)
- result에 실행 결과 반환

## 요소

### 상태 (State)
애플리케이션의 현재 상태를 나타내는 공유 데이터 구조

#### 역할
- 전체 워크플로우의 컨텍스트 유지
- 노드 간 정보 공유
- TypedDict 또는 Pydantic BaseModel로 정의

### 노드 (Node)
실제 작업을 수행하는 Python 함수

#### 역할
- 특정 로직 수행 (예: LLM 호출, 도구 사용)
- 상태 업데이트
- 입력으로 현재 상태를 받고, 업데이트된 상태 반환
    - 만약 Dict가 아닌 다른 타입을 반환한다면 다음과 같은 오류 발생
    ```
    InvalidUpdateError: Expected dict
    ```
    https://langchain-ai.github.io/langgraph/troubleshooting/errors/INVALID_GRAPH_NODE_RETURN_VALUE/


### 엣지 (Edge)
노드 간의 연결을 정의

#### 역할
- 워크플로우의 흐름 제어
- 조건부 라우팅 가능
- 다음에 실행할 노드 결정
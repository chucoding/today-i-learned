# FastAPI

## 특징

- 빠르고 간결한 코드 작성
- 자동 문서화

## 프로젝트 구조
일반적인 간단한 아키텍처
```
|- app/
    |- main.py
    |- models.py
    |- schemas.py
    |- utils.py
|- tests/
|- .env
|- requirements.txt
```

랭체인 프로젝트 사용
```cmd
langchain app new langserve-clova
```

## 디자인 패턴

### 싱글톤 패턴
Python은 기본적으로 모듈 캐싱 매커니즘 덕분에 명시적인 싱글톤 패턴 없이도 전역 상태 관리가 가능

#### 전통적인 싱글톤 패턴
```python
class DatabaseConnection:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = super().__new__(cls)
        return cls._instance

    def __init__(self):
        # 초기화 로직
        pass
```
아래와 같은 이유로 사용하지 않음.
- 쓰레드 안전성 문제
- 테스트 어려움
- 비동기 환경에서 사용 불가

#### 모듈 레벨 초기화(싱글톤) 
- 애플리케이션이 실행하기 전에 로드되기 때문에 환경변수 설정할 때 많이 사용 (Pythonic한 방법)
- 무거운 로직은 에러처리가 어렵기 때문에 lifespan에서 관리하는게 좋음
- 가벼운 상수 등을 불러올 때 사용하면 좋음.
```
class DatabaseConnection:
    def __init__(self): # 생성자
        # 초기화 로직
        pass

# 모듈 레벨에서 인스턴스 생성
db_connection = DatabaseConnection()
```


#### FastAPI에서 설정 관리
```python
from pathlib import Path

from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    """Setting Configuration."""
    OPENAI_API_KEY: str
    class Config:
        case_sensitive = False
        env_file = Path(__file__).parent / ".env"
        env_file_encoding = "utf-8"

settings = Settings()
```
Pydantic의 BaseSettings는 이미 설정 관리를 위한 최적화된 방식을 제공

환경 변수 로딩, 타입 검증, 기본값 설정 등의 기능이 내장되어 있어 별도의 싱글톤 패턴이 필요하지 않음.

# 초기화 변수

### lifespan
컨텍스트 매니저 페턴을 사용하여 시작과 종료를 모두 관리(FastAPI 3.0부터 권장되는 방식)

```python
@asynccontextmanager
async def lifespan(app: FastAPI):
    SchemaLoader() # 시작 시 실행 코드 작성
    yield
    # 종료시 실행 코드 작성
```

아래는 엣날 방식 (startup, shutdown 분리)

```python
@app.on_event("startup")
async def startup_event():
    SchemaLoader()
```

lifespan 방식이 테스트 시 리소스 관리가 더 명확하고 쉬움, 특히 async 테스트에서 더 안정적

> 💡 모듈 레벨 초기화 vs lifespan   
> - 서버 초기화 시점을 명확하게 제어  
> - 에러 처리가 용이
> - 테스트 환경에서 모의 객체(mock)로 대체하기 쉬움


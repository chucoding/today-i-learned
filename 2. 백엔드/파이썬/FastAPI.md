# FastAPI

## 특징

- 빠르고 간결한 코드 작성
- 자동 문서화

## 프로젝트 구조
일반적인 간단한 아키텍처1
```
|- app.py
|- main.py
|- models.py
|- schemas.py
|- utils.py
|- tests/
|- .env
|- requirements.txt
```

일반적인 간단한 아키텍처2
```
|- app/
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

#### 모듈 레벨 싱글톤 (Pythonic한 방법)
```
class DatabaseConnection:
    def __init__(self):
        # 초기화 로직
        pass

# 모듈 레벨에서 인스턴스 생성
db_connection = DatabaseConnection()
```

만약 첫 로드시에만 실행되는 코드가 있다면 다음과 같이 사용(FastAPI)
```python
@asynccontextmanager
async def lifespan(app: FastAPI):
    SchemaLoader()
    yield
```
사용이유
- 타이밍 보장 (애플리케이션이 완전히 시작되기 전에 초기화가 완료됨을 보장)


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
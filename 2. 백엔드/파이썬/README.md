# Python

# 배경
- 1991년 귀도 반 로섬이 개발
- **인터프리터** **언어** 이자 **객체지향 언어**
- 동적 타이핑(dynamic typing) 언어 (실행하는 시점에 인터프리터가 해석)
- 처음 C언어로 구현되었음

구글에서 만든 소프트웨어의 50%이상이 파이썬으로 작성됨 예) 구글, 인스타그램, 드롭박스

# 특징
1. **플랫폼(OS) 독립적인 인터프리터 언어**
디바이스를 고려하지 않아도됨

1. **객체지향의 동적타이핑 언어**
실행 순서가 아닌 단위 모듈(객체) 중심으로 프로그램 작성,
실행 시점에 프로그램이 사용해야할 데이터에 대한 타입을 결정

1. **이해하기 쉬운 문법**
사람의 시간이 기계의 시간보다 중요하다!
한번 쓰고 버리는 코드(Disposable)에 좋음~!

1. **중괄호가 없음**
indentation을 잘 지켜야한다.

2. **일급 객체 함수**
변수나 자료구조에 함수를 담을 수 있다.
함수를 파라미터로 전달할 수 있다.
반환값으로 사용할 수 있다.

1. **java에 비해 프로그램 실행 속도가 느림.**

# 파이썬 컨벤션 (PEP)

1. 들여쓰기 공백 4칸
2. 한 줄은 최대 79자까지 (: 모니터 크기 때문)
3. 연산자는 2칸 이상 안 띄움 (?)
    
    ```python
    variable_example = 12
    ```
    
4. 코드의 마지막에는 항상 한 줄 추가
5. 소문자 l, 대문자 O, 대문자 I 금지
6. 함수명은 소문자로 구성, 필요하면 밑줄로 나눔

**import**

- 한 라인에 하나의 모듈 import, 항상 상단에 위치 시키기

```python
import os, sys, math (X)

import os
import sys
import math
```

- 모듈 import 순서
    - 사이 구분
    - 알파벳 순서 정렬
        - 알파벳순으로 정렬된 모듈은 더 빨리 읽고 검색할 수 있다.
        - 중복 불러오기를 피할 수 있다.
        - from이냐 import이냐? 이건 답이 없으므로  from을 기준으로 정렬한다.

```python
표준 라이브러리 모듈

3rd Party 모듈

직접 만든 모듈
```

- 절대 경로 사용(복잡한 패키지 경로를 갖는 경우에만 상대경로(.) 사용)

```python
import mymodule (X)

import mypkg.mymodule
from mypkg.mymodule import *
from mypkg import mymodule

from . import mymodule #절대경로
from .mymodule import example # 상대경로
```

**변수 이름 작명법**

- 알파벳, 숫자, 언더스코어(_)로 선언 가능
ex) data, _a12, _gg
- 변수명은 의미 있는 단어로 표기하는 것이 좋다(줄이지 말것)
- 변수명은 대소문자가 구분된다
- 특별한 의미가 있는 예약어는 쓰지 않는다.
ex) for, if, else
- 변수에 처음 값을 할당할 때는 띄어쓰기 사용, 키워드 인자(keyword argument)에 값을 할당할 때는 띄어쓰기 사용X ⇒ grep 명령어를 이용해 둘을 구분하기 용이함

**Example**

```python
def some_func(kw1):
    print(kw1)
 
if __name__ == "__main__":
    kw1 = "Hello"
    some_func(kw1=kw1)
```

**컨벤션 체크하는 모듈**

```powershell
(base) conda install flake8    # 잘못된 컨벤션을 보여줌
(base) conda install black     # 잘못된 컨벤션을 고쳐줌

(base) flake8 test.py
(base) black test.py
(base) flake8 test.py
```

vscode에서는 알아서 고쳐주기 때문에 사용할 필요 없음.

**docstring**

모듈, 함수, 클래스 또는 메소드 정의의 첫 번째 명령문으로 발생하는 문자열 리터럴

```python
class CustomClass:
"""
클래스의 문서화 내용을 입력합니다.    
"""

    def custom_function(a, b):
    """
		This is an example of Google style.
		
		Args:
		    param1: This is the first param.
		    param2: This is a second param.
		
		Returns:
		    This is a description of what is returned.
		
		Raises:
		    KeyError: Raises an exception.
		"""
```

vscode - docstring generator 플러그인 설치

# 설치
|이름|설명|비고|
|---|---|---|
| Python | http://www.python.org/downloads/ | ※ 설치시 주의할점 Add Python 3.7 to PATH 체크 |
| Anaconda | python miniconda 검색 | 파이썬 패키지 관리도구(ML 표준) |
| VS Code | 파이썬 Extension 설치 |  |
| JUPYTOR |  | 웹 기반 인터렉티브 코드 편집기의 표준 |

# 실행

설치파일을 실행시키면 ‘대화형 인터프리터(파이썬 쉘)’가 실행된다.
(입력과 출력이 번갈아 이어지는것이 마치 대화하는 것처럼 느껴지기 때문에 대화형이라 부름)

**예제**

```python
>>> 1 + 1
2
```

# 수행 속도

```python
import time

# jupyter 수행속도 명령어
%timeit 함수명()
```

# 참고자료

| TITLE | URL |
| --- | --- |
| 파이썬 코딩 컨벤션 | https://peps.python.org/pep-0008/ |
| 파이썬 코딩 컨벤션 한글 | https://zerosheepmoo.github.io/pep8-in-korean/ |
| PEP-8을 따라야 하는 이유 | https://kimjingo.tistory.com/177 |
| PEP-8 import | https://afsdzvcx123.tistory.com/entry/Python-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A0%88%EB%8C%80-%EA%B2%BD%EB%A1%9C%EC%99%80-%EC%83%81%EB%8C%80%EA%B2%BD%EB%A1%9C-import |
| PEP-8 알파벳 순서정렬 | https://stackoverflow.com/questions/20762662/whats-the-correct-way-to-sort-python-import-x-and-from-x-import-y-statement |
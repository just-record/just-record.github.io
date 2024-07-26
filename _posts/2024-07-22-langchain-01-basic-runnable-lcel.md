---
layout: single
title: "LangChain - 01. 기본 사용 - Runnable, LCEL" 
categories: LangChain
tag: [Python, LLM, LangChain]
toc: true
toc_sticky: true
toc_label: "목차"
---

## LangChain 이란?

LangChain은 대규모 언어 모델(LLM)을 활용한 애플리케이션 개발을 위한 프레임워크입니다. 사용해 가면서 익히는 것이 좋습니다.

[공식 문서](https://python.langchain.com/v0.2/docs/introduction/)

[위의 공식 문서를 보고 연습한 곳](https://github.com/just-record/langchain_practice)

## LangChain 설치

```bash
pip install langchain
pip install langchain-core
pip install langchain-community
pip install langchain-experimental
pip install langchain-openai     # openai의 API를 사용하기 위해
pip install langchain_anthropic  # anthropic의 API를 사용하기 위해
pip install python-dotenv        # 환경변수(API-KEY)를 사용하기 위해
```

### API-KEY 설정

`.env` 파일 생성

```bash
OPENAI_API_KEY='your-api-key-here'
ANTHROPIC_API_KEY='your-api-key-here'
```

API Key확인 하기

```python
from dotenv import load_dotenv
load_dotenv()

import os

print(os.getenv('OPENAI_API_KEY'))
print(os.getenv('ANTHROPIC_API_KEY'))
```

## 기본적인 사용 예시

### openai의 API

```python
from dotenv import load_dotenv
load_dotenv()

from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

result = llm.invoke("hi")
print(result.content)
```

```python
# 결과
Hello! How can I assist you today?
```

### anthropic의 API

동일한 소스 코드에서 llm만 변경 해 주면 됩니다.

```python
from dotenv import load_dotenv
load_dotenv()

# from langchain_openai import ChatOpenAI
from langchain_anthropic import ChatAnthropic

# llm = ChatOpenAI(model="gpt-4o-mini")
llm = ChatAnthropic(model="claude-3-5-sonnet-20240620")

result = llm.invoke("hi")
print(result.content)
```

```python
# 결과
Hello! How can I assist you today? Feel free to ask me any questions or let me know if you need help with anything.
```

**동일한 소스 코드에 여러 llm을 사용**할 수 있습니다.

## Runnable 가법게 이해 하기

다양한 언어 모델 관련 작업들을 표준화된 인터페이스로 묶어 쉽게 조합하고 실행할 수 있게 해주는 프로토콜

- 표준화된 인터페이스: Runnable은 다양한 언어 모델 관련 컴포넌트들(예: 프롬프트, 체인, 에이전트 등)의 일관된 실행 방식을 제공하며 모든 Runnable 객체는 'invoke' 메서드를 통해 실행
- 조합 가능성: Runnable 객체들은 쉽게 서로 연결하거나 조합할 수 있어 복잡한 언어 모델 워크플로우를 구축할 수 있음
- 유연성: 다양한 입력 유형과 출력 유형을 처리할 수 있어, 텍스트 처리부터 구조화된 데이터 처리까지 광범위한 작업을 수행
- 재사용성: Runnable 객체들은 독립적으로 정의되고 재사용될 수 있음

### Runnable 예시

```python
from typing import Any
from langchain.schema.runnable import Runnable

# Runnable 객체를 상속받아 Runnabel 프로토콜을 구현한 TextProcessor 클래스 정의
class TextProcessor(Runnable):
    # Runnable의 추상 메서드인 invoke를 구현
    def invoke(self, input: str) -> str:
        return input.upper()

# Runnable 객체 생성
processor = TextProcessor()

# Runnable 객체 사용
result = processor.invoke("hello, world!")   # invoke 메서드를 호출하여 Runnable 객체 실행
print(result)  # 출력: HELLO, WORLD!
```

### 문자열 -> 대문자 -> 소문자로 변환하기

문자열을 대문자로 변환하고 대문자로 변환 된 문자열을 소문자로 변환하기

> 먼저 Runnable을 쓰지 않고 위의 기능 구현 하기

```python
def uppercase_processor(text: str) -> str:
    return text.upper()

def lowercase_processor(text: str) -> str:
    return text.lower()

def apply_processors(text: str, processors: list) -> str:
    result = text
    for processor in processors:
        result = processor(result)
    return result

# 프로세서 리스트 생성
processors = [uppercase_processor, lowercase_processor]

# 프로세서 적용
input_text = "Hello, World!"
result = apply_processors(input_text, processors)

print(f"Original: {input_text}")
print(f"After uppercase: {uppercase_processor(input_text)}")
print(f"Final result: {result}")
```

```python
# 결과
Original: Hello, World!
After uppercase: HELLO, WORLD!
Final result: hello, world!
```

- `apply_processors`: 문자열과 프로세서 리스트를 입력으로 받아 각 프로세서를 순차적으로 적용하여 결과를 반환 - 개발자가 작성해야 함

> Runnable을 사용하여 위의 기능 구현 하기

```python
from typing import Any
from langchain.schema.runnable import Runnable, RunnableConfig

class UppercaseProcessor(Runnable):
    def invoke(self, input: Any, config: RunnableConfig = None) -> str:
        if isinstance(input, str):
            return input.upper()
        else:
            raise ValueError("Input must be a string")

class LowercaseProcessor(Runnable):
    def invoke(self, input: Any, config: RunnableConfig = None) -> str:
        if isinstance(input, str):
            return input.lower()
        else:
            raise ValueError("Input must be a string")

# Runnable 객체 생성
uppercase_processor = UppercaseProcessor()
lowercase_processor = LowercaseProcessor()

# '|' 연산자를 사용하여 두 Runnable 객체를 체이닝 - 체인된 Runnable 객체 생성
chained_processor = uppercase_processor | lowercase_processor

# 체인된 Runnable 객체 사용
input_text = "Hello, World!"
result = chained_processor.invoke(input_text)

print(f"Original: {input_text}")
print(f"After uppercase: {uppercase_processor.invoke(input_text)}")
print(f"Final result: {result}")
```

```python
# 결과
Original: Hello, World!
After uppercase: HELLO, WORLD!
Final result: hello, world!
```

- `chained_processor`: `|` 연산자를 사용하여 두 Runnable 객체를 체이닝하여 새로운 Runnable 객체 생성
- `chained_processor.invoke`: 체인된 Runnable 객체를 사용하여 입력 문자열을 대문자로 변환한 후 소문자로 변환하여 결과를 반환
- Runnable을 사용하면 `apply_processors` 함수를 직접 작성하지 않아도 되며, 체이닝을 통해 여러 프로세서를 쉽게 연결할 수 있음
- Runnable: 입력 유형과 출력 유형을 명시적으로 지정 / 재사용 가능한 컴포넌트로 정의 / 표준화된 인터페이스를 제공하여 일관된 방식으로 실행(`invoke` 메서드를 통해 실행)

## LCEL 가볍게 이해 하기

LCEL(LangChain Execution Language)은 LLM 워크플로우를 간결하게 표현하는 언어로 'Runnable 프로토콜'을 기반으로 구축된 임의의 맞춤형 체인을 생성하는 방법입니다.

- "임의의 맞춤형 체인": 사용자의 필요에 따라 LLM 처리 과정을 자유롭게 설계
- "Runnable 프로토콜": LangChain에서 다양한 구성 요소들이 일관된 방식으로 실행될 수 있게 하는 표준화된 인터페이스

### Runnable invoke 하기

```python
from langchain_core.runnables import RunnableLambda

runnable = RunnableLambda(lambda x: str(x))
print(f'type runnable: {type(runnable)}')
response = runnable.invoke(5)
print(response)
print(type(response))
```

```python
# 결과
type runnable: <class 'langchain_core.runnables.base.RunnableLambda'>
5
<class 'str'>
```

- 'RunnableLambda': 일반 함수 또는 람다 함수를 Runnable 객체로 변환
- 'invoke': Runnable 객체를 실행하여 결과를 반환

### Runnable 객체로 체인 만들기

```python
from langchain_core.runnables import RunnableLambda

# RunnableLambda를 사용하여 람다 함수를 Runnable 객체로 변환
runnable1 = RunnableLambda(lambda x: {"foo": x}) 
runnable2 = RunnableLambda(lambda x: [x] * 2)

# '|'로 두 Runnable 객체를 체이닝하여 새로운 Runnable 객체 생성
chain = runnable1 | runnable2

# 체인된 Runnable 객체를 invoke로 호출
print(chain.invoke(2))

### 하나씩 호출 ###
print(runnable1.invoke(2))
print(runnable2.invoke({"foo": 2}))
```

```python
# 결과
[{'foo': 2}, {'foo': 2}]
{'foo': 2}
[{'foo': 2}, {'foo': 2}]
```

### Runnable을 병렬로 호출 하기

```python
from langchain_core.runnables import RunnableLambda, RunnableParallel

runnable1 = RunnableLambda(lambda x: {"foo": x})
runnable2 = RunnableLambda(lambda x: [x] * 2)

# RunnableParallel을 사용하여 두 Runnable 객체를 병렬로 실행
# 첫 번째 Runnable 객체의 결과를 'first'에, 두 번째 Runnable 객체의 결과를 'second'에 저장
chain = RunnableParallel(first=runnable1, second=runnable2)

print(chain.invoke(2))
```

```python
# 결과
{'first': {'foo': 2}, 'second': [2, 2]}
```

- 'runnable1'과 'runnable2'의 입력 값은 모두 2
- 물론 'chain = RunnableParallel(first=runnable1, second=runnable2)' 뒤에 다른 Runnable 객체를 체이닝 할 수 있음
- 위의 경우 결과 값인 '{'first': {'foo': 2}, 'second': [2, 2]}'가 추가된 Runnable 의 입력 값이 됨

### RunnablePassthrough 사용하여 값 그대로 전달하기

RunnablePassthrough는 입력 값을 그대로 반환하는 Runnable 객체로, 입력 값을 그대로 전달하고 싶을 때 사용

```python
from langchain_core.runnables import RunnableLambda, RunnablePassthrough

# runnable1 = RunnableLambda(lambda x: {"foo": x})
runnable2 = RunnableLambda(lambda x: [x] * 2)

# chain = runnable1 | runnable2
# chain = {"foo": x} | runnable2
chain = {"foo": RunnablePassthrough()} | runnable2

print(chain.invoke(2))
```

```python
# 결과
[{'foo': 2}, {'foo': 2}]
```

### RunnablePassthrough.assign 사용하여 값 추가하기

- 기존 입력 딕셔너리에 새로운 키-값 쌍을 추가
- 기존 입력 값을 수정 하지 않고 새로운 정보를 덧붙이는데 유용
- 키-값의 값은 Runnable 객체로 지정 - 이 때 Runnable의 입력은 기존 입력값을 그대로 받음

- 기존 입력을 그대로 유지:  RunnablePassthrough
- 'assign()': 기존 입력을 유지 하고 새로운 키-값 쌍을 입력 딕셔너리에 추가 - 기존 입력을 입력으로 사용
- 키-값의 값으로 Runnable 객체 사용: Runnable이 실행되고 그 결과가 새로운 값으로 사용

```python
from langchain_core.runnables import RunnableLambda, RunnablePassthrough

runnable1 = RunnableLambda(lambda x: x["foo"] + 7)

chain = RunnablePassthrough.assign(bar=runnable1)

chain.invoke({"foo": 10})
```

```python
# 결과
{'foo': 10, 'bar': 17}
```

- '{"foo": 10}'입력은 전달 되고 그대로 유지 됨
- 'RunnablePassthrough.assign': 입력 값에 'bar' 키를 추가하고 runnable1의 결과 값을 'bar' 키의 값으로 설정
  - '{"foo": 10}'이 입력이 되어 'x["foo"]'은 10이 되고 10 + 7 = 17이 되어 'bar' 키의 값으로 설정
- '{"foo": 10}'는 유지 되고 새로운 키-값이 추가 되므로 최종 결과: {'foo': 10, 'bar': 17}

### config - Runnable 실행 시에 동적으로 매개변수 설정하기

- config: Runnable 객체의 실행 방식을 세부적으로 조정. 다양한 실행 매개변수를 동적으로 설정
- config의 인자는 Runnable 객체의 타입에 따라 다름. 각 Runnable 타입은 자신만의 특정 config 옵션을 가짐

```python
from langchain_core.runnables import RunnableLambda, RunnableParallel
import time

def function1(x):
    print("function1 is running")
    time.sleep(1)
    return {"foo": x}

def function2(x):
    print("function2 is running")
    time.sleep(1)
    return [x] * 2

def function3(x):
    print("function3 is running")
    time.sleep(1)
    return str(x)

runnable1 = RunnableLambda(function1)
runnable2 = RunnableLambda(function2)
runnable3 = RunnableLambda(function3)

chain = RunnableParallel(first=runnable1, second=runnable2, third=runnable3)

print(chain.invoke(7, config={"max_concurrency": 2}))
```

```python
# 결과
function1 is running
function2 is running
function3 is running
{'first': {'foo': 7}, 'second': [7, 7], 'third': '7'}
```

- max_concurrency: 병렬로 실행할 최대 작업 수: runnable1, runnable2, runnable3 중 2개의 작업이 병렬로 실행 된 후 나머지 1개의 작업이 실행됩니다.

### with_config - Runnable 에 설정된 config를 저장 하기

config로 설정 된 Runnable을 새로운 chain에 저장 할 수도 있음

```python
# ... 위의 코드와 동일

chain = RunnableParallel(first=runnable1, second=runnable2, third=runnable3)
### 1. with_config()
print(chain.with_config(max_concurrency=2).invoke(7))
# {'first': {'foo': 7}, 'second': [7, 7], 'third': '7'}


### 2. 새로운 chain으로 저장 가능
print('-'*30)
configured_chain = chain.with_config(max_concurrency=2)
print(configured_chain.invoke(7))
# {'first': {'foo': 7}, 'second': [7, 7], 'third': '7'}
```

```python
# 결과
function1 is running
function2 is running
function3 is running
{'first': {'foo': 7}, 'second': [7, 7], 'third': '7'}
```

### bind - Runnable의 입력 매개변수를 고정 하기

- bind 메서드: Runnable 객체의 입력 매개변수를 고정하는 데 사용
- Python의 내장 functools.partial나 클로저와 유사한 느낌

```python
from typing import Optional

from langchain_core.runnables import RunnableLambda


def func(main_arg: dict, other_arg: Optional[str] = None) -> dict:
    if other_arg:
        return {**main_arg, **{"foo": other_arg}}
    return main_arg


runnable1 = RunnableLambda(func)
bound_runnable1 = runnable1.bind(other_arg="bye")

bound_runnable1.invoke({"bar": "hello"})
```

```python
# 결과
{'bar': 'hello', 'foo': 'bye'}
```

#### with_config와 bind의 차이

|구분|with_config|bind|
|---|---|---|
|용도|실행 환경 설정 (예: 병렬 처리, 메모리 사용량 등)|함수 인자의 부분 적용|
|유연성|실행 시점에 쉽게 변경 가능합니다.|한번 바인딩된 인자는 고정되며, 실행 시점에 변경하기 어려움|
|적용 범위|주로 체인 전체나 복잡한 Runnable 객체에 적용|개별 함수나 간단한 Runnable 객체에 주로 사용|
|반환 값|원본 객체의 설정이 변경된 새로운 복사본을 반환|부분적으로 적용된 새로운 함수(또는 Runnable)를 반환|

with_config는 실행 환경을 조정하는 데 사용되고, bind는 함수의 인자를 부분적으로 적용하는 데 사용됩니다. 두 메서드 모두 원본 객체를 변경하지 않고 새로운 객체를 반환한다는 점에서 비슷하지만, 그 목적과 사용 방식에서 차이가 있습니다.

예를 들어 위의 코드에서 'configured_chain = chain.with_config(max_concurrency=2)'를 'configured_chain = chain.bind(max_concurrency=2)' 로, with_config 대신 bind를 사용 하면 오류가 발생 합니다. 'max_concurrency'는 실행 설정의 일부로, 함수의 입력 매개변수가 아닙니다. 이는 RunnableParallel의 동작 방식을 제어하는 설정입니다

### with_fallbacks - 오류 발생 시 대체 Runnable 지정 하기

```python
from langchain_core.runnables import RunnableLambda

runnable1 = RunnableLambda(lambda x: x + "foo")
runnable2 = RunnableLambda(lambda x: str(x) + "foo")

chain = runnable1.with_fallbacks([runnable2])

chain.invoke(5)
```

```python
# 결과
'5foo'
```

- 정상적인 경우: runnable1이 성공적으로 실행
- 오류 발생 시: runnable2가 대체 실행
- `5 + "foo"`가 오류를 발생시켜 runnable2가 실행

### with_retry - 작업이 실패할 때 재시도 하기

```python
from langchain_core.runnables import RunnableLambda


counter = 0

def func(x):
    global counter
    counter += 1
    print(f"attempt with {counter=}")
    # return x / counter
    return x / 0


## 1. stop_after_attempt에 의해 2번 재시도 하고 오류 발생
chain = RunnableLambda(func).with_retry(stop_after_attempt=2)

try:
    print(chain.invoke(2))
except Exception as e:
    print(e)

### 2. retry_if_exception_type를 사용 하여 Exception Type을 지정하여 재시도
# 아래 코드는 ZeroDivisionError가 발생하는데 retry_if_exception_type에 해당 Exception이 없어서 재시도 하지 않음
print('-' * 50)
counter = 0
chain = RunnableLambda(func).with_retry(retry_if_exception_type=(ValueError, ConnectionError), stop_after_attempt=2)

try:
    print(chain.invoke(2))
except Exception as e:
    print(e)    
```

```python
# 결과
attempt with counter=1
attempt with counter=2
division by zero   
--------------------------------------------------
attempt with counter=1
division by zero
```

### 비동기 실행

```python
import asyncio
from langchain_core.runnables import RunnableLambda

async def main():
    runnable = RunnableLambda(lambda x: str(x))
    result = await runnable.ainvoke(5)
    print(result)

asyncio.run(main())
```

### stream - 스트림 처리하기

```python
from langchain_core.runnables import RunnableLambda
import time


def func(x):
    for y in x:
        print(f'y: {y}')
        yield str(y)


runnable = RunnableLambda(func)


for chunk in runnable.stream(range(5)):
    time.sleep(0.3)
    print(chunk)
```

```python
y: 0
0
y: 1
1
y: 2
2
y: 3
3
y: 4
4
```

### 비동기 스트림 처리

```python
from langchain_core.runnables import RunnableLambda
import asyncio

async def func(x):
    for y in x:
        print(f'y: {y}')
        yield str(y)

runnable = RunnableLambda(func)

async def main():
    async for chunk in runnable.astream(range(5)):
        await asyncio.sleep(0.3)
        print(chunk)

# 실행
asyncio.run(main())
```  

---

해시태그: #Python #llm #LangChain #openai #anthropic #Runnable #LCEL

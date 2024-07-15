---
layout: single
title: "Python - 비동기 프로그램(asyncio)" 
categories: Python
tag: [Python, asyncio]
toc: true
toc_sticky: true
toc_label: "목차"
---

## 비동기 프로그램 이란

### 동기 vs 비동기 프로그래밍

- 동기 프로그래밍:  작업이 순차적으로 실행되며, 각 작업이 완료될 때까지 다음 작업을 기다림
- 비동기 프로그래밍: 작업을 시작한 후 완료를 기다리지 않고 다른 작업을 수행할 수 있음

### 동기 프로그래밍 예제

```python
import time

def sync_task(name, duration):
    print(f"{name} 작업 시작")
    time.sleep(duration)  # 작업 시뮬레이션
    print(f"{name} 작업 완료")

def main():
    start = time.time()
    sync_task("Task 1", 2)
    sync_task("Task 2", 3)
    sync_task("Task 3", 1)
    end = time.time()
    print(f"총 실행 시간: {end - start:.2f}초")

main()
```

```python
# 실행 결과
Task 1 작업 시작
Task 1 작업 완료
Task 2 작업 시작
Task 2 작업 완료
Task 3 작업 시작
Task 3 작업 완료
총 실행 시간: 6.03초
```

Task1, Task2, Task3 작업이 순차적으로 실행 됨

### 비동기 프로그래밍 예제

```python
import asyncio

async def async_task(name, duration):
    print(f"{name} 작업 시작")
    await asyncio.sleep(duration)  # 비동기적 대기
    print(f"{name} 작업 완료")

async def main():
    start = time.time()
    await asyncio.gather(
        async_task("Task 1", 2),
        async_task("Task 2", 3),
        async_task("Task 3", 1)
    )
    end = time.time()
    print(f"총 실행 시간: {end - start:.2f}초")

asyncio.run(main())
```

```python
# 실행 결과
Task 1 작업 시작
Task 2 작업 시작
Task 3 작업 시작
Task 3 작업 완료
Task 1 작업 완료
Task 2 작업 완료
총 실행 시간: 3.01초
```

Task1, Task2, Task3 작업이 거의 동시에 실행 되고 작업 완료는 소요시간이 짧은 순으로 Task3, Task1, Task2 순으로 완료됨

### 비동기 프로그래밍의 필요성

Python에서는 비동기 프로그래밍은 I/O 바운드 작업에서 중요한 역할을 합니다. 예를 들어, 웹 서버는 여러 클라이언트의 요청을 동시에 처리해야 합니다. 동기 방식으로는 각 요청이 순차적으로 처리되므로 응답 시간이 길어질 수 있지만, 비동기 방식으로는 각 요청을 비동기적으로 처리하여 더 많은 클라이언트를 빠르게 응대할 수 있습니다.

- I/O 바운드 작업 처리: 파일 읽기/쓰기, 네트워크 요청 등에서 효율적
- 동시성 향상: 여러 작업을 동시에 처리하여 전체 실행 시간을 단축
- 리소스 효율성: **하나의 스레드**로 여러 작업을 관리하여 메모리 사용을 줄임
- 확장성: 많은 동시 연결을 처리하는 서버 애플리케이션에 적합

## Python 비동기

### 코루틴(Coroutine) 이란

코루틴은 일반적인 함수와 달리 중간에 실행을 멈췄다가 다시 시작할 수 있는 함수입니다. 코루틴은 제너레이터를 기반으로 발전한 개념으로, yield 키워드를 사용하여 값을 반환하고 받을 수 있습니다. 현재는 async/await 문법을 사용하여 더 쉽게 정의할 수 있습니다.

- 실행을 중간에 멈추고 재개할 수 있음
- 여러 진입점과 종료점을 가질 수 있음
- 상태를 유지하면서 실행/중지가 가능

### async/await

- `async`: 코루틴을 정의하는 데 사용되며 이 키워드를 사용하여 정의된 함수는 호출될 때 코루틴 객체를 반환
- `await`: 코루틴 내부에서 다른 비동기 함수의 실행을 일시 중지하고 해당 함수가 완료될 때까지 기다림. 이를 통해 동기적으로 보이지만 실제로는 비동기적으로 실행되는 코드를 작성

기본적인 예시 코드

```python
import asyncio

async def greet(name):
    print(f"Hello, {name}!")
    await asyncio.sleep(1)  # 비동기적으로 1초 대기
    print(f"Goodbye, {name}!")

async def main():
    print("시작")
    await greet("Alice")
    await greet("Bob")
    await greet("Charlie")
    print("종료")

asyncio.run(main())
```

```python
# 실행 결과
시작
Hello, Alice!
Goodbye, Alice!
Hello, Bob!
Goodbye, Bob!
Hello, Charlie!
Goodbye, Charlie!
종료
```

- `async`로 정의된 `greet` 함수와 `main` 함수는 코루틴으로 정의되어 있음
- `await`를 사용하여 'asyncio.sleep()'의 완료를 기다림
- 'main()' 함수에서 `await`를 사용하여 각 'greet()' 코루틴의 완료를 기다림

조금 복잡한 예시 코드

```python
import asyncio

async def task1():
    print("Task 1 started")
    await asyncio.sleep(2)
    print("Task 1 finished")

async def task2():
    print("Task 2 started")
    await asyncio.sleep(1)
    print("Task 2 finished")

async def main():
    await asyncio.gather(
        task1(),
        task2()
    )

if __name__ == "__main__":
    asyncio.run(main())
```

```python
# 실행 결과
Task 1 started
Task 2 started
Task 2 finished
Task 1 finished
```

- `asyncio.gather()`: 여러 코루틴을 동시에 실행
- Task1, Task2가 거의 동시에 실행되고 작업 완료는 소요시간이 짧은 순으로 Task2, Task1 순으로 완료됨
- 전체 소요 시간은 3초(2초 + 1초)가 아닌 약 2초(동시 실행)가 됨

## asyncio 모듈

asyncio는 Python에서 비동기 I/O, 이벤트 루프, coroutines, Tasks 등을 관리하는 라이브러리

### 이벤트 루프(Event Loop)

이벤트 루프는 비동기 프로그래밍의 핵심 요소로, 여러 비동기 작업을 동시에 관리하고 실행합니다

- 코루틴의 실행을 관리
- I/O 연산 처리
- 하위 프로세스 실행
- 네트워크 연결 관리

```python
import asyncio

async def hello_world():
    print("Hello")
    await asyncio.sleep(1)
    print("World")

# 이벤트 루프 생성 및 실행
loop = asyncio.get_event_loop()
loop.run_until_complete(hello_world())
loop.close()

# Python 3.7 이상에서는 다음과 같이 간단히 실행 가능
# asyncio.run(hello_world())
```

```python
# 실행 결과
DeprecationWarning: There is no current event loop
  loop = asyncio.get_event_loop()
Hello
World
```

- `asyncio.get_event_loop()`: 이벤트 루프를 생성
- `loop.run_until_complete()`: 코루틴을 실행하고 완료될 때까지 기다림
- `loop.close()`: 이벤트 루프를 종료

위의 'asyncio.get_event_loop()'는 Deprecated 될 것이므로 앞으로는 아래와 같이 사용하는 것이 좋습니다.

```python
import asyncio

async def hello_world():
    print("Hello")
    await asyncio.sleep(1)
    print("World")

asyncio.run(hello_world())
```

```python
# 실행 결과
Hello
World
```

- `asyncio.run()`: Python 3.7 이상에서 이벤트 루프를 생성하고 실행하는 간단한 방법

### Tasks

- 코루틴을 실행하기 위한 'asyncio'의 객체
- 비동기 작업을 이벤트 루프에 스케줄링하고 관리
- 여러 Task를 동시에 실행

```python
import asyncio

async def task_coroutine(name, delay):
    print(f"Task {name} started")
    await asyncio.sleep(delay)
    print(f"Task {name} finished after {delay} seconds")

async def main():
    # task1과 task2를 생성하여 이벤트 루프에 스케줄링합니다.
    task1 = asyncio.create_task(task_coroutine("A", 2))
    task2 = asyncio.create_task(task_coroutine("B", 1))
    
    # task1과 task2는 비동기적으로 실행되기 시작합니다.
    
    # await task1: task1이 완료될 때까지 기다립니다.
    await task1
    # task1이 완료된 후, task2가 완료될 때까지 기다립니다.
    await task2

# asyncio.run은 이벤트 루프를 생성하고 main 코루틴을 실행합니다.
asyncio.run(main())
```

```python
# 실행 결과
Task A started
Task B started
Task B finished after 1 seconds
Task A finished after 2 seconds
```

### Futures

- Future는 미래에 완료될 작업을 나타내는 객체
- 비동기 작업의 결과를 나타내며 완료되지 않은 작업의 상태

```python
import asyncio

# 비동기 함수 set_future_value를 정의합니다.
# 이 함수는 future 객체를 받아 1초 후에 값을 설정합니다.
async def set_future_value(future):
    # 1초 동안 비동기적으로 대기합니다.
    await asyncio.sleep(1)
    # 대기 후, future 객체에 결과 값을 설정합니다.
    future.set_result("Future is done!")

# 비동기 함수 main을 정의합니다.
async def main():
    # future 객체를 생성합니다. Future는 나중에 완료될 작업을 나타냅니다.
    future = asyncio.Future()
    # set_future_value 함수를 호출하여 future 객체의 값을 설정합니다.
    # 이 함수는 future 객체를 받아 값을 설정하는 비동기 작업을 수행합니다.
    await set_future_value(future)
    # future 객체의 결과 값을 출력합니다. 이 시점에서 future는 완료되어 값을 가지고 있습니다.
    print(future.result())

# asyncio.run은 새로운 이벤트 루프를 생성하고 main 코루틴을 실행합니다.
asyncio.run(main())
```

```python
# 실행 결과
Future is done!
```

- `asyncio.Future()`: 미래에 완료될 작업을 나타내는 객체를 생성
- `future.set_result()`: Future 객체에 결과 값을 설정
- `future.result()`: Future 객체의 결과 값을 반환

### Taks와 Futures 비교

- `Task`: 코루틴을 실행하기 위한 asyncio의 객체
- `Future`: 미래에 완료될 작업을 나타내는 객체

**Task는 즉시 실행**을 하지만, **Future는 실행을 시작하지 않음**

## 비동기 함수 작성하기

비동기 함수는 `async def` 키워드를 사용하여 정의합니다. 이러한 함수는 코루틴을 반환하며, 다른 비동기 작업의 완료를 기다리기 위해 await 키워드를 사용할 수 있습니다.

### `async def`

`async def` 키워드는 비동기 함수를 정의할 때 사용됩니다. 이 함수는 호출될 때 코루틴 객체를 반환하며, 이는 비동기 작업을 나타냅니다.

```python
# 사용 예시
async def 함수이름(매개변수):
    # 비동기 코드
    ...
```

```python
import asyncio

async def greet(name):
    return f"Hello, {name}!"

# 사용 예
async def main():
    result = await greet("Alice")
    print(result)

asyncio.run(main())
```

```python
# 실행 결과
Hello, Alice!
```

### `await`

`await` 키워드는 비동기 함수 내에서 다른 비동기 작업의 완료를 기다릴 때 사용합니다. `await`는 오직 `async def`로 정의된 함수 내에서만 사용할 수 있습니다.

```python
# 사용 예시
result = await 비동기_함수_또는_코루틴()
```

```python
import asyncio
import random

async def fetch_data(url):
    print(f"Fetching data from {url}")
    await asyncio.sleep(random.uniform(0.5, 2))  # 네트워크 지연 시뮬레이션
    return f"Data from {url}"

async def process_data(data):
    print(f"Processing {data}")
    await asyncio.sleep(0.5)  # 처리 시간 시뮬레이션
    return f"Processed {data}"

async def fetch_and_process(url):
    data = await fetch_data(url)
    result = await process_data(data)
    return result

async def main():
    urls = ['http://example.com', 'http://example.org', 'http://example.net']
    tasks = [fetch_and_process(url) for url in urls]
    results = await asyncio.gather(*tasks)
    
    for result in results:
        print(result)

asyncio.run(main())
```

```python
# 실행 결과
Fetching data from http://example.com
Fetching data from http://example.org
Fetching data from http://example.net
Processing Data from http://example.org
Processing Data from http://example.com
Processing Data from http://example.net
Processed Data from http://example.com
Processed Data from http://example.org
Processed Data from http://example.net
```

- `await fetch_data(url)`: `fetch_data()` 함수의 완료를 기다림
- `await process_data(data)`: `process_data()` 함수의 완료를 기다림
- `await asyncio.gather(*tasks)`: 여러 비동기 작업을 동시에 실행하고 결과를 모음

## 동시성 처리

### `asyncio.gather()`

여러 코루틴을 동시에 실행하고 모든 결과를 기다릴 때 사용합니다. 여러 비동기 작업을 병렬로 실행하고 그 결과를 모아줍니다.

```python
import asyncio
import time

async def fetch_data(name, delay):
    print(f"{name} 시작")
    await asyncio.sleep(delay)  # 네트워크 요청 시뮬레이션
    print(f"{name} 완료")
    return f"{name}의 결과"

async def main():
    start_time = time.time()

    # asyncio.gather()를 사용하여 여러 작업을 동시에 실행
    results = await asyncio.gather(
        fetch_data("Task 1", 3),
        fetch_data("Task 2", 2),
        fetch_data("Task 3", 4)
    )

    end_time = time.time()
    print(f"모든 작업 완료. 소요 시간: {end_time - start_time:.2f}초")
    print("결과:", results)

asyncio.run(main())
```

```python
# 실행 결과
Task 1 시작
Task 2 시작
Task 3 시작
Task 2 완료
Task 1 완료
Task 3 완료
모든 작업 완료. 소요 시간: 4.02초
결과: ['Task 1의 결과', 'Task 2의 결과', 'Task 3의 결과']
```

- `asyncio.gather()`는 세 개의 fetch_data 코루틴을 동시에 실행하고 가장 오래 걸리는 작업(4초)에 맞춰 전체 실행 시간이 결정

### `asyncio.create_task()`

코루틴을 Task 객체로 감싸서 이벤트 루프에 예약합니다. 이를 통해 백그라운드에서 작업을 실행하고 필요할 때 결과를 확인할 수 있습니다.

```python
import asyncio
import time

async def long_running_task(name, delay):
    print(f"{name} 시작")
    await asyncio.sleep(delay)
    print(f"{name} 완료")
    return f"{name}의 결과"

async def main():
    start_time = time.time()

    # 태스크 생성
    task1 = asyncio.create_task(long_running_task("Task 1", 3))
    task2 = asyncio.create_task(long_running_task("Task 2", 2))
    task3 = asyncio.create_task(long_running_task("Task 3", 4))

    # 다른 작업 수행
    print("다른 작업 수행 중...")
    await asyncio.sleep(1)
    print("다른 작업 완료")

    # 모든 태스크의 결과 대기
    results = await asyncio.gather(task1, task2, task3)

    end_time = time.time()
    print(f"모든 작업 완료. 소요 시간: {end_time - start_time:.2f}초")
    print("결과:", results)

asyncio.run(main())
```

```python
# 실행 결과
다른 작업 수행 중...
Task 1 시작
Task 2 시작
Task 3 시작
다른 작업 완료
Task 2 완료
Task 1 완료
Task 3 완료
모든 작업 완료. 소요 시간: 4.01초
결과: ['Task 1의 결과', 'Task 2의 결과', 'Task 3의 결과']
```

- `asyncio.create_task()`를 사용하여 세 개의 task를 생성하고 동시에 **즉시** 실행
- `asyncio.gather()`를 사용하여 모든 task의 결과를 기다림

## 비동기 스트림 처리

aiofiles를 이용한 파일 I/O와 aiohttp를 이용한 네트워크 요청

### `aiofiles`

비동기적으로 파일 I/O 작업을 수행할 수 있게 해주는 라이브러리로 파일 읽기/쓰기 작업을 비동기적으로 처리할 수 있어, I/O 바운드 작업 중에도 다른 작업을 수행할 수 있습니다.

```python
# python.exe -m pip install --upgrade pip
# pip install aiofiles
import asyncio
import aiofiles

async def read_file(filename):
    async with aiofiles.open(filename, mode='r') as f:
        contents = await f.read()
    return contents

async def write_file(filename, content):
    async with aiofiles.open(filename, mode='w') as f:
        await f.write(content)

async def process_file(input_file, output_file):
    content = await read_file(input_file)
    processed_content = content.upper()  # 간단한 처리: 모든 텍스트를 대문자로 변환
    await write_file(output_file, processed_content)

async def main():
    await write_file('input.txt', 'Hello, World!\nThis is a test file.')
    await process_file('input.txt', 'output.txt')
    result = await read_file('output.txt')
    print("Processed content:", result)

asyncio.run(main())
```

```python
# 실행 결과
Processed content: HELLO, WORLD!
THIS IS A TEST FILE.
# 'input.txt' 파일 생성 - Hello, World!\nThis is a test file.
# 'output.txt' 파일 생성 - HELLO, WORLD!\nTHIS IS A TEST FILE.
```

- `aiofiles.open()`: 파일을 비동기적으로 열기
- `await f.read()`: 파일 내용을 읽기

### `aiohttp`

비동기 HTTP 클라이언트/서버를 제공하는 라이브러리로 여러 네트워크 요청을 동시에 처리할 수 있습니다.

```python
import asyncio
import aiohttp

async def fetch_url(session, url):
    async with session.get(url) as response:
        return await response.text()

async def download_sites(sites):
    async with aiohttp.ClientSession() as session:
        tasks = []
        for url in sites:
            task = asyncio.create_task(fetch_url(session, url))
            tasks.append(task)
        results = await asyncio.gather(*tasks)
    return results

async def process_sites(sites):
    results = await download_sites(sites)
    for url, html in zip(sites, results):
        print(f"{url}: {len(html)} characters")

async def main():
    sites = [
        "https://www.python.org",
        "https://docs.python.org",
        "https://pypi.org",
        "https://peps.python.org",
    ]
    await process_sites(sites)

asyncio.run(main())
```
  
```python
# 실행 결과
https://www.python.org: 51601 characters
https://docs.python.org: 17035 characters
https://pypi.org: 20304 characters
https://peps.python.org: 571517 characters
```

- `aiohttp.ClientSession()`: HTTP 클라이언트 세션을 생성
- `session.get(url)`: GET 요청을 보내고 응답을 받음
- `response.text()`: 응답 본문을 텍스트로 변환
- `asyncio.create_task()`: 비동기 작업을 Task로 감싸서 실행
- `asyncio.gather()`: 여러 비동기 작업을 동시에 실행하고 결과를 모음

### 'aiofiles'와 'aiohttp'를 함께 사용

```python
import asyncio
import aiohttp
import aiofiles
import os

async def download_and_save(session, url, folder):
    async with session.get(url) as response:
        filename = os.path.join(folder, url.split('/')[-1] + '.html')
        content = await response.text()
        async with aiofiles.open(filename, 'w', encoding='UTF8') as f:
            await f.write(content)
        return filename, len(content)

async def process_sites(sites, folder):
    async with aiohttp.ClientSession() as session:
        tasks = [asyncio.create_task(download_and_save(session, url, folder)) for url in sites]
        results = await asyncio.gather(*tasks)
    
    for filename, size in results:
        print(f"Downloaded {filename}: {size} characters")

async def main():
    sites = [
        "https://www.python.org",
        "https://docs.python.org",
        "https://pypi.org",
    ]
    folder = 'downloaded_sites'
    os.makedirs(folder, exist_ok=True)
    await process_sites(sites, folder)

asyncio.run(main())
```

```python
# 실행 결과
Downloaded downloaded_sites\www.python.org.html: 51601 characters
Downloaded downloaded_sites\docs.python.org.html: 17035 characters
Downloaded downloaded_sites\pypi.org.html: 20304 characters
# downloaded_sites 디렉토리에 3개의 html 파일이 생성됨
```

## 예외 처리와 디버깅

### 예외 처리

#### try/except 사용

```python
import asyncio

async def risky_operation():
    await asyncio.sleep(1)
    raise ValueError("This is a test error")

async def main():
    try:
        await risky_operation()
    except ValueError as e:
        print(f"Caught an error: {e}")

asyncio.run(main())
```

```python
# 실행 결과
Caught an error: This is a test error
```

#### asyncio.gather()에서의 예외 처리

```python
import asyncio

async def task1():
    await asyncio.sleep(1)
    raise ValueError("Error in task1")

async def task2():
    await asyncio.sleep(2)
    return "Task2 result"

async def main():
    try:
        results = await asyncio.gather(task1(), task2(), return_exceptions=True)
        for result in results:
            if isinstance(result, Exception):
                print(f"Task failed with error: {result}")
            else:
                print(f"Task succeeded with result: {result}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")

asyncio.run(main())
```

```python
# 실행 결과
Task failed with error: Error in task1
Task succeeded with result: Task2 result
```

### 디버깅

#### 디버그 모드 활성화

<https://docs.python.org/ko/3/library/asyncio-dev.html>

`asyncio.run(main(), debug=True)`로 디버그 모드를 활성화할 수 있습니다.

디버그 모드가 활성화 되면:

- asyncio는 기다리지 않은 코루틴을 검사하고 로그 합니다; 이것은 “잊힌 await” 함정을 완화합니다.
- 많은 스레드 안전하지 않은 asyncio API(loop.call_soon()과 loop.call_at() 메서드와 같은)가 잘못된 스레드에서 호출될 때 예외를 발생시킵니다.
- I/O 선택기의 실행 시간은 I/O 연산 수행에 너무 오래 걸리면 로그 됩니다.
- Callbacks taking longer than 100 milliseconds are logged. The loop.

> 일반 모드

```python
import asyncio

async def test():
    print("never scheduled")

async def main():
    test()

asyncio.run(main())
```

```python
# 실행 결과
xxxxx 7: RuntimeWarning: coroutine 'test' was never awaited
  test()
RuntimeWarning: Enable tracemalloc to get the object allocation traceback
```

> 디버그 모드

```python
import asyncio

async def test():
    print("never scheduled")

async def main():
    test()

asyncio.run(main(), debug=True)
```

```python
# 실행 결과
Coroutine created at (most recent call last)
  File "D:\10.dev\10.python\test_app\src\22.asyncio\main.py", line 9, in <module>
    asyncio.run(main(), debug=True)
  File "C:\Users\dev01\AppData\Local\Programs\Python\Python310\lib\asyncio\runners.py", line 44, in run
    return loop.run_until_complete(main)
  File "C:\Users\dev01\AppData\Local\Programs\Python\Python310\lib\asyncio\base_events.py", line 633, in run_until_complete
    self.run_forever()
  File "C:\Users\dev01\AppData\Local\Programs\Python\Python310\lib\asyncio\windows_events.py", line 321, in run_forever
    super().run_forever()
  File "C:\Users\dev01\AppData\Local\Programs\Python\Python310\lib\asyncio\base_events.py", line 600, in run_forever
    self._run_once()
  File "C:\Users\dev01\AppData\Local\Programs\Python\Python310\lib\asyncio\base_events.py", line 1888, in _run_once
    handle._run()
  File "C:\Users\dev01\AppData\Local\Programs\Python\Python310\lib\asyncio\events.py", line 80, in _run
    self._context.run(self._callback, *self._args)
  File "D:\10.dev\10.python\test_app\src\22.asyncio\main.py", line 7, in main
    test()
  test()
RuntimeWarning: Enable tracemalloc to get the object allocation traceback
```

#### logging

logging 모듈을 사용하여 비동기 코드에서 로그를 기록 및 출력할 수 있습니다.

```python
import asyncio
import logging

logging.basicConfig(level=logging.DEBUG)

async def task(name):
    logging.debug(f"Task {name} starting")
    await asyncio.sleep(1)
    logging.debug(f"Task {name} completed")

async def main():
    await asyncio.gather(task("A"), task("B"), task("C"))

asyncio.run(main())
```

```python
# 실행 결과
DEBUG:asyncio:Using proactor: IocpProactor
DEBUG:root:Task A starting
DEBUG:root:Task B starting
DEBUG:root:Task C starting
DEBUG:root:Task A completed
DEBUG:root:Task B completed
DEBUG:root:Task C completed
```

#### asyncio.current_task()와 asyncio.all_tasks()

현재 실행 중인 Task를 가져오거나 모든 Task를 가져올 수 있습니다.

```python
import asyncio

async def task(name):
    print(f"Task {name} starting")
    await asyncio.sleep(1)
    print(f"Task {name} completed")

async def monitor():
    while True:
        current_task = asyncio.current_task()
        all_tasks = asyncio.all_tasks()
        print(f"Current task: {current_task.get_name()}")
        print(f"All tasks: {[t.get_name() for t in all_tasks]}")
        await asyncio.sleep(0.5)

async def main():
    monitor_task = asyncio.create_task(monitor(), name="Monitor")
    await asyncio.gather(
        task("A"),
        task("B"),
        task("C")
    )
    monitor_task.cancel()

asyncio.run(main())
```

```python
# 실행 결과
Current task: Monitor
All tasks: ['Task-1', 'Monitor', 'Task-5', 'Task-4', 'Task-3']
Task A starting
Task B starting
Task C starting
Current task: Monitor
All tasks: ['Task-1', 'Monitor', 'Task-5', 'Task-4', 'Task-3']
Task B completed
Task C completed
Task A completed
Current task: Monitor
All tasks: ['Task-1', 'Monitor']
```

- `asyncio.current_task()`: 현재 실행 중인 Task를 가져옴
- `asyncio.all_tasks()`: 모든 Task를 가져옴

---

해시태그: #Python #비동기 #asyncio #코루틴(Coroutine) #async #await #이벤트루프(Event Loop) #Tasks #Futures #gather #create_task #예외처리 #디버깅

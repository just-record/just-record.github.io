---
layout: single
title: "OpenAI API - 04. Assistants" 
categories: OpenAI-API
tag: [OpenAI-API, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

수정 일자: 2024-04-26

출처: <https://platform.openai.com/docs/assistants/overview/agents>

OpenAI의 공식 문서를 거의 그대로 옮긴 것입니다. Python 기준으로 간단하게 작성하였습니다.

OpenAI의 API를 사용하면 OpenAI에서 제공하는 [웹 사이트](https://chat.openai.com)가 아닌 다른 곳에서 OpenAI의 인공지능 기능을 사용할 수 있습니다. 개발하고자 하는 서비스에 OpenAI의 인공지능 기능을 추가 할 수 있습니다.

OpenAI API 연습 코드: <https://github.com/just-record/openai_api>

문서를 작성하는 동안 Assistant API의 버전이 업데이트 되었습니다. 'V2'로 수정하였습니다.

## Assistants - Overview

<https://platform.openai.com/docs/assistants/overview/overview>

Assistants API를 사용하면 자신의 응용 프로그램에 AI 도우미를 구축할 수 있습니다. 어시스턴트에는 지침이 있으며 모델, 도구 및 지식을 활용하여 사용자 쿼리에 응답할 수 있습니다. Assistants API는 현재 코드 해석기, 검색 및 함수 호출의 세 가지 유형의 도구를 지원합니다.

일반적인 통합 흐름:

1. Assistant 생성: 사용자 지정 지침을 정의하고 모델을 선택하여 생성
2. Thread 생성: 사용자가 대화를 시작하면 새로운 Thread를 생성
3. Thread에 Message를 추가: 사용자자 질문 할 때 메시지 추가
4. Thread에서 Assistant 실행하면 모델과 도구를 호출하여 응답 생성

### 1단계: Assistant 생성 (Create An Assistant)

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()
  
assistant = client.beta.assistants.create(
  name="Math Tutor",
  instructions="You are a personal math tutor. Write and run code to answer math questions.",
  tools=[{"type": "code_interpreter"}],
  model="gpt-4-turbo-preview",
)

print(assistant)
```

- `name`: Assistant의 이름
- `instructions`: Assistant의 지침
- `tools`: Assistant이 사용할 도구 # code_interpreter, search, function_call
- `model`: Assistant이 사용할 모델

결과: Assistant객체가 출력됩니다. Assistant ID를 복사해 둡니다.

웹사이트에서 확인:

- <https://platform.openai.com/assistants>로 접속 (openai에 로그인 되어 있어야 합니다.)
- 로그인 한 계정에서 생성한 Assistant 개체 목록이 표시됩니다.
- Assistant를 클릭하면 Assistant의 상세 정보가 표시됩니다.
- 우측 상단의 `Playground`을 클릭하면 Assistant를 사용할 수 있는 환경이 표시됩니다.

웹 사이트에서 생성하기:

- <https://platform.openai.com/assistants>로 접속
- 우측 상단의 `Create`를 클릭

### 2단계: Thread 생성 (Create A Thread)

Thread는 사용자와 하나 이상의 Assistant 간의 대화를 나타냅니다. 사용자가 어시스턴트와 대화를 시작할 때 Thread를 생성합니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()
  
thread = client.beta.threads.create()

print(thread)
```

결과: Thread 객체가 출력됩니다. Thread ID를 복사해 둡니다.

### 3단계: Thread에 Message를 추가 (Add A Message To A Thread)

메시지의 내용은 모두 Thread의 Message 객체에 추가됩니다. Message에는 텍스트와 파일이 모두 포함될 수 있습니다. Thread에 추가 할 수 있는 Message 수는 제한이 없습니다. 모델의 context에 적합하지 않은 context는 잘라 냅니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

message = client.beta.threads.messages.create(
    # thread.id = 조금전 생성한 Thread ID
    thread_id=thread.id, # Thread_xxxxxxx
    role="user",
    content="I need to solve the equation `3x + 11 = 14`. Can you help me?"
)
```

결과: 오류 없이 실행되었으면 성공입니다.

### 4단계: 실행 생성 (Create a Run)

사용자의 모든 메시지가 Thread에 추가되면 Assistant 사용하여 Thread를 실행 할 수 있습니다. 실행을 생성 하면 Assistant가 관련된 모델 및 도구를 사용하여 응답을 생성 합니다. 이러한 응답은 Thread에 Message로 추가됩니다.

#### 스트리밍으로 (With Streaming)

Python 라이브러리는 스트리밍을 지원합니다.

```python
from typing_extensions import override
from openai import AssistantEventHandler
 
# First, we create a EventHandler class to define
# how we want to handle the events in the response stream.
 
class EventHandler(AssistantEventHandler):    
  @override
  def on_text_created(self, text) -> None:
    print(f"\nassistant > ", end="", flush=True)
      
  @override
  def on_text_delta(self, delta, snapshot):
    print(delta.value, end="", flush=True)
      
  def on_tool_call_created(self, tool_call):
    print(f"\nassistant > {tool_call.type}\n", flush=True)
  
  def on_tool_call_delta(self, delta, snapshot):
    if delta.type == 'code_interpreter':
      if delta.code_interpreter.input:
        print(delta.code_interpreter.input, end="", flush=True)
      if delta.code_interpreter.outputs:
        print(f"\n\noutput >", flush=True)
        for output in delta.code_interpreter.outputs:
          if output.type == "logs":
            print(f"\n{output.logs}", flush=True)
 
# Then, we use the `create_and_stream` SDK helper 
# with the `EventHandler` class to create the Run 
# and stream the response.
 
with client.beta.threads.runs.stream(
  thread_id=thread.id, # Thread_ID
  assistant_id=assistant.id, # Assistant_ID
  instructions="Please address the user as Jane Doe. The user has a premium account.",
  event_handler=EventHandler(),
) as stream:
  stream.until_done()
```

결과:

```plaintext

assistant > Sure, I can help you solve the equation \(3x + 11 = 14\). To solve for \(x\), we need to isolate \(x\) on one side of the equation. This involves two steps:

1. Subtracting 11 from both sides of the equation to eliminate the constant term on the side with \(x\).
2. Dividing both sides by 3 to solve for \(x\).

Let me do the calculations for you.
assistant > code_interpreter

from sympy import symbols, Eq, solve

# Define the symbols
x = symbols('x')

# Define the equation
equation = Eq(3*x + 11, 14)

# Solving the equation
solution = solve(equation, x)
solution

output >

[1]

assistant > The solution to the equation \(3x + 11 = 14\) is \(x = 1\).
```

위의 텍스트가 스트리밍으로 출력됩니다. 정답을 알려 주세요.

#### 스트리밍 없이 (Without Streaming)

위의 스트리밍 예제를 실행 하셨으면 Thread를 새로 생성하고 Message를 추가한 후 아래 코드를 실행 해야 합니다. Thread에 이전 Message가 저장되어 있으므로 혼란을 야기 할 수 있습니다.

Run(실행)은 비동기적으로 실행되므로 `status`가 'completed'가 되었는지 개체를 폴링하여 확인해야 합니다. 아래 코드는 편의를 위해 생성과 폴링을 모두 지원합니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

run = client.beta.threads.runs.create_and_poll(
  thread_id=thread.id,
  assistant_id=assistant.id,
  instructions="Please address the user as Jane Doe. The user has a premium account."
)

if run.status == 'completed': 
    messages = client.beta.threads.messages.list(
        thread_id=Tread.id
    )
    print(messages)
else:
    print(run.status)
```

결과:

```text
The solution to the equation (3x + 11 = 14) is (x = 1).

...

Yes, I can help you solve the equation (3x + 11 = 14).

To solve the equation for (x), we need to isolate (x) on one side of the equation. Here are the steps we'll follow:

1. Subtract 11 from both sides of the equation to get the term with (x) alone on one side.
2. Divide both sides of the new equation by 3 to solve for (x).


Let's perform these steps:"

...

I need to solve the equation `3x + 11 = 14`. Can you help me?
...
```

## 대화 이어 가기

질의를 하면 답변을 합니다. 종료하기 전까지 대화를 이어 갑니다. 이전 질의를 기억하고 있습니다.

`/q`를 입력하면 대화를 종료합니다.

### Streamimg 없이

`main.py`: 실행 파일

```python
from  dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI


# Assistant 생성
def get_assistant(client: OpenAI):
    return client.beta.assistants.create(
        name="Math Tutor",
        instructions="You are a personal math tutor. Write and run code to answer math questions.",
        tools=[{"type": "code_interpreter"}],
        model="gpt-4-turbo-2024-04-09",
    )


# Thread 생성
def get_thread(client: OpenAI):
    return client.beta.threads.create()


# Message 추가
def add_message_to_thread(client: OpenAI, thread_id: str, message: str, role: str = "user"):
    client.beta.threads.messages.create(
        thread_id=thread_id,
        role=role,
        content=message,
    )


# 사용자에게 질문 받기 (/q 입력시 종료)
def get_user_query():
    return input("Enter a math question(Enter '/q' to quit): ")


# Run 생성
def create_run(client: OpenAI, thread_id: str, assistant_id: str):
    return client.beta.threads.runs.create(
        thread_id=thread_id,
        assistant_id=assistant_id
    )


# Run 조회
def get_run(client: OpenAI, thread_id: str, run_id: str):
    return client.beta.threads.runs.retrieve(
        thread_id=thread_id,
        run_id=run_id
    )


# 모니터링 하다가 완료되면 Run 가져오기
def get_completed_run(client: OpenAI, thread_id: str, run_id: str):
    run = get_run(client, thread_id, run_id)
    while run.status == "queued" or run.status == "in_progress":
        run = get_run(client, thread_id, run_id)
    return run


# Assistant 메시지 출력
def print_assistant_messages(client: OpenAI, thread_id: str):
    # thread_messages = client.beta.threads.messages.list(thread_id, order='asc')
    message_history = []
    thread_messages = client.beta.threads.messages.list(thread_id, order='desc')
    datas = thread_messages.data
    for data in datas:
        if data.role == 'assistant':
            for content in data.content:
                if content.type == 'text' and content.text.value:
                    message_history.append(f'Assistant> {content.text.value}')
                elif content.type == 'image_file' and content.image_file.file_id:
                    message_history.append(f'Assistant> {content.image_file.file_id}')
        else:
            break
    
    for message in message_history[::-1]:
        print(message)


# Assistant 삭제
def delete_assistant(client: OpenAI, assistant_id: str):
    client.beta.assistants.delete(assistant_id)

# Thread 삭제
def delete_thread(client: OpenAI, thread_id: str):
    client.beta.threads.delete(thread_id)        


if __name__ == '__main__':
    client = OpenAI()
    assistant = get_assistant(client)   # assistant 생성
    thread = get_thread(client)         # thread 생성
    
    while True:     # 대화 계속 하기
        message = get_user_query()  # "I need to solve the equation `3x + 11 = 14`. Can you help me?"
        role = "user"
        if message == "/q":         # /q 입력시 종료    
            print("Quitting...")
            break
        else:
            add_message_to_thread(client=client, thread_id=thread.id, message=message, role=role)  # message 추가
            print(f'You> {message}')
            run = create_run(client, thread.id, assistant.id)   # run 생성
            run = get_completed_run(client, thread.id, run.id)  # run 완료되면 가져오기

            # Run의 상태가 정상적으로 완료되면 메시지 출력
            if run.status == "completed":
                print(print_assistant_messages(client, thread.id))
            else:
                print("There is a problem, please try again.")

    delete_assistant(client, assistant.id)  # Assistant 삭제        
    delete_thread(client, thread.id)    # Thread 삭제
```

### Streamming으로

`event_handler.py`: 이벤트 핸들러 입니다.

```python
from typing_extensions import override
from openai import AssistantEventHandler
 
class EventHandler(AssistantEventHandler):    
    @override
    def on_text_created(self, text) -> None:
        print(f"\nassistant > ", end="", flush=True)
      
    @override
    def on_text_delta(self, delta, snapshot):
        print(delta.value, end="", flush=True)
      
    def on_tool_call_created(self, tool_call):
        print(f"\nassistant > {tool_call.type}\n", flush=True)
  
    def on_tool_call_delta(self, delta, snapshot):
        if delta.type == 'code_interpreter':
            if delta.code_interpreter.input:
                print(delta.code_interpreter.input, end="", flush=True)
            if delta.code_interpreter.outputs:
                print(f"\n\noutput >", flush=True)
                for output in delta.code_interpreter.outputs:
                    if output.type == "logs":
                        print(f"\n{output.logs}", flush=True)
```

`main_stream.py`: 실행 파일

```python
from  dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI
from event_handler import EventHandler


def get_assistant(client: OpenAI):
    return client.beta.assistants.create(
        name="Math Tutor",
        instructions="You are a personal math tutor. Write and run code to answer math questions.",
        tools=[{"type": "code_interpreter"}],
        model="gpt-4-turbo-2024-04-09",
    )


def get_thread(client: OpenAI):
    return client.beta.threads.create()


def add_message_to_thread(client: OpenAI, thread_id: str, message: str, role: str = "user"):
    client.beta.threads.messages.create(
        thread_id=thread_id,
        role=role,
        content=message,
    )


def get_user_query():
    return input("\nEnter a math question(Enter '/q' to quit): ")


def delete_assistant(client: OpenAI, assistant_id: str):
    client.beta.assistants.delete(assistant_id)


def delete_thread(client: OpenAI, thread_id: str):
    client.beta.threads.delete(thread_id)        


if __name__ == '__main__':
    client = OpenAI()
    assistant = get_assistant(client)
    thread = get_thread(client)
    
    while True:
        message = get_user_query()  # "I need to solve the equation `3x + 11 = 14`. Can you help me?"
        role = "user"
        if message == "/q":
            print("Quitting...")
            break
        else:
            add_message_to_thread(client=client, thread_id=thread.id, message=message, role=role)
            print(f'You> {message}')

            # stream 생성
            with client.beta.threads.runs.stream(
                thread_id=thread.id,
                assistant_id=assistant.id,
                event_handler=EventHandler(),
            ) as stream:
                stream.until_done()

    delete_assistant(client, assistant.id)
    delete_thread(client, thread.id)
```

## 그 외

아래는 위의 github 주소를 링크한 것 입니다.

### Tool('File Search')을 사용 하여 파일 내용 질의 하기

<https://github.com/just-record/openai_api/tree/main/12.assistants-file-search>

### Tool('Code Interpreter')을 사용 하여 코드 생성 및 코드 실행하기

<https://github.com/just-record/openai_api/tree/main/13.assistants-code-interpreter>

### Tool('Function Calling')을 사용 하여 사용자 함수 호출하기

<https://github.com/just-record/openai_api/tree/main/14.assistants-functions-calling>

### File Search 연습

- Assistant와 Thread, Message(File) 각각 Vector Store를 생성하기

<https://github.com/just-record/openai_api/tree/main/20.file_search_vector_store>

### Code Interpreter 연습

- 답변에 이미지가 포함된 경우
- 답변에 text(예: CSV)가 포함된 경우
- Code Interpreter의 input과 output을 출력하기

<https://github.com/just-record/openai_api/tree/main/21.code_interpreter>

### Function Calling 연습

- 사용자 함수 호출하기

<https://github.com/just-record/openai_api/tree/main/22.function_calling>

---

해시태그: #OpenAI #API #Python #assistants #code_interpreter #file_search #function_calling

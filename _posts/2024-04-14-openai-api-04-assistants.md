---
layout: single
title: "OpenAI API - 04. Assistants" 
categories: OpenAI-API
tag: [OpenAI-API, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

수정 일자: 2024-04-10

출처: <https://platform.openai.com/docs/api-reference>

OpenAI의 공식 문서를 거의 그대로 옮긴 것입니다. Python 기준으로 간단하게 작성하였습니다.

OpenAI의 API를 사용하면 OpenAI에서 제공하는 [웹 사이트](https://chat.openai.com)가 아닌 다른 곳에서 OpenAI의 인공지능 기능을 사용할 수 있습니다. 개발하고자 하는 서비스에 OpenAI의 인공지능 기능을 추가 할 수 있습니다.

OpenAI API 연습 코드: <https://github.com/just-record/openai_api>

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

## Assistants - 작동 방식

<https://platform.openai.com/docs/assistants/how-it-works/agents>: Assistant의 작동 방식을 설명하고 있습니다.

<https://platform.openai.com/docs/assistants/how-it-works/objects>: Assistant의 객체에 대한 설명입니다.

<https://platform.openai.com/docs/assistants/how-it-works/creating-assistants>: Assistant를 생성하는 방법에 대한 설명입니다.

<https://platform.openai.com/docs/assistants/how-it-works/managing-threads-and-messages>: Thread와 Message를 관리하는 방법에 대한 설명입니다.

<https://platform.openai.com/docs/assistants/how-it-works/runs-and-run-steps>: Run과 Run Steps에 대한 설명입니다.

<https://platform.openai.com/docs/assistants/how-it-works/limitations>: Assistant의 제한 사항에 대한 설명입니다.

## Assistants - Tools

<https://platform.openai.com/docs/assistants/tools/tools-beta>: Assistant에서 사용할 수 있는 tool에 대한 설명입니다.

<https://platform.openai.com/docs/assistants/tools/code-interpreter>: Code Interpreter에 대한 설명입니다.

<https://platform.openai.com/docs/assistants/tools/knowledge-retrieval>: Knowledge Retrieval에 대한 설명입니다.

<https://platform.openai.com/docs/assistants/tools/function-calling>: Function Calling에 대한 설명입니다.

<https://platform.openai.com/docs/assistants/tools/supported-files>: tool에서 지원하는 파일 형식에 대한 설명입니다.

---

## API

### Assistants - API

<https://platform.openai.com/docs/api-reference/assistants/createAssistant>: Assistant를 생성하는 API

<https://platform.openai.com/docs/api-reference/assistants/createAssistantFile>: Assistant에 파일을 추가하는 API

<https://platform.openai.com/docs/api-reference/assistants/listAssistants>: Assistant 목록을 가져오는 API

<https://platform.openai.com/docs/api-reference/assistants/listAssistantFiles>: Assistant의 파일 목록을 가져오는 API

<https://platform.openai.com/docs/api-reference/assistants/getAssistant>: Assistant를 검색하는 API

<https://platform.openai.com/docs/api-reference/assistants/getAssistantFile>: Assistant의 파일을 검색하는 API

<https://platform.openai.com/docs/api-reference/assistants/modifyAssistant>: Assistant를 수정하는 API

<https://platform.openai.com/docs/api-reference/assistants/deleteAssistant>: Assistant를 삭제하는 API

<https://platform.openai.com/docs/api-reference/assistants/deleteAssistantFile>: Assistant의 파일을 삭제하는 API

<https://platform.openai.com/docs/api-reference/assistants/object>: Assistant 객체

<https://platform.openai.com/docs/api-reference/assistants/object>: Assistant 파일 객체

### Threads - API

<https://platform.openai.com/docs/api-reference/threads/createThread>: Thread를 생성하는 API

<https://platform.openai.com/docs/api-reference/threads/getThread>: Thread를 검색하는 API

<https://platform.openai.com/docs/api-reference/threads/modifyThread>: Thread를 수정하는 API

<https://platform.openai.com/docs/api-reference/threads/deleteThread>: Thread를 삭제하는 API

<https://platform.openai.com/docs/api-reference/threads/object>: Thread 객체

### Messages - API

<https://platform.openai.com/docs/api-reference/messages/createMessage>: Message를 생성하는 API

<https://platform.openai.com/docs/api-reference/messages/listMessages>: Message 목록을 가져오는 API

<https://platform.openai.com/docs/api-reference/messages/listMessageFiles>: Message의 파일 목록을 가져오는 API

<https://platform.openai.com/docs/api-reference/messages/getMessage>: Message를 검색하는 API

<https://platform.openai.com/docs/api-reference/messages/getMessageFile>: Message의 파일을 검색하는 API

<https://platform.openai.com/docs/api-reference/messages/modifyMessage>: Message를 수정하는 API

<https://platform.openai.com/docs/api-reference/messages/object>: Message 객체

<https://platform.openai.com/docs/api-reference/messages/file-object>: Message 파일 객체

### Runs - API

<https://platform.openai.com/docs/api-reference/runs/createRun>: Run을 생성하는 API

<https://platform.openai.com/docs/api-reference/runs/createThreadAndRun>: Thread를 생성하고 하나의 요청으로 Run을 생성하는 API

<https://platform.openai.com/docs/api-reference/runs/listRuns>: Run 목록을 가져오는 API

<https://platform.openai.com/docs/api-reference/runs/listRunSteps>: 특정 Run에 속한 Steps 목록을 가져오는 API

<https://platform.openai.com/docs/api-reference/runs/getRun>: Run을 검색하는 API

<https://platform.openai.com/docs/api-reference/runs/getRunStep>: Run의 Step을 검색하는 API

<https://platform.openai.com/docs/api-reference/runs/modifyRun>: Run을 수정하는 API

<https://platform.openai.com/docs/api-reference/runs/submitToolOutputs>: Tool의 출력을 제출하는 API

<https://platform.openai.com/docs/api-reference/runs/cancelRun>: Run을 취소하는 API

<https://platform.openai.com/docs/api-reference/runs/object>: Run 객체

<https://platform.openai.com/docs/api-reference/runs/step-object>: Run Step 객체

### Streaming - API

<https://platform.openai.com/docs/api-reference/assistants-streaming/message-delta-object>: Message Delta 객체

<https://platform.openai.com/docs/api-reference/assistants-streaming/run-step-delta-object>: Run Step Delta 객체

<https://platform.openai.com/docs/api-reference/assistants-streaming/events>: Run을 streaming 할 때 발생하는 이벤트

---

해시태그: #OpenAI #API #Python #assistants #api

---
layout: single
title: "Gradio - 01. Getting Started" 
categories: Gradio
tag: [Gradio]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Gradio란?

<https://www.gradio.app/>

<https://www.gradio.app/guides/quickstart>

- 오픈소스 Python 패키지
- 머신러닝 모델, API 또는 임의의 Python 함수에 대한 데모나 웹 애플리케이션을 신속하게 구축
- 내장된 공유 기능으로 몇 초 만에 공개 링크 생성
- JavaScript, CSS, 또는 웹 호스팅 경험이 필요 없음

## 설치

- Python 3.10 또는 그 이상이 필요
- 가상환경에서 설치 권장: <https://www.gradio.app/main/guides/installing-gradio-in-a-virtual-environment>

```bash
# 가상환경 생성 - linux
# python -m venv gradio-env
# source gradio-env/bin/activate
pip install gradio
```

## 첫 번째 데모 구축

`app.py`

```python
import gradio as gr

def greet(name, intensity):
    return "Hello, " + name + "!" * int(intensity)

demo = gr.Interface(
    fn=greet,
    inputs=["text", "slider"],
    outputs=["text"],
)

demo.launch()
```

✔️ 실행
  
```bash
python app.py
```

✔️ 결과: <http://127.0.0.1:7860> 에서 확인

`name`입, `intensity`를 입력하고 `Submit` 버튼을 클릭하면 결과가 출력됨

### Hot Reload 사용

`pytyon` 대신 `gradido` 명령어 사용

```bash
# python app.py
gradido app.py
```

자세한 내용: <https://www.gradio.app/guides/developing-faster-with-reload-mode>

### Interface Class의 이해

`gr.Interface class`: 데모를 구축하는 데 사용되는 핵심 클래스

✔️ 3개의 핵심 매개변수

- fn: 사용자 인터페이스(UI)를 감싸는 함수
- inputs: 입력에 사용할 Gradio component(s), component의 수는 함수의 인수 수와 일치
- outputs: 출력에 사용할 Gradio component(s), component의 수는 함수의 반환되는 값 수와 일치

✔️ fn

- fn 인수는 매우 유연하여 UI로 감싸려는 모든 파이썬 함수를 전달 가능.
- 위는 간단한 예제이며 음악 생성기부터 세금 계산기, 머신 러닝 모델의 예측 함수까지 모든 것을 전달 가능

✔️ inputs, oujtputs

- 입력 및 출력 인수에는 하나 이상의 Gradio 구성 요소가 필요
- 예: gr.Textbox(), gr.Image(), gr.HTML()
- Component의 이름을 문자열 또는 class의 인스턴스로 전달 가능
  - `("textbox")` or `gr.Textbox()`
- 함수의 인수가 2개 이상이면 순서대로 list로 전달
  - `["textbox", "slider"]`
  - 함수가 2개 이상의 인수를 반환 하는 경우도 마찬가지

자세한 내용: <https://www.gradio.app/main/guides/the-interface-class>

## 데모 공유

데모를 공유하기 위해 실행 시에 `share=True`로 설정, 누구나 데모에 액세스 가능한 공개 URL을 제공

```python
import gradio as gr

def greet(name):
    return "Hello " + name + "!"

demo = gr.Interface(fn=greet, inputs="textbox", outputs="textbox")
    
demo.launch(share=True)  # Share your demo with just 1 extra parameter 🚀
```

✔️ 실행
  
```bash
python app.py
```

✔️ 결과

```bash
* Running on local URL:  http://127.0.0.1:7860
* Running on public URL: https://a23dsf231adb.gradio.live
```

<https://a23dsf231adb.gradio.live>에서 결과 확인 가능

자세한 내용: <https://www.gradio.app/guides/sharing-your-app>

## 핵심 Gradio Classes

Interface(high-level) class 외의 핵심 클래스

### Custom Demos with gr.Blocks: `gr.Blocks`

- low-level 접근 방식을 제공 - 더 맞춤화 가능한 레이아웃과 데이터 흐름으로 웹 앱을 설계
- 페이지에서 컴포넌트가 나타나는 위치 제어, 다중 데이터 흐름 처리, 더 복잡한 상호작용(예: 출력이 다른 함수의 입력으로 사용될 수 있음) 관리, 사용자 상호작용에 기반한 컴포넌트의 속성/가시성 업데이트 등을 지원
- 모든 코드는 Python으로 가능
- 매우 맞춤화되고 복잡한 애플리케이션을 구축 - 예: 인기 있는 이미지 생성 도구인 Automatic1111 Web UI는 Gradio Blocks를 사용하여 구축

자세한 내용: <https://www.gradio.app/guides/blocks-and-event-listeners>

✔️ 예시

```python
import gradio as gr


def greet(name):
    return "Hello " + name + "!"


with gr.Blocks() as demo:
    name = gr.Textbox(label="Name")
    output = gr.Textbox(label="Output Box")
    greet_btn = gr.Button("Greet")
    greet_btn.click(fn=greet, inputs=name, outputs=output, api_name="greet")

demo.launch()
```

✔️ decorator를 사용하여 Event Listener를 추가하기

```python
import gradio as gr

with gr.Blocks() as demo:
    name = gr.Textbox(label="Name")
    output = gr.Textbox(label="Output Box")
    greet_btn = gr.Button("Greet")

    @greet_btn.click(inputs=name, outputs=output)
    def greet(name):
        return "Hello " + name + "!"

demo.launch()
```

### Chatbots with gr.ChatInterface: `gr.ChatInterface`

- 챗봇 UI를 생성하도록 특별히 설계된 고급 클래스
- 완전히 작동하는 챗봇 UI를 생성

자세한 내용: <https://www.gradio.app/guides/creating-a-chatbot-fast>

✔️ 예시

```python
##############################
# Yes or No 를 대답하는 챗봇
##############################
import random

def random_response(message, history):
    return random.choice(["Yes", "No"])
##############################


import gradio as gr

gr.ChatInterface(random_response, type="messages").launch() 
```

## OpenAI API를 적용한 챗봇 데모

OpenAI API 적용은 이전 포스트에서 참조하시길 바랍니다.

```python
from dotenv import load_dotenv
load_dotenv() # Load environment variables from .env file

import gradio as gr
from openai import OpenAI

client = OpenAI() # OpenAI API client

# Define a function that takes in a message and history and returns a response
def chatting(message, history):
    # print(f'message: {message}')
    # print(f'history: {history}')

    messages = history + [{'role': 'user', 'content': message}]
    
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages
    )

    return response.choices[0].message.content # OpenAI 모델의 답변

gr.ChatInterface(chatting, type="messages").launch() 
```

---

해시태그: #Gradio #설치 #데모 #Interface #Blocks #ChatInterface #OpenAI-API #챗봇

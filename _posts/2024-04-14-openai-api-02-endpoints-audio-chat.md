---
layout: single
title: "OpenAI API - 02. Endpoint (Audio, Chat)" 
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

Endpoint는 OpenAI API의 URL을 의미합니다. 각 Endpoint는 OpenAI의 서비스 중 하나를 나타냅니다. 예를 들어, `https://api.openai.com/v1/chat/completions`는 채팅을 완성하는 데 사용되는 Endpoint입니다.

OpenAI API 연습 코드: <https://github.com/just-record/openai_api>

## Audio

오디오를 Text로 변환하거나 Text를 오디오로 변환하는 API입니다.

### 음성 만들기 (Create Speech)

입력된 텍스트를 음성으로 변환합니다.

TTS 가이드: <https://platform.openai.com/docs/guides/text-to-speech>  
API: <https://platform.openai.com/docs/api-reference/audio/createSpeech>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

speech_file_path = "./speech.mp3"

response = client.audio.speech.create(
  model="tts-1",
  voice="alloy", # "echo", "fable", "onyx", "nova", "shimmer"
  input="The quick brown fox jumped over the lazy dog."
)

# stream_to_file는 deprecated 될 예정 이므로 write_to_file로 대체합니다.
response.write_to_file(speech_file_path)
```

- `model`: 사용할 TTS 모델(`tts-1`)입니다.
- `voice`: 사용할 음성입니다. 사용 가능한 음성은 `alloy`, `echo`, `fable`, `onyx`, `nova`, `shimmer`입니다.
- `input`: 변환할 텍스트입니다.

결과:

`speech.mp3` 파일이 생성되고, `The quick brown fox jumped over the lazy dog.` 문장이 음성으로 변환되어 저장됩니다.

한글 테스트:

```python
### 생략
response = openai.audio.speech.create(
  model="tts-1",
  voice="alloy",
  input="안녕하세요. 반갑습니다.",
  language="ko-KR"

)

response.write_to_file(speech_file_path)
```

한글도 정상적으로 음성으로 변환됩니다.

### 음성을 텍스트로 (Create Transcription)

음성 파일을 텍스트로 변환합니다.

STT 가이드: <https://platform.openai.com/docs/guides/speech-to-text>
API: <https://platform.openai.com/docs/api-reference/audio/createTranscription>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

audio_file_path = "./speech.mp3"
audio_file = open(audio_file_path, "rb")

transcript = client.audio.transcriptions.create(
  model="whisper-1",
  file=audio_file
)

print(transcript.text)
```

- `model`: 사용할 STT 모델(`whisper-1`)입니다.
- `file`: 변환할 오디오 파일입니다. (조금 전 생성한 `speech.mp3` 파일을 텍스트로 변환 또는 원하시는 오디오 파일을 사용하여 텍스트로 변환합니다.)

결과:

음성 파일을 텍스트로 변환한 결과를 출력합니다.

#### `Word timestamps`

`Word timestamps`는 각 단어의 시작 시간과 종료 시간을 제공합니다.

```python
from openai import OpenAI
from dotenv import load_dotenv # type: ignore
load_dotenv()

client = OpenAI()

audio_file = open("speech.mp3", "rb")
transcript = client.audio.transcriptions.create(
  file=audio_file,
  model="whisper-1",
  response_format="verbose_json",
  timestamp_granularities=["word"]
)

print(transcript.words)
```

결과:

```json
{
  "task": "transcribe",
  "language": "english",
  "duration": 8.470000267028809,
  "text": "The beach was a popular spot on a hot summer day. People were swimming in the ocean, building sandcastles, and playing beach volleyball.",
  "words": [
    {
      "word": "The",
      "start": 0.0,
      "end": 0.23999999463558197
    },
    ...
    {
      "word": "volleyball",
      "start": 7.400000095367432,
      "end": 7.900000095367432
    }
  ]
}
```

#### `Segment timestamps`

`Segment timestamps`는 각 문장의 시작 시간과 종료 시간을 제공합니다.

```python
audio_file = open("speech.mp3", "rb")
transcript = client.audio.transcriptions.create(
  file=audio_file,
  model="whisper-1",
  response_format="verbose_json",
  timestamp_granularities=["segment"]
)

print(transcript.words)
```

결과:

```json
{
  "task": "transcribe",
  "language": "english",
  "duration": 8.470000267028809,
  "text": "The beach was a popular spot on a hot summer day. People were swimming in the ocean, building sandcastles, and playing beach volleyball.",
  "words": [
    {
      "word": "The",
      "start": 0.0,
      "end": 0.23999999463558197
    },
    ...
    {
      "word": "volleyball",
      "start": 7.400000095367432,
      "end": 7.900000095367432
    }
  ]
}
```

### 번역 (Create translation)

다른 나라 언어로 녹음된 오디오를 영어로 번역한 텍스트를 생성합니다.

API: <https://platform.openai.com/docs/api-reference/audio/createTranslation>

```python
from openai import OpenAI
client = OpenAI()

audio_file = open("speech.mp3", "rb")
transcript = client.audio.translations.create(
  model="whisper-1",
  file=audio_file
)

print(transcript.to_dict())
```

- `model`: 사용할 번역 모델(`whisper-1`)입니다.
- `file`: 번역할 오디오 파일입니다.

**음~~ 번역이 안되는 것 같습니다. 한글로 녹음된 음성 파일 입니다. 좀 더 찾아보겠습니다.**

### Transcription 객체 (The transcription Object)

<https://platform.openai.com/docs/api-reference/audio/verbose-json-object>를 참조하세요.

## 채팅 (Chat)

### 채팅 완성 (Create Chat Completions)

가이드: <https://platform.openai.com/docs/guides/text-generation/chat-completions-api>  
API: <https://platform.openai.com/docs/api-reference/chat/create>  
예제 코드: <https://cookbook.openai.com/examples/how_to_format_inputs_to_chatgpt_models>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo-0613",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Hello!"}
  ]
)

print(completion.choices[0].message.content)
```

- `model`: 사용할 채팅 모델(`gpt-3.5-turbo-0613`)입니다.
- `role`: 대화 역할입니다. `system`은 시스템, `user`는 사용자입니다. `assistant`는 챗봇(인공지능)입니다.

결과:

```text
Hello! How can I assist you today?
```

Response 샘플:

```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "model": "gpt-3.5-turbo-0613",
  "system_fingerprint": "fp_44709d6fcb",
  "choices": [{
    "index": 0,
    "message": {
      "role": "assistant",
      "content": "\n\nHello there, how may I assist you today?",
    },
    "logprobs": null,
    "finish_reason": "stop"
  }],
  "usage": {
    "prompt_tokens": 9,
    "completion_tokens": 12,
    "total_tokens": 21
  }
}
```

- `choices`: 모델의 답변
  - `index`: 선택된 답변의 인덱스
  - `message`: 답변 내용
    - `content`: 답변 내용
    - `role`: 대화의 역할
- `usage`: 사용량
  - `completion_tokens`: 답변의 토큰 수
  - `prompt_tokens`: 입력의 토큰 수
  - `total_tokens`: 전체 토큰 수

#### 이전 대화 내용 알려 주기

인공지능은 조금 전에 대화한 내용을 기억하지 못합니다. 이전의 대화 내용을 알려 주어야 합니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo-0613",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Hello! My name is Hong Gildong."},
    {"role": "system", "content": "Hello! How can I assist you today?"},
    {"role": "user", "content": "What is my name?."}
  ]
)

print(completion.choices[0].message.content)
```

결과:

```text
Your name is Hong Gildong
```

#### System role

system role을 사용하여 대화의 지침을 설정할 수 있습니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo-0613",
  messages=[
    {"role": "system", "content": "Please translate it into English."},
    {"role": "user", "content": "안녕하세요. 반갑습니다."}
  ]
)

print(completion.choices[0].message.content)
```

결과:

```text
Hello. Nice to meet you.
```

#### 여러 개의 답변을 생성

여러 개의 답변을 생성할 수 있습니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo-0613",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "너가 가장 잘 하는 걸 한 줄로 말해줘."},
  ],
  n=3 # 3개의 답변
)

print(f'답변 개수: {len(completion.choices)}')

for i in range(len(completion.choices)):
  print(f'답변 {i+1}: {completion.choices[i].message.content}')
```

결과:

```text
답변 개수: 3
답변 1: 내 강점은 다양한 주제에 관한 정보를 신속하고 정확하게 제공하는 것입니다.
답변 2: 질문에 최대한 정확하고 빠르게 답변해 드립니다.
답변 3: 사용자를 도와주는 것!
```

#### Token 사용량

사용한 토큰의 양을 확인할 수 있습니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo-0613",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "너가 가장 잘 하는 걸 한 줄로 말해줘."},
  ],
  n=3
)

print(completion.usage.to_dict())
```

결과:

```text
{'completion_tokens': 60, 'prompt_tokens': 38, 'total_tokens': 98}
```

- `completion_tokens`: 답변 생성(output)에 사용한 토큰의 양
- `prompt_tokens`: 질문(input)에 사용한 토큰의 양
- `total_tokens`: 사용한 총 토큰의 양

#### Image input

이미지를 입력으로 사용할 수 있습니다.

가이드: <https://platform.openai.com/docs/guides/vision>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4-turbo-2024-04-09",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "What's in this image? Please answer me in Korean."},
                {
                    "type": "image_url",
                    "image_url": {"url": "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Gfp-wisconsin-madison-the-nature-boardwalk.jpg/2560px-Gfp-wisconsin-madison-the-nature-boardwalk.jpg"},
                },
            ],
        }
    ],
    max_tokens=300,
)

print(response.choices[0].message.content)
```

```text
이 이미지는 푸른 하늘 아래, 들판을 가로질러 나 있는 나무 데크 길을 보여줍니다. 이 길은 풍부한 녹색 풀밭을 관통하고 있으며, 주변에는 수풀과 나무들이 보입니다. 맑은 날씨와 자연의 풍경이 조화롭게 어우러져 평화로운 분위기를 연출하고 있습니다.
```

#### Image input (base64)

내 컴퓨터의 이미지를 Base64로 인코딩하여 질의할 수 있습니다.

`otter.png`:
![otter.png 다운로드]({{site.url}}/images/openai-api/openai-api-03_01.png)

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI
import base64

def encode_image(image_path):
  with open(image_path, "rb") as image_file:
    return base64.b64encode(image_file.read()).decode('utf-8')

image_path = "otter.png" # 이미지 파일 경로
base64_image = encode_image(image_path)

client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4-turbo-2024-04-09",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "What's in this image? Please answer me in Korean."},
                {
                    "type": "image_url",
                    "image_url": {"url": f"data:image/jpeg;base64,{base64_image}"},
                },
            ],
        }
    ],
    max_tokens=300,
)

print(response.choices[0].message.content)
```

결과:

```text
이 이미지에는 물에서 헤엄치고 있는 아주 귀여운 수달 한 마리가 나와 있습니다. 수달은 명랑한 표정을 짓고 있으며, 물결과 햇빛이 반짝이는 배경과 함께 매우 평화로워 보입니다.
```

#### 다중 이미지 질의

여러 개의 이미지를 질의합니다. 하나는 `otter.png` 파일을 업로드 하고 다른 하나는 위에서 연습 한 인터넷 상의 이미지 URL을 사용합니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
import base64
import requests # type: ignore
import os

api_key = os.getenv("OPENAI_API_KEY")

def encode_image(image_path):
  with open(image_path, "rb") as image_file:
    return base64.b64encode(image_file.read()).decode('utf-8')

image_path = "otter.png"

base64_image = encode_image(image_path)

headers = {
  "Content-Type": "application/json",
  "Authorization": f"Bearer {api_key}"
}

payload = {
  "model": "gpt-4-turbo-2024-04-09",
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "What’s in this image? Please answer me in Korean."
        },
        {
          "type": "image_url",
          "image_url": {
            "url": f"data:image/jpeg;base64,{base64_image}"
          }
        }
      ]
    }
  ],
  "max_tokens": 300
}

response = requests.post("https://api.openai.com/v1/chat/completions", headers=headers, json=payload)
print(response.json()['choices'][0]['message']['content'])
```

결과:

```text
첫 번째 이미지는 물 위에 떠 있는 귀여운 수달의 일러스트레이션을 보여줍니다. 수달의 눈이 밝고 표정이 친근감 있게 묘사되어 있습니다.

두 번째 이미지는 넓은 초록색 잔디밭과 하늘을 배경으로 한 목재 데크 길이 있는 풍경 사진입니다. 이 길은 자연 속을 산책하거나 탐험하는 데 이상적인 경치를 제공하고 있습니다.

두 이미지 사이의 가장 큰 차이점은 첫 번째는 애니메이션으로 제작된 동물의 초상화이고, 두 번째는 실제 자연 풍경의 사진이라는 점입니다. 첫 번째 이미지는 예술적인 요소가 강조되어 있으며, 두 번째 이미지는  
자연 그대로의 아름다움을 보여 줍니다.
```

#### Streaming

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

stream = client.chat.completions.create(
    model="gpt-3.5-turbo-0613",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello! Give me a long answer."}
    ],
    stream=True # Stream the response
)

# 결과1
for chunk in stream:
    if chunk.choices[0].delta.content is not None:
        print(chunk.choices[0].delta.content, end="", flush=True) # flush=True is important for streaming

# 결과2
# for chunk in completion:
#   print(chunk.choices[0].delta.model_dump_json())
```

결과1: 텍스트가 Streaming 되어 출력됩니다.

결과2:

```python
{'content': '', 'role': 'assistant'}
{'content': 'Hello'}
{'content': '!'}
{'content': ' How'}
{'content': ' can'}
{'content': ' I'}
{'content': ' assist'}
{'content': ' you'}
{'content': ' today'}
{'content': '?'}
{}
```

#### 함수 (Functions)

tools를 이용하여 함수를 호출하는 데 사용할 수 있는 JSON을 생성합니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI
import random
import json

# 호출할 함수 정의
def get_current_weather(location: str, unit: str = "celsius"):
    temperature = random.randint(0, 100)
    if not unit:
        unit = "celsius"
    return f"The current weather in {location} is {temperature} degrees {unit}."

client = OpenAI()

# AI가 tool을 사용 할 경우 반환 할 함수를 정의
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_current_weather", # 함수 이름
            # 함수 설명: 아주 중요함, 이 설명을 참조 하여 AI가 이 tool을 사용할지 결정
            "description": "Get the current weather in a given location", 
            "parameters": { # 함수의 인자
                "type": "object",
                "properties": { # 인자의 속성
                    "location": { # 인자의 이름
                        "type": "string",
                        # 인자의 설명
                        "description": "The city and state, e.g. San Francisco, CA",
                    },
                    "unit": {
                        "type": "string", 
                        "description": "The unit of temperature to return. Default is celsius.",
                        "enum": ["celsius", "fahrenheit"]
                    }, # 인자의 이름
                },
                "required": ["location"], # 필수 인자
            },
        }
    }
]
messages = [{"role": "user", "content": "What's the weather like in Boston today?"}]
completion = client.chat.completions.create(
    model="gpt-3.5-turbo-0613",
    messages=messages,
    tools=tools,
    tool_choice="auto"
)

# 결과1
if completion.choices[0].message.tool_calls:
    tool_call = completion.choices[0].message.tool_calls[0]
    print(tool_call.function)
    if tool_call.function.name == "get_current_weather":
        arguments = json.loads(tool_call.function.arguments)
        location = arguments.get('location')
        unit = arguments.get('unit')
        response = get_current_weather(location=location, unit=unit)
        print(response)
else:
    if completion.choices[0].message.content:
        print(completion.choices[0].message.content)
    else:
        print("No response from the model.")

# 결과2
# print(completion)
```

결과1:

```python
The current weather in Boston, MA is 37 degrees celsius.
```

결과2 샘플:

```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion",
  "created": 1699896916,
  "model": "gpt-3.5-turbo-0125",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": null,
        "tool_calls": [
          {
            "id": "call_abc123",
            "type": "function",
            "function": {
              "name": "get_current_weather",
              "arguments": "{\n\"location\": \"Boston, MA\"\n}"
            }
          }
        ]
      },
      "logprobs": null,
      "finish_reason": "tool_calls"
    }
  ],
  "usage": {
    "prompt_tokens": 82,
    "completion_tokens": 17,
    "total_tokens": 99
  }
}
```

Function calling 가이드: <https://platform.openai.com/docs/guides/function-calling/function-calling>  
예제 코드: <https://cookbook.openai.com/examples/how_to_call_functions_with_chat_models>

#### Logprobs

logprobs를 사용하여 각 토큰의 확률을 출력합니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo-0613",
  messages=[
    {"role": "user", "content": "Hello!"}
  ],
  logprobs=True, # Return logprobs
  top_logprobs=2 # Return top 2 logprobs
)

print(completion.choices[0].message.content)
print('--------------------')
contents = completion.choices[0].logprobs.content
for i, content in enumerate(contents):
    # token: The token that was generated, logprob: The log-probability of the token
    print(f'{i+1} - token: {content.token}, logprob: {content.logprob}') 
    top_logprobs = content.top_logprobs
    # 확률이 높은 상위 2개의 token과 logprobs
    for j, top_logprob in enumerate(top_logprobs):
        print(f'    {j+1} - token: {top_logprob.token}, logprob: {top_logprob.logprob}')
    print('')
```

결과:

```text
Hello! How can I assist you today?
--------------------
1 - token: Hello, logprob: -0.31725305
    1 - token: Hello, logprob: -0.31725305
    2 - token: Hi, logprob: -1.3190403

2 - token: !, logprob: -0.02380986
    1 - token: !, logprob: -0.02380986
    2 - token:  there, logprob: -3.787621

3 - token:  How, logprob: -5.4669687e-05
    1 - token:  How, logprob: -5.4669687e-05
    2 - token: <|end|>, logprob: -10.953937

4 - token:  can, logprob: -0.015801601
    1 - token:  can, logprob: -0.015801601
    2 - token:  may, logprob: -4.161023

5 - token:  I, logprob: -3.7697225e-06
    1 - token:  I, logprob: -3.7697225e-06
    2 - token:  assist, logprob: -13.596657

6 - token:  assist, logprob: -0.04571125
    1 - token:  assist, logprob: -0.04571125
    2 - token:  help, logprob: -3.1089056

7 - token:  you, logprob: -5.4385737e-06
    1 - token:  you, logprob: -5.4385737e-06
    2 - token:  today, logprob: -12.807695

8 - token:  today, logprob: -0.0040071653
    1 - token:  today, logprob: -0.0040071653
    2 - token: ?, logprob: -5.5247097

9 - token: ?, logprob: -0.0008108172
    1 - token: ?, logprob: -0.0008108172
    2 - token: ?
, logprob: -7.184561
```

### 채팅 완성 객체 (The chat Completion Object)

<https://platform.openai.com/docs/api-reference/chat/object>

- `id`: 채팅 완성 객체의 고유 식별자
- `choices`: 모델의 답변 선택 목록(여러 개 일수 있음)
  - `finish_reason`: 토큰 생성을 중지한 이유
  - `index`: 생성된 답변의 인덱스
  - `message`: 생성된 답변 메세지
    - `content`: 생성된 답변 내용
    - `role`: 생성된 답변 작성자의 역할
    - `tool_call`: 모델에서 생성된 도구 호출
  - `logprobs`: 출력 토큰의 로그 확률
- `created`: 채팅 완성 객체가 생성된 시간(UNIX 시간)
- `model`: 사용된 모델
- `system_fingerprint`: 시스템 지문 - 모델이 실행하는 백엔드 구성
- `object`: 객체 유형 - 'chat.completetiom'
- `usage`: 사용량
  - `completion_tokens`: 생성된 답변의 토큰 수
  - `prompt_tokens`: 입력의 토큰 수
  - `total_tokens`: 전체 토큰 수

#### 채팅 완성 청크 객체 (The Chat Completion chunk Object)

<https://platform.openai.com/docs/api-reference/chat/streaming>를 참조하세요.

### JSON 모드 (JSON mode)

반드시 JSON 개체 형태로 반환하도록 모델에 지시를 해도 유효하지 않은 JSON 개체로 반환될 수 있습니다. 이를 방지하기 위해 `response_format`을 `{ "type": "json_object" }`으로 설정하면 JSON 개체로 구문 분석되는 문자열만 생성합니다. `gpt-4-turbo-preview` or `gpt-3.5-turbo-0125`에 사용 가능합니다.

중요 사항:

- JSON 모드를 사용하는 경우 항상 대화의 일부 메시지(예: 시스템 메시지)를 통해 JSON을 생성하도록 모델에 지시하세요. API는 문자열이 "JSON"컨텍스트 어딘가에 나타나지 않으면 오류를 발생시킵니다.
- 모델이 반환하는 메시지의 JSON은 생성이 초과되었거나 대화가 토큰 제한을 초과했음을 나타내는 finish_reasonis 인 경우 부분적(예: 잘림)일 수 있습니다. `stop`를 제외 하면 구문 분석된 JSON이 아닐 수 있습니다.
- JSON 모드는 출력이 유효하고 오류 없이 구문 분석된다는 것만 보장합니다.

finish_reason:

- stop: 정상적으로 완료된 메시지 (모델이 완전한 메시지를 반환했거나 stop 매개변수 또는 제공된 중지 시퀀스 중 하나에 의해 종료된 메시지)
- length: max_tokens 또는 prompt_tokens 제한으로 인해 불완전한 모델 출력
- function_call: 모델이 함수를 호출하기로 결정한 경우
- content_filter: 콘텐츠 필터의 플래그로 인해 콘텐츠가 누락된 경우
- null: API 응답이 아직 진행 중이거나 완료되지 않았습니다.

```python
from openai import OpenAI
from dotenv import load_dotenv # type: ignore
load_dotenv()

client = OpenAI()

response = client.chat.completions.create(
  model="gpt-3.5-turbo-0125",
  response_format={ "type": "json_object" },
  messages=[
    {"role": "system", "content": "You are a helpful assistant designed to output JSON."},
    {"role": "user", "content": "Who won the world series in 2020?"}
  ]
)
print(response.choices[0].message.content)
```

결과:

```json
{
    "winner": "Los Angeles Dodgers",
    "year": 2020
}
```

---

해시태그: #OpenAI #API #Python #audio #chat #endpoint #TTS #STT #chat-completions #function-calling #JSON-mode

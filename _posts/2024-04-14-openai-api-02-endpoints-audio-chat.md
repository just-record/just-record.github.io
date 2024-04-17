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

출처: <https://platform.openai.com/docs/api-reference/audio>

OpenAI의 공식 문서를 정리(그냥 제가 이해 할 수 있는 것만)한 것입니다. Python 기준으로 간단하게 작성하였습니다.

OpenAI의 API를 사용하면 OpenAI에서 제공하는 [웹 사이트](https://chat.openai.com)가 아닌 다른 곳에서 OpenAI의 인공지능 기능을 사용할 수 있습니다. 개발하고자 하는 서비스에 OpenAI의 인공지능 기능을 추가 할 수 있습니다.

Endpoint는 OpenAI API의 URL을 의미합니다. 각 Endpoint는 OpenAI의 서비스 중 하나를 나타냅니다. 예를 들어, `https://api.openai.com/v1/chat/completions`는 채팅을 완성하는 데 사용되는 Endpoint입니다.

## Audio

오디오를 Text로 변환하거나 Text를 오디오로 변환하는 API입니다.

### 음성 만들기 (Create Speech)

입력된 텍스트를 음성으로 변환합니다.

TTS 가이드: <https://platform.openai.com/docs/guides/text-to-speech>  
API: <https://platform.openai.com/docs/api-reference/audio/createSpeech>

```python
from pathlib import Path
import openai

speech_file_path = Path(__file__).parent / "speech.mp3"
response = openai.audio.speech.create(
  model="tts-1",
  voice="alloy", # "echo", "fable", "onyx", "nova", "shimmer"
  input="The quick brown fox jumped over the lazy dog."
)
response.stream_to_file(speech_file_path)
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

# stream_to_file는 deprecated 될 예정 이므로 write_to_file로 대체합니다.
response.write_to_file(speech_file_path)
```

한글도 정상적으로 음성으로 변환됩니다.

### 음성을 텍스트로 (Create Transcription)

음성 파일을 텍스트로 변환합니다.

STT 가이드: <https://platform.openai.com/docs/guides/speech-to-text>
API: <https://platform.openai.com/docs/api-reference/audio/createTranscription>

```python
from openai import OpenAI
from dotenv import load_dotenv # type: ignore
load_dotenv()

client = OpenAI()

audio_file = open("speech.mp3", "rb")
transcript = client.audio.transcriptions.create(
  model="whisper-1",
  file=audio_file
)

print(transcript.to_dict())
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
from openai import OpenAI
from dotenv import load_dotenv # type: ignore
load_dotenv()

client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Hello!"}
  ]
)

print(completion.choices[0].message)
```

- `model`: 사용할 채팅 모델(`gpt-3.5-turbo`)입니다.
- `role`: 대화 역할입니다. `system`은 시스템, `user`는 사용자입니다. `assistant`는 챗봇(인공지능)입니다.

Response 샘플:

```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "model": "gpt-3.5-turbo-0125",
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

#### Image input

이미지를 입력으로 사용할 수 있습니다.

가이드: <https://platform.openai.com/docs/guides/vision>

```python
from openai import OpenAI

client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4-turbo",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "What's in this image?"},
                {
                    "type": "image_url",
                    # 공식 문서의 코드를 수정하였습니다.
                    "image_url": {"url": "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Gfp-wisconsin-madison-the-nature-boardwalk.jpg/2560px-Gfp-wisconsin-madison-the-nature-boardwalk.jpg"},
                },
            ],
        }
    ],
    max_tokens=300,
)

print(response.choices[0].to_dict())
```

공식 문서의 코드는 오류가 발생 합니다. 위의 코드로 수정 하니 정상적으로 작동합니다.

결과:

```python
{
    'finish_reason': 'stop', 
    'index': 0, 
    'logprobs': None, 
    'message': 
    {
        'content': 'This image features a serene natural landscape dominated by lush, tall grasses under a vibrant blue sky adorned with scattered clouds. A wooden boardwalk extends through the center of the image, inviting viewers to explore deeper into the grassy field. On either side of the pathway, the landscape varies slightly in vegetation density and type, contributing to a rich tapestry of green hues. Toward the horizon, a line of bushes and small trees can be seen, adding a further layer of diversity to the vegetation. The overall mood of the image is tranquil and picturesque, capturing a beautiful day in a natural setting.', 
        'role': 'assistant'
    }
}
```

#### Streaming

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI
client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Hello!"}
  ],
  stream=True
)

for chunk in completion:
  print(chunk.choices[0].delta.to_dict())
```

결과:

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

client = OpenAI()

tools = [
  {
    "type": "function",
    "function": {
      "name": "get_current_weather",
      # description이 아주 중요 합니다. 모델이 이 tool을 선택 할지 말지를 결정합니다.
      "description": "Get the current weather in a given location",
      "parameters": {
        "type": "object",
        "properties": {
          "location": {
            "type": "string",
            "description": "The city and state, e.g. San Francisco, CA",
          },
          "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]},
        },
        "required": ["location"],
      },
    }
  }
]
messages = [{"role": "user", "content": "What's the weather like in Boston today?"}]
completion = client.chat.completions.create(
  model="gpt-3.5-turbo",
  messages=messages,
  tools=tools,
  tool_choice="auto"
)

print(completion)
```

결과 샘플:

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

출력 토큰의 로그 확률을 반환 할지의 여부를 설정합니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI
client = OpenAI()

completion = client.chat.completions.create(
  model="gpt-3.5-turbo",
  messages=[
    {"role": "user", "content": "Hello!"}
  ],
  logprobs=True,
  top_logprobs=2
)

print(completion.choices[0].message)
print(completion.choices[0].logprobs)
```

결과 샘플:

```python
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1702685778,
  "model": "gpt-3.5-turbo-0125",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Hello! How can I assist you today?"
      },
      "logprobs": {
        "content": [
          {
            "token": "Hello",
            "logprob": -0.31725305,
            "bytes": [72, 101, 108, 108, 111],
            "top_logprobs": [
              {
                "token": "Hello",
                "logprob": -0.31725305,
                "bytes": [72, 101, 108, 108, 111]
              },
              {
                "token": "Hi",
                "logprob": -1.3190403,
                "bytes": [72, 105]
              }
            ]
          },
          # ...
        ]
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 9,
    "completion_tokens": 9,
    "total_tokens": 18
  },
  "system_fingerprint": null
}
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

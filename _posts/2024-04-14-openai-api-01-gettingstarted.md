---
layout: single
title: "OpenAI API - 01. Getting Started" 
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

## 소개 (Introduction)

HTTP 요청을 통해서 API를 사용 할 수 있습니다. OpenAI에서는 공식적으로 Python 바인딩과 Node.js 라이브러리를 제공하고 있습니다. 또한 [커뮤니티에서 관리하는 라이브러리](https://platform.openai.com/docs/libraries/community-libraries)도 있습니다.

OpenAI의 API를 사용하기 위한 Python 바인딩을 설치합니다.

```bash
# 터미널에서 실행
pip install openai
```

`main.py` 파일을 생성하고 아래 코드를 작성합니다.

```python
import openai
```

오류가 발생 하지 않으면 정상적으로 설치가 된 것입니다.

## 인증 (Authentication)

OpenAI API는 인증을 위해 API 키를 사용합니다.

API 키는 [이곳](https://platform.openai.com/account/api-keys)에서 발급 받을 수 있습니다. openai에 로그인을 하셔야 합니다. 참고로 최초 API 키를 발급 받았을 때 복사를 해두세요. 나중에 다시 볼수가 없습니다.

API 키를 절대 다른 사람과 공유하거나 클라우드에 노출하지 마십시오. API 사용은 유료입니다.

인증하는 방법은 HTTP 요청의 `Authorization` 헤더에 `Bearer` 키워드와 API 키를 넣어서 전송합니다.

```python
Authorization: Bearer OPENAI_API_KEY
```

조직 항목은 생략합니다. 나중에 조직의 사용량을 계산 할 수 있다고 합니다.

아래 명령어 터미널에서 실행 하여 오류가 발생하지 않으면 정상적으로 API 키가 생성된 것입니다.

```bash
# 터미널에서 실행
# 윈도우의 cmd에서는 \ 대신 ^를 사용합니다.
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

아래는 Python 코드로 작성한 것입니다.

```python
from openai import OpenAI

client = OpenAI(api_key="$OPENAI_API_KEY")
```

## 요청 보내기 (Making requests)

실제 Data(인공지능 모델에 질의)를 포함하여 요청을 보내는 방법입니다.

`curl` 명령어를 사용하여 요청을 보내는 방법입니다.

```bash
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
     "model": "gpt-3.5-turbo",
     "messages": [{"role": "user", "content": "Say this is a test!"}],
     "temperature": 0.7
   }'
```

- `https://api.openai.com/v1/chat/completions`: 요청을 보내는 URL
- `Authorization`: 인증 - API 키
- `model`: 사용할 모델
- `messages`: 사용자가 입력한 데이터
  - `role`: 대화의 역할 ('user', 'assistant', 'system')
  - `content`: 대화 내용
- `temperature`: 모델의 답변이 창의적인 정도를 조절하는 매개변수

`python` 코드로 작성한 것입니다.

```python
from openai import OpenAI


client = OpenAI(api_key="$OPENAI_API")
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "Say this is a test!"}],
    temperature=0.7
)

print(response.model_dump_json())
```

```python
{
    'id': 'chatcmpl-abc123', 
    'choices': 
    [
        {
            'finish_reason': 'stop', 
            'index': 0, 
            'logprobs': None, 
            'message': 
            {
                'content': 'This is a test!', 
                'role': 'assistant'
            }
        }
    ], 
    'created': 1677858242, 
    'model': 'gpt-3.5-turbo-0125', 
    'object': 'chat.completion', 
    'system_fingerprint': '', 
    'usage': 
    {
        'completion_tokens': 5, 
        'prompt_tokens': 13, 
        'total_tokens': 18
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

## API Key를 환경 변수에 저장하기

API 키를 코드에 직접 넣지 않고 환경 변수에 저장하여 사용하는 것이 좋습니다.

<https://platform.openai.com/docs/quickstart/step-2-set-up-your-api-key>

### python-dotenv

`python-dotenv`: 환경 변수를 파일에 저장하고 로드하는 라이브러리입니다.

```bash
# 터미널에서 실행
# python-dotenv 라이브러리 설치
pip install python-dotenv
```

`.env`: 환경 변수를 저장하는 파일입니다.

```bash
# .env 파일의 내용
OPENAI_API_KEY=sk-abc123 # API 키
```

`main.py`: 환경 변수를 로딩 하고 조회하는 예제 코드입니다.

```python
import os
from dotenv import load_dotenv

load_dotenv()  # .env 파일을 로드하여 환경 변수를 사용할 수 있도록 설정

print(os.getenv("OPENAI_API_KEY"))  # 환경 변수를 사용하여 API 키를 가져옴
```

`main.py`(수정): OpenAI API를 사용하는 예제 코드입니다. `.env` 파일에 API 키를 저장하고 사용합니다.

```python
from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()  # .env 파일을 로드하여 환경 변수를 사용할 수 있도록 설정

client = OpenAI()
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "Say this is a test!"}],
    temperature=0.7
)

print(response.model_dump_json())
```

**앞으로 모든 예제 코드는 `.env` 파일에 API 키를 저장했다고 가정하고 작성합니다.**

## 스트리밍 (Streaming)

OpenAI API는 특정 요청에 대해 부분적인 결과를 허용하기 위해 응답을 클라이언트로 다시 스트리밍하는 기능을 제공합니다. Pyton 라이브러리에는 스트리밍을 지원하는 `stream` 매개변수가 있습니다.

```python
from dotenv import load_dotenv # type: ignore
from openai import OpenAI

load_dotenv()

client = OpenAI()

stream = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Say this is a test. Give me a long answer"}],
    stream=True,
)

for chunk in stream:
    if chunk.choices[0].delta.content is not None:
        print(chunk.choices[0].delta.content, end="")
```

Stream을 사용하면 모델이 답변을 생성하는 동안에도 클라이언트가 결과를 받을 수 있습니다.

---

해시태그: #OpenAI #API #Python #OPENAI_API_KEY #Authentication #python-dotenv #Streaming

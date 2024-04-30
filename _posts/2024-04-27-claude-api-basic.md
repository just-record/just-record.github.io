---
layout: single
title: "Claude API 기본 연습하기" 
categories: Claude-API
tag: [Claude-API, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Claude API

Clause의 API를 사용하기 위해 우선 API Key를 발급받겠습니다.

<https://www.anthropic.com/api>

- 'Get Started now': API Key를 발급 -> Click
- 'Developer docs': API 문서

### API Key

- email: `your-email`
- 'Continue with email': Click
- 위의 이메일로 login code 확인하여 입력
- '페이지 이동': <https://console.anthropic.com/create>
- 'Organization name' 'Industry (optional)' 'Website (optional)' 입력 -> 'create account'
- '페이지 이동': <https://console.anthropic.com/dashboard>
- 'Get API Keys'
  - '페이지 이동': <https://console.anthropic.com/settings/keys>
  - 'Create Key': 'Name your key' 입력 -> 'Create Key'
  - 'Copy key'를 하셔서 API Key를 저장해두세요. 다시 볼 수 없습니다.
  - 'claim': Free Credits 제공, 전화번호 입력 필요

### Console 링크

- Dashboard: <https://console.anthropic.com/dashboard>
- Workbench: <https://console.anthropic.com/workbench/> - 코드를 작성하고 실행할 수 있는 환경
- Setting: <https://console.anthropic.com/settings/organization>

결제 링크: <https://console.anthropic.com/settings/plans> - 이 곳에서 결제를 진행해야 API를 사용 할 수 있습니다.

## API

공식 링크:

사용자 가이드: <https://docs.anthropic.com/claude/docs/intro-to-claude>

API Reference: <https://docs.anthropic.com/claude/reference/getting-started-with-the-api>

Prompt library: <https://docs.anthropic.com/claude/prompt-library>

## Python SDK

가이드: <https://docs.anthropic.com/claude/reference/client-sdks>

Github: <https://github.com/anthropics/anthropic-sdk-python>

### 설치

- Claude Client SDK: anthropic
- Python 환경 변수 라이브러리: python-dotenv

```bash
# Claude Client SDK
pip install anthropic
# Python 환경 변수 라이브러리
pip install python-dotenv
```

### 환경 변수 적용

`.env` 파일 생성:

```bash
ANTHROPIC_API_KEY=your-api-key
```

### 사용

```python
from dotenv import load_dotenv
load_dotenv()
import os
from anthropic import Anthropic

client = Anthropic()

message = client.messages.create(
    max_tokens=1024,
    messages=[
        {
            "role": "user",
            "content": "Hello, Claude",
        }
    ],
    model="claude-3-opus-20240229",
)
print(message.content)
print(' ')
for content in message.content:
    print(content.text)
```

## TODO

- 비동기
- 스트리밍
- 스트리밍 도우미
- 토큰 계산
- types 사용
- 오류 처리
- 재시도
- 시간 초과
- 기본 헤더
...

---

해시태그: #claude #api #python #anthropic #claude-3-opus #python-sdk #messages

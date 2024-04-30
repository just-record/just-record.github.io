---
layout: single
title: "Telegram - ChatBot (OpenAI API)" 
categories: Telegram-ChatBot
tag: [Telegram-ChatBot, OpenAI-API, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

Telegram의 API를 이용 하여 ChatBot을 만드는 방법을 가볍게 알아보겠습니다. OpenAI의 API를 사용하여 인공지능이 답변 하도록 하겠습니다.

<!-- ## Telegram 공식 사이트 링크 -->
## 관련 링크

Telegram 공식 사이트: <https://telegram.org>

Telegram 설치: <https://telegram.org/apps>

- 모바일, 데스크탑에 Telegram을 설치 할 수 있습니다.

Telegram API(개요): <https://core.telegram.org/api>

- `Bot API`: 봇을 이용한 간단한 프로그래밍
- `Telegram API and TDLib`: 자신의 앱을 만들어서 Telegram을 사용

- 저희는 `Bot API`를 사용하여 ChatBot을 만들어 보겠습니다.

Telegram Bots(개요): <https://core.telegram.org/bots>

Telegram Bots(튜토리얼):<https://core.telegram.org/bots/tutorial>

### 예제 소스 코드

<https://github.com/just-record/telegram_chatbot>

## Telegram Bot 생성 (BotFather)

<https://core.telegram.org/bots/tutorial#obtain-your-bot-token>

1. Telegram을 실행 합니다.(모바일 또는 데스크탑)
2. `BotFather`를 검색합니다.
3. `BotFather`를 선택합니다.
4. 시작합니다. -> Bot에서 제공하는 명령어를 안내합니다.
5. `/newbot`을 입력합니다.
6. 'Alright, a new bot. How are we going to call it? Please choose a name for your bot.' -> Bot의 이름을 입력합니다.
7. 'Good. Now let's choose a username for your bot. It must end in 'bot'. Like this, for example: TetrisBot or tetris_bot.' -> Bot의 사용자 이름을 입력합니다. bot으로 끝나야 합니다.
8. 'Use this token to access the HTTP API: 123xxx:xxxxxxxxxxx' -> Bot의 토큰을 제공 합니다. -> **Bot의 Token**입니다.

예제 Bot:

- `my_telegram_ai`
- `my_telegram_ai_bot`

## Bot 접속

- `my_telegram_ai`을 검색합니다.
- 채널을 선택 합니다.
- 'START'를 누릅니다.
- 대화를 시작 할 수 있습니다. -> 지금은 코딩이 되어 있지 않아 답변을 하지 않습니다.

## 패키지 설치

- `python-telegram-bot`: telegram의 python 라이브러리
- `python-dotenv`: Python에서 환경 변수를 사용하기 위한 라이브러리

```bash
# 터미널에서 실행
pip install python-telegram-bot
pip install python-dotenv
```

### `.env` 파일 생성

조금 전 생성한 Bot의 Token을 `.env` 파일에 저장합니다.

```text
TELEGRAM_TOKEN=123xxx:xxxxxxxxxxx
```

`load_ent_check.py`: 환경 변수 확인

```python
from dotenv import load_dotenv
load_dotenv()
import os

print(os.getenv("TELEGRAM_TOKEN"))
```

## Pure Telegram Bot API

Telegram에서 제공하는 기본 API를 사용하는 방법을 알아보겠습니다.

GitHub(출처): <https://github.com/python-telegram-bot/python-telegram-bot/wiki/Introduction-to-the-API>

예제 코드: <https://github.com/just-record/telegram_chatbot/tree/01.pure_telegram_bot_api>

### Bot 정보 확인

```python
from dotenv import load_dotenv
load_dotenv()
import os

import asyncio
import telegram


async def get_me(bot):
    async with bot:
        return await bot.get_me()


if __name__ == '__main__':
    telegram_token = os.getenv("TELEGRAM_TOKEN")
    bot = telegram.Bot(telegram_token)
    
    me = asyncio.run(get_me(bot=bot))
    print(me.to_dict())
```

### 메시지와 chat id 확인

전체 메시지를 확인 합니다.

```python
# ...
async def get_updates(bot):
    async with bot:
        return await bot.get_updates()


if __name__ == '__main__':
    telegram_token = os.getenv("TELEGRAM_TOKEN")
    bot = telegram.Bot(telegram_token)
    
    updates = asyncio.run(get_updates(bot=bot))
    for update in updates:
        # print(update.to_dict())
        print(f'message: {update.message.text}')        # 메시지 내용  
        print(f'chat_id: {update.message.chat.id}')  # 채팅 사용자 Id
```

### 메시지 보내기

chat_id에게 메시지를 보냅니다.

```python
# ...    
async def send_message(bot, chat_id, text):
    async with bot:
        await bot.send_message(text=text, chat_id=chat_id)


if __name__ == '__main__':
# ...    
    chat_id = ''
    updates = asyncio.run(get_updates(bot=bot))
    for i, update in enumerate(updates):
        if i == 0:
            chat_id = update.message.chat.id
        print(f'message: {update.message.text}')        # 메시지 내용  
        print(f'chat_id: {update.message.chat.id}')  # 채팅 사용자 Id

    text = "Hello, Telegram!"
    asyncio.run(send_message(bot=bot, chat_id=chat_id, text=text))
```

## My First ChatBot

`python-telegram-bot`를 사용하여 손쉽게 ChatBot을 만드는 방법을 알아보겠습니다.

GitHub(출처): <https://github.com/python-telegram-bot/python-telegram-bot/wiki/Extensions---Your-first-Bot>

예제 코드: <https://github.com/just-record/telegram_chatbot/tree/02.my_first_bot>

### Application Object

ChatBot의 가장 기본이 되는 Application Object를 만들어 보겠습니다.

```python
from dotenv import load_dotenv
load_dotenv()
import os

from telegram.ext import ApplicationBuilder

if __name__ == '__main__':
    # Telegram token from .env file
    telegram_token = os.getenv('TELEGRAM_TOKEN')
    # Create an instance of ApplicationBuilder
    application = ApplicationBuilder().token(telegram_token).build()

    print(application)
```

### 명령어 처리하기

'ommand Handler'를 이용 하여 사용자의 명령어를 처리 합니다.

```python
# ...
from telegram import Update
from telegram.ext import ApplicationBuilder, ContextTypes, CommandHandler

# '/start' 라는 명령어가 입력되면 실행되는 함수입니다.
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await context.bot.send_message(
        chat_id=update.effective_chat.id,
        text="I'm a bot, please talk to me!"
    )

if __name__ == '__main__':
    # ...

    # CommandHandler를 생성합니다.
    # '/start' 라는 명령어가 입력되면 start 함수가 실행됩니다.
    start_handler = CommandHandler('start', start)
    # 생성한 CommandHandler를 application에 추가합니다.
    application.add_handler(start_handler)

    # 프로그램이 종료 되지 않고 계속 실행되면서 Polling 방식으로 메시지를 받아옵니다.
    # Ctrl + C 를 눌러서 프로그램을 종료할 수 있습니다.
    application.run_polling()
```

- 사용자가 `/start` 명령어를 입력하면 답변하는 함수를 만들 수 있습니다.
- 해당 함수는 `/start` 명령어만 반응합니ㅏㄷ.

### Message Handler

'MessageHandler를 사용하여 사용자의 메시지를 처리 합니다. 사용자의 메시지를 그대로 답변하는 함수를 만들어 보겠습니다.

```python
# ...
# 사용자가 Message를 입력 했을 때 답변해주는 함수입니다.
async def echo(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await context.bot.send_message(
        chat_id=update.effective_chat.id, 
        text=update.message.text
    )

if __name__ == '__main__':
    # ...

    # MessageHandler를 생성합니다.
    # filters.TEXT: 사용자가 입력한 메시지가 텍스트인지 확인합니다.
    # ~filters.COMMAND: 사용자가 입력한 메시지가 명령어(/start, /help)가 아닌지 확인합니다.
    # 2개의 조건이 참일 때 echo 함수가 실행됩니다.
    echo_handler = MessageHandler(filters.TEXT & (~filters.COMMAND), echo)
    
    # ...
    # 생성한 MessageHandler를 application에 추가합니다.
    application.add_handler(echo_handler)
    # ...
```

- 사용자가 명령어가 아닌 메시지를 입력하면 그대로 답변합니다.
- Text일 때만 답변합니다.

### 인자가 있는 Command Handler

명령어에 인자를 추가하여 사용자의 명령어를 처리 합니다.

```python
# ...

async def caps(update: Update, context: ContextTypes.DEFAULT_TYPE):
    # 명령어의 인자(뒤에 입력한 텍스트)를 대문자로 변환합니다.
    text_caps = ' '.join(context.args).upper()
    await context.bot.send_message(
        chat_id=update.effective_chat.id, 
        text=text_caps
)


if __name__ == '__main__':
    # ...
    # 인자를 처리 하는 CommandHandler를 생성합니다.
    caps_handler = CommandHandler('caps', caps)
    # ...
    application.add_handler(caps_handler)
    # ...
```

- `/caps` 명령어를 입력하면 뒤에 입력한 텍스트를 대문자로 변환하여 답변합니다.

## 인공지능 답변

OpenAI API와 연동하여 인공지능이 답변하도록 만들어 보겠습니다. OpenAI의 API를 사용 하면 비용이 발생할 수 있습니다.

예제 코드: <https://github.com/just-record/telegram_chatbot/tree/03.telegram_chatbot_chatGPT>

OpenAI API: <https://platform.openai.com/docs/api-reference/chat>

OpenAI API 예제 코드: <https://github.com/just-record/openai_api>

### OPEN_API_KEY 추가

`.env` 파일에 OpenAI API Key를 추가합니다.

```text
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx
```

### 인공지능 답변 생성

OpenAI API를 사용하여 인공지능이 답변하도록 만들어 보겠습니다.

```python
# ...
import os
from openai import OpenAI


# OpenAI API 클라이언트를 생성합니다.
def get_openai_client():
    return OpenAI()


# OpenAI API로 채팅 답변을 생성합니다.
def get_openai_chat_completion(client, model: str, messages: List ):
    return client.chat.completions.create(
        model=model,
        messages=messages
    )

# ...

# 채팅 메시지에 답변을 보내는 함수
async def answer(update: Update, context: ContextTypes.DEFAULT_TYPE):

    # 사용자가 보낸 메시지를 query 변수에 저장
    query = update.message.text
    
    client = get_openai_client()  # OpenAI API 클라이언트 생성
    model = "gpt-3.5-turbo-0613"  # 사용할 모델 이름
    # 사용자가 보낸 메시지를 OpenAI API에 user 역할로 전달
    messages = [{"role": "user", "content": query}]
    # OpenAI API로 답변 생성
    completion = get_openai_chat_completion(
        client=client, 
        model=model, 
        messages=messages
    )

    try:
        answer = completion.choices[0].message.content
    except Exception as e: # 예외 발생 시
        print(f'Error: {e}')
        answer = "죄송합니다. 인공지능이 답변을 생성하는 중에 오류가 발생했습니다."

    # OpenAI API의 답변을 사용자에게 전송
    await context.bot.send_message(
        chat_id=update.effective_chat.id, 
        text=answer
    )


if __name__ == '__main__':
    # ...
    # answer_handler를 생성
    answer_handler = MessageHandler(filters.TEXT & (~filters.COMMAND), answer)
    
    # ...
    application.add_handler(answer_handler)
    # ...
```

- 사용자가 보낸 메시지를 OpenAI API에 전달하여 답변을 생성합니다.

### 특정 기능 부여

OpenAI Chat의 System content를 사용하여 인공 지능이 특정 기능을 하여 답변하도록 만들어 보겠습니다. 사용자가 보낸 메시지를 영어로 번역하도록 하겠습니다.

```python
# ...
# 시스템 메시지를 추가하는 함수
def add_system_content(messages: List, system_message: str):
    messages.insert(0, {"role": "system", "content": system_message})

# ...
async def answer(update: Update, context: ContextTypes.DEFAULT_TYPE):
    # ...
    messages = [{"role": "user", "content": query}]
    # 영어로 번역하라는 시스템 메시지 추가
    add_system_content(messages, "Please translate it into English.")
    # ...


if __name__ == '__main__':
    # ...
```

- 사용자가 보낸 메시지를 영어로 번역하라는 시스템 메시지를 추가하여 답변을 생성합니다.
- 사용자가 보낸 메시지를 영어로 번역하여 답변합니다.

### 이미지 생성

OpenAI API를 사용하여 이미지를 생성하도록 만들어 보겠습니다. 사용자가 보낸 메시지의 요청대로 이미지를 생성하고 생성된 이미지의 URL을 사용자에게 전송합니다.

```python
# ...
def get_openai_generate_image(client, model: str, prompt: str, n: int = 1, size: str = "1024x1024"):
    return client.images.generate(
        model=model,
        prompt=prompt,
        n=n,
        size=size
    )

# ...
async def generate_image(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.message.text
    client = get_openai_client()
    model = "dall-e-3"
    n = 1
    size = "1024x1024"
    images = get_openai_generate_image(
        client=client,
        model=model,
        prompt=query,
        n=n,
        size=size
    )

    try:
        # image_url = images.data[0].url
        answer = images.data[0].url
    except Exception as e: # 예외 발생 시
        print(f'Error: {e}')
        answer = "죄송합니다. 인공지능이 답변을 생성하는 중에 오류가 발생했습니다."

    await context.bot.send_message(
        chat_id=update.effective_chat.id, 
        text=answer
    )        


if __name__ == '__main__':
    # ...
    answer_handler = MessageHandler(filters.TEXT & (~filters.COMMAND), generate_image)
    # ...
```

- 사용자가 보낸 메시지를 OpenAI API에 전달하여 이미지를 생성합니다.
- 생성된 이미지의 URL을 사용자에게 전송합니다.

---

해시태그: #Telegram #ChatBot #API #openai #python #BotFather #Application #CommandHandler #MessageHandler #OpenAI-API

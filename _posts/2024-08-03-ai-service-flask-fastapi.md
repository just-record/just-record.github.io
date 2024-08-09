---
layout: single
title: "AI Service - Flask VS FastAPI" 
categories: AI-Service
tag: [Flask, FastAPI]
toc: true
toc_sticky: true
toc_label: "목차"
---

## AI Service: Flask VS FastAPI

AI Service를 구축하기 위해 Flask와 FastAPI의 비교

> Flask

장점

1. 간단하고 가벼움: 초보자도 쉽게 시작할 수 있는 단순한 구조
2. 유연성: 개발자가 원하는 대로 구조를 설계할 수 있음
3. 광범위한 커뮤니티와 풍부한 확장 기능

단점

1. 비동기 처리가 기본적으로 지원되지 않음
2. 대규모 애플리케이션에서는 구조화에 더 많은 노력이 필요
3. 타입 힌팅이 기본적으로 제공되지 않음

> FastAPI

장점

1. 빠른 성능: 비동기 처리를 기본적으로 지원
2. 자동 문서화: Swagger UI와 ReDoc을 통한 API 문서 자동 생성
3. 타입 힌팅과 데이터 검증: Pydantic을 이용한 강력한 타입 체크와 데이터 검증
4. 현대적인 Python 기능 활용: 비동기 프로그래밍, 타입 힌팅 등

단점

1. 학습 곡선: 비동기 프로그래밍과 타입 시스템에 대한 이해가 필요
2. 상대적으로 새로운 프레임워크: Flask에 비해 커뮤니티와 리소스가 적음
3. 일부 레거시 시스템과의 통합이 어려울 수 있음

> AI 서비스 구축 관점에서

- Flask는 간단한 AI 모델 서빙이나 프로토타이핑에 적합할 수 있습니다.
- FastAPI는 대규모 AI 서비스, 실시간 처리가 필요한 경우, 또는 복잡한 데이터 구조를 다루는 경우에 더 적합할 수 있습니다.

## Flask

### 기본 - Flask

`디렉토리 구조`

```plaintext
project_folder/
├── .env
├── app.py
└── templates/
    └── index.html
```

`.env`

```plaintext
OPENAI_API_KEY=your_openai_api_key
```

`index.html`

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI 챗봇</title>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <style>
        body { font-family: Arial, sans-serif; max-width: 800px; margin: 0 auto; padding: 20px; }
        #chat-container { border: 1px solid #ccc; height: 400px; overflow-y: scroll; padding: 10px; margin-bottom: 10px; }
        #user-input { width: 100%; padding: 5px; margin-bottom: 10px; }
        #send-button { padding: 5px 10px; }
    </style>
</head>
<body>
    <h1>AI 챗봇</h1>
    <div id="chat-container"></div>
    <input type="text" id="user-input" placeholder="메시지를 입력하세요...">
    <button id="send-button">전송</button>

    <script>
        $(document).ready(function() {
            function sendMessage() {
                var message = $("#user-input").val();
                if (message.trim() === "") return;

                $("#chat-container").append("<p><strong>사용자:</strong> " + message + "</p>");
                $("#user-input").val("");

                $.ajax({
                    url: "/chat",
                    type: "POST",
                    contentType: "application/json",
                    data: JSON.stringify({message: message}),
                    success: function(response) {
                        $("#chat-container").append("<p><strong>AI:</strong> " + response.response + "</p>");
                        $("#chat-container").scrollTop($("#chat-container")[0].scrollHeight);
                    }
                });
            }

            $("#send-button").click(sendMessage);
            $("#user-input").keypress(function(e) {
                if (e.which == 13) sendMessage();
            });
        });
    </script>
</body>
</html>
```

`app.py`

```python
from dotenv import load_dotenv
load_dotenv()

from flask import Flask, render_template, request, jsonify
from openai import OpenAI

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/chat", methods=["POST"])
def chat():
    user_message = request.json["message"]

    client = OpenAI()

    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": user_message}
        ]
    )

    ai_message = response.choices[0].message.content
    return jsonify({"response": ai_message})

if __name__ == "__main__":
    app.run(debug=True)
```

```bash
# 실행
python app.py
```

```plaintext
# 브라우저에서 접속
http://localhost:5000
```

### 이전 대화 기억

`app.py`

```python
from dotenv import load_dotenv
load_dotenv()
from flask import Flask, render_template, request, jsonify
from openai import OpenAI
import time

app = Flask(__name__)
client = OpenAI()

# 대화 기록을 저장할 리스트
conversation_history = []

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/chat", methods=["POST"])
def chat():
    time.sleep(5)
    user_message = request.json["message"]
    
    # 사용자 메시지를 대화 기록에 추가
    conversation_history.append({"role": "user", "content": user_message})
    
    # OpenAI API 호출
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
        ] + conversation_history  # 시스템 메시지와 대화 기록을 합침
    )
    
    ai_message = response.choices[0].message.content
    
    # AI 응답을 대화 기록에 추가
    conversation_history.append({"role": "assistant", "content": ai_message})
    
    # 대화 기록이 너무 길어지지 않도록 제한 (선택사항)
    if len(conversation_history) > 10:  # 최근 10개의 메시지만 유지
        conversation_history.pop(0)
    
    return jsonify({"response": ai_message})

@app.route("/history", methods=["GET"])
def get_history():
    return jsonify(conversation_history)

if __name__ == "__main__":
    app.run(debug=True)
```

## FastAPI

### 기본 - FastAPI

`디렉토리 구조`

```plaintext
project_folder/
├── .env
├── main.py
└── chat.py
```

`.env`

```plaintext
OPENAI_API_KEY=your_openai_api_key
```

`main.py`

```python
from dotenv import load_dotenv
load_dotenv()
from fastapi import FastAPI
from pydantic import BaseModel
from openai import OpenAI

app = FastAPI()

# Pydantic 모델 정의
class ChatRequest(BaseModel):
    message: str

class ChatResponse(BaseModel):
    response: str

@app.post("/chat", response_model=ChatResponse)
async def chat(chat_request: ChatRequest):
    client = OpenAI()
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": chat_request.message}
        ]
    )
    ai_message = response.choices[0].message.content
    return ChatResponse(response=ai_message)
```

```bash
# 실행
uvicorn main:app --reload
```

```plaintext
# 브라우저에서 접속
http://localhost:8000/docs
```

`chat.py`

```python
import requests

# FastAPI 서버 URL
url = "http://localhost:8000/chat"

def chat_with_ai():
    print("AI 어시스턴트와의 대화를 시작합니다. 종료하려면 '/q'를 입력하세요.")
    
    while True:
        # 사용자 입력 받기
        user_message = input("You: ")
        
        # 종료 조건 확인
        if user_message.lower() == '/q':
            print("대화를 종료합니다.")
            break
        
        # 요청 데이터 준비
        data = {"message": user_message}
        
        try:
            # HTTP POST 요청 보내기
            response = requests.post(url, json=data)
            
            # 응답 확인
            if response.status_code == 200:
                result = response.json()
                ai_response = result["response"]
                print("AI:", ai_response)
            else:
                print(f"오류 발생: HTTP {response.status_code}")
                print("응답:", response.text)
        
        except requests.RequestException as e:
            print(f"요청 중 오류 발생: {e}")

if __name__ == "__main__":
    chat_with_ai()
```

```bash
# 실행
python chat.py
# /q 입력하면 종료
```

### 이전 대화 기억 - FastAPI

`main.py` 수정

```python
from dotenv import load_dotenv
load_dotenv()
from fastapi import FastAPI
from pydantic import BaseModel
from openai import OpenAI
from typing import List, Dict

app = FastAPI()

# 대화 기록을 저장할 전역 리스트
conversation_history: List[Dict[str, str]] = []

# Pydantic 모델 정의
class ChatRequest(BaseModel):
    message: str

class ChatResponse(BaseModel):
    response: str

@app.post("/chat", response_model=ChatResponse)
async def chat(chat_request: ChatRequest):
    global conversation_history
    
    # 사용자 메시지를 대화 기록에 추가
    conversation_history.append({"role": "user", "content": chat_request.message})
    
    # OpenAI API 호출 준비
    client = OpenAI()
    messages = [
        {"role": "system", "content": "You are a helpful assistant."},
    ] + conversation_history  # 시스템 메시지와 대화 기록을 합침
    
    # OpenAI API 호출
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=messages
    )
    
    ai_message = response.choices[0].message.content
    
    # AI 응답을 대화 기록에 추가
    conversation_history.append({"role": "assistant", "content": ai_message})
    
    # 대화 기록이 너무 길어지지 않도록 제한 (선택사항)
    if len(conversation_history) > 10:  # 최근 10개의 메시지만 유지
        conversation_history = conversation_history[-10:]
    
    return ChatResponse(response=ai_message)

@app.get("/reset")
async def reset_conversation():
    global conversation_history
    conversation_history = []
    return {"message": "Conversation history has been reset."}
```

```bash
# 서버 실행
uvicorn main:app --reload
# 클라이언트 실행
python chat.py
# /q 입력하면 종료
```

### 사용자별로 대화 기록 저장 - FastAPI

`main.py` 수정

```python
from dotenv import load_dotenv
load_dotenv()
from fastapi import FastAPI, Depends
from pydantic import BaseModel
from openai import OpenAI
from typing import List, Dict

app = FastAPI()

# 대화 기록을 저장할 전역 딕셔너리
conversation_history: Dict[str, List[Dict[str, str]]] = {}

# Pydantic 모델 정의
class ChatRequest(BaseModel):
    message: str
    conversation_id: str

class ChatResponse(BaseModel):
    response: str

def get_conversation(conversation_id: str) -> List[Dict[str, str]]:
    if conversation_id not in conversation_history:
        conversation_history[conversation_id] = []
    return conversation_history[conversation_id]

@app.post("/chat", response_model=ChatResponse)
async def chat(chat_request: ChatRequest):
    conversation = get_conversation(chat_request.conversation_id)
    
    # 사용자 메시지를 대화 기록에 추가
    conversation.append({"role": "user", "content": chat_request.message})
    
    # OpenAI API 호출 준비
    client = OpenAI()
    messages = [
        {"role": "system", "content": "You are a helpful assistant."},
    ] + conversation  # 시스템 메시지와 대화 기록을 합침
    
    # OpenAI API 호출
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=messages
    )
    
    ai_message = response.choices[0].message.content
    
    # AI 응답을 대화 기록에 추가
    conversation.append({"role": "assistant", "content": ai_message})
    
    # 대화 기록이 너무 길어지지 않도록 제한 (선택사항)
    if len(conversation) > 10:  # 최근 10개의 메시지만 유지
        conversation = conversation[-10:]
    
    return ChatResponse(response=ai_message)
```

`chat.py` 수정

```python
import requests
import uuid

# FastAPI 서버 URL
url = "http://localhost:8000/chat"

def chat_with_ai():
    print("AI 어시스턴트와의 대화를 시작합니다. 종료하려면 '/q'를 입력하세요.")
    
    # 사용자별 고유 conversation_id 생성
    conversation_id = str(uuid.uuid4())
    print(f"Your conversation ID: {conversation_id}")
    
    while True:
        # 사용자 입력 받기
        user_message = input("You: ")
        
        # 종료 조건 확인
        if user_message.lower() == '/q':
            print("대화를 종료합니다.")
            break
        
        # 요청 데이터 준비
        data = {
            "message": user_message,
            "conversation_id": conversation_id
        }
        
        try:
            # HTTP POST 요청 보내기
            response = requests.post(url, json=data)
            
            # 응답 확인
            if response.status_code == 200:
                result = response.json()
                ai_response = result["response"]
                print("AI:", ai_response)
            else:
                print(f"오류 발생: HTTP {response.status_code}")
                print("응답:", response.text)
        
        except requests.RequestException as e:
            print(f"요청 중 오류 발생: {e}")

if __name__ == "__main__":
    chat_with_ai()
```

```bash
# 서버 실행
uvicorn main:app --reload
### 2개의 클라이언트 실행하여 대화가 구분되어 저장되는지 확인
# A 클라이언트 실행
python chat.py
# B 클라이언트 실행
python chat.py
# /q 입력하면 종료
```

---

해시태그: #AI-Service #Flask #FastAPI #OpenAI #Chatbot #챗봇 #이전 대화 기억 #사용자별 대화 기록

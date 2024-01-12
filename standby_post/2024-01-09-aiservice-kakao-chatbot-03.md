---
layout: single
title: "AI service - 카카오챗봇 03 (답변하기)"
categories: AI-service
tag: [aiservice, 답변]
toc: true
toc_sticky: true
toc_label: "목차"
---
지난번에 카카오챗봇과 연동하는 API 서버를 구축하였습니다. 사용자가 메시지를 입력 했을 때 API 서버를 통해서 **답변**을 하도록 하겠습니다. 스킬 응답에 대한 공식 사이트 링크([응답 타입별 JSON 포맷](https://kakaobusiness.gitbook.io/main/tool/chatbot/skill_guide/answer_json_format#composition-1))입니다.

## 무조건 '안녕하세요!'라고 답변하기

우선 메시지를 받으면 무조건 '안녕하세요!'라고 답변하도록 하겠습니다. 지난번의 'main.py'를 수정하겠습니다.

> `main.py` 수정

```python
from fastapi import Request, FastAPI

app = FastAPI()

@app.post("/mychatbot/")
async def chat(request: Request):
    req_data = await request.json()
    print(req_data)
    return {
        "version": "2.0",
        "template": {
            "outputs": [
                {
                    "simpleText": {
                        "text": "안녕하세요!"
                    }
                }
            ]
        }
    }
```

답변의 json 포맷은 위의 공식 사이트 링크를 참조 하시기 바랍니다.

- 저희는 응답데이터를 '스킬데이터'로 설정 했으므로 'template'는 필수 입니다.
- 'template'의 'outputs'도 필수 이며 `Array<Component>` 타입으로 1개 이상 3개 이하 입니다.
- 'simpleText'로 간단한 텍스트 답변을 할 수 있습니다.

지난번에 API 서버를 `uvicorn main:app --reload`와 같이 실행 하셨다면 자동으로 서버가 재구동 됩니다. 재구동이 안되었다면 서버를 재구동 하시면 됩니다. 카카오 채널에서 테스트를 하겠습니다. '안녕하세요!'라고 답변이 오면 정상적으로 연동이 된 것 입니다.

![카카오톡]({{site.url}}/images/kakao-chatbot/kakao-chatbot-03_01.png)

어떤 메시지를 보내도 무조건 '안녕하세요!'라고만 답하네요.

## 보낸 메시지 그대로 답변하기

조금 응용 해서 사용자가 보낸 메시지를 그대로 답변하도록 하겠습니다.

> `main.py` 수정

```python
from fastapi import Request, FastAPI

app = FastAPI()

@app.post("/mychatbot/")
async def chat(request: Request):
    req_data = await request.json()
    print(req_data)
    return {
        "version": "2.0",
        "template": {
            "outputs": [
                {
                    "simpleText": {
                        "text": req_data['userRequest']['utterance']
                    }
                }
            ]
        }
    }
```

- 바로 전 포스팅에서 request의 body를 확인 하였습니다. 한번 더 확인 하겠습니다.
  ![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_22.png)
- 사용자가 보낸 메시지는 `req_data['userRequest']['utterance']`에 있는걸 알 수 있습니다.
- 조금 전 코드에서 '안녕하세요!'를 `req_data['userRequest']['utterance']`로 변경하였습니다.

서버를 재구동 하고 카카오 채널에서 테스트를 하겠습니다. 사용자가 보낸 메시지가 그대로 답변이 오면 정상적으로 연동이 된 것 입니다.

![카카오톡]({{site.url}}/images/kakao-chatbot/kakao-chatbot-03_02.png)

보낸 메시지를 그대로 잘 대답하네요.

## URL과 이미지 링크

```python
"simpleText": {
    "text": req_data['userRequest']['utterance']
}
```

이 부분을 URL과 이미지 링크로 변경하여 테스트를 하겠습니다.

> URL

```python
"simpleText": {
    "text": "https://www.kakaocorp.com/page/"
}
```

정상 작동 합니다.

![카카오톡]({{site.url}}/images/kakao-chatbot/kakao-chatbot-03_03.png)

> 이미지 링크

```python
"simpleImage": {
    "imageUrl": "https://t1.kakaocdn.net/kakaocorp/kakaocorp/admin/65626a08017800001.png"
}
```

'simpleImage'와 'imageUrl'로 변경하였습니다. 정상 작동 합니다.

![카카오톡]({{site.url}}/images/kakao-chatbot/kakao-chatbot-03_04.png)

## openai의 chatGPT API 사용

이제 openai의 API를 사용하여 답변을 하도록 하겠습니다. [Making requests](https://platform.openai.com/docs/api-reference/making-requests)는 openai의 공식 사이트 링크 입니다. 또는 이 전에 작성한 포스트를 참조 하시기 바랍니다.

> `main.py` 수정

```python
import requests
from fastapi import Request, FastAPI

app = FastAPI()

api_key = '<your api key>'   # openai의 api key를 입력합니다.

@app.post("/mychatbot/")
async def chat(request: Request):
    req_data = await request.json()
    prompt =  req_data['userRequest']['utterance']
    
    response = requests.post(
        "https://api.openai.com/v1/chat/completions",
        headers={
            "Authorization": f"Bearer {api_key}",
            "Content-Type": "application/json"
        },
        json={
            "model": "gpt-3.5-turbo",
            "messages": [{"role": "user", "content": f"{prompt}"}],
            "temperature": 0.7            
        }
    )

    return {
        "version": "2.0",
        "template": {
            "outputs": [
                {
                    "simpleText": {
                        "text": response.json()['choices'][0]['message']['content']
                    }
                }
            ]
        }
    }
```

- `api_key = '<your api key>'` 에 여러분의 openai의 api key를 입력합니다.
- `"https://api.openai.com/v1/chat/completions"`: openai의 API를 호출하는 주소입니다.
- `"Authorization": f"Bearer {api_key}"`: openai의 api key를 헤더에 입력합니다.
- `"Content-Type": "application/json"`: json 형식으로 데이터를 전송합니다.
- `json={...}`: chatGPT API에 전송할 데이터 입니다.
  - `"model": "gpt-3.5-turbo"`: chatGPT API의 모델을 지정합니다.
  - `"messages": [{"role": "user", "content": f"{prompt}"}]`: 사용자가 입력한 메시지를 입력합니다.
  - `"temperature": 0.7`: 답변의 다양성을 지정합니다. 0.0 ~ 1.0 사이의 값을 지정합니다. 0.0은 답변의 다양성이 없고 1.0은 답변의 다양성이 많습니다.

![카카오톡]({{site.url}}/images/kakao-chatbot/kakao-chatbot-03_05.png)

이제 단순한 답변이 아닌 인공지능의 답변이 오는 것을 확인 하였습니다.

---

해시태그: #kakao #chatbot #ai-service

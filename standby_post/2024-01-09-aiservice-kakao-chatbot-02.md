---
layout: single
title: "AI service - 카카오챗봇 02 (API 서버 구축)"
categories: AI-service
tag: [aiservice, API서버]
toc: true
toc_sticky: true
toc_label: "목차"
---
카카오 챗봇과 연동 할 AIP 서버를 구축하겠습니다. API 서버는 **외부에서 접근 가능**해야 합니다. 그리고 **https를 지원**해야 합니다. 별도의 서버가 없으시거나 환경을 구축하기 어려우신 분은 로컬 컴퓨터에 서버를 구축 하실 수 있습니다. 다만 Ngrok을 사용하여 외부에서 접근 가능하도록 해야 합니다.

- https 서버 구축 참조: [FastAPI - 22 (Nginx)]({{site.url}}/fastapi/fastapi-22/)
- Ngrok 사용법 참조: [ngrok]({{site.url}}/ngrok/python-dev-env-03/)

## API 서버 구축

Fastapi를 사용하여 AIP 서버를 구축하겠습니다.

> `main.py` 작성

```python
from fastapi import Request, FastAPI

app = FastAPI()

@app.post("/mychatbot/")
async def chat(request: Request):
    req_data = await request.json()
    print(req_data)
    return req_data
```

API 서버를 구동합니다.

```bash
uvicorn main:app --reload
```

> Fastapi 구동 확인

postman 등을 이용하여 테스트 합니다. 아래의 주소로 접속합니다.

- <http://localhost:8000/mychatbot>  
  {"id": "user01", "name": "홍길동"}  
  post 방식의 요청으로 request body에 json 형식의 데이터를 입력합니다.

아래와 같이 응답이 오면 정상적으로 구동되고 있는 것 입니다.

```json
{"id": "user01", "name": "홍길동"}
```

Nginx 또는 Ngrok을 사용하여 **외부에서 접근 가능한 https 서버로 구동**(위의 링크 참조)합니다. 앞으로 API 서버 주소는 `https://chatbot.mycompany.co.kr`로 가정하겠습니다.

이제 카카오 챗봇을 API 서버와 연동하겠습니다. [챗봇 관리자센터](https://chatbot.kakao.com/)에 접속합니다.

## 스킬 생성

'[카카오톡의 비즈니스 가이드 - 챗봇 관리자 센터 - 스킬](https://kakaobusiness.gitbook.io/main/tool/chatbot/main_notions/skill)'는 공식 사이트의 링크 입니다. 스킬에 대해 알려줍니다.

먼저 지난번에 생성한 '인공지능 챗봇'을 선택합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_01.png)

좌측 메뉴의 [스킬] -> [스킬 목록]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_02.png)

[생성]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_03.png)

스킬 이름, 설명, URL을 입력하고 기본 스킬로 설정합니다. URL은 API 서버 주소(`https://chatbot.mycompany.co.kr/chat`)입니다. [저장]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_04.png)

저장이 완료되면 API 서버와 연동이 잘 되었는지 테스트를 해보겠습니다. 우측 하단의 [스킬서버로 전송]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_05.png)

아래와 같이 응답이 오면 정상적으로 연동이 된 것 입니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_06.png)

## 시나리오 생성

'[카카오톡의 비즈니스 가이드 - 챗봇 관리자 센터 - 시나리오](https://kakaobusiness.gitbook.io/main/tool/chatbot/main_notions/scenario)'는 공식 사이트의 링크 입니다. 시나리오에 대해 알려줍니다.

좌측 메뉴의 [시나리오] -> [+시나리오]를 클릭하여 시나리오를 생성합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_07.png)

[폴백 블록]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_08.png)

우측 상단의 [스킬 검색/선택]에서 조금 전 생성한 '인공지능 챗봇'을 선택합니다. 버전은 자동으로 '1'이 선택됩니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_09.png)

화면 아래의 [스킬데이터]를 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_10.png)

[저장]를 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_11.png)

시나리오 생성이 완료되었습니다.

## 배포

스킬과 시나리오를 생성하였으니 배포를 해보겠습니다. 좌측 메뉴의 [배포] -> [배포]를 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_12.png)

확인 창이 뜨면 [배포]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_13.png)

배포가 완료되었습니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_14.png)

## 채널 확인

상단의 [채널] -> [채널]을 클릭하여 채널 관리자로 이동합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_15.png)

'JBSH 인공지능 챗봇'을 선택합니다. [친구모으기] -> [채널 홍보]를 클릭합니다. [채널 URL]을 복사합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_16.png)

웹 브라우저의 주소창에 붙여 넣어 접속합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_17.png)

우측 상단의 '로봇 모양의 아이콘'을 클릭합니다. 채팅이 시작 됩니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_18.png)

PC에 카카오톡이 설치되어 있으면 카카오톡으로 연결됩니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_19.png)

대화창 화면 입니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_20.png)

'안녕하세요'라고 메시지를 보냈습니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_21.png)

아직은 아무런 대답이 없습니다.  

API 서버에 접속하여 로그를 확인해 보겠습니다. 로그를 보기 좋게 정렬하였습니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-02_22.png)

API 서버에 정상적으로 요청이 오고 있습니다. 이제 API 서버에서 요청을 받아 처리하는 부분을 구현해야 겠습니다.

---

해시태그: #kakao #chatbot #ai-service #채널 #스킬 #시나리오 #배포

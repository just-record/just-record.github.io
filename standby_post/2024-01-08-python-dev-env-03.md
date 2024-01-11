---
layout: single
title: "ngrok - 로컬 서비스를 외부에서 접속"
categories: ngrok
tag: [python, ngrok]
toc: true
toc_sticky: true
toc_label: "목차"
---
ngrok은 **로컬 서비스를 외부에서 접속** 할 수 있도록 해주는 프로그램입니다. 개발을 하다 보면 아래와 같은 곤란한 상황이 발생합니다. 이 때 ngrok을 사용하면 편리합니다.

- 내부에서 개발중인 서비스를 고객이 갑자기 보고 싶어 할 때
- 외부의 서비스와 연동할 테스트 서버가 필요하지만 정식으로 서버 환경을 구축하기 어려울 때(어렵거나 귀찮거나 돈이 없거나...)
- 특히 외부 서비스와 연동할 https 서버가 필요할 때

Windows 10 기준입니다.

## ngork 설치

2024년 1월 11일 기준으로 작성 되었습니다. 사이트의 변경이 있을 수 있습니다.

[https://ngrok.com/](https://ngrok.com/) 에 접속합니다.

![ngrok]({{site.url}}/images/ngrok/ngrok-01_01.png)

[sign up]을 하고 [Login]을 합니다.

![ngrok]({{site.url}}/images/ngrok/ngrok-01_02.png)

Connect -> Windows -> [Download] -> [Download for Windows (64-bit)]를 클릭합니다.

![ngrok]({{site.url}}/images/ngrok/ngrok-01_03.png)

다운로드 받은 파일의 압축을 풀면 'ngrok.exe'를 확인하실 수 있습니다. 적절한 위치로 이동합니다.

![ngrok]({{site.url}}/images/ngrok/ngrok-01_04.png)

인증 토큰을 추가 하기 위한 사이트에서 제공하는 명령어를 복사 합니다.

![ngrok]({{site.url}}/images/ngrok/ngrok-01_05.png)

터미널(cmd)을 실행하고 'ngrok.exe'가 있는 파일로 이동합니다. 복사한 명령어를 붙여 넣고 실행합니다.

![ngrok]({{site.url}}/images/ngrok/ngrok-01_06.png)

인증 토근이 추가 되었습니다.

![ngrok]({{site.url}}/images/ngrok/ngrok-01_07.png)

## 로컬 서버 실행

ngrok는 로컬 서버를 외부에서 접속 할 수 있도록 해주는 프로그램입니다. **로컬 서버가 실행 되어 있어야** 합니다. FastAPI를 사용하여 로컬 서버를 실행하겠습니다. 로컬 서버가 이미 실행되어 있거나 FastAPI를 사용하지 않는 경우는 생략하셔도 됩니다.

`main.py` 작성

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}
```

터미널(cmd)을 실행하고 'main.py'가 있는 파일로 이동하여 아래의 명령어를 실행합니다.

```bash
uvicorn main:app --reload
```

웹 브라우저에서 <http://localhost:8000> 에 접속합니다. 아래와 같이 응답이 옵니다.

```json
{"message": "Hello World"}
```

![로컬 웹브라우저]({{site.url}}/images/ngrok/ngrok-01_08.png)

## ngrok 실행

터미널(cmd)을 실행하고 'ngrok.exe'가 있는 파일로 이동합니다. ngrok을 실행합니다.

```bash
ngrok http 8000
```

로컬 서버가 8000으로 실행되어 있기 때문에 **ngrok도 8000**으로 실행합니다. 아래와 같이 출력 됩니다.

```shell
ngrok                                                                                                                                                                                            (Ctrl+C to quit)

Build better APIs with ngrok. Early access: ngrok.com/early-access

Session Status                online
Account                       계정 (Plan: Free)
Version                       3.5.0
Region                        지역
Latency                       44ms
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://92ee-1-213-195-155.ngrok-free.app -> http://localhost:8000

Connections                   ttl     opn     rt1     rt5     p50     p90     
                              0       0       0.00    0.00    0.00    0.00   
```

- 외부에서 <https://92ee-1-213-195-155.ngrok-free.app>로 접속 하면 로컬 서버의 8000 포트로 접속 됩니다.
- <http://127.0.0.1:4040>는 외부에서 접속한 정보를 확인 할 수 있습니다.

테스트를 위해 **휴대폰의 5G를 사용하여 외부에서 접속**해 보십시오. 아래와 같은 응답이 오면 성공입니다.

```json
{"message": "Hello World"}
```

---

해시태그: #ngrok #python #fastapi

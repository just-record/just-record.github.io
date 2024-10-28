---
layout: single
title: "OpenAI GPTs - 02. GPT Action 연습"
categories: GPTs
tag: [OpenAI, GPTs, Action]
toc: true
toc_sticky: true
toc_label: "목차"
---

실제 API 서버를 구축하고 GPT Action을 사용하여 API 호출을 실행하는 방법을 연습합니다.

GitHub: <https://github.com/just-record/gpts_practice_202410> - 전체 코드

## 연습 내용

- 도시의 현재 날씨 정보 가져오기 - get방식
- 도시의 현재 날씨 정보 가져오기 - post방식
- 도시의 현재 날씨 정보 가져오기 - API Key 사용하여 인증

## 설치

```bash
# 터미널에서 실행
pip install fastapi
pip install uvicorn
pip request
```

## 1. Get 방식

### API 서버 구축 - Get

`app.py`: FastAPI 사용

```python
from fastapi import FastAPI, HTTPException
import uvicorn

app = FastAPI(title="Simple Weather API")

# Sample weather data storage
weather_data = {
    "서울": {
        "temperature": 15,
        "humidity": 55,
        "conditions": "맑음",
        # ...
        "last_updated": "2024-10-25T10:00:00",
    },
    "대구": {
        "temperature": 18,
        "humidity": 60,
        "conditions": "구름 조금",
        # ...
        "last_updated": "2024-10-25T10:00:00",
    },
    "부산": {
        "temperature": 19,  # Coastal climate influence
        "humidity": 65,
        "conditions": "구름많음",
        # ...
        "last_updated": "2024-10-25T10:00:00",
    }
}


@app.get("/weather/{city}")
def get_weather(city: str):
    if city not in weather_data:
        raise HTTPException(status_code=404, detail="City not found")
    return weather_data[city]

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)   
### 이 서비스는 HTTPS를 통해 서비스 되어야 함 ###
```

✔️ 서버 실행

```bash
python app.py
```

### API 호출 테스트 - Get

`call_api.py`: requests 사용

```python
import requests

# API base URL
### 실제 GPT Action의 API로 사용 하기 위해서는 HTTPS로 서비스 되어야 함
BASE_URL = "http://localhost:8000"

def get_weather(city):
    """Get weather for a specific city"""
    response = requests.get(f"{BASE_URL}/weather/{city}")
    print(f"\n Weather for {city}:")
    print(response.json())

# Test all endpoints
try:
    get_weather("서울")

except requests.exceptions.RequestException as e:
    print(f"Error occurred: {e}")
    if hasattr(e, 'response') and e.response is not None:
        print(f"Error details: {e.response.json()}")
```

✔️ 실행

```bash
python call_api.py
```

✔️ 결과

```plaintext
Weather for 서울:
{'temperature': 15, 'humidity': 55, 'conditions': '맑음', 'last_updated': '2024-10-25T10:00:00'}
```

### GPTs - Get

✔️ GPTs 생성하기

- Name: `Get Weather information`
- Description: `Get the weather information for a given city`
- Instructions:

```plaintext
**Context**: A user needs information related to the current weather of a specific city.

**Instructions**:
1. The user will provide a city name or a related landmark (e.g. Eiffel Tower, New York City). If the user does not provide one, ask for the relevant city or landmark.
2. If the user provides a landmark, convert it into the corresponding city name. If required, browse the web to verify the correct location.
3. Use a weather API to retrieve the current weather for the given city.
4. Present the relevant weather data (such as temperature, conditions, humidity, wind speed, etc.) in an easily understandable format for the user.
5. When calling API, the city name must be called in Korean.

**Additional Notes**:
- Assume the user prefers Korea weather units (e.g. Celsius) unless otherwise specified.
- If the user asks for specific weather details (e.g. rainfall, humidity, or wind speed), include those elements in your response.
- If the user says "Let's get started" or "What do I do?", explain the purpose of this Custom GPT and ask them to provide the name of the city or landmark.
```

✔️ GPTs Action schema 생성

- OpenAI의 공식 GPTs(Actions 스키마 생성해 주는 GPTs)를 사용
- <https://chatgpt.com/g/g-TYEliDU6A-actionsgpt>

> 요청

```python
def get_weather(city):
    """Get weather for a specific city"""
    response = requests.get(f"{BASE_URL}/weather/{city}")
    print(f"\n Weather for {city}:")
    print(response.json())

# Test all endpoints
try:
    get_weather("서울")
except requests.exceptions.RequestException as e:
    print(f"Error occurred: {e}")
    if hasattr(e, 'response') and e.response is not None:
        print(f"Error details: {e.response.json()}")


의 Action schema를 생성 해 줘
```

> 결과

아래의 url은 HTTPS로 서비스 되어야 함

```yaml
openapi: 3.1.0
info:
  title: Weather information API
  description: This API provides weather information for various cities
  version: 1.0.0
servers:
  - url: https://localhost:8000
    description: Main production server

paths:
  /weather/{city}:
    get:
      operationId: getWeather
      summary: Get weather for a specific city.
      description: Fetches the weather information for a specified city. The city name must be called in Korean.
      parameters:
        - name: city
          in: path
          required: true
          description: Name of the city to retrieve weather data for.
          schema:
            type: string
      responses:
        '200':
          description: Weather data for the specified city.
          content:
            application/json:
              schema:
                type: object
                properties:
                  city:
                    type: string
                    description: Name of the city.
                  temperature:
                    type: number
                    format: float
                    description: Temperature in the city.
                  condition:
                    type: string
                    description: Current weather condition in the city.
                required:
                  - city
                  - temperature
                  - condition
        '404':
          description: City not found.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
                    description: Error message when the city is not found.
                required:
                  - error
```

✔️ GPTs Action schema 등록

- Action: `create new action`
  - Authentication: None
  - Schema: 위에서 생성한 Open API 스키마
  - Privacy policy: None -> 추후 `Privacy policy` 에 대해 따로 설명

### 테스트 - Get

✔️ What is the weather like in Seoul?

```plaintext
The current weather in Seoul is as follows:
Temperature: 15°C
Conditions: Clear (맑음)
Humidity: 55%
Let me know if you need more details!
```

✔️ 부산의 날씨는?

```plaintext
현재 부산의 날씨는 다음과 같습니다:
기온: 19.5°C
날씨: 눈
습도: 75%
추가 정보가 필요하시면 말씀해 주세요!
```

✔️ 엑스코의 날씨는?

```plaintext
엑스코(EXCO)는 대구에 위치한 컨벤션 센터입니다. 대구의 날씨 정보를 가져오겠습니다.

현재 대구(엑스코)의 날씨는 다음과 같습니다:
기온: 18°C
날씨: 구름 조금
습도: 60%
다른 정보가 필요하시면 알려주세요!
```

## 2. Post 방식

`app.py`

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel   ### 추가된 부분
import uvicorn

app = FastAPI(title="Simple Weather API")

### 추가된 부분
# Pydantic 모델 정의
class WeatherRequest(BaseModel):
    city: str

# Sample weather data storage
# ... 생략

### 수정된 부분
@app.post("/weather")
def get_weather(request: WeatherRequest):
    if request.city not in weather_data:
        raise HTTPException(status_code=404, detail="City not found")
    return weather_data[request.city]

# ... 생략
```

✔️ 서버 실행 - 생략(위와 동일)

### API 호출 테스트 - Post

`call_api.py`

```python
# ... 생략

### 수정된 부분
def get_weather(city):
    """Get weather for a specific city"""
    # POST 요청을 위한 데이터 준비
    data = {"city": city}
    
    # POST 요청 보내기
    headers = {"Content-Type": "application/json"}
    response = requests.post(
        f"{BASE_URL}/weather",
        json=data,
        headers=headers
    )
    
    print(f"\nWeather for {city}:")
    print(response.json())

# ... 생략
```

✔️ 실행 - 생략(위와 동일)

✔️ 결과 - 생략(위와 동일)

### GPTs - Post

✔️ GPTs 생성하기 - 생략(위와 동일)

✔️ GPTs Action schema 생성

> 요청

```python
def get_weather(city):
    """Get weather for a specific city"""
    # POST 요청을 위한 데이터 준비
    data = {"city": city}
    
    # POST 요청 보내기
    headers = {"Content-Type": "application/json"}
    response = requests.post(
        f"{BASE_URL}/weather",
        json=data,
        headers=headers
    )
    
    print(f"\nWeather for {city}:")
    print(response.json())

# Test all endpoints
try:
    get_weather("서울")

except requests.exceptions.RequestException as e:
    print(f"Error occurred: {e}")
    if hasattr(e, 'response') and e.response is not None:
        print(f"Error details: {e.response.json()}")


의 Action schema를 생성 해 줘
```

> 결과

```yaml
# ... 생략

paths:
  /weather/{city}:
paths:
  /weather:
    post:
      operationId: getWeather
      summary: Get weather for a specific city
      description: Retrieves the current weather for a provided city.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                city:
                  type: string
                  description: The name of the city to retrieve weather for
              required:
                - city
      responses:
        '200':
          description: Weather data for the specified city.
          content:
            application/json:
              schema:
                type: object
                properties:
                  city:
                    type: string
                    description: Name of the city.
                  temperature:
                    type: number
                    format: float
                    description: Temperature in the city.
                  condition:
                    type: string
                    description: Current weather condition in the city.
                required:
                  - city
                  - temperature
                  - condition
      responses:
        '200':
          description: Successfully retrieved weather information
          content:
            application/json:
              schema:
                type: object
                properties:
                  temperature:
                    type: number
                    format: float
                    description: Current temperature in the city
                  humidity:
                    type: integer
                    description: Current humidity percentage in the city
                  description:
                    type: string
                    description: A short description of the weather
        '400':
          description: Bad Request - Invalid city parameter
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
                    description: Error message describing what went wrong
        '500':
          description: Server Error - Something went wrong on the server
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
                    description: Error message explaining server failure
```

✔️ GPTs Action schema 등록

- Action: `create new action`
  - Authentication: None
  - Schema: 위에서 생성한 Open API 스키마
  - Privacy policy: None

### 테스트 - Post

✔️ 생략 - Get과 동일

## 3. API Key 사용하여 인증

`app.py`

```python
from fastapi import FastAPI, HTTPException, Depends, Security
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from pydantic import BaseModel
import uvicorn

app = FastAPI(title="Simple Weather API")

### 추가된 부분
# Security scheme 설정
security = HTTPBearer()

### 추가된 부분
# API 키 설정 (실제 환경에서는 환경 변수나 보안 저장소에서 관리해야 함)
API_KEY = "your-secure-api-key-here"

# Pydantic 모델 정의
class WeatherRequest(BaseModel):
    city: str


### 추가된 부분
# 인증 확인 함수
async def verify_token(credentials: HTTPAuthorizationCredentials = Security(security)):
    print(credentials)
    print(credentials.credentials)
    print(API_KEY)
    if credentials.credentials != API_KEY:
        raise HTTPException(
            status_code=401,
            detail="Invalid authentication token",
            headers={"WWW-Authenticate": "Bearer"},
        )
    return credentials.credentials    

# Sample weather data storage
# weather_data = {
# ... 생략

@app.post("/weather")
# def get_weather(request: WeatherRequest):
async def get_weather(request: WeatherRequest, token: str = Depends(verify_token)):    ### 수정된 부분
    if request.city not in weather_data:
        raise HTTPException(status_code=404, detail="City not found")
    return weather_data[request.city]

# ... 생략
```

✔️ 서버 실행 - 생략(위와 동일)

### API 호출 테스트 - API Key

`call_api.py`

```python
# ... 생략

### 추가된 부분
API_KEY = "your-secure-api-key-here"  # 서버의 API_KEY와 동일해야 함

def get_weather(city):
    ### ... 생략

    ### 수정된 부분
    # headers = {"Content-Type": "application/json"}
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {API_KEY}"
    }

    # ... 생략

    ### 추가된 부분
    # 401 Unauthorized
    if response.status_code == 401:
        print("Authentication failed. Please check your API key.")
        return
    
    # 403 Forbidden
    if response.status_code == 403:
        print("Forbidden. You don't have permission to access this resource.")
        return
    
    # ... 생략

# ... 생략
```

✔️ 실행 - 생략(위와 동일)

✔️ 결과 - 생략(위와 동일)

### GPTs - API Key

✔️ GPTs 생성하기 - 생략(위와 동일)

✔️ GPTs Action schema 생성 - 생략(동일한 schema 사용)

✔️ GPTs Action schema 등록

- Action: `create new action`
  - Authentication:
    - Authentication Type: `API_KEY` 선택
    - API Key: `your-secure-api-key-here` 입력
    - Auth Type: `Bearer` 선택
  - Schema: 위에서 생성한 Open API 스키마
  - Privacy policy: 생략 (아래에서 Only me 선택)
- `Create` -> Option 선택 ( Only me / Anyone with the link / GPT Store)

### 테스트 - API Key

✔️ 생략 - Get과 동일

## Privacy policy - 개인정보 처리방침

- Only me로 적용 할 경우 입력하지 않아도 됨 -> 나머지는 모두 입력 필요
- 존재하지 않는 링크를 입력 해도 저장은 가능 함 -> 실 사용시에는 Privacy policy를 입력 해야 하고 진행 해야 함
- 아래는 GPT에게 Privacy policy에 대한 문의의 답

```markdown
### Why the Privacy Policy is Important

A privacy policy is a legal document that explains how you, as a service provider or developer, collect, use, store, and protect user data. Many platforms require it to ensure transparency and comply with data protection regulations, such as the General Data Protection Regulation (GDPR) in Europe or the California Consumer Privacy Act (CCPA) in the U.S.

When you integrate an API or app with a platform, it's essential to show users how their data will be handled. For instance, if your service collects personally identifiable information (PII) like emails, names, or user preferences, the privacy policy should state:

- What data is collected (e.g., name, location, browsing behavior).
- How the data is used (e.g., for improving services, marketing, analytics).
- How long the data is retained.
- Whether the data is shared with third parties.
- User rights (e.g., access to their data, deletion requests).
- Contact information if users have concerns about their privacy.

### Example Privacy Policy

Here’s an example of what you might include:

- What data we collect: We collect email addresses and usage data.
- How we use your data: We use your email to send important updates about our service, and usage data to improve our user experience.
- Data sharing: We don’t share your data with third parties, except for essential service providers such as payment processors or analytics platforms.
- Data retention: Your data is stored for as long as your account remains active or as required by law.
- User rights: You can request the deletion or modification of your data at any time.
- Contact: For questions about your privacy, please reach out to us at privacy@yourapp.com.
```

## `Confirm`, `Deny` Button

✔️ Consequential flag

<https://platform.openai.com/docs/actions/production/consequential-flag>

예를 들어 사용자를 대신해서 호텔 객실을 예약하고 결제한다면 자동으로 처리 하는게 나을까요? 사용자의 동의를 받도록 하는게 나을까요?

- x-openai-isConsequential: true
  - ChatGPT는 "실행하기 전에 반드시 사용자의 확인을 받아야 함"으로 처리
  - "항상 허용" 버튼을 표시하지 않음
- x-openai-isConsequential: false
  - ChatGPT는 "항상 허용" 버튼을 표시
  - "항상 허용" 버튼을 클릭하면 다음 부터는 사용자 동의 없이 자동으로 처리
- 이 필드가 없는 경우
  - 모든 GET 작업: 기본값 - false
  - 다른 모든 작업: true

### 작성 예시

```yaml
paths:
  /todo:
    get:
      operationId: getTODOs
      description: Fetches items in a TODO list from the API.
      security: []
    post:
      operationId: updateTODOs
      description: Mutates the TODO list.
      x-openai-isConsequential: true
```

- 원하는 모든 Action에 대해 각각 `x-openai-isConsequential`을 설정할 수 있음

### 참고

<https://community.openai.com/t/how-to-stop-custom-gpt-displaying-confirm-deny-buttons/874551>

---

해시태그: #OpenAI #GPTs #GPT Action #FastAPI #API #Get #Post #API Key #Authentication #Privacy policy #Consequential flag

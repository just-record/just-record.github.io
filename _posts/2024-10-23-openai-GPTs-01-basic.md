---
layout: single
title: "OpenAI GPTs - 01. GPT Action"
categories: GPTs
tag: [OpenAI, GPTs, Action]
toc: true
toc_sticky: true
toc_label: "목차"
---

## GPTs란

<https://openai.com/index/introducing-gpts/>

- ChatGPT의 맞춤형 버전: 사용자가 특정 목적에 맞게 커스터마이징 가능
- GPT 빌더나 편집기를 통해 쉽게 자신만의 GPTs를 만들고 편집 가능
- instructions(지침), extra knowledge(추가지식), and any combination of skills(다양한 기능)을 결합 가능
- 다른사람과 공유 가능

## 사용법

✔️ 다른 블로그 포스트 참조

- <https://spartacodingclub.kr/blog/how-to-use-gpts>
- <https://www.magicaiprompts.com/docs/gpts-complete-guide/how-to-use-gpts/>

## GPT Action

<https://platform.openai.com/docs/actions/introduction>

GPT Action은 ChatGPT 사용자가 자연어만으로 외부 애플리케이션과 RESTful API 호출을 통해 상호작용 할 수 있게 함

✔️ 작동 방식

- Function Calling을 활용하여 API 호출을 실행(ChatGPT의 데이터 분석 기능(Python 코드를 생성하고 실행하는)과 유사)
  - (1) 사용자의 질문과 관련된 API 호출을 결정
  - (2) API 호출에 필요한 json 입력을 생성
  - Action이 해당 json 입력을 사용하여 API 호출을 실행
- 개발자는 액션의 인증 메커니즘도 지정 가능 -> Custom GPT는 제3자 앱의 인증을 사용하여 API 호출을 실행
- 최종 사용자에게 API 호출의 복잡성을 숨기기: 사용자는 단순히 자연어로 질문하고, ChatGPT도 자연어로 출력을 제공

✔️ 강점

- API는 조직의 상호운용성을 제공하지만 제3자 API에 접근하려면 개발자의 상당한 노력이 필요
- GPT Actions는 개발자가 API 호출의 스키마를 설명하고, 인증을 구성하고, GPT에 몇 가지 지침을 추가하면 됨
- ChatGPT가 사용자의 자연어 질문과 API 계층 사이의 다리 역할

## Getting started with GPT Actions

<https://platform.openai.com/docs/actions/getting-started>

### `Weather.gov` API 호출 예시

국립기상서비스: <https://www.weather.gov/documentation/services-web-api>

사용자가 위도-경도 지점에 대한 일기 예보를 받을 수 있는 공개 API

#### API 호출 - 파이썬 코드를 사용하여 API 호출

<https://www.weather.gov/documentation/services-web-api>

```python
import requests


def get_weather(office, lat, lon):
    url = f"https://api.weather.gov/gridpoints/{office}/{lat},{lon}/forecast"
    
    response = requests.get(url)
    response.raise_for_status()
    return response.json()

print(get_weather('LWX', 97, 71))
```

```python
import requests


def get_weather(latitude, longitude):
    url = f"https://api.weather.gov/points/{latitude},{longitude}"
    
    response = requests.get(url)
    response.raise_for_status()
    return response.json()

print(get_weather(39.7456,-97.0892))
```

#### GPT Action 이용

✔️ Step 1: Open API 스키마 생성

GPT Action은 API 호출의 매개변수를 설명하기 위한 표준인 Open API 스키마가 필요

> Open API의 스키마를 생성 하는 GPTs 활용

<https://chatgpt.com/g/g-TYEliDU6A-actionsgpt>

```plaintext
"https://www.weather.gov/documentation/services-web-api" 로 가서 해당 페이지의 문서를 읽어보세요.

/points/{latitude},{longitude}와 /gridpoints/{office}/{gridX},{gridY}/forecast API 호출을 위한 Open API 스키마를 만들어주세요.
```

생성된 Open API 스키마

```yaml
openapi: 3.1.0
info:
  title: National Weather Service API
  description: Access weather forecasts and observations from the National Weather Service.
  version: 1.0.0
servers:
  - url: https://api.weather.gov
    description: NWS production server
paths:
  /points/{latitude},{longitude}:
    get:
      operationId: getPointData
      summary: Get forecast office and grid for a given latitude and longitude
      parameters:
        - name: latitude
          in: path
          required: true
          description: Latitude of the point
          schema:
            type: number
            format: float
        - name: longitude
          in: path
          required: true
          description: Longitude of the point
          schema:
            type: number
            format: float
      responses:
        '200':
          description: Point data including forecast office and grid
          content:
            application/json:
              schema:
                type: object
                properties:
                  properties:
                    type: object
                    properties:
                      gridId:
                        type: string
                      gridX:
                        type: integer
                      gridY:
                        type: integer
                      forecast:
                        type: string
                        format: uri

  /gridpoints/{office}/{gridX},{gridY}/forecast:
    get:
      operationId: getGridpointForecast
      summary: Get the forecast for a specific grid point
      parameters:
        - name: office
          in: path
          required: true
          description: Forecast office (e.g., "BOU")
          schema:
            type: string
        - name: gridX
          in: path
          required: true
          description: Grid X coordinate
          schema:
            type: integer
        - name: gridY
          in: path
          required: true
          description: Grid Y coordinate
          schema:
            type: integer
      responses:
        '200':
          description: Forecast data for the specified grid point
          content:
            application/json:
              schema:
                type: object
                properties:
                  properties:
                    type: object
                    properties:
                      periods:
                        type: array
                        items:
                          type: object
                          properties:
                            name:
                              type: string
                            temperature:
                              type: integer
                            temperatureUnit:
                              type: string
                            detailedForecast:
                              type: string

```

**핵심:** Open API 스키마에서 사용하는 **스키마 이름**과 **설명**에 특별히 주의를 기울이세요. ChatGPT는 이러한 이름과 설명을 사용하여 (a) 어떤 API 액션이 호출되어야 하는지와 (b) 어떤 매개변수가 사용되어야 하는지를 이해합니다. 만약 필드가 특정 값들로만 제한되는 경우, 설명적인 카테고리 이름을 가진 "enum"을 제공할 수도 있습니다.

✔️ Step 2: 인증 요구사항

- 2가지 인증 방식: API 키, OAuth
- 위의 Weather는 제3자 서비스는 인증이 필요하지 않으므로 이 단계는 생략 가능

✔️ Step 3: GPT action 생성 및 테스트

> 생성

- Name: `Get Weather Forecast`
- Description: `Get the weather forecast for a given latitude and longitude`
- Instructions:

```plaintext
**Context**: A user needs information related to a weather forecast of a specific location.

**Instructions**:
1. The user will provide a lat-long point or a general location or landmark (e.g. New York City, the White House). If the user does not provide one, ask for the relevant location
2. If the user provides a general location or landmark, convert that into a lat-long coordinate. If required, browse the web to look up the lat-long point. 
3. Run the "getPointData" API action and retrieve back the gridId, gridX, and gridY parameters.
4. Apply those variables as the office, gridX, and gridY variables in the "getGridpointForecast" API action to retrieve back a forecast
5. Use that forecast to answer the user's question 

**Additional Notes**: 
- Assume the user uses US weather units (e.g. Fahrenheit) unless otherwise specified
- If the user says "Let's get started" or "What do I do?", explain the purpose of this Custom GPT
```

- Action: `create new action`
  - Authentication: None
  - Schema: 위에서 생성한 Open API 스키마
  - Privacy policy: <https://www.weather.gov/privacy/>

> 테스트

생성된 GPTs(Get Weather Forecast) 이동

```python
# 채팅
What should I pack for a trip to the White House this weekend?
```

```markdown
For your trip to the White House this weekend, the weather looks pleasant but with some cooler temperatures in the evenings. Here's the forecast:

- Saturday (Oct 26th): Mostly sunny, with a high around 72°F. Winds will be light, between 3 and 12 mph.
- Saturday Night: Mostly clear, with a low around 44°F. Winds from the northwest at 6 to 10 mph.
- Sunday (Oct 27th): Sunny, with a high near 60°F. Light winds around 7 mph.
- Sunday Night: Mostly clear, with a low around 40°F.

Packing Tips:

- Daytime: Light clothing such as a t-shirt or light sweater should be comfortable. Bring sunglasses or a hat for the sunny weather.
- Evenings: It will get cooler, so pack a jacket or sweater for the evening and nighttime temperatures.
- Shoes: Comfortable walking shoes for touring.

Enjoy your trip!
```

---

해시태그: #OpenAI #GPTs #GPT Action #API #Weather.gov #Open API 스키마 #Action authentication

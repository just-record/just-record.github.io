---
layout: single
title: "Python - JSON(JavaScript Object Notation)"
categories: Python
tag: [Python, JSON]
toc: true
toc_sticky: true
toc_label: "목차"
---

## JSON 이란

- JSON은 간결하고 가벼운 데이터 교환 형식입니다.
- 사람이 읽고 쓰기 쉬우며, 기계가 구문 분석하고 생성하기 쉽습니다.
- 2001년 Douglas Crockford에 의해 처음 지정되었습니다.
- 당초에는 JavaScript 프로그래밍 언어의 하위 집합으로 설계되었지만, 현재는 언어에 독립적인 데이터 형식으로 사용됩니다.

사용 사례

- 웹 애플리케이션에서 브라우저와 서버 간에 데이터를 전송하는 데 널리 사용됩니다.
- 다양한 프로그래밍 언어에서 JSON 데이터를 쉽게 읽고 쓸 수 있습니다.
- REST API에서 데이터를 교환하는 표준 형식으로 자리 잡았습니다.
- NoSQL 데이터베이스(MongoDB 등)에서 데이터를 저장하는 형식으로 활용됩니다.
- 구성 파일, 로그 파일 등의 표현에도 사용될 수 있습니다.

다른 데이터 형식(XML, YAML 등)과의 비교

- XML: JSON보다 복잡하고 무거운 구조입니다. 태그를 사용하므로 가독성이 좋지 않습니다.
- YAML: JSON과 유사하지만 구문이 더 간결하고 가독성이 좋습니다. 하지만 JSON만큼 광범위하게 지원되지 않습니다.

## 'json' 모듈

파이썬 표준 라이브러리에 포함된 json 모듈을 사용하여 JSON 데이터를 처리할 수 있습니다.

- JSON 문자열을 인코딩/디코딩
- JSON 파일 읽기/쓰기 등의 기능을 제공합니다.

## JSON 인코딩

파이썬 객체를 JSON 문자열로 변환하는 방법입니다.

```python
import json

data = {
    "name": "Alice",
    "age": 25,
    "is_student": False
}

json_string = json.dumps(data)
print(json_string)
```

## JSON 디코딩

JSON 문자열을 파이썬 객체로 변환하는 방법입니다.

```python
import json

json_string = '{"name": "Alice", "age": 25, "is_student": false}'
data = json.loads(json_string)
print(data)
```

## JSON 파일 쓰기

JSON 파일을 쓰는 방법입니다.

```python
import json

# JSON 파일 쓰기
data = {
    "name": "Alice",
    "age": 25,
    "is_student": False
}

with open("data.json", "w") as file:
    json.dump(data, file)
```

결과: `data.json` 파일에 아래 내용이 저장됩니다.

```plaintext
{"name": "Alice", "age": 25, "is_student": false}
```

### JSON 파일 읽기

```python
import json

# JSON 파일 읽기
with open("data.json", "r") as file:
    data = json.load(file)
    print(data)
```

결과: `data.json` 파일의 내용이 파이썬 객체로 변환되어 출력됩니다.

```python
{'name': 'Alice', 'age': 25, 'is_student': False}
```

## 예외: 'JSONDecodeError'

JSON 데이터를 파이썬 객체로 변환할 때 예외가 발생할 수 있습니다.

```python
import json

json_string = '{"name": "Alice", "age": 25, "is_student": false}'
try:
    data = json.loads(json_string)
    print(data)
except json.JSONDecodeError as e:
    print("JSONDecodeError:", e)
```

## JSON과 웹 API

REST API와 JSON 데이터 전송

- RESTful API는 웹 서비스를 제공하는 가장 일반적인 방식 중 하나입니다.
- JSON은 REST API에서 데이터를 전송하는 주요 형식으로 사용됩니다.
- API 클라이언트와 서버 간에 JSON 형태로 데이터를 교환합니다.
- JSON은 가볍고 구조화된 데이터 표현 방식이므로 API 통신에 적합합니다.

requests 라이브러리를 이용한 JSON API 호출

- requests는 파이썬에서 HTTP 요청을 보내는 인기 있는 라이브러리입니다.
- JSON API를 호출하고 응답 데이터를 처리하는 데 유용합니다.

GET 요청 예시

```python
import requests

response = requests.get('https://api.example.com/data')
data = response.json()
print(data)
```

POST 요청 예시 (JSON 데이터 전송)

```python
import requests

data = {'name': 'John', 'age': 30}
headers = {'Content-Type': 'application/json'}
response = requests.post('https://api.example.com/user', json=data, headers=headers)
print(response.json())
```

- requests 라이브러리를 사용하면 URL 파라미터, 헤더, 인증 등을 쉽게 설정할 수 있습니다.
- 응답 데이터는 .json() 메서드를 호출하여 JSON 객체로 변환할 수 있습니다.

JSON은 웹 API 개발에서 데이터 전송 표준으로 자리잡았습니다. requests 라이브러리를 활용하면 파이썬에서 손쉽게 JSON API를 호출하고 데이터를 주고받을 수 있습니다.

## JSON과 데이터베이스

MongoDB와 JSON 데이터 저장

- MongoDB는 대표적인 NoSQL 문서 데이터베이스입니다.
- MongoDB는 JSON 객체와 유사한 BSON(Binary JSON) 형식으로 데이터를 저장합니다.
- Python에서 pymongo 라이브러리를 사용하여 MongoDB와 상호작용할 수 있습니다.

pymongo 라이브러리를 이용한 JSON 데이터 삽입

```python
# pip install pymongo
import pymongo

client = pymongo.MongoClient('mongodb://localhost:27017/')
db = client['mydatabase']
collection = db['users']

user = {'name': 'John', 'age': 30, 'city': 'New York'}
result = collection.insert_one(user)
print(result.inserted_id)
```

JSON과 매우 유사한 딕셔너리 형태로 데이터를 저장하고 조회할 수 있습니다.

## JSON 스키마란?

- JSON 스키마는 JSON 데이터의 구조와 형식을 정의하는 규칙 집합입니다.
- JSON 데이터가 특정 스키마를 따르는지 검증하고 유효성을 확인할 수 있습니다.
- 스키마에는 데이터 타입, 필수 필드, 범위 제약 조건 등이 지정될 수 있습니다.
- JSON 스키마를 사용하면 잘못된 JSON 데이터로 인해 발생할 수 있는 오류를 방지할 수 있습니다.

### JSON 스키마 검증

jsonschema는 파이썬에서 JSON 스키마를 다루기 위한 인기 있는 라이브러리입니다. 스키마를 정의하고 JSON 데이터를 스키마에 대해 검증할 수 있습니다.

```python
import jsonschema

schema = {
    "type": "object",
    "properties": {
        "name": {"type": "string"},
        "age": {"type": "number"},
        "city": {"type": "string"}
    },
    "required": ["name", "age"]
}

data = {"name": "John", "age": 30}

try:
    jsonschema.validate(instance=data, schema=schema)
    print("JSON data is valid")
except jsonschema.exceptions.ValidationError as e:
    print(f"JSON data is invalid: {e.message}")
```

## JSON 고급

JSON과 병렬 처리

- 대규모 JSON 데이터를 처리할 때 병렬 처리를 활용하면 성능을 향상시킬 수 있습니다.
- Python의 다중 프로세싱 또는 다중 스레딩을 사용하여 JSON 데이터를 분할하고 병렬로 처리할 수 있습니다.
- 예를 들어, 여러 개의 큰 JSON 파일을 동시에 읽어와 처리하거나, 웹 API에서 가져온 JSON 데이터를 병렬로 파싱할 수 있습니다.

JSON과 지연 로딩

- 매우 큰 JSON 데이터를 한 번에 모두 메모리에 로드하는 것은 비효율적일 수 있습니다.
- 지연 로딩(lazy loading) 기법을 사용하면 필요한 부분만 로드하고 나머지는 나중에 필요할 때 로드할 수 있습니다.
- Python의 제너레이터나 반복자를 활용하여 JSON 데이터를 스트리밍 방식으로 처리할 수 있습니다.
- 대용량 JSON 데이터를 효율적으로 다루는 데 도움이 됩니다.

JSON Web Token (JWT)

- JWT는 JSON 기반의 토큰 형식으로, 인증과 권한 부여에 주로 사용됩니다.
- JWT는 Header, Payload, Signature 세 부분으로 구성되며, 모두 Base64URL 인코딩된 JSON 데이터입니다.
- Python에서는 PyJWT 라이브러리를 사용하여 JWT를 생성, 검증, 디코딩할 수 있습니다.
- JWT는 웹 애플리케이션, 모바일 앱, 마이크로서비스 등에서 인증 방식으로 널리 사용됩니다.

## 예제

데이터 다루기

```python
import json

# JSON 문자열을 Python 객체로 변환
json_str = '{"name": "Alice", "age": 25, "city": "New York"}'
data = json.loads(json_str)
print(data)
print(data["name"])

# Python 객체를 JSON 문자열로 변환
person = {"name": "Bob", "age": 30, "city": "London"}
json_str = json.dumps(person)
print(json_str)

# JSON 파일 읽기/쓰기
with open("data.json", "w") as f:
    json.dump(person, f, indent=4)

with open("data.json", "r") as f:
    data = json.load(f)
    print(data)
```

JSON API 호출

```python
import requests

# GET 요청: 실제 서비스 되고 있지 않은 URL입니다.
response = requests.get("https://api.example.com/users")
data = response.json()
print(data)

# POST 요청
headers = {"Content-Type": "application/json"}
payload = {"name": "Charlie", "age": 35}
response = requests.post("https://api.example.com/users", headers=headers, json=payload)
print(response.json())
```

MongoDB와 상호작용

```python
import pymongo

client = pymongo.MongoClient("mongodb://localhost:27017/")

db = client["mydatabase"]
collection = db["users"]

user = {"name": "David", "age": 40, "city": "Paris"}
result = collection.insert_one(user)
print(result.inserted_id)
```

---

해시태그: #Python #JSON #dumps #loads #dump #load

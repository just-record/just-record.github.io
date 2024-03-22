---
layout: single
title: "Python - Pydantic"
categories: Python
tag: [Python, Pydantic]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Pydantic 이란?

pydantic은 Python의 Type Annotations을 활용하여 데이터 유효성을 검사 하는 라이브러리입니다.

- 데이터 모델링: pydantic을 사용하면 데이터 모델을 정의할 수 있습니다. 이 모델은 데이터의 구조, 유형, 제약 조건 등을 정의합니다.
- 데이터 유효성 검사: pydantic은 정의된 모델에 따라 데이터의 유효성을 자동으로 검사합니다. 잘못된 유형의 데이터나 제약 조건을 위반하는 경우 예외를 발생시킵니다.
- 데이터 변환: pydantic은 데이터 유형 간 자동 변환을 지원합니다. 예를 들어 문자열을 날짜 객체로 변환하거나 JSON 데이터를 Python 객체로 변환할 수 있습니다.
- JSON 지원: pydantic은 JSON 데이터를 Python 객체로, Python 객체를 JSON 데이터로 쉽게 변환할 수 있습니다.
- 재사용 가능한 모델: pydantic 모델은 다른 모델에서 상속하거나 포함할 수 있어 코드 재사용성이 높습니다.
- 별표 가져오기: pydantic은 데이터 모델의 필드를 편리하게 가져올 수 있습니다.
- 성능: pydantic은 데이터 유효성 검사와 변환에서 높은 성능을 제공합니다.

pydantic은 주로 웹 애플리케이션, API, 데이터 파이프라인 등에서 사용됩니다. 예를 들어 FastAPI나 Flask 같은 웹 프레임워크와 함께 사용하면 API 입력 데이터의 유효성 검사와 변환을 자동으로 처리할 수 있습니다. 또한 데이터 파이프라인에서 데이터 구조를 정의하고 유효성을 검사하는 데 유용합니다.

## BaseModel

pydantic의 핵심 클래스인 BaseModel을 상속하여 데이터 모델을 정의합니다. BaseModel은 데이터 모델의 필드, 유효성 검사, 데이터 변환 등을 제공합니다.

```python
# pydantic의 BaseModel을 상속하여 데이터 모델을 정의
from pydantic import BaseModel

class User(BaseModel):
    id: int
    name: str
    email: str
    is_active: bool = True
```

- User 클래스는 id, name, email, is_active 필드를 갖는 데이터 모델
- 각 필드의 데이터 유형 지정 (예: id: int, name: str, email: str)
- 기본값 설정 (예: is_active: bool = True)

## 데이터 유효성 검사

```python
from pydantic import BaseModel

class User(BaseModel):
    id: int
    name: str
    email: str
    is_active: bool = True

user = User(id=1, name='John', email='john@example.com')
print(user)  # id=1 name='John' email='john@example.com' is_active=True

# 유효하지 않은 데이터
try:
    invalid_user = User(id='a', name=123, email='invalid')
except ValueError as e:
    print(e)
```

- 'is_active'는 생략 되어도 기본값에 의해 True로 설정됩니다.
- 'invalid_user'는 'id'가 문자열, 'name'이 정수로 전달되어 ValueError가 발생합니다.

## 데이터 변환

```python
from pydantic import BaseModel
from datetime import date

class Person(BaseModel):
    name: str
    birth_date: date

person = Person(name='Alice', birth_date='1990-01-01')
print(person)  # name='Alice' birth_date=datetime.date(1990, 1, 1)
```

- birth_date는 문자열로 전달되었지만, date 유형으로 자동 변환되었습니다.

## Dict 지원

```python
from pydantic import BaseModel

class User(BaseModel):
    id: int
    name: str
    email: str
    is_active: bool = True

user_dict = {"id": 2, "name": "Jane", "email": "jane@example.com"}
user = User(**user_dict)
print(user)  # id=2 name='Jane' email='jane@example.com' is_active=True

print(dict(user))  # {"id": 2, "name": "Jane", "email": "jane@example.com", "is_active": true}
```

---

해시태그: #Python #pydantic #BaseModel #데이터모델링 #데이터유효성검사 #데이터변환 #Dict지원

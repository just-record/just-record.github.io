---
layout: single
title: "FastAPI - Session"
categories: FastAPI
tag: [FastAPI, Session]
toc: true
toc_sticky: true
toc_label: "목차"
---

## 세션(Session) 이란?

웹 애플리케이션에서는 사용자가 **웹사이트를 방문할 때마다 새로운 요청**을 보냅니다. 하지만 HTTP는 **이전 요청과의 연관성을 기억하지 못합니다.** 예를 들어 사용자가 장바구니에 상품을 담고 다른 페이지로 이동하면(새로운 요청), 이전에 담은 상품 정보를 기억하지 못합니다.

세션은 **사용자별(실제로는 조금 다름)로 공간을 마련하여 이 공간에 사용자의 정보나 상태를 저장**합니다. 예를 들어, 로그인 상태나 장바구니 내역 등을 세션에 저장할 수 있습니다.

세션은 **서버 측에서 생성되며, 클라이언트(브라우저)에는 세션 ID라는 작은 키 값만 전달**됩니다. 브라우저는 이 세션 ID를 쿠키나 URL 매개변수로 저장합니다. 그리고 **다음 요청 시 서버에 세션 ID를 함께 보내면**, 서버는 이 ID를 통해 해당 사용자의 세션을 찾아 데이터를 읽거나 쓸 수 있습니다.

세션을 사용하면 사용자의 상태를 지속적으로 유지할 수 있고 세션 데이터는 서버 측에 저장되므로 보안성이 높고, 데이터 크기에 제한이 없는 장점이 있습니다.

하지만 세션 데이터가 서버 측에 저장되므로 서버 자원을 사용하므로 **서버의 부하가 증가**할 수 있습니다.

### 세션 작동 방식

1. 사용자가 웹 사이트에 접속하면 서버는 사용자 브라우저에 세션 쿠키를 생성
2. 사용자가 웹 사이트를 이용하면서 서버는 세션 쿠키를 통해 사용자를 식별하고 세션 정보에 저장
3. 사용자가 웹 사이트를 종료하거나 브라우저를 닫으면 세션 쿠키는 만료되거나 삭제

### 웹 애플리케이션에서 세션의 역할

- 사용자 식별: 사용자 로그인 정보를 저장하여 로그인 상태를 유지
- 상태 유지: 사용자의 작업 상태를 저장하여 페이지 이동 시에도 정보를 유지
- 개인화: 사용자 선호도에 맞게 웹 사이트를 개인화

### FastAPI에서 세션

FastAPI는 웹 API 개발을 위한 프레임워크입니다. API 서버에서 Session을 사용 하는 이유는 무엇일까요?

- 사용자 정보: 사용자 정보를 저장하여 사용자에게 맞는 데이터를 제공
- 상태 유지: 사용자의 작업 상태를 저장하여 다른 API 요청 시에도 정보를 유지
- 데이터 공유: 여러 API 요청에서 공유할 데이터를 저장

## 세션 미들웨어 (SessionMiddleware)

FastAPI는 자체적으로 세션 관리 기능이 내장되어 있지 않지만 starlette의 SessionMiddleware를 사용하여 세션을 관리할 수 있습니다.

일반적으로 Session 정보는 서버 측에 저장되나 **SessionMiddleware는 클라이언트의 쿠키에 직접 저장**합니다.

'Starlette' 설치:

```bash
pip install starlette
```

FastAPI에서 세션 미들웨어를 사용하려면 `SessionMiddleware`를 FastAPI 앱에 추가해야 합니다.

```python
from fastapi import FastAPI
from starlette.middleware.sessions import SessionMiddleware

app = FastAPI()

# Set up middleware with a secret key
app.add_middleware(SessionMiddleware, secret_key="your_secret_key")
```

### 시크릿 키(Secret Key)

`secret_key`: 세션 암호화를 위한 비밀 키로 주로 32바이트 이상의 랜덤 문자열을 사용합니다.

생성 예시 - 터미널에서 실행:

```bash
# Linux에서만 가능
openssl rand -hex 32

# 또는
python -c 'import os; print(os.urandom(32).hex())'
```

### 세션 미들웨어 예시

```python
from fastapi import FastAPI, Request
from starlette.middleware.sessions import SessionMiddleware

app = FastAPI()
app.add_middleware(SessionMiddleware, secret_key="your_secret_key")

@app.get("/")
async def read_items(request: Request):
    user_count = request.session.get("user_count", 0)
    request.session["user_count"] = user_count + 1
    return {"user_count": user_count}

@app.get("/logout/")
async def logout(request: Request):
    request.session.pop("user_count", None)
    return {"message": "Logged out"}
```

- `request.session.get("user_count", 0)`: 세션에서 `user_count` 키의 값을 가져옵니다. 키가 없으면 기본값 0을 반환합니다.
- `request.session["user_count"] = user_count + 1`: 세션에 `user_count` 키의 값을 1 증가시킵니다.
- `request.session.pop("user_count", None)`: 세션에서 `user_count` 키를 삭제합니다.
- `request.session`: 세션 데이터에 접근할 수 있는 딕셔너리 객체
- `request.session.clear()`: 세션 데이터를 모두 삭제합니다.

FastAPI 실행:

```bash
uvicorn main:app --reload
```

접속 URL: `http://localhost:8000/`

테스트 절차:

1. `http://localhost:8000/` 접속 -> `{"user_count": 0}` 확인
2. reload -> `{"user_count": 1}` 확인
3. 다른 브라우저로 접속 -> `{"user_count": 0}` 확인
4. `http://localhost:8000/logout/` 접속 -> `{"message": "Logged out"}` 확인
5. `http://localhost:8000/` 접속 -> `{"user_count": 0}` 확인 (카운트 초기화)

- 쿠기 확인: 브라우저 개발자 도구(F12) > Application 탭 > Cookies
- 'logout'을 하면 쿠키가 삭제되어 세션 데이터가 초기화됩니다.

### 세션 미들웨어 설정

예시:

```python
app.add_middleware(
    SessionMiddleware,
    secret_key="your_secret_key",
    session_cookie="session_id",  # 세션 쿠키 이름
    max_age=1800,  # 세션 만료 시간 (초)
    same_site="strict",  # 쿠키 SameSite 속성
    https_only=True,  # HTTPS 전용 쿠키
)
```

- `session_cookie`: 세션 쿠키 이름
- `max_age`: 세션 만료 시간 (초)
- `same_site`: 쿠키 SameSite 속성
  - SameSite 속성은 쿠키가 어떤 상황에서 서버로 전송되는지 제어하는 속성
  - `strict`: 쿠키가 동일 출처 요청에만 전송, CSRF 공격을 방지할 수 있음
  - 그 외 `lax`, `none`, `default`
- `https_only`: HTTPS 전용 쿠키

## 초간단 login 예시 - 세션 미들웨어

```python
from fastapi import FastAPI, Request
from starlette.middleware.sessions import SessionMiddleware

app = FastAPI()
app.add_middleware(SessionMiddleware, secret_key="your_secret_key")

def create_session(request: Request, username: str) -> None:
    dict_value = {'user_id': username, 'age': 21, 'email': 'aaa@aaa.com'}
    request.session['username'] = dict_value
    
def get_session(request: Request) -> object:
    return request.session.get('username', None)

def update_session(request: Request, username: str, value: object) -> None:
    request.session['username'] = value

def clear_session(request: Request) -> None:
    if request.session:
        request.session.clear()
    

@app.get("/login/{username}/")
async def login(request: Request, username: str):
    create_session(request, username=username)
    return {"message": "Logged in"}


@app.get("/logout/")
async def logout(request: Request):
    clear_session(request)
    return {"message": "Logged out"}

@app.get("/get/")
async def get(request: Request):
    username_value = get_session(request)
    if not username_value:
        return {"message": "Login first"}
    return {"session_info": username_value} 
```

- 간단히 테스트 하기 위해 로그인은 Path Parameter로 처리
- 세션 정보는 브라우저(클라이언트)에 저장되므로 서버를 재시작해도 세션 정보가 유지

테스트 절차:

1. `http://localhost:8000/login/hong/` 접속 -> `{"message": "Logged in"}` 확인
2. `http://localhost:8000/get/` 접속 -> `{"session_info": {"user_id": "hong", "age": 21, "email": "aaa@aaa.com"}}` 확인
3. `http://localhost:8000/logout/` 접속 -> `{"message": "Logged out"}` 확인
4. `http://localhost:8000/get/` 접속 -> `{"message": "Login first"}` 확인

## 세션 정보를 크게

세션 미들웨어는 세션 정보를 브라우저 쿠키에 저장하므로 세션 정보가 많아지면 쿠키 크기가 커질 수 있습니다. 쿠키 크기는 **4KB**로 제한되어 있으므로 세션 정보가 많아지면 쿠키가 잘리거나 삭제될 수 있습니다.

세션 정보를 크게 만들어 보겠습니다.

```python
def create_session(request: Request, username: str) -> None:
    # dict_value = {'user_id': username, 'age': 21, 'email': 'aaa@aaa.com'}
    dict_value = {}
    for i in range(1000000):
        dict_value[i] = i
    request.session['username'] = dict_value
```

쿠키가 생성 되는 않는 걸 확인 할 수 있습니다.

## 서버에 세션 정보 저장 - FastAPI

FastAPI에서 세션 정보를 서버에 저장하려면 **데이터베이스**를 사용하거나 **캐시**를 사용할 수 있습니다.

- 데이터베이스: 세션 정보를 데이터베이스에 저장
- 캐시: 세션 정보를 캐시에 저장

FastAPI에서는 **Redis**를 사용하여 세션 정보를 캐시에 저장할 수 있습니다.

Redis는 메모리 기반의 오픈 소스 키-값 저장소로서, NoSQL 데이터베이스로 분류됩니다. 인메모리와 NoSQL로 빠른 속도와 다양한 데이터 구조를 지원합니다.

### 데이터베이스 사용

SessionMiddleware를 사용하여 username만 쿠키에 저장하고, 브라우저로부터 받은 username을 통해 데이터베이스(PostgreSQL)에 세션 정보를 저장하고 조회하는 방법을 가볍게 구현해 보았습니다. 제가 임의로 만든 예시이며, 실제로 사용하기에 적합하지 않습니다.

```python
from fastapi import FastAPI, Request
from starlette.middleware.sessions import SessionMiddleware
from sqlalchemy import create_engine, Column, Integer, String, MetaData, Table
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker


DATABASE_URL = "postgresql://user:password@localhost/dbname"
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

class Session(Base):
    __tablename__ = "sessions"
    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, index=True)
    age = Column(Integer)
    email = Column(String)

Base.metadata.create_all(bind=engine)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

app = FastAPI()
app.add_middleware(SessionMiddleware, secret_key="your_secret_key")



def create_session(request: Request, username: str) -> None:
    request.session['username'] = username
    session = Session(username=username, age=21, email='abc') 

    db = next(get_db())
    db.add(session)
    db.commit()    
    
def get_session(request: Request) -> object:
    username = request.session.get('username', None)
    if username:
        db = next(get_db())
        return db.query(Session).filter(Session.username == username).first()
    return None

def clear_session(request: Request) -> None:
    if request.session:
        username = request.session.get('username', None)
        if username:
            db = next(get_db())
            db.query(Session).filter(Session.username == username).delete()
            db.commit()

        request.session.clear()
    

@app.get("/login/{username}/")
async def login(request: Request, username: str):
    create_session(request, username=username)
    return {"message": "Logged in"}


@app.get("/logout/")
async def logout(request: Request):
    clear_session(request)
    return {"message": "Logged out"}

@app.get("/get/")
async def get(request: Request):
    username_value = get_session(request)
    if not username_value:
        return {"message": "Login first"}
    return {"session_info": username_value} 
```

---

해시태그: #Python #FastAPI #Session #SessionMiddleware #Starlette #Redis #Database #PostgreSQL

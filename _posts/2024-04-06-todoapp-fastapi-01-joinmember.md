---
layout: single
title: "FastAPI를 사용하여 TODO 애플리케이션 만들기 - 01 DB설정 및 회원 가입" 
categories: TODO-App
tag: [Python, FastAPI, TODO]
toc: true
toc_sticky: true
toc_label: "목차"
---

FastAPI를 사용하여 서비스를 개발하여 FastAPI와 Python의 기능을 익히고자 합니다. 애플리케이션은 TODO 애플리케이션을 개발하고자 합니다. 이번 내용은 서비스 개발에 중점을 두었습니다. Python과  FastAPI 기능에 대한 내용은 생략하겠습니다. 추가로 Dabtbase는 PostgreSQL을 사용합니다.

첫 번째로 데이터베이스 설정 및 회원 가입 기능입니다.

## 1. FastAPI 실행

`main.py`(새로 생성): FastAPI 애플리케이션을 생성합니다.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")

def read_root():
    return {"message": "Hello, FastAPI!"}
```

FastAPI 애플리케이션을 실행합니다.

```bash
uvicorn main:app --reload
# 또는
# uvicorn main:app --reload --host 0.0.0.0 --port=8000
```

`http://localhost:8000`에 접속하여 `{"message": "Hello, FastAPI!"}`가 출력되는 것을 확인할 수 있습니다.

## 2. Database 연결

- `database.py`: 데이터베이스 연결을 위한 파일을 생성합니다.
- `models.py`: 데이터베이스 모델을 생성합니다.
- `main.py`: FastAPI 애플리케이션에 데이터베이스 연결을 추가합니다.
- FastAPI 애플리케이션을 실행 하면 PostgreSQL 데이터베이스에 `users`와 `todos` 테이블이 생성됩니다.
- PostgreSQL가 아닌 다른 데이터베이스를 사용 하셔도 됩니다.

`db/database.py`(새로 생성): 데이터베이스 연결을 위한 파일을 생성합니다.

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import declarative_base, sessionmaker

# user01 사용자, user01password 비밀번호, db01 데이터베이스
SQLALCHEMY_DATABASE_URL = "postgresql://user01:user01password@localhost/db01"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, future=True
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine, future=True)
Base = declarative_base()
```

`models/models.py`(새로 생성): 데이터베이스 모델을 생성합니다.

```python
from sqlalchemy import Column, Integer, String, Boolean, ForeignKey
from sqlalchemy.orm import relationship
from db.database import Base


class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, index=True, unique=True)
    fullname = Column(String)
    password = Column(String)
    is_active = Column(Boolean, default=True)

    #  relationship 함수를 사용하여 Item 모델과 연결
    todos = relationship("Todo", back_populates="user")

class Todo(Base):
    __tablename__ = "todos"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(String)
    completed = Column(Boolean, default=False)
    user_id = Column(Integer, ForeignKey("users.id"))

    #  relationship 함수를 사용하여 User 모델과 연결
    user = relationship("User", back_populates="todos")
```

`main.py`(수정): FastAPI 애플리케이션에 데이터베이스 연결을 추가합니다.

```python
from fastapi import FastAPI
from db.database import SessionLocal, engine
from db.models import Base

Base.metadata.create_all(bind=engine)

app = FastAPI()

@app.get("/")

def read_root():
    return {"message": "Hello, FastAPI!"}


def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

## 3. endpoint(users) 추가

### 3.1. users route 추가

`api/endpoints/users.py`(새로생성): 사용자 데이터를 처리하는 endpoint를 추가합니다.

```python
from fastapi import APIRouter

router = APIRouter(prefix="/users")


@router.post("/add_user/")
def add_user():
    return {"message": "add_user"}
```

`main.py`(수정): FastAPI 애플리케이션에 `users` endpoint를 추가합니다.

```python
from fastapi import FastAPI
from db.database import SessionLocal, engine
from db.models import Base

from api.endpoints import users

Base.metadata.create_all(bind=engine)

app = FastAPI()

app.include_router(users.router)

@app.get("/")


def read_root():
    return {"message": "Hello, FastAPI!"}


def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

`http://localhost:8000/docs`에 접속하여 `users` endpoint를 확인합니다.

### 3.2. users schema 추가

`schemas/schema_user.py`(새로생성): 사용자 데이터를 위한 스키마를 생성합니다.

```python
from pydantic import BaseModel, ConfigDict

class UserBase(BaseModel):
    username: str
    fullname: str | None = None


class UserCreate(UserBase):
    password: str


class User(UserBase):
    id: int
    is_active: bool

    class Config:
        # orm_mode = True
        model_config = ConfigDict(from_attributes=True)


class UserInDB(User):
    password: str
```

`services/service_user.py`(새로생성): 사용자 데이터를 처리하는 CRUD 함수를 생성합니다.

```python
from sqlalchemy.orm import Session
from models.models import User

def get_user(db: Session, user_id: int):
    return db.query(User).filter(User.id == user_id).first()

def get_user_by_name(db: Session, username: str):
    return db.query(User).filter(User.username == username).first()

def create_user(db: Session, user: User):
    db_user = User(username=user.username, password=user.password)
    db.add(db_user)
    db.commit() 
    db.refresh(db_user)
    return db_user
```

### 3.3. users endpoint 수정

`api/endpoints/users.py`(수정): 사용자 데이터를 처리하는 endpoint를 추가합니다.

```python
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from db.database import SessionLocal
from schemas.schema_user import User, UserCreate
from services.service_user import get_user_by_name, create_user

router = APIRouter(prefix="/users")

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@router.post("/add_user/", response_model=User)
def add_user(user: UserCreate, db: Session = Depends(get_db)):
    db_user = get_user_by_name(db, username=user.username)
    if db_user:
        raise HTTPException(status_code=400, detail="User already registered")
    return create_user(db=db, user=user)
```

### 3.4. users endpoint 테스트

- `http://localhost:8000/docs`에 접속
- `POST /users/add_user/` endpoint를 실행
- `Request Body`에 `username`, `fullname`, `password`를 입력하고 `Execute` 버튼을 클릭
- `Response`에 `id`, `name`, `is_active`가 출력되는 것을 확인할 수 있습니다.
- Database에 `users` 테이블에 사용자 데이터가 추가되는 것을 확인할 수 있습니다.

---

해시태그: #TODO #FastAPI #Python #PostgreSQL #SQLAlchemy #CRUD #User #Endpoint #Schema #Service #Route

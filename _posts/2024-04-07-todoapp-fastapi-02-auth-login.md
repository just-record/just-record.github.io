---
layout: single
title: "FastAPI를 사용하여 TODO 애플리케이션 만들기 - 02 인증: 로그인" 
categories: TODO-App
tag: [Python, FastAPI, TODO]
toc: true
toc_sticky: true
toc_label: "목차"
---

FastAPI를 사용하여 서비스를 개발하여 FastAPI와 Python의 기능을 익히고자 합니다. 애플리케이션은 TODO 애플리케이션을 개발하고자 합니다. 이번 내용은 서비스 개발에 중점을 두었습니다. Python과  FastAPI 기능에 대한 내용은 생략하겠습니다. 추가로 Dabtbase는 PostgreSQL을 사용합니다.

두 번째로 로그인 기능입니다.

## 4. Login

로그인을 위한 endpoint를 추가하고, 로그인 성공 시 JWT 토큰을 생성합니다.

### `db/utils.py`(새로생성)

get_db 함수를 작성 하여 공용으로 사용할 수 있도록 합니다.

```python
from .database import SessionLocal

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

### `api/endpoints/users.py`(수정)

get_db 함수 코드를 삭제하고 위의 'db/utils.py'에 있는 get_db 함수를 import

```python
from fastapi import APIRouter, Depends, HTTPException
from db.utils import get_db
from sqlalchemy.orm import Session
from schemas.schema_user import User, UserCreate
from services.service_user import get_user_by_name, create_user

router = APIRouter(prefix="/users")


@router.post("/add_user/", response_model=User)
def add_user(user: UserCreate, db: Session = Depends(get_db)):
    db_user = get_user_by_name(db, username=user.username)
    if db_user:
        raise HTTPException(status_code=400, detail="User already registered")
    return create_user(db=db, user=user)
```

### `shemas/schema_user.py`(수정)

Token, TokenData 스키마를 추가합니다.

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


class Token(BaseModel):
    access_token: str
    token_type: str


class TokenData(BaseModel):
    username: str | None = None        
```

### `api/endpoints/auth.py`(새로 생성)

로그인을 위한 endpoint를 추가하고, JWT토큰 생성 기능을 구현합니다. 토큰을 생성 하기 위해서는 secret_key가 필요합니다.

```python
from datetime import datetime, timedelta, timezone
from fastapi import APIRouter, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordRequestForm
from jose import jwt, JWTError
from db.utils import get_db
from services.service_user import get_user_by_name, verify_password
from schemas.schema_user import Token

### secret_key: 토큰 발생 시 사용 
# linux만: openssl rand -hex 32
# 또는: python -c "import os; print(os.urandom(32).hex())"
secret_key = '1184cf8c81a173ab236d500cd2a98cf37cf2ad32d33052a523107d892dfad6a3'
algorithm = 'HS256'
ACCESS_TOKEN_EXPIRE_MINUTES = 30


router = APIRouter()

# JWT 토큰 생성
def create_access_token(data: dict, expires_delta: timedelta | None = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.now(timezone.utc) + expires_delta
    else:
        expire = datetime.now(timezone.utc) + timedelta(minutes=15)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, secret_key, algorithm=algorithm)
    return encoded_jwt


# 로그인 - 성공 시 토큰 생성
@router.post("/token", response_model=Token)
async def login(form_data: OAuth2PasswordRequestForm = Depends(), db = Depends(get_db)):
    user = get_user_by_name(db, form_data.username)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
            )

    if not verify_password(form_data.password, user.password):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
            )
    
    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user.username}, expires_delta=access_token_expires
    )
    return {"access_token": access_token, "token_type": "bearer"}
```

### `main.py`(수정)

추가된 auth endpioint include하고 get_db 함수의 코드를 삭제하고 import 합니다.

```python
from fastapi import FastAPI
from db.database import SessionLocal, engine
from models.models import Base

from api.endpoints import users
from api.endpoints import auth

Base.metadata.create_all(bind=engine)

app = FastAPI()

app.include_router(users.router)
app.include_router(auth.router)

@app.get("/")

def read_root():
    return {"message": "Hello, FastAPI!"}
```

### 테스트

<http://localhost:8000/docs> 에 접속하여 `token` endpoint를 확인합니다.

- 회원가입 하셨던 사용자로 로그인을 시도합니다.
- '200' status code와 함께 토큰이 발급되는지 확인합니다.

---

해시태그: #TODO #FastAPI #Python #Login #JWT #Token #Authentication

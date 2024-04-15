---
layout: single
title: "FastAPI를 사용하여 TODO 애플리케이션 만들기 - 03 인증: 현재 사용자" 
categories: TODO-App
tag: [Python, FastAPI, TODO]
toc: true
toc_sticky: true
toc_label: "목차"
---

FastAPI를 사용하여 서비스를 개발하여 FastAPI와 Python의 기능을 익히고자 합니다. 애플리케이션은 TODO 애플리케이션을 개발하고자 합니다. 이번 내용은 서비스 개발에 중점을 두었습니다. Python과  FastAPI 기능에 대한 내용은 생략하겠습니다. 추가로 Dabtbase는 PostgreSQL을 사용합니다.

github: [https://github.com/just-record/todo_fastapi](https://github.com/just-record/todo_fastapi)

- 각 단계별로 branch를 생성하여 작업합니다.

세 번째로 JWT 토큰을 이용하여 현재 사용자를 확인하는 기능입니다.

## 5. 현재 사용자

### `api/endpoints/auth.py`(수정)

전송 된 JWT 토큰을 이용 하여 현재 사용자를 확인하는 endpoint('me')를 추가합니다.

- endpoint('users/me'): 전송 된 JWT 토큰을 이용 하여 현재 사용자를 확인
- get_current_user 함수: JWT 토큰을 이용하여 현재 사용자를 확인하는 함수
- get_current_active_user 함수: get_current_user 함수를 이용하여 현재 사용자를 조회 하고 해당 사용자가 활성화 되어 있는지 확인하는 함수

```python
from datetime import datetime, timedelta, timezone
from typing import Annotated
from fastapi import APIRouter, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import jwt, JWTError
from db.utils import get_db
from models.models import User
from services.service_user import get_user_by_name, verify_password
from schemas.schema_user import Token, TokenData
from sqlalchemy.orm import Session

secret_key = '1184cf8c81a173ab236d500cd2a98cf37cf2ad32d33052a523107d892dfad6a3'
algorithm = 'HS256'
ACCESS_TOKEN_EXPIRE_MINUTES = 30

# OAuth2PasswordBearer: 토큰을 받아서 사용자를 확인하는 함수
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


# 포맷 변경
def create_access_token(
    data: dict, 
    expires_delta: timedelta | None = None
):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.now(timezone.utc) + expires_delta
    else:
        expire = datetime.now(timezone.utc) + timedelta(minutes=15)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, secret_key, algorithm=algorithm)
    return encoded_jwt


# 현재 사용자 확인 - JWT 토큰의 사용자 확인
async def get_current_user(
    token: Annotated[str, Depends(oauth2_scheme)], 
    db: Annotated[Session, Depends(get_db)]
):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, secret_key, algorithms=algorithm)
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
        token_data = TokenData(username=username)
    except JWTError:
        raise credentials_exception
    username = token_data.username
    user = get_user_by_name(db=db, username=username)
    if user is None:
        raise credentials_exception
    return user


# get_current_user 함수를 이용하여 조회한 사용자가 활성화 되어 있는지 확인
async def get_current_active_user(
    current_user: Annotated[User, Depends(get_current_user)]
):
    if not current_user.is_active:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST, 
            detail="Inactive user")
    return current_user


# 위치 변경
router = APIRouter()


@router.post("/token", response_model=Token)
# async def login(form_data: OAuth2PasswordRequestForm = Depends(), db = Depends(get_db)):
# Annotated 추가
async def login(
    form_data: Annotated[OAuth2PasswordRequestForm, Depends()], 
    db: Annotated[Session, Depends(get_db)]
):
    
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

### `api/endpoints/users.py`(수정)

```python
from typing import Annotated
from fastapi import APIRouter, Depends, HTTPException, status
from api.endpoints.auth import get_current_active_user
from db.utils import get_db
from sqlalchemy.orm import Session
from schemas.schema_user import User, UserCreate
from services.service_user import get_user_by_name, create_user

router = APIRouter(prefix="/users")


# status_code 추가, Annotated 추가
@router.post("/add_user/", status_code=status.HTTP_201_CREATED, response_model=User)
def add_user(user: UserCreate, db: Annotated[Session, Depends(get_db)]):
    db_user = get_user_by_name(db, username=user.username)
    if db_user:
        raise HTTPException(status_code=400, detail="User already registered")
    return create_user(db=db, user=user)


# endpoint 추가 - 현재 사용자 확인 
@router.post("/me/", status_code=status.HTTP_200_OK, response_model=User)
def get_me(current_user: Annotated[User, Depends(get_current_active_user)]):
    return current_user
```

### 테스트

- <http://localhost:8000/docs> 에 접속합니다.
- `users/me` endpoint를 확인합니다.
  - 로그인 되어 있지 않으므로 '401' status code와 함께 "detail": "Not authenticated" 출력됩니다.
- 우측 상단의 'Authorize' 버튼을 클릭하여 로그인(토큰 발급)을 합니다.
- `users/me` endpoint를 확인합니다.
  - '200' status code와 함께 현재 사용자의 정보가 출력됩니다.

---

## 비밀번호 해싱

비밀번호를 해시하여 저장하고 비교하는 함수를 추가합니다.

### `services/service_user.py`(수정)

- hash_password 함수: 비밀번호를 해시하여 저장하는 함수
- verify_password 함수: 일반 텍스트값과 해싱된 값를 비교하는 함수
- 회원 가입 할 때는 hash_password 함수를 사용하여 비밀번호를 해싱하여 저장하고 로그인을 할 때는 verify_password 함수를 사용하여 비밀번호를 비교합니다.

```python
from sqlalchemy.orm import Session
from models.models import User
from passlib.context import CryptContext


# 비밀번호 해싱
def hash_password(password: str):
    bcrypt_context = CryptContext(schemes=['bcrypt'], deprecated="auto")
    return bcrypt_context.hash(password)


# 비밀번호 비교
def verify_password(plain_password: str, hashed_password: str):
    bcrypt_context = CryptContext(schemes=['bcrypt'], deprecated="auto")
    return bcrypt_context.verify(plain_password, hashed_password)


def get_user(db: Session, user_id: int):
    return db.query(User).filter(User.id == user_id).first()


def get_user_by_name(db: Session, username: str):
    return db.query(User).filter(User.username == username).first()


def create_user(db: Session, user: User):
    # 비밀번호를 해싱하여 저장
    db_user = User(username=user.username, password=hash_password(user.password))
    db.add(db_user)
    db.commit() 
    db.refresh(db_user)
    return db_user
```

### 테스트 - 비밀번호 해싱

- <http://localhost:8000/docs> 에 접속합니다.
- `users/add_user` endpoint를 확인합니다. - 사용자 등록
- DB에 저장된 비밀번호가 해싱되어 저장되었습니다.
- 'Authorize' 버튼을 클릭하여 로그인을 합니다.
- 정상적으로 로그인 되었습니다.

## 이전 코드 오류 수정 - 사용자 등록의 fullname 오류

사용자 등록 시 fullname이 DB에 저장되지 않는 오류가 있었네요. 지금 수정 하겠습니다.

### `services/service_user.py`(수정) - fullname 추가

```python
from sqlalchemy.orm import Session
from models.models import User
from passlib.context import CryptContext


def hash_password(password: str):
    bcrypt_context = CryptContext(schemes=['bcrypt'], deprecated="auto")
    return bcrypt_context.hash(password)


def verify_password(plain_password: str, hashed_password: str):
    bcrypt_context = CryptContext(schemes=['bcrypt'], deprecated="auto")
    return bcrypt_context.verify(plain_password, hashed_password)


def get_user(db: Session, user_id: int):
    return db.query(User).filter(User.id == user_id).first()


def get_user_by_name(db: Session, username: str):
    return db.query(User).filter(User.username == username).first()


# fullname 추가
def create_user(db: Session, user: User):
    user.password = hash_password(user.password)
    db_user = User(**dict(user))
    db.add(db_user)
    db.commit() 
    db.refresh(db_user)
    return db_user
```

---

해시태그: #TODO #FastAPI #Python #OAuth2PasswordRequestForm #OAuth2PasswordBearer #JWT #Token #Authentication #current_user

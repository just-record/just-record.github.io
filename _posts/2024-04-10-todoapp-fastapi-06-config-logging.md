---
layout: single
title: "FastAPI를 사용하여 TODO 애플리케이션 만들기 - 06 Configuration, Logging" 
categories: TODO-App
tag: [Python, FastAPI, TODO]
toc: true
toc_sticky: true
toc_label: "목차"
---

FastAPI를 사용하여 서비스를 개발하여 FastAPI와 Python의 기능을 익히고자 합니다. 애플리케이션은 TODO 애플리케이션을 개발하고자 합니다. 이번 내용은 서비스 개발에 중점을 두었습니다. Python과  FastAPI 기능에 대한 내용은 생략하겠습니다. 추가로 Dabtbase는 PostgreSQL을 사용합니다.

github: [https://github.com/just-record/todo_fastapi](https://github.com/just-record/todo_fastapi)

- 각 단계별로 branch를 생성하여 작업합니다.

여섯번 째로 Configuration과 Logging을 적용하였습니다.

## 8. Configuration

### yaml 파일을 사용하여 Configuration 설정

`core/config.yaml`(새로생성): Configuration 파일을 생성합니다.

```yaml
database:
  db_host: localhost
  db_port: 5432
  db_name: db01
  db_user: user01
  db_password: user01

jwt:
  secret_key: 1184cf8c81a173ab236d500cd2a98cf37cf2ad32d33052a523107d892dfad6a3
  algorithm: HS256
  ACCESS_TOKEN_EXPIRE_MINUTES: 30   
```

### config 객체 구현

config 객체는 팩토리 패턴을 사용하여 싱글톤으로 구현하였습니다.

`core/config.py`(새로생성): Configuration 파일을 읽어오는 코드를 작성합니다.

```python
import yaml

class Config:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(Config, cls).__new__(cls)
            cls._load_config()
        return cls._instance

    @classmethod
    def _load_config(cls):
        with open("core/config.yaml", "r") as file:
            cls.config = yaml.safe_load(file)

    @classmethod
    def get_config(cls):
        return cls.config
```

### Config 적용

`db/database.py`: Configuration을 적용합니다.

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import declarative_base, sessionmaker
from core.config import Config

config = Config().get_config()

db_host = config['database']['db_host']
db_port = config['database']['db_port']
db_name = config['database']['db_name']
db_user = config['database']['db_user']
db_password = config['database']['db_password']


SQLALCHEMY_DATABASE_URL = f"postgresql://{db_user}:{db_password}@{db_host}/{db_name}"


engine = create_engine(
    SQLALCHEMY_DATABASE_URL, future=True
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine, future=True)
Base = declarative_base()
```

`api/endpoints/auth.py`: Configuration을 적용합니다.

```python
### 생략

from sqlalchemy.orm import Session
from core.config import Config  # 추가

config = Config().get_config() # 추가

secret_key = config['jwt']['secret_key'] # 수정
algorithm = config['jwt']['algorithm'] # 수정
ACCESS_TOKEN_EXPIRE_MINUTES = config['jwt']['ACCESS_TOKEN_EXPIRE_MINUTES'] # 수정

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

### 생략
```

## 9. Logging

### Logging 설정

yaml 파일을 사용하여 Logging 설정하였습니다.

먼저 `logs` 디렉토리를 생성합니다.

```bash
mkdir logs
```

`core/config.yaml`(수정): Logging 설정을 추가합니다.

```yaml
database:
  db_host: localhost
  db_port: 5432
  db_name: db01
  db_user: user01
  db_password: user01

jwt:
  secret_key: 1184cf8c81a173ab236d500cd2a98cf37cf2ad32d33052a523107d892dfad6a3
  algorithm: HS256
  ACCESS_TOKEN_EXPIRE_MINUTES: 30  

logging:
  format: '%(asctime)s - %(name)s - %(funcName)s - %(levelname)s - %(message)s'
  timed_rotating_when: W6 # S, D, H, M, midnight, W0-W6
  timed_rotating_interval: 2
  timed_rotating_backup_count: 100
  stream_handler_yn: True # True, False
  file_handler_yn: True   # True, False
  log_file: logs/app.log
  log_level: logging.DEBUG # logging.DEBUG, logging.INFO, logging.WARNING, logging.ERROR, logging.CRITICAL
```  

### Logging 초기화

`core/init_config.py`(새로생성): 프로그램의 초기값을 설정합니다. (Logging 설정 포함)

```python
import logging
import logging.handlers

def setup_logging(
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
        file_handler_yn=True,
        stream_handler_yn=True,
        timed_rotating_when='W6',
        timed_rotating_interval=1,
        timed_rotating_backup_count=10,
        log_file='app.log', 
        log_level=logging.INFO):

    # 루트 로거 설정
    logger = logging.getLogger()
    logger.setLevel(log_level)

    formatter = logging.Formatter(format)

    if file_handler_yn:
        file_handler = logging.handlers.TimedRotatingFileHandler(
            log_file, 
            when=timed_rotating_when, 
            interval=timed_rotating_interval,
            backupCount=timed_rotating_backup_count
            )
        file_handler.setLevel(log_level)
        file_handler.setFormatter(formatter)

        logger.addHandler(file_handler)

    if stream_handler_yn:
        stream_handler = logging.StreamHandler()
        stream_handler.setLevel(log_level)
        stream_handler.setFormatter(formatter)

        logger.addHandler(stream_handler)


def init():

    from core.config import Config
    config = Config().get_config()

    log_params = {
        'format': config['logging']['format'], 
        'file_handler_yn': config['logging']['file_handler_yn'], 
        'stream_handler_yn': config['logging']['stream_handler_yn'], 
        'timed_rotating_when': config['logging']['timed_rotating_when'], 
        'timed_rotating_interval': config['logging']['timed_rotating_interval'], 
        'timed_rotating_backup_count': config['logging']['timed_rotating_backup_count'], 
        'log_file': config['logging']['log_file'], 
        'log_level': eval(config['logging']['log_level']), 
    }

    setup_logging(**log_params)
```

`main.py`: 초기화 함수를 호출합니다.

```python
from fastapi import FastAPI
from db.database import engine
from models.models import Base

from api.endpoints import users
from api.endpoints import auth
from api.endpoints import todos

from core.init_config import init

init()

Base.metadata.create_all(bind=engine)

app = FastAPI()

app.include_router(users.router)
app.include_router(auth.router)
app.include_router(todos.router)

# 삭제
# @app.get("/")
# def read_root():
#     return {"message": "Hello, FastAPI!"}
```

### Logging 적용

모든 print문을 logging으로 변경합니다. 특히 Error의 print문은 logging.error로 변경합니다. 개발 시 필요한 부분은 logging.debug로 변경합니다. 운영의 config.yaml 파일의 log_level을 변경하여 debug를 출력하지 않도록 합니다.

`api/endpoints/todos.py`: Logging을 적용합니다.

```python
from typing import Annotated
from fastapi import APIRouter, Depends, Response, status
from fastapi.responses import JSONResponse
from api.endpoints.auth import get_current_active_user
from db.utils import get_db
from sqlalchemy.orm import Session
from models.models import User
from schemas.schema_todo import Todo, TodoCreate
from services.service_todo import create_todo, get_todos_by_user_with_filter, update_todo, update_todo_status, delete_todo
# logging import
import logging


# logger 설정
logger = logging.getLogger(__name__)
router = APIRouter(prefix="/todos")


@router.post("/add_todo", status_code=status.HTTP_201_CREATED, response_model=Todo)
def add_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
             todo: TodoCreate, 
             db: Session = Depends(get_db)) -> Todo:
    try:
        todo.user_id = current_user.id
        return create_todo(db=db, todo=todo)
    except Exception as e:
        # logging
        logger.error(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )        


@router.get("/get_todos", status_code=status.HTTP_200_OK, response_model=list[Todo])
def get_todos(current_user: Annotated[User, Depends(get_current_active_user)], 
              completed: bool = False,
              db: Session = Depends(get_db)) -> list[Todo]:
    try:
        results = get_todos_by_user_with_filter(db=db, user_id=current_user.id, completed=completed)
        # 만약 조회 결과가 없다면 204 No Content를 반환
        if not results:
            return Response(status_code=status.HTTP_204_NO_CONTENT)
        return results
    except Exception as e:
        logger.error(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )


@router.put("/edit_todo/{todo_id}", status_code=status.HTTP_200_OK, response_model=Todo)
def edit_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
              todo: TodoCreate,
              todo_id: int,
              db: Session = Depends(get_db)) -> Todo:
    try:
        todo.user_id = current_user.id
        return update_todo(db=db, todo_id=todo_id, todo=todo)
    except Exception as e:
        logger.error(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
    


@router.patch("/edit_todo_status/{todo_id}", status_code=status.HTTP_200_OK, response_model=Todo)
def edit_todo_status(current_user: Annotated[User, Depends(get_current_active_user)], 
                       completed: bool,
                       todo_id: int,
                       db: Session = Depends(get_db)) -> Todo:
    try:
        return update_todo_status(db=db, todo_id=todo_id, completed=completed, user_id=current_user.id)
    except Exception as e:
        logger.error(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )


@router.delete("/remove_todo/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
def remove_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
                todo_id: int,
                db: Session = Depends(get_db)) -> None:
    try:
        delete_todo(db=db, todo_id=todo_id, user_id=current_user.id)
        return None
    except Exception as e:
        logger.error(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
```

`api/endpoints/users.py`: Logging을 동일 하게 적용합니다.

```python
### 생략
import logging


logger = logging.getLogger(__name__)
router = APIRouter(prefix="/users")


@router.post("/add_user/", status_code=status.HTTP_201_CREATED, response_model=User)
def add_user(user: UserCreate, db: Annotated[Session, Depends(get_db)]):
    try:
        db_user = get_user_by_name(db, username=user.username)
        if db_user:
            return Response(status_code=status.HTTP_400_BAD_REQUEST, content="User already registered")
        return create_user(db=db, user=user)
    except Exception as e:
        logger.error(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
### 생략
```

나머지 모든 파일도 적용합니다.

### 테스트

`services/service_todo.py`: debug 로그를 추가합니다.

```python
### 생략
import logging


logger = logging.getLogger(__name__)


def create_todo(db: Session, todo: TodoCreate) -> Todo:
    try:
        logger.debug(f'Creating Todo: {todo}')
        db_todo = Todo(**dict(todo))
        db.add(db_todo)
        db.commit()
        db.refresh(db_todo)
        return db_todo
    except Exception as e:
        db.rollback()
        logger.error(f'Error: {e}')
        raise e
### 생략
```

- `http://localhost:8000/docs`에 접속
- 'Authorize'를 통해 로그인
- `POST /todos/add_todo` endpoint를 실행
- logs 디렉토리에 app.log 파일이 생성되고 로그가 기록되는 것을 확인할 수 있습니다.

---

해시태그: #TODO #FastAPI #Python #TODO #Configuration #Logging

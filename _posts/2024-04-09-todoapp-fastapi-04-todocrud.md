---
layout: single
title: "FastAPI를 사용하여 TODO 애플리케이션 만들기 - 04 TODO CRUD" 
categories: TODO-App
tag: [Python, FastAPI, TODO]
toc: true
toc_sticky: true
toc_label: "목차"
---

FastAPI를 사용하여 서비스를 개발하여 FastAPI와 Python의 기능을 익히고자 합니다. 애플리케이션은 TODO 애플리케이션을 개발하고자 합니다. 이번 내용은 서비스 개발에 중점을 두었습니다. Python과  FastAPI 기능에 대한 내용은 생략하겠습니다. 추가로 Dabtbase는 PostgreSQL을 사용합니다.

github: [https://github.com/just-record/todo_fastapi](https://github.com/just-record/todo_fastapi)

- 각 단계별로 branch를 생성하여 작업합니다.

네 번째로 TODO CRUD(Create, Read, Update, Delete) 기능입니다.

## 6. TODO CRUD

### todo schema 추가

`schemas/schema_todo.py`(새로생성): TODO 스키마를 추가합니다.

```python
from pydantic import BaseModel, ConfigDict


class TodoBase(BaseModel):
    title: str
    description: str | None = None
    completed: bool = False


class TodoCreate(TodoBase):
    user_id: int | None = None


class Todo(TodoBase):
    id: int
    user_id: int

    class Config:
        model_config = ConfigDict(from_attributes=True)
```

### TODO 등록

#### todo service 추가

`services/service_todo.py`(새로생성): TODO 데이터를 처리하는 CRUD 함수를 추가합니다.

```python
from sqlalchemy.orm import Session
from models.models import Todo
from schemas.schema_todo import TodoCreate


def create_todo(db: Session, todo: TodoCreate) -> Todo:
    db_todo = Todo(**dict(todo))
    db.add(db_todo)
    db.commit()
    db.refresh(db_todo)
    return db_todo
```

#### todo router 추가

`api/endpoints/todos.py`(새로생성): TODO를 추가하는 endpoint('todos')를 추가합니다.

```python
from typing import Annotated
from fastapi import APIRouter, Depends, status
from api.endpoints.auth import get_current_active_user
from db.utils import get_db
from sqlalchemy.orm import Session
from models.models import User
from schemas.schema_todo import Todo, TodoCreate
from services.service_todo import create_todo


router = APIRouter(prefix="/todos")


@router.post("/add_todo", status_code=status.HTTP_201_CREATED, response_model=Todo)
def add_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
             todo: TodoCreate, 
             db: Session = Depends(get_db)) -> Todo:
    todo.user_id = current_user.id
    return create_todo(db=db, todo=todo)
```

#### main.py 수정

`main.py`(수정): `api/endpoints/todos.py`를 import합니다.

```python
from fastapi import FastAPI
from db.database import engine
from models.models import Base

from api.endpoints import users
from api.endpoints import auth
from api.endpoints import todos

Base.metadata.create_all(bind=engine)

app = FastAPI()

app.include_router(users.router)
app.include_router(auth.router)
app.include_router(todos.router)

@app.get("/")

def read_root():
    return {"message": "Hello, FastAPI!"}
```

#### 테스트 - TODO 추가

- `http://localhost:8000/docs`으로 접속합니다.
- 'Authorize' 버튼을 클릭하여 로그인합니다.(비밀번호 해싱 적용 후 가입 한 사용자로 로그인)
- 'POST /todos/add_todo' endpoint를 테스트 합니다.
  
  ```json
  {
    "title": "TODO 1",
    "description": "TODO 1 description",
    "completed": false
  }
  ```

- status code 201과 response body를 확인합니다.
- Database에 데이터가 추가되었는지 확인합니다.
- 사용자는 로그인한 사용자로 추가되었는지 확인합니다.

### TODO 목록 가져오기

#### todo service 수정

`services/service_todo.py`(수정): TODO 목록을 가져오는 함수를 추가합니다.

```python
from sqlalchemy.orm import Session
from models.models import Todo
from schemas.schema_todo import TodoCreate


def create_todo(db: Session, todo: TodoCreate) -> Todo:
    db_todo = Todo(**dict(todo))
    db.add(db_todo)
    db.commit()
    db.refresh(db_todo)
    return db_todo


# TODO 목록 가져오기
def get_todos(db: Session, skip: int = 0, limit: int = 100) -> list[Todo]:
    return db.query(Todo).offset(skip).limit(limit).all()


# TODO ID로 하나의 TODO 가져오기
def get_todo_by_id(db: Session, todo_id: int) -> Todo:
    return db.query(Todo).filter(Todo.id == todo_id).first()


# 사용자 ID로 TODO 목록 가져오기
def get_todos_by_user(db: Session, user_id: int, skip: int = 0, limit: int = 100) -> list[Todo]:
    return db.query(Todo).filter(Todo.user_id == user_id).offset(skip).limit(limit).all()


# 완료 여부로 TODO 목록 가져오기
def get_todos_with_filter(db: Session, skip: int = 0, limit: int = 100, completed: bool = False) -> list[Todo]:
    return db.query(Todo).filter(Todo.completed == completed).offset(skip).limit(limit).all()


# 사용자 ID와 완료 여부로 TODO 목록 가져오기
def get_todos_by_user_with_filter(db: Session, user_id: int, skip: int = 0, limit: int = 100, completed: bool = False) -> list[Todo]:
    return db.query(Todo).filter(Todo.user_id == user_id, Todo.completed == completed).offset(skip).limit(limit).all()
```

#### todo router 수정

`api/endpoints/todos.py`(수정): TODO 목록을 가져오는 endpoint('todos')를 추가합니다.

```python
from typing import Annotated
from fastapi import APIRouter, Depends, status
from api.endpoints.auth import get_current_active_user
from db.utils import get_db
from sqlalchemy.orm import Session
from models.models import User
from schemas.schema_todo import Todo, TodoCreate
from services.service_todo import create_todo, get_todos_by_user_with_filter


router = APIRouter(prefix="/todos")


@router.post("/add_todo", status_code=status.HTTP_201_CREATED, response_model=Todo)
def add_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
             todo: TodoCreate, 
             db: Session = Depends(get_db)) -> Todo:
    todo.user_id = current_user.id
    return create_todo(db=db, todo=todo)


@router.get("/get_todos", status_code=status.HTTP_200_OK, response_model=list[Todo])
def get_todos(current_user: Annotated[User, Depends(get_current_active_user)], 
              completed: bool = False,
              db: Session = Depends(get_db)) -> list[Todo]:
    return get_todos_by_user_with_filter(db=db, user_id=current_user.id, completed=completed)
```

#### 테스트 - TODO 목록 가져오기

- `http://localhost:8000/docs`으로 접속
- 'Authorize' 버튼을 클릭하여 로그인합니다.
- 'GET /todos/get_todos' endpoint를 테스트합니다.
- status code 200과 response body를 확인합니다.
- TODO 목록이 정상적으로 가져와지는지 확인합니다.

### TODO 수정, 삭제

#### todo service 수정 - TODO 수정, 삭제

`services/service_todo.py`(수정): TODO 수정, 삭제하는 함수를 추가합니다.

```python
from sqlalchemy.orm import Session
from models.models import Todo
from schemas.schema_todo import TodoCreate


def create_todo(db: Session, todo: TodoCreate) -> Todo:
    db_todo = Todo(**dict(todo))
    db.add(db_todo)
    db.commit()
    db.refresh(db_todo)
    return db_todo


def get_todos(db: Session, skip: int = 0, limit: int = 100) -> list[Todo]:
    return db.query(Todo).offset(skip).limit(limit).all()


def get_todo_by_id(db: Session, todo_id: int) -> Todo:
    return db.query(Todo).filter(Todo.id == todo_id).first()


def get_todos_by_user(db: Session, user_id: int, skip: int = 0, limit: int = 100) -> list[Todo]:
    return db.query(Todo).filter(Todo.user_id == user_id).offset(skip).limit(limit).all()


def get_todos_with_filter(db: Session, skip: int = 0, limit: int = 100, completed: bool = False) -> list[Todo]:
    return db.query(Todo).filter(Todo.completed == completed).offset(skip).limit(limit).all()


def get_todos_by_user_with_filter(db: Session, user_id: int, skip: int = 0, limit: int = 100, completed: bool = False) -> list[Todo]:
    return db.query(Todo).filter(Todo.user_id == user_id, Todo.completed == completed).offset(skip).limit(limit).all()


# TODO 수정
def update_todo(db: Session, todo_id: int, todo: TodoCreate) -> Todo:
    db_todo = db.query(Todo).filter(Todo.id == todo_id, Todo.user_id == todo.user_id).first()    
    for key, value in dict(todo).items():
        setattr(db_todo, key, value)
    db.commit()
    db.refresh(db_todo)
    return db_todo


# TODO 상태 업데이트
def update_todo_status(db: Session, todo_id: int, completed: bool, user_id: int) -> Todo:
    db_todo = db.query(Todo).filter(Todo.id == todo_id, Todo.user_id == user_id).first()
    db_todo.completed = completed
    db.commit()
    db.refresh(db_todo)
    return db_todo


# TODO 삭제
def delete_todo(db: Session, todo_id: int, user_id: int) -> None:
    db_todo = db.query(Todo).filter(Todo.id == todo_id, Todo.user_id == user_id).first()
    db.delete(db_todo)
    db.commit()
    return None
```

#### todo router 수정 - TODO 수정, 삭제

`api/endpoints/todos.py`(수정): TODO 수정, 삭제하는 endpoint('todos')를 추가합니다.

```python
from typing import Annotated
from fastapi import APIRouter, Depends, status
from api.endpoints.auth import get_current_active_user
from db.utils import get_db
from sqlalchemy.orm import Session
from models.models import User
from schemas.schema_todo import Todo, TodoCreate
from services.service_todo import create_todo, get_todos_by_user_with_filter, update_todo, update_todo_status, delete_todo


router = APIRouter(prefix="/todos")


@router.post("/add_todo", status_code=status.HTTP_201_CREATED, response_model=Todo)
def add_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
             todo: TodoCreate, 
             db: Session = Depends(get_db)) -> Todo:
    todo.user_id = current_user.id
    return create_todo(db=db, todo=todo)


@router.get("/get_todos", status_code=status.HTTP_200_OK, response_model=list[Todo])
def get_todos(current_user: Annotated[User, Depends(get_current_active_user)], 
              completed: bool = False,
              db: Session = Depends(get_db)) -> list[Todo]:
    return get_todos_by_user_with_filter(db=db, user_id=current_user.id, completed=completed)


@router.put("/edit_todo/{todo_id}", status_code=status.HTTP_200_OK, response_model=Todo)
def edit_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
              todo: TodoCreate,
              todo_id: int,
              db: Session = Depends(get_db)) -> Todo:
    todo.user_id = current_user.id
    return update_todo(db=db, todo_id=todo_id, todo=todo)


@router.patch("/edit_todo_status/{todo_id}", status_code=status.HTTP_200_OK, response_model=Todo)
def edit_todo_status(current_user: Annotated[User, Depends(get_current_active_user)], 
                       completed: bool,
                       todo_id: int,
                       db: Session = Depends(get_db)) -> Todo:
    return update_todo_status(db=db, todo_id=todo_id, completed=completed, user_id=current_user.id)


@router.delete("/remove_todo/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
def remove_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
                todo_id: int,
                db: Session = Depends(get_db)) -> None:
    delete_todo(db=db, todo_id=todo_id, user_id=current_user.id)
    return None
```

#### 테스트 - TODO 수정, 삭제

- `http://localhost:8000/docs`으로 접속
- 'Authorize' 버튼을 클릭하여 로그인합니다.

수정 테스트 - 전체:

- 'PUT /todos/edit_todo/{todo_id}' endpoint를 테스트합니다.
- todo_id는 로그인 한 사용자가 추가한 TODO의 id를 입력합니다.
  
  ```json
  {
    "title": "TODO 1 updated",
    "description": "TODO 1 description updated",
    "completed": true
  }
  ```

- status code 200과 response body를 확인합니다.

수정 테스트 - 상태:

- 'PATCH /todos/edit_todo_status/{todo_id}' endpoint를 테스트합니다.
- todo_id는 로그인 한 사용자가 추가한 TODO의 id를 입력합니다.
  
  ```json
  {
    "completed": true
  }
  ```

- status code 200과 response body를 확인합니다.
- Database에 데이터가 수정되었는지 확인합니다.

삭제 테스트:

- 'DELETE /todos/remove_todo/{todo_id}' endpoint를 테스트합니다.
- todo_id는 로그인 한 사용자가 추가한 TODO의 id를 입력합니다.
- status code 204를 확인합니다.
- Database에 데이터가 삭제되었는지 확인합니다.

---

해시태그: #TODO #FastAPI #Python #TODO #CRUD #create_todo #get_todos #update_todo #delete_todo

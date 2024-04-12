---
layout: single
title: "FastAPI를 사용하여 TODO 애플리케이션 만들기 - 05 Exception 처리하기" 
categories: TODO-App
tag: [Python, FastAPI, TODO]
toc: true
toc_sticky: true
toc_label: "목차"
---

FastAPI를 사용하여 서비스를 개발하여 FastAPI와 Python의 기능을 익히고자 합니다. 애플리케이션은 TODO 애플리케이션을 개발하고자 합니다. 이번 내용은 서비스 개발에 중점을 두었습니다. Python과  FastAPI 기능에 대한 내용은 생략하겠습니다. 추가로 Dabtbase는 PostgreSQL을 사용합니다.

다섯번 째로 Exception 처리를 추가하겠습니다.

## 7. Exception

제가 적용하고 있는 예외 처리 방식이 맞는지 모르겠습니다. 자료를 제대로 찾아 보지 않아서 인지 어떤지 모르지만 참조 할 만한 자료를 찾지 못했습니다. 제가 생각한 방식을 적용해 보았습니다.

### 서비스 함수에 exception 처리 추가

모든 서비스 함수에 try-except를 추가하였습니다. 예외 처리는 해당 예외를 다시 발생시켜 호출하는 곳에서 예외를 알 수 있도록 하였습니다. DB관련 작업일 경우는 db를 rollback하였습니다.

물론 서비스 함수를 호출하는 곳에서 예외 처리를 할 수 있습니다. 하지만 Logging과 서비스 영역에서 예외 처리(예를 들면 DB rollback)를 해야 하는 부분이 있다고 생각되었습니다.

`services/service_todo.py`(수정)

```python
from sqlalchemy.orm import Session
from models.models import Todo
from schemas.schema_todo import TodoCreate


def create_todo(db: Session, todo: TodoCreate) -> Todo:
    try:
        db_todo = Todo(**dict(todo))
        db.add(db_todo)
        db.commit()
        db.refresh(db_todo)
        return db_todo
    except Exception as e:
        db.rollback()
        print(f'Error: {e}')
        raise e


def get_todos(db: Session, skip: int = 0, limit: int = 100) -> list[Todo]:
    try:
        return db.query(Todo).offset(skip).limit(limit).all()
    except Exception as e:
        print(f'Error: {e}')
        raise e


def get_todo_by_id(db: Session, todo_id: int) -> Todo:
    try:
        return db.query(Todo).filter(Todo.id == todo_id).first()
    except Exception as e:
        print(f'Error: {e}')
        raise e


def get_todos_by_user(db: Session, user_id: int, skip: int = 0, limit: int = 100) -> list[Todo]:
    try:
        return db.query(Todo).filter(Todo.user_id == user_id).offset(skip).limit(limit).all()
    except Exception as e:
        print(f'Error: {e}')
        raise e


def get_todos_with_filter(db: Session, skip: int = 0, limit: int = 100, completed: bool = False) -> list[Todo]:
    try:
        return db.query(Todo).filter(Todo.completed == completed).offset(skip).limit(limit).all()
    except Exception as e:
        print(f'Error: {e}')
        raise e


def get_todos_by_user_with_filter(db: Session, user_id: int, skip: int = 0, limit: int = 100, completed: bool = False) -> list[Todo]:
    try:
        return db.query(Todo).filter(Todo.user_id == user_id, Todo.completed == completed).offset(skip).limit(limit).all()
    except Exception as e:
        print(f'Error: {e}')
        raise e
    

def update_todo(db: Session, todo_id: int, todo: TodoCreate) -> Todo:
    try:
        db_todo = db.query(Todo).filter(Todo.id == todo_id, Todo.user_id == todo.user_id).first()    
        if not db_todo:
            raise Exception('요청한 Todo가 존재하지 않습니다. 본인이 작성한 Todo인지 확인해주세요.')
        for key, value in dict(todo).items():
            setattr(db_todo, key, value)
        db.commit()
        db.refresh(db_todo)
        return db_todo
    except Exception as e:
        db.rollback()
        print(f'Error: {e}')
        raise e


def update_todo_status(db: Session, todo_id: int, completed: bool, user_id: int) -> Todo:
    try:
        db_todo = db.query(Todo).filter(Todo.id == todo_id, Todo.user_id == user_id).first()
        if not db_todo:
            raise Exception('요청한 Todo가 존재하지 않습니다. 본인이 작성한 Todo인지 확인해주세요.')
        db_todo.completed = completed
        db.commit()
        db.refresh(db_todo)
        return db_todo
    except Exception as e:
        db.rollback()
        print(f'Error: {e}')
        raise e


def delete_todo(db: Session, todo_id: int, user_id: int) -> None:
    try:
        db_todo = db.query(Todo).filter(Todo.id == todo_id, Todo.user_id == user_id).first()
        if not db_todo:
            raise Exception('요청한 Todo가 존재하지 않습니다. 본인이 작성한 Todo인지 확인해주세요.')
        db.delete(db_todo)
        db.commit()
        return None
    except Exception as e:
        db.rollback()
        print(f'Error: {e}')
        raise e
```

`services/service_user.py`(수정)

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
    try:
        return db.query(User).filter(User.id == user_id).first()
    except Exception as e:
        print(f'Error: {e}')
        raise e


def get_user_by_name(db: Session, username: str):
    try:
        return db.query(User).filter(User.username == username).first()
    except Exception as e:
        print(f'Error: {e}')
        raise e


def create_user(db: Session, user: User):
    try:
        user.password = hash_password(user.password)
        db_user = User(**dict(user))
        db.add(db_user)
        db.commit() 
        db.refresh(db_user)
        return db_user
    except Exception as e:
        db.rollback()
        print(f'Error: {e}')
        raise e
```

### endpoints에 exception 처리 추가

모든 endpoints에 try-except를 추가하였습니다. 예외처리는 JSONResponse의 content에 에러 메세지를 설정하고 status_code를 HTTP_500_INTERNAL_SERVER_ERROR를 설정하였습니다.

내부의 연산이나 서비스 함수에서 예외가 발생하므로 HTTP_500_INTERNAL_SERVER_ERROR로 설정하였습니다.

추가로 특정 항목(지정된 todo_id)을 조회했는데 조회한 결과가 없을 경우 HTTP_204_NO_CONTENT를 반환하도록 하였습니다.

`api/endpoints_todo.py`(수정)

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


router = APIRouter(prefix="/todos")


@router.post("/add_todo", status_code=status.HTTP_201_CREATED, response_model=Todo)
def add_todo(current_user: Annotated[User, Depends(get_current_active_user)], 
             todo: TodoCreate, 
             db: Session = Depends(get_db)) -> Todo:
    try:
        todo.user_id = current_user.id
        return create_todo(db=db, todo=todo)
    except Exception as e:
        print(f'Error: {e}')
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
        print(f'Error: {e}')
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
        print(f'Error: {e}')
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
        print(f'Error: {e}')
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
        print(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
```

`api/endpoints_user.py`(수정)

```python
from typing import Annotated
from fastapi import APIRouter, Depends, HTTPException, Response, status
from fastapi.responses import JSONResponse
from api.endpoints.auth import get_current_active_user
from db.utils import get_db
from sqlalchemy.orm import Session
from schemas.schema_user import User, UserCreate
from services.service_user import get_user_by_name, create_user

router = APIRouter(prefix="/users")


@router.post("/add_user/", status_code=status.HTTP_201_CREATED, response_model=User)
def add_user(user: UserCreate, db: Annotated[Session, Depends(get_db)]):
    try:
        db_user = get_user_by_name(db, username=user.username)
        if db_user:
            # raise HTTPException(status_code=400, detail="User already registered")
            return Response(status_code=status.HTTP_400_BAD_REQUEST, content="User already registered")
        return create_user(db=db, user=user)
    except Exception as e:
        print(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )


@router.post("/me/", status_code=status.HTTP_200_OK, response_model=User)
def get_me(current_user: Annotated[User, Depends(get_current_active_user)]):
    try:
        return current_user
    except Exception as e:
        print(f'Error: {e}')
        return JSONResponse(
            content={"detail": str(e)}, 
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
```

### 테스트

테스트 케이스: 로그인 한 사용자가 작성하지 않은 todo_id를 수정하려고 할 때

- `http://localhost:8000/docs`에 접속하여 로그인을 합니다.
- `PATCH /todos/edit_todo_status/{todo_id}`에 접속합니다.
- `todo_id`에 로그인한 사용자가 작성하지 않은 todo_id를 입력합니다.
- `completed`에 `true`를 입력하고 `Execute`를 클릭합니다.
- status_code가 500이고 `detail`에 `요청한 Todo가 존재하지 않습니다. 본인이 작성한 Todo인지 확인해주세요.`가 출력되는지 확인합니다.
- consloe에 `Error: 요청한 Todo가 존재하지 않습니다. 본인이 작성한 Todo인지 확인해주세요.`가 출력되는지 확인합니다.

테스트 케이스: DB 테이블의 컬럼이 변경되어서 오류가 발생할 때

- DB 테이블의 컬럼명을 변경합니다. (예를 들어 `Todo` 테이블의 `title` 컬럼을 `title2`로 변경)
- `http://localhost:8000/docs`에 접속하여 로그인을 합니다.
- `POST /todos/add_todo`에 접속합니다.
- 각 항목에 값을 입력하고 `Execute`를 클릭합니다.
- Error: (psycopg2.errors.UndefinedColumn) 오류:  "title" 칼럼은 "todos" 릴레이션(relation)에 없음 LINE 1: INSERT INTO todos (title, description, completed, user_id) V...

---

해시태그: #TODO #FastAPI #Python #TODO #Exception

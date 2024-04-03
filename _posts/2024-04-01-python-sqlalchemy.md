---
layout: single
title: "Python - SQLAlchemy"
categories: Python
tag: [Python, SQLAlchemy]
toc: true
toc_sticky: true
toc_label: "목차"
---

## SQLAlchemy 란?

SQLAlchemy는 Python에서 데이터베이스와 상호작용합니다.

- 데이터베이스와 애플리케이션을 연결
- SQL 쿼리를 생성 및 실행
- ORM(Object-Relational Mapping - 데이터베이스 스키마와 Python 객체를 매핑) 기능 제공
- SQL문 없이 Python 객체로 데이터를 조작
- 또한 다양한 데이터베이스 엔진(SQLite, PostgreSQL, MySQL, Oracle 등)을 지원 - 이식성이 높음

### ORM(Object-Relational Mapping) 이란?

ORM은 객체 지향 프로그래밍 언어에서 사용하는 객체와 관계형 데이터베이스의 데이터(테이블의 행과 열)를 매핑해주는 기술입니다. 프로그래밍 언어의 객체와 데이터베이스의 데이터를 1대1로 연결시켜줍니다.

예를 들어, 프로그램에서 '사용자' 객체를 만들면, ORM은 이 객체를 데이터베이스의 '사용자' 테이블과 자동으로 연결시켜줍니다. 개발자는 SQL 쿼리를 직접 작성하지 않고, 객체를 조작하는 것만으로 데이터베이스 데이터를 추가, 수정, 삭제할 수 있습니다.

이렇게 ORM을 사용하면 객체 지향 프로그래밍 방식으로 데이터베이스를 다룰 수 있어 코드 가독성과 유지보수성이 높아지는 장점이 있습니다. 또한 SQL 쿼리 작성 없이 데이터베이스를 조작할 수 있어 생산성도 향상됩니다.

## SQLAlchemy 설치

```bash
pip install sqlalchemy
### PostgreSQL 사용 시
# pip install psycopg2
```

Database로 PostgreSQL을 사용할 경우 `psycopg2` 패키지도 설치해야 합니다.

## 사전 테스트 조건

- PostgreSQL 데이터베이스 서버가 실행 중이어야 합니다.
- user: 'user', password: 'password', database: 'my_database', port: '5432'로 접속 가능해야 합니다.

## 데이터베이스 연결

데이터베이스 엔진(Database Engine)

데이터베이스 엔진(Database Engine)은 SQLAlchemy에서 실제 데이터베이스와 상호작용하기 위한 핵심 컴포넌트로 애플리케이션 코드에서 엔진을 통해 데이터베이스와 상호작용할 수 있습니다. SQLAlchemy는 SQLite, PostgreSQL, MySQL, Oracle 등 다양한 데이터베이스 엔진을 지원하므로 하나의 SQLAlchemy 코드로 여러 데이터베이스를 사용할 수 있습니다.

- 데이터베이스 연결 관리
- SQL 구문 실행
- 연결 풀링(Connection Pooling) 제공
- 트랜잭션 관리

create_engine 함수에 Engine URL 문자열을 인자로 전달하여 엔진 객체를 생성합니다.

```python
from sqlalchemy import create_engine

### SQLite 엔진 생성
# engine = create_engine('sqlite:///my_database.db')

### PostgreSQL 엔진 생성 (연결 풀링 옵션 설정)
engine = create_engine('postgresql://user:password@localhost:5432/my_database',
                       pool_size=10, max_overflow=20)
```

- 'pool_size': 연결 풀의 최대 크기
- 'max_overflow': 연결 풀이 최대 크기를 초과할 때 추가 연결을 생성할 수 있는 최대 값

## 메타데이터와 테이블, 열 정의

메타데이터는 테이블 및 관련 개체의 정의를 저장합니다. 코드에 정의한 모든 테이블 개체를 나타내며 데이터베이스에 SQL 명령을 실행하는 데 사용할 수 있습니다. 메타데이터는 SQLAlchemy 개체에 대한 데이터베이스 스키마를 생성하고 관리합니다.

메타데이터와 테이블(열 포함)을 정의하는 예제 소스 코드

```python
from sqlalchemy import MetaData, Table, Column, Integer, String

metadata = MetaData()

users = Table('users', metadata,
              Column('id', Integer, primary_key=True),
              Column('name', String),
              Column('age', Integer),
              Column('city', String)
              )

print(f'metadata: {metadata}')
print(f'metadata.tables: {metadata.tables}')
print(f'metadata.tables.users: {metadata.tables["users"]}')
print(f'metadata.tables.users.columns: {metadata.tables["users"].columns}')
print(f'users.columns: {users.columns}')              
```

- MetaData: 데이터베이스 스키마를 정의하는 객체
- Table: 테이블 객체
- Column: 열(Column) 객체
- primary_key: 기본 키 설정
- Integer, String: 데이터 타입
- name, age, city: 열 이름
- users: 테이블 이름

### 2개의 테이블 정의

```python
from sqlalchemy import MetaData, Table, Column, Integer, String

metadata = MetaData()

users = Table('users', metadata,
              Column('id', Integer, primary_key=True),
              Column('name', String),
              Column('age', Integer),
              Column('city', String)
              )

items = Table('items', metadata,
                Column('id', Integer, primary_key=True),
                Column('name', String),
                Column('cost', Integer)
                )

print(f'metadata: {metadata}')
print(f'metadata.tables: {metadata.tables}')
print(f'metadata.tables.users: {metadata.tables["users"]}')
print(f'metadata.tables.items: {metadata.tables["items"]}')
print(f'metadata.tables.users.columns: {metadata.tables["users"].columns}')
print(f'metadata.tables.items.columns: {metadata.tables["items"].columns}')
print(f'users.columns: {users.columns}')
print(f'items.columns: {items.columns}')
```

### 테이블 생성

```python
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String

engine = create_engine('postgresql://user:password@localhost:5432/my_database',
                       pool_size=10, max_overflow=20)

metadata = MetaData()                       

users = Table('users', metadata,
              Column('id', Integer, primary_key=True),
              Column('name', String),
              Column('age', Integer),
              Column('city', String)
              )

items = Table('items', metadata,
                Column('id', Integer, primary_key=True),
                Column('name', String),
                Column('cost', Integer)
                )

# 메타데이터에 정의된 모든 테이블을 데이터베이스에 생성
metadata.create_all(engine)
```

몇 가지 테스트

- 소스 코드를 2번 실행: 오류가 발생하지 않고 생성된 테이블에 변동 사항이 없음
- items 테이블의 Column을 변경하고 실행: 오류가 발생 하지 않고 테이블은 수정 되지 않음. metadata의 정보는 코드에서 정의한 값을 가지고 있음
- items 테이블을 삭제하고 실행: 오류가 발생하지 않고 테이블이 생성됨

## SQLAlchemy ORM

SQLAlchemy의 ORM은 Python의 객체를 관계형 데이터베이스 세계에 연결하는 기술로, 원시 SQL 쿼리를 작성하는 대신 Python 객체를 사용하여 데이터베이스를 조작할 수 있습니다.

- Session: 데이터베이스와의 연결을 관리하고 트랜잭션을 제어하는 객체. 'session.commit()'을 호출할 때까지 데이터베이스에 변경 사항을 반영하지 않음.
- Declarative Base: 모델 정의를 위한 기본 클래스 역할. 매핑된 모든 클래스는 이 클래스를 상속받아야 함.
- ORM Classes (Models): 데이터베이스 테이블에 매핑되는 Python 클래스. 테이블의 구조와 테이블 간의 관계를 정의

모델과 세션 정의: `model.py`

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

engine = create_engine('postgresql://user:password@localhost:5432/my_database',
                       pool_size=10, max_overflow=20)

# Base 클래스를 상속받아 모델 클래스를 정의
Base = declarative_base()

# User 모델 정의
class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)
    city = Column(String)

# 테이블 생성
Base.metadata.create_all(engine)

# 엔진이 제공하는 연결을 사용하여 세션 클래스 생성
Session = sessionmaker(bind=engine)
```

### ORM CRUD

Create

```python
from model import Session, User

session = Session()

new_user_1 = User(name='Alice', age=25, city='Seoul')
new_user_2 = User(name='Bob', age=30, city='New York')

session.add(new_user_1)
session.add(new_user_2)
session.commit()
```

Read

```python
from model import Session, User

session = Session()

# 모든 사용자 조회
users = session.query(User).all()
print(users)
for user in users:
    print(user.name, user.age, user.city)

# 이름이 'Alice'인 사용자 조회
user = session.query(User).filter_by(name='Alice').first()
print(user)
print(user.name, user.age, user.city)
```

Update

```python
from model import Session, User

session = Session()

# 이름이 'Alice'인 사용자의 나이를 30으로 변경
user = session.query(User).filter_by(name='Alice').first()
user.age = 30
session.commit()

# 변경 사항 확인
user = session.query(User).filter_by(name='Alice').first()
print(user.name, user.age, user.city)
```

Delete

```python
from model import Session, User

session = Session()

# 이름이 'Alice'인 사용자 삭제
user = session.query(User).filter_by(name='Alice').first()
session.delete(user)
session.commit()

user = session.query(User).filter_by(name='Alice').first()
if user:
    print(user.name, user.age, user.city)
else:
    print('사용자가 없습니다.')    
```

## Metadata와 ORM 객체 비교

| Metadata | ORM |
|:---|:---|
| 테이블 개체 및 관련 스키마 구성의 컬렉션으로 데이터베이스 스키마를 정의하고 검사합니다. 테이블, 열, 데이터 유형, 제약 조건 및 인덱스를 포함하여 데이터베이스 구조에 대한 SQL 수준 세부 정보를 나타냅니다. | 데이터베이스 테이블에 매핑되는 Python 클래스를 참조합니다. 객체 지향 방식으로 데이터베이스와 상호 작용하여 SQL 쿼리 대신 Python 개체로 작업합니다. |
| 스키마 정의, 리플렉션(기존 데이터베이스에서 스키마 로드) 및 스키마 생성(스키마 정의를 기반으로 데이터베이스에 테이블 생성) | SQLAlchemy ORM - 테이블: 클래스, 행: 해당 클래스의 인스턴스 |
| 데이터베이스 스키마 정의를 세밀하게 제어해야 할 때, 동적 스키마로 작업할 때, 데이터베이스 작업이 주로 SQL 중심일 때 | 객체 지향 방식을 선호할 때, 데이터베이스 작업을 추상화하려고 할 때 |

Metadata와 ORM 객체를 함께 사용 하는 경우:

대부분은 ORM 개체를 사용하고 스키마나 SQL에 대한 보다 직접적인 제어 할 경우에는 메타데이터를 사용할 수 있습니다. 실질적으로 ORM의 Declarative Base(선언적 기본 클래스)는 내부적으로 메타데이터를 사용합니다.

## 쿼리하기

### 쿼리 객체(Query Objects)

Session의 `query()` 메서드를 사용합니다.

```python
from model import Session, User

session = Session()

query = session.query(User)
for user in query.all():
    print(user.name)
```

조회 테스트를 위해 데이터베이스에 사용자 데이터를 추가합니다.

```python
from model import Session, User

session = Session()

new_user_1 = User(name='Alice', age=25, city='Seoul')
new_user_2 = User(name='Bob', age=30, city='New York')
new_user_3 = User(name='John Doe', age=35, city='London')
new_user_4 = User(name='Jane Doe', age=40, city='Paris')
new_user_5 = User(name='Charlie', age=45, city='Berlin')
new_user_6 = User(name='David', age=50, city='Tokyo')

session.add(new_user_1)
session.add(new_user_2)
session.add(new_user_3)
session.add(new_user_4)
session.add(new_user_5)
session.add(new_user_6)
session.commit()
```

### 필터링(Filtering), 정렬(Sorting), 제한(Limiting)

```python
from model import Session, User

session = Session()

query = session.query(User)

# 이름이 'John Doe'인 사용자 조회
users_named_john = session.query(User).filter_by(name='John Doe').all()
for user in users_named_john:
    print(user.name, user.age, user.city)

print()

# 이름으로 정렬
sorted_users = session.query(User).order_by(User.name).all()
for user in sorted_users:
    print(user.name, user.age, user.city)

print()    

# 조회 결과 제한
limited_users = session.query(User).limit(5).all()
for user in limited_users:
    print(user.name, user.age, user.city)
```

### 쿼리 연산자(Query Operators)

```python
from model import Session, User
from sqlalchemy import and_, or_

session = Session()

users_or = session.query(User).filter(or_(User.name == 'John Doe', User.city == 'Seoul')).all()
for user in users_or:
    print(user.name, user.age, user.city)

print()    

users_and = session.query(User).filter(or_(User.name == 'John Doe', User.age == 35, User.city == 'London')).all()
for user in users_and:
    print(user.name, user.age, user.city)

print()

users_not = session.query(User).filter(User.name != 'John Doe').all()
for user in users_not:
    print(user.name, user.age, user.city)
```

### JOIN

JOIN을 테스트 하기 위해 'Address' 모델을 추가합니다. `model.py` 파일에 추가합니다.

```python
# Add this in model.py
from sqlalchemy import ForeignKey, create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker, relationship

engine = create_engine('postgresql://user:password@localhost:5432/my_database',
                       pool_size=10, max_overflow=20)

Base = declarative_base()

class User(Base):
    __tablename__ = 'users'
    
    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)
    city = Column(String)
    
    addresses = relationship('Address', back_populates='user', cascade="all, delete, delete-orphan")

class Address(Base):
    __tablename__ = 'addresses'
    
    id = Column(Integer, primary_key=True)
    email_address = Column(String, nullable=False)
    user_id = Column(Integer, ForeignKey('users.id'))
    
    user = relationship('User', back_populates='addresses')

Base.metadata.create_all(engine)

Session = sessionmaker(bind=engine)
```

- `address = relationship...`
  - User가 여러 Address 인스턴스를 가질 수 있음
  - User는 Address 객체를 addresses 속성을 통해 접근
  - `back_populates='user'`는 Address 모델 내의 user 속성과 이 관계를 양방향으로 연결
  - `cascade="all, delete, delete-orphan"` 옵션은 User 객체가 삭제되었을 때 관련된 Address 객체들도 자동으로 삭제

- `user = relationship...`
  - Address는 하나의 User 인스턴스를 가질 수 있음 - `ForeignKey('users.id')`
  - Address는 User 객체를 user 속성을 통해 접근
  - `back_populates='addresses'`는 User 모델 내의 addresses 속성과 이 관계를 양방향으로 연결

JOIN 테스트를 위해 'Address' 데이터를 추가합니다.

```python
from model import Session, User, Address

session = Session()

new_address_1 = Address(email_address='aaa@aaa.com', user_id=1)
new_address_2 = Address(email_address='bbb@aaa.com', user_id=2)
new_address_3 = Address(email_address='ccc@aaa.com', user_id=3)
new_address_4 = Address(email_address='ddd@aaa.com', user_id=4)
# new_address_5 = Address(email_address='eee@aaa.com', user_id=5)
# new_address_6 = Address(email_address='fff@aaa.com', user_id=6)

session.add_all([new_address_1, new_address_2, new_address_3, new_address_4, new_address_5, new_address_6])
session.commit()
```

#### JOIN 쿼리

```python
from model import Session, User, Address

session = Session()

users_with_addresses = session.query(User).join(User.addresses).all()
for user in users_with_addresses:
    for address in user.addresses:
        print(f'name: {user.name}, age: {user.age}, city: {user.city}, email_address: {address.email_address}')
```

#### 다른 방법

```python
from model import Session, User, Address

session = Session()

# Query addresses and access related user objects
addresses = session.query(Address).all()
for address in addresses:
    print(f'email_address: {address.email_address}, name: {address.user.name}, age: {address.user.age}, city: {address.user.city}')
```

#### OUTER JOIN

```python
from model import Session, User, Address

session = Session()

users_with_addresses = session.query(User, Address.email_address).outerjoin(Address, User.id == Address.user_id)
for user, email_address in users_with_addresses:
    print(f'name: {user.name}, age: {user.age}, city: {user.city}, email_address: {email_address}')
```

## 고급 모델 관계

### 1:N 관계

'ForeignKey'를 사용하여 부모-자식 관계를 정의합니다.

```python
from sqlalchemy import create_engine, Column, Integer, String, ForeignKey
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship, sessionmaker

Base = declarative_base()
engine = create_engine('sqlite:///:memory:', echo=True)

class Parent(Base):
    __tablename__ = 'parent'
    id = Column(Integer, primary_key=True)
    children = relationship("Child", back_populates="parent")

class Child(Base):
    __tablename__ = 'child'
    id = Column(Integer, primary_key=True)
    parent_id = Column(Integer, ForeignKey('parent.id'))
    parent = relationship("Parent", back_populates="children")

Base.metadata.create_all(engine)
```

### N:M 관계

두 관련 테이블의 ForeignKey를 사용하려면 연결 테이블이 필요합니다.

```python
from sqlalchemy import Table

# 연결 테이블 생성
association_table = Table('association', Base.metadata,
                          Column('left_id', Integer, ForeignKey('left.id')),
                          Column('right_id', Integer, ForeignKey('right.id'))
                         )

class Left(Base):
    __tablename__ = 'left'
    id = Column(Integer, primary_key=True)
    rights = relationship("Right", secondary=association_table, back_populates="lefts")

class Right(Base):
    __tablename__ = 'right'
    id = Column(Integer, primary_key=True)
    lefts = relationship("Left", secondary=association_table, back_populates="rights")
```

연결 테이블에 추가 컬럼이 필요한 경우:

```python
class Association(Base):
    __tablename__ = 'association'
    left_id = Column(Integer, ForeignKey('left.id'), primary_key=True)
    right_id = Column(Integer, ForeignKey('right.id'), primary_key=True)
    extra_data = Column(String)
    left = relationship("Left", back_populates="associations")
    right = relationship("Right", back_populates="associations")

class Left(Base):
    __tablename__ = 'left'
    id = Column(Integer, primary_key=True)
    associations = relationship("Association", back_populates="left")

class Right(Base):
    __tablename__ = 'right'
    id = Column(Integer, primary_key=True)
    associations = relationship("Association", back_populates="right")
```

### 자기 참조 관계

자신(자신의 다른 행)을 참조 하는 관계입니다.

```python
class Employee(Base):
    __tablename__ = 'employee'
    id = Column(Integer, primary_key=True)
    manager_id = Column(Integer, ForeignKey('employee.id'))
    manager = relationship("Employee", remote_side=[id], backref="subordinates")
```

## Transaction (트랜잭션)

테스트 되지 않았습니다. 추후를 위해 내용만 작성합니다.

```python
try:
    user = session.query(User).filter_by(name='John Doe').one()
    user.email = 'john.doe@example.com'
    # Simulate an error
    raise Exception("Something went wrong!")
    session.commit()
except Exception as e:
    session.rollback()  # Rollback changes
    print("Transaction rolled back due to error:", e)
finally:
    session.close()  # Always close the session
```

### 세션 관리 자동화

컨텍스트 관리자나 'scoped_session'을 사용하여 사용 후 세션이 자동으로 닫히도록 할 수 있습니다.

```python
from sqlalchemy.orm import scoped_session

session_factory = sessionmaker(bind=engine)
ScopedSession = scoped_session(session_factory)

with ScopedSession() as session:
    user = session.query(User).first()
    print(user.name)
```

---

해시태그: #Python #SQLAlchemy #ORM #create_engine #MetaData #Table #Column #Session #DeclarativeBase #relationship #ForeignKey #join #outerjoin

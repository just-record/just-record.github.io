---
layout: single
title: "PostgreSQL - 04 Python에서 PostgreSQL 사용하기"
categories: PostgreSQL
tag: [PostgreSQL, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

PostgreSQL의 설치는 이전 포스트에서 확인 하실 수 있습니다.

## 'psycopg2' 모듈

Python에서 PostgreSQL을 사용하기 위해 'psycopg2' 모듈을 사용합니다.

psycopg2 설치

```bash
pip install psycopg2
```

## PostgreSQL 연결

### 연결 객체 생성

연결 객체는 데이터베이스와 연결하는 객체로 트랜잭션 관리, 연결 종료 등의 작업 수행합니다.

연결 문자열을 사용하여 연결 객체를 생성합니다.

- host: 데이터베이스 서버 주소
- port: 데이터베이스 서버 포트 번호
- database: 데이터베이스 이름
- user: 데이터베이스 사용자 이름
- password: 데이터베이스 사용자 비밀번호

```python
import psycopg2

# 연결 객체 생성
conn = psycopg2.connect(
    host="localhost",
    database="mydb",
    user="myuser",
    password="mypassword"
)
```

### 커서 객체 생성

커서 객체는 실제 데이터베이스 작업을 수행하는 객체로 SQL 쿼리 실행, 결과 반환 등의 작업 수행합니다.

```python
import psycopg2

conn = psycopg2.connect(host="localhost", database="mydb", user="myuser", password="mypassword")

# 커서 객체 생성
cur = conn.cursor()
```

## 기본 SQL 쿼리 실행

앞으로 DB연결과 커서 생성의 코드는 생략합니다.

### SELECT문 실행

```python
# 전체 데이터 조회
cur.execute("SELECT * FROM users")
rows = cur.fetchall()
for row in rows:
    print(row)
```

fetchall(): 모든 결과 행, 리스트로 반환

```python
cur.execute("SELECT * FROM users")
rows = cur.fetchall()
for row in rows:
    print(row)
```

fetchone(): 한 건만, 튜플로 반환

```python
cur.execute("SELECT * FROM users")
row = cur.fetchone()
while row:
    print(row)
    row = cur.fetchone()
```

fetchmany(): size로 지정한 수만큼, 리스트로 반환

```python
cur.execute("SELECT * FROM users")
while True:
    rows = cur.fetchmany(100)
    if not rows:
        break
    for row in rows:
        print(row)
```

### INSERT문 실행

단일 레코드 추가

```python
# 새 사용자 추가
cur.execute("INSERT INTO users (name, age) VALUES (%s, %s)", ("John Doe", 35))
conn.commit()
```

다중 레코드 추가

```python
# 여러 사용자 추가
data = [("Jane Smith", 28), ("Bob Johnson", 42)]
query = "INSERT INTO users (name, age) VALUES (%s, %s)"
cur.executemany(query, data)
conn.commit()
```

### UPDATE문 실행

단일 레코드 수정

```python
# 특정 사용자의 나이 업데이트
cur.execute("UPDATE users SET age = 40 WHERE name = 'John Doe'")
conn.commit()
```

다중 레코드 수정

```python
# 모든 사용자의 나이 1 증가
cur.execute("UPDATE users SET age = age + 1")
conn.commit()
```

### DELETE문 실행

단일 레코드 삭제

```python
# 특정 사용자 삭제
cur.execute("DELETE FROM users WHERE name = 'Jane Smith'")
conn.commit()
```

다중 레코드 삭제

```python
# 나이가 30 미만인 사용자 삭제
cur.execute("DELETE FROM users WHERE age < 30")
conn.commit()
```

## SQL문과 데이터의 분리

### SQL 인젝션(SQL Injection) 이란?

SQL 인젝션은 악의적인 사용자가 애플리케이션의 입력 필드에 SQL 쿼리를 삽입하여 데이터베이스를 조작하는 공격 기법입니다.

```python
# 취약한 코드
name = "John Doe'; DROP TABLE users; --"
query = f"SELECT * FROM users WHERE name = '{name}'"
cur.execute(query)
# SELECT * FROM users WHERE name = 'John Doe'; DROP TABLE users; --'
```

### 파라미터화 된 쿼리

SQL 문과 데이터를 분리하여 데이터는 별도의 파라미터로 전달하는 방법입니다.

작성 예시: `name`이 쿼리 문자열로 대체되는 것이 아니라 파라미터로 전달되어 바인딩되어 SQL 인젝션을 방지합니다.

```python
name = "John Doe'; DROP TABLE users; --"
query = "SELECT * FROM users WHERE name = %s"
cur.execute(query, (name,))
```

insert 문 예시

```python
data = ("Jane Smith", 35)
query = "INSERT INTO users (name, age) VALUES (%s, %s)"
cur.execute(query, data)
conn.commit()
```

update 문 예시

```python
name = "John Doe"
new_age = 40
query = "UPDATE users SET age = %s WHERE name = %s"
cur.execute(query, (new_age, name))
conn.commit()
```

## 트랜잭션

```python
try:
    cur.execute("UPDATE users SET age = age + 1")
    cur.execute("DELETE FROM users WHERE age > 60")
    conn.commit()  # 변경 사항 커밋
except psycopg2.Error as e:
    print(f"Error: {e}")
    conn.rollback()  # 변경 사항 롤백
```

---

해시태그: #postgresql #python #psycopg2 #데이터조회 #데이터입력 #데이터수정 #데이터삭제 #SQL인젝션 #트랜잭션

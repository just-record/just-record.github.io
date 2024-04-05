---
layout: single
title: "Redis - Python으로 사용하기"
categories: Redis
tag: [Redis, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Redis란?

Redis는 오프 소스 기반의 인 메모리 데이터 구조 저장소입니다. 키-값 구조로 데이터를 저장하며, 다양한 데이터 유형을 지원합니다.

특징:

- 인-메모리 데이터베이스: 모든 데이터를 메모리에 저장하여 빠른 처리 속도 제공
- 다양한 데이터 구조 지원: String, List, Hash, Set, Sorted Set 등 다양한 데이터 구조 제공
- 높은 확장성: 수평적 확장(샤딩)을 통해 데이터 용량 및 처리 능력 확장 가능
- 다양한 프로그래밍 언어 지원: Python, Java, C++, Go 등 다양한 언어에서 사용 가능

활용 분야:

- 캐싱: 웹 애플리케이션에서 데이터를 캐싱하여 성능 향상
- 메시징: 큐 시스템, 실시간 알림 등 메시징 시스템 구축
- 세션 관리: 사용자 세션 정보 저장
- 분석: 실시간 데이터 분석

## Redis 설치

### Ubuntu

```bash
sudo apt update
sudo apt install redis-server

# 부팅 시 자동 실행 설정
sudo systemctl enable redis-server
```

설치 후 Redis 서버가 실행 중인지 확인:

```bash
sudo systemctl status redis-server
```

### macOS

```bash
brew install redis
# Redis 서버 실행
brew services start redis
```

### Windows

Redis는 Windows를 공식적으로 지원하지 않습니다. 그러나 WSL(Linux용 Windows 하위 시스템)을 사용하여 Redis를 실행할 수 있습니다. 여기서는 생략 하겠습니다.

## Redis 서버 접속

Redis 서버에 접속하여 명령을 실행하려면 `redis-cli` 명령을 사용합니다.

```bash
redis-cli
```

Redis 서버에 접속 후 다음과 같은 명령을 실행할 수 있습니다.

```bash
### Redis 서버 접속

# ping
ping # PONG

# Redis 서버 정보 확인
info

# 키-값 저장 - SET key value
SET mykey "Hello, Redis!"
SET mynum 100

# 키로 값 조회 - GET key
GET mykey
GET mynum

# 모든 키 조회 - KEYS *
KEYS *

# 키 삭제 - DEL key
DEL mykey
DEL mynum

# 종료 - exit
exit
```

## Redis - Python 라이브러리

Python에서 Redis를 사용하기 위해서는 `redis-py` 라이브러리를 설치해야 합니다.

```bash
pip install redis
```

`redis-py`를 사용하여 Redis 서버에 연결하고 명령을 실행할 수 있습니다.

```python
import redis

# Redis 서버 연결
r = redis.Redis(host='localhost', port=6379, db=0)

# 키-값 저장
r.set('mykey', 'Hello, Redis!')
r.set('mynum', 100)

# 키로 값 조회
print(r.get('mykey'))
print(r.get('mynum'))
print(r.get('mything')) # key가 없는 경우 None 반환

# 모든 키 조회
print(r.keys('*'))

# 키 삭제
print(r.delete('mykey'))
print(r.delete('mynum'))
print(r.delete('mything')) # key가 없는 경우 0 반환

print(r.keys('*'))
```

`db=0`: Redis 데이터베이스 번호. 기본값은 0이며, 0부터 15까지 사용 가능합니다.

## Redis 데이터 구조

Redis는 다양한 데이터 구조를 지원하면 각 유형에 따라 다양한 명령을 제공합니다.

### String (문자열)

```python
import redis

r = redis.Redis(host='localhost', port=6379, db=0)

r.set('mykey', 'Hello, Redis!')

print(r.get('mykey'))
```

### Hashes (해시)

해시는 필드-값 구성된 컬렉션입니다. 해시 내의 각 필드-값 쌍은 개별적으로 읽거나 쓸 수 있어 객체를 표현하는 데 유용합니다.

```python
import redis

r = redis.Redis(host='localhost', port=6379, db=0)

# 해시 설정
r.hset('user01', 'name', 'Alice')
r.hset('user01', 'age', 30)

r.hset('user02', 'name', 'Bob')
r.hset('user02', 'age', 25)

# 해시 값 가져오기
user01_name = r.hget('user01', 'name')
user01_age = r.hget('user01', 'age')

user02_name = r.hget('user02', 'name')
user02_age = r.hget('user02', 'age')

print(user01_name, user01_age)  # 출력: b'Alice' b'30'
print(user02_name, user02_age)  # 출력: b'Bob' b'25'
```

### Lists (리스트)

리스트는 문자열 요소의 순서가 있는 컬렉션입니다. 리스트의 양쪽 끝에 요소를 추가하거나 제거할 수 있습니다.

```python
import redis

r = redis.Redis()

# 리스트에 요소 추가
r.lpush('tasks', 'write code')  # 왼쪽 추가
r.rpush('tasks', 'test code')   # 오른쪽 추가

# 리스트에서 요소를 가져오면서 삭제
print(r.lpop('tasks').decode())  # 왼쪽 pop, 출력: write code
print(r.rpop('tasks').decode())  # 오른쪽 pop, 출력: test code

# 범위 지정하여 요소 가져오기
r.rpush('tasks', 'write code', 'review code', 'test code')
print(r.lrange('tasks', 0, -1))  # 전체 리스트 출력

# 다른 리스트에 요소 추가
r.lpush('other_tasks', 'write code 2') # 다른 리스트에 요소 추가
print(r.lrange('other_tasks', 0, -1)) # 다른 리스트만 출력
print(r.lrange('no_exist_tasks', 0, -1)) # 존재하지 않는 리스트 출력
```

### Sets (집합)

집합은 고유한 문자열 요소의 무순서 컬렉션입니다. 집합에는 중복 요소가 없으며, 집합에 요소를 추가, 제거하거나 집합 간의 교집합, 합집합, 차집합을 계산할 수 있습니다.

```python
import redis

r = redis.Redis()

# 집합에 요소 추가
r.sadd('tags', 'python', 'fastapi', 'redis')

# 집합에 요소 추가
r.sadd('tags2', 'python', 'django', 'redis')

# 집합에 요소 추가
r.sadd('tags3', 'python', 'django', 'fastapi')

# 집합 요소 가져오기
print(r.smembers('tags'))  # 출력: {b'python', b'fastapi', b'redis'}

# 집합 간의 교집합
print(r.sinter('tags', 'tags2'))  # 출력: {b'python', b'redis'}

# 집합 간의 합집합
print(r.sunion('tags', 'tags2'))  # 출력: {b'python', b'fastapi', b'redis', b'django'}

# 집합 간의 차집합
print(r.sdiff('tags', 'tags2'))  # 출력: {b'fastapi'}
```

### Sorted Sets (정렬된 집합)

정렬된 집합은 각 요소에 대한 점수를 가지며, 요소는 점수에 따라 정렬됩니다.

```python
import redis

r = redis.Redis()

# 정렬된 집합에 요소 추가
r.zadd('scores', {'Alice': 100, 'Bob': 90, 'Charlie': 80})

# 점수 추가
r.zincrby('scores', 3, 'Bob')  # Bob's score +3

# 정렬된 집합 요소 가져오기
print(r.zrange('scores', 0, -1, withscores=True))  # 출력: [(b'Charlie', 80.0), (b'Bob', 90.0), (b'Alice', 100.0)]
print(r.zrank('scores', 'Bob'))  # Bob's rank
print(r.zscore('scores', 'Bob'))  # Bob's score
```

### Redis의 고급 데이터 구조

Redis는 다양한 고급 데이터 구조를 지원합니다. Redis의 고급 데이터 구조에는 HyperLogLog, Bitmaps, Geospatial Indexes, Streams 등이 있습니다.

추후 필요 할 때 자세히 알아 볼 예정입니다.

#### HyperLogLog

HyperLogLogs는 대규모 데이터 세트의 카디널리티(고유 요소 수)를 근사적으로 계산합니다. 메모리 사용량이 작고 정확한 카운팅보다는 근사치를 제공합니다.

```python
import redis

r = redis.Redis()

# 요소 추가
r.pfadd('views', *range(1000))

# 추정 카디널리티
print(r.pfcount('views'))  # Close to 1000
```

#### Bitmaps

Bitmaps는 비트 배열로서, 각 비트는 특정 이벤트의 발생 여부를 나타냅니다. 비트 연산을 사용하여 여러 이벤트를 추적하고 집계할 수 있습니다.

```python
import redis

r = redis.Redis()

# Bitmap 생성 및 값 설정
r.setbit('mybits', 1, 1)  # 인덱스 1에 1 설정
r.setbit('mybits', 3, 1)  # 인덱스 3에 1 설정

# Bitmap 값 가져오기
bit_value = r.getbit('mybits', 1)
print(bit_value)  # 출력: 1

# Bitmap 카운팅
count = r.bitcount('mybits')
print(count)  # 출력: 2
```

## Pipeline (파이프라인)

Redis 파이프라인은 여러 명령을 한 번에 보내고 결과를 한 번에 수신하여 네트워크 오버헤드를 줄이는 기능입니다.

```python
import redis

r = redis.Redis()

pipeline = r.pipeline()

# Queue multiple commands
pipeline.set('user:101:balance', 200)
pipeline.incrby('user:101:balance', 100)
pipeline.get('user:101:balance')

# Execute all commands in the pipeline and return their responses
responses = pipeline.execute()
print("Pipelined responses:", responses)
```

- `pipeline.set`: 키-값 설정
- `pipeline.incrby`: 값 증가
- `pipeline.get`: 값 가져오기
- `pipeline.execute`: 파이프라인 실행

결과: `Pipelined responses: [True, 300, b'300']`

- `set` 성공: `True`
- `incrby`: 증가된 값인 `300`을 반환
- `get`: 값 `300`을 반환.

### transaction (트랜잭션)

`r.pipeline(transaction=True)`를 사용하면 Redis 트랜잭션 내에서 명령을 래핑하는 파이프라인 개체가 생성됩니다. 이는 파이프라인에서 execute()가 호출될 때 명령 대기열의 시작 부분에 암시적으로 MULTI를 실행하고 끝에 EXEC를 실행함으로써 달성됩니다. 이는 파이프라인의 모든 명령이 단일 트랜잭션으로 원자적으로 실행되도록 보장합니다. 다른 클라이언트의 다른 명령은 트랜잭션의 명령 사이에서 실행될 수 없습니다.

```python
import redis

r = redis.Redis()

pipeline = r.pipeline(transaction=True)

# Queue multiple commands
pipeline.set('user:101:balance', 200)
pipeline.incrby('user:101:balance', 100)
pipeline.get('user:101:balance')

# Execute all commands in the pipeline and return their responses
responses = pipeline.execute()

print("Pipelined responses:", responses)
```

## Pub/Sub (Publish/Subscribe)

Redis는 Pub/Sub 메커니즘을 지원하여 메시지 브로커로 사용할 수 있습니다. Pub/Sub은 발행-구독 메시징 패턴으로, 발행자(Publisher)가 메시지를 발행하면 구독자(Subscriber)가 메시지를 수신합니다.

먼저 구독자를 실행한 후 발행자를 실행하면 구독자에서 메시지를 수신합니다.

`subscriber.py`: 구독자

```python
import redis

def message_handler(message):
    print(f"Received message: {message['data'].decode()}")

# Create a Redis connection
r = redis.Redis()

# Subscribe to a channel
pubsub = r.pubsub()
pubsub.subscribe(**{'news': message_handler})

# Listen for messages
print('Listening for messages on channel: news')
pubsub.run_in_thread(sleep_time=1)
```

- `message_handler`: 메시지 수신 핸들러
- `pubsub.subscribe`: 채널 구독
- `pubsub.run_in_thread`: 메시지 수신 대기
- `sleep_time`: 메시지 수신 주기
- `news`: 채널 이름

`publisher.py`: 발행자

```python
import redis

# Create a Redis connection
r = redis.Redis()

# Publish messages to a channel
channel = 'news'
r.publish(channel, 'Redis 6.2 Released!')
r.publish(channel, 'New Tutorial on Redis Pub/Sub')
```

- `r.publish`: 메시지 발행
- `channel`: 채널 이름

구독자가 실행 된 상태에서 발행자를 실행하면 구독자에서 메시지를 수신합니다. 발행자를 계속 실행하면 구독자에서 메시지를 계속 수신합니다.

## 간단한 캐시 시스템

Redis는 캐싱에 매우 적합한 데이터베이스입니다. 캐싱은 데이터베이스 또는 다른 소스에서 데이터를 검색하고 결과를 캐시에 저장하여 다음 요청에서 빠르게 반환하는 메커니즘입니다.

```python
import redis
import time

r = redis.Redis()

def get_data(key):
    # 캐시에서 데이터 가져오기
    data = r.get(key)
    if data:
        return data

    # 데이터가 없으면 계산하고 캐시에 저장
    data = compute_expensive_data()
    r.set(key, data, ex=60)  # 60초 동안 캐시
    return data

def compute_expensive_data():
    # 시간이 오래 걸리는 계산 작업 시뮬레이션
    time.sleep(2)
    return b'computed_data'

# 캐시 미스
data = get_data('my_key')
print(data)  # 출력: b'computed_data'

# 캐시 히트
data = get_data('my_key')
print(data)  # 출력: b'computed_data'
```

최초의 get_data 호출에서 캐싱되어 있지 않아 compute_expensive_data 함수를 호출하여 데이터를 계산하고 캐시에 저장합니다. 다음 호출에서는 캐시에서 데이터를 빠르게 가져옵니다.

## 간단한 작업 큐(Job Queue) 구현

Redis는 작업 큐를 구현하는 데 사용할 수 있습니다. 작업 큐는 작업을 생성하고 처리하는 메커니즘으로, 작업을 생성하면 작업자가 작업을 처리합니다.

`worker.py`: 작업자

```python
import redis
import time

r = redis.Redis()

# 작업 추가
def enqueue_job(job_data):
    r.rpush('job_queue', job_data)

# 작업 처리
def worker():
    while True:
        job = r.lpop('job_queue')
        if job:
            process_job(job)
        else:
            time.sleep(1)

def process_job(job):
    # 작업 처리 로직
    print(f'Processing job: {job}')

# 작업 큐에 작업 추가
enqueue_job(b'job1')
enqueue_job(b'job2')
enqueue_job(b'job3')

# 워커 실행
worker()
```

`enqueue_jobs_1.py`: 작업 생성자 1

```python
import redis

r = redis.Redis()

# 작업 추가
def enqueue_job(job_data):
    r.rpush('job_queue', job_data)

# 작업 큐에 작업 추가
enqueue_job(b'job4')
enqueue_job(b'job5')
```

`enqueue_jobs_2.py`: 작업 생성자 2

```python
import redis

r = redis.Redis()

# 작업 추가
def enqueue_job(job_data):
    r.rpush('job_queue', job_data)

# 작업 큐에 작업 추가
enqueue_job(b'job6')
enqueue_job(b'job7')
enqueue_job(b'job8')
```

작업자를 실행한 후 작업 생성자(예제는 2개)를 실행하면 작업자가 작업을 처리합니다.

## 실시간 데이터 스트리밍 처리

Redis Streams는 실시간 데이터 스트리밍 처리를 위한 데이터 구조입니다. 스트림은 메시지의 연속이며, 각 메시지는 ID와 필드-값 쌍으로 구성됩니다.

```python
import redis

r = redis.Redis()

# 스트림 생성
r.xadd('stream', {'name': 'Alice', 'age': 30})

# 스트림에서 메시지 가져오기
messages = r.xrange('stream', '-', '+')
print(messages)
```

## 세션 (Session) 관리

Redis를 사용하여 세션을 관리할 수 있습니다. 세션은 사용자별로 공간을 마련하여 사용자의 정보나 상태를 저장합니다.

```python
import redis
import uuid

r = redis.Redis()

def create_session(user_id):
    session_id = str(uuid.uuid4())
    r.hset(f'sessions:{session_id}', 'user_id', user_id)
    return session_id

def get_session_data(session_id):
    session_data = r.hgetall(f'sessions:{session_id}')
    return session_data

def delete_session(session_id):
    r.delete(f'sessions:{session_id}')

# 세션 생성
session_id = create_session('user123')
print(session_id)

# 세션 데이터 가져오기
session_data = get_session_data(session_id)
print(session_data)

# 세션 삭제
delete_session(session_id)
```

- `hset`: 해시 설정
- `hgetall`: 해시 값 가져오기
- `delete`: 키 삭제

## 웹 애플리케이션에서 Redis를 이용한 세션 사용

### Flask

Flask에서 Redis를 사용하여 세션을 저장하려면 `flask-session` 라이브러리를 사용합니다.

```bash
pip install flask
pip install flask-session
```

`app.py`

```python
import os
import redis
from flask import Flask, session
from flask_session import Session

app = Flask(__name__)
app.config["SESSION_TYPE"] = "redis"
app.config["SESSION_PERMANENT"] = False
app.config["SESSION_USE_SIGNER"] = True
app.config["SECRET_KEY"] = os.environ.get("SECRET_KEY", "a_secure_default_key")
app.config["SESSION_REDIS"] = redis.Redis(host='localhost', port=6379)

# Initialize the session
Session(app)

@app.route('/')
def index():
    session['user'] = 'John Doe'
    return 'Session stored in Redis!'

@app.route('/get')
def get():
    return session.get('user', 'not set')


if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=True)
    # 외부에서 접속 가능하도록 host='0.0.0.0'
```

실행:

```bash
python app.py
```

테스트:

- `http://localhost:5000/get`에 접속: 'not set'
- `http://localhost:5000/`에 접속: 세션 정보 저장
- `http://localhost:5000/get`에 접속: 세션 정보 가져오기 - `John Doe`
- 다른 브라우저에서 `http://localhost:5000/get`에 접속: 'not set'

### FastAPI

```bash
pip install fastapi uvicorn[standard]
pip install redis aioredis
```

`main.py`

```python
import redis
import aioredis
import uuid
from fastapi import FastAPI, Response, Cookie

# Redis 연결 설정
redis_client = redis.Redis(host='localhost', port=6379, db=0)
redis = aioredis.from_url("redis://localhost", decode_responses=True)

# 세션 생성
def create_session(user_id):
    session_id = str(uuid.uuid4())
    data = {'user_id': user_id}
    redis_client.hset(f'sessions:{session_id}', mapping=data)
    return session_id

# 세션 데이터 가져오기
async def get_session_data(session_id):
    data = await redis.hgetall(f'sessions:{session_id}')
    return data

# 세션 삭제
def delete_session(session_id):
    redis_client.delete(f'sessions:{session_id}')

app = FastAPI()

# @app.post('/create_session')
@app.get('/create_session') # 간단한 테스트를 위해 GET으로 변경
def create_session_endpoint(response: Response, user_id: str):
    session_id = create_session(user_id)
    response.set_cookie(key='session_id', value=session_id)
    return {'message': 'Session created'}

@app.get('/get_session_data')
async def get_session_data_endpoint(session_id: str = Cookie(None)):
    if session_id:
        session_data = await get_session_data(session_id)
        return session_data
    else:
        return {'message': 'No session found'}
```

실행:

```bash
uvicorn main:app --reload
# 외부 접속 허용은 아래와 같이 실행
# uvicorn main:app --reload --host 0.0.0.0 --port=8000
```

테스트:

- `http://localhost:8000/get_session_data`에 접속: 'No session found'
- `http://localhost:8000/create_session?user_id=user123`에 접속: 세션 생성 및 쿠키 설정
- `http://localhost:8000/get_session_data`에 접속: `user_id: user123`
- 다른 브라우저에서 `http://localhost:8000/get_session_data`에 접속: 'No session found'

---

해시태그: #Redis #Python #데이터구조 #캐시 #작업큐 #스트림 #세션 #FastAPI #Flask

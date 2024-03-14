---
layout: single
title: "Python - Logging - 2 (로깅 적용)"
categories: Python
tag: [Python, Logging]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Flask에 로깅 적용

Flask에는 기본적으로 로깅이 설정되어 있습니다. logging를 import 하지 않고 `app.logger`로 사용할 수 있습니다.

```python
# pip install flask
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    app.logger.info('Root endpoint was called')
    return 'Hello, World!'

if __name__ == '__main__':
    # 로깅 레벨을 DEBUG로 설정하여 앱을 시작합니다.
    app.logger.setLevel('DEBUG')
    app.run(debug=True)
```

`http://127.0.0.1:5000/`에 접속하면 Console에 'Root endpoint was called'가 출력 됩니다.

### Flask 고급 로깅 설정

logging 모듈을 사용하여 로거, 핸들러 및 포매터를 적용해보겠습니다.

```python
from flask import Flask
import logging
from logging.handlers import RotatingFileHandler

app = Flask(__name__)

# 로그 파일 핸들러 설정
handler = RotatingFileHandler('app.log', maxBytes=10000, backupCount=3)
handler.setLevel(logging.INFO)
handler.setFormatter(logging.Formatter(
    '%(asctime)s %(levelname)s: %(message)s '
    '[in %(pathname)s:%(lineno)d]'
))

app.logger.addHandler(handler)

@app.route('/')
def hello_world():
    app.logger.info('Root endpoint was called')
    return 'Hello, World!'

if __name__ == '__main__':
    app.logger.setLevel(logging.INFO)
    app.run(debug=True)
```

`http://127.0.0.1:5000/`에 접속하면 'app.log' 파일에 설정된 format 형식으로 로그가 기록됩니다.

## FastAPI에 로깅 적용

FastAPI에는 Flask처럼 기본적인 로깅이 설정되어 있지 않아 'logging' 모듈을 사용하여 명시적으로 로깅 설정을 구성해야 합니다.

`main.py`: FastAPI의 최초 실행 파일입니다.

```python
import logging
from fastapi import FastAPI

app = FastAPI()

# 로깅 설정
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# 스트림 핸들러 생성 및 로거에 추가
handler = logging.StreamHandler()
handler.setLevel(logging.DEBUG)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
logger.addHandler(handler)

@app.get("/")
async def read_root():
    logger.info("Root endpoint called")
    return {"Hello": "World"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

FastAPI는 아래와 같은 명령어로 실행합니다.

```bash
uvicorn main:app --reload
```

`http://127.0.0.1:8000/`에 접속하면 Console에 'Root endpoint called'가 출력 됩니다.

## 여러 모듈 적용

여러 모듈로 구성된 프로그램에서 로깅을 사용하는 예제입니다.

`logging_config.py`: 최초 한번 로깅을 설정 합니다.

```python
import logging
import logging.handlers

def setup_logging(log_file, log_level=logging.INFO):

    formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

    # 파일 핸들러 설정
    # TimedRotatingFileHandler: 1초에 한 번씩 새로운 로그 파일을 생성
    file_handler = logging.handlers.TimedRotatingFileHandler(
      log_file, when='S', interval=1, backupCount=100)
    file_handler.setLevel(log_level)
    file_handler.setFormatter(formatter)

    # 스트림 핸들러 설정
    stream_handler = logging.StreamHandler()
    stream_handler.setLevel(log_level)
    stream_handler.setFormatter(formatter)

    # 루트 로거 설정
    logger = logging.getLogger()
    logger.setLevel(log_level)
    logger.addHandler(file_handler)
    logger.addHandler(stream_handler)
```

`app.py`: 프로그램에서 최초로 실행되는 파일입니다.

```python
import logging
from log_config import setup_logging
import other_module
import time

# 로깅 설정 초기화
setup_logging('app.log', logging.DEBUG)

# 메인 애플리케이션 로거 생성
logger = logging.getLogger(__name__)

def main():
    logger.info("Application start")
    time.sleep(1)
    other_module.do_something()
    time.sleep(1)
    logger.debug("Application end")

if __name__ == "__main__":
    main()
```

`other_module.py`: 'app.py'에서 호출되는 모듈입니다.

```python
import logging

# 모듈 로거 생성
logger = logging.getLogger(__name__)

def do_something():
    logger.debug("Doing something")
    logger.info("Task completed")
```

---

해시태그: #Python #Logging #logging #flask #fastapi

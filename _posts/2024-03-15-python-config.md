---
layout: single
title: "Python - 프로그램 환경 설정값 관리와 로깅 적용"
categories: Python
tag: [Python, Configuration, Logging]
toc: true
toc_sticky: true
toc_label: "목차"
---

파이썬 프로그램의 환경 설정값(DB 접속 정보, Logging 설정 등)을 관리하고 로깅을 적용하기

- YAML 파일을 사용하여 프로그램 환경 설정값을 관리
- 환경 설정 값은 한번만 읽어오기
- 환경 설정 값을 이용 하여 Logging 설정, DB 접속 하기
- 로깅의 핸들러와 포매터는 한번만 설정하기
- 모듈 별로 logger를 생성 하고 하나의 파일에 로그를 기록하기

## 파일 구조

```plaintext
.
├─ app_init.py       # 프로그램 최초 환경 설정(로깅 설정)
├─ config.py         # 환경 설정값 로드
├─ config.yaml       # 환경 설정값
├─ main.py           # 메인 프로그램
├─ db
|  └── connect_db.py # 데이터베이스 연결
├─ logs
|  └── app.log       # 로그 파일
└─ service
        ├── service_bbs.py  # 게시판 서비스
        └── service_qna.py  # QnA 서비스
```

## config.yaml

```yaml
database:
  db_host: 127.0.0.1
  db_port: 5432
  db_name: my_db
  db_user: my_user
  db_password: my_password

logging:
  format: '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
  log_file: logs/app.log
  stream_handler_yn: True # True, False
  file_handler_yn: True   # True, False  
  log_level: logging.DEBUG

service:
  bbs: bbs_test # test를 위한 값
  qna: qna_test # test를 위한 값
```

## config.py

config.yaml 파일을 읽어서 환경 설정값을 로드. 싱글톤 패턴을 사용하여 한번만 로드 하도록 구현.

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
        with open("config.yaml", "r") as file:
            cls.config = yaml.safe_load(file)

    @classmethod
    def get_config(cls):
        return cls.config
```

## app_init.py

프로그램이 시작될 때 최초 환경을 초기화 하기 위한 파일. 로깅 설정을 예를 들어 구현. 로깅 설정 시에 환경 설정값을 사용.

```python
import logging
import logging.handlers

def setup_logging(
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', 
        log_file='logs/app.log', 
        stream_handler_yn=True, 
        file_handler_yn=True, 
        log_level=logging.INFO
        ):

    logger = logging.getLogger()
    logger.setLevel(log_level)

    formatter = logging.Formatter(format)

    if file_handler_yn:
        file_handler = logging.handlers.TimedRotatingFileHandler(
            log_file, 
            when='W6', 
            interval=2, 
            backupCount=24, 
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
    from config import Config
    config = Config().get_config()  

   
    setup_logging(format=config['logging']['format'],
                  log_file=config['logging']['log_file'],
                  stream_handler_yn=config['logging']['stream_handler_yn'],
                  file_handler_yn=config['logging']['file_handler_yn'],
                  log_level=eval(config['logging']['log_level']))
```

## main.py

메인 프로그램. 환경 설정값을 로드하고 로깅 설정을 초기화. 다른 모듈 호출.

```python
from app_init import init
from config import Config
import logging

from service.service_bbs import get_bbs
from service.service_qna import get_qna

def select_bbs():
    bbs = get_bbs()
    logger.info(bbs)

def select_qna():
    qna = get_qna()
    logger.info(qna)    

if __name__ == "__main__":
    init()

    # 환경설정 로드 테스트
    config = Config().get_config()
    print(config)

    # 로깅 테스트
    logger = logging.getLogger(__name__)
    logger.info("Application start")

    ### 다른 모듈 환경 설정 조회 및 로깅 테스트
    select_bbs()
    select_qna()
```

## db/connect_db.py

데이터베이스 연결 모듈. 환경 설정값을 사용하여 데이터베이스 연결. 실제 데이터베이스 연결이 없이 환경 설정 값만 출력.

```python
import psycopg2
from config import Config
config = Config().get_config()

# 설정값 조회 테스트
print(f'database.db_host: {config["database"]["db_host"]}')
print(f'database.db_port: {config["database"]["db_port"]}')
print(f'database.db_name: {config["database"]["db_name"]}')
print(f'database.db_user: {config["database"]["db_user"]}')
print(f'database.db_password: {config["database"]["db_password"]}')

# 실제 데이터베이스 연결이 없으므로 주석처리

# class PostgreSQLCon():
#     def __init__(self):

#         self.connection_dict = {
#             'host': config['database']['db_host'],
#             'port': config['database']['db_port'],
#             'database': config['database']['db_name'],
#             'user': config['database']['db_user'],
#             'password': config['database']['db_password']
#         }
#         self.connection = None

#     def __enter__(self):
#         self.connection = psycopg2.connect(
#             user     = self.connection_dict['user'],
#             password = self.connection_dict['password'],
#             host     = self.connection_dict['host'],
#             port     = self.connection_dict['port'],
#             database = self.connection_dict['database'],
#         )

#         self.connection.autocommit = False
#         return self.connection

#     def __exit__(self, exc_type, exc_val, exc_tb):
#         if self.connection:
#             if exc_type is not None:
#                 self.connection.rollback()
#             else:
#                 self.connection.commit()
#             self.connection.close()
```

## service/service_bbs.py

다른 서비스(또는 모듈)에서 환경 설정값 로드 및 로깅을 테스트하기 위한 예시. 실제 데이터베이스 연결이 없으므로 주석처리.

```python
import logging
from config import Config
# from db.connect_db import PostgreSQLCon
import psycopg2.extras

logger = logging.getLogger(__name__)

config = Config().get_config()

def get_bbs():
    # 설정값 조회 테스트
    logger.debug(f'service.bbs: {config["service"]["bbs"]}')

    # 로깅 테스트
    logger.debug(f'------start get_bbs------')

    # 실제 데이터베이스 연결이 없으므로 주석처리
    # with PostgreSQLCon() as connection:
    #     cursor = connection.cursor(cursor_factory=psycopg2.extras.RealDictCursor)
    #     sql = 'select bbs_1'
    #     try:
    #         cursor.execute(sql)
    #         rows = cursor.fetchall()
    #         connection.commit()
    #     except Exception as e: 
    #         connection.rollback()
    #         logger.error(e)
    #     logger.debug(f'rows: {rows}')    
    #     logger.debug(f'------end get_bbs------')

    rows = [{'bbs_1': 'bbs_1'}, {'bbs_2': 'bbs_2'}]

    return rows
```

## service/service_qna.py

테스트를 위한 다른 서비스(또는 모듈) 하나 더 추가.

```python
import logging
from config import Config
# from db.connect_db import PostgreSQLCon
import psycopg2.extras

logger = logging.getLogger(__name__)

config = Config().get_config()

def get_qna():
    # 설정값 조회 테스트
    logger.debug(f'service.qna: {config["service"]["qna"]}')

    # 로깅 테스트
    logger.debug(f'------start get_qna------')

    # 실제 데이터베이스 연결이 없으므로 주석처리
    # with PostgreSQLCon() as connection:
    #     cursor = connection.cursor(cursor_factory=psycopg2.extras.RealDictCursor)
    #     sql = 'select qna_1'
    #     try:
    #         cursor.execute(sql)
    #         rows = cursor.fetchall()
    #         connection.commit()
            
    #     except Exception as e: 
    #         connection.rollback()
    #         logger.error(e)
    #     logger.debug(f'rows: {rows}')    
    #     logger.debug(f'------end get_qna------')

    rows = [{'qna_1': 'qna_1'}, {'qna_2': 'qna_2'}]

    return rows
```

## logs/app.log

프로그램을 실행 하면 로그 파일이 생성되고 로그가 기록.

```python
# 기록된 로그, 실제에서 YYYY-MM-DD는 실행한 날짜와 시간으로 출력. 
YYYY-MM-DD 15:45:27,049 - __main__ - INFO - Application start
YYYY-MM-DD 15:45:27,050 - service.service_bbs - DEBUG - service.bbs: bbs_test
YYYY-MM-DD 15:45:27,050 - service.service_bbs - DEBUG - ------start get_bbs------
YYYY-MM-DD 15:45:27,050 - __main__ - INFO - [{'bbs_1': 'bbs_1'}, {'bbs_2': 'bbs_2'}]
YYYY-MM-DD 15:45:27,050 - service.service_qna - DEBUG - service.qna: qna_test
YYYY-MM-DD 15:45:27,051 - service.service_qna - DEBUG - ------start get_qna------
YYYY-MM-DD 15:45:27,051 - __main__ - INFO - [{'qna_1': 'qna_1'}, {'qna_2': 'qna_2'}]
```

---

해시태그: #Python #config #YAML #설정값 #logging

---
layout: single
title: "Python - Logging - 1 (로깅 기본)"
categories: Python
tag: [Python, Logging]
toc: true
toc_sticky: true
toc_label: "목차"
---

## 로깅(Logging)이란?

- 로깅(Logging)은 애플리케이션이나 시스템의 실행 중에 발생하는 이벤트나 메시지를 기록하는 과정
- 디버깅, 모니터링, 문제 해결, 감사 목적 등으로 사용
- 애플리케이션의 실행 상태를 추적하고 오류 발생 시 원인 분석
- 로그를 통해 시스템의 활동을 기록하고 모니터링
- 로그는 개발 단계뿐만 아니라 **운영 환경에서도 매우 중요한 역할**

### 로깅 레벨(Level)의 이해

로깅 레벨은 로그 메시지의 심각성이나 중요도를 나타내는 수준입니다.

일반적인 로깅 레벨

| 로깅 레벨 | 내용 |
| :------ | :------ |
| DEBUG | 개발 시 디버깅을 위해 정보 기록 |
| INFO | 프로그램 실행 과정에서 발생하는 정보 기록 |
| WARNING | 발생 가능한 문제 상황에 대한 경고 메시지 기록 |
| ERROR | 에러 발생 시 에러 정보 기록 |
| CRITICAL | 치명적인 오류로 프로그램 실행이 불가능한 상황 기록 |

로깅 레벨이 높을수록 더 중요한 메시지를 의미합니다. 개발 단계에서는 DEBUG 레벨까지 로깅하고, 프로덕션 단계에서는 INFO 이상의 레벨만 로깅하는 것이 일반적입니다. 로깅 레벨을 적절히 설정하여 필요한 정보만 기록함으로써 로그 파일의 크기를 관리할 수 있습니다.

## 파이썬 로깅 모듈(logging)

logging은 Python에서 로깅 기능을 제공하는 표준 라이브러리 모듈입니다.

이 모듈은 로거(Logger), 핸들러(Handler), 포맷터(Formatter), 필터(Filter) 등의 주요 컴포넌트로 구성되어 있습니다.

| 컴포넌트 | 내용 |
| :------ | :------ |
| 로거 | 로그 메시지를 생성 |
| 핸들러 | 로그 메시지를 특정 대상(콘솔, 파일 등)으로 전송 |
| 포맷터 | 로그 메시지의 형식을 지정 |
| 필터 | 로그 메시지를 필터링하는 역할 |

### 로거(Logger) 객체 생성

로깅을 시작하려면 먼저 로거 객체를 생성해야 합니다.

- logging.getLogger()를 사용하여 로거 객체를 생성할 수 있습니다.
- logging.getLogger('로거이름')과 같이 로거 이름을 지정할 수 있습니다.
- 로거 이름은 계층 구조를 가질 수 있으며, 점(.)으로 구분됩니다. (예: 'main.sub')
- 같은 이름으로 getLogger()를 호출하면 이전에 생성된 로거 객체를 반환합니다.
- logging.root 또는 logging.getLogger()를 호출하면 루트 로거를 가져올 수 있습니다.

```python
import logging

# 로거 생성
logger = logging.getLogger('my_logger')

# 루트 로거 생성
root_logger = logging.getLogger()
```

## 로그 메시지 출력

로거 객체는 로그 메시지를 출력하는 다양한 메서드를 제공합니다. 각 메서드는 로그 레벨에 따라 다르며, 해당 레벨 이상의 로그 메시지만 출력됩니다.

```python
import logging

logger = logging.getLogger('my_logger')

logger.debug('This is a debug message')
logger.info('This is an info message')
logger.warning('This is a warning message')
logger.error('This is an error message')
logger.critical('This is a critical message')
```

debug와 info는 출력 되지 않습니다. 기본적으로 로깅 레벨은 WARNING으로 설정되어 있기 때문입니다.

```bash
# 결과
This is a warning message
This is an error message  
This is a critical message
```

### 로그 메시지 포맷팅

로그 메시지에 추가 정보를 포함하거나 특정 형식으로 출력하려면 포맷팅을 사용할 수 있습니다. 로그 메시지 포맷팅은 % 연산자나 str.format() 메서드, 또는 f-strings를 사용할 수 있습니다.

```python
import logging

logger = logging.getLogger('my_logger')

# % 연산자 사용
logger.warning('Processing file %s', 'data.csv')

# str.format() 메서드 사용
opt = 'a'
logger.warning('{} is not a valid option'.format(opt))

# f-strings 사용
value = 42
logger.warning(f'The value is {value}')
```

## 로그 핸들러(Handler)

### 스트림 핸들러(StreamHandler)

로그 메시지를 파일이나 스트림(stream)으로 출력합니다.

```python
import logging

# 스트림 핸들러 생성
stream_handler = logging.StreamHandler()

# 로거에 핸들러 추가
logger = logging.getLogger('my_logger')
logger.addHandler(stream_handler)

# 로그 메시지 출력
logger.warning('This is an warning message')
```

### 파일 핸들러(FileHandler)

로그 메시지를 파일에 기록합니다.

```python
import logging

# 파일 핸들러 생성
file_handler = logging.FileHandler('app.log', mode='w')

# 로거에 핸들러 추가
logger = logging.getLogger('my_logger')
logger.addHandler(file_handler)

# 로그 메시지 출력
logger.error('This is an error message')
```

app.log 파일이 생성되고, 해당 파일에 로그 메시지(This is an error message)가 기록됩니다.

### 다양한 핸들러 유형

스트림 핸들러와 파일 핸들러가 가장 일반적으로 사용되지만, 다양한 핸들러 유형을 사용할 수 있으며 여러 개의 핸들러를 동시에 사용 할 수도 있습니다.

| 핸들러 유형 | 내용 |
| :------ | :------ |
| RotatingFileHandler | 파일 크기가 일정 수준 이상이 되면 새로운 파일을 생성하고 로그를 기록 |
| TimedRotatingFileHandler | 지정된 시간 간격마다 새로운 로그 파일을 생성 |
| SocketHandler | 로그 메시지를 네트워크 소켓으로 전송 |
| SMTPHandler | 로그 메시지를 이메일로 전송 |
| HTTPHandler | 로그 메시지를 HTTP GET 또는 POST 요청으로 전송 |
| MemoryHandler |  로그 메시지를 메모리 버퍼에 저장 |

## 로그 포맷터(Formatter)

로그 포맷터를 사용하여 로그 메시지의 형식을 변경 할 수 있습니다.

```python
import logging

# 포맷터 생성
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# 핸들러에 포맷터 설정
stream_handler = logging.StreamHandler()
stream_handler.setFormatter(formatter)

# 로거에 핸들러 추가
logger = logging.getLogger('my_logger')
logger.addHandler(stream_handler)

# 로그 메시지 출력
logger.warning('This is an warning message')
```

> 예시 포맷 문자열  
> %(asctime)s - %(levelname)s - %(message)s

## 로그 필터링(Filtering)

로그 레벨을 기준으로 로그 메시지를 필터링할 수 있습니다. 로거와 핸들러 모두에 로그 레벨을 설정할 수 있습니다. 로거의 로그 레벨은 해당 로거의 하위 로거에도 영향을 미칩니다.

```python
import logging

# 로거 생성 및 레벨 설정
logger = logging.getLogger('my_logger')
logger.setLevel(logging.WARNING) # 로거 레벨 설정

# 스트림 핸들러 생성 및 레벨 설정
stream_handler = logging.StreamHandler()

# 파일 핸들러 생성 및 레벨 설정
file_handler = logging.FileHandler('app.log', mode='w')
# 핸들러에 레벨을 설정 하면 logger의 레벨에 추가로 필터링 됨
file_handler.setLevel(logging.ERROR)

# 핸들러를 로거에 추가
logger.addHandler(stream_handler)
logger.addHandler(file_handler)

# 로그 메시지 출력 (WARNING 이상만 출력됨)
logger.debug('This is a debug message')   # 콘솔, 파일에 출력되지 않음
logger.info('This is an info message')    # 콘솔, 파일에 출력되지 않음
logger.warning('This is a warning message') # 콘솔 출력됨, 파일 출력되지 않음
logger.error('This is an error message')  # 콘솔, 파일에 출력됨
```

## 로그 회전(Log Rotation)

애플리케이션이 실행되는 동안 로그 파일의 크기가 계속 증가하게 됩니다. 로그 파일이 무한정 커지면 디스크 공간을 많이 차지하고, 로그 파일 관리가 어려워집니다.
이를 방지하기 위해 로그 회전(Log Rotation) 기능을 사용하여 로그 파일을 주기적으로 교체하고 관리할 수 있습니다. 로그 회전을 통해 로그 파일의 크기를 제한하고, 오래된 로그 파일을 압축하거나 삭제할 수 있습니다.

### RotatingFileHandler

로그 파일의 크기가 지정된 값을 초과하면 새로운 로그 파일을 생성합니다.

```python
import logging
from logging.handlers import RotatingFileHandler

# 로그 회전 핸들러 생성
handler = RotatingFileHandler('app.log', maxBytes=10000, backupCount=5)

# 로거에 핸들러 추가
logger = logging.getLogger('my_logger')
logger.setLevel(logging.INFO)
logger.addHandler(handler)

# 로그 메시지 출력
for i in range(1000):
    logger.info(f'This is log message {i}')
```

`app.log.2`, `app.log.1`, `app.log` 파일이 생성되었습니다.

- maxBytes: 로그 파일의 최대 크기를 지정
- backupCount: 오래된 로그 파일을 유지할 최대 개수

### TimedRotatingFileHandler

로그 파일을 지정된 시간 간격마다 생성합니다.

```python
import logging
from logging.handlers import TimedRotatingFileHandler
import time

# 로그 회전 핸들러 생성 (매일 자정에 로그 파일 교체)
handler = TimedRotatingFileHandler('app.log', when='S', interval=2, backupCount=7)

# 로거에 핸들러 추가
logger = logging.getLogger('my_logger')
logger.addHandler(handler)
logger.setLevel(logging.INFO)

# 로그 메시지 출력
for i in range(3):
    time.sleep(1)
    logger.info(f'{i}: This is an info message')
```

`app.log.년월일시분초`, `app.log` 파일이 생성되었습니다.

- when: 'S', 'M', 'H', 'D', 'W0'-'W6' - 로그 파일 회전 주기를 설정
- interval: 로그 파일 회전 간격 (when 매개변수와 함께 사용)
- backupCount: 최대 백업 파일 수

| when | 회전 주기 |
| :------ | :------ |
| 'S' | 매 초마다 로그 파일 회전 |
| 'M5' | 5분마다 로그 파일 회전 |
| 'H12' | 12시간마다 로그 파일 회전 |
| 'W0' | 매주 월요일 자정에 로그 파일 회전 - W1(화요일)... |
| 'midnight' | 매일 자정에 로그 파일 회전 |
| 'noon' | 매일 정오에 로그 파일 회전 |

## 로깅 구성(Configuration)

구성 파일을 사용하여 로깅 설정 할 수 있습니다. 로깅 구성 파일은 일반적으로 INI 형식, JSON 형식, YAML 형식 등으로 작성됩니다.

`logging.yaml`: 구성 파일

```yaml
version: 1
disable_existing_loggers: False
formatters:
  simple:
    format: '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
handlers:
  console:
    class: logging.StreamHandler
    formatter: simple
    stream: ext://sys.stdout
  file:
    class: logging.FileHandler
    filename: app.log
    formatter: simple
loggers:
  my_logger:
    handlers: [console, file]
    level: INFO
```

| 항목 | 내용 |
| :------ | :------ |
| version | 구성 파일 버전, 현재 1이 유일한 유효한 값이며 필수항목 |
| disable_existing_loggers | 기존 로거 비활성화 여부, False - 이미 존재하는 로거들을 비활성화하지 않음 |
| formatters | 포맷터 설정 |
| handlers | 핸들러 설정 |
| loggers | 로거 설정 |

```python
# pip install PyYAML # 설치 필요
import logging.config
import yaml

with open('logging.yaml', 'r') as f:
    config = yaml.safe_load(f.read())
    logging.config.dictConfig(config)

# 로거 사용
logger = logging.getLogger('my_logger')
logger.info('This is an info message')
```

---

해시태그: #Python #Logging #logging #logger #handler #StreamHandler #FileHandler #formatter #filter #yaml

---
layout: single
title: "Python - Python에서 YAML 사용하기"
categories: Python
tag: [Python, YAML]
toc: true
toc_sticky: true
toc_label: "목차"
---

## YAML이란?

- YAML(YAML Ain't Markup Language)은 가독성이 좋고, 계층 구조를 표현하기 쉬우며, 코드 생성이 용이합니다.
- YAML은 설정 파일, 로그 파일, 데이터 교환 등 다양한 분야에서 활용됩니다.

### 구성 요소 및 문법

- 스칼라 값(scalar)은 문자열, 숫자, 불린 등의 단일 값을 의미합니다.
- 시퀀스(sequence)는 순서가 있는 값의 목록을 나타냅니다.
- 매핑(mapping)은 키-값 쌍으로 이루어진 데이터 구조입니다.
- 주석은 # 기호로 시작합니다.
- 앵커(&)와 별칭(*)을 사용하여 중복 데이터를 재사용할 수 있습니다.

### JSON과의 비교

| 항목 | 설명 |
| :------ | :------ |
| 가독성 | YAML은 사람이 읽고 쓰기 쉽도록 설계되었으며, JSON보다 더 자연스러운 언어 구조를 가지고 있습니다 |
| 주석 | YAML은 주석을 지원하여 문서 내에서 설명을 추가할 수 있지만, JSON은 주석을 지원하지 않습니다 |
| 표현력 | YAML은 앵커와 별칭 같은 고급 기능을 제공하여 데이터의 중복을 줄일 수 있지만, JSON은 이러한 기능을 제공하지 않습니다 |
| 사용 사례 | YAML은 주로 설정 파일, 문서 헤더 등 사람이 직접 다루는 데이터에 사용됩니다. 반면, JSON은 웹 API와 같은 애플리케이션 간 데이터 교환에 널리 사용됩니다 |

### YAML 예시

`document.yaml`

```yaml
# This is a comment.

# Scalar values
name: John Doe
age: 30
gender: male

# Sequence
hobbies:
  - Hiking
  - Reading
  - Cooking

# Mapping
address:
  street: 123 Main Street
  city: Anytown
  state: CA
  zip: 12345
```

## PyYAML 설치

- Python의 YAML 라이브러리: PyYAML
- PyYAML 설치: `pip install pyyaml`
- 라이브러리 로드: `import yaml`

## YAML 파일 읽기

하나의 문서를 읽을 때는 `yaml.safe_load()` 함수를 사용합니다.

```python
import yaml

with open('document.yaml') as f:
    document = yaml.safe_load(f)

print(document)    
```

- YAML의 스칼라 값은 Python의 문자열, 숫자, 불린 등에 매핑됩니다.
- YAML의 시퀀스는 Python의 리스트에 매핑됩니다.
- YAML의 매핑은 Python의 딕셔너리에 매핑됩니다.

여러 문서를 읽을 때는 `yaml.safe_load_all()` 함수를 사용합니다.

`documents.yaml`

```yaml
---
name: John
age: 30
---
name: Jane
age: 25
```

```python
import yaml

with open('documents.yaml') as f:
    documents = yaml.safe_load_all(f)

    for document in yaml.safe_load_all(f):
        print(document)
```

## YAML 파일 쓰기

- yaml.dump(data, stream) 함수를 사용하여 Python 데이터 구조를 YAML 형식으로 변환할 수 있습니다.
- data에는 Python 데이터 구조(리스트, 딕셔너리 등)를 전달합니다.
- stream에는 YAML 문자열이나 파일 객체를 전달할 수 있습니다.

```python
import yaml

config = {
    'name': 'John Doe',
    'age': 30,
    'skills': ['Python', 'YAML']
}
with open('config.yaml', 'w') as f:
    yaml.dump(config, f)
```

아래와 같은 `config.yaml` 파일이 생성됩니다.

```yaml
age: 30
name: John Doe
skills:
- Python
- YAML
```

## 프로젝트 설정 관리 예시

`config.yaml`: 예시

```yaml
database:
  host: localhost
  port: 3306
  username: user
  password: pass

server:
  port: 8080

logging:
  level: INFO
```

`app.py`: 예시

```python
import yaml

with open('config.yaml') as f:
    config = yaml.safe_load(f)

print(config['database']['host'])
print(config['server']['port'])
print(config['logging']['level'])
```

> 프로젝트 설정 파일을 개발와 운영을 분리하여 관리할 수 있습니다.  
> 개발 환경에서는 `config_dev.yaml`, 운영 환경에서는 `config_prod.yaml` 파일을 사용할 수 있습니다.

## 앵커(&)와 별칭(*)을 사용

앵커(&)와 별칭(*)은 문서 내에서 반복되는 값을 정의하고 재사용하기 위해 사용됩니다.

`server_config.yaml`

```yaml
default_settings: &defaultSettings
  timeout: 120
  enable: true
  max_connections: 10

server1:
  <<: *defaultSettings
  ip: 192.168.1.1

server2:
  <<: *defaultSettings
  ip: 192.168.1.2
  max_connections: 20
```

```python
import yaml

with open('server_config.yaml') as f:
    server_config = yaml.safe_load(f)

print(server_config)
```

```python
# 결과
{'default_settings': {'timeout': 120, 'enable': True, 'max_connections': 10}, 'server1': {'timeout': 120, 'enable': True, 'max_connections': 10, 'ip': '192.168.1.1'}, 'server2': {'timeout': 120, 'enable': True, 'max_connections': 20, 'ip': '192.168.1.2'}}
```

## 한글이 포함된 YAML 파일

한글이 포함된 YAML 파일을 읽을 때는 `encoding='utf-8'` 옵션을 사용합니다.

```yaml
# document_kr.yaml
name: 홍길동
age: 30
```

```python
import yaml

with open('document_kr.yaml', encoding='utf-8') as f:
    document_kr = yaml.safe_load(f)

print(document_kr)
```

---

해시태그: #Python #YAML #PyYAML #스칼라 #시퀀스 #매핑 #앵커 #별칭 #JSON #한글

---
layout: single
title: "Python - 가상환경(venv)"
categories: Python
tag: [Python, venv]
toc: true
toc_sticky: true
toc_label: "목차"
---

## 가상환경 (Virtual Environment) 이란

가상환경이란 파이썬 인터프리터, 라이브러리, 스크립트 등이 포함된 독립된 파이썬 실행 환경을 의미하며 프로젝트마다 서로 다른 버전의 패키지가 필요 때 가상환경으로 분리하여 관리할 수 있습니다.

일반적으로 새로운 프로젝트를 시작할 때마다 가상환경을 생성하여 작업합니다.

### 장점

- 패키지 버전 충돌 방지
- 격리된 실행 환경 제공
- 프로젝트 의존성 관리 용이
- 재현 가능한 개발 환경 구축

## 'venv' 모듈

Python 3.3 버전부터 기본적으로 제공되는 'venv' 모듈을 사용하여 가상환경을 생성할 수 있습니다.

### 가상환경 생성

터미널이나 명령 프롬프트에서 실행합니다.

```bash
# 가상 환경을 생성하기 원하는 디렉토리로 이동
python -m venv myenv
```

'myenv'는 가상환경의 이름으로 원하는 이름으로 변경 가능합니다.

### 가상환경 활성화

가상 환경 사용하려면 활성화해야 합니다. 명령은 운영 체제에 따라 다릅니다.

- Windows

```bash
myenv\Scripts\activate
```

- macOS, Linux

```bash
source myenv/bin/activate
```

가상환경이ㅣ 활성화되면 프롬프트 앞에 가상환경 이름이 표시됩니다.

```bash
(myenv) $
```

### 패키지 설치

가상환경이 활성화된 상태에서 필요한 패키지를 설치합니다.

```bash
pip install pandas
```

가상환경 내에서만 패키지가 설치되며 다른 환경에 영향을 주지 않습니다.

### 가상환경 비활성화

가상환경을 비활성화하려면 `deactivate` 명령을 실행합니다.

```bash
deactivate
```

## 패키지 분리 확인

'myenv'와 'myenv2'라는 가상환경을 생성하고 각각에 'pandas' 패키지를 설치한 후 패키지 목록을 확인합니다.

### 'myenv' 가상환경

```bash
# 가상환경 생성
python -m venv myenv

# 가상환경 활성화
source myenv/bin/activate

# 패키지 설치
pip install pandas

# 패키지 목록 확인
pip list

# import 패키지
python

>>> import pandas
>>>
# 정상적으로 패키지가 설치되어 import할 수 있습니다.
```

디렉토리 경로 확인: `\myenv\Lib\site-packages` 디렉토리에 'pandas' 패키지가 설치되어 있습니다.

### 'myenv2' 가상환경

```bash
# 가상환경 생성
python -m venv myenv2

# 가상환경 활성화
source myenv2/bin/activate

# 패키지 목록 확인
pip list

# import 패키지
python

>>> import pandas
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'pandas'
# 패키지가 설치되지 않아 import할 수 없습니다.
```

디렉토리 경로 확인: `\myenv2\Lib\site-packages` 디렉토리에 'pandas' 패키지가 없습니다.

## requirements.txt

'requirements.txt' 파일은 프로젝트에 필요한 패키지 목록을 저장하는 파일입니다. `pip install -r requirements.txt` 명령을 사용하여 한 번에 패키지를 설치할 수 있습니다.

`pip freeze` 명령을 사용하여 현재 활성화된 가상환경의 패키지 목록을 `requirements.txt` 파일로 저장할 수 있습니다.

### 설치하기

`requirements.txt`

```plaintext
beautifulsoup4
requests
```

버전 명시

```plaintext
beautifulsoup4==4.9.3
requests==2.25.1
```

터미널에서 아래 명령을 실행합니다.

```bash
pip install -r requirements.txt
```

'requirements.txt' 파일의 내용대로 패키지가 설치됩니다.

### 생성하기

터미널에서 아래 명령을 실행하여 현재 활성화된 가상환경의 패키지 목록을 `requirements.txt` 파일로 저장합니다.

```bash
pip freeze > requirements.txt
```

---

해시태그: #Python #가상환경 #VirtualEnvironment #venv #Source #activate #deactivate #requirements.txt

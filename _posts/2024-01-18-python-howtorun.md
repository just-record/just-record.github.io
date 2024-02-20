---
layout: single
title: "Python - 실행 방법"
categories: Python
tag: [Python]
toc: true
toc_sticky: true
toc_label: "목차"
---
Python을 실행하는 방법은 2가지가 있습니다.

## Interactive mode(대화형 모드)

터미널에서 python을 입력하면 interactive mode(대화형 모드)로 진입합니다.

```bash
python
```

```text
Python 3.10.0 (default, Oct  6 2021, 12:14:54)
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license".
>>>
```

`hello world`를 출력해 보겠습니다.

```text
>>> print('hello world')
hello world
```

변수 `a`에 `hello world`를 저장하고 출력해 보겠습니다.

```text
>>> a = 'hello world'
>>> print(a)
hello world
```

`python`을 인수없이 실행 시키면 대화형 모드로 진입합니다. python 코드를 한 줄 입력하고 엔터를 누르면 바로 코드가 실행되어 결과를 확인 할 수 있습니다.

## Script Execution(스크립트 실행)

python 코드를 파일에 저장하고 실행하는 방법입니다. `hello.py` 파일을 생성하고 아래의 코드를 입력합니다.

```python
a = 'hello world'
print(a)
```

터미널에서 아래의 명령어를 입력합니다.

```bash
python hello.py
```

```text
hello world
```

`python` 명령어 뒤에 실행할 파일(.py)을 입력하면 해당 파일의 코드가 실행됩니다.

## 정리

python을 설치하면 python이란 명령어를 사용할 수 있습니다. 아무런 인수 없이 python이란 명령어를 입력하면 대화형 모드로 진입합니다. python 파일(.py)을 생성하고 python 명령어 뒤에 파일명을 입력하면 해당 파일의 코드가 실행됩니다.

visual studio code나 pycharm 같은 IDE도 위의 방법을 벗어나서 실행하는 방법은 없습니다. IDE에서는 위의 방법을 편리하게 사용할 수 있도록 도와줍니다.

1. python이란 명령어를 인식해야 합니다.  
2. 실행 하고자 하는 .py를 찾을 수 있도록 경로를 설정해야 합니다.

---

해시태그: #python #decorator #closure

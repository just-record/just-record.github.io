---
layout: single
title: "Python - 중급 07 (decorator)"
categories: Python
tag: [Python, decorator]
toc: true
toc_sticky: true
toc_label: "목차"
---
decorator(데코레이터)란?

예시를 바로 보도록 하겠습니다.

## 예시

```python
def decorator_function(original_function):
    def wrapper_function():
        print('before')
        original_function()
        print('after')
    return wrapper_function


def display():
    print('display 함수가 실행됐습니다.')

decorated_display = decorator_function(display)
decorated_display()
```

```python
display 함수가 실행됐습니다.
```

display 함수를 파라미터로 decorator_function을 실행하고 그 결과값(decorated_display)을 다시 함수처럼 호출 하였더니 **앞서 파라미터로 전달한 display 함수가 실행**되었습니다. 뭔가 복잡합니다. 하나씩 살펴보겠습니다.

- decorator_function
  - 내부에서 wrapper_function을 정의하고 반환합니다.
  - wrapper_function은 decorator_function의 파라미터로 전달된 original_function을 호출합니다.
- 문자열을 출력하는 display 함수를 정의합니다.
- 이제 decorator_function을 호출합니다.
  - display 함수가 decorator_function의 파라미터(original_function)로 전달됩니다.
  - 반환된 값은 original_function을 실행 하는 wrapper_function입니다.
- 반환된 wrapper_function을 호출합니다.
  - wrapper_function은 decorator_function의 파라미터로 전달된 original_function를 기억하고 있다가 호출하게 됩니다.(closure)
  - display 함수가 실행됩니다.

> original_function에 파라미터가 있는 경우

```python
def decorator_function(original_function):
    def wrapper_function(*args, **kwargs):
        print('before')
        original_function(*args, **kwargs)
        print('after')
    return wrapper_function


def display():
    print('display 함수가 실행됐습니다.')

def display_info(name, age):
    print(f'display_info({name}, {age}) 함수가 실행됐습니다.')

decorated_display = decorator_function(display)
decorated_display()
print()
decorated_display = decorator_function(display_info)
decorated_display('John', 25)
```

```python
before
display 함수가 실행됐습니다.
after

before
display_info(John, 25) 함수가 실행됐습니다.
after
```

첫번째 예시와 차이점은 **wrapper_function 함수에 파라미터가 추가** 되었습니다. original_function를 호출 할 때 파라미터를 전달하기 위해서 입니다.

- 파라미터가 없는 display 함수를 인수로 전달하여 호출합니다.
  - 반환된 decorated_display를 호출하면 'display 함수가 실행됐습니다.'라는 문자열이 출력됩니다.
- 2개의 파라미터(name, age)를 받는 display_info 함수를 인수로 전달하여 호출합니다.
  - wrapper_function은 파라미터(name, age)가 포함 된 채로  original_function을 기억합니다.
  - 반환된 decorated_display를 호출하면 warpper_function가 실행 되고 파라미터(name, age)가 포함 된 채로 original_function이 실행됩니다.
  - 'display_info(John, 25) 함수가 실행됐습니다.'라는 문자열이 출력됩니다.

> @ 심볼을 사용한 데코레이터

```python
def decorator_function(original_function):
    def wrapper_function(*args, **kwargs):
        print('before')
        original_function(*args, **kwargs)
        print('after')
    return wrapper_function


@decorator_function
def display():
    print('display 함수가 실행됐습니다.')

@decorator_function
def display_info(name, age):
    print(f'display_info({name}, {age}) 함수가 실행됐습니다.')

display()
print()
display_info('John', 25)
```

```python
before
display 함수가 실행됐습니다.
after

before
display_info(John, 25) 함수가 실행됐습니다.
after
```

앞의 예시와 잘 비교해 보겠습니다.

```python
def display():
    print('display 함수가 실행됐습니다.')

decorated_display = decorator_function(display)
decorated_display()
```

```python
@decorator_function
def display():
    print('display 함수가 실행됐습니다.')

display()
```

- decorator_function를 호출 할 때 display를 인수로 전달  
  -> @decorator_function 다음에 displya 함수를 정의
- decorator_function의 반환값을 decorated_display에 할당하고 decorated_display를 호출  
  -> 그저 display 함수를 호출

사용법이 한결 간단해졌습니다.

## decorator의 여러 예시

> 함수의 실행 시간을 출력하는 예시

```python
import time


def decorator_function(original_function):
    def wrapper_function(*args, **kwargs):
        start_time = time.time()
        result = original_function(*args, **kwargs)
        end_time = time.time()
        print(f'{original_function.__name__} 함수의 실행 시간은 {end_time - start_time}초 입니다.')
        return result
    return wrapper_function


@decorator_function
def display():
    print('display 함수가 실행됐습니다.')
    time.sleep(1)


@decorator_function
def display_info(name, age):
    print(f'display_info({name}, {age}) 함수가 실행됐습니다.')
    time.sleep(1)


display()
print()
display_info('John', 25)
```

```python
display 함수가 실행됐습니다.
display 함수의 실행 시간은 1.0010712146759033초 입니다.

display_info(John, 25) 함수가 실행됐습니다.
display_info 함수의 실행 시간은 1.0010995864868164초 입니다.
```

## decorator의 활용

> flask에서 decorator를 사용하는 예시를 살펴보겠습니다.

`app.py` 파일을 생성하고 아래의 코드를 작성합니다.

```python
# !pip install flask

from flask import Flask

app = Flask(__name__)   # Flask 객체 생성(__name__ 이름으로)

@app.route('/')
def home():
    return '<h1>Hello Flask!</h1>'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port='5050', debug=True)
```

아래와 같이 python app.py를 실행하면 flask 서버가 실행됩니다.

```bash
python app.py
```

아래의 URL로 접속합니다.

<http://localhost:5050/>

Hello Flask!라는 문자열이 출력됩니다.

```text
Hello Flask!
```

## 연습 문제

- 주어진 함수가 실행될 때마다 "함수가 호출 되었습니다"라는 메시지를 출력하세요.  
  def hello():  
    print("Hello World!")

```python
def decorator_function(original_function):
    def wrapper_function():
        print('함수가 호출 되었습니다.')
        original_function()
    return wrapper_function


@decorator_function
def hello():
    print('Hello World!')

hello()
```

```python
함수가 호출 되었습니다.
Hello World!
```

- 주어진 함수가 실행될 때마다 "함수가 호출 되었습니다 - 파라미터는 {}입니다."라는 메시지를 출력하세요.  
  def hello(name):  
    print("Hello World!")

```python
def decorator_function(original_function):
    def wrapper_function(*args, **kwargs):
        print(f'함수가 호출 되었습니다 - 파라미터는 {args}입니다.')
        original_function(*args, **kwargs)
    return wrapper_function


@decorator_function
def hello(name):
    print('Hello World!')

hello('John')
```

```python
함수가 호출 되었습니다 - 파라미터는 ('John',)입니다.
Hello World!
```

---

해시태그: #python #decorator #closure

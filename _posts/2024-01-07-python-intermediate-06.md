---
layout: single
title: "Python - 중급 06 (closure)"
categories: Python
tag: [Python, closure]
toc: true
toc_sticky: true
toc_label: "목차"
---
closure(클로저)란?  

어떻게 설명 드리면 좋을지 계속 고민하다가 모든 걸 접고 사용 예시를 보여 드리는게 가장 좋은 방법이라고 결론을 내렸습니다.

## 예시

```python
def outer_function():
    message = 'Hi'

    def inner_function():
        print(message)

    return inner_function


my_closure = outer_function()
my_closure()
```

```python
Hi
```

- outer_function은 내부에서 inner_function을 정의하고 반환합니다.
- inner_function은 outer_function의 지역 변수 message를 참조합니다.
- outer_function()을 호출하면 inner_function을 반환합니다. my_closure에는 inner_function이 매핑됩니다.
- my_closure를 호출하면 inner_function이 실행됩니다. 이 때 inner_function은 outter_function의 지역 변수인 **message 변수의 값을 기억**하고 있습니다. `print(message)`를 통해 message 변수의 값을 출력합니다.

> 파라미터가 있는 경우

```python
def outer_function(msg):
    message = msg

    def inner_function():
        print(message)

    return inner_function


my_closure = outer_function('Hi')
my_closure()
```

```python
Hi
```

- 첫번째 예시와 다른 점은 outer_function의 파라미터로 message를 전달 받습니다.
- outer_function('Hi')를 호출 할 때 전달된 인수 값 'Hi'를 inner_function이 기억하고 있다가 호출 될 때 출력됩니다.

> 인수를 다르게 호출

```python
def outer_function(msg):
    message = msg

    def inner_function():
        print(message)

    return inner_function


hi_closure = outer_function('Hi')
hello_closure = outer_function('Hello')

hi_closure()
hello_closure()
```

```python
Hi
Hello
```

- outer_function을 호출 할 때 인수로 'Hi'와 'Hello'로 다르게 호출합니다.
- outer_function의 결과로 각각 hi_closure와 hello_closure에 inner_function이 매핑됩니다.
- hi_closure()와 hello_closure에()를 동일하게 정의된 inner_function이 실행되지만 저장된 message 변수의 값이 다르기 때문에 각각 다른 값인 'Hi'와 'Hello'가 출력됩니다.

> nonlocal 키워드

```python
def outer_function(msg):
    message = msg

    def inner_function():
        nonlocal message
        message = message + 'Hello'
        print(message)

    return inner_function


hi_closure = outer_function('Hi')
hi_closure()
```

```python
Hello
```

- inner_function에서 message 변수의 값을 변경하여 다시 저장하고 싶은 경우 nonlocal 키워드를 사용합니다.
- nonlocal 키워드를 사용하지 않으면 message 변수는 inner_function의 지역 변수로 사용 되기 때문에 `UnboundLocalError: local variable 'message' referenced before assignment` 에러가 발생합니다.

## closure의 특징

- 외부 함수가 종료된 후에도 외부 함수의 지역 변수의 상태를 유지하여 해당 변수들에 대한 접근과 조작이 가능합니다.(message 변수의 값을 기억하고 있다가 호출 될 때 출력)
- 외부에서 직접 접근할 수 없는 변수를 포함하여 데이터를 은닉하고 캡슐화합니다.(message 변수에 직접 접근 할 수 없습니다.)
- 필요한 데이터만을 유지하므로, 메모리 사용에 있어서 효율적입니다. 큰 데이터 구조를 전역으로 유지할 필요가 없습니다.(message 변수만 유지하므로 메모리 사용에 효율적입니다.)
- 실행 시점에 동적으로 동작을 생성할 수 있습니다. 동일한 외부 함수에 대해 다양한 동작을 하는 여러 클로저를 생성할 수 있습니다.(outer_function을 호출 할 때 파라미터를 다르게 전달하여 호출)

## class를 이용한 동일 기능 구현

class를 이용하여 동일한 기능을 구현 할 수 있습니다.

```python
class OuterClass:
    def __init__(self, msg):
        self.message = msg

    def __call__(self):
        print(self.message)


hi_closure = OuterClass('Hi')
hello_closure = OuterClass('Hello')

hi_closure()
hello_closure()
```

```python
Hi
Hello
```

- class의 생성자(`__init__`)에서 message를 인스턴스 변수로 저장합니다.
- `__call__` 메서드를 정의하여 인스턴스를 함수처럼 호출 할 수 있도록 합니다.
- hi_closure()와 hello_closure()를 호출하면 `__call__` 메서드가 실행되어 message 변수의 값을 출력합니다.

위와 같이 class를 이용하여 기능을 구현 할 수 있습니다. 하지만 closure를 이용하면 **더 간단하게 구현** 할 수 있습니다.

> `__call__`

- 매직 메서드(magic method)라고도 합니다.
- `__call__` 메서드를 정의하면 인스턴스를 함수처럼 호출 할 수 있습니다.
- 위의 예시에서 OuterClass의 인스턴스인 hi_closure와 hello_closure를 함수처럼 호출 하였습니다.

## 연습 문제

- 지정된 값에서 시작 하여 호출 될 때마다 1씩 증가하는 숫자를 반환하는 closure를 만드세요.

```python
def counter(start=0):
    count = start

    def increment():
        nonlocal count
        count += 1
        return count

    return increment


c1 = counter(start=5)
print(c1())
print(c1())

c2 = counter(start=10)
print(c2())
print(c2())
```

```python
6
7
11
12
```

---

해시태그: #python #closure #call

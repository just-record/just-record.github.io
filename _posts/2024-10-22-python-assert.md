---
layout: single
title: "Python - asser"
categories: Python
tag: [Python, assert]
toc: true
toc_sticky: true
toc_label: "목차"
---

## assert란

- 조건을 테스트하는 디버깅 문: 운영환경에서는 사용 하지 않음
- 조건이 참이면 정상적으로 실행이 계속됩니다.
- 거짓이면 AssertionError가 발생

✔️ 사용 사례

- 함수 인수 또는 출력이 유효한지 확인
- 프로그램을 실행하는 동안 특정 조건이 사실인지 확인

## 문장 구조

✔️ 기본 문장

```python
assert condition
```

✔️ 메시지 포함

```python
assert condition, message
```

## 예제 코드

✔️ 조건이 참이면 정상적으로 실행

```python
x = 10
assert x > 0, "x must be positive"
```

✔️ 조건이 거짓이면 AssertionError 발생

```python
x = -10
assert x > 0, "x must be positive"
```

실행 결과

```bash
Traceback (most recent call last):
  File "assert.py", line 2, in <module>
    assert x > 0, "x must be positive"
AssertionError: x must be positive
```

## 사용 사례

✔️ 입력 유효성: 입력 인수가 사전 조건을 충족하는지 확인

```python
def divide(a, b):
    assert b != 0, "Division by zero!"
    return a / b


print(divide(10, 2))  # 5.0
print(divide(10, 0))  # AssertionError: Division by zero!
```

✔️ 출력 확인: 실행 후 사후 조건이 유지되는지 확인

```python
def calculate_discount_price(original_price, discount_rate):
    discounted_price = original_price * (1 - discount_rate)
    assert discounted_price >= 0, "할인된 가격은 음수가 될 수 없습니다"
    return discounted_price


print(calculate_discount_price(100, 0.2))  # 80.0
print(calculate_discount_price(100, 1.2))  # AssertionError: 할인된 가격은 음수가 될 수 없습니다
```

✔️ 불변성 검사: 실행 중에 조건이 계속 참인지 확인

```python
def increment(counter):
    counter += 1
    assert counter >= 0, "Counter cannot be negative"
    return counter


print(increment(2))  # 1
print(increment(-5))  # AssertionError: Counter cannot be negative
```

## 디버깅 목적

- assertion은 운영 환경에서 사용하기 위한 것이 아님 - 디버깅 목적으로 사용
- 최적화 모드(python -O)에서 Python은 모든 assertion을 무시
- 가정을 더 이상 확인할 필요가 없는 운영 환경에서 성능상의 이점을 얻을 수 있음

`sript.py`

```python
x = -10
assert x > 0, "x must be positive"
print("x is positive")
```

✔️ 일반 실행

```bash
python script.py
# 실행 결과
# Traceback (most recent call last):
#   File "script.py", line 2, in <module>
#     assert x > 0, "x must be positive"
# AssertionError: x must be positive
```

✔️ 최적화 모드 실행

```bash
python -O script.py
# 실행 결과
# x is positive
```

## Assertion과 Exception의 차이

- Assertion: 개발 중에 "절대 발생하면 안 되는" 조건을 확인하기 위한 것. 일반적인 오류 처리에 사용되지 않음.
- Exception: 런타임 중 예상되는 오류를 처리하는 데 사용(예: ValueError, TypeError).

✔️ 런타임 오류 처리를 위한 예외 사용 예시:

```python
def divide(a, b):
    if b == 0:
        raise ValueError("0으로 나눌 수 없습니다")
    return a / b


print(divide(10, 2))  # 5.0
print(divide(10, 0))  # ValueError: 0으로 나눌 수 없습니다
```

실행 결과

```bash
5.0
Traceback (most recent call last):
  File "exception.py", line 8, in <module>
    print(divide(10, 0))  # ValueError: 0으로 나눌 수 없습니다
  File "exception.py", line 3, in divide
    raise ValueError("0으로 나눌 수 없습니다")
```

✔️ 운영 환경에서는 명시적인 오류 처리

```python
class InvalidAgeError(Exception):
    pass

def check_age(age):
    if age < 0:
        raise InvalidAgeError("나이는 음수가 될 수 없습니다")


try:
    check_age(-10)
except InvalidAgeError as e:
    print(e)
```

실행 결과

```bash
나이는 음수가 될 수 없습니다
```

---

해시태그: #Python #assert #문장 구조 #예제 코드 #사용 사례 #입력 유효성 #출력 확인 #불변성 검사 #디버깅 목적 #Assertion과 Exception의 차이 #런타임 오류 처리

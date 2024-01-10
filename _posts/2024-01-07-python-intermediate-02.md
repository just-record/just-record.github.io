---
layout: single
title: "Python - 중급 02 (map, filter, reduce)"
categories: Python
tag: [Python, map, filter, reduce]
toc: true
toc_sticky: true
toc_label: "목차"
---
python의 map 함수를 처음 접하면 상당히 혼란스러울 수 있습니다. **음~~ 이게 뭐지?** 우선 map 함수를 사용하지 않고도 원하는 결과를 얻을 수 있습니다. 그럼 몰라도 되는거 아냐? 그러나 세상은 그렇게 호락호락하지 않습니다. 다른 사람이 작성 한 코드에는 map 함수가 사용되어 있을 수 있습니다. 개념을 모르면 이해 하는 데 어려움을 겪을 수 있습니다.  

추가로 간단히 말씀을 드리자면 map은 **인자에 함수를 전달** 합니다. 이를 함수형 프로그래밍이라고 합니다. python은 함수형 프로그래밍을 지원하는 언어입니다. map의 사용법을 이해하면 python의 함수형 프로그램밍을 이해하는데 도움이 될 것입니다.  

설명을 하는 것 보다 간단한 예시를 통해 이해 하는 것이 더 좋을 것 같습니다.

## 간단한 예시

항목이 [1, 2, 3, 4, 5]인 리스트 a가 있습니다. 이 리스트의 각 항목을 제곱하여 새로운 리스트를 생성하고자 합니다.  
**음~~ 이거 어디서 많이 본 것 같은데?**  
네 맞습니다. list comprehension을 사용한 예시입니다. 조금 전 말씀 드렸듯이 map 함수를 사용하지 않고도 원하는 결과를 얻을 수 있습니다.  
list comprehension은 [이전 포스팅](/python/python-01/)에서 확인 할 수 있습니다.

```python
def square(x):
    return x**2


a = [1, 2, 3, 4, 5]
result = map(square, a)
print(list(result))
```

결과를 말씀 드리면 list a의 각 항목에 square 함수의 제곱 연산을 수행했습니다.

```python
[1, 4, 9, 16, 25]
```

## 기본 문법

```python
map(function, iterable)
```

- function: iterable의 요소를 하나씩 가져와서 수행할 함수를 의미합니다.
- iterable: iterable은 반복 가능한 객체를 의미합니다. 리스트, 튜플, 문자열 등이 사용될 수 있습니다.
- map 함수의 결과는 map 객체로 반환됩니다. list 함수를 통해 리스트로 변환할 수 있습니다. map 객체는 나중에 다시 설명 드리겠습니다.

> 위의 예시를 다시 확인해 보겠습니다.

`map(square, a)`  

반복 가능한 객체인 a(`iterable`)를 반복하면서 요소를 가져와 square(`function`)라는 함수에 인수로 전달합니다. 그리고 square 함수의 반환 값을 map 객체에 저장합니다.

## map의 여러 예시

> lambda 함수

```python
a = [1, 2, 3, 4, 5]
result = map(lambda x: x**2, a)
print(list(result))
```

```bash
[1, 4, 9, 16, 25]
```

> 여러 개의 iterable을 사용하는 경우

```python
a = [1, 2, 3, 4, 5]
b = [2, 4, 6, 8, 10]
result = map(lambda x, y: x+y, a, b)
print(list(result))
```

```bash
[3, 6, 9, 12, 15]
```

> 문자열 길이 구하기

```python
a = ['apple', 'banana', 'orange']
result = map(len, a)
print(list(result))
```

```bash
[5, 6, 6]
```

> 데이터 타입 변환

```python
a = ['1', '2', '3', '4', '5']
result = map(int, a)
print(list(result))
```

```bash
[1, 2, 3, 4, 5]
```

## filter

map을 이해했다면 filter도 어렵지 않게 이해할 수 있을 것입니다. filter는 **조건에 맞는 요소만 반환**합니다. filter의 기본 문법은 다음과 같습니다.

```python
filter(function, iterable)
```

- function: iterable의 요소를 하나씩 가져와서 수행할 함수를 의미합니다.
- iterable: iterable은 반복 가능한 객체를 의미합니다. 리스트, 튜플, 문자열 등이 사용될 수 있습니다.
- filter 함수의 결과는 filter 객체로 반환됩니다. list 함수를 통해 리스트로 변환할 수 있습니다.
- function의 **반환 값은 True 또는 False를 반환하는 함수**여야 합니다.

> filter 예시

```python
def is_even(x):
    return x % 2 == 0


a = [1, 2, 3, 4, 5]
result = filter(is_even, a)
print(result)
print(list(result))
```

```bash
[2, 4]
```

## reduce

reduce는 **반복 가능한 객체의 각 요소를 지정된 함수로 처리한 후 이전 결과와 누적해서 반환**합니다. reduce의 기본 문법은 다음과 같습니다.

```python
reduce(function, iterable)
```

- function: iterable의 요소를 하나씩 가져와서 수행할 함수를 의미합니다.
- iterable: iterable은 반복 가능한 객체를 의미합니다. 리스트, 튜플, 문자열 등이 사용될 수 있습니다.
- reduce 함수의 결과는 reduce 객체로 반환됩니다. list 함수를 통해 리스트로 변환할 수 있습니다.

> reduce 예시

```python
from functools import reduce


def sum(x, y):
    return x + y


a = [1, 2, 3, 4, 5]
result = reduce(sum, a)
print(result)
```

```python
15
```

먼저 reduce는 내장 함수가 아니기 때문에 functools 모듈에서 import 해야 합니다.  
아래와 같이 수행됩니다.  

```python
1 + 2 = 3
3 + 3 = 6
6 + 4 = 10
10 + 5 = 15
```

## map 객체

map 함수의 결과는 map 객체로 반환됩니다. 여기서 반환 된 map 객체는 직접적으로 값을 볼 수 없습니다. 예시에서는 list로 변환하여 사용 했습니다. 왜 그래야 할 까요?  
map 객체는 모든 값을 한 번에 반환하지 않고 **필요할 때마다 하나씩 반환**합니다. 이를 lazy evaluation이라고 합니다. next를 사용하여 다음 값을 가져올 수 있습니다.

> map 객체 예시

```python
def square(x):
    return x**2


a = [1, 2, 3, 4, 5]
result = map(square, a)


print(next(result))
print(next(result))
print(next(result))
print(next(result))
print(next(result))
print(next(result))
```

```bash
1
4
9
16
25
Traceback (most recent call last):
  File "main.py", line 13, in <module>
    print(next(result))
StopIteration
```

next를 사용 하여 map 객체의 값을 하나씩 가져옵니다. 모든 값을 가져오고 **다음 값이 없는데도 next를 사용하면 StopIteration 에러**가 발생합니다.  
위의 예시에서 map 객체는 5개의 값을 가지고 있습니다. next를 6번 사용하면 StopIteration 에러가 발생합니다.

map 객체와 filter 객체는 lazy evaluation을 수행하는 iterator이고 reduce 객체는 iterator가 아닙니다. reduce 객체는 모든 값을 한 번에 반환합니다.  
iterator는 [Python - 중급 05 (iterator는)](/python/python-intermediate-04/)를 참조 하십시오.

## map, filter, reduce 정리

map, filter, reduce는 모두 **반복 가능한 객체의 요소를 하나씩 가져와서 수행할 함수를 적용**합니다.

- map: 반복 가능한 객체의 요소를 하나씩 가져와서 수행할 함수를 적용한 후 결과를 반환합니다.
- filter: 반복 가능한 객체의 요소를 하나씩 가져와서 수행할 함수를 적용한 후 결과가 True인 요소만 반환합니다.
- reduce: 반복 가능한 객체의 요소를 하나씩 가져와서 수행할 함수를 적용한 후 이전 결과와 누적해서 반환합니다.
- map, filter, reduce는 모두 함수를 인자로 전달합니다. 이를 **함수형 프로그래밍이**라고 합니다.
- map, filter, reduce는 map 객체, filter 객체, reduce 객체를 반환합니다. list 함수를 통해 리스트로 변환할 수 있습니다.

## 연습문제

- 다음 리스트에서 홀수만 필터링하여 새로운 리스트를 생성하세요.
  numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

```python
def is_odd(x):
    return x % 2 == 1


numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
result = filter(is_odd, numbers)
print(list(result))
```

- 다음 문자열 리스트의 모든 단어를 대문자로 변환하세요.
  words = ["hello", "world", "python", "programming"]

```python
def upper(x):
    return x.upper()


words = ["hello", "world", "python", "programming"]
result = map(upper, words)
print(list(result))
```

- 다음 리스트의 모든 숫자의 곱을 계산하세요.
  numbers = [1, 2, 3, 4, 5]

```python
from functools import reduce


def multiply(x, y):
    return x * y


numbers = [1, 2, 3, 4, 5]
result = reduce(multiply, numbers)
print(result)
```

---

해시태그: #python #map #filter #reduce #함수형프로그래밍 #lazy evaluation #iterable

---
layout: single
title: "Python - 중급 01 (list comprehension)"
categories: Python
tag: [Python, list comprehension]
toc: true
toc_sticky: true
toc_label: "목차"
---
Python은 리스트 컴프리헨션(list comprehension)이라는 기능이 있습니다. list comprehension은 음~~~  
의미를 설명 하는 것 보다 간단한 예시를 통해 이해 하는 것이 더 좋을 것 같습니다.

## 간단한 예시

항목이 [1, 2, 3, 4, 5]인 리스트 a가 있습니다. 이 리스트의 각 항목을 제곱하여 새로운 리스트를 생성하고자 합니다. 이때 list comprehension을 사용하면 아래와 같이 간단히 표현 할 수 있습니다.

```python
a = [1, 2, 3, 4, 5]
result = [i**2 for i in a]
print(result)
```

`[i**2 for i in a]` 이 부분이 list comprehension입니다. 결과 값은 아래와 같습니다.

```python
[1, 4, 9, 16, 25]
```

## 기본 문법

```python
[expression for item in iterable if condition]
```

생각의 흐름대로 항목의 순서를 변경하여 설명 하겠습니다.  

- iterable: 제일 먼저 iterable을 보시면 됩니다. 반복 가능한 객체입니다. 해당 객체를 반복하면서 item을 가져와 필요한 처리를 합니다.  반복가능(iterable)한 리스트, 튜플, 문자열 등이 사용될 수 있습니다.
- item: iterable을 반복하면서 가져온 요소를 의미합니다. 가져온 요소의 변수명이라고 생각하시면 됩니다.
- expression: expression은 item(iterable의 요소)을 이용해 수행할 연산이나 함수를 의미합니다. 위의 예시에서는 item을 제곱하는 연산을 수행했습니다.
- if: if는 선택 사항입니다. item 요소를 가져온 후 조건문을 통해 해당 요소를 사용할지 결정합니다. for문 뒤에 위치하며 생략 가능합니다.

> 위의 예시를 다시 확인해 보겠습니다.

`[i**2 for i in a]`  

반복 가능한 객체인 a(`iterable`)를 반복하면서 요소를 가져와 i(`item`)라는 변수에 저장합니다. 그리고 i를 제곱(`expression`)하여 새로운 리스트를 생성합니다. `if`는 생략되었습니다.

## if문이 포함된 예시

```python
a = [1, 2, 3, 4, 5]
result = [i**2 for i in a if i % 2 == 0]
print(result)
```

if 조건문에 의해 i가 짝수인 경우에만 제곱 연산을 수행하여 새로운 리스트에 포함합니다. 결과 값은 아래와 같습니다.

```python
[4, 16]
```

i가 짝수인 2, 4만 제곱 연산을 수행하여 새로운 리스트에 포함되었습니다.

## 다른 예시

> 문자열 예시

```python
a = 'Hello World'
result = [i.lower() for i in a if i != ' ']
print(result)
```

문자열도 반복 가능(iterable)한 객체이므로 list comprehension을 사용할 수 있습니다. 문자열의 공백을 제외한 문자를 소문자로 변경하여 새로운 리스트를 생성합니다.

```python
['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd']
```

> 튜플 예시

```python
a = (1, 2, 3, 4, 5)
result = [i**2 for i in a if i % 2 == 0]
print(result)
```

튜플도 반복 가능(iterable)한 객체이므로 list comprehension을 사용할 수 있습니다.

```python
[4, 16]
```

> range 예시

```python
result = [i**2 for i in range(1, 6) if i % 2 == 0]
print(result)
```

range도 반복 가능(iterable)한 객체이므로 list comprehension을 사용할 수 있습니다. 1부터 5까지의 숫자 중 짝수인 경우에만 제곱 연산을 수행하여 새로운 리스트에 포함합니다.

```python
[4, 16]
```

> 딕셔너리 예시

```python
a = {'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}
result = [i**2 for i in a.values() if i % 2 == 0]
print(result)
```

딕셔너리의 values()도 반복 가능(iterable)한 객체이므로 list comprehension을 사용할 수 있습니다.

```python
[4, 16]
```

## dictionary comprehension

dictionary도 comprehension을 사용할 수 있습니다. dictionary comprehension은 expression 부분에 key와 value를 가지는 식을 사용합니다.

```python
squares = {x: x*x for x in range(6)}
print(squares)
```

{key: value ...} 형태로 사용합니다.

```python
{0: 0, 1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

## set comprehension

set도 comprehension을 사용할 수 있습니다. set comprehension은 expression 부분에 value를 가지는 식을 사용합니다.

```python
squares = {x*x for x in range(6)}
print(squares)
```

{value ...} 형태로 사용합니다.

```python
{0, 1, 4, 9, 16, 25}
```

## generator expression

generator는 compexpression이라고 합니다. generator는 list와 달리 한 번에 모든 값을 메모리에 저장하지 않고 필요할 때마다 값을 생성합니다.

```python
squares = (x*x for x in range(6))
print(squares)
print(type(squares))
```

generator expression ()를 사용합니다. **()는 tuple의 기호이지만 tuple이 아닌 generator를 생성**합니다.  
geneartor는 [Python - 중급 05 (generator)](/python/python-intermediate-05/)를 참조 하십시오.

```python
<generator object <genexpr> at 0x000001B8677E4350>
<class 'generator'>
```

## tuple comprehension은 python에 존재하지 않습니다

tuple comprehension은 python에 존재하지 않습니다. tuple은 immutable(변하지 않는)한 객체이므로 comprehension을 사용할 수 없습니다. comprehension을 사용하고 싶다면 generator expression 사용하고 tuple로 변환하면 됩니다.

```python
squares = tuple(x*x for x in range(6))
print(squares)
```

generator expression으로 생성된 값을 tuple로 변환합니다.

```python
(0, 1, 4, 9, 16, 25)
```

## 연습 문제

- 0부터 20까지의 숫자 중에서 짝수만 포함하는 리스트를 만드세요

```python
result = [i for i in range(21) if i % 2 == 0]
print(result)
```

- 다음 리스트에서 길이가 5 이상인 단어들만 포함하는 새로운 리스트를 만드세요  
  words = ["apple", "banana", "cherry", "date", "elderberry", "fig", "grape"]

```python
words = ["apple", "banana", "cherry", "date", "elderberry", "fig", "grape"]
result = [i for i in words if len(i) >= 5]
print(result)
```

- 다음 리스트의 숫자들을 제곱하여, 결과가 50 이상인 숫자들만 포함하는 리스트를 만드세요  
  numbers = [5, 7, 10, 12, 15, 18, 20]

```python
numbers = [5, 7, 10, 12, 15, 18, 20]
result = [i**2 for i in numbers if i**2 >= 50]
print(result)
```

- 아래 딕셔너리에서 값이 200 이상인 항목들의 키만을 포함하는 리스트를 만드세요  
  prices = {"apple": 150, "banana": 200, "cherry": 250, "date": 300, "elderberry": 180}

```python
prices = {"apple": 150, "banana": 200, "cherry": 250, "date": 300, "elderberry": 180}
result = [key for key, value in prices.items() if value >= 200]
print(result)
```

- 주어진 문자열에서 모음(a, e, i, o, u)을 제거한 새로운 문자열을 생성하세요  
  sentence = "List comprehension is really useful!"

```python
sentence = "List comprehension is really useful!"
result = ''.join([i for i in sentence if i not in 'aeiou'])
print(result)
```

---

해시태그: #python #list_comprehension #dictionary_comprehension #set_comprehension #generator_expression

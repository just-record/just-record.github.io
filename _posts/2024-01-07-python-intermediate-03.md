---
layout: single
title: "Python - 중급 03 (lambda)"
categories: Python
tag: [Python, lambda]
toc: true
toc_sticky: true
toc_label: "목차"
---
일반적으로 코딩 할 때 함수를 많이 사용합니다. def로 함수를 정의하고 필요할 때 호출을 합니다. 그런데 만약 **한번만 사용하면 되는 함수**가 있다고 가정해 보겠습니다. 이런 경우에 def로 함수를 정의 하지 않고 바로 사용 할 수 있다면 편리 하겠죠. python은 이러한 기능을 제공하고 있으며 이를 lambda 함수라고 합니다.

## 간단한 예시

함수 선언

```python
def add(a, b):
    return a + b


print(add(1, 2))
```

```python
3
```

lambda 함수

```python
print((lambda a, b: a + b)(1, 2))
```

```python
3
```

## 기본 문법

```python
lambda arguments: expression
```

- lambda: lambda 키워드 - 이건 lambda 함수라는 의미입니다.
- arguments: 함수에 전달되는 파라미터를 의미합니다.
- expression: 함수의 반환 값에 해당하는 식을 의미합니다. - 즉 함수의 기능을 구현하는 부분입니다.

> 위의 예시를 다시 확인해 보겠습니다.

`(lambda a, b: a + b)(1, 2)`

우리는 def를 통해 add라는 이름으로 함수를 정의 했습니다. add 함수를 호출하기 위해서는 add(1, 2)와 같이 사용합니다. 우선은 add(1, 2)에서 add 자리에 `(lambda a, b: a + b)`를 그대로 넣는다고 생각 하시면 됩니다.  

`a, b`는 함수의 파라미터(arguments)를 의미하고 `a + b`는 함수의 반환 값(expression)을 의미합니다. `(1, 2)`는 실제 함수에 전달되는 값 의미합니다.

## Lambda 함수의 특징

- 이름이 없습니다. 함수 자체를 변수에 할당하거나 다른 함수의 인수로 바로 사용 할 수 있습니다.
- 단 할 줄로 작성 할 수 있어 코드를 간결 하게 만들지만 다른 의미로는 여러 문장을 사용할 수 없다는 의미입니다.
- return 키워드를 사용할 수 없습니다. 따라서 lambda 함수의 표현식은 식(expression)이어야 합니다. 식은 값을 반환하는 문장을 의미합니다.
- 디버깅이 어려운 단점이 있습니다.
- map, filter 등의 함수와 함께 사용하여 코드를 간결하게 만들 수 있습니다.

## lambda 함수의 여러 예시

> 변수에 할당

```python
add = lambda a, b: a + b
print(add(1, 2))
```

```python
3
```

> map 함수와 함께 사용

```python
a = [1, 2, 3, 4, 5]
result = map(lambda x: x**2, a)
print(list(result))
```

```python
[1, 4, 9, 16, 25]
```

> filter 함수와 함께 사용

```python
a = [1, 2, 3, 4, 5]
result = filter(lambda x: x % 2 == 0, a)
print(list(result))
```

```python
[2, 4]
```

> reduce 함수와 함께 사용

```python
from functools import reduce

a = [1, 2, 3, 4, 5]
result = reduce(lambda x, y: x + y, a)
print(result)
```

```python
15
```

> sorted 함수와 함께 사용

```python
tuples = [(1, 'd'), (2, 'b'), (4, 'a'), (3, 'c')]
sorted_tuples = sorted(tuples, key=lambda x: x[1])
print(sorted_tuples)
```

```python
[(4, 'a'), (2, 'b'), (3, 'c'), (1, 'd')]
```

## 연습 문제

- 두 숫자의 곱을 반환하는 lambda 함수를 작성하세요.

```python
multiply = lambda a, b: a * b
print(multiply(1, 2))
```

```python
2
```

- 주어진 문자열 리스트를 길이에 따라 정렬하는 lambda 함수를 사용하세요.
  words = ["Python", "is", "awesome", "language"]

```python
words = ["Python", "is", "awesome", "language"]
sorted_words = sorted(words, key=lambda x: len(x))
print(sorted_words)
```

```python
['is', 'Python', 'language', 'awesome']
```

- 다음 리스트에서 짝수만 필터링하는 lambda 함수를 사용하세요.
  numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_numbers = filter(lambda x: x % 2 == 0, numbers)
print(list(even_numbers))
```

```python
[2, 4, 6, 8, 10]
```

- 주어진 숫자 리스트의 각 요소를 제곱하는 lambda 함수를 사용하세요.
  numbers = [1, 2, 3, 4, 5]

```python
numbers = [1, 2, 3, 4, 5]
squared_numbers = map(lambda x: x**2, numbers)
print(list(squared_numbers))
```

```python
[1, 4, 9, 16, 25]
```

---

해시태그: #python #lambda #map #filter #reduce #sorted

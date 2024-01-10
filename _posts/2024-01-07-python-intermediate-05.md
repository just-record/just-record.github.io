---
layout: single
title: "Python - 중급 05 (generator)"
categories: Python
tag: [Python, generator]
toc: true
toc_sticky: true
toc_label: "목차"
---
generator는 한 마디로 **iterator를 생성해 주는 함수**입니다. iterator와 동일하게 next() 함수를 사용하여 데이터를 순차적으로 호출 할 수 있습니다. generator를 알기 위해 먼저 yield 키워드에 대해 살펴보겠습니다.  
iterator는 [Python - 중급 04 (iterator)](/python/python-intermediate-04/)를 참조 하십시오.

## yield 키워드

yield는 함수에서 값을 반환(return)하고, 함수의 실행을 그 지점에서 일시적으로 중지합니다. 다음 번에 함수가 호출되면, yield 다음의 명령문부터 실행이 재개됩니다. 예시를 통해 이해해 보겠습니다.

```python
def test():
    print('A')
    yield 1
    print('B')
    yield 2
    print('C')
    yield 3


t = test()
print(next(t))
print('======')
print(next(t))
print('======')
print(next(t))
print('======')
print(next(t))
```

```python
A
1
======
B
2
======
C
3
======
Traceback (most recent call last):
  File "test.py", line 16, in <module>
    print(next(t))
```

- 처음 next(t)를 호출하면 print('A')가 실행되고 yield 1에서 일시적으로 중지됩니다.
- 다음 번 next(t)를 호출하면 yield 1 다음의 명령문인 print('B')가 실행되고 yield 2에서 일시적으로 중지됩니다.
- 마지막으로 next(t)를 호출하면 yield 2 다음의 명령문인 print('C')가 실행되고 yield 3에서 일시적으로 중지됩니다.
- 이후 next(t)를 호출하면 StopIteration 예외가 발생합니다.

## generator 함수

위의 소스에서 `t`의 타입을 확인해 보겠습니다.

```python
def test():
    print('A')
    yield 1
    print('B')
    yield 2
    print('C')
    yield 3


t = test()
print(type(test))
print(type(t))
```

```python
<class 'function'>
<class 'generator'>
```

- test 타입은 function입니다.
- test 함수를 호출하여 반환된 t의 타입은 generator입니다.

generator는 **yield를 사용한 함수**를 호출하여 반환된 객체입니다.

## generator의 특징

- 모든 데이터를 메모리에 미리 로드하지 않습니다. 필요할 때마다 데이터를 하나씩 '생성(yield)'합니다. 이로 인해 큰 데이터셋을 처리할 때 메모리를 절약할 수 있습니다.
- Lazy Evaluation(지연 실행): 값이 필요할 때까지 계산을 미룹니다. 즉, next() 함수가 호출될 때 다음 값을 생성합니다.
- 실행 중인 상태를 유지합니다. 함수의 로컬 변수와 실행 상태는 yield가 발생할 때마다 보존되고, 다음 값이 요청될 때까지 해당 상태가 유지됩니다.
- 더 이상 반환할 값이 없을 때 자동으로 StopIteration 예외를 발생시킵니다. 이는 반복이 끝났음을 나타냅니다.
- 간결한 코드: 복잡한 iterator를 만드는 대신, 간단하게 yield 키워드를 사용하여 값들을 생성할 수 있어 코드가 더 간결해집니다.

## generator의 예시

> 간단한 카운트다운

```python
def countdown(n):
    while n > 0:
        yield n
        n -= 1

for num in countdown(5):
    print(num)
```

```python
5
4
3
2
1
```

> 무한한 카운트다운

```python
def infinite_sequence():
    num = 0
    while True:
        yield num
        num += 1

gen = infinite_sequence()
print(next(gen))
print(next(gen))
```

```python
0
1
```

## generator expression

generator expression은 list, set, dictionary comprehension과 유사하게 사용할 수 있습니다. generator 표현식은 ()를 사용합니다. generator 표현식은 list, set, dictionary comprehension과 다르게 **한 번에 모든 값을 메모리에 저장하지 않고 필요할 때마다 값을 생성**합니다. comprehension은 [Python - 중급 01 (list comprehension)](/python/python-intermediate-01/)를 참조 하십시오.

> generator 표현식 예시

```python
a = [1, 2, 3, 4, 5]
result = (x**2 for x in a)
print(result)
print(type(result))
```

```python
<generator object <genexpr> at 0x7f8f6c1b5d90>
<class 'generator'>
```

## iterator와 generator의 차이점은?

- iterator는 `__next__()` 메서드를 구현해야 합니다.
- generator는 yield 키워드를 사용한 함수를 구현해야 합니다.
- iterator는 사용자 정의 클래스로 복잡한 반복 동작을 구현할 수 있습니다.
- generator는 간결한 코드로 간단한 데이터 시퀀스를 생성하는 데 사용됩니다.

## 연습 문제

- 1부터 100까지의 숫자 중 3의 배수만 출력하는 generator를 작성하십시오.

```python
def gen():
    for i in range(1, 101):
        if i % 3 == 0:
            yield i

for i in gen():
    print(i)
```

- 1부터 100까지의 숫자 중 3의 배수만 출력하는 generator expression을 작성하십시오.

```python
result = (i for i in range(1, 101) if i % 3 == 0)

for i in result:
    print(i)
```

---

해시태그: #python #generator #yield #iterator #comprehension

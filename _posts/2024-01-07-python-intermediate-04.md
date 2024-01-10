---
layout: single
title: "Python - 중급 04 (iterator)"
categories: Python
tag: [Python, iterator]
toc: true
toc_sticky: true
toc_label: "목차"
---
python의 iterator는 뭘까요? 한 마디로 **next()를 사용 하여 다음 요소**를 가져올 수 있는 객체를 의미합니다. 이해가 안되시죠? 하나씩 알아보도록 하겠습니다.

## 먼저, 반복 가능(iterable)하다는 것은?

iterable 하다는 것은 여러 값이 그룹화 되어 있는 데이터 구조에서 **요소를 하나씩 꺼내어 사용**할 수 있다는 의미입니다. 이러한 데이터 구조는 list, tuple, dictionary, set 등이 있습니다. for문으로 값을 읽어 올 수 있으면 iterable 하다고 할 수 있습니다.

```python
a = [1, 2, 3, 4, 5]
# a = (1, 2, 3, 4, 5)
# a = {1, 2, 3, 4, 5}
# a = {'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}

for i in a:
    print(i)
```

## 그럼, iterator는 무엇인가?

제일 처음 iteator를 말씀 드릴 때 next 함수를 사용 한다고 말씀 드렸습니다. 다른 각도로 **next 함수로 다음 요소를 가져 올 수 없으면 iterator가 아닙니다.** 그럼 위의 iterable한 객체를 next를 사용 하여 호출 해보겠습니다.

```python
a = [1, 2, 3, 4, 5]

print(next(a))
```

아래와 같은 에러가 발생합니다.

```python
TypeError: 'list' object is not an iterator
```

음~~ list는 iterator가 아니라고 합니다. list는 iterable한 객체이지만 iterator는 아니라는 의미입니다. tuple, dictionary, set도 마찬가지 입니다. 그럼 iterator는 어떻게 만들 수 있을까요?

> iter()로 생성 할 수 있습니다.

iterabla한 객체를 iter()에 전달하면 iterator를 생성 할 수 있습니다.

```python
a = [1, 2, 3, 4, 5]
it = iter(a)

print(it)

print(next(it))
print(next(it))
print(next(it))
print(next(it))
print(next(it))

print(next(it))
```

위의 코드를 실행하면 아래와 같은 결과가 출력됩니다.

```python
<list_iterator object at 0x7f8f6c1b5d90>
1
2
3
4
5
Traceback (most recent call last):
  File "main.py", line 11, in <module>
    print(next(it))
```

- iter()로 형변환 한 it을 출력 하면 list_iterator object가 출력됩니다.
- next()로 값을 하나씩 가져올 수 있습니다.
- 다음 요소가 없는데도 next()를 호출하면 StopIteration 에러가 발생합니다.

## iterator의 특징

- next() 함수를 사용하여 다음 요소를 가져올 수 있습니다.
- 다음 요소가 없는데도 next()를 호출하면 StopIteration 에러가 발생합니다.
- iterator는 모든 값을 한 번에 반환하지 않고 필요할 때마다 하나씩 반환합니다. 이를 lazy evaluation이라고 합니다.
- iterator는 모든 값을 메모리에 저장하지 않고 필요할 때마다 값을 생성합니다.
- iterator는 한 번 사용하면 다시 사용 할 수 없습니다.
- iterator는 for문에서 사용할 수 있습니다.

## iter()를 사용하지 않고 iterator를 생성 할 수 있을까요?

직접 iterator를 생성 할 수 있습니다. iter()를 통해 iterator를 반환하고 next()를 통해 다음 요소를 가져오도록 구현하면 됩니다.  
class를 사용하여 iterator를 생성해 보겠습니다.

```python
class Countdown:
    def __init__(self, start):
        self.current = start  # 초기화 때 입력 받은 값을 현재 값으로 설정

    def __iter__(self):
        return self       # 자기 자신을 반환 - Countdown 객체가 iterator가 됨

    def __next__(self):   # iterator의 다음 요소를 반환
        if self.current <= 0: # 현재 값이 0보다 작거나 같으면(다음 요소가 없을 때를 정의)
            raise StopIteration # StopIteration 에러 발생
        else:
            num = self.current  # 현재 값을 반환
            self.current -= 1   # 다음 요소를 위해 현재 값을 1 감소
            return num

it_counter = Countdown(3)  # 초기값을 3으로 Countdown 객체 생성
print(it_counter)

# next 함수 호출
print(next(it_counter))
print(next(it_counter))
print(next(it_counter))

# StopIteration 에러 발생
print(next(it_counter))
```

위의 코드를 실행하면 아래와 같은 결과가 출력됩니다.

```python
<__main__.Countdown object at 0x7f6f93ad7dc0>
3
2
1
Traceback (most recent call last):
  File "main.py", line 25, in <module>
    print(next(it_counter))
  File "main.py", line 9, in __next__
    raise StopIteration
StopIteration
```

## 매직 메소드(magic method) 또는 스페셜 메소드(special method)

매직 메소드는 클래스 내부에서 사용되며 `__`로 시작하고 `__`로 끝납니다.

> `__iter__`

- 객체가 "반복 가능(iterable)"임을 나타냅니다.
- 반복을 위한 iterator 객체를 반환해야 합니다.
- 이 메서드가 반환하는 객체는 `__next__` 메서드를 구현해야 하며, 이는 보통 iterator 객체 자신입니다.

> `__next__`

- iterator 객체의 핵심 부분으로, 반복에서 다음 요소를 반환합니다.
- 이 메서드가 호출될 때마다, iterator는 컬렉션의 다음 요소를 반환해야 합니다.
- 더 이상 반환할 요소가 없을 때 StopIteration 예외를 발생시켜야 합니다.
- for 루프와 같은 반복 구조는 StopIteration 예외를 만나면 반복을 종료합니다.

## 연습 문제

- 0부터 주어진 n까지의 숫자를 순차적으로 반환하는 iterator를 만드세요. 예를 들어, n=5일 때 iterator는 0, 1, 2, 3, 4, 5를 순차적으로 반환해야 합니다.

```python
class Countdown:
    def __init__(self, start):
        self.current = start  # 초기화 때 입력 받은 값을 현재 값으로 설정

    def __iter__(self):
        return self       # 자기 자신을 반환 - Countdown 객체가 iterator가 됨

    def __next__(self):   # iterator의 다음 요소를 반환
        if self.current <= 0: # 현재 값이 0보다 작거나 같으면(다음 요소가 없을 때를 정의)
            raise StopIteration # StopIteration 에러 발생
        else:
            num = self.current  # 현재 값을 반환
            self.current -= 1   # 다음 요소를 위해 현재 값을 1 감소
            return num

for i in Countdown(5):
    print(i)
```

아래는 generator를 사용하여 구현한 예시입니다.  
generator는 [Python - 중급 05 (generator)](/python/python-intermediate-05/)를 참조 하십시오.

```python
def countdown(n):
    i = n
    while i >= 0:
        yield i
        i -= 1

for i in countdown(5):
    print(i)
```

---

해시태그: #python #iterator #iterable #next #lazy_evaluation #StopIteration #iter #next #매직메소드 #스페셜메소드 #magicmethod #specialmethod

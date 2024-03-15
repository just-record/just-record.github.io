---
layout: single
title: "Python - 패킹(Packing), 언패킹(Unpacking)"
categories: Python
tag: [Python, Packing, Unpacking]
toc: true
toc_sticky: true
toc_label: "목차"
---

## 패킹(Packing)

여러 개의 값을 하나의 자료구조(리스트, 튜플 등)로 묶는 것을 의미.

```python
### 리스트 패킹
# 3개의 값을 리스트에 담아 my_list라는 변수에 할당
my_list = [1, 2, 3]

### 딕셔너리 패킹
# 3개의 값을 딕셔너리에 담아 my_dict라는 변수에 할당
my_dict = {'a': 1, 'b': 2, 'c': 3}

### 튜플 패킹
# 3개의 값을 튜플에 담아 my_tuple이라는 변수에 할당
my_tuple = (1, 2, 3)

# 튜플을 () 생략하고 사용 가능
my_tuple = 1, 2, 3
print(my_tuple) # (1, 2, 3)
```

아래 코드는?

```python
a, b, c = 1, 2, 3  # 패킹?
# (a, b, c) = (1, 2, 3) # 언패킹?

print(a, b, c)
```

## 언패킹(Unpacking)

하나의 컬렉션에 담긴 여러 데이터를 각각 분리하여 변수에 할당.

```python
### 리스트 언패킹
# 리스트의 각 요소를 변수에 할당

my_list = [1, 2, 3]
a, b, c = my_list

print(a) # 1
print(b) # 2
print(c) # 3

### 튜플 언패킹
# 튜플의 각 요소를 변수에 할당

my_tuple = (1, 2, 3)
a, b, c = my_tuple

print(a) # 1
print(b) # 2
print(c) # 3

### 딕셔너리 언패킹
# 딕셔너리의 키와 값을 변수에 할당

my_dict = {'a': 1, 'b': 2, 'c': 3}
a, b, c = my_dict.values() # 값들을 개별 변수로 언패킹

print(a) # 1
print(b) # 2
print(c) # 3
```

## 패킹의 확장

### '*'를 사용한 확장 패킹

함수 인자 패킹

```python
def my_function(a, b, *args):
    print(a, b, args)

my_function(1, 2, 3, 4, 5)  # 1 2 (3, 4, 5) 출력

# *args가 아닌 다른 변수명도 사용 가능
def my_function2(a, b, *args2):
    print(a, b, args2)

my_function2(6, 7, 8, 9, 10) # 6 7 (8, 9, 10) 출력
```

함수 리턴값 패킹

```python
def my_function():
    return 1, 2, 3

result = my_function()

print(result)  # 1 2 3 출력
```

### '**'를 사용한 확장 패킹

함수 인자 패킹

```python
def my_function(a, b, **kwargs):
    print(a, b, kwargs)

my_function(1, 2, c=3, d=4)  # 1 2 {'c': 3, 'd': 4} 출력
```

## 언패킹의 확장

### '*'를 사용한 확장 언패킹

변수 할당 패킹

```python
x, y, *z = [1, 2, 3, 4, 5]
print(x, y, z)  # 1 2 [3, 4, 5] 출력

x, *y, z = [1, 2, 3, 4, 5]
print(x, y, z)  # 1 [2, 3, 4] 5 출력
```

함수 인자 언패킹

```python
def my_function(a, b, c):
    return a + b + c

number = [1, 2, 3]
result = my_function(*number)
print(result)  # 6 출력
```

기타 다른 예시

```python
list1 = [1, 2, 3]
list2 = [4, 5, 6]
combined_list = [*list1, *list2]
print(combined_list) # [1, 2, 3, 4, 5, 6]

*a, b = "Hello"
print(a)  # ['H', 'e', 'l', 'l']
print(b)  # 'o'
```

### '**'를 사용한 확장 언패킹

'**'를 사용한 언패킹은 주로 딕셔너리에서 사용되며 딕셔너리의 키-값 쌍을 다른 딕셔너리나 함수의 인자로 전달할 때 사용

함수 인자 언패킹

```python
def introduce_yourself(name, age, country):
    print(f"My name is {name}, I'm {age} years old, and I'm from {country}.")

info = {"name": "John", "age": 30, "country": "USA"}
introduce_yourself(**info)
# introduce_yourself(name="John", age=30, country="USA")
# 출력: My name is John, I'm 30 years old, and I'm from USA.
```

딕셔러리 합치기

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
combined_dict = {**dict1, **dict2}
print(combined_dict)
# 출력: {'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

## '*'와 '**'를 같이 사용하기

패킹

```python
def function_with_packing(*args, **kwargs):
    print("Positional arguments:", args)
    print("Keyword arguments:", kwargs)

function_with_packing(1, 2, 3, a=4, b=5)
# 출력:
# Positional arguments: (1, 2, 3)
# Keyword arguments: {'a': 4, 'b': 5}
```

언패킹

```python
def another_function(a, b, c, d, e):
    print(a, b, c, d, e)

# 리스트와 딕셔너리 정의
args_list = [2, 3]
kwargs_dict = {"d": 4, "e": 5}

# 함수 호출시 *와 ** 사용하여 언패킹
another_function(1, *args_list, **kwargs_dict)
# 출력: 1 2 3 4 5
```

### 참고

args, kwargs의 위치 변경 -> 에러 발생

```python
def function_with_packing(**kwargs, *args):
    print("Positional arguments:", args)
    print("Keyword arguments:", kwargs)

function_with_packing(a=4, b=5, 1, 2, 3)
# SyntaxError: invalid syntax
```

함수에 전달된 인자들 중에서 어디까지가 위치 인자이고 어디서부터 키워드 인자인지를 명확히 구분이 되어야 하기 때문에 args가 먼저 나오고 kwargs가 나와야 합니다.

---

해시태그: #Python #Packing #Unpacking #*args #**kwargs

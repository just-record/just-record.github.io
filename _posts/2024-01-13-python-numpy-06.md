---
layout: single
title: "Python - numpy 06 (통계함수, axis)"
categories: Python-Data_analysis
tag: [Python, numpy]
toc: true
toc_sticky: true
toc_label: "목차"
---
numpy의 통계 함수와 **axis**에 대해 알아보겠습니다. 특히 axis는 데이터 분석, 머신러닝을 하실 분은 꼭 알아야 하는 개념입니다.

## 통계 함수

numpy는 다양한 통계 함수를 제공합니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr.sum())
print(arr.mean())
print(arr.max())
print(arr.min())
print(arr.std())
print(arr.var())
print(arr.argmin())
print(arr.argmax())
```

- `arr.sum()`: arr의 모든 원소의 합을 구합니다.
- `arr.mean()`: arr의 모든 원소의 평균을 구합니다.
- `arr.max()`: arr의 모든 원소의 최댓값을 구합니다.
- `arr.min()`: arr의 모든 원소의 최솟값을 구합니다.
- `arr.std()`: arr의 모든 원소의 표준편차를 구합니다.
- `arr.var()`: arr의 모든 원소의 분산을 구합니다.
- `arr.argmin()`: arr의 모든 원소 중 최솟값의 인덱스를 구합니다.
- `arr.argmax()`: arr의 모든 원소 중 최댓값의 인덱스를 구합니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
45
4.5
9
0
2.8722813232690143
8.25
0
9
```

> 다른 방법(numpy 모듈의 함수)

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(np.sum(arr))
print(np.mean(arr))
print(np.max(arr))
print(np.min(arr))
print(np.median(arr))
print(np.std(arr))
print(np.var(arr))
print(np.argmin(arr))
print(np.argmax(arr))
```

- `np.sum(arr)`: arr의 모든 원소의 합을 구합니다.
- `np.mean(arr)`: arr의 모든 원소의 평균을 구합니다.
- `np.max(arr)`: arr의 모든 원소의 최댓값을 구합니다.
- `np.min(arr)`: arr의 모든 원소의 최솟값을 구합니다.
- `np.median(arr)`: arr의 모든 원소의 중앙값을 구합니다.
- `np.std(arr)`: arr의 모든 원소의 표준편차를 구합니다.
- `np.var(arr)`: arr의 모든 원소의 분산을 구합니다.
- `np.argmin(arr)`: arr의 모든 원소 중 최솟값의 인덱스를 구합니다.
- `np.argmax(arr)`: arr의 모든 원소 중 최댓값의 인덱스를 구합니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
45
4.5
9
0
4.5
2.8722813232690143
8.25
0
9
```

## 2차원 배열의 sum

2차원 배열의 sum을 구해보겠습니다.

```python
import numpy as np

arr = np.arange(10).reshape(2, 5)

print(arr)
print(arr.sum())
```

```shell
[[0 1 2 3 4]
 [5 6 7 8 9]]
45
```

모두 더한 합인 45가 나왔습니다.

### 만약 가로 합이나 세로 합을 구하고 싶다면?

```python
import numpy as np

arr = np.arange(10).reshape(2, 5)

print(arr)
print()
print(arr.sum(axis=0))
print(arr.sum(axis=1))
```

- `arr.sum(axis=0)`: 0축의 합을 구합니다.
- `arr.sum(axis=1)`: 1축의 합을 구합니다.

```shell
[[0 1 2 3 4]
 [5 6 7 8 9]]
[ 5  7  9 11 13]
[10 35]
```

**axis는 축**을 의미합니다. axis=0은 가로 축(행), axis=1은 세로 축(열)을 의미합니다. 이해 하기 쉽도록 가로 축(행)으로 표현 했습니다. 가로축(행)의 합은 **가로축 방향**으로 연산합니다. ([**0+5**, 1+6, 2+7, 3+8, 4+9]) 세로축(열)의 합은 **세로축 방향**으로 연산합니다. ([**0+1+2+3+4**, 5+6+7+8+9])

> axis를 2로 설정하면?

```python
import numpy as np

arr = np.arange(10).reshape(2, 5)

print(arr)
print()
print(arr.sum(axis=2))
```

```shell
[[0 1 2 3 4]
 [5 6 7 8 9]]

Traceback (most recent call last):
  File "C:/Users/82109/PycharmProjects/pythonProject/venv/Scripts/2024-01-13-python-numpy-06.py", line 7, in <module>
    print(arr.sum(axis=2))
  File "<__array_function__ internals>", line 5, in sum
  File "C:\Users\82109\PycharmProjects\pythonProject\venv\lib\site-packages\numpy\core\fromnumeric.py", line 2257, in sum
    return _wrapreduction(a, np.add, 'sum', axis, dtype, out, keepdims=keepdims,
  File "C:\Users\82109\PycharmProjects\pythonProject\venv\lib\site-packages\numpy\core\fromnumeric.py", line 87, in _wrapreduction
    return ufunc.reduce(obj, axis, dtype, out, **passkwargs)
ValueError: 'axis' entry is out of bounds
```

axis를 2로 설정하면 에러가 발생합니다. **2차원 배열이므로 axis는 2(0, 1)개**가 존재합니다.

## 3차원 배열의 sum

3차원 배열의 sum을 구해보겠습니다.

```python
import numpy as np

arr = np.arange(18).reshape(2, 3, 3)

print(arr)
print()
print(arr.sum(axis=0))
print(arr.sum(axis=1))
print(arr.sum(axis=2))
```

```shell
[[[ 0  1  2]
  [ 3  4  5]
  [ 6  7  8]]

 [[ 9 10 11]
  [12 13 14]
  [15 16 17]]]

[[ 9 11 13]
 [15 17 19]
 [21 23 25]]

[[ 9 12 15]
 [36 39 42]]

[[ 3 12 21]
 [30 39 48]]
```

---

해시태그: #python #numpy #통계함수 #sum

---
layout: single
title: "Python - numpy 05 (연산, broadcasting)"
categories: Python-Data_analysis
tag: [Python, numpy]
toc: true
toc_sticky: true
toc_label: "목차"
---
지난 포스팅에서 numpy의 ndarray의 생성 방법과 원소를 가져오는 방법을 알아보았습니다. 이번에는 ndarray의 **원소 간의 연산**과 shape(모양)이 달라도 연산 할 수 있는 **broadcasting**에 대해 알아보겠습니다.

## 연산

ndarray의 원소 간의 연산은 기본적으로 **요소별(element-wise) 연산**입니다. 요소별 연산이란 같은 위치에 있는 원소끼리 연산을 하는 것을 말합니다.

```python
import numpy as np

arr1 = np.arange(10)
arr2 = np.arange(10, 20)

print(arr1)
print(arr2)

print(arr1 + arr2)
print(arr1 - arr2)
print(arr1 * arr2)
print(arr1 / arr2)
```

- `arr1 + arr2`: arr1과 arr2의 같은 위치에 있는 원소끼리 더합니다.
- `arr1 - arr2`: arr1과 arr2의 같은 위치에 있는 원소끼리 뺍니다.
- `arr1 * arr2`: arr1과 arr2의 같은 위치에 있는 원소끼리 곱합니다.
- `arr1 / arr2`: arr1과 arr2의 같은 위치에 있는 원소끼리 나눕니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
[10 11 12 13 14 15 16 17 18 19]
[10 12 14 16 18 20 22 24 26 28]
[-10 -10 -10 -10 -10 -10 -10 -10 -10 -10]
[  0  11  24  39  56  75  96 119 144 171]
[0.         0.09090909 0.16666667 0.23076923 0.28571429 0.33333333 0.375      0.41176471 0.44444444 0.47368421]
```

> 함수를 이용한 연산

```python
import numpy as np

arr1 = np.arange(10)
arr2 = np.arange(10, 20)

print(arr1)
print(arr2)

print(np.add(arr1, arr2))
print(np.subtract(arr1, arr2))
print(np.multiply(arr1, arr2))
print(np.divide(arr1, arr2))
```

- `np.add(arr1, arr2)`: arr1과 arr2의 같은 위치에 있는 원소끼리 더합니다.
- `np.subtract(arr1, arr2)`: arr1과 arr2의 같은 위치에 있는 원소끼리 뺍니다.
- `np.multiply(arr1, arr2)`: arr1과 arr2의 같은 위치에 있는 원소끼리 곱합니다.
- `np.divide(arr1, arr2)`: arr1과 arr2의 같은 위치에 있는 원소끼리 나눕니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
[10 11 12 13 14 15 16 17 18 19]
[10 12 14 16 18 20 22 24 26 28]
[-10 -10 -10 -10 -10 -10 -10 -10 -10 -10]
[  0  11  24  39  56  75  96 119 144 171]
[0.         0.09090909 0.16666667 0.23076923 0.28571429 0.33333333 0.375      0.41176471 0.44444444 0.47368421]
```

> 주의 할 점

연산을 할 때 **shape(모양)이 같아야** 합니다. shape이 다르면 에러가 발생합니다.

```python
import numpy as np

arr1 = np.arange(10)
arr2 = np.arange(5)

print(arr1)
print(arr2)

print(arr1 + arr2)
```

- `arr1 + arr2`: arr1과 arr2의 shape이 다르기 때문에 에러가 발생합니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
[0 1 2 3 4]
Traceback (most recent call last):
  File "main.py", line 8, in <module>
    print(arr1 + arr2)
ValueError: operands could not be broadcast together with shapes (10,) (5,)
```

## broadcasting(브로드캐스팅)

shape이 다른 배열끼리 연산을 할 때, **shape을 맞춰주는 것**을 브로드캐스팅이라고 합니다. 브로드캐스팅은 shape이 다른 배열끼리 연산을 할 때, shape을 맞춰주는 것을 말합니다. 다만 **조건이 있습니다.**  아래 그림을 보면서 브로드캐스팅이 가능한 조건을 알아보겠습니다.

![브로드캐스팅](http://www.astroml.org/_images/fig_broadcast_visual_1.png)  
참조: [http://www.astroml.org/book_figures/appendix/fig_broadcast_visual.html](http://www.astroml.org/book_figures/appendix/fig_broadcast_visual.html)

- 두 배열의 차원이 같거나, 한쪽이 1이어야 합니다.
- 두 배열의 차원이 같지 않다면, 차원이 1인 배열의 shape을 다른 배열의 shape에 맞춰줍니다.
- 두 배열의 shape이 같지 않다면, shape이 같지 않은 배열의 shape을 다른 배열의 shape에 맞춰줍니다.
- 두 배열의 shape이 같지 않고, 차원이 1이 아니라면 에러가 발생합니다.

무슨 말인지 어렵습니다. 그나마 그림이 낫네요. 예시를 그림을 보면서 알아보겠습니다.

> (3, ) 과 상수

```python
import numpy as np

arr1 = np.arange(3)
arr2 = 5

print(arr1)
print(arr2)

print(arr1 + arr2)
```

- `arr1 + arr2`: arr1의 shape은 (3, )이고, arr2의 shape은 ()이기 때문에 브로드캐스팅이 가능합니다.

```shell
[0 1 2]
5
[5 6 7]
```

> (3, 3) 과 (3, )

```python
import numpy as np

arr1 = np.arange(9).reshape(3, 3)
arr2 = np.arange(3)

print(arr1)
print(arr2)

print(arr1 + arr2)
```

- `arr1 + arr2`: arr1의 shape은 (3, 3)이고, arr2의 shape은 (3, )이기 때문에 브로드캐스팅이 가능합니다.

```shell
[[0 1 2]
 [3 4 5]
 [6 7 8]]
[0 1 2]
[[ 0  2  4]
 [ 3  5  7]
 [ 6  8 10]]
```

> (3, 1) 과 (3, )

```python
import numpy as np

arr1 = np.arange(3).reshape(3, 1)
arr2 = np.arange(3)

print(arr1)
print(arr2)

print(arr1 + arr2)
```

- `arr1 + arr2`: arr1의 shape은 (3, 1)이고, arr2의 shape은 (3, )이기 때문에 브로드캐스팅이 가능합니다.

```shell
[[0]
 [1]
 [2]]
[0 1 2]
[[0 1 2]
 [1 2 3]
 [2 3 4]]
```

> (3, 1) 과 (1, 3) - 실질적으로는 (3, ) 과 (3, ) 과 같습니다.

```python
import numpy as np

arr1 = np.arange(3).reshape(3, 1)
arr2 = np.arange(3).reshape(1, 3)

print(arr1)
print(arr2)

print(arr1 + arr2)
```

- `arr1 + arr2`: arr1의 shape은 (3, 1)이고, arr2의 shape은 (1, 3)이기 때문에 브로드캐스팅이 가능합니다.

```shell
[[0]
 [1]
 [2]]
[[0 1 2]]
[[0 1 2]
 [1 2 3]
 [2 3 4]]
```

> (3, 1) 과 (3, 2) - 오류

```python
import numpy as np

arr1 = np.arange(3).reshape(3, 1)
arr2 = np.arange(6).reshape(3, 2)

print(arr1)
print(arr2)

print(arr1 + arr2)
```

- `arr1 + arr2`: arr1의 shape은 (3, 1)이고, arr2의 shape은 (3, 2)이기 때문에 브로드캐스팅이 불가능합니다.

```shell
[[0]
 [1]
 [2]]
[[0 1]
 [2 3]
 [4 5]]
Traceback (most recent call last):
  File "main.py", line 8, in <module>
    print(arr1 + arr2)
ValueError: operands could not be broadcast together with shapes (3,1) (3,2)
```

---

해시태그: #python #numpy #연산 #broadcasting

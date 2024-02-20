---
layout: single
title: "Python - numpy 03 (reshape)"
categories: Python-Data_analysis
tag: [Python, numpy]
toc: true
toc_sticky: true
toc_label: "목차"
---
지난 포스팅에서 numpy의 ndarray가 뭔지, 어떻게 생성하는지 알아보았습니다. 이번에는 ndarray의 **shape(모양)을 변경**하는 reshape에 대해 알아보겠습니다.

## reshape

reshape는 ndarray의 shape(모양)을 변경합니다. reshape는 ndarray의 메소드입니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr.shape)
print(arr.reshape(2, 5))
print(arr.reshape(5, 2))
```

- `np.arange(10)`: 0부터 9까지의 ndarray를 생성합니다.
- `arr.reshape(2, 5)`: arr의 shape를 2x5로 변경합니다.
- `arr.reshape(5, 2)`: arr의 shape를 5x2로 변경합니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
(10,)
[[0 1 2 3 4]
 [5 6 7 8 9]]
[[0 1]
 [2 3]
 [4 5]
 [6 7]
 [8 9]]
```

- `arr.shape`: arr의 shape는 (10,) 입니다.
- `arr.reshape(2, 5)`: arr의 shape는 (2, 5) 입니다.
- `arr.reshape(5, 2)`: arr의 shape는 (5, 2) 입니다.

> 주의 할 점

변경되는 shape의 원소의 개수와 원본 ndarray의 **원소의 개수가 같아야** 합니다. 예를 들어, 10개의 원소를 가진 ndarray의 shape를 3x3으로 변경할 수 없습니다.

## -1 파라미터

reshape의 파라미터에 -1을 사용하면 나머지 파라미터를 기반으로 **자동으로 원소의 개수를 계산**하여 shape를 변경합니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr.shape)
print(arr.reshape(2, -1))
print(arr.reshape(-1, 2))
```

- `np.arange(10)`: 0부터 9까지의 ndarray를 생성합니다.
- `arr.shape`: arr의 shape를 출력합니다.
- `arr.reshape(2, -1)`: arr의 shape를 2x5로 변경합니다.
- `arr.reshape(-1, 2)`: arr의 shape를 5x2로 변경합니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
(10,)
[[0 1 2 3 4]
 [5 6 7 8 9]]
[[0 1]
 [2 3]
 [4 5]
 [6 7]
 [8 9]]
```

- `arr.reshape(2, -1)`: arr의 shape는 (2, 5) 입니다.
- `arr.reshape(-1, 2)`: arr의 shape는 (5, 2) 입니다.

> 주의 할 점

-1 파라미터는 **한번만 사용**할 수 있습니다. 예를 들어, 10개의 원소를 가진 ndarray의 shape를 (3, -1, -1)으로 변경할 수 없습니다.

## 차원 늘리기

reshape를 사용하여 차원을 늘릴 수 있습니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr.shape)
print(arr.reshape(2, 5))
print(arr.reshape(2, 5).shape)
print(arr.reshape(2, 5, 1))
print(arr.reshape(2, 5, 1).shape)
print(arr.reshape(1, 2, 5,))
print(arr.reshape(1, 2, 5).shape)
```

- `arr.reshape(2, 5, 1)`: arr의 shape를 2x5x1로 변경합니다.
- `arr.reshape(2, 5, 1).shape`: arr의 shape를 출력합니다.
- `arr.reshape(1, 2, 5)`: arr의 shape를 1x2x5로 변경합니다.
- `arr.reshape(1, 2, 5).shape`: arr의 shape를 출력합니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
(10,)
[[0 1 2 3 4]
 [5 6 7 8 9]]
(2, 5)
[[[0]
  [1]
  [2]
  [3]
  [4]]

 [[5]
  [6]
  [7]
  [8]
  [9]]]
(2, 5, 1)
[[[0 1 2 3 4]
  [5 6 7 8 9]]]
(1, 2, 5)
```

- `arr.reshape(2, 5, 1)`의 값은 [[[0] [1] [2] [3] [4]] [[5] [6] [7] [8] [9]]] 입니다.
- `arr.reshape(2, 5, 1).shape`의 값은 (2, 5, 1) 입니다.
- `arr.reshape(1, 2, 5)`의 값은 [[[0 1 2 3 4] [5 6 7 8 9]]] 입니다.
- `arr.reshape(1, 2, 5).shape`의 값은 (1, 2, 5) 입니다.

## 차원 줄이기

reshape를 사용하여 차원을 줄일 수 있습니다.

```python
import numpy as np

arr = np.arange(10).reshape(2, 5, 1)

print(arr)
print(arr.shape)
print(arr.reshape(10))
print(arr.reshape(10).shape)
print(arr.reshape(-1))
print(arr.reshape(-1).shape)
print(arr.reshape(5, 2))
print(arr.reshape(5, 2).shape)
```

- `np.arange(10).reshape(2, 5, 1)`: 0부터 9까지의 ndarray를 생성하고 shape를 2x5x1로 변경합니다.
- `arr.reshape(10)`: arr의 shape를 10으로 변경합니다.
- `arr.reshape(-1)`: arr의 shape를 10으로 변경합니다.
- `arr.reshape(5, 2)`: arr의 shape를 5x2로 변경합니다.

```shell
[[[0]
  [1]
  [2]
  [3]
  [4]]

 [[5]
  [6]
  [7]
  [8]
  [9]]]
(2, 5, 1)
[0 1 2 3 4 5 6 7 8 9]
(10,)
[0 1 2 3 4 5 6 7 8 9]
(10,)
[[0 1]
 [2 3]
 [4 5]
 [6 7]
 [8 9]]
(5, 2)
```

- `arr`: arr의 값은 [[[0] [1] [2] [3] [4]] [[5] [6] [7] [8] [9]]] 입니다.
- `arr.shape`: arr의 shape는 (2, 5, 1) 입니다.
- `arr.reshape(10).shape`: arr의 shape는 (10,) 입니다.
- `arr.reshape(-1).shape`: arr의 shape는 (10,) 입니다.
- `arr.reshape(5, 2).shape`: arr의 shape는 (5, 2) 입니다.
- `arr.reshape(2, 5, 1).shape`의 값은 (2, 5, 1) 입니다.
- `arr.reshape(1, 2, 5).shape`의 값은 (1, 2, 5) 입니다.

## 1차원 배열로 변경하는 다른 방법

### flatten

flatten은 다차원 배열을 1차원 배열로 변경합니다. flatten은 ndarray의 메소드입니다.

```python
import numpy as np

arr = np.arange(10).reshape(2, 5, 1)

print(arr)
print(arr.shape)
print(arr.flatten())
print(arr.flatten().shape)
```

- `arr.flatten()`: arr의 shape를 10으로 변경합니다.

```shell
[[[0]
  [1]
  [2]
  [3]
  [4]]

 [[5]
  [6]
  [7]
  [8]
  [9]]]
(2, 5, 1)
[0 1 2 3 4 5 6 7 8 9]
(10,)
```

- `arr.shape`: arr의 shape는 (2, 5, 1) 입니다.
- `arr.flatten()`: arr의 shape는 (10,) 입니다.

### ravel

ravel은 다차원 배열을 1차원 배열로 변경합니다. ravel은 ndarray의 메소드입니다.

```python
import numpy as np

arr = np.arange(10).reshape(2, 5, 1)

print(arr)
print(arr.shape)
print(arr.ravel())
print(arr.ravel().shape)
```

- `arr.ravel()`: arr의 shape를 10으로 변경합니다.

```shell
[[[0]
  [1]
  [2]
  [3]
  [4]]

 [[5]
  [6]
  [7]
  [8]
  [9]]]
(2, 5, 1)
[0 1 2 3 4 5 6 7 8 9]
(10,)
```

- `arr.shape`: arr의 shape는 (2, 5, 1) 입니다.
- `arr.ravel()`: arr의 shape는 (10,) 입니다.

### flatten과 ravel의 차이

flatten과 ravel은 ndarray의 shape를 1차원으로 변경합니다. 차이점은 **복사본을 반환**하느냐 아니냐 입니다. flatten은 복사본을 반환하고 ravel은 원본을 반환합니다.

---

해시태그: #python #numpy #ndarray #reshape #flatten #ravel

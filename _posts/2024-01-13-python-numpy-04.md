---
layout: single
title: "Python - numpy 04 (indexing, slicing)"
categories: Python-Data_analysis
tag: [Python, numpy]
toc: true
toc_sticky: true
toc_label: "목차"
---
지난 포스팅에서 numpy의 ndarray가 뭔지, 어떻게 생성하는지 모양은 어떻게 바꾸는지 알아보았습니다. 이번에는 ndarray의 **특정 원소를 가져오기**와 **특정 범위(어디에서 어디까지)의 원소를 가져**오는 방법을 알아보겠습니다.

## indexing(인덱싱)

indexing은 특정 원소를 가져오는 방법입니다. list의 indexing과 비슷합니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr[0])
print(arr[5])
print(arr[-1])
```

- `arr[0]`: arr의 첫 번째 원소를 가져옵니다.
- `arr[5]`: arr의 여섯 번째 원소를 가져옵니다. 0부터 시작합니다.
- `arr[-1]`: arr의 마지막 원소를 가져옵니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
0
5
9
```

### 2차원의 indexing

2차원은 이해 하기 쉽게 (행, 열)로 설명 드리겠습니다.

```python
import numpy as np

arr = np.arange(10).reshape(2, 5)

print(arr)
print(arr[0, 0])
print(arr[0, 1])
print(arr[1, 0])
print(arr[1, 1])
```

- `arr[0, 0]`: arr의 첫 번째 행의 첫 번째 원소를 가져옵니다.
- `arr[0, 1]`: arr의 첫 번째 행의 두 번째 원소를 가져옵니다.
- `arr[1, 0]`: arr의 두 번째 행의 첫 번째 원소를 가져옵니다.
- `arr[1, 1]`: arr의 두 번째 행의 두 번째 원소를 가져옵니다.

```shell
[[0 1 2 3 4]
 [5 6 7 8 9]]
0
1
5
6
```

### 3차원의 indexing

3차원도 이해 하기 쉽게 (면, 행, 열)로 설명 드리겠습니다.

```python
import numpy as np

arr = np.arange(18).reshape(2, 3, 3)

print(arr)
print(arr[0, 0, 0])
print(arr[0, 0, 1])
print(arr[0, 1, 0])
print(arr[1, 1, 2])
```

- `arr[0, 0, 0]`: arr의 첫 번째 면의 첫 번째 행의 첫 번째 원소를 가져옵니다.
- `arr[0, 0, 1]`: arr의 첫 번째 면의 첫 번째 행의 두 번째 원소를 가져옵니다.
- `arr[0, 1, 0]`: arr의 첫 번째 면의 두 번째 행의 첫 번째 원소를 가져옵니다.
- `arr[1, 1, 2]`: arr의 두 번째 면의 두 번째 행의 세 번째 원소를 가져옵니다.

```shell
[[[ 0  1  2]
  [ 3  4  5]
  [ 6  7  8]]

 [[ 9 10 11]
  [12 13 14]
  [15 16 17]]]
0
1
3
14
```

> indexing을 하면 차원이 하나 줄어듭니다.

## slicing(슬라이싱)

slicing은 특정 범위의 원소를 가져오는 방법입니다. list의 slicing과 비슷합니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr[0:3])
print(arr[1:3])
print(arr[:3])
print(arr[3:])
print(arr[:])
```

- `arr[0:3]`: arr의 0번째 원소부터 2번째 원소까지 가져옵니다. 마지막 원소는 가져오지 않습니다.
- `arr[1:3]`: arr의 1번째 원소부터 2번째 원소까지 가져옵니다.
- `arr[:3]`: arr의 처음부터 2번째 원소까지 가져옵니다. 비워 두면 처음을 의미합니다.
- `arr[3:]`: arr의 3번째 원소부터 마지막 원소까지 가져옵니다. 비워 두면 마지막을 의미합니다.
- `arr[:]`: arr의 처음부터 마지막 원소까지 가져옵니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
[0 1 2]
[1 2]
[0 1 2]
[3 4 5 6 7 8 9]
[0 1 2 3 4 5 6 7 8 9]
```

### 2차원의 slicing

```python
import numpy as np

arr = np.arange(10).reshape(2, 5)

print(arr)
print(arr[0, 1:3])
print(arr[0, :3])
print(arr[1, 3:])
print(arr[1, :])
print(arr[0:2, 1:3])
print(arr[:, 1:3])
```

- `arr[0, 1:3]`: arr의 첫 번째 행의 1번째 원소부터 2번째 원소까지 가져옵니다.
- `arr[0, :3]`: arr의 첫 번째 행의 처음부터 2번째 원소까지 가져옵니다.
- `arr[1, 3:]`: arr의 두 번째 행의 3번째 원소부터 마지막 원소까지 가져옵니다.
- `arr[1, :]`: arr의 두 번째 행의 처음부터 마지막 원소까지 가져옵니다.
- `arr[0:2, 1:3]`: arr의 첫 번째 행부터 1번째 행의 1번째 원소부터 2번째 원소까지 가져옵니다.
- `arr[:, 1:3]`: arr의 모든 행의 1번째 원소부터 2번째 원소까지 가져옵니다.

```shell
[[0 1 2 3 4]
 [5 6 7 8 9]]
[1 2]
[0 1 2]
[8 9]
[5 6 7 8 9]
[[1 2]
 [6 7]]
[[1 2]
 [6 7]]
```

### 3차원의 slicing

```python
import numpy as np

arr = np.arange(20).reshape(2, 2, 5)

print(arr)
print(arr[0, 0, 1:3])
print(arr[0, 1, :3])
print(arr[1, 0:2, 3:])
print(arr[1, :, :])
print(arr[0:2, 1, 1:3])
print(arr[:, :, 1:3])
```

- `arr[0, 0, 1:3]`: arr의 첫 번째 면의 첫 번째 행의 1번째 원소부터 2번째 원소까지 가져옵니다.
- `arr[0, 1, :3]`: arr의 첫 번째 면의 두 번째 행의 처음부터 2번째 원소까지 가져옵니다.
- `arr[1, 0:2, 3:]`: arr의 두 번째 면의 첫 번째 행부터 1번째 행의 3번째 원소부터 마지막 원소까지 가져옵니다.
- `arr[1, :, :]`: arr의 두 번째 면의 모든 행의 처음부터 마지막 원소까지 가져옵니다.
- `arr[0:2, 1, 1:3]`: arr의 첫 번째 면부터 1번째 면의 1번째 행의 1번째 원소부터 2번째 원소까지 가져옵니다.
- `arr[:, :, 1:3]`: arr의 모든 면의 모든 행의 1번째 원소부터 2번째 원소까지 가져옵니다.

```shell
[[[ 0  1  2  3  4]
  [ 5  6  7  8  9]]

 [[10 11 12 13 14]
  [15 16 17 18 19]]]
[1 2]
[5 6 7]
[[13 14]
 [18 19]]
[[10 11 12 13 14]
 [15 16 17 18 19]]
[[ 6  7]
 [16 17]]
[[[ 1  2]
  [ 6  7]]

 [[11 12]
  [16 17]]]
```

> slicing을 하면 차원이 그대로 유지됩니다.

## fancy indexing(팬시 인덱싱)

fancy indexing은 지정된(여러개) 인덱스의 원소를 가져오는 방법입니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr[[0, 2, 4]])
```

- `np.arange(10)`: 0부터 9까지의 ndarray를 생성합니다.
- `arr[[0, 2, 4]]`: arr의 0번째, 2번째, 4번째 원소를 가져옵니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
[0 2 4]
```

### 2차원의 fancy indexing

```python
import numpy as np

arr = np.arange(10).reshape(2, 5)

print(arr)
print(arr[[0, 1], [1, 3]])
```

- `arr[[0, 1], [1, 3]]`: arr의 0번째 행의 1번째 원소와 1번째 행의 3번째 원소를 가져옵니다.

```shell
[[0 1 2 3 4]
 [5 6 7 8 9]]
[1 8]
```

## boolean indexing(불린 인덱싱)

boolean indexing은 조건에 맞는 원소를 가져오는 방법입니다. 데이터 분석 등에 많이 사용됩니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr[arr > 5])
```

- `arr[arr > 5]`: arr의 원소 중 5보다 큰 원소를 가져옵니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
[6 7 8 9]
```

### 2차원의 boolean indexing

```python
import numpy as np

arr = np.arange(10).reshape(2, 5)

print(arr)
print(arr[arr > 5])
```

- `arr[arr > 5]`: arr의 원소 중 5보다 큰 원소를 가져옵니다.

```shell
[[0 1 2 3 4]
 [5 6 7 8 9]]
[6 7 8 9]
```

### 3차원의 boolean indexing

```python
import numpy as np

arr = np.arange(20).reshape(2, 2, 5)

print(arr)
print(arr[arr > 5])
```

- `arr[arr > 5]`: arr의 원소 중 5보다 큰 원소를 가져옵니다.

```shell
[[[ 0  1  2  3  4]
  [ 5  6  7  8  9]]

 [[10 11 12 13 14]
  [15 16 17 18 19]]]
[ 6  7  8  9 10 11 12 13 14 15 16 17 18 19]
```

### 2개 이상의 조건

2개 이상의 조건을 사용할 수 있습니다.

```python
import numpy as np

arr = np.arange(10)

print(arr)
print(arr[(arr > 5) & (arr < 8)])
```

- `arr[(arr > 5) & (arr < 8)]`: arr의 원소 중 5보다 크고 8보다 작은 원소를 가져옵니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
[6 7]
```

> `&`와 `|`를 사용합니다.

`&`는 and를 의미하고 `|`는 or를 의미합니다. 반드시 `()`를 사용하여 조건을 묶어 주어야 합니다.

---

해시태그: #python #numpy #ndarray #indexing #slicing #fancy_indexing #boolean_indexing

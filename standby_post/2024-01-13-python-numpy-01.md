---
layout: single
title: "Python - numpy 01 (ndarray란?)"
categories: Python-Data_analysis
tag: [Python, numpy]
toc: true
toc_sticky: true
toc_label: "목차"
---
numpy(넘파이)란?  

Numerical Python입니다. 수를 다루기 위한 파이썬 패키지로 예상 할 수 있겠네요. 맞습니다. 수 중에서도 **다차원 배열를 쉽고(?) 효율적**으로 다루기 위한 패키지 입니다.

numpy는 아래와 같이 특징이 있습니다.

- python의 List에 비해 빠르고, 메모리를 효율적으로 사용합니다.
  - (C언어로 구현되어 생성 시에 물리적으로 연속적인 메모리에 생성됩니다.)
- 반복문 없이 데이터 배열을 처리하여 빠르고 편리합니다.
  - 선형대수와 관련된 다양한 기능을 제공합니다.

음~~ 특징을 보다가 다른 곳으로 이동 하시겠네요. 우선 numpy를 사용해 보겠습니다. 그게 numpy를 이해 하는 가장 빠른 방법이라고 생각합니다.

## 설치

```shell
pip install numpy
```

설치는 pip를 이용하여 설치합니다. 간단하네요.

## ndarray란?

ndarray는 numpy의 기본 데이터 타입입니다. ndarray는 n-dimensional array의 약자입니다. 다차원 배열을 의미합니다. numpy의 기본 데이터 타입이라고 했는데, **numpy의 모든 데이터 타입은 ndarray를 기반**으로 만들어 집니다.

ndarray를 만들어 보겠습니다.

```python
import numpy as np

arr = np.array([1, 2, 3])
print(type(arr))
print(arr)
```

- `import numpy as np`: numpy를 np라는 이름으로 import 합니다. **관례**적으로 np라는 이름을 사용합니다.
- `np.array([1, 2, 3])`: numpy의 array 함수를 사용하여 ndarray를 생성합니다.
- `print(type(arr))`: arr의 타입을 출력합니다.
- `print(arr)`: arr의 값을 출력합니다.

```shell
<class 'numpy.ndarray'>
[1 2 3]
```

- `type(arr)`: arr의 타입은 numpy의 ndarray 입니다.
- `arr`: arr의 값은 [1 2 3] 입니다.

### 2차원 배열

2차원 배열을 만들어 보겠습니다.

```python
import numpy as np

dim2_list = [[1, 2, 3], [4, 5, 6]]

arr = np.array(dim2_list)
print(type(arr))
print(arr)
```

- `dim2_list = [[1, 2, 3], [4, 5, 6]]`: 2차원 list를 생성합니다.
- `np.array(dim2_list)`: numpy의 array 함수를 사용하여 ndarray를 생성합니다.

```shell
<class 'numpy.ndarray'>
[[1 2 3]
 [4 5 6]]
```

### 3차원 배열

3차원 배열을 만들어 보겠습니다.

```python
import numpy as np

dim3_list = [[[1, 2, 3], [4, 5, 6]], [[7, 8, 9], [10, 11, 12]]]

arr = np.array(dim3_list)
print(type(arr))
print(arr)
```

```shell
<class 'numpy.ndarray'>
[[[ 1  2  3]
  [ 4  5  6]]

 [[ 7  8  9]
  [10 11 12]]]
```

## ndarray 차원과 모양보기

ndarray은 다차원 배열이라고 하였습니다. 차원과 모양이 중요합니다. 제 생각에는 특히 모양이 중요합니다. 머신러닝에서도 데이터의 모양을 잘 이해 해야 합니다. 지금은 그저 가볍게 알아보겠습니다.

### ndim(차원)

```python
import numpy as np

arr = np.array([1, 2, 3])
print(arr.ndim)
```

- `arr.ndim`: arr의 차원을 확인합니다.

```shell
1
```

- `arr.ndim`: arr의 차원은 1차원 입니다.

### shape(모양)

```python
import numpy as np

arr = np.array([1, 2, 3])
print(arr.shape)
```

- `arr.shape`: arr의 모양을 확인합니다.

```shell
(3,)
```

- `arr.shape`: arr의 모양은 (3,) 입니다.

### 2차원 배열의 차원과 모양

```python
import numpy as np

dim2_list = [[1, 2, 3], [4, 5, 6]]

arr = np.array(dim2_list)
print(arr.ndim)
print(arr.shape)
```

```shell
2
(2, 3)
```

- `arr.ndim`: arr의 차원은 2차원 입니다.
- `arr.shape`: arr의 모양은 (2, 3) 입니다.
- 2차원 배열의 모양은 (행, 열) 입니다.

### 3차원 배열의 차원과 모양

```python
import numpy as np

dim3_list = [[[1, 2, 3], [4, 5, 6]], [[7, 8, 9], [10, 11, 12]]]

arr = np.array(dim3_list)
print(arr.ndim)
print(arr.shape)
```

```shell
3
(2, 2, 3)
```

- `arr.ndim`: arr의 차원은 3차원 입니다.
- `arr.shape`: arr의 모양은 (2, 2, 3) 입니다.
- 3차원 배열의 모양은 (면, 행, 열) 입니다.

## ndarray의 데이터 타입

ndarray의 데이터 타입을 확인해 보겠습니다.

```python
import numpy as np

arr = np.array([1, 2, 3])
print(arr.dtype)
```

- `arr.dtype`: arr의 데이터 타입을 확인합니다.

```shell
int32
```

- `arr.dtype`: arr의 데이터 타입은 int32 입니다.

### float 타입

```python
import numpy as np

arr = np.array([1.0, 2.0, 3.0])
print(arr.dtype)
```

```shell
float64
```

### 데이터 타입을 지정하여 생성

```python
import numpy as np

arr = np.array([1, 2, 3], dtype='float64')
print(arr.dtype)
```

- `dtype='float64'`: dtype 파라미터를 사용하여 데이터 타입을 지정할 수 있습니다.

```shell
float64
```

### 'astype'으로 데이터 타입 변경

```python
import numpy as np

arr = np.array([1, 2, 3])
print(arr.dtype)

arr = arr.astype('float64')
print(arr.dtype)
```

- `arr.astype('float64')`: astype 함수를 사용하여 데이터 타입을 변경할 수 있습니다.

```shell
int32
float64
```

- 처음 생성된 arr의 데이터 타입은 int32 이며 astype 함수를 사용하여 변경된 arr의 데이터 타입은 float64 입니다.

### 그 외 데이터 타입과 관련된 내용

- ndarray는 같은 데이터 타입의 데이터만 저장할 수 있습니다. 예를 들어 int32와 float64는 같은 ndarray에 저장할 수 없습니다.
- ndarray의 데이터 타입은 int, float, str, bool, object 등이 있습니다. 주로 int, float을 사용합니다.

---

해시태그: #python #numpy #ndarray #data_analysis

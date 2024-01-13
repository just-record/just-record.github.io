---
layout: single
title: "Python - numpy 02 (ndarray 여러 가지 생성 방법)"
categories: Python-Data_analysis
tag: [Python, numpy]
toc: true
toc_sticky: true
toc_label: "목차"
---
지난 포스팅에서 numpy의 ndarray가 무엇인지 간단하게 알아보았습니다. 아직 감이 오지 않으시겠죠. 조금 더 진행 해 보도록 하겠습니다. 이번에는 ndarray를 생성하는 여러 가지 방법을 알아보겠습니다. 지난 번에 list를 이용 하여 ndarray를 생성하였습니다. ndarray는 **list외에도 다양한 방법**으로 생성할 수 있습니다.

## np.arange

np.arange를 이용하여 ndarray를 생성해 보겠습니다. 어디선가 많이 보신 것 같죠? 맞습니다. **arange는 range와 비슷**합니다. range는 list를 생성히고 arange는 ndarray를 생성합니다.

```python
import numpy as np

arr = np.arange(10)
print(arr)
```

- `np.arange(10)`: 0부터 9까지의 ndarray를 생성합니다.

```shell
[0 1 2 3 4 5 6 7 8 9]
```

## np.zeros

np.zeros는 0으로 채워진 ndarray를 생성합니다.

```python
import numpy as np

arr = np.zeros(10)
print(arr)
```

- `np.zeros(10)`: 0으로 채워진 10개의 ndarray를 생성합니다.

```shell
[0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
```

> shape(모양)을 지정하여 생성

```python
import numpy as np

arr = np.zeros((3, 4))
print(arr)
```

- `np.zeros((3, 4))`: 0으로 채워진 3x4의 ndarray를 생성합니다.

```shell
[[0. 0. 0. 0.]
 [0. 0. 0. 0.]
 [0. 0. 0. 0.]]
```

## np.ones

np.ones는 1로 채워진 ndarray를 생성합니다.

```python
import numpy as np

arr = np.ones(10)
print(arr)
```

- `np.ones(10)`: 1로 채워진 10개의 ndarray를 생성합니다.

```shell
[1. 1. 1. 1. 1. 1. 1. 1. 1. 1.]
```

> 모양을 지정하여 생성

```python
import numpy as np

arr = np.ones((3, 4))
print(arr)
```

- `np.ones((3, 4))`: 1로 채워진 3x4의 ndarray를 생성합니다.

```shell
[[1. 1. 1. 1.]
 [1. 1. 1. 1.]
 [1. 1. 1. 1.]]
```

## np.random

np.random은 **랜덤한 값**으로 채워진 ndarray를 생성합니다. random은 생각보다 많이 사용합니다. 예를 들어 random 값으로 채워진 ndarray를 생성하여 머신러닝의 데이터로 사용하기도 합니다. 그 외에도 프로그래밍을 하다 보면 정해진 값이 아닌 random 값이 필요할 때가 있습니다. np.random을 알아보기 전에 균등부포와 정규분포를 알아보겠습니다.

### 균등분포(uniform distribution)와 정규분포(normal distribution)

먼저 균등분포와 정규분포에 대해 알아보겠습니다.

- 균등분포는 **모든 값이 나올 확률이 동일**합니다. 모든 값이 균등한 확률로 일어날 것이라고 예상될 때 사용합니다. 예를 들어 주사위를 던져서 나오는 값은 1부터 6까지의 값이 나올 확률이 동일합니다. 미래의 결과값을 경험으로 알 수 없을 때 사용합니다.
- 정규분포는 **평균을 중심으로 값이 모여있는 분포**입니다. 모든 값이 나올 확률이 동일하지 않습니다. 평균을 중심으로 값이 모여있는 분포입니다. 예를 들어 한 학급의 성적은 정규분포를 따릅니다. 표준편차는 평균을 기준으로 값이 퍼져 있는 정도를 나타냅니다. 현실 세계에서는 정규분포를 따르는 경우가 많습니다. 미래의 결과값을 경험으로 알 수 있을 때 사용합니다.

### np.random.rand

np.random.rand는 균등분포(0과 1사이)를 따르는 랜덤한 값으로 채워진 ndarray를 생성합니다.

```python
import numpy as np

arr = np.random.rand(10)
print(arr)
```

- `np.random.rand(10)`: 균등분포를 따르는 랜덤한 값으로 채워진 10개의 ndarray를 생성합니다.

```shell
[0.81483179 0.38642987 0.96337695 0.77482397 0.33684728 0.57664842 0.60956433 0.93551999 0.24402638 0.99773951]
```

> 모양을 지정하여 생성

```python
import numpy as np

arr = np.random.rand(3, 4)
print(arr)
```

- `np.random.rand(3, 4)`: 균등분포를 따르는 랜덤한 값으로 채워진 3x4의 ndarray를 생성합니다.

```shell
[[0.81483179 0.38642987 0.96337695 0.77482397]
 [0.33684728 0.57664842 0.60956433 0.93551999]
 [0.24402638 0.99773951 0.81483179 0.38642987]]
```

### np.random.randn

np.random.randn은 정규분포(평균이 0이고 표준편차가 1 -> 대체로 -1과 1사이의 값)를 따르는 랜덤한 값으로 채워진 ndarray를 생성합니다.

```python
import numpy as np

arr = np.random.randn(10)
print(arr)
```

- `np.random.randn(10)`: 정규분포를 따르는 랜덤한 값으로
- 채워진 10개의 ndarray를 생성합니다.

```shell
[-0.16595599  0.44044174  0.89489355  0.80616842  0.05257897 -0.46830699 -0.16595599  0.44044174  0.89489355  0.80616842]
```

> 모양을 지정하여 생성

```python
import numpy as np

arr = np.random.randn(3, 4)
print(arr)
```

- `np.random.randn(3, 4)`: 정규분포를 따르는 랜덤한 값으로 채워진 3x4의 ndarray를 생성합니다.

```shell
[[-0.16595599  0.44044174  0.89489355  0.80616842]
 [ 0.05257897 -0.46830699 -0.16595599  0.44044174]
 [ 0.89489355  0.80616842  0.05257897 -0.46830699]]
```

### np.random.randint

np.random.randint는 정수의 균등분포를 따르는 랜덤한 값으로 채워진 ndarray를 생성합니다.

```python
import numpy as np

arr = np.random.randint(10, size=10)
print(arr)
```

- `np.random.randint(10, size=10)`: 0부터 9까지의 정수의 균등분포를 따르는 랜덤한 값으로 채워진 10개의 ndarray를 생성합니다.

```shell
[7 6 3 1 3 5 2 7 2 9]
```

> 옵션을 추가하여 생성

```python
import numpy as np

arr = np.random.randint(1, 30, size=(3, 4))
print(arr)
```

- `np.random.randint(1, 30, size=(3, 4))`: 1부터 29까지의 정수의 균등분포를 따르는 랜덤한 값으로 채워진 3x4의 ndarray를 생성합니다.
- `numpy.random.randint(low, high=None, size=None, dtype='I')`
  - low: 최소값(정수), 파라미터가 1개 일 경우 0 ~ low 까지
  - high: 최대값(정수), 선택 파라미터
  - size: shape(모양)
  - dtype: 데이터 타입

```shell
[[ 8  1  7  1]
 [ 2  2  2  2]
 [ 1  2  1  1]]
```

### np.random.seed

np.random.seed는 랜덤한 값을 생성할 때 마다 같은 값이 나오도록 설정합니다. seed값은 어떤 값이든 상관 없습니다.

```python
import numpy as np

for i in range(5):
    arr = np.random.randint(10, size=10)
    print(arr)

print()

for i in range(5):
    np.random.seed(0)
    arr = np.random.randint(10, size=10)
    print(arr)
```

- `np.random.seed(0)`: 랜덤한 값을 생성할 때 마다 같은 값이 나오도록 설정합니다.

```shell
[6 0 3 4 9 7 7 9 8 2]
[1 9 5 9 1 6 8 9 9 3]
[4 6 3 2 6 3 7 2 1 2]
[4 0 1 6 9 6 0 8 5 9]
[5 3 6 5 1 5 1 4 7 6]

[5 0 3 3 7 9 3 5 2 4]
[5 0 3 3 7 9 3 5 2 4]
[5 0 3 3 7 9 3 5 2 4]
[5 0 3 3 7 9 3 5 2 4]
[5 0 3 3 7 9 3 5 2 4]
```

## np.linspace

linspace는 지정한 범위의 ndarray를 생성합니다. 알아 두면 유용합니다. 그래프를 그릴 때 x축의 범위를 지정할 때 많이 사용합니다.

```python
import numpy as np

arr = np.linspace(0, 100, 5)
print(arr)
```

- `np.linspace(0, 100, 5)`: 0부터 100까지 5개의 ndarray를 생성합니다.

```shell
[  0.  25.  50.  75. 100.]
```

## 그 외

### np.full

full은 지정한 값으로 채워진 ndarray를 생성합니다.

```python
import numpy as np

arr = np.full(10, 5)
print(arr)
```

- `np.full(10, 5)`: 5로 채워진 10개의 ndarray를 생성합니다.

```shell
[5 5 5 5 5 5 5 5 5 5]
```

### np.eye

eye는 단위 행렬을 생성합니다.

```python
import numpy as np

arr = np.eye(3)
print(arr)
```

- `np.eye(3)`: 3x3의 단위 행렬을 생성합니다.

```shell
[[1. 0. 0.]
 [0. 1. 0.]
 [0. 0. 1.]]
```

### np.empty

empty는 초기화 되지 않은 ndarray를 생성합니다.

```python
import numpy as np

arr = np.empty(10)
print(arr)
```

- `np.empty(10)`: 초기화 되지 않은 10개의 ndarray를 생성합니다.

```shell
[0.00000000e+000 2.44769159e-307 9.79088179e-307 1.29061685e-306 1.24608618e-306 1.37962456e-306 6.45306730e-307 0.00000000e+000 0.00000000e+000 0.00000000e+000]
```

- 초기화를 하지 않았기 때문에 메모리 상에 있는 값이 그대로 출력됩니다. 어떤 값이 출력될지 모릅니다.

---

해시태그: #python #numpy #ndarray #arange #zeros #ones #random #rand #randn #randint #seed #linspace #full #eye #empty

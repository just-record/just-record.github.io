---
layout: single
title: "Python - pandas 02 (Series 함수, 연산)"
categories: Python-Data_analysis
tag: [Python, pandas, series]
toc: true
toc_sticky: true
toc_label: "목차"
---
## Series의 주요 속성

### index

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s.index)
```

- `s.index`: s의 인덱스를 출력합니다.

```shell
Index(['a', 'b', 'c'], dtype='object')
```

### values

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s.values)
```

- `s.values`: s의 값들을 출력합니다.

```shell
[1 2 3]
```

### dtype

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s.dtype)
```

- `s.dtype`: s의 데이터 타입을 출력합니다.

```shell
int64
```

### size

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s.size)
```

- `s.size`: s의 크기를 출력합니다.

```shell
3
```

### shape

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s.shape)
```

- `s.shape`: s의 모양을 출력합니다.

```shell
(3,)
```

### ndim

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s.ndim)
```

- `s.ndim`: s의 차원을 출력합니다.

```shell
1
```

## Series의 주요 함수

### head

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
print(s.head(3))
```

- `s.head(3)`: s의 앞에서 3개의 데이터를 출력합니다.

```shell
a    1
b    2
c    3
dtype: int64
```

### tail

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
print(s.tail(3))
```

- `s.tail(3)`: s의 뒤에서 3개의 데이터를 출력합니다.

```shell
c    3
d    4
e    5
dtype: int64
```

### sort_index

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
print(s.sort_index())
```

- `s.sort_index()`: s의 인덱스를 기준으로 정렬합니다.

```shell
a    1
b    2
c    3
d    4
e    5
dtype: int64
```

### sort_values

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
print(s.sort_values())
```

- `s.sort_values()`: s의 값을 기준으로 정렬합니다.

```shell
a    1
b    2
c    3
d    4
e    5
dtype: int64
```

### unique

```python
import numpy as np
import pandas as pd

s = pd.Series([1, 2, 3, 4, np.NaN, 1, 2, 3, 4, 5])
print(s.unique())
```

- `s.unique()`: s의 중복되지 않는 값들을 출력합니다.

```shell
[ 1.  2.  3.  4. nan  5.]
```

### count

```python
import numpy as np
import pandas as pd

s = pd.Series([1, 2, 3, 4, np.NaN, 1, 2, 3, 4, 5])
print(s.count())
```

- `s.count()`: s의 데이터의 개수(NaN 제외)를 출력합니다.

```shell
9
```

### value_counts

```python
import numpy as np
import pandas as pd

s = pd.Series([1, 2, 3, 4, np.NaN, 1, 2, 3, 4, 5])
print(s.value_counts())
```

- `s.value_counts()`: s의 데이터의 개수(NaN 제외)를 출력합니다.

```shell
1.0    2
2.0    2
3.0    2
4.0    2
5.0    1
Name: count, dtype: int64
```

### mean

```python
import numpy as np
import pandas as pd

s = pd.Series([1, 2, 3, 4, np.NaN, 1, 2, 3, 4, 5])
print(s.mean())
```

- `s.mean()`: s의 평균(NaN 제외)을 출력합니다.

```shell
2.7777777777777777
```

### apply

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])

def add_10(n):
    return n + 10

print(s.apply(add_10))
```

- `s.apply(add_10)`: s의 모든 데이터에 add_10 함수를 적용합니다.

```shell
a    11
b    12
c    13
d    14
e    15
dtype: int64
```

## Series 연산

### Series + Series

```python
import pandas as pd

s1 = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
s2 = pd.Series([10, 20, 30, 40, 50], ['a', 'b', 'c', 'd', 'e'])
print(s1 + s2)
```

- `s1 + s2`: s1과 s2의 같은 인덱스의 데이터를 더합니다.

```shell
a    11
b    22
c    33
d    44
e    55
dtype: int64
```

### Series + Scalar

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
print(s + 10)
```

- `s + 10`: s의 모든 데이터에 10을 더합니다.

```shell
a    11
b    12
c    13
d    14
e    15
dtype: int64
```

### Series + Series (index가 다른 경우)

```python
import pandas as pd

s1 = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
s2 = pd.Series([10, 20, 30, 40, 50], ['a', 'b', 'c', 'd', 'f'])
print(s1 + s2)
```

- `s1 + s2`: s1과 s2의 같은 인덱스의 데이터를 더합니다. 같은 인덱스가 없는 경우 NaN으로 처리합니다.

```shell
a    11.0
b    22.0
c    33.0
d    44.0
e     NaN
f     NaN
dtype: float64
```

### Series - Series

```python
import pandas as pd

s1 = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
s2 = pd.Series([10, 20, 30, 40, 50], ['a', 'b', 'c', 'd', 'e'])
print(s1 - s2)
```

- `s1 - s2`: s1과 s2의 같은 인덱스의 데이터를 뺍니다.

```shell
a    -9
b   -18
c   -27
d   -36
e   -45
dtype: int64
```

### Series - Scalar

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
print(s - 10)
```

- `s - 10`: s의 모든 데이터에 10을 뺍니다.

```shell
a    -9
b    -8
c    -7
d    -6
e    -5
dtype: int64
```

### Series * Series

```python
import pandas as pd

s1 = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
s2 = pd.Series([10, 20, 30, 40, 50], ['a', 'b', 'c', 'd', 'e'])
print(s1 * s2)
```

- `s1 * s2`: s1과 s2의 같은 인덱스의 데이터를 곱합니다.

```shell
a     10
b     40
c     90
d    160
e    250
dtype: int64
```

### Series / Series

```python
import pandas as pd

s1 = pd.Series([1, 2, 3, 4, 5], ['a', 'b', 'c', 'd', 'e'])
s2 = pd.Series([10, 20, 30, 40, 50], ['a', 'b', 'c', 'd', 'e'])
print(s1 / s2)
```

- `s1 / s2`: s1과 s2의 같은 인덱스의 데이터를 나눕니다.

```shell
a    0.1
b    0.1
c    0.1
d    0.1
e    0.1
dtype: float64
```

---

해시태그: #python #pandas #series #index #values #dtype #size #shape #ndim #head #tail #sort_index #sort_values #unique #count #value_counts #mean #apply

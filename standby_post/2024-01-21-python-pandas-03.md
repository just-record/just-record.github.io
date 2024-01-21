---
layout: single
title: "Python - pandas 03 (DataFrame이란?)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
DataFrame이란?

2차원 데이터 구조(Series가 모여서)로, 행과 열이 있는 테이블 형태의 데이터 구조입니다. 행(row)과 열(column)은 각각 Series로 구성되어 있어 각각의 index를 가집니다.

## DataFrame 생성

### Series를 이용한 DataFrame 생성

```python
import pandas as pd

s1 = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s2 = pd.Series([4, 5, 6], ['a', 'b', 'c'])
s3 = pd.Series([7, 8, 9], ['a', 'b', 'c'])

df = pd.DataFrame([s1, s2, s3])
print(df)
```

- `pd.DataFrame([s1, s2, s3])`: DataFrame을 생성합니다. DataFrame의 인자로 Series를 리스트 형태로 전달합니다.

```shell
   a  b  c
0  1  2  3
1  4  5  6
2  7  8  9
```

#### index 지정

```python
import pandas as pd

s1 = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s2 = pd.Series([4, 5, 6], ['a', 'b', 'c'])
s3 = pd.Series([7, 8, 9], ['a', 'b', 'c'])

df = pd.DataFrame([s1, s2, s3], index=['r1', 'r2', 'r3'])
print(df)
```

- `pd.DataFrame([s1, s2, s3], index=['r1', 'r2', 'r3'])`: DataFrame을 생성합니다. DataFrame의 인자로 Series를 리스트 형태로 전달합니다. index를 지정하여 DataFrame을 생성합니다.

```shell
    a  b  c
r1  1  2  3
r2  4  5  6
r3  7  8  9
```

#### index가 다른 경우

```python
import pandas as pd

s1 = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s2 = pd.Series([4, 5, 6], ['a', 'b', 'd'])
s3 = pd.Series([7, 8, 9], ['a', 'b', 'e'])

df = pd.DataFrame([s1, s2, s3])
print(df)
```

- `pd.DataFrame([s1, s2, s3])`: DataFrame을 생성합니다. DataFrame의 인자로 Series를 리스트 형태로 전달합니다.

```shell
     a    b    c    d    e
0  1.0  2.0  3.0  NaN  NaN
1  4.0  5.0  NaN  6.0  NaN
2  7.0  8.0  NaN  NaN  9.0
```

- index가 다른 경우, 해당 index에 해당하는 값이 없으면 NaN(Not a Number)으로 표시됩니다.

### dictionary를 이용한 DataFrame 생성

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
print(df)
```

- `pd.DataFrame(data)`: DataFrame을 생성합니다. DataFrame의 인자로 dict를 전달합니다.

```shell
   a  b  c
0  1  4  7
1  2  5  8
2  3  6  9
```

- dict의 key가 DataFrame의 column이 됩니다.
- dict의 value가 DataFrame의 row가 됩니다.
- dict의 value의 길이가 다르면 오류가 발생합니다.

#### index 길이가 다른 경우

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8]
}

df = pd.DataFrame(data)
print(df)
```

- `pd.DataFrame(data)`: DataFrame을 생성합니다. DataFrame의 인자로 dict를 전달합니다.

```shell
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
...
ValueError: All arrays must be of the same length
```

## 주요 속성

### values

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
print(df.values)
```

- `df.values`: DataFrame의 값들을 조회합니다.

```shell
[[1 4 7]
 [2 5 8]
 [3 6 9]]
```

- DataFrame의 값들은 ndarray로 반환됩니다.
- DataFrame의 값들은 2차원 배열로 반환됩니다.
- DataFrame의 값들은 DataFrame의 index와 column을 제외한 값들입니다.

### index

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data, index=['r1', 'r2', 'r3'])
print(df.index)
```

- `df.index`: DataFrame의 index를 조회합니다.

```shell
Index(['r1', 'r2', 'r3'], dtype='object')
```

- DataFrame의 index는 Index로 반환됩니다.

### columns

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9],
    'd': [10, 11, 12]
}

df = pd.DataFrame(data)
print(df.columns)
```

- `df.columns`: DataFrame의 column을 조회합니다.

```shell
Index(['a', 'b', 'c', 'd'], dtype='object')
```

- DataFrame의 column은 Index로 반환됩니다.

### `shape`

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9],
    'd': [10, 11, 12]
}

df = pd.DataFrame(data)
print(df.shape)
```

- `df.shape`: DataFrame의 모양을 조회합니다.

```shell
(3, 4)
```

- DataFrame의 모양은 (행, 열)로 반환됩니다.
- DataFrame의 모양은 튜플로 반환됩니다.

### size

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9],
    'd': [10, 11, 12]
}

df = pd.DataFrame(data)
print(df.size)
```

- `df.size`: DataFrame의 크기를 조회합니다.

```shell
12
```

- DataFrame의 크기는 행과 열의 곱으로 반환됩니다.
- DataFrame의 크기는 정수로 반환됩니다.
- DataFrame의 크기는 DataFrame의 값들의 개수입니다.

### ndim

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9],
    'd': [10, 11, 12]
}

df = pd.DataFrame(data)
print(df.ndim)
```

- `df.ndim`: DataFrame의 차원을 조회합니다.
- 모두 2차원이기 때문에 2가 반환됩니다.

```shell
2
```

## index - 상세

### index 조회

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
print(df.index)
```

- `df.index`: DataFrame의 index를 조회합니다.

```shell
RangeIndex(start=0, stop=3, step=1)
```

- RangeIndex는 0부터 3까지 1씩 증가하는 index입니다.

### index 변경

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
df.index = ['r1', 'r2', 'r3']
print(df)
```

- `df.index = ['r1', 'r2', 'r3']`: DataFrame의 index를 변경합니다.

```shell
    a  b  c
r1  1  4  7
r2  2  5  8
r3  3  6  9
```

### index 이름 변경

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
df.index = ['r1', 'r2', 'r3']
df.index.name = 'index'
print(df)
```

- `df.index.name = 'index'`: DataFrame의 index의 이름을 변경합니다.

```shell
        a  b  c
index
r1      1  4  7
r2      2  5  8
r3      3  6  9
```

### index 초기화

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
df.index = ['r1', 'r2', 'r3']
df = df.reset_index()
print(df)
```

- `df = df.reset_index()`: DataFrame의 index를 초기화합니다.

```shell
   index  a  b  c
0     r1  1  4  7
1     r2  2  5  8
2     r3  3  6  9
```

- index가 초기화되면서 기존 index는 column으로 변경됩니다.
- index가 초기화되면서 RangeIndex가 생성됩니다.
- index가 초기화되면서 index의 이름이 삭제됩니다.

### column을 index로 변경

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
df = df.set_index('a')
print(df)
```

- `df = df.set_index('a')`: DataFrame의 index를 'a' column으로 변경합니다.

```shell
   b  c
a
1  4  7
2  5  8
3  6  9
```

- index가 'a' column으로 변경됩니다.

## columns - 상세

### column 조회

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
print(df.columns)
```

- `df.columns`: DataFrame의 column을 조회합니다.

```shell
Index(['a', 'b', 'c'], dtype='object')
```

- Index는 column의 이름을 가지고 있습니다.
- dtype='object'는 column의 데이터 타입이 object라는 의미입니다.

### column 이름 변경

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
df.columns = ['col1', 'col2', 'col3']
print(df)
```

- `df.columns = ['col1', 'col2', 'col3']`: DataFrame의 column의 이름을 변경합니다.

```shell
   col1  col2  col3
0     1     4     7
1     2     5     8
2     3     6     9
```

### column 이름 변경 - rename

```python
import pandas as pd

data = {
    'a': [1, 2, 3],
    'b': [4, 5, 6],
    'c': [7, 8, 9]
}

df = pd.DataFrame(data)
df = df.rename(columns={'a': 'col1', 'b': 'col2', 'c': 'col3'})
print(df)
```

- `df = df.rename(columns={'a': 'col1', 'b': 'col2', 'c': 'col3'})`: DataFrame의 column의 이름을 변경합니다.

```shell
   col1  col2  col3
0     1     4     7
1     2     5     8
2     3     6     9
```

- `df.rename(columns={'a': 'col1', 'b': 'col2', 'c': 'col3'})`: DataFrame의 column의 이름을 변경합니다.

## `inplace` 옵션

`inplace` 옵션은 DataFrame의 원본 데이터를 변경할지 여부를 결정합니다. `inplace=True`로 설정하면 원본 데이터를 변경하고, `inplace=False`로 설정하면 원본 데이터를 변경하지 않습니다. `inplace` 옵션의 기본값은 `False`입니다.

- set_index는 `inplace=True`를 설정하면 원본 데이터를 변경합니다. 그렇지 않으면 원본 데이터를 변경하지 않습니다. 아니면 다시 변수에 할당해야 합니다.
- reset_index도 동일합니다.
- rename도 동일합니다.

---

해시태그: #python #pandas #dataframe #series #index #columns #inplace #set_index #reset_index #rename

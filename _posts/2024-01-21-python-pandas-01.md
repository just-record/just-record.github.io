---
layout: single
title: "Python - pandas 01 (pandas란? Series란?)"
categories: Python-Data_analysis
tag: [Python, pandas, series]
toc: true
toc_sticky: true
toc_label: "목차"
---
pandas(판다스)란?

파이썬 프로그래밍 언어를 위한 오픈 소스 라이브러리로서, 데이터 분석 및 조작을 위해 주로 사용됩니다. 이 라이브러리는 2008년 웨스 맥키니(Wes McKinney)에 의해 개발되었으며, 데이터 과학 및 분석 분야에서 널리 사용됩니다. 판다스의 주요 특징은 다음과 같습니다:

- 데이터 구조: 주로 두 가지 주요 데이터 구조를 제공합니다:
  - DataFrame: 2차원(row, column)의 형태 데이터 구조로, SQL 테이블이나 엑셀 스프레드시트와 유사합니다. 다양한 유형의 데이터(숫자, 문자열, 부울 등)를 담을 수 있으며, 각 열은 서로 다른 데이터 유형을 가질 수 있습니다.
  - Series: 1차원 레이블이 붙은 배열로, 단일 데이터 유형만을 포함할 수 있습니다.
- 데이터 처리 및 분석: 데이터를 쉽게 가져오고, 변형하고, 분석할 수 있는 다양한 기능을 제공합니다. 이에는 데이터 정렬, 그룹화, 병합, 결측치 처리, 시계열 데이터 분석 등이 포함됩니다.
- 입출력 도구: 다양한 형식의 데이터를 읽고 쓸 수 있습니다. 예를 들어 CSV, 엑셀, JSON, SQL 데이터베이스 등에서 데이터를 불러오거나 저장할 수 있습니다.
- 통합 기능: 판다스는 NumPy와 통합되어 있으며, Matplotlib, Seaborn과 같은 데이터 시각화 도구와도 잘 호환됩니다.

## 설치 및 시작

```shell
pip install pandas
```

설치는 pip를 이용하여 설치합니다.

> 설치 확인

```python
import pandas as pd
```

오류 없이 import가 되었다면 설치가 잘 된 것 입니다. 관례적으로 pandas를 pd라는 이름으로 import 합니다.

> pandas의 데이터 구조

pandas는 크게 2가지 데이터 구조를 제공합니다.

- Series
- DataFrame

## Series란?

Series란?

1차원 배열로, 인덱스가 붙은 데이터를 저장하는 구조입니다. Series는 다음과 같은 특징을 가집니다:

- 1차원 배열: Series는 1차원 형태의 배열입니다
- 데이터 타입: Series 내의 모든 데이터는 동일한 데이터 타입을 가집니다. 예를 들어, 모든 요소가 정수, 부동 소수점 수, 문자열 등일 수 있습니다.
- 인덱스: 각 Series 요소에는 기본적으로 0부터 시작하는 정수 인덱스가 부여됩니다. 하지만 사용자는 문자열, 날짜 등 다양한 형태의 인덱스를 사용자 정의하여 할당할 수도 있습니다.
- 데이터 조작 및 분석: Series는 다양한 데이터 조작 및 분석 기능을 제공합니다. 예를 들어, 통계 계산, 데이터 변환 등의 작업을 쉽게 수행할 수 있습니다.
- DataFrame과의 관계: DataFrame은 여러 개의 Series가 모여 형성된 2차원 데이터 구조입니다. 각 Series는 DataFrame의 열을 형성합니다.
- 생성 방법: Series는 list, numpy, dictionary 등 다양한 데이터 소스에서 생성할 수 있습니다.

### Series 생성 - list

```python
import pandas as pd

s = pd.Series([1, 2, 3])
print(type(s))
print(s.dtype)
print(s.values)
print(type(s.values))
print(s.index)
print(s.index.values)
print(s)
```

- `pd.Series([1, 2, 3])`: pandas의 Series 함수를 사용하여 Series를 생성합니다.
- `print(type(s))`: s의 타입을 출력합니다.
- `print(s.dtype)`: s의 데이터 타입을 출력합니다.
- `print(s.values)`: s의 값들을 출력합니다.
- `print(type(s.values))`: s의 값들의 타입을 출력합니다.
- `print(s.index)`: s의 인덱스를 출력합니다.
- `print(s.index.values)`: s의 인덱스의 값들을 출력합니다.
- `print(s)`: s를 출력합니다.

```shell
<class 'pandas.core.series.Series'>
int64
[1 2 3]
<class 'numpy.ndarray'>
RangeIndex(start=0, stop=3, step=1)
[0 1 2]
0    1
1    2
2    3
dtype: int64
```

- `print(type(s))`: s의 타입은 pandas의 Series 타입입니다.
- `print(s.dtype)`: s의 데이터 타입은 int64 입니다.
- `print(s.values)`: s의 값들은 numpy의 ndarray 타입으로 [1 2 3] 입니다.
- `print(type(s.values))`: s의 값들의 타입은 numpy의 ndarray 타입입니다.
- `print(s.index)`: s의 인덱스는 0부터 3까지 1씩 증가하는 RangeIndex 타입입니다.
- `print(s.index.values)`: s의 인덱스의 값들은 numpy의 ndarray 타입입니다.
- `print(s)`: s의 값들을 출력합니다.

### Series 생성 - list + index

```python
import pandas as pd

s = pd.Series([1, 2, 3], index=['a', 'b', 'c'])
print(s.index)
print(s)
```

- `pd.Series([1, 2, 3], index=['a', 'b', 'c'])`: 인덱스를 지정하여 생성합니다.

```shell
Index(['a', 'b', 'c'], dtype='object')
a    1
b    2
c    3
dtype: int64
```

- `print(s.index)`: s의 인덱스는 ['a', 'b', 'c'] 입니다.

### Series 생성 - list + dtype

```python
import pandas as pd

s = pd.Series([1, 2, 3], dtype='float64')
print(s.dtype)
print(s)
```

- `pd.Series([1, 2, 3], dtype='float64')`: 데이터 타입을 지정하여 생성합니다.

```shell
float64
0    1.0
1    2.0
2    3.0
dtype: float64
```

### Series 생성 - numpy

```python
import pandas as pd
import numpy as np

s = pd.Series(np.array([1, 2, 3]))
print(types(s.dtype))
print(s)
```

- `pd.Series(np.array([1, 2, 3]))`: numpy의 array를 사용하여 Series를 생성합니다.

```shell
<class 'numpy.dtype'>
0    1
1    2
2    3
dtype: int64
```

- `print(types(s.dtype))`: s의 데이터 타입은 numpy의 dtype 타입입니다.
- `print(s)`: s의 값들을 출력합니다.

### Series 생성 - dictionary

```python
import pandas as pd

s = pd.Series({'a': 1, 'b': 2, 'c': 3})
print(s.index)
print(s)
```

- `pd.Series({'a': 1, 'b': 2, 'c': 3})`: dictionary를 사용하여 Series를 생성합니다.

```shell
Index(['a', 'b', 'c'], dtype='object')
a    1
b    2
c    3
dtype: int64
```

- `print(s.index)`: s의 인덱스는 dictionary의 key 값들입니다.

## Series 읽기, 수정

### index, values

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s.index)
print(s.values)
```

- `s.index`: s의 인덱스를 출력합니다.
- `s.values`: s의 값들을 출력합니다.

```shell
Index(['a', 'b', 'c'], dtype='object')
[1 2 3]
```

### Data 조회

#### Data 조회 - index

index를 이용하여 특정 데이터를 조회할 수 있습니다.

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s['a'])
```

- `s['a']`: s의 인덱스가 'a'인 데이터를 조회합니다.

```shell
1
```

#### Data 조회 - index list

index list를 이용하여 특정 데이터를 조회할 수 있습니다.

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s[['a', 'c']])
```

- `s[['a', 'c']]`: s의 인덱스가 'a' 또는 'c'인 데이터를 조회합니다.

```shell
a    1
c    3
dtype: int64
```

#### Data 조회 - index slicing

index slicing을 이용하여 특정 데이터를 조회할 수 있습니다.

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s['a':'c'])
```

- `s['a':'c']`: s의 인덱스가 'a'부터 'c'인 데이터를 조회합니다.

```shell
a    1
b    2
c    3
dtype: int64
```

#### Data 조회 - boolean indexing

boolean indexing을 이용하여 특정 데이터를 조회할 수 있습니다.

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
print(s[s > 1])
```

- `s[s > 1]`: s의 값이 1보다 큰 데이터를 조회합니다.

```shell
b    2
c    3
dtype: int64
```

### Data 수정

#### Data 수정 - index

index를 이용하여 특정 데이터를 수정할 수 있습니다.

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s['a'] = 10
print(s)
```

- `s['a'] = 10`: s의 인덱스가 'a'인 데이터를 10으로 수정합니다.

```shell
a    10
b     2
c     3
dtype: int64
```

#### Data 수정 - index list

index list를 이용하여 특정 데이터를 수정할 수 있습니다.

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s[['a', 'c']] = 10
print(s)
```

- `s[['a', 'c']] = 10`: s의 인덱스가 'a' 또는 'c'인 데이터를 10으로 수정합니다.

```shell
a    10
b     2
c    10
dtype: int64
```

> index slicing, boolean indexing을 이용하여 수정할 수 있습니다.

### Data 삭제

#### Data 삭제 - drop

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s.drop('a')
print(s)
```

- `s.drop('a')`: s의 인덱스가 'a'인 데이터를 삭제합니다.

```shell
a    1
b    2
c    3
dtype: int64
```

s를 확인 하면 'a'가 삭제되지 않은 것을 확인할 수 있습니다. drop은 원본 데이터를 삭제하지 않습니다.

> 원본(s)에서 데이터를 삭제하려면 drop의 결과를 다시 원본(s)에 할당해야 합니다.

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s = s.drop('a')
print(s)
```

```shell
b    2
c    3
dtype: int64
```

> 또는 inplace 파라미터를 사용하여 원본 데이터를 삭제할 수 있습니다.

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s.drop('a', inplace=True)
print(s)
```

```shell
b    2
c    3
dtype: int64
```

#### Data 삭제 - del

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
del s['a']
print(s)
```

- `del s['a']`: s의 인덱스가 'a'인 데이터를 삭제합니다.

```shell
b    2
c    3
dtype: int64
```

'del'은 원본 데이터를 삭제합니다.

### index 변경

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s.index = ['d', 'e', 'f']
print(s)
```

- `s.index = ['d', 'e', 'f']`: s의 인덱스를 ['d', 'e', 'f']로 변경합니다.

```shell
d    1
e    2
f    3
dtype: int64
```

### index 참조

```python
import pandas as pd

s = pd.Series([1, 2, 3], ['a', 'b', 'c'])
s1 = pd.Series([5, 6, 7], s.index)
print(s1)
```

- `s1 = pd.Series([5, 6, 7], s.index)`: s의 인덱스를 s1의 인덱스로 사용하여 s1을 생성합니다.

```shell
a    5
b    6
c    7
dtype: int64
```

---

해시태그: #python #pandas #series #index #values #dataframe

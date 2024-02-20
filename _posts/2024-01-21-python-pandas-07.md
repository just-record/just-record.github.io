---
layout: single
title: "Python - pandas 07 (결측치)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
결측치란?

결측치는 데이터의 값이 없는 것을 의미합니다.

Sample Data는 이전 포스팅의 `타이타닉 자료(train.csv)`를 기반으로 합니다.

## 결측치 확인

### isnull(isna와 동일)

결측치 여부를 확인하는 함수입니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.isnull())
```

```shell
     PassengerId  Survived  Pclass   Name    Sex    Age  SibSp  Parch  Ticket   Fare  Cabin  Embarked
0          False     False   False  False  False  False  False  False   False  False   True     False
1          False     False   False  False  False  False  False  False   False  False  False     False
...
```

- `print(df.isnull())`: df의 결측치 여부(True, False)를 출력합니다.

### isnull().sum()

결측치의 개수를 확인하는 함수입니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.isnull().sum())
```

```shell
PassengerId      0
Survived         0
Pclass           0
Name             0
Sex              0
Age            177
SibSp            0
Parch            0
Ticket           0
Fare             0
Cabin          687
Embarked         2
dtype: int64
```

### 특정 column의 결측치 확인

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df['Age'].isnull())
print()
print(df['Age'].isnull().sum())
```

- `print(df['Age'].isnull())`: df의 'Age' column의 결측치 여부(True, False)를 출력합니다.
- `print(df['Age'].isnull().sum())`: df의 'Age' column의 결측치의 개수를 출력합니다.

```shell
0      False
1      False
2      False
...
Name: Age, Length: 891, dtype: bool

177
```

### 특정 column의 결측치가 있는 Data 조회

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.shape)

df_no_age = df[df['Age'].isnull()]

print(df_no_age.shape)
```

- `df_no_age = df[df['Age'].isnull()]`: df의 'Age' column의 결측치가 있는 Data를 df_no_age에 할당합니다.

```shell
(891, 12)
(177, 12)
```

## 결측치 처리 - dropna

`dropna`는 결측치가 row(행)을 삭제합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.shape)

df_dropna = df.dropna()
print(df_dropna.shape)
```

- `df_dropna = df.dropna()`: df의 결측치가 있는 Data를 삭제합니다.

```shell
(891, 12)
(183, 12)
```

### dropna - how

- `how='any'`: 결측치가 하나라도 있으면 삭제합니다.
- `how='all'`: 모든 값이 결측치인 경우에만 삭제합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.shape)

df_dropna_any = df.dropna(how='any')
print(df_dropna_any.shape)

df_dropna_all = df.dropna(how='all')
print(df_dropna_all.shape)
```

```shell
(891, 12)
(183, 12)
(891, 12)
```

### dropna - subset

`subset`에 명시된 column의 결측치만 삭제합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.shape)

df_dropna_subset = df.dropna(subset=['Age'])
print(df_dropna_subset.shape)
```

- `df_dropna_subset = df.dropna(subset=['Age'])`: df의 'Age' column의 결측치가 있는 Data를 삭제합니다.

```shell
(891, 12)
(714, 12)
```

### dropna - axis

- `axis=0`: row(행)을 삭제합니다.
- `axis=1`: column(열)을 삭제합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.shape)

df_dropna_row = df.dropna(axis=0)
print(df_dropna_row.shape)

df_dropna_column = df.dropna(axis=1)
print(df_dropna_column.shape)
```

- `axis=0`: row(행)을 기준으로 결측치가 있는 Data의 row(행)를 삭제합니다.
- `axis=1`: column(열)을 기준으로 결측치가 있는 Data의 column(열)를 삭제합니다.

```shell
(891, 12)
(183, 12)
(891, 9)
```

### dropna - inplace

`inplace`를 사용하면 원본 데이터를 삭제합니다. 변수에 다시 할당하지 않아도 됩니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.shape)

df.dropna(inplace=True)
print(df.shape)
```

```shell
(891, 12)
(183, 12)
```

## 결측치 처리 - fillna

`fillna`는 결측치를 다른 값으로 대체합니다.

### fillna - value

`value`에 명시된 값으로 결측치를 대체합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df[df['Age'].isnull()].head(1))
print()

df_fillna = df.fillna(value=0)
print(df_fillna.isnull().sum().sum())
print()
print(df_fillna.loc[5]['Age'])
```

- `df_fillna = df.fillna(value=0)`: df의 결측치를 0으로 대체합니다.

```shell
(common) D:\10.dev\10.python\01.common\src\91.2023_project\06.blog_temp>python main.py
   PassengerId  Survived  Pclass              Name   Sex  Age  SibSp  Parch  Ticket    Fare Cabin Embarked
5            6         0       3  Moran, Mr. James  male  NaN      0      0  330877  8.4583   NaN        Q

0

0.0
```

> 평균 값으로 대체 하기

```python
import pandas as pd

df = pd.read_csv('train.csv')

df_fillna_mean = df.fillna(value=df['Age'].mean())
print(df_fillna_mean.isnull().sum().sum())
print()
print(df_fillna_mean.loc[5]['Age'])
```

```shell
0

29.69911764705882
```

### fillna - method

- `method='ffill'`: 결측치를 이전 값으로 대체합니다. `method='pad'`와 동일합니다.
- `method='bfill'`: 결측치를 다음 값으로 대체합니다. `method='backfill'`와 동일합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.loc[4, 5, 6]['Age']])

df_fillna_ffill = df.fillna(method='ffill')
print(df_fillna_ffill.loc[5]['Age'])
print()

df_fillna_bfill = df.fillna(method='bfill')
print(df_fillna_bfill.loc[5]['Age'])
```

```shell
4    35.0
5     NaN
6    54.0
Name: Age, dtype: float64
35.0

54.0
```

### fillna - limit

`limit`에 명시된 값만큼만 결측치를 대체합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

df_fillna_limit = df.fillna(method='ffill', limit=1)
print(df_fillna_limit['Age'].isnull().sum())
```

```shell
26
```

### fillna - inplace

`inplace`를 사용하면 원본 데이터를 대체합니다. 변수에 다시 할당하지 않아도 됩니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.isnull().sum().sum())

df.fillna(value=0, inplace=True)
print(df.isnull().sum().sum())
```

```shell
866
0
```

### fillna - 각 column별로 다른 값으로 대체

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.isnull().sum().sum())
print()

dic_fill = {'Age':0, 'Cabin':'no-value'}
df.fillna(value=dic_fill, inplace=True)
print(df.isnull().sum().sum())
```

- `dic_fill = {'Age':0, 'Cabin':'no-value'}`: 'Age' column의 결측치는 0으로, 'Cabin' column의 결측치는 'no-value'로 대체합니다.

```shell
866

2
```

## 문제

'Age'가 결측치인 값을 생존자와 사망자의 각 평균 값으로 대체하기

```python
import pandas as pd

df = pd.read_csv('train.csv')

mean_age_sur = df[df['Survived'] == 1]['Age'].mean()
mean_age_nosur = df[df['Survived'] == 0]['Age'].mean()

df.loc[df['Survived'] == 1, 'Age'] = df[df['Survived'] == 1]['Age'].fillna(mean_age_sur)
df.loc[df['Survived'] == 0, 'Age'] = df[df['Survived'] == 0]['Age'].fillna(mean_age_nosur)
```

- `mean_age_sur = df[df['Survived'] == 1]['Age'].mean()`: 생존자의 평균 나이를 구합니다.
- `mean_age_nosur = df[df['Survived'] == 0]['Age'].mean()`: 사망자의 평균 나이를 구합니다.
- `df[df['Survived'] == 1]['Age'].fillna(mean_age_sur)`: 생존자 중 'Age'의 결측치를 생존자의 평균 나이로 대체합니다. Series로 반환됩니다. index는 보존 되어 있습니다.
- `df.loc[df['Survived'] == 1, 'Age'] = ...`: 위의 반환된 Series를 df의 'Age'에 할당합니다. index가 보존 되어 있는 자신에게 update됩니다.
- 사망자도 동일합니다.

---

해시태그: #python #pandas #dataframe #dropna #fillna #isnull #isna #sum #value #method #limit #inplace #결측치

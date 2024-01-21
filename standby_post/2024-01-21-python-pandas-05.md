---
layout: single
title: "Python - pandas 05 (데이터 조회)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
Sample Data는 이전 포스팅의 `타이타닉 자료(train.csv)`를 기반으로 합니다.

## Row(행) 조회

### loc

index를 명시하여 행 데이터를 조회합니다. index값과 동일한 값을 가진 행 데이터를 반환합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[3])
```

- `df.loc[3]`: index가 3인 행 데이터를 조회합니다.

```shell
PassengerId                                               4
Survived                                                  1
Pclass                                                    1
Name           Futrelle, Mrs. Jacques Heath (Lily May Peel)
Sex                                                  female
Age                                                    35.0
SibSp                                                     1
Parch                                                     0
Ticket                                               113803
Fare                                                   53.1
Cabin                                                  C123
Embarked                                                  S
Name: 3, dtype: object
```

> `df.loc[3]`과 같이 scalar 값을 입력하면 Series를 반환하고 `df.loc[[3]]`과 같이 리스트를 입력하면 DataFrame을 반환합니다.

#### loc - slicing

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[3:5])
```

- `df.loc[3:5]`: index가 3부터 5까지인 행 데이터를 조회합니다.

```shell
   PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
3            4         1       1  ...  53.1000  C123         S
4            5         0       3  ...   8.0500   NaN         S
5            6         0       3  ...   8.4583   NaN         Q

[3 rows x 12 columns]
```

#### loc - index list

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[[3, 5, 7]])
```

- `df.loc[[3, 5, 7]]`: index가 3, 5, 7인 행 데이터를 조회합니다.

```shell
   PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
3            4         1       1  ...  53.1000  C123         S
5            6         0       3  ...   8.4583   NaN         Q
7            8         0       3  ...  21.0750   NaN         S

[3 rows x 12 columns]
```

#### loc 오류

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[900])
```

- `df.loc[900]`: index가 900인 행 데이터를 조회합니다.

```shell
KeyError: 900
```

index가 900인 행 데이터가 없기 때문에 오류가 발생합니다.

### iloc

index의 값과 상관없이 주어지 값의 index 순서(순번)에 해당 하는 행 데이터를 조회합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.iloc[3])
```

- `df.iloc[3]`: 4(0부터 시작)번째 행 데이터를 조회합니다.

```shell
PassengerId                                               4
Survived                                                  1
Pclass                                                    1
Name           Futrelle, Mrs. Jacques Heath (Lily May Peel)
Sex                                                  female
Age                                                    35.0
SibSp                                                     1
Parch                                                     0
Ticket                                               113803
Fare                                                   53.1
Cabin                                                  C123
Embarked                                                  S
Name: 3, dtype: object
```

> `df.iloc[3]`과 같이 scalar 값을 입력하면 Series를 반환하고 `df.iloc[[3]]`과 같이 리스트를 입력하면 DataFrame을 반환합니다.

#### iloc - slicing

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.iloc[3:5])
```

- `df.iloc[3:5]`: 4(0부터 시작)번째부터 6번째까지 행 데이터를 조회합니다.

```shell
   PassengerId  Survived  Pclass  ...   Fare Cabin  Embarked
3            4         1       1  ...  53.10  C123         S
4            5         0       3  ...   8.05   NaN         S

[2 rows x 12 columns]
```

#### iloc - index list

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.iloc[[3, 5, 7]])
```

- `df.iloc[[3, 5, 7]]`: 4(0부터 시작)번째, 6번째, 8번째 행 데이터를 조회합니다.

```shell
   PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
3            4         1       1  ...  53.1000  C123         S
5            6         0       3  ...   8.4583   NaN         Q
7            8         0       3  ...  21.0750   NaN         S

[3 rows x 12 columns]
```

## Column(열) 조회

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.columns)
```

- `df.columns`: df의 column을 조회합니다.

```shell
Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',
       'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'],
      dtype='object')
```

### 하나의 열 조회

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df['Name'])
```

- `df['Name']`: Name 열을 조회합니다.

```shell
0                                Braund, Mr. Owen Harris
1      Cumings, Mrs. John Bradley (Florence Briggs Th...
2                                 Heikkinen, Miss. Laina
3           Futrelle, Mrs. Jacques Heath (Lily May Peel)
4                               Allen, Mr. William Henry
                             ...
886                                Montvila, Rev. Juozas
887                         Graham, Miss. Margaret Edith
888             Johnston, Miss. Catherine Helen "Carrie"
889                                Behr, Mr. Karl Howell
890                                  Dooley, Mr. Patrick
Name: Name, Length: 891, dtype: object
```

### 여러 열 조회

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df[['Survived', 'Pclass', 'Name', 'Age']])
```

- `df[['Survived', 'Pclass', 'Name', 'Age']]`: Survived, Pclass, Name, Age 열을 조회합니다.

```shell
     Survived  Pclass                                               Name   Age
0           0       3                            Braund, Mr. Owen Harris  22.0
1           1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...  38.0
2           1       3                             Heikkinen, Miss. Laina  26.0
3           1       1       Futrelle, Mrs. Jacques Heath (Lily May Peel)  35.0
4           0       3                           Allen, Mr. William Henry  35.0
..        ...     ...                                                ...   ...
886         0       2                              Montvila, Rev. Juozas  27.0
887         1       1                       Graham, Miss. Margaret Edith  19.0
888         0       3           Johnston, Miss. Catherine Helen "Carrie"   NaN
889         1       1                              Behr, Mr. Karl Howell  26.0
890         0       3                                Dooley, Mr. Patrick  32.0

[891 rows x 4 columns]
```

## Row(행) + Column(열) 조회

### loc + column

loc는 column의 이름을 명시하여 column을 조회할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[[3, 5, 7], 'Name'])
```

- `df.loc[[3, 5, 7], 'Name']`: index가 3, 5, 7인 행의 Name column을 조회합니다.

```shell
3    Futrelle, Mrs. Jacques Heath (Lily May Peel)
5                                Moran, Mr. James
7                  Palsson, Master. Gosta Leonard
Name: Name, dtype: object
```

### loc + column list

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[[3, 5, 7], ['Name', 'Age']])
```

- `df.loc[[3, 5, 7], ['Name', 'Age']]`: index가 3, 5, 7인 행의 Name, Age column을 조회합니다.

```shell
                                           Name   Age
3  Futrelle, Mrs. Jacques Heath (Lily May Peel)  35.0
5                              Moran, Mr. James   NaN
7                Palsson, Master. Gosta Leonard   2.0
```

### iloc + column

iloc는 column의 순서를 명시하여 column을 조회할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.iloc[[3, 5, 7], 3])
```

- `df.iloc[[3, 5, 7], 3]`: 4(0부터 시작)번째, 6번째, 8번째 행의 4(0부터 시작)번째 column을 조회합니다.

```shell
3    Futrelle, Mrs. Jacques Heath (Lily May Peel)
5                                Moran, Mr. James
7                  Palsson, Master. Gosta Leonard
Name: Name, dtype: object
```

### iloc + column list

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.iloc[[3, 5, 7], [3, 5]])
```

- `df.iloc[[3, 5, 7], [3, 5]]`: 4(0부터 시작)번째, 6번째, 8번째 행의 4(0부터 시작)번째, 6번째 column을 조회합니다.

```shell
                                           Name   Age
3  Futrelle, Mrs. Jacques Heath (Lily May Peel)  35.0
5                              Moran, Mr. James   NaN
7                Palsson, Master. Gosta Leonard   2.0
```

### 오류가 발생 하는 경우

#### loc - column에 순번 적용

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.loc[[3, 5, 7], 3]
```

- `df.loc[[3, 5, 7], 3]`: index가 3, 5, 7인 행의 4(0부터 시작)번째 column을 조회합니다.

```shell
KeyError: 3
```

loc에는 column에 순번을 적용할 수 없습니다.

#### iloc - column에 이름 적용

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.iloc[[3, 5, 7], 'Name']
```

- `df.iloc[[3, 5, 7], 'Name']`: 4(0부터 시작)번째, 6번째, 8번째 행의 Name column을 조회합니다.

```shell
ValueError: Location based indexing can only have [integer, integer slice (START point is INCLUDED, END point is EXCLUDED), listlike of integers, boolean array] types
```

iloc에는 column에 이름을 적용할 수 없습니다.

## 조건을 만족하는 데이터 조회

### 조건 연산자

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df['Age'] > 25)
```

- `df['Age'] > 25`: Age column의 값이 25보다 큰지 확인합니다.

```shell
0      False
1       True
2       True
3       True
4       True
       ...
886     True
887    False
888    False
889     True
890     True
Name: Age, Length: 891, dtype: bool
```

조건 연산자를 사용하면 boolean 값으로 반환됩니다.

> 반환 값이 boolean 값이기 때문에 조건 연산자를 사용하여 데이터를 조회할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

maks = df['Age'] > 25
print(df[maks])
```

- `df[maks]`: Age column의 값이 25보다 큰 데이터를 조회합니다.

```shell
     PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
1              2         1       1  ...  71.2833   C85         C
2              3         1       3  ...   7.9250   NaN         S
3              4         1       1  ...  53.1000  C123         S
4              5         0       3  ...   8.0500   NaN         S
6              7         0       1  ...  51.8625   E46         S
..           ...       ...     ...  ...      ...   ...       ...
883          884         0       2  ...  10.5000   NaN         S
885          886         0       3  ...  29.1250   NaN         Q
886          887         0       2  ...  13.0000   NaN         S
889          890         1       1  ...  30.0000  C148         C
890          891         0       3  ...   7.7500   NaN         Q

[413 rows x 12 columns]
```

### 논리 연산자(and, or, not)

```python
import pandas as pd

df = pd.read_csv('train.csv')

mask1 = df['Age'] > 25
mask2 = df['Age'] < 40

print(df[mask1 & mask2])
```

- `df[mask1 & mask2]`: Age column의 값이 25보다 크고 40보다 작은 데이터를 조회합니다.

```shell
     PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
1              2         1       1  ...  71.2833   C85         C
2              3         1       3  ...   7.9250   NaN         S
3              4         1       1  ...  53.1000  C123         S
4              5         0       3  ...   8.0500   NaN         S
8              9         1       3  ...  11.1333   NaN         S
..           ...       ...     ...  ...      ...   ...       ...
883          884         0       2  ...  10.5000   NaN         S
885          886         0       3  ...  29.1250   NaN         Q
886          887         0       2  ...  13.0000   NaN         S
889          890         1       1  ...  30.0000  C148         C
890          891         0       3  ...   7.7500   NaN         Q

[250 rows x 12 columns]
```

> 조건을 바로 적용할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df[(df['Age'] > 25) & (df['Age'] < 40)])
```

- `df[(df['Age'] > 25) & (df['Age'] < 40)]`: Age column의 값이 25보다 크고 40보다 작은 데이터를 조회합니다.
- `() & ()`와 같이 `()`로 묶어주어야 합니다.

```shell
     PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
1              2         1       1  ...  71.2833   C85         C
2              3         1       3  ...   7.9250   NaN         S
3              4         1       1  ...  53.1000  C123         S
4              5         0       3  ...   8.0500   NaN         S
8              9         1       3  ...  11.1333   NaN         S
..           ...       ...     ...  ...      ...   ...       ...
883          884         0       2  ...  10.5000   NaN         S
885          886         0       3  ...  29.1250   NaN         Q
886          887         0       2  ...  13.0000   NaN         S
889          890         1       1  ...  30.0000  C148         C
890          891         0       3  ...   7.7500   NaN         Q

[250 rows x 12 columns]
```

### loc + 조건

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[df['Age'] > 25, 'Name'])
```

- `df.loc[df['Age'] > 25, 'Name']`: Age column의 값이 25보다 큰 데이터의 Name column을 조회합니다.

```shell
1      Cumings, Mrs. John Bradley (Florence Briggs Th...
2                                 Heikkinen, Miss. Laina
3           Futrelle, Mrs. Jacques Heath (Lily May Peel)
4                               Allen, Mr. William Henry
6                                McCarthy, Mr. Timothy J
                             ...
883                        Banfield, Mr. Frederick James
885                 Rice, Mrs. William (Margaret Norton)
886                                Montvila, Rev. Juozas
889                                Behr, Mr. Karl Howell
890                                  Dooley, Mr. Patrick
Name: Name, Length: 413, dtype: object
```

---

해시태그: #python #pandas #dataframe #row #column #loc #iloc #조건 #데이터 #조회

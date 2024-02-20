---
layout: single
title: "Python - pandas 06 (데이터 변경)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
Sample Data는 이전 포스팅의 `타이타닉 자료(train.csv)`를 기반으로 합니다.

## Column 추가

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age_double'] = df['Age'] * 2
print(df.head())
```

- `df['Age_double']`: df Age_double 컬럼을 생성합니다.
- `df['Age'] * 2`: df Age 컬럼의 값에 2를 곱한 값을 Age_double 컬럼에 할당합니다.

```shell
   PassengerId  Survived  Pclass  ... Cabin Embarked  Age_double
0            1         0       3  ...   NaN        S        44.0
1            2         1       1  ...   C85        C        76.0
2            3         1       3  ...   NaN        S        52.0
3            4         1       1  ...  C123        S        70.0
4            5         0       3  ...   NaN        S        70.0
```

- `df['Age_double']`: train의 Age_double 컬럼이 생성되었습니다.
- `df['Age'] * 2`: train의 Age 컬럼의 값에 2를 곱한 값이 Age_double 컬럼에 할당되었습니다.

### Column 연산으로 추가

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age_Fare'] = df['Age'] + df['Fare']

print(df.head())
```

- `df['Age_Fare']`: train의 Age_Fare 컬럼을 생성합니다.
- `df['Age'] + df['Fare']`: train의 Age 컬럼의 값과 Fare 컬럼의 값을 더한 값을 Age_Fare 컬럼에 할당합니다.

```shell
   PassengerId  Survived  Pclass  ... Cabin Embarked  Age_Fare
0            1         0       3  ...   NaN        S   29.2500
1            2         1       1  ...   C85        C  109.2833
2            3         1       3  ...   NaN        S   33.9250
3            4         1       1  ...  C123        S   88.1000
4            5         0       3  ...   NaN        S   43.0500
```

### 빈 값으로 Column 추가

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age_double'] = None
print(df.head())
```

- `df['Age_double']`: train의 Age_double 컬럼을 생성합니다.
- `None`: train의 Age_double 컬럼에 빈 값을 할당합니다.

```shell
   PassengerId  Survived  Pclass  ... Cabin Embarked  Age_double
0            1         0       3  ...   NaN        S        None
1            2         1       1  ...   C85        C        None
2            3         1       3  ...   NaN        S        None
3            4         1       1  ...  C123        S        None
4            5         0       3  ...   NaN        S        None
```

- `df['Age_double']`: train의 Age_double 컬럼이 생성되었습니다.
- `None`: train의 Age_double 컬럼에 빈 값이 할당되었습니다.

## Column 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age_double'] = df['Age'] * 2
print(df.head())

df['Age_double'] = df['Age_double'] + 1
print(df.head())
```

- `df['Age_double']`: `Age_double`값이 없으면 column이 생성 됩니다.
- `Age_double`이 있으면 `Age_double`값이 변경 됩니다.

```shell
   PassengerId  Survived  Pclass  ... Cabin Embarked  Age_double
0            1         0       3  ...   NaN        S        44.0
1            2         1       1  ...   C85        C        76.0
2            3         1       3  ...   NaN        S        52.0
3            4         1       1  ...  C123        S        70.0
4            5         0       3  ...   NaN        S        70.0

[5 rows x 13 columns]
   PassengerId  Survived  Pclass  ... Cabin Embarked  Age_double
0            1         0       3  ...   NaN        S        45.0
1            2         1       1  ...   C85        C        77.0
2            3         1       3  ...   NaN        S        53.0
3            4         1       1  ...  C123        S        71.0
4            5         0       3  ...   NaN        S        71.0

[5 rows x 13 columns]
```

## Column 삭제

### drop

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age_double'] = df['Age'] * 2
print(df.columns)

print()

df = df.drop('Age_double', axis=1)
print(df.columns)
```

- `df.drop('Age_double', axis=1)`: df의 Age_double 컬럼을 삭제합니다.
- `axis=1`: 축을 column(열)로 설정합니다.
- `drop`는 원본 데이터를 변경하지 않습니다. 따라서, `df = df.drop('Age_double', axis=1)`으로 재할당 해줘야 합니다.
- `inplace=True`을 사용하면 원본 데이터를 변경합니다.
  - `df.drop('Age_double', axis=1, inplace=True)`

```shell
Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',
       'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked', 'Age_double'],
      dtype='object')

Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',
       'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'],
      dtype='object')
```

### del

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age_double'] = df['Age'] * 2

del df['Age_double']
print(df.columns)
```

- `del df['Age_double']`: df의 Age_double 컬럼을 삭제합니다.
- `del`은 원본 데이터를 변경합니다.

### 여러 Column 삭제

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age_double'] = df['Age'] * 2
df['Age_triple'] = df['Age'] * 3
print(df.columns)

print()

df = df.drop(['Age_double', 'Age_triple'], axis=1)
print(df.columns)
```

- `df.drop(['Age_double', 'Age_triple'], axis=1)`: df의 Age_double, Age_triple 컬럼을 삭제합니다.

## Row 추가

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.index)

df.loc[891] = 0
print(df.tail(2))
```

- `df.loc[891]`: df의 index값이 `891`이고 값이 `0`인 row를 추가합니다.
- `df.loc[891] = 0`은 `df.loc[891, :] = 0`과 같습니다.

```shell
     PassengerId  Survived  Pclass                 Name   Sex   Age  SibSp  Parch  Ticket  Fare Cabin Embarked
890          891         0       3  Dooley, Mr. Patrick  male  32.0      0      0  370376  7.75   NaN        Q
891            0         0       0                    0     0   0.0      0      0       0  0.00     0        0
```

### list로 Row 추가

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.loc[891] = [892, 0, 1, 'test', 0, 0, 0, 0, 0, 0, 0, 0]
print(df.tail(2))
```

- `df.loc[891]`: df의 index값이 `891`이고 `[892, 0, 1, 'test', 0, 0, 0, 0, 0, 0, 0, 0]`인 row를 추가합니다.

```shell
     PassengerId  Survived  Pclass                 Name   Sex   Age  SibSp  Parch  Ticket  Fare Cabin Embarked
890          891         0       3  Dooley, Mr. Patrick  male  32.0      0      0  370376  7.75   NaN        Q
891          892         0       1                 test     0   0.0      0      0       0  0.00     0        0
```

### dict로 Row 추가

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.loc[891] = {
   'PassengerId':1, 'Survived':1, 'Pclass':2, 'Name':'g', 
   'Sex':'female', 'Age':68.0, 'SibSp':1,
   'Parch':0, 'Ticket':'h', 'Fare':1, 'Cabin':'j', 'Embarked':'k'
   }

print(df.tail(2))
```

```shell
     PassengerId  Survived  Pclass                 Name     Sex   Age  SibSp  Parch  Ticket  Fare Cabin Embarked
890          891         0       3  Dooley, Mr. Patrick    male  32.0      0      0  370376  7.75   NaN        Q
891            1         1       2                    g  female  68.0      1      0       h  1.00     j        k
```

## Row 변경

### list로 Row 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.loc[890] = [892, 0, 1, 'test', 0, 0, 0, 0, 0, 0, 0, 0]
print(df.tail(2))
```

- `df.loc[890]`: df의 index값이 `890`인 row의 값을 `[892, 0, 1, 'test', 0, 0, 0, 0, 0, 0, 0, 0]`로 변경합니다.

```shell
     PassengerId  Survived  Pclass                   Name   Sex   Age  SibSp  Parch  Ticket  Fare Cabin Embarked
889          890         1       1  Behr, Mr. Karl Howell  male  26.0      0      0  111369  30.0  C148        C
890          892         0       1                   test     0   0.0      0      0       0   0.0     0        0
```

### dict로 Row 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.loc[890] = {
   'PassengerId':1, 'Survived':1, 'Pclass':2, 'Name':'g', 
   'Sex':'female', 'Age':68.0, 'SibSp':1,
   'Parch':0, 'Ticket':'h', 'Fare':1, 'Cabin':'j', 'Embarked':'k'
   }

print(df.tail(2))
```

```shell
     PassengerId  Survived  Pclass                   Name     Sex   Age  SibSp  Parch  Ticket  Fare Cabin Embarked
889          890         1       1  Behr, Mr. Karl Howell    male  26.0      0      0  111369  30.0  C148        C
890            1         1       2                      g  female  68.0      1      0       h   1.0     j        k
```

## Row 삭제

```python
import pandas as pd

df = pd.read_csv('train.csv')

df = df.drop(0, axis=0)
print(df.head(2))
```

- `df.drop(0, axis=0)`: df의 index값이 `0`인 row를 삭제합니다.
- `axis=0`: 축을 row(행)로 설정합니다.
- `drop`은 원본 데이터를 변경하지 않습니다. 따라서, `df = df.drop(0, axis=0)`으로 재할당 해줘야 합니다.
- `inplace=True`을 사용하면 원본 데이터를 변경합니다.
  - `df.drop(0, axis=0, inplace=True)`

```shell
   PassengerId  Survived  Pclass                                               Name  ...            Ticket     Fare  Cabin  Embarked
1            2         1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...  ...          PC 17599  71.2833    C85         C       
2            3         1       3                             Heikkinen, Miss. Laina  ...  STON/O2. 3101282   7.9250    NaN         S       

[2 rows x 12 columns]
```

## loc를 사용하여 data 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.loc[3, 'Name'] = 'No name'

print(df.loc[3]['Name'])
```

- `df.loc[df['Age'] < 10, 'Age']`: df의 Age 컬럼의 값이 10보다 작은 row의 Age 컬럼의 값을 0으로 변경합니다.
- `loc`는 원본 데이터를 바로 변경 합니다. 따라서, `df = df.loc[df['Age'] < 10, 'Age']`으로 재할당 해줄 필요도 없으며 `inplace=True`도 필요 없습니다.

```shell
No name
```

### loc + 조건

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.loc[df['Age'] > 60, 'Name'] = 'old'

print(df[df['Age'] > 60].head()['Name'])
```

```shell
33     old
54     old
96     old
116    old
170    old
Name: Name, dtype: object
```

## Column 타입 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df['Survived'].dtypes)

print()

df = df.astype({'Survived': str})
print(df['Survived'].dtypes)
```

```shell
int64

object
```

### NaN 값이 있는 경우 오류 발생

```python
import pandas as pd

df = pd.read_csv('train.csv')

df = df.astype({'Age': int})
print(df['Age'].dtypes)
```

```shell
pandas.errors.IntCastingNaNError: Cannot convert non-finite values (NA or inf) to integer: Error while type casting for column 'Age'
```

> Nan 처리 후 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age'] = df['Age'].fillna(0)
df = df.astype({'Age': int})
print(df['Age'].dtypes)
```

```shell
int32
```

### Series로 타입 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Age'] = df['Age'].astype(float)
print(df['Age'].dtypes)
```

```shell
float64
```

- `df['Age'] = df['Age'].astype(float)`: Series의 데이터 타입을 변경하고 다시 할당합니다.

## Column 이름 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.columns = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l']
print(df.columns)
```

```shell
Index(['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l'], dtype='object')
```

### 특정 Column 이름 변경

```python
import pandas as pd

df = pd.read_csv('train.csv')

df.rename(columns={'Name': 'English_Name'}, inplace=True)
print(df.columns)
```

```shell
Index(['PassengerId', 'Survived', 'Pclass', 'English_Name', 'Sex', 'Age',
       'SibSp', 'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'],
      dtype='object')
```

---

해시태그: #python #pandas #dataframe #row #column #add #change #delete #loc #astype

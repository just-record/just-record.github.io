---
layout: single
title: "Python - pandas 08 (자료 형태)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
Sample Data는 이전 포스팅의 `타이타닉 자료(train.csv)`를 기반으로 합니다.

## 자료 형태

자료의 형태는 다음과 같이 구분할 수 있습니다.

### 범주형

데이터가 몇 개의 범주 또는 그룹으로 분류될 수 있는 경우에 사용됩니다. 이 데이터는 주로 문자열 형태로 나타나며, 수치적 의미를 가지지 않습니다. 명목형과 순서형으로 구분됩니다.

#### 명목형

서로 구분되는 범주나 이름을 가진 데이터입니다. 이 데이터 유형에서는 순서나 크기를 의미하는 것이 없으며, 단순히 항목을 구별하는 데 사용됩니다. 예를 들어, 사람의 혈액형(A, B, AB, O), 성별(남성, 여성), 국가 이름 등이 명목형 데이터에 해당합니다.

타이타닉 자료에서는 `Sex`, `Embarked`가 명목형 데이터에 해당합니다.

#### 순서형

명목형과 유사하지만, 범주 간에 명확한 순서가 있는 데이터 유형입니다. 이 데이터는 서열화가 가능하지만, 범주 간의 간격이 균등하지 않을 수 있습니다. 예를 들어, 교육 수준(초등학교, 중학교, 고등학교, 대학교), 제품 평가(매우 불만족, 불만족, 보통, 만족, 매우 만족) 등이 순서형 데이터에 해당합니다.

타이타닉 자료에서는 `Pclass`가 순서형 데이터에 해당합니다.

### 수치형

숫자 형태로 나타나며, 수량적 측정이 가능한 데이터입니다. 이 데이터는 계산이 가능합니다. 이산형과 연속형으로 구분됩니다.

#### 이산형

특정 값들 사이에 중간값이 존재하지 않는, 셀 수 있는 값으로 이루어진 데이터입니다. 이산형 데이터는 주로 개수를 나타내며, 정수 형태를 띠는 경우가 많습니다. 예를 들어, 학급의 학생 수, 책의 페이지 수, 가게의 판매 건수 등이 이산형 데이터에 속합니다.

타이타닉 자료에서는 `SibSp`, `Parch`가 이산형 데이터에 해당합니다.

#### 연속형

연속적인 값을 가지며, 어떤 두 값 사이에도 무한히 많은 값들이 존재할 수 있습니다. 이 데이터는 보통 실수의 형태를 띠며, 어떤 범위 내에서 어떤 값이든 취할 수 있습니다. 예를 들어, 사람의 키나 몸무게, 온도, 시간 등이 연속형 데이터에 해당합니다.

타이타닉 자료에서는 `Age`, `Fare`가 연속형 데이터에 해당합니다.

## 수치형을 범주형으로 변환하기

'Pclass'는 수치형 데이터이지만, 데이터의 값은 1, 2, 3으로 객샐 등급의 범주로 이루어져 있습니다. 이를 범주형 데이터로 변환해 보겠습니다.

### astype

데이터 타입을 변경하는 함수입니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df['Pclass'].dtypes)

df['Pclass'] = df['Pclass'].astype('object')
print(df['Pclass'].dtypes)
```

- `df['Pclass'] = df['Pclass'].astype('object')`: `Pclass` 열의 데이터 타입을 `object`로 변경합니다.

```shell
int64
object
```

## 범주형을 수치형으로 변환하기

조금 전 'Pclass'를 범주형 데이터로 변환 했습니다. 연습 삼아 수치형 데이터로 변환해 보겠습니다.

### pd.to_numeric

데이터를 수치형으로 변환하는 함수입니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
df['Pclass'] = df['Pclass'].astype('object')
print(df['Pclass'].dtypes)

df['Pclass'] = pd.to_numeric(df['Pclass'])
print(df['Pclass'].dtypes)
```

- `df['Pclass'] = pd.to_numeric(df['Pclass'])`: `Pclass` 열의 데이터 타입을 `int64`로 변경합니다.

```shell
object
int64
```

### pd.to_numeric - errors

먼저 수치형으로 변환할 수 없는 데이터가 있으면 어떻게 되는지 확인해 보겠습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Pclass'] = df['Pclass'].astype('object')
print(df['Pclass'].dtypes)

# 수치형으로 변환할 수 없는 데이터 생성
df.loc[0, 'Pclass'] = 'a'

df['Pclass'] = pd.to_numeric(df['Pclass'])
print(df['Pclass'].dtypes)
```

```shell
ValueError: Unable to parse string "a" at position 0
```

- 'a'값으로 인해 ValueError가 발생합니다.

#### errors - coerce

`errors` 옵션을 이용하여 변환할 수 없는 데이터가 있을 경우 어떻게 처리할지 설정할 수 있습니다. 'coerce'는 변환할 수 없는 데이터를 `NaN`으로 처리합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Pclass'] = df['Pclass'].astype('object')
print(df['Pclass'].dtypes)

df.loc[0, 'Pclass'] = 'a'

df['Pclass'] = pd.to_numeric(df['Pclass'], errors='coerce')
print(df['Pclass'].dtypes)

print(df.loc[df['Pclass'].isnull(), 'Pclass'])
```

- `errors='coerce'`: 변환할 수 없는 데이터는 `NaN`으로 처리합니다.

```shell
object
float64
0   NaN
Name: Pclass, dtype: float64
```

#### errors - ignore

'ignore'는 변환할 수 없는 데이터가 존재해도 오류가 발생하지 않습니다. 다만 data type이 변환되지 않습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

df['Pclass'] = df['Pclass'].astype('object')
print(df['Pclass'].dtypes)

df.loc[0, 'Pclass'] = 'a'

df['Pclass'] = pd.to_numeric(df['Pclass'], errors='ignore')
print(df['Pclass'].dtypes)
```

- `errors='coerce'`: 변환할 수 없는 데이터는 `NaN`으로 처리합니다.

```shell
object
object
```

## apply 함수

`apply` 함수는 행 또는 열 단위로 함수를 적용합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[0:4, 'Age'])
print()

def add_10(n):
    return n + 10

df['Age'] = df['Age'].apply(add_10)

print(df.loc[0:4, 'Age'])
```

- `df['Age'] = df['Age'].apply(add_10)`: `Age` 열의 데이터에 `add_10` 함수를 적용합니다.

```shell
0    22.0
1    38.0
2    26.0
3    35.0
4    35.0
Name: Age, dtype: float64

0    32.0
1    48.0
2    36.0
3    45.0
4    45.0
Name: Age, dtype: float64
```

### apply 함수 - lambda

`lambda`를 이용하여 함수를 만들어 `apply` 함수에 적용할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[0:4, 'Age'])
print()

df['Age'] = df['Age'].apply(lambda x: x + 10)

print(df.loc[0:4, 'Age'])
```

- `df['Age'] = df['Age'].apply(lambda x: x + 10)`: `Age` 열의 데이터에 `lambda` 함수를 적용합니다.

```shell
0    22.0
1    38.0
2    26.0
3    35.0
4    35.0
Name: Age, dtype: float64

0    32.0
1    48.0
2    36.0
3    45.0
4    45.0
Name: Age, dtype: float64
```

### apply 함수 - 여러 개의 column에 적용하기

`apply` 함수를 이용하여 여러 개의 column에 함수를 적용할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

print(df.loc[0:4, ['Age', 'Fare']])
print()

def add_two_obj(a, b):
    return a + b

df['Add'] = df.apply(lambda x: add_two_obj(x['Age'], x['Fare']), axis=1)

print(df.loc[0:4, ['Age', 'Fare', 'Add']])
```

조금 전까지는 df['Age'](Series)에만 apply를 적용했지만, 이번 에는 df(DataFrame)에 apply를 적용합니다. lambda 함수의 인수에 dataframe의 column을 한 개 이상 지정하여 함수를 적용합니다. 반드시 axis=1을 지정하여야 합니다. axis=0은 행에 적용하는 것이고, axis=1은 열에 적용하는 것입니다.

- `df['Add'] = df.apply(lambda x: add_two_obj(x['Age'], x['Fare']), axis=1)`: `Age`와 `Fare` 열의 데이터에 `add_two_obj` 함수를 적용하여 `Add` 열을 생성합니다.

```shell
    Age     Fare
0  22.0   7.2500
1  38.0  71.2833
2  26.0   7.9250
3  35.0  53.1000
4  35.0   8.0500

    Age     Fare       Add
0  22.0   7.2500   29.2500
1  38.0  71.2833  109.2833
2  26.0   7.9250   33.9250
3  35.0  53.1000   88.1000
4  35.0   8.0500   43.0500
```

## one-hot encoding

범주형은 데이터를 분석 또는 학습 하기가 곤란합니다. 수치형 데이터로 변환하여 분석 또는 학습을 진행 합니다. 이번에는 범주형 데이터를 수치형 데이터로 변환하는 방법 중 하나인 one-hot encoding에 대해 알아보겠습니다.

'one-hot encoding'은 범주의 수만큼 column을 생성하고, 해당 범주에 해당하는 column에 1을 표시하고 나머지 column에는 0을 표시하는 방법입니다.

### get_dummies

pandas는 `get_dummies` 함수를 이용하여 one-hot encoding을 수행합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

df = pd.get_dummies(df, columns=['Pclass', 'Sex', 'Embarked'])

print(df.columns)
print()
print(df.loc[:2, ['Pclass_1', 'Pclass_2', 'Pclass_3', 'Sex_female', 'Sex_male', 'Embarked_C', 'Embarked_Q', 'Embarked_S']])
```

```shell
Index(['PassengerId', 'Survived', 'Name', 'Age', 'SibSp', 'Parch', 'Ticket',
       'Fare', 'Cabin', 'Pclass_1', 'Pclass_2', 'Pclass_3', 'Sex_female',
       'Sex_male', 'Embarked_C', 'Embarked_Q', 'Embarked_S'],
      dtype='object')

   Pclass_1  Pclass_2  Pclass_3  Sex_female  Sex_male  Embarked_C  Embarked_Q  Embarked_S
0     False     False      True       False      True       False       False        True
1      True     False     False        True     False        True       False       False
2     False     False      True        True     False       False       False        True
```

### get_dummies - drop_first

`get_dummies` 함수는 기본적으로 모든 범주형 column을 변환합니다. 이때, 첫 번째 범주를 기준으로 변환합니다. `drop_first=True` 옵션을 추가하면 첫 번째 범주를 제외하고 변환합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

df = pd.get_dummies(df, columns=['Pclass', 'Sex', 'Embarked'], drop_first=True)

print(df.columns)
print()
print(df.loc[:2, ['Pclass_2', 'Pclass_3', 'Sex_male', 'Embarked_Q','Embarked_S']])
```

```shell
Index(['PassengerId', 'Survived', 'Name', 'Age', 'SibSp', 'Parch', 'Ticket',
       'Fare', 'Cabin', 'Pclass_2', 'Pclass_3', 'Sex_male', 'Embarked_Q',
       'Embarked_S'],
      dtype='object')

   Pclass_2  Pclass_3  Sex_male  Embarked_Q  Embarked_S
0     False      True      True       False        True
1     False     False     False       False       False
2     False      True     False       False        True
```

---

해시태그: #python #pandas #dataframe #자료형태 #범주형 #수치형 #명목형 #순서형 #이산형 #연속형 #astype #pd.to_numeric #apply #lambda #one-hot_encoding #get_dummies

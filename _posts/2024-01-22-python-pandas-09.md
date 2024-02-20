---
layout: single
title: "Python - pandas 09 (정렬/분석함수)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
Sample Data는 이전 포스팅의 `타이타닉 자료(train.csv)`를 기반으로 합니다.

## 데이터 정렬

### index를 기준으로 정렬

`sort_index()` 함수를 사용합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

df = df.sort_index()
print(df.head(3))
```

- sort_index() 함수는 기본적으로 오름차순으로 정렬됩니다.

```python
   PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
0            1         0       3  ...   7.2500   NaN         S
1            2         1       1  ...  71.2833   C85         C
2            3         1       3  ...   7.9250   NaN         S
```

#### index를 기준으로 내림차순 정렬

`ascending=False` 옵션을 주면 내림차순으로 정렬됩니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')

df = df.sort_index(ascending=False)
print(df.head(3))
```

```python
     PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
890          891         0       3  ...   7.7500   NaN         Q
889          890         1       1  ...  30.0000  C148         C
888          889         0       3  ...  23.4500   NaN         S
```

### 특정 열을 기준으로 정렬

`sort_values()` 함수를 사용합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
df = df.sort_values(by='Age', ascending=False)
print(df.head(3))
```

`ascending=False` 옵션을 주면 내림차순으로 정렬됩니다.

#### 여러 열을 기준으로 정렬

`sort_values()` 함수에 여러 열을 리스트로 입력합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
df.sort_values(by=['Pclass', 'Age'])
```

`Pclass`를 기준으로 먼저 정렬되고, 그 안에서 `Age`를 기준으로 정렬됩니다.

#### 특정 열에 대해 정렬 순서를 다르게

`ascending` 옵션을 리스트로 입력합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
df.sort_values(by=['Pclass', 'Age'], ascending=[False, True])
```

`Pclass`는 내림차순, `Age`는 오름차순으로 정렬됩니다.

### axis=1

`axis=1` 옵션을 주면 열을 기준으로 정렬됩니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
df = df.sort_index(axis=1)
print(df.head(3))
```

```python
    Age Cabin Embarked     Fare  ...     Sex  SibSp  Survived            Ticket
0  22.0   NaN        S   7.2500  ...    male      1         0         A/5 21171
1  38.0   C85        C  71.2833  ...  female      1         1          PC 17599
2  26.0   NaN        S   7.9250  ...  female      0         1  STON/O2. 3101282
```

> `ascending=False` 옵션을 주면 내림차순으로 정렬됩니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
df = df.sort_index(axis=1, ascending=False)
print(df.head(3))
```

```python
             Ticket  Survived  SibSp     Sex  ...     Fare  Embarked  Cabin   Age
0         A/5 21171         0      1    male  ...   7.2500         S    NaN  22.0
1          PC 17599         1      1  female  ...  71.2833         C    C85  38.0
2  STON/O2. 3101282         1      0  female  ...   7.9250         S    NaN  26.0
```

## 데이터 분석 함수

### 평균값 구하기

`mean()` 함수를 사용합니다.

```python
df['Age'].mean()
```

### 특정 열의 중앙값 구하기

`median()` 함수를 사용합니다.

```python
df['Age'].median()
```

### 특정 열의 최댓값 구하기

`max()` 함수를 사용합니다.

```python
df['Age'].max()
```

### 특정 열의 최솟값 구하기

`min()` 함수를 사용합니다.

```python
df['Age'].min()
```

### 특정 열의 표준편차 구하기

`std()` 함수를 사용합니다.

```python
df['Age'].std()
```

### 특정 열의 분산 구하기

`var()` 함수를 사용합니다.

```python
df['Age'].var()
```

### 특정 열의 사분위수

`quantile()` 함수를 사용합니다.

```python
df['Age'].quantile(0.25)
df['Age'].quantile(0.5)
df['Age'].quantile(0.75)
```

### 특정 열의 요약 정보

`describe()` 함수를 사용합니다.

```python
df['Age'].describe()
```

### 특정 열의 유일한 값 구하기

`unique()` 함수를 사용합니다.

```python
df['Age'].unique()
```

### 특정 열의 유일한 항목의 개수 구하기

`nunique()` 함수를 사용합니다.

```python
df['Age'].nunique()
```

### 특정 열의 유일한 값의 개수 구하기

`value_counts()` 함수를 사용합니다.

```python
df['Age'].value_counts()
```

### 특정 열의 가장 많이 나타나는 값 구하기

`mode()` 함수를 사용합니다.

```python
df['Age'].mode()
```

### 특정 열의 누적 합 구하기

`cumsum()` 함수를 사용합니다.

```python
df['Age'].cumsum()
```

---

해시태그: #python #pandas #dataframe #정렬 #분석함수 #mean #median #max #min #std #var #quantile #describe #unique #nunique #value_counts #mode #cumsum

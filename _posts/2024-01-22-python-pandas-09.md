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

print(df.sort_index().head(3))
# print(df.sort_index(axis=1))
# print(df.sort_index(ignore_index=True))
# print(df.sort_index(na_position='first'))
# print(df.sort_index(na_position='last'))
```

- sort_index() 함수는 기본적으로 오름차순으로 정렬됩니다.

<!-- - `ascending=False` 옵션을 주면 내림차순으로 정렬됩니다.
- `axis=1` 옵션을 주면 열을 기준으로 정렬됩니다.
- `na_position='first'` 옵션을 주면 결측치가 먼저 정렬됩니다.
- `na_position='last'` 옵션을 주면 결측치가 나중에 정렬됩니다.
- `ignore_index=True` 옵션을 주면 인덱스가 재정렬됩니다.
- `inplace=True` 옵션을 주면 원본 데이터프레임이 정렬됩니다. -->

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

df.sort_index(ascending=False).head(3)
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
df.sort_values(by='Age', ascending=False)
```

`ascending=False` 옵션을 주면 내림차순으로 정렬됩니다.

### 여러 열을 기준으로 정렬

`sort_values()` 함수에 여러 열을 리스트로 입력합니다.

```python
df.sort_values(by=['Pclass', 'Age'])
```

`Pclass`를 기준으로 먼저 정렬되고, 그 안에서 `Age`를 기준으로 정렬됩니다.

### 특정 열에 대해 정렬 순서를 다르게

`ascending` 옵션을 리스트로 입력합니다.

```python
df.sort_values(by=['Pclass', 'Age'], ascending=[False, True])
```

`Pclass`는 내림차순, `Age`는 오름차순으로 정렬됩니다.

## 데이터 분석 함수


## 데이터 분석 함수

### 특정 열의 평균값 구하기

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



---
해시태그: #python #pandas #dataframe #자료형태 #범주형 #수치형 #명목형 #순서형 #이산형 #연속형 #astype #pd.to_numeric #apply #lambda #one-hot_encoding #get_dummies

---

해시태그: #python #pandas #dataframe #자료형태 #범주형 #수치형 #명목형 #순서형 #이산형 #연속형 #astype #pd.to_numeric #apply #lambda #one-hot_encoding #get_dummies

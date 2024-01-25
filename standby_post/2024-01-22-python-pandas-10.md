---
layout: single
title: "Python - pandas 10 (groupby)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
Sample Data는 이전 포스팅의 `타이타닉 자료(train.csv)`를 기반으로 합니다.

## 데이터 집계(groupby)

'groupby'는 데이터를 그룹 별로 나누어 집계를 수행하는 함수입니다. SQL의 `groupby`와 비슷합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
gb = df.groupby('Pclass')
print(gb)
```

```python
<pandas.core.groupby.generic.DataFrameGroupBy object at 0x000001187EC4EE00>
```

'gb'는 `DataFrameGroupBy` 객체입니다. `DataFrameGroupBy` 객체는 `DataFrame`을 그룹 별로 나누어 저장하고 있습니다.

> 'gb'를 이용하여 그룹 별로 집계를 수행할 수 있습니다. 먼저 'Age'의 평균을 구해보겠습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
gb = df.groupby('Pclass')
print(gb['Age'].mean())
```

```python
Pclass        
1    38.233441
2    29.877630
3    25.140620
Name: Age, dtype: float64
```

'Pclass'가 그룹별(1, 2, 3) 'Age'의 평균을 구했습니다.

> 'gb'를 할당하는 중간 과정 없이 바로 집계를 수행할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].mean())
```

> 여러 개의 컬럼을 그룹 별로 집계할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')[['Age', 'Fare']].mean())
```

```python
              Age       Fare
Pclass
1       38.233441  84.154687
2       29.877630  20.662183
3       25.140620  13.675550
```

## 집계 함수

### 평균(mean)

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].mean())
```

```python
Pclass
1    38.233441
2    29.877630
3    25.140620
Name: Age, dtype: float64
```

### 중앙값(median)

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].median())
```

```python
Pclass
1    37.0
2    29.0
3    24.0
Name: Age, dtype: float64
```

### 최대값(max)

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].max())
```

```python
Pclass
1    80.0
2    70.0
3    74.0
Name: Age, dtype: float64
```

### 최소값(min)

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].min())
```

```python
Pclass
1    0.92
2    0.67
3    0.42
Name: Age, dtype: float64
```

### 표준편차(std)

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].std())
```

```python
Pclass
1    14.802856
2    14.001077
3    12.495398
Name: Age, dtype: float64
```

### 분산(var)

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].var())
```

```python
Pclass
1    219.525490
2    196.183266
3    156.781904
Name: Age, dtype: float64
```

### 사분위수(quantile)

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].quantile(0.25))
```

```python
Pclass
1    27.0
2    23.0
3    18.0
Name: Age, dtype: float64
```

### 기술통계(describe)

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].describe())
```

```python
        count       mean        std   min   25%   50%   75%   max
Pclass
1       186.0  38.233441  14.802856  0.92  27.0  37.0  49.0  80.0
2       173.0  29.877630  14.001077  0.67  23.0  29.0  36.0  70.0
3       355.0  25.140620  12.495398  0.42  18.0  24.0  32.0  74.0
```

## 2개 이상의 컬럼으로 그룹화

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby(['Pclass', 'Sex'])['Age'].mean())
```

```python
Pclass  Sex   
1       female    34.611765
        male      41.281386
2       female    28.722973
        male      30.740707
3       female    21.750000
        male      26.507589
Name: Age, dtype: float64
```

'Pclass'와 'Sex'의 조합으로 그룹화하여 'Age'의 평균을 구했습니다.

> 여러 개의 컬럼을 그룹 별로 집계할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby(['Pclass', 'Sex'])[['Age', 'Fare']].mean())
```

```python
                     Age        Fare
Pclass Sex
1      female  34.611765  106.125798
       male    41.281386   67.226127
2      female  28.722973   21.970121
       male    30.740707   19.741782
3      female  21.750000   16.118810
       male    26.507589   12.661633
```

'Pclass'와 'Sex'의 조합으로 그룹화하고 'Age'와 'Fare'의 평균을 구했습니다.

## 확인 문제

> 성별에 따른 생존률 구하기

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Sex')['Survived'].mean())
```

```python
Sex
female    0.742038
male      0.188908
Name: Survived, dtype: float64
```

> 선실 등급에 따른 생존률 구하기

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Survived'].mean())
```

```python
Pclass
1    0.629630
2    0.472826
3    0.242363
Name: Survived, dtype: float64
```

> 성별과 선실 등급에 따른 생존률 구하기

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby(['Sex', 'Pclass'])['Survived'].mean())
```

```python
Sex     Pclass
female  1         0.968085
        2         0.921053
        3         0.500000
male    1         0.368852
        2         0.157407
        3         0.135447
Name: Survived, dtype: float64
```

> 성별과 선실 등급에 따른 생존자 수 구하기

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby(['Sex', 'Pclass'])['Survived'].sum())
```

```python
Sex     Pclass
female  1         91
        2         70
        3         72
male    1         45
        2         17
        3         47
Name: Survived, dtype: int64
```

## aggregate 함수

`aggregate` 함수는 여러 개의 집계 함수를 동시에 사용할 수 있습니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')[['Age']].aggregate(['max', 'mean', 'sum', 'count', 'median']))
```

```python
         Age
         max       mean      sum count median
Pclass
1       80.0  38.233441  7111.42   186   37.0
2       70.0  29.877630  5168.83   173   29.0
3       74.0  25.140620  8924.92   355   24.0
```

'Pclass'로 그룹화하여 'Age'의 최대값, 평균, 합계, 개수, 중앙값을 구했습니다.

> 'agg'와 'aggregate'는 동일한 함수입니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')[['Age']].agg(['max', 'mean', 'sum', 'count', 'median']))
```

```python
         Age
         max       mean      sum count median
Pclass
1       80.0  38.233441  7111.42   186   37.0
2       70.0  29.877630  5168.83   173   29.0
3       74.0  25.140620  8924.92   355   24.0
```

'Pclass'로 그룹화하여 'Age'의 최대값, 평균, 합계, 개수, 중앙값을 구했습니다.

### column별 다른 집계 함수

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass').agg({'Age':'mean', 'Fare':'max'}))
```

```python
              Age      Fare
Pclass
1       38.233441  512.3292
2       29.877630   73.5000
3       25.140620   69.5500
```

'Pclass'로 그룹화하여 'Age'의 평균과 'Fare'의 최대값을 구했습니다.

### column별 여러 집계 함수

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass').agg({'Age':['max', 'min'], 'Fare':['mean', 'median']}))
```

```python
         Age             Fare
         max   min       mean   median
Pclass
1       80.0  0.92  84.154687  60.2875
2       70.0  0.67  20.662183  14.2500
3       74.0  0.42  13.675550   8.0500
```

'Pclass'로 그룹화하여 'Age'의 최대값, 최소값과 'Fare'의 평균, 중앙값을 구했습니다.

## Index를 Column으로 변환

Grouping을 하면 Grouping한 Column이 Index로 변환됩니다. 상황에 따라 Index를 Column으로 변환해야 할 때가 있습니다. 이때는 `reset_index` 함수를 사용합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.groupby('Pclass')['Age'].mean())
print()
print(df.groupby('Pclass')['Age'].mean().reset_index())
```

```python
Pclass
1    38.233441
2    29.877630
3    25.140620
Name: Age, dtype: float64

   Pclass        Age
0       1  38.233441
1       2  29.877630
2       3  25.140620
```

---

해시태그: #python #pandas #dataframe #groupby #aggregate #agg #reset_index

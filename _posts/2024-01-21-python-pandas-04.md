---
layout: single
title: "Python - pandas 04 (CSV 파일 분석)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
## 타이타닉 자료

파일명: `train.csv`

[자료출처: https://www.kaggle.com/hesh97/titanicdataset-traincsv/data](https://www.kaggle.com/hesh97/titanicdataset-traincsv/data)

| 항목 | 설명 |
| ------ | ------ |
|PassengerId|승객ID|
|Survived|생존여부|
|Pclass|객실등급|
|Name|승객명|
|Sex|성별|
|Age|나이|
|SibSp|타이타닉에 탑승한 형제/배우자의 수|
|Parch|타이타닉에 탑승한 부모/자녀의 수|
|Ticket|티켓번호|
|Fare|승객 요금|
|Cabin|객실 번호|
|Embarked|탑승 위치 C, Q, S|

## CSV 파일 읽기

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df)
```

- `pd.read_csv('train.csv')`: CSV 파일을 읽어 DataFrame을 생성합니다.

```shell
     PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
0              1         0       3  ...   7.2500   NaN         S
1              2         1       1  ...  71.2833   C85         C
...
```

- `print(df)`: df를 출력합니다.

### read_csv의 주요 인자

- `filepath_or_buffer`: 파일 경로 또는 버퍼
- `sep`: 필드 구분자 (기본값: ',')
- `delimiter`: 필드 구분자 (기본값: None)
- `header`: 헤더 행의 번호 또는 헤더 이름 (기본값: 'infer')
- `names`: 열 이름으로 사용할 문자열의 리스트 (기본값: None)
- `index_col`: 행 인덱스로 사용할 열의 번호 또는 이름 (기본값: None)
- `usecols`: 반환할 열의 인덱스 또는 열 이름의 리스트 (기본값: None)

> 적용 예시

```python
import pandas as pd

df = pd.read_csv('train.csv', index_col='PassengerId', usecols=['PassengerId', 'Survived', 'Pclass', 'Name'])
print(df)
```

- `index_col='PassengerId'`: PassengerId를 index로 사용합니다.
- `usecols`: PassengerId, Survived, Pclass, Name 열만 사용합니다.

```shell
             Survived  Pclass                                               Name
PassengerId
1                   0       3                            Braund, Mr. Owen Harris
2                   1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...
...
```

## 탐색적 데이터 분석(EDA)

### ED(AExploratory Data Analysis)란?

탐색적 데이터 분석(Exploratory Data Analysis)은 데이터 분석의 초기 단계에서 수행되는 중요한 과정으로 데이터를 '탐색'하고 '이해'하는 것입니다. 데이터 세트 내의 숨겨진 패턴, 이상치, 구조 및 중요한 변수들을 발견하고자 합니다.

EDA의 중요성

- 데이터 이해: 데이터의 기본적인 구조, 타입 및 분포를 이해합니다.
- 이상치 발견: 데이터에서 이상치나 예외적인 값들을 찾아냅니다.
- 패턴과 관계 식별: 변수 간의 관계 및 패턴을 식별합니다.
- 가설 수립: 분석 결과를 바탕으로 가설을 세우고, 이를 추후 분석에서 검증할 수 있습니다.
- 데이터 전처리 결정: EDA를 통해 데이터 정제, 변환, 결측치 처리 등 데이터 전처리에 필요한 결정을 내릴 수 있습니다.

EDA 방법론

- 기술 통계: 평균, 중앙값, 표준편차 등의 기술 통계를 사용해 데이터의 중심 경향성과 분산을 이해합니다.
- 시각화: 막대 그래프, 히스토그램, 박스 플롯, 산점도 등 다양한 시각화 도구를 사용하여 데이터를 시각적으로 탐색합니다.
- 상관 분석: 변수들 간의 상관 관계를 분석합니다.
<!-- - 다변량 분석: 여러 변수 간의 관계를 동시에 분석합니다. -->
- 그룹별 분석: 데이터를 여러 그룹으로 나누어 각 그룹의 특성을 비교 분석합니다.

도구 및 기술

- Pandas(판다스): 데이터 조작 및 분석을 위한 기본 도구입니다.
- Seaborn(시본)과 Matplotlib(맷플롯립): 데이터 시각화에 주로 사용되는 라이브러리입니다.

### head

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.head())
```

- `df.head()`: df의 앞에서 5개의 데이터를 가져옵니다.

```shell
   PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
0            1         0       3  ...   7.2500   NaN         S
1            2         1       1  ...  71.2833   C85         C
2            3         1       3  ...   7.9250   NaN         S
3            4         1       1  ...  53.1000  C123         S
4            5         0       3  ...   8.0500   NaN         S
```

**head(3)** 가 같이 사용되면 **3개**의 데이터를 가져옵니다.

### tail

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.tail())
```

- `df.tail()`: df의 뒤에서 5개의 데이터를 가져옵니다.

```shell
     PassengerId  Survived  Pclass  ...   Fare Cabin  Embarked
886          887         0       2  ...  13.00   NaN         S
887          888         1       1  ...  30.00   B42         S
888          889         0       3  ...  23.45   NaN         S
889          890         1       1  ...  30.00  C148         C
890          891         0       3  ...   7.75   NaN         Q
```

**tail(3)** 가 같이 사용되면 **3개**의 데이터를 가져옵니다.

### shape

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.shape)
```

- `df.shape`: df의 모양을 출력합니다.

```shell
(891, 12)
```

- `df.shape`: df의 모양은 (행, 열)로 반환됩니다.

### info

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.info())
```

- `df.info()`: df의 정보를 출력합니다.

```shell
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype  
---  ------       --------------  -----  
 0   PassengerId  891 non-null    int64  
 1   Survived     891 non-null    int64  
 2   Pclass       891 non-null    int64  
 3   Name         891 non-null    object 
 4   Sex          891 non-null    object 
 5   Age          714 non-null    float64
 6   SibSp        891 non-null    int64  
 7   Parch        891 non-null    int64  
 8   Ticket       891 non-null    object 
 9   Fare         891 non-null    float64
 10  Cabin        204 non-null    object 
 11  Embarked     889 non-null    object 
dtypes: float64(2), int64(5), object(5)
memory usage: 83.7+ KB
```

### columns

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.columns)
```

- `df.columns`: df의 column을 출력합니다.

```shell
Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',
       'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'],
      dtype='object')
```

### index

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.index)
```

- `df.index`: df의 index를 출력합니다.

```shell
RangeIndex(start=0, stop=891, step=1)
```

### describe

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.describe())
```

- `print(df.describe())`: df의 기술 통계를 출력합니다.

```shell
       PassengerId    Survived      Pclass  ...       SibSp       Parch        Fare
count   891.000000  891.000000  891.000000  ...  891.000000  891.000000  891.000000
mean    446.000000    0.383838    2.308642  ...    0.523008    0.381594   32.204208
std     257.353842    0.486592    0.836071  ...    1.102743    0.806057   49.693429
min       1.000000    0.000000    1.000000  ...    0.000000    0.000000    0.000000
25%     223.500000    0.000000    2.000000  ...    0.000000    0.000000    7.910400
50%     446.000000    0.000000    3.000000  ...    0.000000    0.000000   14.454200
75%     668.500000    1.000000    3.000000  ...    1.000000    0.000000   31.000000
max     891.000000    1.000000    3.000000  ...    8.000000    6.000000  512.329200
```

- `count`: 데이터의 개수
- `mean`: 평균
- `std`: 표준편차
- `min`: 최소값
- `25%`: 1사분위수
- `50%`: 2사분위수
- `75%`: 3사분위수
- `max`: 최대값

### describe(include='object')

object 타입의 데이터에 대한 기술 통계를 출력합니다.

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.describe(include='object'))
```

```shell
                          Name  Sex Ticket  Cabin Embarked
count                      891  891    891    204      889
unique                     891    2    681    147        3
top    Braund, Mr. Owen Harris male 347082 B96B98        S
freq                         1  577      7      4      644
```

- `count`: 데이터의 개수
- `unique`: 고유값의 개수
- `top`: 최빈값
- `freq`: 최빈값의 개수

### 상관 분석

```python
import pandas as pd

df = pd.read_csv('train.csv')
print(df.corr())
```

- `df.corr()`: df의 상관 분석을 출력합니다.

```shell
             PassengerId  Survived    Pclass  ...     SibSp     Parch      Fare
PassengerId     1.000000 -0.005007 -0.035144  ... -0.057527 -0.001652  0.012658
Survived       -0.005007  1.000000 -0.338481  ... -0.035322  0.081629  0.257307
Pclass         -0.035144 -0.338481  1.000000  ...  0.083081  0.018443 -0.549500
Age             0.036847 -0.077221 -0.369226  ... -0.308247 -0.189119  0.096067
SibSp          -0.057527 -0.035322  0.083081  ...  1.000000  0.414838  0.159651
Parch          -0.001652  0.081629  0.018443  ...  0.414838  1.000000  0.216225
Fare            0.012658  0.257307 -0.549500  ...  0.159651  0.216225  1.000000
```

---

해시태그: #python #pandas #dataframe #csv #eda #head #tail #shape #info #columns #index #describe #corr

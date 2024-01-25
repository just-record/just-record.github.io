---
layout: single
title: "Python - pandas 11 (Concat, Merge)"
categories: Python-Data_analysis
tag: [Python, pandas]
toc: true
toc_sticky: true
toc_label: "목차"
---
Sample Data는 이전 포스팅의 `타이타닉 자료(train.csv)`를 기반으로 합니다.

'Concat'과 'Merge'를 이용 해서 2개 이상의 DataFrame을 합치는 방법을 알아보겠습니다.

## 'Concat' 실습 데이터 만들기

```python
import pandas as pd

df = pd.read_csv('train.csv')

df1 = df.loc[:4, ['PassengerId', 'Survived', 'Pclass', 'Name']]
df2 = df.loc[5:9, ['PassengerId', 'Survived', 'Pclass', 'Name']]
df3 = df2.copy()
df3.columns = ['PassengerId', 'Survived', 'Pclass', 'Name2']

print(df1)
print()
print(df2)
print()
print(df3)
```

```python
   PassengerId  Survived  Pclass                                               Name
0            1         0       3                            Braund, Mr. Owen Harris
1            2         1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...
2            3         1       3                             Heikkinen, Miss. Laina
3            4         1       1       Futrelle, Mrs. Jacques Heath (Lily May Peel)
4            5         0       3                           Allen, Mr. William Henry

   PassengerId  Survived  Pclass                                               Name
5            6         0       3                                   Moran, Mr. James
6            7         0       1                            McCarthy, Mr. Timothy J
7            8         0       3                     Palsson, Master. Gosta Leonard
8            9         1       3  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
9           10         1       2                Nasser, Mrs. Nicholas (Adele Achem)

   PassengerId  Survived  Pclass                                              Name2
5            6         0       3                                   Moran, Mr. James
6            7         0       1                            McCarthy, Mr. Timothy J
7            8         0       3                     Palsson, Master. Gosta Leonard
8            9         1       3  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
9           10         1       2                Nasser, Mrs. Nicholas (Adele Achem)
```

- 'df1'과 'df2'는 column이 같지만 index가 모두 다릅니다.
- 'df2'와 'df3'은 column이 하나(Name, Name2) 다르지만 index가 같습니다.
- 'df1'과 'df3'은 column이 하나(Name, Name2) 다르고 index가 모두 다릅니다.

앞으로 'Concat' 실습 소스 코드에서 윗 부분은 생략하겠습니다.

## Concat

'Concat'은 2개 이상의 DataFrame을 이어 붙이는 함수입니다.

### 세로(아래) 붙이기

세로(아래)로 붙이기 위해서는 axis=0을 사용합니다. axis의 기본값이 0으로 생략되어 있습니다.

```python
import pandas as pd

# df1, df2, df3 생성

print(pd.concat([df1, df2]))
```

```python
   PassengerId  Survived  Pclass                                               Name
0            1         0       3                            Braund, Mr. Owen Harris
1            2         1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...
2            3         1       3                             Heikkinen, Miss. Laina
3            4         1       1       Futrelle, Mrs. Jacques Heath (Lily May Peel)
4            5         0       3                           Allen, Mr. William Henry
5            6         0       3                                   Moran, Mr. James
6            7         0       1                            McCarthy, Mr. Timothy J
7            8         0       3                     Palsson, Master. Gosta Leonard
8            9         1       3  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
9           10         1       2                Nasser, Mrs. Nicholas (Adele Achem)
```

- 'df1'과 'df2'를 세로로 붙여서 출력합니다.
- 세로로 붙이기 위해서는 Column을 기준으로 같은 Column을 붙이게 됩니다.
- 'df1'과 'df2'의 Column이 같기 때문에, Column이 같은 부분만 붙여서 출력됩니다.

### Column명이 다른 경우 - 세로

```python
import pandas as pd

# df1, df2, df3 생성

print(pd.concat([df1, df3]))
```

```python
   PassengerId  Survived  Pclass                                               Name                                              Name2
0            1         0       3                            Braund, Mr. Owen Harris                                                NaN
1            2         1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...                                                NaN
2            3         1       3                             Heikkinen, Miss. Laina                                                NaN
3            4         1       1       Futrelle, Mrs. Jacques Heath (Lily May Peel)                                                NaN
4            5         0       3                           Allen, Mr. William Henry                                                NaN
5            6         0       3                                                NaN                                   Moran, Mr. James
6            7         0       1                                                NaN                            McCarthy, Mr. Timothy J
7            8         0       3                                                NaN                     Palsson, Master. Gosta Leonard
8            9         1       3                                                NaN  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
9           10         1       2                                                NaN                Nasser, Mrs. Nicholas (Adele Achem)
```

- 'df1'과 'df3'의 Column이 다른 부분(Name, Name2)이 있습니다.
- 'df1'과 'df3'의 Column이 다른 경우에는 이어 붙이지 못하고 각각 Column을 그대로 가져옵니다.
- Column이 없는 부분은 NaN으로 출력됩니다.

### 가로(옆으로) 붙이기

가로로 붙이기 위해서는 axis=1을 사용합니다. 세로로 붙일 때는 axis=0을 사용합니다. 위의 소스 코드에서 세로로 붙은 이유는 axis의 기본값이 0으로 생략되어 있었기 때문입니다.

```python
import pandas as pd

# df1, df2, df3 생성

print(pd.concat([df2, df3], axis=1))
```

```python
   PassengerId  Survived  Pclass                                               Name  PassengerId  Survived  Pclass                                              Name2
5            6         0       3                                   Moran, Mr. James            6         0       3                                   Moran, Mr. James
6            7         0       1                            McCarthy, Mr. Timothy J            7         0       1                            McCarthy, Mr. Timothy J
7            8         0       3                     Palsson, Master. Gosta Leonard            8         0       3                     Palsson, Master. Gosta Leonard
8            9         1       3  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)            9         1       3  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
9           10         1       2                Nasser, Mrs. Nicholas (Adele Achem)           10         1       2                Nasser, Mrs. Nicholas (Adele Achem)
```

- 가로(옆으로) 붙이기 위해서는 index를 기준으로 같은 index끼리 붙이게 됩니다.
- 'df2'와 'df3'의 index(5, 6, 7, 8, 9)가 같기 때문에, index가 같은 부분만 붙여서 출력됩니다.
- Column은 자신의 DataFrame의 Column을 그대로 가져옵니다.

### index가 다른 경우 - 가로

```python
import pandas as pd

# df1, df2, df3 생성

print(pd.concat([df1, df2], axis=1))
```

```python
   PassengerId  Survived  Pclass                                               Name  PassengerId  Survived  Pclass                                               Name
0          1.0       0.0     3.0                            Braund, Mr. Owen Harris          NaN       NaN     NaN                                                NaN
1          2.0       1.0     1.0  Cumings, Mrs. John Bradley (Florence Briggs Th...          NaN       NaN     NaN                                                NaN
2          3.0       1.0     3.0                             Heikkinen, Miss. Laina          NaN       NaN     NaN                                                NaN
3          4.0       1.0     1.0       Futrelle, Mrs. Jacques Heath (Lily May Peel)          NaN       NaN     NaN                                                NaN
4          5.0       0.0     3.0                           Allen, Mr. William Henry          NaN       NaN     NaN                                                NaN
5          NaN       NaN     NaN                                                NaN          6.0       0.0     3.0                                   Moran, Mr. James
6          NaN       NaN     NaN                                                NaN          7.0       0.0     1.0                            McCarthy, Mr. Timothy J
7          NaN       NaN     NaN                                                NaN          8.0       0.0     3.0                     Palsson, Master. Gosta Leonard
8          NaN       NaN     NaN                                                NaN          9.0       1.0     3.0  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
9          NaN       NaN     NaN                                                NaN         10.0       1.0     2.0                Nasser, Mrs. Nicholas (Adele Achem)
```

- 가로(옆으로) 붙이기 위해서는 index를 기준으로 같은 index끼리 붙이게 됩니다.
- 'df1'과 'df2'의 index가 다르기 때문에, index가 같은 부분만 붙여서 출력됩니다.
- index가 다른 경우에는 NaN으로 출력됩니다.

## 'Merge' 실습 데이터 만들기

```python
import numpy as np
import pandas as pd

df = pd.read_csv('train.csv')

df1 = df.loc[:4, ['PassengerId', 'Survived', 'Pclass', 'Name']]
df2 = df.loc[5:9, ['PassengerId', 'Survived', 'Pclass', 'Name']]
df2.loc[:7, 'PassengerId'] = np.arange(1,4)
df2.columns = ['PassengerId', 'Survived', 'c1', 'c2']

print(df1)
print()
print(df2)
```

```python
   PassengerId  Survived  Pclass                                               Name
0            1         0       3                            Braund, Mr. Owen Harris
1            2         1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...
2            3         1       3                             Heikkinen, Miss. Laina
3            4         1       1       Futrelle, Mrs. Jacques Heath (Lily May Peel)
4            5         0       3                           Allen, Mr. William Henry

   PassengerId  Survived  c1                                                 c2
5            1         0   3                                   Moran, Mr. James
6            2         0   1                            McCarthy, Mr. Timothy J
7            3         0   3                     Palsson, Master. Gosta Leonard
8            9         1   3  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
9           10         1   2                Nasser, Mrs. Nicholas (Adele Achem)
```

- 'df2'의 'PassengerId'의 3개(1, 2, 3)를 'df1'의 PassengerId와 동일하게 생성하였습니다. 나머지는 다릅니다.
- 'df2'의 Column명의 일부를 'c1', 'c2'로 변경하였습니다. 'PassengerId'와 'Survived'를 제외하고 나머지 Column명을 변경하였습니다.

앞으로 'Merge' 실습 소스 코드에서 윗 부분은 생략하겠습니다.

## Merge

'Merge'는 2개의 DataFrame을 합치는 함수입니다. 'Concat'과 다르게 2개의 DataFrame을 합칠 때, 기준이 되는 Column을 지정할 수 있습니다.

### Column값이 동일한 경우만 합치기

how='inner'를 사용하면 Column값이 동일한 경우만 합칩니다.

```python
import pandas as pd

# df1, df2 생성

print(pd.merge(df1, df2, on='PassengerId', how='inner'))
```

```python
   PassengerId  Survived_x  Pclass                                               Name  Survived_y  c1                              c2
0            1           0       3                            Braund, Mr. Owen Harris           0   3                Moran, Mr. James
1            2           1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...           0   1         McCarthy, Mr. Timothy J
2            3           1       3                             Heikkinen, Miss. Laina           0   3  Palsson, Master. Gosta Leonard
```

- 'on'에 지정한 Column(PassengerId)의 값을 기준으로 합칩니다.
- 'how'는 합치는 방법을 지정합니다. 'inner'는 Column값이 동일한 경우만 합칩니다.
- 'df1'과 'df2'의 PassengerId의 값이 동일한 경우만 합쳐서 출력합니다.

### 왼쪽 DataFrame은 모두 출력하기

how='left'를 사용하면 왼쪽 DataFrame은 모두 출력하고 오른쪽 DataFrame은 Column값이 동일한 경우만 값이 출력됩니다.

```python
import pandas as pd

# df1, df2 생성

print(pd.merge(df1, df2, on='PassengerId', how='left'))
```

```python
   PassengerId  Survived_x  Pclass                                               Name  Survived_y   c1                              c2
0            1           0       3                            Braund, Mr. Owen Harris         0.0  3.0                Moran, Mr. James
1            2           1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...         0.0  1.0         McCarthy, Mr. Timothy J
2            3           1       3                             Heikkinen, Miss. Laina         0.0  3.0  Palsson, Master. Gosta Leonard
3            4           1       1       Futrelle, Mrs. Jacques Heath (Lily May Peel)         NaN  NaN                             NaN
4            5           0       3                           Allen, Mr. William Henry         NaN  NaN                             NaN
```

- `how='left'`를 사용하면 왼쪽 DataFrame은 모두 출력하고 오른쪽 DataFrame은 Column값이 동일한 경우만 합칩니다.
- 'PassengerId' 값이 동일한 1, 2, 3은 합쳐졌습니다.
- 'PassengerId' 값이 동일하지 않을 때 왼쪽 DataFrame(4, 5)는 합쳐지고 오른쪽 DataFrame(9, 10)은 합쳐지지 않습니다.
- 왼쪽 Dataframe의 4, 5는 오른쪽 DataFrame의 Column값이 없기 때문에 NaN으로 출력됩니다.

### 오른쪽 DataFrame은 모두 출력하기

how='right'를 사용하면 오른쪽 DataFrame은 모두 출력하고 왼쪽 DataFrame은 Column값이 동일한 경우만 값이 출력됩니다.

```python
import pandas as pd

# df1, df2 생성

print(pd.merge(df1, df2, on='PassengerId', how='right'))
```

```python
   PassengerId  Survived_x  Pclass                                               Name  Survived_y  c1                                                 c2
0            1         0.0     3.0                            Braund, Mr. Owen Harris           0   3                                   Moran, Mr. James
1            2         1.0     1.0  Cumings, Mrs. John Bradley (Florence Briggs Th...           0   1                            McCarthy, Mr. Timothy J
2            3         1.0     3.0                             Heikkinen, Miss. Laina           0   3                     Palsson, Master. Gosta Leonard
3            9         NaN     NaN                                                NaN           1   3  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
4           10         NaN     NaN                                                NaN           1   2                Nasser, Mrs. Nicholas (Adele Achem)
```

- `how='right'`를 `how='left'`와 동일한 규칙이 적용됩니다. 다만 왼쪽이 아니라 오른쪽 DataFrame을 기준으로 적용됩니다.

### 양쪽 DataFrame 모두 출력하기

how='outer'를 사용하면 양쪽 DataFrame 모두 출력하고 Column값이 동일한 경우만 값이 출력됩니다.

```python
import pandas as pd

# df1, df2 생성

print(pd.merge(df1, df2, on='PassengerId', how='outer'))
```

```python
   PassengerId  Survived_x  Pclass                                               Name  Survived_y   c1                                                 c2
0            1         0.0     3.0                            Braund, Mr. Owen Harris         0.0  3.0                                   Moran, Mr. James
1            2         1.0     1.0  Cumings, Mrs. John Bradley (Florence Briggs Th...         0.0  1.0                            McCarthy, Mr. Timothy J
2            3         1.0     3.0                             Heikkinen, Miss. Laina         0.0  3.0                     Palsson, Master. Gosta Leonard
3            4         1.0     1.0       Futrelle, Mrs. Jacques Heath (Lily May Peel)         NaN  NaN                                                NaN
4            5         0.0     3.0                           Allen, Mr. William Henry         NaN  NaN                                                NaN
5            9         NaN     NaN                                                NaN         1.0  3.0  Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)
6           10         NaN     NaN                                                NaN         1.0  2.0                Nasser, Mrs. Nicholas (Adele Achem)
```

- `how='outer'`를 사용하면 양쪽 DataFrame 모두 출력하고 Column값이 동일한 경우만 합칩니다.
- Column값이 동일하지 않은 경우에는 NaN으로 출력됩니다.

### 2개 이상의 Column을 기준으로 합치기

```python
import pandas as pd

# df1, df2 생성

print(pd.merge(df1, df2, on=['PassengerId', 'Survived'], how='inner'))
```

```python
   PassengerId  Survived  Pclass                     Name  c1                c2
0            1         0       3  Braund, Mr. Owen Harris   3  Moran, Mr. James
```

- `on`에 2개 이상의 Column을 리스트로 지정하면 2개 이상의 Column을 기준으로 합칩니다.
- 'PassengerId'와 'Survived'의 값이 동일한 경우만 합쳐서 출력합니다.

### Column명이 다를 때 합치기

테스트 데이터를 조금 변경하겠습니다.

```python
import numpy as np

# df1, df2 생성
df2.rename(columns = {'Survived':'c3'}, inplace=True)

print(pd.merge(df1, df2, left_on=['PassengerId', 'Survived'], right_on=['PassengerId', 'c1'], how='inner'))
```

- 'df2'의 'Survived' Column명을 'c3'로 변경하였습니다.

```python
   PassengerId  Survived  Pclass                                               Name  c3  c1                       c2
0            2         1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...   0   1  McCarthy, Mr. Timothy J
```

- 'left_on'으로 왼쪽의 Column명을 지정하고, 'right_on'으로 오른쪽의 Column명을 지정합니다.

---

해시태그: #python #pandas #dataframe #concat #merge #inner #outer #left #right #on #left_on #right_on

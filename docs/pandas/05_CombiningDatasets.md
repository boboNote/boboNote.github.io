---
layout: default
title: CombiningDatasets
parent: Pandas
nav_order: 50
has_children: false
permalink: /docs/pandas/combiningDatasets 
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# CombiningDatasets
데이터에 대한 가장 흥미로운 연구 중 일부는 서로 다른 데이터 소스를 결합한 것입니다. 이러한 작업에는 두 개의 서로 다른 데이터 세트를 매우 간단하게 연결하는 것부터 데이터 세트 간의 겹침을 올바르게 처리하는 더 복잡한 데이터베이스 스타일의 조인 및 병합에 이르기까지 모든 것이 포함될 수 있습니다. Series 및 DataFrames는 이러한 유형의 작업을 염두에 두고 구축되었으며 Pandas에는 이러한 종류의 데이터 랭글링을 빠르고 간단하게 만드는 기능과 메서드가 포함되어 있습니다.

여기에서 pd.concat 함수를 사용하여 Series 및 DataFrames를 연결하는 간단한 방법을 살펴보겠습니다. 나중에 우리는 Pandas에서 구현된 보다 정교한 인메모리 병합 및 조인에 대해 알아볼 것입니다.


```python
import pandas as pd
import numpy as np

def make_df(cols, ind):
    """빠르게 DataFrame 생성"""
    data = {c: [str(c) + str(i) for i in ind]
            for c in cols}
    return pd.DataFrame(data, ind)

# DataFrame 예제
make_df('ABC', range(3))
    |   | A  | B  | C  |
    |---|----|----|----|
    | 0 | A0 | B0 | C0 |
    | 1 | A1 | B1 | C1 |
    | 2 | A2 | B2 | C2 |

```

##  Recall: Concatenation of NumPy Arrays 
``Series`` 및 ``DataFrame`` 객체의 연결은 Numpy 배열의 연결과 매우 유사하며, 이는 에서 논의된 바와 같이 ``np.concatenate`` 기능을 통해 수행할 수 있습니다.

```python 
x = [1, 2, 3]
y = [4, 5, 6]
z = [7, 8, 9]
np.concatenate([x, y, z])
    array([1, 2, 3, 4, 5, 6, 7, 8, 9])



x = [[1, 2],
     [3, 4]]
np.concatenate([x, x], axis=1) # axis 축을 이용하여 행에 방향으로 추가 
    array([ [1, 2, 1, 2],
            [3, 4, 3, 4]])

```

## Simple Concatenation with ``pd.concat`` 
Pandas에는 np.concatenate와 구문이 유사하지만 잠시 논의할 여러 옵션이 포함된 pd.concat() 함수가 있습니다.

```
# 팬더스 0.19에서 pd.concat() 함수 시그니처
pd.concat(objs, axis=0, join='outer', join_axes=None, ignore_index=False,
          keys=None, levels=None, names=None, verify_integrity=False,
          copy=True)
```

pd.concat()은 단순한 배열 연결에 np.concatenate()를 사용할 수 있는 것처럼 Series 또는 DataFrame 객체의 간단한 연결에 사용할 수 있습니다.

```python 
df1 = make_df('AB', [1, 2])
df2 = make_df('AB', [3, 4])
print(df1); print(df2); print(pd.concat([df1, df2])) 
        A   B
    1  A1  B1
    2  A2  B2
        A   B
    3  A3  B3
    4  A4  B4
        A   B
    1  A1  B1
    2  A2  B2
    3  A3  B3
    4  A4  B4
```

기본적으로 연결은 DataFrame 내에서 행 단위로 발생합니다(즉, axis=0). np.concatenate와 마찬가지로 pd.concat은 연결이 수행될 축을 지정할 수 있습니다. 다음 예를 고려하십시오.

```python 
df3 = make_df('AB', [0, 1])
df4 = make_df('CD', [0, 1])
print(df3); print(df4); print(pd.concat([df3, df4], axis=1)) 
        A   B
    0  A0  B0
    1  A1  B1
        C   D
    0  C0  D0
    1  C1  D1
        A   B   C   D
    0  A0  B0  C0  D0
    1  A1  B1  C1  D1
```

### Duplicate indices 
``np.concatenate``와 ``pd.concat``의 중요한 차이점 중 하나는 결과에 중복 인덱스가 있더라도 Pandas 연결이 **인덱스를 보존**한다는 것입니다!
다음과 같은 간단한 예를 고려하십시오.
```python 
x = make_df('AB', [0, 1])
y = make_df('AB', [2, 3])
y.index = x.index  # 복제 인덱스 생성!
print(x); print(y); print(pd.concat([x, y])) 
        A   B
    0  A0  B0
    1  A1  B1
        A   B
    0  A2  B2
    1  A3  B3
        A   B
    0  A0  B0
    1  A1  B1
    0  A2  B2
    1  A3  B3
```

인덱스를 보존 함에 있어서 잘못된 결과를 반환할수 있다. 이를 방지할수 있는 방법을 알아보자. 

```python 

# Validation 
try:
    pd.concat([x, y], verify_integrity=True)
except ValueError as e:
    print("ValueError:", e)

ValueError: Indexes have overlapping values: [0, 1]

# Multiple Index
print(x); print(y); print(pd.concat([x, y], keys=['x', 'y'])) 
        A   B
    0  A0  B0
    1  A1  B1
        A   B
    0  A2  B2
    1  A3  B3
        A   B
    x 0  A0  B0
    1  A1  B1
    y 0  A2  B2
    1  A3  B3
    
```

### Concatenation with joins
방금 살펴본 간단한 예에서는 주로 ``DataFrame``을 공유 열 이름과 연결했습니다.
실제로 다른 소스의 데이터는 다른 열 이름 세트를 가질 수 있으며 이 경우 ``pd.concat``은 여러 옵션을 제공합니다.
다음 두 개의 ``DataFrame``을 연결하는 것을 고려하십시오. 여기에는 일부(전부는 아니지만!) 열이 공통적으로 있습니다.

```python 
df5 = make_df('ABC', [1, 2])
df6 = make_df('BCD', [3, 4])
print(df5); print(df6); print(pd.concat([df5, df6])) 
        A   B   C
    1  A1  B1  C1
    2  A2  B2  C2
        B   C   D
    3  B3  C3  D3
    4  B4  C4  D4
        A   B   C    D
    1   A1  B1  C1  NaN
    2   A2  B2  C2  NaN
    3  NaN  B3  C3   D3
    4  NaN  B4  C4   D4
```

기본적으로 사용 가능한 데이터가 없는 항목은 NA 값으로 채워집니다. 이를 변경하기 위해 연결 함수의 join 및 join_axes 매개변수에 대해 여러 옵션 중 하나를 지정할 수 있습니다. 기본적으로 조인은 입력 열의 합집합(join='outer')이지만 join='inner'를 사용하여 열의 교집합으로 변경할 수 있습니다.

```python 
print(df5); print(df6); 
print(pd.concat([df5, df6], join='inner')) 
#---------------------------------------------
# 'NaN' 값이 존재 하는 컬럼 정보는 빠지게 된다. 
#---------------------------------------------
        A   B   C
    1  A1  B1  C1
    2  A2  B2  C2
        B   C   D
    3  B3  C3  D3
    4  B4  C4  D4
        B   C
    1  B1  C1
    2  B2  C2
    3  B3  C3
    4  B4  C4

```

### The ``append()`` method 
직접 배열 연결이 매우 일반적이기 때문에 ``Series`` 및 ``DataFrame`` 개체에는 더 적은 키 입력으로 동일한 작업을 수행할 수 있는 ``append`` 메서드가 있습니다.
예를 들어, ``pd.concat([df1, df2])``를 호출하는 대신 ``df1.append(df2)``를 호출하면 됩니다. 

```python 
print(df5); print(df6); print(df5.append(df6)) 
display('df1', 'df2', "df1.append(df2)")
        A   B   C
    1  A1  B1  C1
    2  A2  B2  C2
        B   C   D
    3  B3  C3  D3
    4  B4  C4  D4
        A   B   C    D
    1   A1  B1  C1  NaN
    2   A2  B2  C2  NaN
    3  NaN  B3  C3   D3
    4  NaN  B4  C4   D4

```



[Pandas Merge, join, concatenate and compare](https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html)

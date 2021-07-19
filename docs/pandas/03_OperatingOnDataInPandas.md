---
layout: default
title: Operation in Pandas
parent: Pandas
nav_order: 30
has_children: false
permalink: /docs/pandas/operation 
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Operation On Data in Pandas
NumPy의 필수 요소 중 하나는 기본 산술(덧셈, 뺄셈, 곱셈 등)과 보다 정교한 연산(삼각 함수, 지수 및 로그 함수 등)으로 요소별 연산을 빠르게 수행하는 기능입니다. Pandas는 NumPy에서 이 기능의 대부분을 상속하며 NumPy 배열의 계산: 범용 함수에서 소개한 ufuncs가 핵심입니다.

Pandas에는 몇 가지 유용한 비틀기가 포함되어 있습니다. 그러나 부정 및 삼각 함수와 같은 단항 연산의 경우 이러한 ufunc는 출력에서 ​​인덱스 및 열 레이블을 유지하고 덧셈 및 곱셈과 같은 이진 연산의 경우 Pandas는 객체를 전달할 때 인덱스를 자동으로 정렬합니다. 우펑. 이는 데이터의 컨텍스트를 유지하고 다른 소스의 데이터를 결합하는 것(둘 다 잠재적으로 오류가 발생하기 쉬운 원시 NumPy 배열을 사용하는 작업)이 Pandas를 사용하면 기본적으로 완벽한 작업이 된다는 것을 의미합니다. 또한 1차원 Series 구조와 2차원 DataFrame 구조 사이에 잘 ​​정의된 작업이 있음을 알 수 있습니다.

## Ufuncs: Index Preservation 
Pandas는 NumPy와 함께 작동하도록 설계되었으므로 모든 NumPy ufunc는 Pandas ``Series`` 및 ``DataFrame`` 개체에서 작동합니다.

```python 
import pandas as pd
import numpy as np

rng = np.random.RandomState(42)
ser = pd.Series(rng.randint(0, 10, 4))
ser
    0    6
    1    3
    2    7
    3    4
    dtype: int64

df = pd.DataFrame(rng.randint(0, 10, (3, 4)),
                  columns=['A', 'B', 'C', 'D'])
    |   | A | B | C |
    |---|---|---|---|
    | 0 | 6 | 9 | 2 |
    | 1 | 7 | 4 | 3 |

np.exp(ser)
    0     403.428793
    1      20.085537
    2    1096.633158
    3      54.598150
    dtype: float64

np.sin(df * np.pi / 4)
    |   | A         | B        | C         | D         |
    |---|-----------|----------|-----------|-----------|
    | 0 | -1        | 7.07E-01 | 1         | -1.00E+00 |
    | 1 | -0.707107 | 1.22E-16 | 0.707107  | -7.07E-01 |
    | 2 | -0.707107 | 1.00E+00 | -0.707107 | 1.22E-16  |

```

## UFuncs: Index Alignment 
두 개의 ``Series`` 또는 ``DataFrame`` 객체에 대한 이진 연산의 경우 Pandas는 연산을 수행하는 과정에서 인덱스를 정렬합니다.
다음 몇 가지 예에서 볼 수 있듯이 이는 불완전한 데이터로 작업할 때 매우 편리합니다.

###  Index alignment in Series
예를 들어 두 개의 서로 다른 데이터 소스를 결합하고 *area*로 미국 상위 3개 주만 찾고 *population*으로 미국 상위 3개 주만 찾습니다. 

```python 
area = pd.Series({'Alaska': 1723337, 'Texas': 695662,'California': 423967}, name='area')
area
    Alaska        1723337
    Texas          695662
    California     423967
    Name: area, dtype: int64

population = pd.Series({'California': 38332521, 'Texas': 26448193,'New York': 19651127}, name='population')
    California    38332521
    Texas         26448193
    New York      19651127
    Name: population, dtype: int64

calDF = population / area 
    Alaska              NaN
    California    90.413926
    New York            NaN
    Texas         38.018740
    dtype: float64

area.index | population.index
    Index(['Alaska', 'California', 'New York', 'Texas'], dtype='object')

```

```python 
A = pd.Series([2, 4, 6], index=[0, 1, 2])
B = pd.Series([1, 3, 5], index=[1, 2, 3])
A + B
    0    NaN
    1    5.0
    2    9.0
    3    NaN
    dtype: float64

A.add(B, fill_value=0)
    0    2.0
    1    5.0
    2    9.0
    3    5.0
    dtype: float64
```

### Index alignment in DataFrame
``DataFrame``에 대한 작업을 수행할 때 열과 인덱스 *모두*에 대해 유사한 유형의 정렬이 발생합니다. 

```python 
rng = np.random.RandomState(42)
A = pd.DataFrame(rng.randint(0, 20, (2, 2)),
                 columns=list('AB'))
A
    |   | A  | B  |
    |---|----|----|
    | 0 | 6  | 19 |
    | 1 | 14 | 10 |


B = pd.DataFrame(rng.randint(0, 10, (3, 3)),
                 columns=list('BAC'))
B
    |   | B | A | C |
    |---|---|---|---|
    | 0 | 4 | 0 | 9 |
    | 1 | 5 | 8 | 0 |
    | 2 | 9 | 2 | 6 |

A + B
    |   | A   | B   | C   |
    |---|-----|-----|-----|
    | 0 | 1   | 15  | NaN |
    | 1 | 13  | 6   | NaN |
    | 2 | NaN | NaN | NaN |


fill = A.stack().mean()
```

다음 표에는 Python 연산자와 이에 상응하는 Pandas 객체 메서드가 나와 있습니다. 

| Python Operator | Pandas Method(s)                      |
|-----------------|---------------------------------------|
| ``+``           | ``add()``                             |
| ``-``           | ``sub()``, ``subtract()``             |
| ``*``           | ``mul()``, ``multiply()``             |
| ``/``           | ``truediv()``, ``div()``, ``divide()``|
| ``//``          | ``floordiv()``                        |
| ``%``           | ``mod()``                             |
| ``**``          | ``pow()``                             |

## Ufuncs: Operations Between DataFrame and Series 
``DataFrame``과 ``Series`` 간에 작업을 수행할 때 인덱스와 열 정렬이 유사하게 유지됩니다.

``DataFrame``과 ``Series`` 간의 작업은 2차원 및 1차원 NumPy 배열 간의 작업과 유사합니다.
2차원 배열과 그 행 중 하나의 차이를 찾는 일반적인 작업 하나를 고려하십시오.

```python 
A = rng.randint(10, size=(3, 4))
A
    array([ [3, 8, 2, 4],
            [2, 6, 4, 8],
            [6, 1, 3, 8]])

A - A[0]
    array([ [ 0,  0,  0,  0],
            [-1, -2,  2,  4],
            [ 3, -7,  1,  4]])

df = pd.DataFrame(A, columns=list('QRST'))
df - df.iloc[0]
    |   | Q  | R  | S | T |
    |---|----|----|---|---|
    | 0 | 0  | 0  | 0 | 0 |
    | 1 | -1 | -2 | 2 | 4 |
    | 2 | 3  | -7 | 1 | 4 |

df.subtract(df['R'], axis=0)
    |   | Q  | R | S  | T  |
    |---|----|---|----|----|
    | 0 | -5 | 0 | -6 | -4 |
    | 1 | -4 | 0 | -2 | 2  |
    | 2 | 5  | 0 | 2  | 7  |

halfrow = df.iloc[0, ::2]
print(type(halfrow))
    <class 'pandas.core.series.Series'>

halfrow
    Q    3
    S    2
    Name: 0, dtype: int64

# ***********************************************
# DataFrame And Series Operation 
# DataFrame 과 Series 간 인덱스를 자동으로 정렬합니다. 
# ***********************************************
df - halfrow
    |   | Q  | R   | S | T   |
    |---|----|-----|---|-----|
    | 0 | 0  | NaN | 0 | NaN |
    | 1 | -5 | NaN | 1 | NaN |
    | 2 | -4 | NaN | 1 | NaN |
```
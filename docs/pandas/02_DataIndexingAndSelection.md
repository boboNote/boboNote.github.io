---
layout: default
title: IndexingAndSelection
parent: Pandas
nav_order: 20
has_children: false
permalink: /docs/pandas/indexingAndSelection
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Data Indexing and Selection
여기에서는 Pandas ``Series`` 및 ``DataFrame`` 객체의 값에 액세스하고 수정하는 유사한 방법을 살펴보겠습니다.
NumPy 패턴을 사용한 적이 있다면 Pandas의 해당 패턴이 매우 친숙하게 느껴질 것입니다. 하지만 알아야 할 몇 가지 단점이 있습니다.

1차원 ``Series`` 객체의 간단한 경우부터 시작하여 더 복잡한 2차원 ``DataFrame`` 객체로 넘어갈 것입니다.

## Data Selection in Series
이전 섹션에서 보았듯이 Series 객체는 여러 면에서 1차원 NumPy 배열과 같은 역할을 하고 여러 면에서 표준 Python 사전과 같은 역할을 합니다. 이 두 가지 중복되는 유추를 염두에두면 이러한 배열에서 데이터 인덱싱 및 선택 패턴을 이해하는 데 도움이 됩니다.

### Series as dictionary
dictionary와 마찬가지로 ``Series`` 객체는 키 컬렉션에서 값 컬렉션으로의 매핑을 제공합니다.

* Series 객체는 사전과 같은 구문으로 수정할 수도 있습니다. 새 키에 할당하여 사전을 확장할 수 있는 것처럼 새 인덱스 값에 할당하여 Series를 확장할 수 있습니다. 
* 객체의 이러한 쉬운 변경성은 편리한 기능입니다. 내부적으로 Pandas는 발생해야 할 수 있는 메모리 레이아웃 및 데이터 복사에 대한 결정을 내리고 있습니다. 사용자는 일반적으로 이러한 문제에 대해 걱정할 필요가 없습니다.

```python 
mport pandas as pd
data = pd.Series([0.25, 0.5, 0.75, 1.0],
                 index=['a', 'b', 'c', 'd'])
data
    a    0.25
    b    0.50
    c    0.75
    d    1.00
    dtype: float64

data['b']
    0.5

'a' in data
    True

data.keys()
    Index(['a', 'b', 'c', 'd'], dtype='object')   # <class 'pandas.core.indexes.base.Index'> 


list(data.items())
    [('a', 0.25), ('b', 0.5), ('c', 0.75), ('d', 1.0)]

print(type(data.items()))
    <class 'zip'>

data['e'] = 1.25
data
    a    0.25
    b    0.50
    c    0.75
    d    1.00
    e    1.25
    dtype: float64

```

### Series as one-dimensional array 
A 시리즈는 이 사전과 같은 인터페이스를 기반으로 하며 NumPy 배열과 동일한 기본 메커니즘, 즉 슬라이스, 마스킹 및 멋진 인덱싱을 통해 배열 스타일 항목 선택을 제공합니다. 이러한 예는 다음과 같습니다.

```python 
# 명시적인 인덱스로 슬라이싱하기
data['a':'c']
    a    0.25
    b    0.50
    c    0.75
    dtype: float64

# 암묵적 정수 인덱스로 슬라이싱하기 
data[0:2]
    a    0.25
    b    0.50
    dtype: float64

# 마스킹
data[(data > 0.3) & (data < 0.8)]
    b    0.50
    c    0.75
    dtype: float64

# 팬시 인덱
data[['a', 'e']]
    a    0.25
    e    1.25
    dtype: float64

```

**이 중 슬라이싱이 가장 혼란스러운 원인이 될 수 있습니다. 명시적 인덱스(즉, data['a':'c'])로 슬라이싱할 때 최종 인덱스가 슬라이스에 포함되는 반면 암시적 인덱스(즉, data[0:2])로 슬라이싱할 때, 최종 인덱스는 슬라이스에서 제외됩니다.**

### Indexers: loc, iloc, and ix
이러한 슬라이싱 및 인덱싱 규칙은 혼란의 원인이 될 수 있습니다.
예를 들어 ``Series``에 명시적 정수 인덱스가 있는 경우 ``data[1]``와 같은 인덱싱 작업은 명시적 인덱스를 사용하는 반면 ``data[1:3]``와 같은 슬라이싱 작업은 암시적 Python 스타일 인덱스를 사용합니다.

```python 
data = pd.Series(['a', 'b', 'c'], index=[1, 3, 5])
data
    1    a
    3    b
    5    c
    dtype: object

# 인덱싱할 때 명시적인 인덱스 사용
data[1]
    'a'

# 슬라이싱할 때 암묵적 인덱스 사용
data[1:3]
    3    b
    5    c
    dtype: object

```

정수 인덱스의 경우 이러한 잠재적인 혼동 때문에 Pandas는 특정 인덱싱 체계를 명시적으로 노출하는 몇 가지 특수 인덱서 속성을 제공합니다. 이는 기능적 메서드가 아니라 Series의 데이터에 특정 슬라이싱 인터페이스를 노출하는 속성입니다.

첫째, loc 속성은 항상 명시적 인덱스를 참조하는 인덱싱 및 슬라이싱을 허용합니다.

```python 
data.loc[1]
    'a'

data.loc[1:3]
    1    a
    3    b
    dtype: object

data.iloc[1]
    'b'

data.iloc[1:3]
    3    b
    5    c
    dtype: object

```

## Data Selection in DataFrame
``DataFrame``은 2차원 배열이나 구조화된 배열처럼 여러 가지 방식으로 작동하고, 다른 방식으로는 동일한 인덱스를 공유하는 ``Series`` 구조 사전과 같은 역할을 한다는 점을 기억하세요.
이러한 유추는 이 구조 내에서 데이터 선택을 탐색할 때 염두에 두는 데 도움이 될 수 있습니다.

###  DataFrame as a dictionary 
DataFrame 은 Series 로 구셩되어 있습니다. 

```python 
area = pd.Series({'California': 423967, 'Texas': 695662,
                  'New York': 141297, 'Florida': 170312,
                  'Illinois': 149995})
pop = pd.Series({'California': 38332521, 'Texas': 26448193,
                 'New York': 19651127, 'Florida': 19552860,
                 'Illinois': 12882135})
data = pd.DataFrame({'area':area, 'pop':pop})
data
    |            | area   | pop      |
    |------------|--------|----------|
    | California | 423967 | 38332521 |
    | Florida    | 170312 | 19552860 |
    | Illinois   | 149995 | 12882135 |
    | New York   | 141297 | 19651127 |
    | Texas      | 695662 | 26448193 |

data['area']
    California    423967
    Florida       170312
    Illinois      149995
    New York      141297
    Texas         695662
    Name: area, dtype: int64

data.area
    California    423967
    Florida       170312
    Illinois      149995
    New York      141297
    Texas         695662
    Name: area, dtype: int64

data.area is data['area']
    True 

# dataFrame 에 pop Method 가 존재한다. 고로 False 
data.pop is data['pop']
    False

data['density'] = data['pop'] / data['area']
data
    |            | area   | pop      | density    |
    |------------|--------|----------|------------|
    | California | 423967 | 38332521 | 90.413926  |
    | Florida    | 170312 | 19552860 | 114.806121 |
    | Illinois   | 149995 | 12882135 | 85.883763  |
    | New York   | 141297 | 19651127 | 139.076746 |
    | Texas      | 695662 | 26448193 | 38.01874   |

```

###  DataFrame as two-dimensional array 
앞서 언급했듯이 ``DataFrame``을 향상된 2차원 배열로 볼 수도 있습니다.
``values`` 속성을 사용하여 원시 기본 데이터 배열을 검사할 수 있습니다.

```python 

# Numpy Array 객체로 변환된 된다. 
data.values
    array([ [4.23967000e+05, 3.83325210e+07, 9.04139261e+01],
            [6.95662000e+05, 2.64481930e+07, 3.80187404e+01],
            [1.41297000e+05, 1.96511270e+07, 1.39076746e+02],
            [1.70312000e+05, 1.95528600e+07, 1.14806121e+02],
            [1.49995000e+05, 1.28821350e+07, 8.58837628e+01]])

# 전치 행렬로 변환 
data.T 
    |         | California | Florida  | Illinois | New York | Texas    |
    |---------|------------|----------|----------|----------|----------|
    | area    | 4.24E+05   | 1.70E+05 | 1.50E+05 | 1.41E+05 | 6.96E+05 |
    | pop     | 3.83E+07   | 1.96E+07 | 1.29E+07 | 1.97E+07 | 2.64E+07 |
    | density | 9.04E+01   | 1.15E+02 | 8.59E+01 | 1.39E+02 | 3.80E+01 |

# Numpy Array를 이용하여 Indexing 한 결과 
data.values[0]
    array([  4.23967000e+05,   3.83325210e+07,   9.04139261e+01])

# DataFrame Slice != Numpy Array 와는 같지 않다. 
data[0:1]
    |            | area   | pop      | density   |
    |------------|--------|----------|-----------|
    | California | 423967 | 38332521 | 90.413926 |

# DataFrame Indexing 을 통한 결과 
data['area']
    California    423967
    Florida       170312
    Illinois      149995
    New York      141297
    Texas         695662
    Name: area, dtype: int64
```

iloc 를 이용하여 Slicing 을 구성할수 있다. 

```python 
data.iloc[:3, :2]
    |            | area   | pop      |
    |------------|--------|----------|
    | California | 423967 | 38332521 |
    | Texas      | 695662 | 26448193 |
    | New York   | 141297 | 19651127 |

data.loc[:'Illinois', :'pop']
    |            | area   | pop      |
    |------------|--------|----------|
    | California | 423967 | 38332521 |
    | Florida    | 170312 | 19552860 |
    | Illinois   | 149995 | 12882135 |

data.ix[:3, :'pop']
    |            | area   | pop      |
    |------------|--------|----------|
    | California | 423967 | 38332521 |
    | Florida    | 170312 | 19552860 |
    | Illinois   | 149995 | 12882135 |

```

정수 인덱스의 경우 ix 인덱서는 정수 인덱스 시리즈 개체에 대해 논의된 것과 동일한 잠재적인 혼란 소스에 영향을 받습니다.

익숙한 NumPy 스타일 데이터 액세스 패턴은 이러한 인덱서 내에서 사용할 수 있습니다. 예를 들어, loc 인덱서에서 다음과 같이 마스킹과 팬시 인덱싱을 결합할 수 있습니다.

```python 
data.loc[data.density > 100, ['pop', 'density']]
    |          | pop      | density    |
    |----------|----------|------------|
    | Florida  | 19552860 | 114.806121 |
    | New York | 19651127 | 139.076746 |

```

이러한 인덱싱 규칙을 사용하여 값을 설정하거나 수정할 수도 있습니다. 이것은 NumPy 작업에 익숙할 수 있는 표준 방식으로 수행됩니다.
```python 
data.iloc[0, 2] = 90
data
    |            | area   | pop      | density    |
    |------------|--------|----------|------------|
    | California | 423967 | 38332521 | 90         |
    | Florida    | 170312 | 19552860 | 114.806121 |
    | Illinois   | 149995 | 12882135 | 85.883763  |
    | New York   | 141297 | 19651127 | 139.076746 |
    | Texas      | 695662 | 26448193 | 38.01874   |


```

### Additional indexing conventions 
앞의 논의와 상반되는 것처럼 보일 수 있는 몇 가지 추가 인덱싱 규칙이 있지만 실제로는 매우 유용할 수 있습니다. 첫째, 인덱싱은 열을 참조하는 반면 슬라이싱은 행을 참조합니다.

```python 
data['Florida':'Illinois']
    |          | area   | pop      | density    |
    |----------|--------|----------|------------|
    | Florida  | 170312 | 19552860 | 114.806121 |
    | Illinois | 149995 | 12882135 | 85.883763  |

data[1:3]
    |          | area   | pop      | density    |
    |----------|--------|----------|------------|
    | Florida  | 170312 | 19552860 | 114.806121 |
    | Illinois | 149995 | 12882135 | 85.883763  |

data[data.density > 100]
    |            | area   | pop      | density    |
    |------------|--------|----------|------------|
    | Florida    | 170312 | 19552860 | 114.806121 |
    | New York   | 141297 | 19651127 | 139.076746 |
    

```
---
layout: default
title: Pandas Object
parent: Pandas
nav_order: 10
has_children: false
permalink: /docs/pandas/pandasObject
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Introducing Pandas Objects
매우 기본적인 수준에서 Pandas 객체는 행과 열이 단순한 정수 인덱스가 아닌 레이블로 식별되는 NumPy 구조화된 배열의 향상된 버전으로 생각할 수 있습니다. 이 장의 과정에서 볼 수 있듯이 Pandas는 기본 데이터 구조 외에도 유용한 도구, 방법 및 기능을 제공하지만, 이어지는 거의 모든 항목에서는 이러한 구조가 무엇인지 이해해야 합니다. 따라서 더 진행하기 전에 Series, DataFrame 및 Index의 세 가지 기본 Pandas 데이터 구조를 소개하겠습니다.

## The Pandas Series Object 
Pandas의 ``Series``는 인덱싱된 데이터의 1차원 배열입니다.
다음과 같이 목록 또는 배열에서 만들 수 있습니다.

```python 
data = pd.Series([0.25, 0.5, 0.75, 1.0])
data 
    0    0.25
    1    0.50
    2    0.75
    3    1.00
    dtype: float64

data.values
    array([ 0.25,  0.5 ,  0.75,  1.  ])
```

출력에서 볼 수 있듯이 Series는 값 및 인덱스 속성으로 액세스할 수 있는 값 시퀀스와 인덱스 시퀀스를 모두 래핑합니다. 값은 단순히 친숙한 NumPy 배열입니다.

인덱스는 pd.Index 유형의 배열과 유사한 개체입니다. 이에 대해서는 잠시 후에 더 자세히 설명하겠습니다.

```python 
data.index
    RangeIndex(start=0, stop=4, step=1)
```

NumPy 배열과 마찬가지로 친숙한 Python 대괄호 표기법을 통해 연결된 인덱스에서 데이터에 액세스할 수 있습니다. 

```python 
data[1] 
    0.5
data[1:3]
    1    0.50
    2    0.75
    dtype: float64
```

앞으로 보게 되겠지만 Pandas 시리즈는 에뮬레이트하는 1차원 NumPy 배열보다 훨씬 더 일반적이고 유연합니다.


### Series as generalized NumPy array 
지금까지 본 것에서 Series 객체는 기본적으로 1차원 NumPy 배열과 교환할 수 있는 것처럼 보일 수 있습니다. 근본적인 차이점은 인덱스가 있다는 것입니다. Numpy Array에는 값에 액세스하는 데 사용되는 암시적으로 정의된 정수 인덱스가 있는 반면 Pandas 시리즈에는 값과 연결된 명시적으로 정의된 인덱스가 있습니다.

이 명시적 인덱스 정의는 Series 개체에 추가 기능을 제공합니다. 예를 들어, 인덱스는 정수일 필요는 없지만 원하는 유형의 값으로 구성될 수 있습니다. 예를 들어 원하는 경우 문자열을 인덱스로 사용할 수 있습니다.


```python
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

data = pd.Series([0.25, 0.5, 0.75, 1.0],
                 index=[2, 5, 3, 7])
data
    2    0.25
    5    0.50
    3    0.75
    7    1.00
    dtype: float64

data[5]
    0.5
```


### Series as specialized dictionary
NumPy 배열 뒤에 있는 유형별 컴파일된 코드가 특정 작업에 대해 Python 목록보다 더 효율적으로 만드는 것처럼 Pandas ``Series``의 유형 정보는 특정 작업에 대해 Python 사전보다 훨씬 더 효율적입니다.

기본적으로 정렬된 키에서 인덱스를 가져오는 시리즈가 생성됩니다. 여기에서 일반적인 사전 스타일 항목 액세스를 수행할 수 있습니다.key(Index) + value 
```python 
population_dict = {'California': 38332521,
                   'Texas': 26448193,
                   'New York': 19651127,
                   'Florida': 19552860,
                   'Illinois': 12882135}
population = pd.Series(population_dict)
population
    California    38332521
    Florida       19552860
    Illinois      12882135
    New York      19651127
    Texas         26448193
    dtype: int64

population['California']
    38332521

# supports array-style operations such as slicing: 
population['California':'Illinois']
    California    38332521
    Florida       19552860
    Illinois      12882135
    dtype: int64
```

### Series 객체 생성하기 

pd.Series(data, index=index) 
index parameter is an optional argument and data can be on of many entities. 

```python 
pd.Series([2, 4, 6])
    0    2
    1    4
    2    6
    dtype: int64


pd.Series(5, index=[100, 200, 300])
    100    5
    200    5
    300    5
    dtype: int64

pd.Series({2:'a', 1:'b', 3:'c'})
    1    b
    2    a
    3    c
    dtype: object

pd.Series(
    {
        2:'a', 
        1:'b', 
        3:'c'
    }, index=[3, 2])
    3    c
    2    a
    dtype: object

```


## The Pandas DataFrame Object 
Pandas의 다음 기본 구조는 DataFrame입니다. 이전 섹션에서 논의한 Series 객체와 마찬가지로 DataFrame은 NumPy 배열의 일반화 또는 Python 사전의 특수화로 생각할 수 있습니다. 이제 이러한 각 관점을 살펴보겠습니다.


### numpy array를 이용한 DataFrame 
Series가 유연한 인덱스가 있는 1차원 배열의 유사체인 경우 DataFrame은 유연한 행 인덱스와 유연한 열 이름이 모두 있는 2차원 배열의 유사체입니다. 2차원 배열을 정렬된 1차원 열의 정렬된 시퀀스로 생각할 수 있는 것처럼 DataFrame을 정렬된 Series 개체의 시퀀스로 생각할 수 있습니다. 여기서 "정렬"이라는 것은 동일한 인덱스를 공유한다는 의미입니다.

```python 
area_dict = {'California': 423967, 'Texas': 695662, 'New York': 141297,
             'Florida': 170312, 'Illinois': 149995}
area = pd.Series(area_dict)
area
    California    423967
    Florida       170312
    Illinois      149995
    New York      141297
    Texas         695662
    dtype: int64

population_dict = {'California': 38332521,
                   'Texas': 26448193,
                   'New York': 19651127,
                   'Florida': 19552860,
                   'Illinois': 12882135}
population = pd.Series(population_dict)
population
    California    38332521
    Florida       19552860
    Illinois      12882135
    New York      19651127
    Texas         26448193
    dtype: int64

# Index를 통한 Col append 가 된다. 
states = pd.DataFrame({'population': population,
                       'area': area})
states
    |            | area   | population |
    |------------|--------|------------|
    | California | 423967 | 38332521   |
    | Florida    | 170312 | 19552860   |
    | Illinois   | 149995 | 12882135   |
    | New York   | 141297 | 19651127   |
    | Texas      | 695662 | 26448193   |


states.index
    Index(['area', 'population'], dtype='object')


```

### DataFrame as specialized dictionary 
컬럼 명을 이용하여 선택시 Series 객체가 반환된다. (Index - value[specialized dictionary]) , 2차원 NumPy 배열에서 data[0]은 첫 번째 행을 반환합니다. DataFrame의 경우 data['col0']은 첫 번째 열을 반환합니다. 이 때문에 상황을 보는 두 가지 방법 모두 유용할 수 있지만 DataFrames를 일반화된 배열보다 일반화된 사전으로 생각하는 것이 더 나을 수 있습니다. 데이터 인덱싱 및 선택에서 DataFrame을 인덱싱하는 보다 유연한 방법을 살펴보겠습니다.

```python 
states['area']
    California    423967
    Florida       170312
    Illinois      149995
    New York      141297
    Texas         695662
    Name: area, dtype: int64
```


### Constructing DataFrame objects 
Pandas의 ``DataFrame``은 다양한 방법으로 구성할 수 있습니다.
여기에서 우리는 몇 가지 예를 제공할 것입니다.

* DataFrame은 Series 개체의 모음이며 단일 열 DataFrame은 단일 Series에서 구성할 수 있습니다.
* 모든 사전 목록을 DataFrame으로 만들 수 있습니다. 간단한 목록 이해를 사용하여 일부 데이터를 생성합니다.
* 사전의 일부 키가 누락된 경우에도 Pandas는 NaN(즉, "숫자가 아님") 값으로 키를 채웁니다.
* 2차원 데이터 배열이 주어지면 지정된 열 및 인덱스 이름으로 DataFrame을 만들 수 있습니다. 생략하면 각각에 대해 정수 인덱스가 사용됩니다.
* 구조적 데이터: NumPy의 구조적 배열에서 구조적 배열을 다뤘습니다. Pandas DataFrame은 구조화된 배열과 매우 유사하게 작동하며 다음 중 하나에서 직접 생성할 수 있습니다.

**DataFrame은 Series 개체의 모음이며 단일 열 DataFrame은 단일 Series에서 구성할 수 있습니다.**

```python 
pd.DataFrame(population, columns=['population'])
    |            | population |
    |------------|------------|
    | California | 38332521   |
    | Florida    | 19552860   |
    | Illinois   | 12882135   |
    | New York   | 19651127   |
    | Texas      | 26448193   |

data = [{'a': i, 'b': 2 * i}
        for i in range(3)]
pd.DataFrame(data)
    |   | a | b |
    |---|---|---|
    | 0 | 0 | 0 |
    | 1 | 1 | 2 |
    | 2 | 2 | 4 |

pd.DataFrame([{'a': 1, 'b': 2}, {'b': 3, 'c': 4}])
    |   | a   | b | c   |
    |---|-----|---|-----|
    | 0 | 1   | 2 | NaN |
    | 1 | NaN | 3 | 4   |

pd.DataFrame(np.random.rand(3, 2),
             columns=['foo', 'bar'],
             index=['a', 'b', 'c'])

    |   | foo      | bar      |
    |---|----------|----------|
    | a | 0.865257 | 0.213169 |
    | b | 0.442759 | 0.108267 |
    | c | 0.04711  | 0.905718 |

A = np.zeros(3, dtype=[('A', 'i8'), ('B', 'f8')])
A
    array(  [(0, 0.0), (0, 0.0), (0, 0.0)], 
            dtype=[('A', '<i8'), ('B', '<f8')])

pd.DataFrame(A)
    |   | A | B |
    |---|---|---|
    | 0 | 0 | 0 |
    | 1 | 0 | 0 |
    | 2 | 0 | 0 |
```


## The Pandas Index Object 
여기에서 ``Series`` 및 ``DataFrame`` 객체 모두 데이터를 참조하고 수정할 수 있는 명시적 *인덱스*를 포함하고 있음을 확인했습니다.
이 ``Index`` 객체는 그 자체로 흥미로운 구조이며, *불변 배열* 또는 *순서화된 집합*(기술적으로 ``Index`` 객체에 포함될 수 있는 다중 집합)으로 생각할 수 있습니다. 반복되는 값).
이러한 보기는 ``색인`` 개체에서 사용할 수 있는 작업에서 몇 가지 흥미로운 결과를 가져옵니다.
간단한 예로 정수 목록에서 ``색인``을 구성해 보겠습니다.

```python 
ind = pd.Index([2, 3, 5, 7, 11])
ind
    Int64Index([2, 3, 5, 7, 11], dtype='int64')


```

### Index as immutable array 
인덱스는 여러 면에서 배열처럼 작동합니다. 예를 들어 표준 Python 인덱싱 표기법을 사용하여 값이나 조각을 검색할 수 있습니다.

```python 
ind[1]
    3
ind[::2]
    Int64Index([2, 5, 11], dtype='int64')

# Numpy 속성 정보 
print(ind.size, ind.shape, ind.ndim, ind.dtype)
    5 (5,) 1 int64
```

인덱스 객체와 NumPy 배열의 한 가지 차이점은 인덱스가 변경 불가능하다는 것입니다. 즉, 일반적인 수단을 통해 수정할 수 없습니다. 이러한 불변성은 우발적인 인덱스 수정으로 인한 부작용의 가능성 없이 여러 DataFrame과 배열 간에 인덱스를 더 안전하게 공유할 수 있도록 합니다.

```python
ind[1] = 0
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-7-906a9fa1424c> in <module>
    ----> 1 ind[1] = 0

    ~/miniforge3/envs/ml/lib/python3.8/site-packages/pandas/core/indexes/base.py in __setitem__(self, key, value)
    4275     @final
    4276     def __setitem__(self, key, value):
    -> 4277         raise TypeError("Index does not support mutable operations")
    4278 
    4279     def __getitem__(self, key):

    TypeError: Index does not support mutable operations
```

### Index as ordered set 
Pandas 개체는 집합 산술의 여러 측면에 따라 달라지는 데이터 집합 간의 조인과 같은 작업을 용이하게 하도록 설계되었습니다.
``Index`` 객체는 Python의 내장 ``set`` 데이터 구조에서 사용하는 많은 규칙을 따르므로 합집합, 교집합, 차이 및 기타 조합을 익숙한 방식으로 계산할 수 있습니다.

```python 
indA = pd.Index([1, 3, 5, 7, 9])
indB = pd.Index([2, 3, 5, 7, 11])

indA & indB  # 교집합
    Int64Index([3, 5, 7], dtype='int64')

indA | indB  # 합집합
    Int64Index([1, 2, 3, 5, 7, 9, 11], dtype='int64')

indA ^ indB  # 대칭차(두 집합의 상대 여집합의 합)
    Int64Index([1, 2, 9, 11], dtype='int64')

```
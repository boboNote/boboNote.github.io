---
layout: default
title: Hierarchical Indexing
parent: Pandas
nav_order: 40
has_children: false
permalink: /docs/pandas/hierarchical 
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Hierarchical Indexing
지금까지는 Pandas Series 및 DataFrame 개체에 각각 저장된 1차원 및 2차원 데이터에 주로 중점을 두었습니다. 종종 이것을 넘어 더 높은 차원의 데이터, 즉 하나 또는 두 개 이상의 키로 인덱싱된 데이터를 저장하는 것이 유용합니다. Pandas는 3차원 및 4차원 데이터를 기본적으로 처리하는 Panel 및 Panel4D 개체를 제공하지만(Aside: Panel Data 참조) 실제로 훨씬 더 일반적인 패턴은 계층적 인덱싱(다중 인덱싱이라고도 함)을 사용하여 다음을 수행하는 것입니다. 단일 인덱스 내에서 여러 인덱스 수준을 통합합니다. 이러한 방식으로 고차원 데이터는 친숙한 1차원 Series 및 2차원 DataFrame 개체 내에서 간결하게 표현될 수 있습니다.

이 섹션에서는 MultiIndex 개체의 직접 생성, 다중 인덱싱된 데이터에서 통계를 인덱싱, 분할 및 계산할 때의 고려 사항, 데이터의 단순 표현과 계층적으로 인덱싱된 표현 사이를 변환하는 데 유용한 루틴을 살펴봅니다.


## A Multiply Indexed Series
1차원 ``시리즈`` 내에서 2차원 데이터를 표현하는 방법을 고려하는 것으로 시작하겠습니다.
구체적으로 각 포인트에 문자와 숫자 키가 있는 일련의 데이터를 고려할 것입니다.

### The bad way 
서로 다른 두 연도의 주에 대한 데이터를 추적하려고 한다고 가정합니다. 우리가 이미 다룬 Pandas 도구를 사용하면 단순히 Python 튜플을 키로 사용하고 싶은 유혹을 받을 수 있습니다.

```python 
index = [('California', 2000), ('California', 2010),
         ('New York', 2000), ('New York', 2010),
         ('Texas', 2000), ('Texas', 2010)
         
populations = [33871648, 37253956,
               18976457, 19378102,
               20851820, 25145561]
pop = pd.Series(populations, index=index)
pop
    (California, 2000)    33871648
    (California, 2010)    37253956
    (New York, 2000)      18976457
    (New York, 2010)      19378102
    (Texas, 2000)         20851820
    (Texas, 2010)         25145561
    dtype: int64

pop[('California', 2010):('Texas', 2000)]
    (California, 2010)    37253956
    (New York, 2000)      18976457
    (New York, 2010)      19378102
    (Texas, 2000)         20851820
    dtype: int64

pop.iloc[1:5]
    (California, 2010)    37253956
    (New York, 2000)      18976457
    (New York, 2010)      19378102
    (Texas, 2000)         20851820
    dtype: int64


pop[[i for i in pop.index if i[1] == 2010]]
    (California, 2010)    37253956
    (New York, 2010)      19378102
    (Texas, 2010)         25145561
    dtype: int64

```

### The Better Way: Pandas MultiIndex 
튜플 기반 인덱싱은 기본적으로 기본적인 다중 인덱스이며 Pandas의 ``MultiIndex`` 유형은 우리가 원하는 작업 유형을 제공합니다.
다음과 같이 튜플에서 다중 인덱스를 만들 수 있습니다.

```python 
index = pd.MultiIndex.from_tuples(index)
index
    MultiIndex(levels=[
                ['California', 'New York', 'Texas'], 
                [2000, 2010]],
            labels=[[0, 0, 1, 1, 2, 2], [0, 1, 0, 1, 0, 1]]
            )

pop = pop.reindex(index)
pop
    California  2000    33871648
                2010    37253956
    New York    2000    18976457
                2010    19378102
    Texas       2000    20851820
                2010    25145561
    dtype: int64
```

MultiIndex에는 여러 수준의 인덱싱이 포함되어 있습니다. 이 경우 주 이름과 연도, 이러한 수준을 인코딩하는 각 데이터 포인트에 대한 여러 레이블이 있습니다.
이 MultiIndex로 시리즈를 다시 인덱싱하면 데이터의 계층적 표현이 표시됩니다.

여기서 시리즈 표현의 처음 두 열은 여러 인덱스 값을 표시하고 세 번째 열은 데이터를 표시합니다. 첫 번째 열에서 일부 항목이 누락되었음을 알 수 있습니다. 이 다중 색인 표현에서 빈 항목은 그 위의 행과 동일한 값을 나타냅니다.

**Index에도 Shape이 존재하며 slice를 이용하여 Selection 된다.**

```python 
pop[:, 2010]
    California    37253956
    New York      19378102
    Texas         25145561
    dtype: int64

pop['California' , 2010]
    37253956
```

### MultiIndex as extra dimension 
인덱스 및 열 레이블이 있는 간단한 ``DataFrame``을 사용하여 동일한 데이터를 쉽게 저장할 수 있습니다.
사실 Pandas는 이러한 동등성을 염두에 두고 만들어졌습니다. ``unstack()`` 메소드는 다중 인덱싱된 ``Series``를 기존의 인덱싱된 ``DataFrame``으로 빠르게 변환합니다.

```python
########################################
# pop은 Series 객체이며 
# unstack 하는 순가 DataFrame 으로 변경된다. 
########################################
pop_df = pop.unstack()
pop_df
    |            | 2000     | 2010     |
    |------------|----------|----------|
    | California | 33871648 | 37253956 |
    | New York   | 18976457 | 19378102 |
    | Texas      | 20851820 | 25145561 |

```

다중 인덱싱을 사용하여 1차원 Series 내에서 2차원 데이터를 나타낼 수 있었던 것처럼 Series 또는 DataFrame에서 3차원 이상의 데이터를 나타내는 데 사용할 수도 있습니다. 다중 인덱스의 각 추가 수준은 데이터의 추가 차원을 나타냅니다. 이 속성을 활용하면 표현할 수 있는 데이터 유형에서 훨씬 더 많은 유연성을 얻을 수 있습니다. 구체적으로, 매년 각 주의 인구통계학적 데이터 열을 추가할 수 있습니다(예: 18세 미만 인구). MultiIndex를 사용하면 DataFrame에 다른 열을 추가하는 것만큼 쉽습니다.

```python 

########################################
# pop_df은 DataFrame 객체이며 
# stack 하는 순가 Series 객체로 변경된다. 
########################################
pop_df.stack()
    California  2000    33871648
                2010    37253956
    New York    2000    18976457
                2010    19378102
    Texas       2000    20851820
                2010    25145561
    dtype: int64

############################################
# .DataFrame 을 이용하여 객체 생성시 아래와 같이 Series 객체가 
# DataFrame 객체로 변경된다. 여기서 컬럼(Axis=1) 추가가 가능하다. 
############################################
pop_df = pd.DataFrame({'total': pop,
                       'under18': [9267089, 9284094,
                                   4687374, 4318033,
                                   5906301, 6879014]})
pop_df
    |            |      | total    | under18 |
    |------------|------|----------|---------|
    | California | 2000 | 33871648 | 9267089 |
    |            | 2010 | 37253956 | 9284094 |
    | New York   | 2000 | 18976457 | 4687374 |
    |            | 2010 | 19378102 | 4318033 |
    | Texas      | 2000 | 20851820 | 5906301 |
    |            | 2010 | 25145561 | 6879014 |


f_u18 = pop_df['under18'] / pop_df['total']
f_u18
    California  2000    0.273594
                2010    0.249211
    New York    2000    0.247010
                2010    0.222831
    Texas       2000    0.283251
                2010    0.273568
    dtype: float64

f_u18.unstack()
|            | 2000     | 2010     |
|------------|----------|----------|
| California | 0.273594 | 0.249211 |
| New York   | 0.24701  | 0.222831 |
| Texas      | 0.283251 | 0.273568 |
    
```

## Methods of MultiIndex Creation
다중 인덱싱된 ``Series`` 또는 ``DataFrame``을 구성하는 가장 간단한 방법은 생성자에 두 개 이상의 인덱스 배열 목록을 전달하는 것입니다.

```python
df = pd.DataFrame(np.random.rand(4, 2),
                  index=[['a', 'a', 'b', 'b'], [1, 2, 1, 2]],
                  columns=['data1', 'data2'])
df
    |   |   | data1    | data2    |
    |---|---|----------|----------|
    | a | 1 | 0.554233 | 0.356072 |
    |   | 2 | 0.925244 | 0.219474 |
    | b | 1 | 0.441759 | 0.610054 |
    |   | 2 | 0.171495 | 0.886688 |


```
MultiIndex 생성 작업은 백그라운드에서 수행됩니다.
마찬가지로 적절한 튜플이 있는 사전을 키로 전달하면 Pandas는 이를 자동으로 인식하고 기본적으로 MultiIndex를 사용합니다.

```python
data = {('California', 2000): 33871648,
        ('California', 2010): 37253956,
        ('Texas', 2000): 20851820,
        ('Texas', 2010): 25145561,
        ('New York', 2000): 18976457,
        ('New York', 2010): 19378102}
pd.Series(data)

    California  2000    33871648
                2010    37253956
    New York    2000    18976457
                2010    19378102
    Texas       2000    20851820
                2010    25145561
    dtype: int64

```

###  Explicit MultiIndex constructors 
인덱스 구성 방식의 유연성을 높이려면 ``pd.MultiIndex``에서 사용할 수 있는 클래스 메서드 생성자를 대신 사용할 수 있습니다.
예를 들어, 이전에 했던 것처럼 각 수준 내에서 인덱스 값을 제공하는 간단한 배열 목록에서 ``MultiIndex``를 구성할 수 있습니다. 

```python 
pd.MultiIndex.from_arrays([['a', 'a', 'b', 'b'], [1, 2, 1, 2]])
pd
    MultiIndex([('a', 1),
                ('a', 2),
                ('b', 1),
                ('b', 2)],
            )

pd.MultiIndex.from_tuples([('a', 1), ('a', 2), ('b', 1), ('b', 2)])
    MultiIndex([('a', 1),
                ('a', 2),
                ('b', 1),
                ('b', 2)],
            )

pd.MultiIndex.from_product([['a', 'b'], [1, 2]])
    MultiIndex([('a', 1),
                ('a', 2),
                ('b', 1),
                ('b', 2)],
            )

```

### MultiIndex level names 
때로는 MultiIndex의 수준에 이름을 지정하는 것이 편리합니다. 이것은 위의 MultiIndex 생성자 중 하나에 names 인수를 전달하거나 사실 뒤에 인덱스의 names 속성을 설정하여 수행할 수 있습니다.

```python 
print(type(pop))
pop
    <class 'pandas.core.series.Series'>

    California  2000    33871648
                2010    37253956
    New York    2000    18976457
                2010    19378102
    Texas       2000    20851820
                2010    25145561
    dtype: int64

###################################
# Index names 변경 
###################################
pop.index.names = ['state', 'year']
pop
    state       year
    California  2000    33871648
                2010    37253956
    New York    2000    18976457
                2010    19378102
    Texas       2000    20851820
                2010    25145561
    dtype: int64
```

### MultiIndex for columns 
DataFrame에서 행과 열은 완전히 대칭이며 행에 여러 수준의 인덱스가 있을 수 있는 것처럼 열에도 여러 수준이 있을 수 있습니다. 일부 (다소 현실적인) 의료 데이터의 모형인 다음을 고려하십시오.

```python 
#######################################################################
# 계층적 인덱스와 열
# 2 * 2 Shape을 가진 Index 를 생성한다 
#(2013,1) (2013,2) (2014,1) (2014,2)
# array([[-1.9,  1.5],
       [ 1.3, -0.5]]) 
#######################################################################
index = pd.MultiIndex.from_product([[2013, 2014], [1, 2]],
                                   names=['year', 'visit'])
columns = pd.MultiIndex.from_product([['Bob', 'Guido', 'Sue'], ['HR', 'Temp']],
                                     names=['subject', 'type'])

# 일부 데이터 모형 만들기
data = np.round(np.random.randn(4, 6), 1)

######################################################################
# ::2 =  [0 , 3 , 5 ]
# data[:,[0 , 3 , 5 ]] 와 data[:, ::2] 같다
######################################################################
data[:, ::2] *= 10
data += 37

# DataFrame 생성하기
health_data = pd.DataFrame(data, index=index, columns=columns)
health_data
    |      | subject | Bob       || Guido       || Sue       ||
    |------|---------|-----|------|-------|------|-----|------|
    |      | type    | HR  | Temp | HR    | Temp | HR  | Temp |
    | year | visit   |     |      |       |      |     |      |
    | 2013 | 1       | 30  | 37.5 | 47    | 36.7 | 43  | 36.8 |
    |      | 2       | 34  | 37.9 | 39    | 36.8 | 39  | 35.9 |
    | 2014 | 1       | 29  | 35.7 | 45    | 36.6 | 24  | 37.5 |
    |      | 2       | 28  | 35.1 | 41    | 37.8 | 33  | 35.7 |


health_data['Guido']
    |      | type  | HR | Temp |
    |------|-------|----|------|
    | year | visit |    |      |
    | 2013 | 1     | 32 | 36.7 |
    |      | 2     | 50 | 35   |
    | 2014 | 1     | 39 | 37.8 |
    |      | 2     | 48 | 37.3 |

```


## Indexing and Slicing a MultiIndex 
``MultiIndex``에 대한 인덱싱 및 슬라이싱은 직관적으로 설계되었으며 인덱스를 추가된 차원으로 생각하면 도움이 됩니다.
먼저 다중 인덱싱된 ``Series``를 인덱싱한 다음 다중 인덱싱된 ``DataFrame``을 살펴보겠습니다. 

### Multiply indexed Series 
```python
pop
    state       year
    California  2000    33871648
                2010    37253956
    New York    2000    18976457
                2010    19378102
    Texas       2000    20851820
                2010    25145561
    dtype: int64

pop['California', 2000]
    33871648

pop['California']
    year
    2000    33871648
    2010    37253956
    dtype: int64


print(pop['California'])
<class 'pandas.core.series.Series'>


pop.loc['California':'New York']
    state       year
    California  2000    33871648
                2010    37253956
    New York    2000    18976457
                2010    19378102
    dtype: int64

pop[:, 2000]
    state
    California    33871648
    New York      18976457
    Texas         20851820
    dtype: int64

# 다른 유형의 인덱싱 및 선택(데이터 인덱싱 및 선택에서 설명)도 작동합니다 With Mask
pop[pop > 22000000]
    state       year
    California  2000    33871648
                2010    37253956
    Texas       2010    25145561
    dtype: int64


pop[['California', 'Texas']]
    state       year
    California  2000    33871648
                2010    37253956
    Texas       2000    20851820
                2010    25145561
    dtype: int64

```

### Multiply indexed DataFrames
다중 인덱싱된 ``DataFrame``은 비슷한 방식으로 작동합니다.
이전의 장난감 의료용 ``DataFrame``을 고려하십시오.

```python 
index = pd.MultiIndex.from_product([[2013, 2014], [1, 2]],
                                   names=['year', 'visit'])
columns = pd.MultiIndex.from_product([['Bob', 'Guido', 'Sue'], ['HR', 'Temp']],
                                     names=['subject', 'type'])

# 일부 데이터 모형 만들기
data = np.round(np.random.randn(4, 6), 1)
data[:, ::2] *= 10
data += 37

# DataFrame 생성하기
health_data = pd.DataFrame(data, index=index, columns=columns)
health_data
    |      | subject | Bob |      | Guido |      | Sue |      |
    |------|---------|-----|------|-------|------|-----|------|
    |      | type    | HR  | Temp | HR    | Temp | HR  | Temp |
    | year | visit   |     |      |       |      |     |      |
    | 2013 | 1       | 31  | 38.7 | 32    | 36.7 | 35  | 37.2 |
    |      | 2       | 44  | 37.7 | 50    | 35   | 29  | 36.7 |
    | 2014 | 1       | 30  | 37.4 | 39    | 37.8 | 61  | 36.9 |
    |      | 2       | 47  | 37.8 | 48    | 37.3 | 51  | 36.5 |


data_mean = health_data.mean(level='year')
data_mean
    | subject | Bob  |      | Guido |       | Sue |       |
    |---------|------|------|-------|-------|-----|-------|
    | type    | HR   | Temp | HR    | Temp  | HR  | Temp  |
    | year    |      |      |       |       |     |       |
    | 2013    | 37.5 | 38.2 | 41    | 35.85 | 32  | 36.95 |
    | 2014    | 38.5 | 37.6 | 43.5  | 37.55 | 56  | 36.7  |

data_mean.mean(axis=1, level='type')
    | type | HR        | Temp      |
    |------|-----------|-----------|
    | year |           |           |
    | 2013 | 36.833333 | 37        |
    | 2014 | 46        | 37.283333 |

```
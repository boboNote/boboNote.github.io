---
layout: default
title: Aggregation and Grouping
parent: Pandas
nav_order: 70
has_children: false
permalink: /docs/pandas/AggregationAndGrouping
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Aggregation and Grouping
대용량 데이터 분석의 필수적인 부분은 효율적인 요약입니다. sum(), mean(), median(), min() 및 max()와 같은 집계 계산에서 단일 숫자는 잠재적으로 큰 데이터의 특성에 대한 통찰력을 제공합니다. 데이터세트. 이 섹션에서는 NumPy 배열에서 본 것과 유사한 간단한 작업에서 groupby 개념을 기반으로 하는 보다 정교한 작업에 이르기까지 Pandas의 집계를 살펴봅니다.

```python
import pandas as pd
import numpy as np

class display(object):
    """Display HTML representation of multiple objects"""
    template = """<div style="float: left; padding: 10px;">
    <p style='font-family:"Courier New", Courier, monospace'>{0}</p>{1}
    </div>"""
    def __init__(self, *args):
        self.args = args
        
    def _repr_html_(self):
        return '\n'.join(self.template.format(a, eval(a)._repr_html_())
                         for a in self.args)
    
    def __repr__(self):
        return '\n\n'.join(a + '\n' + repr(eval(a))
                           for a in self.args)
```

## Planets Data
여기에서는 [Seaborn 패키지](http://seaborn.pydata.org/)를 통해 사용할 수 있는 Planet 데이터 세트를 사용합니다([Visualization With Seaborn](04.14-Visualization-With-Seaborn.ipynb) 참조).
천문학자들이 다른 별 주변에서 발견한 행성에 대한 정보를 제공합니다(*외계행성* 또는 *외계행성*으로 줄여서 알려짐). 간단한 Seaborn 명령으로 다운로드할 수 있습니다.

```python 
import seaborn as sns
planets = sns.load_dataset('planets')
planets.shape
    (1035, 6)
planets.head()
    |   | method          | number | orbital_period | mass | distance | year |
    |---|-----------------|--------|----------------|------|----------|------|
    | 0 | Radial Velocity | 1      | 269.3          | 7.1  | 77.4     | 2006 |
    | 1 | Radial Velocity | 1      | 874.774        | 2.21 | 56.95    | 2008 |
    | 2 | Radial Velocity | 1      | 763            | 2.6  | 19.84    | 2011 |
    | 3 | Radial Velocity | 1      | 326.03         | 19.4 | 110.62   | 2007 |
    | 4 | Radial Velocity | 1      | 516.22         | 10.5 | 119.47   | 2009 |
    
```

This has some details on the 1,000+ extrasolar planets discovered up to 2014.


##  Simple Aggregation in Pandas
이전에 NumPy 배열에 사용할 수 있는 일부 데이터 집계("집계: 최소, 최대 및 모든 것")를 살펴보았습니다. 1차원 NumPy 배열과 마찬가지로 Pandas 시리즈의 경우 집계는 단일 값을 반환합니다.

```python 
rng = np.random.RandomState(42)
ser = pd.Series(rng.rand(5))   # 평균 0 표준편차 1 인 
ser
    0    0.374540
    1    0.950714
    2    0.731994
    3    0.598658
    4    0.156019
    dtype: float6

ser.sum()
    2.811925491708157

ser.mean()
    0.5623850983416314

df = pd.DataFrame({'A': rng.rand(5),
                   'B': rng.rand(5)})
df
    |   | A        | B        |
    |---|----------|----------|
    | 0 | 0.155995 | 0.020584 |
    | 1 | 0.058084 | 0.96991  |
    | 2 | 0.866176 | 0.832443 |
    | 3 | 0.601115 | 0.212339 |
    | 4 | 0.708073 | 0.181825 |

df.mean()
    A    0.477888
    B    0.443420
    dtype: float64

df.mean(axis='columns') # or df.mean(axis=1) 
    0    0.088290
    1    0.513997
    2    0.849309
    3    0.406727
    4    0.444949
    dtype: float64
```

Pandas Series 및 DataFrames에는 Aggregations: Min, Max 및 Everything In Between에 언급된 모든 공통 집계가 포함됩니다. 또한 각 열에 대해 여러 공통 집계를 계산하고 결과를 반환하는 편리한 메서드 describe()가 있습니다. 이제 누락된 값이 있는 행을 삭제하기 위해 Planet 데이터에서 이것을 사용하겠습니다.


```python 
planets.describe()
    |       | number   | orbital_period | mass     | distance   | year        |
    |-------|----------|----------------|----------|------------|-------------|
    | count | 1035     | 992            | 513      | 808        | 1035        |
    | mean  | 1.785507 | 2002.917596    | 2.638161 | 264.069282 | 2009.070531 |
    | std   | 1.240976 | 26014.7283     | 3.818617 | 733.116493 | 3.972567    |
    | min   | 1        | 0.090706       | 0.0036   | 1.35       | 1989        |
    | 25%   | 1        | 5.44254        | 0.229    | 32.56      | 2007        |
    | 50%   | 1        | 39.9795        | 1.26     | 55.25      | 2010        |
    | 75%   | 2        | 526.005        | 3.04     | 178.5      | 2012        |
    | max   | 7        | 730000         | 25       | 8500       | 2014        |

planets.dropna().describe()
|       | number  | orbital_period | mass     | distance  | year       |
|-------|---------|----------------|----------|-----------|------------|
| count | 498     | 498            | 498      | 498       | 498        |
| mean  | 1.73494 | 835.778671     | 2.50932  | 52.068213 | 2007.37751 |
| std   | 1.17572 | 1469.128259    | 3.636274 | 46.596041 | 4.167284   |
| min   | 1       | 1.3283         | 0.0036   | 1.35      | 1989       |
| 25%   | 1       | 38.27225       | 0.2125   | 24.4975   | 2005       |
| 50%   | 1       | 357            | 1.245    | 39.94     | 2009       |
| 75%   | 2       | 999.6          | 2.8675   | 59.3325   | 2011       |
| max   | 6       | 17337.5        | 25       | 354       | 2014       |
```

이는 데이터세트의 전체 속성을 이해하는 데 유용한 방법이 될 수 있습니다. 예를 들어, 연도 열에서 우리는 외계행성이 1989년에 발견되었지만 알려진 모든 외계행성 중 절반이 2010년 또는 그 이후까지 발견되지 않았음을 알 수 있습니다. 이것은 주로 다른 별 주위의 일식 행성을 찾기 위해 특별히 설계된 우주 기반 망원경인 케플러 임무 덕분입니다.

The following table summarizes some other built-in Pandas aggregations:

| Aggregation              | Description                     |
|--------------------------|---------------------------------|
| ``count()``              | Total number of items           |
| ``first()``, ``last()``  | First and last item             |
| ``mean()``, ``median()`` | Mean and median                 |
| ``min()``, ``max()``     | Minimum and maximum             |
| ``std()``, ``var()``     | Standard deviation and variance |
| ``mad()``                | Mean absolute deviation         |
| ``prod()``               | Product of all items            |
| ``sum()``                | Sum of all items                |


이들은 모두 ``DataFrame`` 및 ``Series`` 객체의 메소드입니다. 

그러나 데이터에 더 깊이 들어가려면 단순한 집계만으로는 충분하지 않은 경우가 많습니다. 다음 수준의 데이터 요약은 데이터 하위 집합에 대한 집계를 빠르고 효율적으로 계산할 수 있는 groupby 작업입니다. 

## GroupBy: Split, Apply, Combine 
간단한 집계는 데이터 세트의 특징을 제공할 수 있지만 종종 일부 레이블이나 인덱스에 대해 조건부로 집계하는 것을 선호합니다. 이는 소위 ``groupby`` 작업으로 구현됩니다.
"group by"라는 이름은 SQL 데이터베이스 언어의 명령에서 유래했지만 Rstats로 유명한 Hadley Wickham이 처음 만든 용어인 *split, apply, Combine*으로 생각하는 것이 더 명확할 것입니다.


### Split, apply, combine
"적용"이 합산 집계인 이 분할-적용-결합 작업의 표준 예가 다음 그림에 나와 있습니다. 
![split_transform_combined](https://miro.medium.com/max/770/1*JbF6nhrQsn4f-TaSF6IR9g.png)
<!-- [figure source in Appendix](06.00-Figure-Code.ipynb#Split-Apply-Combine) -->

* 분할 단계에는 지정된 키 값에 따라 DataFrame을 분할하고 그룹화하는 작업이 포함됩니다.  
* 적용 단계에는 개별 그룹 내에서 일반적으로 집계, 변환 또는 필터링과 같은 일부 기능을 계산하는 작업이 포함됩니다.
* 결합 단계는 이러한 작업의 결과를 출력 배열로 병합합니다. 

이것은 앞서 다룬 마스킹, 집계 및 병합 명령의 일부 조합을 사용하여 확실히 수동으로 수행할 수 있지만 중요한 인식은 중간 분할을 명시적으로 인스턴스화할 필요가 없다는 것입니다. 오히려 GroupBy는 (종종) 데이터에 대한 단일 패스로 이 작업을 수행하여 도중에 각 그룹에 대한 합계, 평균, 개수, 최소값 또는 기타 집계를 업데이트할 수 있습니다. GroupBy의 장점은 다음 단계를 추상화한다는 것입니다. 사용자는 내부에서 계산이 수행되는 방식에 대해 생각할 필요가 없고 오히려 작업 전체에 대해 생각합니다.

구체적인 예로 이 다이어그램에 표시된 계산에 Pandas를 사용하는 방법을 살펴보겠습니다. 입력 DataFrame을 만드는 것으로 시작하겠습니다.

```python 
df = pd.DataFrame({'key': ['A', 'B', 'C', 'A', 'B', 'C'],
                   'data': range(6)}, columns=['key', 'data'])
df
    |   | key | data |
    |---|-----|------|
    | 0 | A   | 0    |
    | 1 | B   | 1    |
    | 2 | C   | 2    |
    | 3 | A   | 3    |
    | 4 | B   | 4    |
    | 5 | C   | 5    |

```

가장 기본적인 split-apply-combine 작업은 원하는 키 열의 이름을 전달하는 DataFrames의 groupby() 메서드로 계산할 수 있습니다.

반환되는 것은 DataFrames 집합이 아니라 DataFrameGroupBy 개체입니다. 이 개체는 마법이 있는 곳입니다. DataFrame의 특별한 보기로 생각할 수 있습니다. 이 보기는 그룹을 파고들 준비가 되어 있지만 집계가 적용될 때까지 실제 계산을 하지 않습니다. 이 "지연 평가" 접근 방식은 공통 집계가 사용자에게 거의 투명한 방식으로 매우 효율적으로 구현될 수 있음을 의미합니다.

결과를 생성하기 위해 이 DataFrameGroupBy 개체에 집계를 적용할 수 있습니다. 그러면 원하는 결과를 생성하기 위해 적절한 적용/결합 단계를 수행합니다.

sum() 메서드는 여기에서 하나의 가능성일 뿐입니다. 다음 토론에서 볼 수 있듯이 거의 모든 일반적인 Pandas 또는 NumPy 집계 기능과 거의 모든 유효한 DataFrame 작업을 적용할 수 있습니다.


```python 
df.groupby('key')
    <pandas.core.groupby.DataFrameGroupBy object at 0x117272160>

df.groupby('key').sum()
    |     | data |
    |-----|------|
    | key |      |
    | A   | 3    |
    | B   | 5    |
    | C   | 7    |
```

### The GroupBy object 
``GroupBy`` 객체는 매우 유연한 추상화입니다.
여러 면에서 단순히 ``DataFrame``의 모음인 것처럼 취급할 수 있으며 내부에서 어려운 작업을 수행합니다. Planets 데이터를 사용하는 몇 가지 예를 살펴보겠습니다.

아마도 ``GroupBy``에서 사용할 수 있는 가장 중요한 작업은 **aggregate**, **filter**, **transform** 및 **apply**일 것입니다.
["Aggregate, Filter, Transform, Apply"](#Aggregate,-Filter,-Transform,-Apply)에서 이들 각각에 대해 더 자세히 논의할 것이지만, 그 전에 다음과 함께 사용할 수 있는 몇 가지 다른 기능을 소개하겠습니다. 기본 ``GroupBy`` 작업.

#### Column indexing 
``GroupBy`` 객체는 ``DataFrame``과 동일한 방식으로 열 인덱싱을 지원하며 수정된 ``GroupBy`` 객체를 반환합니다.

여기에서 열 이름을 참조하여 원래 DataFrame 그룹에서 특정 시리즈 그룹을 선택했습니다. GroupBy 객체와 마찬가지로 객체에 대한 집계를 호출할 때까지 계산이 수행되지 않습니다.

```python 
planets.groupby('method')
    <pandas.core.groupby.DataFrameGroupBy object at 0x1172727b8>

planets.groupby('method')['orbital_period']
    <pandas.core.groupby.generic.SeriesGroupBy object at 0x167e846a0>

planets.groupby('method')['orbital_period'].median()
    method
    Astrometry                         631.180000
    Eclipse Timing Variations         4343.500000
    Imaging                          27500.000000
    Microlensing                      3300.000000
    Orbital Brightness Modulation        0.342887
    Pulsar Timing                       66.541900
    Pulsation Timing Variations       1170.000000
    Radial Velocity                    360.200000
    Transit                              5.714932
    Transit Timing Variations           57.011000
    Name: orbital_period, dtype: float64

planets.groupby('method')['orbital_period'].max()

```

####  Iteration over groups 
``GroupBy`` 객체는 그룹에 대한 직접 반복을 지원하여 각 그룹을 ``Series`` 또는 ``DataFrame``으로 반환합니다. 

```python 
# shape을 보면 Group By 를 통해 Row 수를 확인 할수 있다. 
for (method, group) in planets.groupby('method'):
    print("{0:30s} shape={1}".format(method, group.shape))


    Astrometry                     shape=(2, 6)   # group by key = 'Astrometry', row 2 
    Eclipse Timing Variations      shape=(9, 6)   # group by key = 'Eclipse Timing Variations', row 9 
    Imaging                        shape=(38, 6)  # group by key = 'Eclipse Timing Variations', row 38 
    Microlensing                   shape=(23, 6)
    Orbital Brightness Modulation  shape=(3, 6)
    Pulsar Timing                  shape=(5, 6)
    Pulsation Timing Variations    shape=(1, 6)
    Radial Velocity                shape=(553, 6)
    Transit                        shape=(397, 6)
    Transit Timing Variations      shape=(4, 6)

```

이것은 특정 작업을 수동으로 수행하는 데 유용할 수 있지만, 내장된 ``적용`` 기능을 사용하는 것이 훨씬 빠른 경우가 많습니다. 이에 대해서는 잠시 후에 설명하겠습니다.

#### Dispatch methods
일부 Python 클래스 마술을 통해 ``GroupBy`` 객체에 의해 명시적으로 구현되지 않은 모든 메서드는 ``DataFrame`` 또는 ``Series`` 객체인지 여부에 관계없이 그룹을 통해 전달되고 호출됩니다.
예를 들어 ``DataFrame``의 ``describe()`` 메서드를 사용하여 데이터의 각 그룹을 설명하는 집계 집합을 수행할 수 있습니다.

```python 
planets.groupby('method')['year'].describe()
    |                               | count | mean        | std      | min  | 25%     | 50%    | 75%     | max  |
    |-------------------------------|-------|-------------|----------|------|---------|--------|---------|------|
    | method                        |       |             |          |      |         |        |         |      |
    | Astrometry                    | 2     | 2011.5      | 2.12132  | 2010 | 2010.75 | 2011.5 | 2012.25 | 2013 |
    | Eclipse Timing Variations     | 9     | 2010        | 1.414214 | 2008 | 2009    | 2010   | 2011    | 2012 |
    | Imaging                       | 38    | 2009.131579 | 2.781901 | 2004 | 2008    | 2009   | 2011    | 2013 |
    | Microlensing                  | 23    | 2009.782609 | 2.859697 | 2004 | 2008    | 2010   | 2012    | 2013 |
    | Orbital Brightness Modulation | 3     | 2011.666667 | 1.154701 | 2011 | 2011    | 2011   | 2012    | 2013 |
    | Pulsar Timing                 | 5     | 1998.4      | 8.38451  | 1992 | 1992    | 1994   | 2003    | 2011 |
    | Pulsation Timing Variations   | 1     | 2007        | NaN      | 2007 | 2007    | 2007   | 2007    | 2007 |
    | Radial Velocity               | 553   | 2007.518987 | 4.249052 | 1989 | 2005    | 2009   | 2011    | 2014 |
    | Transit                       | 397   | 2011.236776 | 2.077867 | 2002 | 2010    | 2012   | 2013    | 2014 |
    | Transit Timing Variations     | 4     | 2012.5      | 1.290994 | 2011 | 2011.75 | 2012.5 | 2013.25 | 2014 |


planets.groupby('method')['year'].describe().unstack()
            method                       
    count   Astrometry                          2.0
            Eclipse Timing Variations           9.0
            Imaging                            38.0
            Microlensing                       23.0
            Orbital Brightness Modulation       3.0
                                            ...  
    max     Pulsar Timing                    2011.0
            Pulsation Timing Variations      2007.0
            Radial Velocity                  2014.0
            Transit                          2014.0
            Transit Timing Variations        2014.0
    Length: 80, dtype: float64

```
이 표를 보면 데이터를 더 잘 이해하는 데 도움이 됩니다. 예를 들어, 대부분의 행성은 Radial Velocity 및 Transit 방법으로 발견되었지만 후자는 지난 10년 동안(새롭고 더 정확한 망원경으로 인해) 보편화되었습니다. . 최신 방법은 2011년까지 새로운 행성을 발견하는 데 사용되지 않은 Transit Timing Variation 및 Orbital Brightness Modulation인 것 같습니다.

이것은 디스패치 메소드의 유용성의 한 예일 뿐입니다. 각 개별 그룹에 적용되고 결과가 GroupBy 내에서 결합되어 반환됩니다. 다시 말하지만, 모든 유효한 DataFrame/Series 메서드를 해당 GroupBy 개체에서 사용할 수 있으므로 매우 유연하고 강력한 작업이 가능합니다!

### Aggregate, filter, transform, apply 
이전 논의에서는 결합 작업에 대한 집계에 중점을 두었지만 더 많은 옵션을 사용할 수 있습니다.
특히 ``GroupBy`` 객체에는 다양한 유용한 기능을 효율적으로 구현하는 ``aggregate()``, 그룹화된 데이터를 결합하기 전에 작업을 수행합니다.

다음 하위 섹션의 목적을 위해 이 ``DataFrame``을 사용합니다. 

```python 
rng = np.random.RandomState(0)
df = pd.DataFrame({'key': ['A', 'B', 'C', 'A', 'B', 'C'],
                   'data1': range(6),
                   'data2': rng.randint(0, 10, 6)},
                   columns = ['key', 'data1', 'data2'])
df
    |   | key | data1 | data2 |
    |---|-----|-------|-------|
    | 0 | A   | 0     | 5     |
    | 1 | B   | 1     | 0     |
    | 2 | C   | 2     | 3     |
    | 3 | A   | 3     | 3     |
    | 4 | B   | 4     | 7     |
    | 5 | C   | 5     | 9     |
```

#### Aggregation
우리는 이제 ``sum()``, ``median()`` 등을 사용한 ``GroupBy`` 집계에 익숙하지만 ``aggregate()`` 메서드를 사용하면 훨씬 더 유연합니다.
문자열, 함수 또는 그 목록을 사용하여 모든 집계를 한 번에 계산할 수 있습니다.
다음은 이 모든 것을 결합한 빠른 예입니다.

```python 
df.groupby('key').aggregate(['min', np.median, max])
    |     | data1 |        |     | data2 |        |     |
    |-----|-------|--------|-----|-------|--------|-----|
    |     | min   | median | max | min   | median | max |
    | key |       |        |     |       |        |     |
    | A   | 0     | 1.5    | 3   | 3     | 4      | 5   |
    | B   | 1     | 2.5    | 4   | 0     | 3.5    | 7   |
    | C   | 2     | 3.5    | 5   | 3     | 6      | 9   |

```

또 다른 유용한 패턴은 열 이름을 해당 열에 적용할 작업에 매핑하는 사전을 전달하는 것입니다.

```python 
df.groupby('key').aggregate({'data1': 'min','data2': 'max'})
    |     | data1 | data2 |
    |-----|-------|-------|
    | key |       |       |
    | A   | 0     | 5     |
    | B   | 1     | 7     |
    | C   | 2     | 9     |
```

#### Filtering 
필터링 작업을 사용하면 그룹 속성을 기반으로 데이터를 삭제할 수 있습니다.
예를 들어 표준 편차가 일부 임계값보다 큰 모든 그룹을 유지하려고 할 수 있습니다.

```python 
def filter_func(x):
    return x['data2'].std() > 4

display('df', "df.groupby('key').std()", "df.groupby('key').filter(filter_func)")


| df |     |       |       || df.groupby('key').std() |         |          || df.groupby('key').filter(filter_func) |     |       |       |
|----|-----|-------|-------||-------------------------|---------|----------||---------------------------------------|-----|-------|-------|
|    | key | data1 | data2 ||                         | data1   | data2    ||                                       | key | data1 | data2 |
| 0  | A   | 0     | 5     || key                     |         |          || 1                                     | B   | 1     | 0     |
| 1  | B   | 1     | 0     || A                       | 2.12132 | 1.414214 || 2                                     | C   | 2     | 3     |
| 2  | C   | 2     | 3     || B                       | 2.12132 | 4.949747 || 4                                     | B   | 4     | 7     |
| 3  | A   | 3     | 3     || C                       | 2.12132 | 4.242641 || 5                                     | C   | 5     | 9     |
| 4  | B   | 4     | 7     |
| 5  | C   | 5     | 9     |
```

필터 함수는 그룹이 필터링을 통과하는지 여부를 지정하는 부울 값을 반환해야 합니다. 여기서 그룹 A는 4보다 큰 표준 편차를 가지지 않으므로 결과에서 제외됩니다.

#### Transformation 
집계는 축소된 버전의 데이터를 반환해야 하지만 변환은 전체 데이터의 일부 변환된 버전을 반환하여 재결합할 수 있습니다.
이러한 변환의 경우 출력은 입력과 동일한 모양입니다.
일반적인 예는 그룹별 평균을 빼서 데이터를 중앙에 배치하는 것입니다. 

df.groupby('key').transform(lambda x: x - x.mean())
|   | data1 | data2 |
|---|-------|-------|
| 0 | -1.5  | 1     |
| 1 | -1.5  | -3.5  |
| 2 | -1.5  | -3    |
| 3 | 1.5   | -1    |
| 4 | 1.5   | 3.5   |
| 5 | 1.5   | 3     |

#### The apply() method 
``apply()`` 메소드를 사용하면 그룹 결과에 임의의 함수를 적용할 수 있습니다.
함수는 ``DataFrame``을 취해야 하며 Pandas 객체(예: ``DataFrame``, ``Series``) 또는 스칼라를 반환해야 합니다. 결합 작업은 반환된 출력 유형에 맞게 조정됩니다.

예를 들어, 다음은 첫 번째 열을 두 번째 열의 합으로 정규화하는 ``apply()``입니다.

```python 
def norm_by_data2(x):
    # x는 그룹의 값을 가지는 DataFrame
    x['data1'] /= x['data2'].sum()
    return x

display('df', "df.groupby('key').apply(norm_by_data2)")
| df |     |       |       || df.groupby('key').apply(norm_by_data2) |     |          |       |
|----|-----|-------|-------||----------------------------------------|-----|----------|-------|
|    | key | data1 | data2 ||                                        | key | data1    | data2 |
| 0  | A   | 0     | 5     || 0                                      | A   | 0        | 5     |
| 1  | B   | 1     | 0     || 1                                      | B   | 0.142857 | 0     |
| 2  | C   | 2     | 3     || 2                                      | C   | 0.166667 | 3     |
| 3  | A   | 3     | 3     || 3                                      | A   | 0.375    | 3     |
| 4  | B   | 4     | 7     || 4                                      | B   | 0.571429 | 7     |
| 5  | C   | 5     | 9     || 5                                      | C   | 0.416667 | 9     |

```

GroupBy 내의 apply()는 매우 유연합니다. 유일한 기준은 함수가 DataFrame을 사용하고 Pandas 객체 또는 스칼라를 반환한다는 것입니다. 중간에 무엇을 하느냐는 당신에게 달려 있습니다!

#### Specifying the split key 
이전에 제시된 간단한 예에서는 단일 열 이름에서 ``DataFrame``을 분할했습니다.
이것은 그룹을 정의할 수 있는 많은 옵션 중 하나일 뿐이며 여기에서 그룹 지정에 대한 몇 가지 다른 옵션을 살펴보겠습니다.

#### A list, array, series, or index providing the grouping keys 
키는 ``DataFrame``의 길이와 일치하는 시리즈 또는 목록일 수 있습니다.

```python 
L = [0, 1, 0, 1, 2, 0]
# List 객체가 groupby 로 들어가도 OK!
# 아래서 나오겠지만 Series 객체가 들어가도 OK!
display('df', 'df.groupby(L).sum()')
| df |     |       |       || df.groupby(L).sum() |       |       |
|----|-----|-------|-------||---------------------|-------|-------|
|    | key | data1 | data2 ||                     | data1 | data2 |
| 0  | A   | 0     | 5     || 0                   | 7     | 17    |
| 1  | B   | 1     | 0     || 1                   | 4     | 3     |
| 2  | C   | 2     | 3     || 2                   | 4     | 7     |
| 3  | A   | 3     | 3     |
| 4  | B   | 4     | 7     |
| 5  | C   | 5     | 9     |

```

물론 이것은 이전의 df.groupby('key') 를 수행하는 또 다른 더 장황한 방법이 있음을 의미합니다.

```python 
display('df', "df.groupby(df['key']).sum()")

| df |     |       |       || df.groupby(df['key']).sum() |       |       |
|----|-----|-------|-------||-----------------------------|-------|-------|
|    | key | data1 | data2 ||                             | data1 | data2 |
| 0  | A   | 0     | 5     || key                         |       |       |
| 1  | B   | 1     | 0     || A                           | 3     | 8     |
| 2  | C   | 2     | 3     || B                           | 5     | 7     |
| 3  | A   | 3     | 3     || C                           | 7     | 12    |
| 4  | B   | 4     | 7     |
| 5  | C   | 5     | 9     |

```

#### A dictionary or series mapping index to group 
또 다른 방법은 인덱스 값을 그룹 키에 매핑하는 사전을 제공하는 것입니다.

```python 
# key 값을 Index로 하여 Group by를 진행한다. 
df2 = df.set_index('key')
df2 
    |     | data1 | data2 |
    |-----|-------|-------|
    | key |       |       |
    | A   | 0     | 5     |
    | B   | 1     | 0     |
    | C   | 2     | 3     |
    | A   | 3     | 3     |
    | B   | 4     | 7     |
    | C   | 5     | 9     |

mapping = {'A': 'vowel', 'B': 'consonant', 'C': 'consonant'}
display('df2', 'df2.groupby(mapping).sum()')

| df2 |       |       || df2.groupby(mapping).sum() |       |       |
|-----|-------|-------||----------------------------|-------|-------|
|     | data1 | data2 ||                            | data1 | data2 |
| key |       |       || consonant                  | 12    | 19    |
| A   | 0     | 5     || vowel                      | 3     | 8     |
| B   | 1     | 0     |
| C   | 2     | 3     |
| A   | 3     | 3     |
| B   | 4     | 7     |
| C   | 5     | 9     |


```

#### Any Python function 
매핑과 유사하게 인덱스 값을 입력하고 그룹을 출력하는 모든 Python 함수를 전달할 수 있습니다.

```python 
display('df2', 'df2.groupby(str.lower).mean()')

df2 
    |     | data1 | data2 || df2.groupby(str.lower).mean() |       |       |
    |-----|-------|-------||-------------------------------|-------|-------|
    | key |       |       ||                               | data1 | data2 |
    | A   | 0     | 5     || a                             | 1.5   | 4     |
    | B   | 1     | 0     || b                             | 2.5   | 3.5   |
    | C   | 2     | 3     || c                             | 3.5   | 6     |
    | A   | 3     | 3     |
    | B   | 4     | 7     |
    | C   | 5     | 9     |
```

#### A list of valid keys 
또한 앞의 키 선택 사항 중 하나를 결합하여 다중 인덱스에서 그룹화할 수 있습니다.
```python 
df2.groupby([str.lower, mapping]).mean()
    |   |           | data1 | data2 |
    |---|-----------|-------|-------|
    | a | vowel     | 1.5   | 4     |
    | b | consonant | 2.5   | 3.5   |
    | c | consonant | 3.5   | 6     |

```

### Grouping example 
이에 대한 예로서, 몇 줄의 Python 코드에서 이 모든 것을 통합하고 방법 및 10년 단위로 발견된 행성을 계산할 수 있습니다.

```python 
decade = 10 * (planets['year'] // 10)
decade = decade.astype(str) + 's'
decade.name = 'decade'
planets.groupby(['method', decade])['number'].sum().unstack().fillna(0)

    | decade                        | 1980s | 1990s | 2000s | 2010s |
    |-------------------------------|-------|-------|-------|-------|
    | method                        |       |       |       |       |
    | Astrometry                    | 0     | 0     | 0     | 2     |
    | Eclipse Timing Variations     | 0     | 0     | 5     | 10    |
    | Imaging                       | 0     | 0     | 29    | 21    |
    | Microlensing                  | 0     | 0     | 12    | 15    |
    | Orbital Brightness Modulation | 0     | 0     | 0     | 5     |
    | Pulsar Timing                 | 0     | 9     | 1     | 1     |
    | Pulsation Timing Variations   | 0     | 0     | 1     | 0     |
    | Radial Velocity               | 1     | 52    | 475   | 424   |
    | Transit                       | 0     | 0     | 64    | 712   |
    | Transit Timing Variations     | 0     | 0     | 0     | 9     |
```
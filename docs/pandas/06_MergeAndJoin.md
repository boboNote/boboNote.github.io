---
layout: default
title: Merge and Join
parent: Pandas
nav_order: 60
has_children: false
permalink: /docs/pandas/MergeAndJoin
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Merge and Join
Pandas가 제공하는 필수 기능 중 하나는 고성능 메모리 내 결합 및 병합 작업입니다. 데이터베이스로 작업한 적이 있다면 이러한 유형의 데이터 상호 작용에 익숙해야 합니다. 이에 대한 주요 인터페이스는 pd.merge 함수이며 이것이 실제로 어떻게 작동하는지에 대한 몇 가지 예를 볼 것입니다.


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

## Relational Algebra 
``pd.merge()``에 구현된 동작은 관계형 데이터를 조작하기 위한 공식 규칙 세트인 *관계형 대수학*으로 알려진 것의 하위 집합이며 대부분의 데이터베이스에서 사용할 수 있는 작업의 개념적 기초를 형성합니다.
관계형 대수학 접근 방식의 강점은 몇 가지 기본 연산을 제안한다는 점이며, 이는 모든 데이터 세트에서 더 복잡한 연산의 빌딩 블록이 됩니다.
데이터베이스나 다른 프로그램에서 효율적으로 구현된 기본 연산의 이 사전을 사용하여 상당히 복잡한 복합 연산을 광범위하게 수행할 수 있습니다.

Pandas는 ``pd.merge()`` 함수와 ``Series`` 및 ``Dataframe``의 관련 ``join()`` 메서드에서 이러한 몇 가지 기본 빌딩 블록을 구현합니다.
앞으로 살펴보겠지만, 이를 통해 다양한 소스의 데이터를 효율적으로 연결할 수 있습니다.

## Categories of Joins 
``pd.merge()`` 함수는 *일대일*, *다대일* 및 *다대다* 조인과 같은 여러 유형의 조인을 구현합니다.
세 가지 유형의 조인 모두 ``pd.merge()`` 인터페이스에 대한 동일한 호출을 통해 액세스됩니다. 수행되는 조인 유형은 입력 데이터의 형식에 따라 다릅니다.
여기에서는 세 가지 유형의 병합에 대한 간단한 예를 보여주고 아래에서 자세한 옵션에 대해 논의합니다.

### One-to-one joins 
아마도 가장 간단한 유형의 병합 표현은 일대일 조인일 것입니다. 이는 데이터 집합 결합: 연결 및 추가에서 볼 수 있는 열 단위 연결과 여러 면에서 매우 유사합니다. 구체적인 예로, 회사의 여러 직원에 대한 정보를 포함하는 다음 두 DataFrames를 고려하십시오.


pd.merge() 함수는 각 DataFrame에 "employee" 열이 있음을 인식하고 이 열을 키로 사용하여 자동으로 조인합니다. 병합의 결과는 두 입력의 정보를 결합하는 새로운 DataFrame입니다. 각 열의 항목 순서가 반드시 유지되는 것은 아닙니다. 이 경우 "employee" 열의 순서는 df1과 df2 간에 다르며 pd.merge() 함수가 이를 올바르게 설명합니다. 또한 인덱스에 의한 병합의 특별한 경우를 제외하고 일반적으로 병합은 인덱스를 버립니다(잠시 논의되는 left_index 및 right_index 키워드 참조).

```python
df1 = pd.DataFrame({'employee': ['Bob', 'Jake', 'Lisa', 'Sue'],
                    'group': ['Accounting', 'Engineering', 'Engineering', 'HR']})
df2 = pd.DataFrame({'employee': ['Lisa', 'Bob', 'Jake', 'Sue'],
                    'hire_date': [2004, 2008, 2012, 2014]})
display('df1', 'df2')
    | df1 |          |             || df2 |          |             |
    |-----|----------|-------------||-----|----------|-------------|
    |     | employee | group       ||     | employee | hire_date   |
    | 0   | Bob      | Accounting  || 0   | Lisa     | 2004        |
    | 1   | Jake     | Engineering || 1   | Bob      | 2008        |
    | 2   | Lisa     | Engineering || 2   | Jake     | 2012        |
    | 3   | Sue      | HR          || 3   | Sue      | 2014        |

df3 = pd.merge(df1, df2) 
# or pd.merge(df1, df2 , on="employee") 
df3
    |   | employee | group       | hire_date |
    |---|----------|-------------|-----------|
    | 0 | Bob      | Accounting  | 2008      |
    | 1 | Jake     | Engineering | 2012      |
    | 2 | Lisa     | Engineering | 2004      |
    | 3 | Sue      | HR          | 2014      |

```

### Many-to-many joins 
다대다 조인은 개념적으로 약간 혼동되지만 그럼에도 불구하고 잘 정의되어 있습니다. 왼쪽 및 오른쪽 배열의 키 열에 중복 항목이 포함된 경우 결과는 다대다 병합입니다. 이것은 아마도 구체적인 예를 통해 가장 명확할 것입니다. 특정 그룹과 관련된 하나 이상의 기술을 보여주는 DataFrame이 있는 다음을 고려하십시오. 다대다 조인을 수행하여 개인과 관련된 기술을 복구할 수 있습니다.

```python 
df5 = pd.DataFrame({'group': ['Accounting', 'Accounting',
                              'Engineering', 'Engineering', 'HR', 'HR'],
                    'skills': ['math', 'spreadsheets', 'coding', 'linux',
                               'spreadsheets', 'organization']})
display('df1', 'df5', "pd.merge(df1, df5)")


| df1 |          |             || df5 |             |              || pd.merge(df1, df5) |          |             |              |
|-----|----------|-------------||-----|-------------|--------------||--------------------|----------|-------------|--------------|
|     | employee | group       ||     | group       | skills       ||                    | employee | group       | skills       |
| 0   | Bob      | Accounting  || 0   | Accounting  | math         || 0                  | Bob      | Accounting  | math         |
| 1   | Jake     | Engineering || 1   | Accounting  | spreadsheets || 1                  | Bob      | Accounting  | spreadsheets |
| 2   | Lisa     | Engineering || 2   | Engineering | coding       || 2                  | Jake     | Engineering | coding       |
| 3   | Sue      | HR          || 3   | Engineering | linux        || 3                  | Jake     | Engineering | linux        |
                                | 4   | HR          | spreadsheets || 4                  | Lisa     | Engineering | coding       |                                
                                | 5   | HR          | organization || 5                  | Lisa     | Engineering | linux        |                                
                                                                    | 6                  | Sue      | HR          | spreadsheets |                 
                                                                    | 7                  | Sue      | HR          | organization |                 
                                                                                     

```

이 세 가지 유형의 조인을 다른 Pandas 도구와 함께 사용하여 다양한 기능을 구현할 수 있습니다. 그러나 실제로 데이터 세트는 여기에서 작업하는 데이터 세트만큼 깨끗한 경우는 거의 없습니다. 다음 섹션에서는 조인 작업이 작동하는 방식을 조정할 수 있도록 하는 pd.merge()에서 제공하는 몇 가지 옵션을 고려할 것입니다.

## Specification of the Merge Key 
우리는 이미 pd.merge()의 기본 동작을 보았습니다. 두 입력 간에 일치하는 하나 이상의 열 이름을 찾고 이를 키로 사용합니다. 그러나 종종 열 이름이 잘 일치하지 않으며 pd.merge()는 이를 처리하기 위한 다양한 옵션을 제공합니다.

### The ``on`` keyword 
예제로 확인 
```python 
display('df1', 'df2', "pd.merge(df1, df2, on='employee')")

```

###  The ``left_on`` and ``right_on`` keywords 
열 이름이 다른 두 데이터세트를 병합하려는 경우가 있습니다. 예를 들어 직원 이름이 "직원"이 아닌 "이름"으로 레이블이 지정된 데이터 세트가 있을 수 있습니다.
이 경우 ``left_on`` 및 ``right_on`` 키워드를 사용하여 두 개의 열 이름을 지정할 수 있습니다.

```python 
df3 = pd.DataFrame({'name': ['Bob', 'Jake', 'Lisa', 'Sue'],
                    'salary': [70000, 80000, 120000, 90000]})
display('df1', 'df3', 'pd.merge(df1, df3, left_on="employee", right_on="name")')

| df1 |          |             || df3 |      |        |
|-----|----------|-------------||-----|------|--------|
|     | employee | group       ||     | name | salary |
| 0   | Bob      | Accounting  || 0   | Bob  | 70000  |
| 1   | Jake     | Engineering || 1   | Jake | 80000  |
| 2   | Lisa     | Engineering || 2   | Lisa | 120000 |
| 3   | Sue      | HR          || 3   | Sue  | 90000  |
                                
| pd.merge(df1, df3, left_on="employee", right_on="name") |          |             |      |        |
|---------------------------------------------------------|----------|-------------|------|--------|
|                                                         | employee | group       | name | salary |
| 0                                                       | Bob      | Accounting  | Bob  | 70000  |
| 1                                                       | Jake     | Engineering | Jake | 80000  |
| 2                                                       | Lisa     | Engineering | Lisa | 120000 |
| 3                                                       | Sue      | HR          | Sue  | 90000  |                                
pd.merge(df1, df3, left_on="employee", right_on="name").drop('name', axis=1)
|   | employee | group       | salary |
|---|----------|-------------|--------|
| 0 | Bob      | Accounting  | 70000  |
| 1 | Jake     | Engineering | 80000  |
| 2 | Lisa     | Engineering | 120000 |
| 3 | Sue      | HR          | 90000  |

```

### The ``left_index`` and ``right_index`` keywords 
때로는 열에서 병합하는 대신 인덱스에서 병합하고 싶을 때가 있습니다.
예를 들어 데이터는 다음과 같을 수 있습니다. 

```python 
df1a = df1.set_index('employee')
df1a 
    |          | group       |
    |----------|-------------|
    | employee |             |
    | Bob      | Accounting  |
    | Jake     | Engineering |
    | Lisa     | Engineering |
    | Sue      | HR          |

df2a = df2.set_index('employee')
df2a 
    |          | hire_date |
    |----------|-----------|
    | employee |           |
    | Lisa     | 2004      |
    | Bob      | 2008      |
    | Jake     | 2012      |
    | Sue      | 2014      |

display('df1a', 'df2a')

```
pd.merge()에서 left_index 및/또는 right_index 플래그를 지정하여 인덱스를 병합용 키로 사용할 수 있습니다.
```python 
pd.merge(df1a, df2a, left_index=True, right_index=True)
    |          | group       |
    |----------|-------------|
    | employee |             |
    | Bob      | Accounting  |
    | Jake     | Engineering |
    | Lisa     | Engineering |
    | Sue      | HR          |

```

편의를 위해 DataFrames는 기본적으로 인덱스에 결합하는 병합을 수행하는 join() 메서드를 구현합니다.

```python 
df1a.join(df2a)
    | df1a.join(df2a) |             |           |
    |-----------------|-------------|-----------|
    |                 | group       | hire_date |
    | employee        |             |           |
    | Bob             | Accounting  | 2008      |
    | Jake            | Engineering | 2012      |
    | Lisa            | Engineering | 2004      |
    | Sue             | HR          | 2014      |

pd.merge(df1a, df3, left_index=True, right_on='name')
    | pd.merge(df1a, df3, left_index=True, right_on='name') |             |      |        |
    |-------------------------------------------------------|-------------|------|--------|
    |                                                       | group       | name | salary |
    | 0                                                     | Accounting  | Bob  | 70000  |
    | 1                                                     | Engineering | Jake | 80000  |
    | 2                                                     | Engineering | Lisa | 120000 |
    | 3                                                     | HR          | Sue  | 90000  |
```

## Specifying Set Arithmetic for Joins 
앞의 모든 예에서 우리는 조인을 수행할 때 한 가지 중요한 고려 사항인 조인에 사용되는 집합 산술 유형에 대해 설명했습니다. 이것은 값이 한 키 열에 나타나지만 다른 열에는 나타나지 않을 때 나타납니다. 다음 예를 고려하십시오.

```python 
df6 = pd.DataFrame({'name': ['Peter', 'Paul', 'Mary'],
                    'food': ['fish', 'beans', 'bread']},
                   columns=['name', 'food'])
df7 = pd.DataFrame({'name': ['Mary', 'Joseph'],
                    'drink': ['wine', 'beer']},
                   columns=['name', 'drink'])

display('df6', 'df7', 'pd.merge(df6, df7)')

| df6 |       |       || df7 |        |       || pd.merge(df6, df7) |      |       |       |
|-----|-------|-------||-----|--------|-------||--------------------|------|-------|-------|
|     | name  | food  ||     | name   | drink ||                    | name | food  | drink |
| 0   | Peter | fish  || 0   | Mary   | wine  || 0                  | Mary | bread | wine  |
| 1   | Paul  | beans || 1   | Joseph | beer  |
| 2   | Mary  | bread |

```

기본적으로 결과에는 두 입력 집합의 교집합이 포함됩니다. 이것은 내부 조인으로 알려진 것입니다. 기본값은 ``inner`` 인 how 키워드를 사용하여 명시적으로 지정할 수 있습니다.

```python 
pd.merge(df6, df7, how='inner')
    | name | food  | drink |
    |------|-------|-------|
    | Mary | bread | wine  |
```

how 키워드에 대한 다른 옵션은 'outer', 'left' 및 'right'입니다. 외부 조인은 입력 열의 합집합에 대한 조인을 반환하고 모든 누락된 값을 NA로 채웁니다.

```python 
display('df6', 'df7', "pd.merge(df6, df7, how='outer')")
    | df6 |       |       || df7 |        |       || pd.merge(df6, df7, how='outer') |        |       |       |
    |-----|-------|-------||-----|--------|-------||---------------------------------|--------|-------|-------|
    |     | name  | food  ||     | name   | drink ||                                 | name   | food  | drink |
    | 0   | Peter | fish  || 0   | Mary   | wine  || 0                               | Peter  | fish  | NaN   |
    | 1   | Paul  | beans || 1   | Joseph | beer  || 1                               | Paul   | beans | NaN   |
    | 2   | Mary  | bread |                        | 2                               | Mary   | bread | wine  |  
                                                   | 3                               | Joseph | NaN   | beer  |  

display('df6', 'df7', "pd.merge(df6, df7, how='left')")
    | df6 |       |       || df7 |        |       || pd.merge(df6, df7, how='left') |       |       |       |
    |-----|-------|-------||-----|--------|-------||--------------------------------|-------|-------|-------|
    |     | name  | food  ||     | name   | drink ||                                | name  | food  | drink |
    | 0   | Peter | fish  || 0   | Mary   | wine  || 0                              | Peter | fish  | NaN   |
    | 1   | Paul  | beans || 1   | Joseph | beer  || 1                              | Paul  | beans | NaN   |
    | 2   | Mary  | bread |                        | 2                              | Mary  | bread | wine  |
                                                   
```

## Overlapping Column Names: The ``suffixes`` Keyword 
마지막으로 두 입력 DataFrame에 충돌하는 열 이름이 있는 경우가 발생할 수 있습니다. 다음 예를 고려하십시오. 

```python 
df8 = pd.DataFrame({'name': ['Bob', 'Jake', 'Lisa', 'Sue'],
                    'rank': [1, 2, 3, 4]})
df9 = pd.DataFrame({'name': ['Bob', 'Jake', 'Lisa', 'Sue'],
                    'rank': [3, 1, 4, 2]})
display('df8', 'df9', 'pd.merge(df8, df9, on="name")')

| df8 |      |      || df9 |      |      || pd.merge(df8, df9, on="name") |      |        |        |
|-----|------|------||-----|------|------||-------------------------------|------|--------|--------|
|     | name | rank ||     | name | rank ||                               | name | rank_x | rank_y |
| 0   | Bob  | 1    || 0   | Bob  | 3    || 0                             | Bob  | 1      | 3      |
| 1   | Jake | 2    || 1   | Jake | 1    || 1                             | Jake | 2      | 1      |
| 2   | Lisa | 3    || 2   | Lisa | 4    || 2                             | Lisa | 3      | 4      |
| 3   | Sue  | 4    || 3   | Sue  | 2    || 3                             | Sue  | 4      | 2      |

```

출력에는 두 개의 충돌하는 열 이름이 있으므로 병합 기능은 자동으로 접미사 _x 또는 _y를 추가하여 출력 열을 고유하게 만듭니다. 이러한 기본값이 부적절하면 suffixes 키워드를 사용하여 사용자 정의 접미사를 지정할 수 있습니다.
```python 
display('df8', 'df9', 'pd.merge(df8, df9, on="name", suffixes=["_L", "_R"])')
| pd.merge(df8, df9, on="name", suffixes=["_L", "_R"]) |      |        |        |
|------------------------------------------------------|------|--------|--------|
|                                                      | name | rank_L | rank_R |
| 0                                                    | Bob  | 1      | 3      |
| 1                                                    | Jake | 2      | 1      |
| 2                                                    | Lisa | 3      | 4      |
| 3                                                    | Sue  | 4      | 2      |

```

---
layout: default
title: Vectorized String Operations
parent: Pandas
nav_order: 90
has_children: false
permalink: /docs/pandas/VectorizedStringOperations
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# VectorizedStringOperations
Python의 한 가지 강점은 문자열 데이터를 처리하고 조작하는 것이 상대적으로 쉽다는 것입니다. Pandas는 이를 기반으로 하며 실제 데이터로 작업(읽기: 정리)할 때 필요한 정리 유형의 필수 부분이 되는 포괄적인 벡터화된 문자열 연산 세트를 제공합니다. 이 섹션에서는 Pandas 문자열 작업 중 일부를 살펴보고 이를 사용하여 인터넷에서 수집한 매우 지저분한 레시피 데이터 세트를 부분적으로 정리하는 방법을 살펴보겠습니다.

## Introducing Pandas String Operations
이전 섹션에서 NumPy 및 Pandas와 같은 도구가 산술 연산을 일반화하여 많은 배열 요소에서 동일한 연산을 쉽고 빠르게 수행할 수 있는 방법을 보았습니다

이 연산의 벡터화는 데이터 배열에 대한 연산 구문을 단순화합니다. 더 이상 배열의 크기나 모양에 대해 걱정할 필요가 없지만 수행하려는 연산에 대해 걱정할 필요가 없습니다. 문자열 배열의 경우 NumPy는 이러한 간단한 액세스를 제공하지 않으므로 더 자세한 루프 구문을 사용하는 데 갇히게 됩니다.

``None`` 누락된 값이 있으면 중단됩니다. 
```python 
import numpy as np
x = np.array([2, 3, 5, 7, 11, 13])
x * 2
    array([ 4,  6, 10, 14, 22, 26])

data = ['peter', 'Paul', 'MARY', 'gUIDO']
[s.capitalize() for s in data]
    ['Peter', 'Paul', 'Mary', 'Guido']

data = ['peter', None, 'MARY', 'gUIDO']
[s.capitalize() for s in data]
    ---------------------------------------------------------------------------
    AttributeError                            Traceback (most recent call last)
    <ipython-input-1-3b0264c38d59> in <module>
        1 data = ['peter', 'Paul', None, 'MARY', 'gUIDO']
    ----> 2 [s.capitalize() for s in data]

    <ipython-input-1-3b0264c38d59> in <listcomp>(.0)
        1 data = ['peter', 'Paul', None, 'MARY', 'gUIDO']
    ----> 2 [s.capitalize() for s in data]

    AttributeError: 'NoneType' object has no attribute 'capitalize'

```

str 속성 정보를 이용하여 문자열 처리가 가능합니다. 
이 ``str`` 속성에 탭 완성을 사용하면 Pandas에서 사용할 수 있는 모든 벡터화된 문자열 메서드가 나열됩니다.

```python 
import pandas as pd
names = pd.Series(data)
names.str.capitalize()
    0    Peter
    1     Paul
    2     None
    3     Mary
    4    Guido
    dtype: object
```

## Tables of Pandas String Methods 
Python의 문자열 조작에 대해 잘 이해하고 있다면 대부분의 Pandas 문자열 구문은 사용 가능한 메서드 테이블을 나열하는 것으로 충분할 정도로 직관적입니다. 몇 가지 미묘함에 더 깊이 들어가기 전에 여기에서 시작하겠습니다.
이 섹션의 예에서는 다음과 같은 일련의 이름을 사용합니다.

```python 
monte = pd.Series(['Graham Chapman', 'John Cleese', 'Terry Gilliam',
                   'Eric Idle', 'Terry Jones', 'Michael Palin'])

```

### Methods similar to Python string methods 
거의 모든 Python의 내장 문자열 메서드는 Pandas 벡터화된 문자열 메서드에 의해 미러링됩니다. 다음은 Python 문자열 메서드를 미러링하는 Pandas ``str`` 메서드 목록입니다.

|             |                  |                  |                  |
|-------------|------------------|------------------|------------------|
|``len()``    | ``lower()``      | ``translate()``  | ``islower()``    | 
|``ljust()``  | ``upper()``      | ``startswith()`` | ``isupper()``    | 
|``rjust()``  | ``find()``       | ``endswith()``   | ``isnumeric()``  | 
|``center()`` | ``rfind()``      | ``isalnum()``    | ``isdecimal()``  | 
|``zfill()``  | ``index()``      | ``isalpha()``    | ``split()``      | 
|``strip()``  | ``rindex()``     | ``isdigit()``    | ``rsplit()``     | 
|``rstrip()`` | ``capitalize()`` | ``isspace()``    | ``partition()``  | 
|``lstrip()`` |  ``swapcase()``  |  ``istitle()``   | ``rpartition()`` |

```python 
monte.str.lower()
    0    graham chapman
    1       john cleese
    2     terry gilliam
    3         eric idle
    4       terry jones
    5     michael palin
    dtype: object
```

### Methods using regular expressions 
또한 각 문자열 요소의 내용을 검사하기 위해 정규식을 받아들이고 Python의 내장 ``re`` 모듈의 일부 API 규칙을 따르는 여러 메서드가 있습니다. 

| Method | Description |
|--------|-------------|
| ``match()`` | Call ``re.match()`` on each element, returning a boolean. |
| ``extract()`` | Call ``re.match()`` on each element, returning matched groups as strings.|
| ``findall()`` | Call ``re.findall()`` on each element |
| ``replace()`` | Replace occurrences of pattern with some other string|
| ``contains()`` | Call ``re.search()`` on each element, returning a boolean |
| ``count()`` | Count occurrences of pattern|
| ``split()``   | Equivalent to ``str.split()``, but accepts regexps |
| ``rsplit()`` | Equivalent to ``str.rsplit()``, but accepts regexps |


```python 
monte.str.extract('([A-Za-z]+)', expand=False)

```

### Miscellaneous methods 
마지막으로 다른 편리한 작업을 가능하게 하는 몇 가지 기타 방법이 있습니다.

| Method | Description |
|--------|-------------|
| ``get()`` | Index each element |
| ``slice()`` | Slice each element|
| ``slice_replace()`` | Replace slice in each element with passed value|
| ``cat()``      | Concatenate strings|
| ``repeat()`` | Repeat values |
| ``normalize()`` | Return Unicode form of string |
| ``pad()`` | Add whitespace to left, right, or both sides of strings|
| ``wrap()`` | Split long strings into lines with length less than a given width|
| ``join()`` | Join strings in each element of the Series with passed separator|
| ``get_dummies()`` | extract dummy variables as a dataframe |


#### Vectorized item access and slicing 
특히 ``get()`` 및 ``slice()`` 작업은 각 배열에서 벡터화된 요소 액세스를 가능하게 합니다.
예를 들어, ``str.slice(0, 3)``를 사용하여 각 배열의 처음 세 문자의 조각을 얻을 수 있습니다.
이 동작은 Python의 일반 인덱싱 구문을 통해서도 사용할 수 있습니다. 예를 들어 ``df.str.slice(0, 3)``는 ``df.str[0:3]``과 동일합니다.

```python 
monte.str[0:3]
    0    Gra
    1    Joh
    2    Ter
    3    Eri
    4    Ter
    5    Mic
    dtype: object
```

df.str.get(i) 및 df.str[i]를 통한 인덱싱도 유사합니다.

이러한 get() 및 slice() 메서드를 사용하면 split()에서 반환된 배열 요소에 액세스할 수도 있습니다. 예를 들어, 각 항목의 성을 추출하기 위해 split()과 get()을 결합할 수 있습니다.

```python 
monte.str.split()
    0    [Graham, Chapman]
    1       [John, Cleese]
    2     [Terry, Gilliam]
    3         [Eric, Idle]
    4       [Terry, Jones]
    5     [Michael, Palin]
    dtype: object
monte.str.split().str.get(-1)
    0    Chapman
    1     Cleese
    2    Gilliam
    3       Idle
    4      Jones
    5      Palin
    dtype: object
```

#### Indicator variables  (onehot encoder)
약간의 추가 설명이 필요한 또 다른 메소드는 ``get_dummies()`` 메소드입니다.
이는 데이터에 일종의 코딩된 지표가 포함된 열이 있는 경우에 유용합니다.
예를 들어 A="born in America", B="born in the United Kingdom", C="like cheese" D="like spam"과 같은 코드 형식의 정보가 포함된 데이터세트가 있을 수 있습니다. :

```python 
full_monte = pd.DataFrame({'name': monte,
                           'info': ['B|C|D', 'B|D', 'A|C',
                                    'B|D', 'B|C', 'B|C|D']})
full_monte

    |   | info  | name           |
    |---|-------|----------------|
    | 0 | B|C|D | Graham Chapman |
    | 1 | B|D   | John Cleese    |
    | 2 | A|C   | Terry Gilliam  |
    | 3 | B|D   | Eric Idle      |
    | 4 | B|C   | Terry Jones    |
    | 5 | B|C|D | Michael Palin  |
```

get_dummies() 루틴을 사용하면 이러한 표시기 변수를 DataFrame으로 빠르게 분할할 수 있습니다.

```python 

full_monte['info'].str.get_dummies(['info']) 
    |   | A|C | B|C | B|C|D | B|D |
    |---|-----|-----|-------|-----|
    | 0 | 0   | 0   | 1     | 0   |
    | 1 | 0   | 0   | 0     | 1   |
    | 2 | 1   | 0   | 0     | 0   |
    | 3 | 0   | 0   | 0     | 1   |
    | 4 | 0   | 1   | 0     | 0   |
    | 5 | 0   | 0   | 1     | 0   |
full_monte['info'].str.get_dummies('|')  # or 
full_monte['info'].str.get_dummies('').drop(['|'], axis=1)
    |   | A | B | C | D |
    |---|---|---|---|---|
    | 0 | 0 | 1 | 1 | 1 |
    | 1 | 0 | 1 | 0 | 1 |
    | 2 | 1 | 0 | 1 | 0 |
    | 3 | 0 | 1 | 0 | 1 |
    | 4 | 0 | 1 | 1 | 0 |
    | 5 | 0 | 1 | 1 | 1 |

```





---
layout: default
title: Time And Date
parent: Pandas
nav_order: 100
has_children: false
permalink: /docs/pandas/TimeAndDate
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Time with Time Series 
Pandas는 재무 모델링의 맥락에서 개발되었으므로 예상할 수 있듯이 날짜, 시간 및 시간 인덱싱된 데이터 작업을 위한 상당히 광범위한 도구 세트가 포함되어 있습니다. 날짜 및 시간 데이터는 몇 가지 유형으로 제공되며 여기에서 논의할 것입니다.

* 타임스탬프는 특정 순간을 참조합니다(예: 2015년 7월 4일 오전 7시).
* 시간 간격 및 기간은 특정 시작 지점과 끝 지점 사이의 시간 길이를 나타냅니다. 예를 들어, 2015년입니다. 기간은 일반적으로 각 간격의 길이가 균일하고 겹치지 않는 특별한 경우의 시간 간격을 나타냅니다(예: 24시간 동안 여러 날로 구성된 기간).
* 시간 델타 또는 지속 시간은 정확한 시간 길이를 참조합니다(예: 22.56초의 지속 시간).

이 섹션에서는 Pandas에서 이러한 각 유형의 날짜/시간 데이터를 사용하는 방법을 소개합니다. 이 짧은 섹션은 Python 또는 Pandas에서 사용할 수 있는 시계열 도구에 대한 완전한 가이드가 아니라 사용자가 시계열 작업에 접근하는 방법에 대한 광범위한 개요를 제공하기 위한 것입니다. Pandas에서 제공하는 도구에 대해 더 구체적으로 설명하기 전에 Python에서 날짜와 시간을 처리하는 도구에 대한 간략한 설명으로 시작하겠습니다. 더 깊이 있는 몇 가지 리소스를 나열한 후 Pandas에서 시계열 데이터로 작업하는 몇 가지 짧은 예를 검토합니다.


## Dates and Times in Python
Python 세계에는 날짜, 시간, 델타 및 시간 범위에 대한 사용 가능한 표현이 많이 있습니다.
Pandas에서 제공하는 시계열 도구는 데이터 과학 응용 프로그램에 가장 유용한 경향이 있지만 Python에서 사용되는 다른 패키지와의 관계를 보는 것이 도움이 됩니다.

### Native Python dates and times: ``datetime`` and ``dateutil`` 
날짜 및 시간 작업을 위한 Python의 기본 객체는 내장된 ``datetime`` 모듈에 있습니다.
타사 ``dateutil`` 모듈과 함께 이 모듈을 사용하여 날짜 및 시간에 대한 유용한 기능을 빠르게 수행할 수 있습니다.
예를 들어 ``datetime`` 유형을 사용하여 수동으로 날짜를 작성할 수 있습니다.

또는 dateutil 모듈을 사용하여 다양한 문자열 형식에서 날짜를 구문 분석할 수 있습니다.

datetime 객체가 있으면 요일 출력과 같은 작업을 수행할 수 있습니다. 

```python 
from datetime import datetime
datetime(year=2015, month=7, day=4)
    datetime.datetime(2015, 7, 4, 0, 0)

from dateutil import parser
date = parser.parse("4th of July, 2015")
date
    datetime.datetime(2015, 7, 4, 0, 0)

date.strftime('%A')
    'Saturday'
```

마지막 줄에서 날짜 인쇄를 위한 표준 문자열 형식 코드 중 하나(``"%A"``)를 사용했습니다. [strftime 섹션](https://docs.python.conf)에서 읽을 수 있습니다. [날짜/시간 문서](https://docs.python.org/3/library/datetime.html).
다른 유용한 날짜 유틸리티에 대한 문서는 [dateutil의 온라인 문서](http://labix.org/python-dateutil)에서 찾을 수 있습니다.
주의해야 할 관련 패키지는 [``pytz``](http://pytz.sourceforge.net/)로, 이 패키지에는 가장 편두통을 유발하는 시계열 데이터인 시간대 작업을 위한 도구가 포함되어 있습니다.

``datetime``과 ``dateutil``의 장점은 유연성과 쉬운 구문에 있습니다. 이러한 객체와 내장 메서드를 사용하여 관심 있는 거의 모든 작업을 쉽게 수행할 수 있습니다.
그들이 분해되는 곳은 날짜와 시간의 큰 배열로 작업하려는 경우입니다.
Python 숫자 변수 목록이 NumPy 스타일의 유형이 지정된 숫자 배열에 비해 차선책인 것처럼 Python datetime 객체 목록은 인코딩된 날짜의 유형이 지정된 배열에 비해 차선입니다.

### Typed arrays of times: NumPy's ``datetime64`` 
Python의 날짜/시간 형식의 약점은 NumPy 팀이 NumPy에 기본 시계열 데이터 유형 세트를 추가하도록 영감을 주었습니다.
``datetime64`` dtype은 날짜를 64비트 정수로 인코딩하므로 날짜 배열을 매우 간결하게 표현할 수 있습니다.
``datetime64``에는 매우 구체적인 입력 형식이 필요합니다.

```python 
import numpy as np
date = np.array('2015-07-04', dtype=np.datetime64)
date
    array('2015-07-04', dtype='datetime64[D]')

# dtype datetime64 을 이용하명 백터 연산이 다 빨리 가능하다. 
date + np.arange(12)
    array(['2015-07-04', '2015-07-05', '2015-07-06', '2015-07-07',
        '2015-07-08', '2015-07-09', '2015-07-10', '2015-07-11',
        '2015-07-12', '2015-07-13', '2015-07-14', '2015-07-15'],
        dtype='datetime64[D]')
```

NumPy datetime64 배열의 균일한 유형 때문에 이러한 유형의 작업은 특히 배열이 커질수록 Python의 datetime 객체로 직접 작업하는 경우보다 훨씬 더 빠르게 수행할 수 있습니다(이 유형의 벡터화는 NumPy Arrays의 Computation: Universal에서 도입했습니다. 기능).

datetime64 및 timedelta64 개체의 세부 사항 중 하나는 기본 시간 단위를 기반으로 구축된다는 것입니다. datetime64 개체는 64비트 정밀도로 제한되기 때문에 인코딩 가능한 시간의 범위는 이 기본 단위의 264배입니다. 즉, datetime64는 시간 해상도와 최대 시간 범위 사이에 절충안을 부과합니다.

예를 들어, 1나노초의 시간 분해능을 원하는 경우 264나노초 또는 600년 미만의 범위를 인코딩하기에 충분한 정보만 있으면 됩니다. NumPy는 입력에서 원하는 단위를 추론합니다. 예를 들어 다음은 날짜 기반 날짜/시간입니다.

```python 
np.datetime64('2015-07-04')
    numpy.datetime64('2015-07-04')
np.datetime64('2015-07-04 12:00')
    numpy.datetime64('2015-07-04T12:00')
```

시간대는 코드를 실행하는 컴퓨터의 현지 시간으로 자동 설정됩니다. 많은 형식 코드 중 하나를 사용하여 원하는 기본 단위를 강제 실행할 수 있습니다. 예를 들어 여기에서 나노초 기반 시간을 강제 적용합니다.

```python 
np.datetime64('2015-07-04 12:59:59.50', 'ns')
    numpy.datetime64('2015-07-04T12:59:59.500000000')
```

NumPy datetime64 문서에서 가져온 다음 표에는 인코딩할 수 있는 상대 및 절대 시간 범위와 함께 사용 가능한 형식 코드가 나열되어 있습니다.
|Code    | Meaning     | Time span (relative) | Time span (absolute)   |
|--------|-------------|----------------------|------------------------|
| ``Y``  | Year	       | ± 9.2e18 years       | [9.2e18 BC, 9.2e18 AD] |
| ``M``  | Month       | ± 7.6e17 years       | [7.6e17 BC, 7.6e17 AD] |
| ``W``  | Week	       | ± 1.7e17 years       | [1.7e17 BC, 1.7e17 AD] |
| ``D``  | Day         | ± 2.5e16 years       | [2.5e16 BC, 2.5e16 AD] |
| ``h``  | Hour        | ± 1.0e15 years       | [1.0e15 BC, 1.0e15 AD] |
| ``m``  | Minute      | ± 1.7e13 years       | [1.7e13 BC, 1.7e13 AD] |
| ``s``  | Second      | ± 2.9e12 years       | [ 2.9e9 BC, 2.9e9 AD]  |
| ``ms`` | Millisecond | ± 2.9e9 years        | [ 2.9e6 BC, 2.9e6 AD]  |
| ``us`` | Microsecond | ± 2.9e6 years        | [290301 BC, 294241 AD] |
| ``ns`` | Nanosecond  | ± 292 years          | [ 1678 AD, 2262 AD]    |
| ``ps`` | Picosecond  | ± 106 days           | [ 1969 AD, 1970 AD]    |
| ``fs`` | Femtosecond | ± 2.6 hours          | [ 1969 AD, 1970 AD]    |
| ``as`` | Attosecond  | ± 9.2 seconds        | [ 1969 AD, 1970 AD]    |

실제 세계에서 볼 수 있는 데이터 유형의 경우 유용한 기본값은 ``datetime64[ns]``입니다. 적절한 정밀도로 현대 날짜의 유용한 범위를 인코딩할 수 있기 때문입니다.

마지막으로 ``datetime64`` 데이터 유형은 내장 Python ``datetime`` 유형의 일부 결함을 해결하지만 ``datetime`` 및 특히 ``dateutil``.
자세한 내용은 [NumPy의 datetime64 문서](http://docs.scipy.org/doc/numpy/reference/arrays.datetime.html)에서 확인할 수 있습니다.

### Dates and times in pandas: best of both worlds 
Pandas는 ``datetime`` 및 ``dateutil``의 사용 편의성을 ``numpy'의 효율적인 저장 및 벡터화된 인터페이스와 결합한 ``Timestamp`` 개체를 제공하기 위해 방금 논의한 모든 도구를 기반으로 합니다. datetime64``.
이러한 ``Timestamp`` 객체 그룹에서 Pandas는 ``Series`` 또는 ``DataFrame``의 데이터를 색인화하는 데 사용할 수 있는 ``DatetimeIndex``를 구성할 수 있습니다. 우리는 아래에서 이에 대한 많은 예를 보게 될 것입니다.

예를 들어 Pandas 도구를 사용하여 위의 데모를 반복할 수 있습니다.
유연한 형식의 문자열 날짜를 구문 분석하고 형식 코드를 사용하여 요일을 출력할 수 있습니다. 

```python 
import pandas as pd
date = pd.to_datetime("4th of July, 2015")
date
    Timestamp('2015-07-04 00:00:00'

date.strftime('%A')
    'Saturday'

date + pd.to_timedelta(np.arange(12), 'D')
    DatetimeIndex(['2015-07-04', '2015-07-05', '2015-07-06', '2015-07-07',
                '2015-07-08', '2015-07-09', '2015-07-10', '2015-07-11',
                '2015-07-12', '2015-07-13', '2015-07-14', '2015-07-15'],
                dtype='datetime64[ns]', freq=None)
```

## Pandas Time Series: Indexing by Time 
Pandas 시계열 도구가 실제로 유용해지는 곳은 **타임스탬프로 데이터 색인**을 시작할 때입니다.
예를 들어, 시간 인덱싱된 데이터가 있는 ``Series`` 객체를 생성할 수 있습니다.
```python 
index = pd.DatetimeIndex(['2014-07-04', '2014-08-04',
                          '2015-07-04', '2015-08-04'])
data = pd.Series([0, 1, 2, 3], index=index)
data
    2014-07-04    0
    2014-08-04    1
    2015-07-04    2
    2015-08-04    3
    dtype: int64

data['2014-07-04':'2015-07-04']
    2014-07-04    0
    2014-08-04    1
    2015-07-04    2
    dtype: int64

data['2015']
    2015-07-04    2
    2015-08-04    3
    dtype: int64
```
이제 시리즈에 이 데이터가 있으므로 이전 섹션에서 논의한 시리즈 인덱싱 패턴을 사용하여 날짜로 강제 변환할 수 있는 값을 전달할 수 있습니다


## Pandas Time Series Data Structures 
이 섹션에서는 시계열 데이터 작업을 위한 기본적인 Pandas 데이터 구조를 소개합니다.

- **타임스탬프**의 경우 Pandas는 ``timestamp`` 유형을 제공합니다. 앞서 언급했듯이 본질적으로 Python의 기본 ``datetime``을 대체하지만 더 효율적인 ``numpy.datetime64`` 데이터 유형을 기반으로 합니다. 연결된 인덱스 구조는 ``DatetimeIndex``입니다.
- **time Periods**의 경우 Pandas는 ``기간`` 유형을 제공합니다. 이것은 ``numpy.datetime64``를 기반으로 고정 주파수 간격을 인코딩합니다. 연결된 인덱스 구조는 ``PeriodIndex``입니다.
- **time deltas** 또는 **durations**의 경우 Pandas는 ``Timedelta`` 유형을 제공합니다. ``Timedelta``는 Python의 기본 ``datetime.timedelta`` 유형을 보다 효율적으로 대체하며 ``numpy.timedelta64``를 기반으로 합니다. 연결된 인덱스 구조는 ``TimedeltaIndex``입니다.

이러한 날짜/시간 개체의 가장 기본적인 것은 Timestamp 및 DatetimeIndex 개체입니다. 이러한 클래스 객체를 직접 호출할 수 있지만 다양한 형식을 구문 분석할 수 있는 pd.to_datetime() 함수를 사용하는 것이 더 일반적입니다. 단일 날짜를 pd.to_datetime()에 전달하면 타임스탬프가 생성됩니다. 기본적으로 일련의 날짜를 전달하면 DatetimeIndex가 생성됩니다.

```python 
dates = pd.to_datetime([datetime(2015, 7, 3), '4th of July, 2015',
                       '2015-Jul-6', '07-07-2015', '20150708'])
dates
    DatetimeIndex(['2015-07-03', '2015-07-04', '2015-07-06', '2015-07-07',
                '2015-07-08'],
                dtype='datetime64[ns]', freq=None)

dates.to_period('D')
    PeriodIndex(['2015-07-03', '2015-07-04', '2015-07-06', '2015-07-07',
                '2015-07-08'],
                dtype='int64', freq='D')

# 데이트 연산이 가능하다. --; 
dates - dates[0]
    TimedeltaIndex(['0 days', '1 days', '3 days', '4 days', '5 days'], dtype='timedelta64[ns]', freq=None)
```


### Regular sequences: ``pd.date_range()`` 
일반 날짜 시퀀스 생성을 보다 편리하게 하기 위해 Pandas는 이러한 목적을 위해 몇 가지 기능을 제공합니다: 타임스탬프용 ``pd.date_range()``, 마침표용 ``pd.period_range()``, ()`` 시간 델타.
우리는 파이썬의 ``range()``와 NumPy의 ``np.arange()``가 시작점, 끝점 및 선택적 단계 크기를 시퀀스로 바꾸는 것을 보았습니다.
마찬가지로, ``pd.date_range()``는 시작 날짜, 종료 날짜 및 선택적 빈도 코드를 허용하여 날짜의 규칙적인 시퀀스를 생성합니다.
기본적으로 빈도는 하루입니다.

```python 
pd.date_range('2015-07-03', '2015-07-10')
    DatetimeIndex(['2015-07-03', '2015-07-04', '2015-07-05', '2015-07-06',
                '2015-07-07', '2015-07-08', '2015-07-09', '2015-07-10'],
                dtype='datetime64[ns]', freq='D')

```
또는 날짜 범위를 시작 및 끝점이 아니라 시작점과 여러 기간으로 지정할 수 있습니다.
```python 
pd.date_range('2015-07-03', periods=8)
    DatetimeIndex(['2015-07-03', '2015-07-04', '2015-07-05', '2015-07-06',
                '2015-07-07', '2015-07-08', '2015-07-09', '2015-07-10'],
                dtype='datetime64[ns]', freq='D')

pd.date_range('2015-07-03', periods=8, freq='H')
    DatetimeIndex(['2015-07-03 00:00:00', '2015-07-03 01:00:00',
                '2015-07-03 02:00:00', '2015-07-03 03:00:00',
                '2015-07-03 04:00:00', '2015-07-03 05:00:00',
                '2015-07-03 06:00:00', '2015-07-03 07:00:00'],
                dtype='datetime64[ns]', freq='H')
```
Period 또는 Timedelta 값의 규칙적인 시퀀스를 생성하려면 매우 유사한 pd.period_range() 및 pd.timedelta_range() 함수가 유용합니다. 다음은 몇 가지 월간 기간입니다. 

```python 
pd.period_range('2015-07', periods=8, freq='M')
    PeriodIndex(['2015-07', '2015-08', '2015-09', '2015-10', '2015-11', '2015-12',
                '2016-01', '2016-02'],
                dtype='int64', freq='M')

pd.timedelta_range(0, periods=10, freq='H')
    TimedeltaIndex(['00:00:00', '01:00:00', '02:00:00', '03:00:00', '04:00:00',
                    '05:00:00', '06:00:00', '07:00:00', '08:00:00', '09:00:00'],
                dtype='timedelta64[ns]', freq='H')
```


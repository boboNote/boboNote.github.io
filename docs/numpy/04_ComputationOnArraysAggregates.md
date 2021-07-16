---
layout: default
title: Aggregation 
parent: Numpy
nav_order: 40
has_children: false
permalink: /docs/numpy/ComputationOnArraysAggregates
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Aggregations: Min, Max, and Everything In Between
종종 많은 양의 데이터에 직면했을 때 첫 번째 단계는 해당 데이터에 대한 요약 통계를 계산하는 것입니다. 아마도 가장 일반적인 요약 통계는 데이터 세트의 "전형적인" 값을 요약할 수 있는 평균 및 표준 편차이지만 다른 집계(합계, 곱, 중앙값, 최소값 및 최대값, 분위수 등)도 유용합니다. ).

NumPy에는 배열 작업을 위한 빠른 내장 집계 함수가 있습니다. 우리는 여기에서 그들 중 일부를 논의하고 시연할 것입니다.

## Summing the Values in an Array
빠른 예로서, 배열에 있는 모든 값의 합을 계산하는 것을 고려하십시오.
파이썬 자체는 내장된 ``sum`` 함수를 사용하여 이것을 할 수 있습니다:

```python 
import numpy as np

L = np.random.random(100) 
    array([ 0.34418595, 0.69141116, 0.31003297, 0.00791705, 0.61435161,
            0.02202142, 0.36469199, 0.31138919, 0.65016503, 0.00419661,
            0.65046542, 0.15264837, 0.66490646, 0.63488939, 0.59463359,
            0.76696222, 0.50886286, 0.07192312, 0.72703033, 0.38741703,
            0.66925678, 0.1167406 , 0.42422424, 0.49181127, 0.51902424,
            0.47184415, 0.37497961, 0.41779237, 0.93525278, 0.51514935,
            0.17760963, 0.63493344, 0.86954249, 0.42116996, 0.83305442,
            0.84477141, 0.64949828, 0.64960349, 0.86970081, 0.26926016,
            0.279132  , 0.94322261, 0.62917275, 0.27298398, 0.16950967,
            0.67062265, 0.76933514, 0.49843553, 0.34251219, 0.73564729,
            0.30726413, 0.90540878, 0.75271158, 0.04555391, 0.02403682,
            0.17294599, 0.4813733 , 0.9127613 , 0.28475578, 0.23144697,
            0.38785219, 0.87373867, 0.19075646, 0.78887353, 0.19868475,
            0.29459425, 0.45240614, 0.70364896, 0.43944366, 0.42564603,
            0.0572335 , 0.41926151, 0.07429234, 0.69807706, 0.60738325,
            0.76522845, 0.78248608, 0.07030944, 0.49997586, 0.66786631,
            0.92466939, 0.75708515, 0.08612084, 0.11234131, 0.76513945,
            0.14974248, 0.53154238, 0.81549574, 0.45006199, 0.84335111,
            0.58762769, 0.45484077, 0.37491343, 0.59216706, 0.27014178,
            0.22349027, 0.36993863, 0.26216947, 0.44353164, 0.18573038])


%timeit sum(L) 
    4.35 µs ± 70 ns per loop (mean ± std. dev. of 7 runs, 100000 loops each)
    55.612091166049424

%timeit np.sum(L) ## NumPy's version of the operation is computed much more quickly:  
    1.48 µs ± 4.76 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)

E.g) min(big_array), max(big_array) 
E.g) np.min(big_array) or big_array.min() , np.max(big_array)   big_array.max() ,sum(big_array) or  big_array.sum()
```


## 멀티 차원 집계 (min , max, sum)
한 가지 일반적인 집계 작업 유형은 행 또는 열을 따라 집계하는 것입니다.
2차원 배열에 일부 데이터가 저장되어 있다고 가정해 보겠습니다. 
기본적으로 Numpy는 전체에 해당하는 집계를 진행합니다. axis(축)을 인수로 취하고 축에 따라 집계 함수를 수행합니다. 

```python 
M = np.random.random((3, 4))
    array([ [0.16198651, 0.1135108 , 0.65571732, 0.12056812],
            [0.49653283, 0.61902739, 0.7331316 , 0.26568089],
            [0.77648257, 0.40269847, 0.71164727, 0.75119673]])


M.sum()
    5.80818050943761

M.min(axis=0)
    array([0.16198651, 0.1135108 , 0.65571732, 0.12056812])

M.min(axis=1)
    array([0.1135108 , 0.26568089, 0.40269847])

```

## 기타 집계 함수 
많은 집계 기능을 제공하지만 여기에서 자세히 논의하지 않겠습니다. 아래 표를 참조. 


| Function Name | NaN-safe Version | Description                               |
| ------------- | ---------------- | ----------------------------------------- |
| np.sum        | np.nansum        | Compute sum of elements                   |
| np.prod       | np.nanprod       | Compute product of elements (요서의 곱 계산) |
| np.mean       | np.nanmean       | Compute mean of elements                  |
| np.std        | np.nanstd        | Compute standard deviation  (표준편차)      |
| np.var        | np.nanvar        | Compute variance            (분산)         |
| np.min        | np.nanmin        | Find minimum value                        |
| np.max        | np.nanmax        | Find maximum value                        |
| np.argmin     | np.nanargmin     | Find index of minimum value               |
| np.argmax     | np.nanargmax     | Find index of maximum value               |
| np.median     | np.nanmedian     | Compute median of elements                |
| np.percentile | np.nanpercentile | Compute rank-based statistics of elements |
| np.any        | N/A              | Evaluate whether any elements are true    |
| np.all        | N/A              | Evaluate whether all elements are true    |


## 미국 대통령 평균키 
NumPy에서 사용할 수 있는 집계는 값 집합을 요약하는 데 매우 유용할 수 있습니다. 간단한 예로서 모든 미국 대통령의 키를 생각해 봅시다. 이 데이터는 쉼표로 구분된 레이블 및 값 목록인 President_heights.csv 파일에서 사용할 수 있습니다.

```python 
!head -4 data/president_heights.csv
    order,name,height(cm)
    1,George Washington,189
    2,John Adams,170
    3,Thomas Jefferson,189


import pandas as pd
data = pd.read_csv('data/president_heights.csv')
heights = np.array(data['height(cm)'])  # pandas axis=0  transform to Numpy 
heights
    array([ 189, 170, 189, 163, 183, 171, 185, 168, 173, 183, 173, 173, 175,
            178, 183, 193, 178, 173, 174, 183, 183, 168, 170, 178, 182, 180,
            183, 178, 182, 188, 175, 179, 183, 193, 182, 183, 177, 185, 188,
            188, 182, 185])


print("Mean height:       ", heights.mean())
print("Standard deviation:", heights.std())
print("Minimum height:    ", heights.min())
print("Maximum height:    ", heights.max())
    Mean height:        179.738095238
    Standard deviation: 6.93184344275
    Minimum height:     163
    Maximum height:     193

print("25th percentile:   ", np.percentile(heights, 25)) # 25%에 해당하는값 
print("Median:            ", np.median(heights))         # 중앙값
print("75th percentile:   ", np.percentile(heights, 75)) # 75%에 해당하는값
    25th percentile:    174.25
    Median:             182.0
    75th percentile:    183.0


```


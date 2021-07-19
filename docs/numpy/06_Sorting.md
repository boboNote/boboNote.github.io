---
layout: default
title: Sorting
parent: Numpy
nav_order: 60
has_children: false
permalink: /docs/numpy/Sorting
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Sort
np.sort 및 np.argsort
파이썬에는 목록과 함께 작동하는 내장 정렬 및 정렬 기능이 있지만 NumPy의 np.sort 기능이 우리의 목적에 훨씬 더 효율적이고 유용하기 때문에 여기에서 논의하지 않습니다. 기본적으로 np.sort는 mergesort 및 heapsort도 사용할 수 있지만 퀵소트 알고리즘인 $\mathcal{O}[N\log N]$를 사용합니다. 대부분의 애플리케이션에서 기본 퀵소트는 충분합니다.

```python
import numpy as np

x = np.array([2, 1, 4, 3, 5])
np.sort(x)
    array([1, 2, 3, 4, 5])

x.sort()   # in place 선호하는 경우 사용가능 
    array([1, 2, 3, 4, 5])

```

`argsort` 사용시에는 인덱스 위치값(*indices of the sorted elements*) 값을 반환 한다. 
이 결과의 첫 번째 요소는 가장 작은 요소의 인덱스를 제공하고 두 번째 값은 두 번째로 작은 요소의 인덱스를 제공하는 식입니다. 그런 다음 원하는 경우 이러한 인덱스를 사용하여(멋진 인덱싱을 통해) 정렬된 배열을 구성할 수 있습니다.


```python 
x = np.array([2, 1, 4, 3, 5])
i = np.argsort(x)
    array([1, 0, 3, 2, 4])

x[i]   # argsort 로 반환된 numpyArray 에서 위치 인덱스를 이용한 Selection 
    array([1, 2, 3, 4, 5])
```

## Sorting along rows or columns 
NumPy 정렬 알고리즘의 유용한 기능은 ``axis`` 인수를 사용하여 다차원 배열의 특정 행이나 열을 따라 정렬하는 기능입니다.
**이것은 각 행이나 열을 독립적인 배열로 취급하고 행이나 열 값 사이의 모든 관계가 손실된다는 점을 명심하십시오!**


```python 
rand = np.random.RandomState(42)
X = rand.randint(0, 10, (4, 6))
X
    array([ [6, 3, 7, 4, 6, 9],
            [2, 6, 7, 4, 3, 7],
            [7, 2, 5, 4, 1, 7],
            [5, 1, 4, 0, 9, 5]])

# X의  각 열을 정렬 
np.sort(X, axis=0)
    array([ [2, 1, 4, 0, 1, 5],
            [5, 2, 5, 4, 3, 7],
            [6, 3, 7, 4, 6, 7],
            [7, 6, 7, 4, 9, 9]])

# X의 각 행을 정렬
np.sort(X, axis=1)
    array([ [3, 4, 6, 6, 7, 9],
            [2, 3, 4, 6, 7, 7],
            [1, 2, 4, 5, 7, 7],
            [0, 1, 4, 5, 5, 9]])

```

## Partial Sorts: Partitioning 
때때로 전체 배열을 정렬할 필요는 없고 단순히 배열에서 K개의 가장 작은 값을 찾고 싶을때가 있다. `np.partition`은 배열과 숫자 K를 취해 새로운 배열을 반환하는데, 반환된 파티션의 왼쪽에는 K개의 가장 작은 값이 있고 오른쪽에는 나머지 값이 임의의 순서로 채워져 있다. 

K parameter 값을 바까 확인한결결 k 개의 작은 값은 Sort 정렬되지 않는다. 
 ``` python 

 x = np.array([7, 2, 3, 1, 6, 5, 4])
np.partition(x, 1)
np.partition(x, 2)
np.partition(x, 3)
np.partition(x, 4)
    array([1, 2, 3, 7, 6, 5, 4])
    array([1, 2, 3, 7, 6, 5, 4])
    array([2, 1, 3, 4, 6, 5, 7])
    array([2, 1, 3, 4, 5, 6, 7])

 ```

## Example: k-Nearest Neighbors
여러 축을 따라 이 ``argsort`` 함수를 사용하여 집합에서 각 점의 가장 가까운 이웃을 찾는 방법을 빠르게 살펴보겠습니다.
2차원 평면에 임의의 10개 점 집합을 만드는 것으로 시작하겠습니다.
표준 규칙을 사용하여 $10 \times 2$ 배열로 정렬합니다.

```python 

rand = np.random.RandomState(89)
X = rand.rand(5, 2)
    array([ [0.49969432, 0.25593713],
            [0.25810063, 0.09692171],
            [0.56418511, 0.01599007],
            [0.15259523, 0.48024773],
            [0.09987276, 0.41696389]])

%matplotlib inline
import matplotlib.pyplot as plt
import seaborn; seaborn.set() # 플롯 스타일링
plt.scatter(X[:, 0], X[:, 1], s=100);

```

![06_plit](./img/06_plit.png)
이제 각 점 쌍 사이의 거리를 계산합니다. 두 점 사이의 거리 제곱은 각 차원의 차이 제곱의 합입니다 

```python 
dist_sq = np.sum((X[:, np.newaxis, :] - X[np.newaxis, :, :]) ** 2, axis=-1)
    array([ [0.        , 0.08365342, 0.06173365, 0.17079302, 0.1857869 ],
            [0.08365342, 0.        , 0.10023764, 0.15807023, 0.12746306],
            [0.06173365, 0.10023764, 0.        , 0.38494141, 0.37636597],
            [0.17079302, 0.15807023, 0.38494141, 0.        , 0.0067845 ],
            [0.1857869 , 0.12746306, 0.37636597, 0.0067845 , 0.        ]])

# 각 쌍의 점들 사이의 좌표 차이를 계산함
differences = X[:, np.newaxis, :] - X[np.newaxis, :, :]
differences.shape
    (10, 10, 2)

# 좌표 차이를 제곱함
sq_differences = differences ** 2
sq_differences.shape
    (10, 10, 2)

# 제곱 거리를 구하기 위해 좌표 차이를 더함
dist_sq = sq_differences.sum(-1)
dist_sq.shape
    (10, 10)

dist_sq.diagonal()
    array([0., 0., 0., 0., 0., 0., 0., 0., 0., 0.])

nearest = np.argsort(dist_sq, axis=1)
nearest
```





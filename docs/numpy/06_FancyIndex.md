---
layout: default
title: FancyIndex
parent: Numpy
nav_order: 50
has_children: false
permalink: /docs/numpy/FancyIndex
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## FancyIndex
이전 섹션에서 단순 인덱스(예: arr[0]), 슬라이스(예: arr[:5]), 부울 마스크(예: arr[arr > 0])를 사용하여 배열의 일부에 액세스하고 수정하는 방법을 보았습니다. ). 이 섹션에서는 멋진 인덱싱으로 알려진 또 다른 스타일의 배열 인덱싱을 살펴보겠습니다. 멋진 인덱싱은 우리가 이미 본 간단한 인덱싱과 비슷하지만 단일 스칼라 대신 인덱스 배열을 전달합니다. 이를 통해 배열 값의 복잡한 하위 집합에 매우 빠르게 액세스하고 수정할 수 있습니다.

## Exploring Fancy Indexing
멋진 인덱싱은 개념적으로 간단합니다. 한 번에 여러 배열 요소에 액세스하기 위해 인덱스 배열을 전달하는 것을 의미합니다. 예를 들어 다음 배열을 고려하십시오.


```python 
import numpy as np
rand = np.random.RandomState(42)

x = rand.randint(100, size=10)
x
    array([51, 92, 14, 71, 60, 20, 82, 86, 74, 74])

[x[3], x[7], x[2]]
    [71, 86, 14]

ind = [3, 7, 4]
x[ind]
    array([71, 86, 60])

ind = np.array([[3, 7],
                [4, 5]])
x[ind]
    array([ [71, 86],
            [60, 20]])

X = np.arange(12).reshape((3, 4))
X
    array([ [ 0,  1,  2,  3],
            [ 4,  5,  6,  7],
            [ 8,  9, 10, 11]])

row = np.array([0, 1, 2])
col = np.array([2, 1, 3])

X[row, col]  # (row,col), (row,col), (row,col) 조합으로 결과를 가져온다. 
    array([ 2,  5, 11])


print(X)
    [   [ 0  1  2  3]
        [ 4  5  6  7]
        [ 8  9 10 11]]
    
X[2, [2, 0, 1]]            # row index 2 , col [2, 0, 1]
    array([10,  8,  9])

X[1:, [2, 0, 1]]           # row index 1,2 , col [2, 0, 1]
    array([ [ 6,  4,  5],
            [10,  8,  9]])

mask = np.array([1, 0, 1, 0], dtype=bool)
row[:, np.newaxis]
    array([ [0],
            [1],
            [2]])

X[row[:, np.newaxis], mask]
    array([ [ 0,  2],
            [ 4,  6],
            [ 8, 10]])
```
인덱스 시작위치는 0 부터 

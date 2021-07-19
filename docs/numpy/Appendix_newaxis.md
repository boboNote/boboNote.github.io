---
layout: default
title: newaxis
parent: Numpy
nav_order: 1000
has_children: false
permalink: /docs/numpy/newaxis
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## newaxis 표현은 무엇이냐? 
간단히 이야기 해서 존재하는 numpy array의 차원을 늘려준다 보시면 되겠습니다. 
**1D 는 2D가 되고 2D는 3D가 되고 3D는 +1D**

[![newaxis](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcSoMOs%2Fbtqt0a2Dc2y%2FQhkfwhiWqeUKvNfsM2H29K%2Fimg.png)](https://img1.daumcdn.net)


## Scenario 
3가지 시나리오에 사용하기 적합해 보입니다. 

### First 
1D array를 row vector나 column vector로 사용하고 싶을 경우
```python 
import numpy as np
arr = np.arange(4)
print(arr)
print(arr.shape)
    [0 1 2 3]
    (4,)

row_vec = arr[np.newaxis, :]
print(row_vec)
print(row_vec.shape)
col_vec = arr[:, np.newaxis]
print(col_vec)
print(col_vec.shape)
    [[0 1 2 3]]
    (1, 4)
    [   [0]
        [1]
        [2]
        [3]]
    (4, 1)
```

### Second 
numpy broadcasting이라고 shape이 다른 array간 연산을 할때도  유용합니다.

```python 
x1 = np.array([1, 2, 3, 4, 5])
x2 = np.array([5, 4, 3])
x1+x2

    ---------------------------------------------------------------------------
    ValueError                                Traceback (most recent call last)
    <ipython-input-8-916b8842afd1> in <module>
        1 x1 = np.array([1, 2, 3, 4, 5])
        2 x2 = np.array([5, 4, 3])
    ----> 3 x1+x2

    ValueError: operands could not be broadcast together with shapes (5,) (3,) 
```

shape 이 서로 다른 두 백터 연산은 불가능 합니다. 
shape에 변화를 주고 BroadCast 기능을 이용하여 연산을 수행합니다. 

```python 
x1 = np.array([1, 2, 3, 4, 5])
x2 = np.array([5, 4, 3])

x1_col = x1[:, np.newaxis]
addX = x1_col + x2
addX

    array([ [ 6,  5,  4],
            [ 7,  6,  5],
            [ 8,  7,  6],
            [ 9,  8,  7],
            [10,  9,  8]])
```

### Third 
고차원으로 만들고 싶다면 2차 이상의 Array를 만들어 낼수 있습니다. 

```python 
arr = np.arange(5*5).reshape(5,5)
arr.shape
    (5, 5)

# promoting 2D array to a 5D array
arr_5D = arr[np.newaxis, np.newaxis, np.newaxis]
arr_5D.shape
    (1, 1, 1, 5, 5)
```





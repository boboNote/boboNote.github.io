---
layout: default
title: Numpy 배열의 기본 기능 
parent: Numpy
nav_order: 20
has_children: false
permalink: /docs/numpy/theBasicsOfNumpyArrays
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 기본 배열 조작 기능 목록 

* 배열 속성 지정: 배열의 크기, 모양, 메모리 소비량, 데이터 타입을 결정
* 배열 인덱싱: 개별 배열 요솟값을 가져오고 설정
* 배열 슬라이싱: 큰 배열 내에 있는 작은 하위 배열을 가져오고 설정
* 배열 재구조화: 해당 배열의 형상을 변경
* 배열 결합 및 분할: 여러 배열을 하나로 결합하고 하나의 배열을 여러 개로 분할


### 배열 속성 

* ndim : 차원의 개수 
* shape : 각 차원의 크기 
* size : 전체 배열의 크기 
* dtype : 배열의 데이터 타입 
* itemsize : 배열 요소의 크기 dtype 크기 와 동일 
* nbytes : 전체크기 

```python 
import numpy as np
np.random.seed(0)  # 재현 가능성을 위한 시드 값

x1 = np.random.randint(10, size=6)  # 1차원 배열
x2 = np.random.randint(10, size=(3, 4))  # 2차원 배열
x3 = np.random.randint(10, size=(3, 4, 5))  # 3차원 배열

print("x3 ndim: ", x3.ndim)
print("x3 shape:", x3.shape)
print("x3 size: ", x3.size)
    x3 ndim:  3
    x3 shape: (3, 4, 5)
    x3 size:  60

print("dtype:", x3.dtype) 
    dtype: int64

print("itemsize:", x3.itemsize, "bytes")
print("nbytes:", x3.nbytes, "bytes") 
    itemsize: 8 bytes
    nbytes: 480 bytes
```

### 슬라이싱 
`:` 기호로 표기되는 슬라이스(slice)표기법으로 하위 배열에 접근할 수 있다. 

```python 
x1 
    array([5, 0, 3, 3, 7, 9])

x1[0] # 5
x1[4] # 7
x1[-1] # 9 
x1[-2] # 7

x2 
    array([ [3, 5, 2, 4],
            [7, 6, 8, 8],
            [1, 6, 7, 7]])

x2[0, 0] # 3 
x2[2, 0] # 1 
x2[2, -1] # 7 

x2[0, 0] = 12 # 속성 값이 변경될수 있다.  
    array([ [12,  5,  2,  4],
            [ 7,  6,  8,  8],
            [ 1,  6,  7,  7]])

x1[0] = 3.14159  # 이 값의 소수점 이하는 잘릴 것이다! array([3, 0, 3, 3, 7, 9]) 


## One-dimensional subarrays
x = np.arange(10) # array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
x[:5]  # 첫 다섯 개의 요소 array([0, 1, 2, 3, 4])
x[5:]  # 인덱스 5 다음의 요소들 array([5, 6, 7, 8, 9])
x[4:7]  # 중간 하위 배열 array([4, 5, 6])
x[::2]  # 하나 걸러 하나씩의 요소들로 구성된 배열 array([0, 2, 4, 6, 8])
x[1::2]  # 인덱스 1로부터 시작하여 하나 걸러 하나씩 요소들로 구성된 배열 array([1, 3, 5, 7, 9]) 


x[::-1]  # 모든 요소들을 거꾸로 나열 array([9, 8, 7, 6, 5, 4, 3, 2, 1, 0]) 
x[5::-2]  # 인덱스 5부터 하나 걸러 하나씩 요소들을 거꾸로 나열 array([5, 3, 1]) 

## Multi-dimensional subarrays 
x2 
    array(  [[12,  5,  2,  4],
            [ 7,  6,  8,  8],
            [ 1,  6,  7,  7]])


x2[:2, :3]  # 두 개의 행, 세 개의 열
    array([[12,  5,  2],
        [ 7,  6,  8]])

x2[:3, ::2]  # 모든 행, 한 열 걸러 하나씩
    array(  [[12,  2],
            [ 7,  8],
            [ 1,  7]])

x2[::-1, ::-1] 
    array(  [[ 7,  7,  6,  1],
            [ 8,  8,  6,  7],
            [ 4,  2,  5, 12]])


# Accessing array rows and columns 선택적 인덱싱 슬라싱 
x2[:, 0]  # x2의 첫 번째 열 
    array([12  7  1])

x2[0, :]  # x2의 첫 번째 행  `x2[0]` 와 동일하다.  
    array([12  5  2  4])


# Subarrays as no-copy views  selection 이후 반환된 결과는 복재본이 아니다. 
x2 
    array(  [[3, 5, 2, 4],
            [7, 6, 8, 8],
            [1, 6, 7, 7]])

x2_sub = x2[:2, :2]
x2_sub[0, 0] = 99

x2
    array(  [[99  5  2  4]
    [ 7  6  8  8]
    [ 1  6  7  7]])


# 'copy()' 를 이용하여 객체 복사가 된다. 
x2_sub_copy = x2[:2, :2].copy()
x2_sub_copy
    array(  [[3 5]
    [7 6]])

x2_sub_copy[0, 0] = 42
x2_sub_copy
    array(  [[42  5]
    [ 7  6]])

x2
    array([[3, 5, 2, 4],
        [7, 6, 8, 8],
        [1, 6, 7, 7]])



```


## Reshaping of Arrays 
``reshape`` 을 이용하여 차원 변경이 가능하다. 이것이 작동하려면 초기 배열의 크기가 재구성된 배열의 크기와 일치해야 합니다. 가능한 경우 reshape 메서드는 초기 배열의 복사 금지 보기를 사용하지만 비연속 메모리 버퍼의 경우 항상 그런 것은 아닙니다.또 다른 일반적인 모양 변경 패턴은 1차원 배열을 2차원 행 또는 열 행렬로 변환하는 것입니다. 이것은 reshape 메서드로 수행하거나 슬라이스 작업 내에서 newaxis 키워드를 사용하여 더 쉽게 수행할 수 있습니다.


```python 
# reshape `Element 갯수와 차원에 속한 Element 갯수가 동일해야 한다.`
grid = np.arange(1, 10).reshape((3, 3))
grid
    array(  [[1, 2, 3],
            [4, 5, 6],
            [7, 8, 9]])

x = np.array([1, 2, 3])  # shape (3, )
    array([1, 2, 3])

x.reshape((1, 3))        # 행 백터로 변경, shape (1,3) 
    array([[1, 2, 3]])

x[np.newaxis, :]         # newaxis를 이용한 행 벡터 이건 잘모르겠다. 
    array([[1, 2, 3]])

x.reshape((3, 1))        # reshape을 이용한 열 벡터로 변경 
    array(  [[1],
            [2],
            [3]])

x[:, np.newaxis]         # newaxis를 이용한 열 벡터
    array(  [[1],
            [2],
            [3]])



```

## Array Concatenation and Splitting
여러 배열을 하나로 결합하고 반대로 단일 배열을 여러 배열로 분할하는 것도 가능합니다.

### Concatenation of arrays
Numpy에서 두 배열의 연결 또는 결합은 주로 np.concatenate , np.vstack , np.hstack 루틴을 사용하여 수행됩니다. 

```python 
x = np.array([1, 2, 3])
y = np.array([3, 2, 1])

np.concatenate([x, y])
resunt 
    array([1, 2, 3, 3, 2, 1])

z = [99, 99, 99]
np.concatenate([x, y, z])
    array([ 1  2  3  3  2  1 99 99 99])


## 2차원 Array 도 가능하다. 
grid = np.array([[1, 2, 3],
                 [4, 5, 6]])

np.concatenate([grid, grid])
    array([[1, 2, 3],
       [4, 5, 6],
       [1, 2, 3],
       [4, 5, 6]])

# 두 번째 축을 따라 연결(0부터 시작되는 인덱스 방식)  
# (2, 3) 에서 axis=1 을 이용하여 concatenate를 진행하면 shape이 변경된다. 
np.concatenate([grid, grid], axis=1)
    array(  [[1, 2, 3, 1, 2, 3],
            [4, 5, 6, 4, 5, 6]])

# 가로(Horizontal) ,세로(Vertical) 
x = np.array([1, 2, 3])
grid = np.array([[9, 8, 7],
                 [6, 5, 4]])

# 세로(수직)으로 붙여넣기 
np.vstack([x, grid])
    array(  [[1, 2, 3],
            [9, 8, 7],
            [6, 5, 4]])

# 가로로 붙여넣기
y = np.array([[99],
              [99]])
np.hstack([grid, y])
    array([ [ 9,  8,  7, 99],
            [ 6,  5,  4, 99]])


```

## Splitting of arrays
``np.split``, ``np.hsplit``, ``np.vsplit`` 을 이용하여 나눌수 있다. 

```python
x = [1, 2, 3, 99, 99, 3, 2, 1]
x1, x2, x3 = np.split(x, [3, 5])
print(x1, x2, x3)
    [1 2 3] [99 99] [3 2 1]

grid = np.arange(16).reshape((4, 4))
    array([ [ 0,  1,  2,  3],
            [ 4,  5,  6,  7],
            [ 8,  9, 10, 11],
            [12, 13, 14, 15]])

upper, lower = np.vsplit(grid, [2])
print(upper)
print(lower)
    [   [0 1 2 3]
        [4 5 6 7]]
    [   [ 8  9 10 11]
        [12 13 14 15]]

left, right = np.hsplit(grid, [2])
print(left)
print(right)
    [   [ 0  1]
        [ 4  5]
        [ 8  9]
        [12 13]]
    [   [ 2  3]
        [ 6  7]
        [10 11]
        [14 15]]

```


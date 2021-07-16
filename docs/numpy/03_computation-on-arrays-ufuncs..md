---
layout: default
title: Numpy ufuncs
parent: Numpy
nav_order: 30
has_children: false
permalink: /docs/numpy/ComputationOnArraysUfuncs.
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Numpy Array : Universla Function 
NumPy 배열의 계산은 매우 빠르거나 매우 느릴 수 있습니다. 이를 빠르게 만드는 핵심은 일반적으로 NumPy의 범용 함수(ufuncs)를 통해 구현되는 벡터화된 작업을 사용하는 것입니다. 이 섹션은 NumPy의 ufuncs에 대한 필요성을 동기를 부여합니다. NumPy는 배열 요소에 대한 반복 계산을 훨씬 더 효율적으로 만드는 데 사용할 수 있습니다. 그런 다음 NumPy 패키지에서 사용할 수 있는 가장 일반적이고 유용한 산술 함수를 소개합니다.

## The Slowness of Loops
Python의 기본 구현(CPython이라고 함)은 일부 작업을 매우 느리게 수행합니다. 이것은 부분적으로 언어의 동적이고 해석된 특성 때문입니다. 유형이 유연하여 작업 시퀀스를 C 및 Fortran과 같은 언어에서와 같이 효율적인 기계 코드로 컴파일할 수 없다는 사실입니다. 최근에 이 약점을 해결하기 위한 다양한 시도가 있었습니다. 잘 알려진 예는 Python의 JIT 컴파일 구현인 PyPy 프로젝트입니다. Python 코드를 컴파일 가능한 C 코드로 변환하는 Cython 프로젝트; Python 코드 조각을 빠른 LLVM 바이트 코드로 변환하는 Numba 프로젝트. 이들 각각은 장단점이 있지만 세 가지 접근 방식 중 어느 것도 아직 표준 CPython 엔진의 도달 범위와 인기를 능가하지 못했다고 말하는 것이 안전합니다.

Python의 상대적인 부진은 일반적으로 많은 작은 작업이 반복되는 상황에서 나타납니다. 예를 들어 각 요소에서 작동하기 위해 배열을 반복합니다. 예를 들어 값의 배열이 있고 각각의 역수를 계산하고 싶다고 상상해 보십시오. 간단한 접근 방식은 다음과 같습니다.


```python 
import numpy as np
np.random.seed(0)

def compute_reciprocals(values):
    output = np.empty(len(values))
    for i in range(len(values)):
        output[i] = 1.0 / values[i]
    return output
        
values = np.random.randint(1, 10, size=5)
compute_reciprocals(values)


big_array = np.random.randint(1, 100, size=1000000)
%timeit compute_reciprocals(big_array)
 
    1 loop, best of 3: 2.91 s per loop
```
위 소스 코드 런타임을 확인하면 compute_reciprocals 수행시간이 오래 걸린다는걸 확인할수 있다. 

## UFuncs 
백터 연산에 최적화 되어 있다. 
Brodcast 
[![Numpy Brodcast](http://www.astroml.org/_images/fig_broadcast_visual_1.png)](http://www.astroml.org/)


| Operator | Equivalent ufunc | Description                         |
| -------- | ---------------- | ----------------------------------- |
| +        | np.add           | Addition (e.g., 1 + 1 = 2)          |
| \-       | np.subtract      | Subtraction (e.g., 3 - 2 = 1)       |
| \-       | np.negative      | Unary negation (e.g., \-2)          |
| \*       | np.multiply      | Multiplication (e.g., 2 \* 3 = 6)   |
| /        | np.divide        | Division (e.g., 3 / 2 = 1.5)        |
| //       | np.floor\_divide | Floor division (e.g., 3 // 2 = 1)   |
| \*\*     | np.power         | Exponentiation (e.g., 2 \*\* 3 = 8) |
| %        | np.mod           | Modulus/remainder (e.g., 9 % 4 = 1) |


``` python 
x = np.arange(4)
print("x     =", x)
print("x + 5 =", x + 5)
print("x - 5 =", x - 5)
print("x * 2 =", x * 2)
print("x / 2 =", x / 2)
print("x // 2 =", x // 2)  # 바닥 나눗셈(나머지는 버림)

print("-x     = ", -x)
print("x ** 2 = ", x ** 2)
print("x % 2  = ", x % 2)
    x     = [0 1 2 3]
    x + 5 = [5 6 7 8]
    x - 5 = [-5 -4 -3 -2]
    x * 2 = [0 2 4 6]
    x / 2 = [ 0.   0.5  1.   1.5]
    x // 2 = [0 0 1 1]

    -x     =  [ 0 -1 -2 -3]
    x ** 2 =  [0 1 4 9]
    x % 2  =  [0 1 0 1]



-(0.5*x + 1) ** 2



```

## Absolute value 
파이썬 Built-in 산수를 이용하여 운영가능하다. 

```python 
x = np.array([-2, -1, 0, 1, 2])
abs(x)  # np.absolute(x)  or np.abs(x)  
    array([2, 1, 0, 1, 2])


x = np.array([3 - 4j, 4 - 3j, 2 + 0j, 0 + 1j])
np.abs(x) 
    array([ 5.,  5.,  2.,  1.])


```

### 삼각함수 

```python
theta = np.linspace(0, np.pi, 3)
print("theta      = ", theta)
print("sin(theta) = ", np.sin(theta))
print("cos(theta) = ", np.cos(theta))
print("tan(theta) = ", np.tan(theta)) 
    theta      =  [ 0.          1.57079633  3.14159265]
    sin(theta) =  [  0.00000000e+00   1.00000000e+00   1.22464680e-16]
    cos(theta) =  [  1.00000000e+00   6.12323400e-17  -1.00000000e+00]
    tan(theta) =  [  0.00000000e+00   1.63312394e+16  -1.22464680e-16]
```

### (지수함수 ,로그함수) And (지수함수 1p , log 1p) 

```python 
x = [1, 2, 3]
print("x     =", x)
print("e^x   =", np.exp(x))
print("2^x   =", np.exp2(x))
print("3^x   =", np.power(3, x)) 
    x     = [1, 2, 3]
    e^x   = [  2.71828183   7.3890561   20.08553692]
    2^x   = [ 2.  4.  8.]
    3^x   = [ 3  9 27]

x = [1, 2, 4, 10]
print("x        =", x)
print("ln(x)    =", np.log(x))
print("log2(x)  =", np.log2(x))
print("log10(x) =", np.log10(x)) 
    x        = [1, 2, 4, 10]
    ln(x)    = [ 0.          0.69314718  1.38629436  2.30258509]
    log2(x)  = [ 0.          1.          2.          3.32192809]
    log10(x) = [ 0.          0.30103     0.60205999  1.        ]


x = [0, 0.001, 0.01, 0.1]
print("exp(x) - 1 =", np.expm1(x))
print("log(1 + x) =", np.log1p(x)) 
    exp(x) - 1 = [ 0.          0.0010005   0.01005017  0.10517092]
    log(1 + x) = [ 0.          0.0009995   0.00995033  0.09531018]  
```
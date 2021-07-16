---
layout: default
title: 파이썬의 리스트/배열 이해하기 
parent: Numpy
nav_order: 10
has_children: false
permalink: /docs/numpy/list_array
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## List Vs Array 

* 리스트 : 여러 개의 요소를 담는 가변적인 표준 컨테이너 
* 배열 : 단일 데이터 타입을 담는 컨테이너, 리스트와 유사하나 데이터 타입이 고정 1개로 제한된다. 데이터타입은 객체 생성 시점에 type code를 명시해주어야 한다. 

리스트와 같이 이렇게 유연한 타입을 허용하려면 리스트의 각 항목에 타입 정보와 참조 횟수, 기타 정보가 들어가야 한다. 즉, 각 항목이 완전한 파이썬 객체인 셈이다. 모든 변수가 같은 타입인 경우에는 이 정보가 대부분  불필요하게 중복되므로 고정 타입 배열에 데이터를 저장하는 것이 더 효율적일 수 있다. ``파이썬의 리스트는 동적 타입 리스트이고, 배열(그리고 Numpy 의 배열)은 고정타입이다.``

 파이썬의 array 객체는 배열 기반의 데이터에 효율적인 저장소를 제공하는 반면, NumPy 는 그 데이터에 효율적인 연산을 추가한다. 

 ```python 
np.array([1, 4, 2, 5, 3]) # 정수 배열: 
np.array([3.14, 4, 2, 3]) # 
np.array([1, 2, 3, 4], dtype='float32')
np.array([range(i, i + 3) for i in [2, 4, 6]])  # 리스트를 중첩시키면 다차원 배열이 됨 
    array([[2, 3, 4],
        [4, 5, 6],
        [6, 7, 8]])



np.zeros(10, dtype=int) # 0으로 채원진 길이 10의 정수 배열 만들기 
    array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0])


np.ones((3, 5), dtype=float) # 1로 채워진 3x5 부동소수 배열 만들기 
    array([[ 1.,  1.,  1.,  1.,  1.],
        [ 1.,  1.,  1.,  1.,  1.],
        [ 1.,  1.,  1.,  1.,  1.]])

np.full((3, 5), 3.14) # 3.14로 채워진 배열 만들기 
    array([[ 3.14,  3.14,  3.14,  3.14,  3.14],
        [ 3.14,  3.14,  3.14,  3.14,  3.14],
        [ 3.14,  3.14,  3.14,  3.14,  3.14]])

np.arange(0, 20, 2)   # 선형 수열로 채워진 배열 만들기  0 에서 시작해서 2씩 더하기 20까지 채움 (내장 함수인 range()와 유사함)
    array([ 0,  2,  4,  6,  8, 10, 12, 14, 16, 18])


np.linspace(0, 1, 5) # 0과 1 사이에 일정한 간격을 가진 다섯 개의 값으로 채원진 배열 만들기 
    array([ 0.  ,  0.25,  0.5 ,  0.75,  1.  ])


np.random.random((3, 3)) # 표준 정규 분표를 이용하여 3*3 배열 만들기 
    array([[ 0.99844933,  0.52183819,  0.22421193],
       [ 0.08007488,  0.45429293,  0.20941444],
       [ 0.14360941,  0.96910973,  0.946117  ]])

np.random.normal(0, 1, (3, 3)) # 정규 분포(평균=0, 표준편차=1)의 난수로 채워진 3x3 배열 만들기
    array([[ 1.51772646,  0.39614948, -0.10634696],
       [ 0.25671348,  0.00732722,  0.37783601],
       [ 0.68446945,  0.15926039, -0.70744073]])


np.random.randint(0, 10, (3, 3)) # 구간 [0,10] 사이의 임의의 정수로 채워진 3x3 배열 만들기 
    array([[2, 3, 4],
       [5, 7, 8],
       [0, 5, 0]])


np.eye(3) # 3x3 단위 행렬 만들기  정사 행렬을 만든다. 
    array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],
       [ 0.,  0.,  1.]])



np.empty(3) # 세 개의 정수를 가지는 초기화되지 않은 배열 만들기 / 값은 해당 메모리 위치에 이미 존재하고 있는 값으로 채워짐 
    array([ 1.,  1.,  1.])

 ```


## Numpy에서의 자료형 
NumPy에서는 제공하는 숫자형 자료형의 종류는&nbsp;정수(int), 부호없는 정수(uint), 실수(float), 복소수(complex), 논리(bool)로 총 5가지로 나뉜다.

넘파이 패키지를 사용해서 파이썬 코딩을 하다보면 float32, float64, int8, int16, uint8, bool과 같은 것을 보게 됩니다. 오늘은 이것들이 무엇을 의미하는지 정리하도록 하겠습니다. 
우선 int는 정수형, uint는 부호가 없는 정수형, float는 부동소수형을 의미합니다. 그리고 뒤에 붙는 숫자는 값을 저장하는데 사용되는 비트수를 의미합니다. 클수록 더 넓은 범위의 숫자를 담을 수 있습니다. 다루는 숫자의 크기를 고려해서 적절한 자료형을 선택해주면 됩니다. 


### 정수형 자료형 
int8 => 1비트당 2개의 숫자를 표현할 수 있으므로 8비트면 2^8 = 256개의 정수를 표현할 수 있습니다. 즉 -128에서 127까지 표현가능합니다.

### 양수만 표현하는 정수형 자료형 
uint8 => 2^8개의 부호없는 정수표현 가능. 0에서 255까지. 그레이스케일 또는 3채널 컬러 이미지를 담을 때 많이 사용됩니다. 

### 부동소수형(실수형) 자료형 
float16 => 1비트는 부호에, 5비트는 정수부분을 나타내는데, 10비트는 소수부분을 나타내는데 사용됩니다. 이에 대한 자세한 내용은 https://en.wikipedia.org/wiki/Single-precision_floating-point_format를 참고해주세요.^^

### 논리형 자료형 
bool => 참(True)과 거짓(False)을 표현하기 위한 자료형입니다. 


| Data type  | Description                                                                    |
| ---------- | ------------------------------------------------------------------------------ |
| bool\_     | Boolean (True or False) stored as a byte                                       |
| int\_      | Default integer type (same as C long; normally either int64 or int32)          |
| intc       | Identical to C int (normally int32 or int64)                                   |
| intp       | Integer used for indexing (same as C ssize\_t; normally either int32 or int64) |
| int8       | Byte (-128 to 127)                                                             |
| int16      | Integer (-32768 to 32767)                                                      |
| int32      | Integer (-2147483648 to 2147483647)                                            |
| int64      | Integer (-9223372036854775808 to 9223372036854775807)                          |
| uint8      | Unsigned integer (0 to 255)                                                    |
| uint16     | Unsigned integer (0 to 65535)                                                  |
| uint32     | Unsigned integer (0 to 4294967295)                                             |
| uint64     | Unsigned integer (0 to 18446744073709551615)                                   |
| float\_    | Shorthand for float64.                                                         |
| float16    | Half precision float: sign bit, 5 bits exponent, 10 bits mantissa              |
| float32    | Single precision float: sign bit, 8 bits exponent, 23 bits mantissa            |
| float64    | Double precision float: sign bit, 11 bits exponent, 52 bits mantissa           |
| complex\_  | Shorthand for complex128.                                                      |
| complex64  | Complex number, represented by two 32-bit floats                               |
| complex128 | Complex number, represented by two 64-bit floats                               |






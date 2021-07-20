---
layout: default
title: High-Performance
parent: Pandas
nav_order: 110
has_children: false
permalink: /docs/pandas/High-Performance
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# High-Performance Pandas: eval() and query()
이전 섹션에서 이미 보았듯이 PyData 스택의 기능은 직관적인 구문을 통해 기본 작업을 C로 푸시하는 NumPy 및 Pandas의 기능을 기반으로 합니다. 예는 NumPy의 벡터화/브로드캐스트 작업 및 그룹화 유형 작업입니다. 판다에서. 이러한 추상화는 많은 일반적인 사용 사례에서 효율적이고 효과적이지만, 종종 임시 중간 개체 생성에 의존하므로 계산 시간과 메모리 사용에 과도한 오버헤드가 발생할 수 있습니다.

버전 0.13(2014년 1월 출시)부터 Pandas에는 값비싼 중간 배열 할당 없이 C 속도 작업에 직접 액세스할 수 있는 몇 가지 실험 도구가 포함되어 있습니다. 이것은 Numexpr 패키지에 의존하는 eval() 및 query() 함수입니다. 이 노트북에서는 사용 방법을 살펴보고 사용에 대해 생각할 수 있는 경우에 대한 몇 가지 경험적 규칙을 제공합니다.

##  Motivating ``query()`` and ``eval()``: Compound Expressions
우리는 이전에 NumPy와 Pandas가 빠른 벡터화된 연산을 지원한다는 것을 보았습니다. 예를 들어, 두 배열의 요소를 추가할 때:

NumPy 배열에 대한 계산: 범용 함수에서 논의한 바와 같이, 이것은 파이썬 루프 또는 이해를 통해 추가를 수행하는 것보다 훨씬 빠릅니다.

```python 
import numpy as np
rng = np.random.RandomState(42)
x = rng.rand(1000000)
y = rng.rand(1000000)
%timeit x + y
    724 µs ± 24.3 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)

%timeit np.fromiter((xi + yi for xi, yi in zip(x, y)), dtype=x.dtype, count=len(x))
    92.9 ms ± 2.86 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

그러나 이 추상화는 복합 표현식을 계산할 때 효율성이 떨어질 수 있습니다. 예를 들어 다음 표현식을 고려하십시오.
```python 
mask = (x > 0.5) & (y < 0.5)
```
그러나 이 추상화는 복합 표현식을 계산할 때 효율성이 떨어질 수 있습니다. 예를 들어 다음 표현식을 고려하십시오.
```python 
tmp1 = (x > 0.5)
tmp2 = (y < 0.5)
mask = tmp1 & tmp2
```

즉, 모든 중간 단계는 메모리에 명시적으로 할당됩니다. x 및 y 배열이 매우 큰 경우 상당한 메모리 및 계산 오버헤드가 발생할 수 있습니다. Numexpr 라이브러리는 전체 중간 배열을 할당할 필요 없이 요소별로 이러한 유형의 복합 표현식을 계산할 수 있는 기능을 제공합니다. Numexpr 문서에 더 자세한 내용이 있지만 현재로서는 라이브러리가 계산하려는 NumPy 스타일 표현식을 제공하는 문자열을 허용한다고 말하는 것으로 충분합니다.

```python 
import numexpr
mask_numexpr = numexpr.evaluate('(x > 0.5) & (y < 0.5)')
np.allclose(mask, mask_numexpr)

```

여기서의 이점은 Numexpr이 전체 크기의 임시 배열을 사용하지 않는 방식으로 표현식을 평가하므로 특히 큰 배열의 경우 NumPy보다 훨씬 더 효율적일 수 있다는 것입니다.
여기서 논의할 Pandas의 ``eval()`` 및 ``query()`` 도구는 개념적으로 유사하며 Numexpr 패키지에 의존합니다.

## ``pandas.eval()`` for Efficient Operations 
Pandas의 ``eval()`` 함수는 문자열 표현식을 사용하여 ``DataFrame``을 사용하여 연산을 효율적으로 계산합니다.
예를 들어 다음 ``DataFrame``을 고려하십시오.

```python 
import pandas as pd
nrows, ncols = 100000, 100
rng = np.random.RandomState(42)
# 임의의 대이터 프레임 4개 생성 
df1, df2, df3, df4 = (pd.DataFrame(rng.rand(nrows, ncols))
                      for i in range(4))

%timeit df1 + df2 + df3 + df4
    20.8 ms ± 186 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
%timeit pd.eval('df1 + df2 + df3 + df4')
    11.7 ms ± 229 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

np.allclose(df1 + df2 + df3 + df4,pd.eval('df1 + df2 + df3 + df4'))
    True

```

이 표현식의 eval() 버전은 동일한 결과를 제공하면서 약 50% 더 빠르고(훨씬 적은 메모리를 사용합니다):

### Operations supported by ``pd.eval()`` 
Pandas v0.16부터 ``pd.eval()``은 광범위한 작업을 지원합니다.
이를 시연하기 위해 다음 정수 ``DataFrame``을 사용합니다
```python 
df1, df2, df3, df4, df5 = (pd.DataFrame(rng.randint(0, 1000, (100, 3)))
                           for i in range(5))

```

#### Arithmetic operators (산술연산자)
``pd.eval()``은 모든 산술 연산자를 지원합니다. 예:
```python 
result1 = -df1 * df2 / (df3 + df4) - df5
result2 = pd.eval('-df1 * df2 / (df3 + df4) - df5')
np.allclose(result1, result2)
    True
```

#### Comparison operators (비교연산자)
``pd.eval()``은 연결된 표현식을 포함한 모든 비교 연산자를 지원합니다
```python
result1 = (df1 < df2) & (df2 <= df3) & (df3 != df4)
result2 = pd.eval('df1 < df2 <= df3 != df4')
np.allclose(result1, result2)
    True
```

#### Bitwise operators (논리연산자)
``pd.eval()``은 ``&`` 및 ``|`` ``and`` ``or``비트 연산자를 지원합니다.
```python 
result1 = (df1 < 0.5) & (df2 < 0.5) | (df3 < df4)
result2 = pd.eval('(df1 < 0.5) & (df2 < 0.5) | (df3 < df4)')
np.allclose(result1, result2)
    True

result3 = pd.eval('(df1 < 0.5) and (df2 < 0.5) or (df3 < df4)')
np.allclose(result1, result3)
    True

```

####  Object attributes and indices (객체 속성 및 인덱스)
``pd.eval()``은 ``obj.attr`` 구문을 통해 객체 속성에 대한 액세스를 지원하고 ``obj[index]`` 구문을 통해 색인을 지원합니다.

```python 
result1 = df2.T[0] + df3.iloc[1]
result2 = pd.eval('df2.T[0] + df3.iloc[1]')
np.allclose(result1, result2)
    True
```

### Other operations 
함수 호출, 조건문, 루프 및 기타 관련 구문과 같은 기타 작업은 현재 pd.eval()에서 구현되지 않습니다. 이러한 더 복잡한 유형의 표현식을 실행하려면 Numexpr 라이브러리 자체를 사용할 수 있습니다.

## ``DataFrame.eval()`` for Column-Wise Operations 
Pandas에 최상위 ``pd.eval()`` 함수가 있는 것처럼 ``DataFrame``에는 유사한 방식으로 작동하는 ``eval()`` 메서드가 있습니다.
``eval()`` 메서드의 이점은 열을 *이름으로* 참조할 수 있다는 것입니다.
이 레이블이 지정된 배열을 예로 사용합니다.

```python 
df = pd.DataFrame(rng.rand(1000, 3), columns=['A', 'B', 'C'])
df.head()
    |   | A        | B        | C        |
    |---|----------|----------|----------|
    | 0 | 0.375506 | 0.406939 | 0.069938 |
    | 1 | 0.069087 | 0.235615 | 0.154374 |
    | 2 | 0.677945 | 0.433839 | 0.652324 |
    | 3 | 0.264038 | 0.808055 | 0.347197 |
    | 4 | 0.589161 | 0.252418 | 0.557789 |

```
위와 같이 pd.eval()을 사용하여 다음과 같이 세 개의 열로 표현식을 계산할 수 있습니다.
```python 
result1 = (df['A'] + df['B']) / (df['C'] - 1)
result2 = pd.eval("(df.A + df.B) / (df.C - 1)")
np.allclose(result1, result2)
    True
```

DataFrame.eval() 메서드를 사용하면 열이 있는 표현식을 훨씬 더 간결하게 평가할 수 있습니다.
```python 
result3 = df.eval('(A + B) / (C - 1)')
np.allclose(result1, result3)
    True
```

### Assignment in DataFrame.eval() 
192 / 5000
번역 결과
방금 설명한 옵션 외에도 ``DataFrame.eval()``을 사용하면 모든 열에 할당할 수 있습니다.
``'A'``, ``'B'`` 및 ``'C'`` 열이 있는 이전의 ``DataFrame``을 사용하겠습니다. 

df.eval()을 사용하여 새 열 'D'를 만들고 여기에 다른 열에서 계산된 값을 할당할 수 있습니다. 

```python 
df.head()
    |   | A        | B        | C        |
    |---|----------|----------|----------|
    | 0 | 0.375506 | 0.406939 | 0.069938 |
    | 1 | 0.069087 | 0.235615 | 0.154374 |
    | 2 | 0.677945 | 0.433839 | 0.652324 |
    | 3 | 0.264038 | 0.808055 | 0.347197 |
    | 4 | 0.589161 | 0.252418 | 0.557789 |

df.eval('D = (A + B) / C', inplace=True)
df.head()
    |   | A        | B        | C        | D        |
    |---|----------|----------|----------|----------|
    | 0 | 0.375506 | 0.406939 | 0.069938 | 11.18762 |
    | 1 | 0.069087 | 0.235615 | 0.154374 | 1.973796 |
    | 2 | 0.677945 | 0.433839 | 0.652324 | 1.704344 |
    | 3 | 0.264038 | 0.808055 | 0.347197 | 3.087857 |
    | 4 | 0.589161 | 0.252418 | 0.557789 | 1.508776 |
```

같은 방식으로 기존 열을 수정할 수 있습니다.
```python 
df.eval('D = (A - B) / C', inplace=True)
df.head()
    |   | A        | B        | C        | D         |
    |---|----------|----------|----------|-----------|
    | 0 | 0.375506 | 0.406939 | 0.069938 | -0.449425 |
    | 1 | 0.069087 | 0.235615 | 0.154374 | -1.078728 |
    | 2 | 0.677945 | 0.433839 | 0.652324 | 0.374209  |
    | 3 | 0.264038 | 0.808055 | 0.347197 | -1.566886 |
    | 4 | 0.589161 | 0.252418 | 0.557789 | 0.603708  |

```

### Local variables in DataFrame.eval() 
``DataFrame.eval()`` 메서드는 로컬 Python 변수와 함께 작동할 수 있는 추가 구문을 지원합니다.
다음을 고려하세요:

```python 
result1 = df[(df.A < 0.5) & (df.B < 0.5)]
result2 = pd.eval('df[(df.A < 0.5) & (df.B < 0.5)]')
np.allclose(result1, result2)
    True
```
여기서 @ 문자는 열 이름이 아닌 변수 이름을 표시하고 두 "네임스페이스", 즉 열의 네임스페이스와 Python 개체의 네임스페이스를 포함하는 표현식을 효율적으로 평가할 수 있도록 합니다. 이 @ 문자는 pandas.eval() 함수가 아닌 DataFrame.eval() 메서드에서만 지원됩니다. pandas.eval() 함수는 하나(Python) 네임스페이스에만 액세스할 수 있기 때문입니다.

## DataFrame.query() Method 
``DataFrame``에는 평가된 문자열을 기반으로 하는 ``query()`` 메서드라는 또 다른 메서드가 있습니다.
다음을 고려하세요:

```python 
result1 = df[(df.A < 0.5) & (df.B < 0.5)]
result2 = pd.eval('df[(df.A < 0.5) & (df.B < 0.5)]')
np.allclose(result1, result2)   
    True
```
DataFrame.eval()에 대한 논의에서 사용된 예와 같이 이것은 DataFrame의 열을 포함하는 표현식입니다. 그러나 DataFrame.eval() 구문을 사용하여 표현할 수 없습니다! 대신 이 유형의 필터링 작업에 대해 query() 메서드를 사용할 수 있습니다.

```python 
result2 = df.query('A < 0.5 and B < 0.5')
np.allclose(result1, result2)
```

마스킹 표현식에 비해 계산이 더 효율적일 뿐만 아니라 훨씬 더 읽기 쉽고 이해하기 쉽습니다. query() 메서드는 지역 변수를 표시하기 위해 @ 플래그도 허용합니다.
```python 
Cmean = df['C'].mean()
result1 = df[(df.A < Cmean) & (df.B < Cmean)]
result2 = df.query('A < @Cmean and B < @Cmean')
np.allclose(result1, result2)
    True
```

## Performance: When to Use These Functions 
이러한 기능을 사용할지 여부를 고려할 때 **계산 시간** 및 **메모리 사용**의 두 가지 고려 사항이 있습니다.
메모리 사용은 가장 예측 가능한 측면입니다. 이미 언급했듯이 NumPy 배열 또는 Pandas ``DataFrame``과 관련된 모든 복합 표현식은 임시 배열을 암시적으로 생성합니다.
예를 들면 다음과 같습니다.


임시 DataFrame의 크기가 사용 가능한 시스템 메모리(일반적으로 수 기가바이트)에 비해 상당한 경우 eval() 또는 query() 표현식을 사용하는 것이 좋습니다. 다음을 사용하여 배열의 대략적인 크기를 바이트 단위로 확인할 수 있습니다.

```python 

x = df[(df.A < 0.5) & (df.B < 0.5)]
# 아래와 동일한 순서대로 작동된다. 
# tmp1 = df.A < 0.5
# tmp2 = df.B < 0.5
# tmp3 = tmp1 & tmp2
# x = df[tmp3] 

```

성능 측면에서 eval()은 시스템 메모리를 최대로 사용하지 않는 경우에도 더 빠를 수 있습니다. 문제는 임시 DataFrame이 시스템의 L1 또는 L2 CPU 캐시 크기(2016년에는 일반적으로 몇 메가바이트)와 비교되는 방식입니다. 그것들이 훨씬 더 크다면, eval()은 다른 메모리 캐시 사이에서 잠재적으로 느린 값 이동을 피할 수 있습니다. 실제로, 나는 전통적인 방법과 eval/query 방법 사이의 계산 시간의 차이가 일반적으로 중요하지 않다는 것을 알게 되었습니다. eval/query의 이점은 주로 저장된 메모리에 있으며 때로는 더 깔끔한 구문을 제공합니다.

여기에서 eval() 및 query()에 대한 대부분의 세부 정보를 다뤘습니다. 이에 대한 자세한 내용은 Pandas 설명서를 참조하세요. 특히 이러한 쿼리를 실행하기 위해 다른 파서와 엔진을 지정할 수 있습니다. 이에 대한 자세한 내용은 "성능 향상" 섹션의 토론을 참조하십시오.

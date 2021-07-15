---
layout: default
title: Dimension Reduction
parent: Machine learning
nav_order: 5
has_children: false
permalink: /docs/ml/dimensionReduction
use_math: true
---

# ML regression
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 백터의 내적 
원점에서 시작하는 두백터$\[ \vec{\mathbf{a}} , \vec{\mathbf{b}}  \]$에 내적은 공식은  아래와 같다.

$\vec{\mathbf{a}} \cdot \vec{\mathbf{b}} \cdot \cos \theta (0 \leq \theta \pi )$ 

내적에 합이 0 이 되는 상황은 아래와 같다. 당연한 소리겠지만 곱셈공식이 적용된 항목에서 변수 하나라도 0이 되는 순가 결과 스칼라는 0이다. 

* $\vec{\mathbf{a}}$ = 0 or  $\vec{\mathbf{b}}$ = 0 일때 내적은 0 이다. 
* $\vec{\mathbf{a}} \cdot \vec{\mathbf{a}} = \|\vec{\mathbf{a}}\| * \|\vec{\mathbf{a}}\| * \cos^{\theta(0)} = \|\vec{\mathbf{a}}^2\|$ 

<!--
$\| \vec{\mathbf{b}} \|^2 $ (두 백터 간에 내적 OR 한 백터의 제곱)기하학적 의미는 무엇일까?
백터($\vec{\mathbf{a}}$)를 백터($\vec{\mathbf{b}}$)로 정사형시킨 백터로 정사형 한 백터$\vec{\mathbf{Oh}}$를  $\cdot$을 통한 결과를 실수를 반환 하며 실수는 더백터에 거리를 말한다.
그럼 $\frac{\| \vec{\mathbf{a}} \| * \| \vec{\mathbf{b}} \| }{\| \vec{\mathbf{b}} \|^2} $ 는 백터 $\vec{\mathbf{b}}$를 기준으로 얼마나 유사 한지를 알수 있다. 
-->

## 공분산 행렬의 수학적 의미 
고유 벡터(eigenvector)의 의미를 잘 생각해보면, 고유 벡터는 그 행렬이 벡터에 작용하는 주축(principal axis)의 방향을 나타내므로 공분산 행렬의 고유 벡터는 데이터가 어떤 방향으로 분산되어 있는지를 나타내준다고 할 수 있다.

$\sum = \frac{X^{T}X}{n} = \frac{1}{n}\begin{pmatrix} dot(X_1 , X_1) & dot(X_1 , X_1) & ... & dot(X_1 , X_d) \\\ dot(X_2 , X_1) & dot(X_2 , X_1) & ... & dot(X_2 , X_d) \\\ ... & ... & ... & ... \\\ dot(X_d , X_1) & dot(X_d , X_2) & ... & dot(X_d , X_d) \\\ \end{pmatrix}$  

일단은 복잡한 수식적인 전개 이전에 공분산 행렬을 기하학적으로 파악해보도록 하자. 행렬이란 선형 변환이고 하나의 벡터 공간을 선형적으로 다른 벡터 공간으로 mapping 하는 기능을 가진다.백터($\vec{\mathbf{a}}$) 에 선형변환이라 하면 $\vec{\mathbf{a}}$에 Matrix를 통과하게 되면 다른 백터 공간에 Mapping 하게 된다. 

즉, 조금 다르게 말하면 우리는 지금의 데이터의 분포에 대해 “원래의 원의 형태로 주어졌던 데이터가 선형변환에 의해 변환된 결과로써 보자”라는 관점에서 데이터를 보고자 한다.

공분산 관련 수식에서 확인해야 할내용은 선형적 특징과 백터에 내적이 있다. 
선형변환이란 통과한 Matrix는 ``함수``로써 동작하게 되고 백터 내적에서 공부한 내용처럼 백터를 $\| \vec{\mathbf{b}} \|^2 $을통해 유사도를 가질수 측정할수 있다. 

$X^{t}X$의 i행 j열의 성분이 $(X^{t}X)_ij$는 d개의 feature 중 i번째 feature와 j번째 feature가 얼마나 닮았는지를 모든 사람으로부터 값을 얻어서 내적 연산을 취함으로써 확인시켜준다. 

### 공분산 속성 = 픽치간 선형관계 
백터 속성 값이 1 or -1 에 가까울수록 두 Feature는 상관계계가 크며 0 에 가까울수록 상관관계가 없어 진다. 



## 고유값 , 고유백터 정의 
정방행렬 A에 대하여 다음이 성립하는 0이 아닌 백터 x가 존재할 때 

$ \vec{\mathbf{a}} \cdot x = \lambda x (상수 \lambda)$

$\begin{pmatrix} dot(a_{11}) & ... & dot(a_{1n}) \\\ \vdots & \ddots & \vdots \\\ dot(a_{n1}) & ... & dot(a_{1n}) \\\ \end{pmatrix} \begin{pmatrix} x_1 \\\ \vdots \\\ x_n \end{pmatrix}  = \lambda \begin{pmatrix} x_1 \\\ \vdots \\\ x_n  \end{pmatrix} $

상수 $\lambda$ 를 행렬 A의 고유값(eigenvalue) x를 이에 대응하는 고유백터(eigenvector) 라고 함

[![EigenvalueAndEigenvector](https://t1.daumcdn.net/cfile/tistory/2753B53C5708DC5021)](https://t1.daumcdn.net)

고유값(eigenvalue)와 고유벡터(eigenvector)의 기하학적인 의미를 살펴보면, 벡터 x에 대해 n차 정방행렬 A를 곱하는 결과와 상수 λ를 곱하는 결과가 같다는 의미입니다. 즉, 행렬의 곱의 결과가 원래 벡터와 "방향"은 같고, "배율"만 상수 λ 만큼만 비례해서 변했다는 의미입니다.  이게 고유값(eigenvalue)과 고유벡터(eigenvector)가 무척 중요한 이유입니다.  행렬과 벡터 곱을 했더니 "방향"도 바뀌고 "크기(배율)"도 모두 바뀌는 것과, "방향"은 그대로 있고 "크기(배율)"만 바뀌는 것 중에 뭐가 연산이 간단할 지 생각해보시면 됩니다.  

[![MagnificationAndDirection](https://t1.daumcdn.net/cfile/tistory/2666DF4B5708FD4318)](https://t1.daumcdn.net)

REF : https://rfriend.tistory.com/181?category=606751 [R, Python 분석과 프로그래밍의 친구 (by R Friend)]

## 차원의 저주 
차원이 증가에 따른 직면하는 문제는 단순하게 생각하면 (Sparse Vector)가 된다는 것이다. 

* 데이터 포인트들간 거리가 크게 늘어남 
* 데이터가 회소화(Sparse)됨 

## 피처 선택과 피처 추출 

### 피처 선택 Feature Selection 
특정 피처에 종속성이 강한 불필요한 피처는 아예 제거하고, 데이터의 특징을 잘 나타내는 주요 피처만 선택하는 것입니다.[상관관계가 높은 픽처중 대표 픽처만 선택]

### 피처 추출 
피처(특성) 추출은 기존 피처를 저차원의 중요 피처로 압축해서 추출하는 것입니다. 이렇게 새롭게 추출된 중요 특성은 기존의 피처를 반영해 압축된 것이지만 새로운 피처로 추출하는 것입니다. [잘요약해서 새로운 픽처로 생성]

## 차원 축소의 장점 
차원 축소를 통해 픽처를 작은 수의 피처들로 축소한다면 

* 학습 데이터 크기를 줄여서 학습 시간 절약 
* 불필요한 피처들을 줄여서 모델 성능 향상에 기여 
* 다차원 데이터를 3차원 이하의 차원 축소를 통해서 시각적으로 보다 쉽게 데이터 패턴 인지 

## 올바른 차원축소 
원본 데이터의 정보를 최대한으로 유지한 채로 차원 축소를 수행할 것이다. 차춴 축소는 단순히 데이터의 압출을 의미하는 것이 아니라 더 중요한 의미는 차원 축소를 통해 좀 더 데이터를 잘 설명할수 있는 잠재적(Latent)인 요소를 추출하는 데에 있습니다. 

## algorithm 
설명은 아래 링크로 대처한다.

[PCA Link button](https://roytravel.tistory.com/64){: .btn .btn-green }
[LDA Link button](https://ratsgo.github.io/machine%20learning/2017/03/21/LDA/){: .btn .btn-green }
[SVD Link button](https://ratsgo.github.io/from%20frequency%20to%20semantics/2017/04/06/pcasvdlsa/){: .btn .btn-green }
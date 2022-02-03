---
layout: default
title: Exponential Funtion And Log Function
parent: Deep Learning
nav_order: 20
has_children: true
permalink: /docs/deeplearning/ExponentialAndLog
use_math: true
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
## ExponentialFuntion (지수함수)
교환 결합 분배 법칙 관련 수식만 정의 해보자. 

### 지수

<div class="code-example" markdown="1">
<b>지수 기본 법칙</b>

 - $a^0=1$
 - $a^{-1} = \frac{1}{a^n}$
 - $\sqrt[n]{a} = a^{\frac{1}{n}}$
</div>


<div class="code-example" markdown="1">
<b>지수 연산 법칙</b>

 - $a^n \times a^m = a^{n+m}$
 - $(a^{m})^n = a^{mn}$
 - $(ab)^n = a^n \times a^m$
 - $\left(\frac{a}{b}\right)^n = \frac{a^n}{b^n}$
</div>

<div class="code-example" markdown="1">
<b>법칙의 응용</b>

 - $a^n \div a^m = a^{n-m}$
 - $\sqrt[m]{a^n} = (a^n)^{\frac{1}{m}} = a^{\frac{n}{m}}$
</div>

## LogFuntion 

### 로그 함수 
 $y=\log x$

### 지수와 로그의 관계 (자연상수)
$y = e^x , \log y = x $

<div class="code-example" markdown="1">
<b>로그 기본 법칙</b>

 - $\log 1 = 0 $
 - $\log 0 = -\propto$
</div>

<div class="code-example" markdown="1">
<b>로그 연산 법칙</b>

 - $\log a + \log b = \log ab $
 - $\log a - \log b = \log \frac{a}{b}$
 - $\log a^b = b\log {a}$
</div>


### 음수지수 
음수지수 관련 이미지를 통해 역수가 어떻게 나오는지 도식화 해본다. 

| -       | -       | -     | -     | -     | -   | -     | - | -     | - | -     | -   | -     | -     |
|---------|---------|-------|-------|-------|-----|-------|---|-------|---|-------|-----|-------|-------|
| 역방향 곱   |         | ←*1/a |       | ←*1/a |     | ←*1/a |   | ←*1/a |   | ←*1/a |     | ←*1/a |       |
| 지수      | -3      |       | -2    |       | -1  |       | 0 |       | 1 |       | 2   |       | 3     |
| 정방향 곱   |         | → *a   |       | → *a   |     | → *a   |   | → *a   |   | → *a   |     | → *a   |       |
| Result  | 1/a·a·a |       | 1/a·a |       | 1/a |       | 1 |       | a |       | a*a |       | a*a*a |




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

### 거듭제곱
어떤 수 $a$를 $n$번 곱한 것을 $a^n$ 으로 나타내고, $a, a^2, a^3, ..., a^n,... $ 을 통틀어 a의 거듭제곰이라고 한다. 
이때 $a^n$에서 a를 거듭제곱의 밑, n을 거듭제곱의 지수라고 한다. 

<div class="code-example" markdown="1">
<b>지수가 자연수일 때의 지수 법칙  $a , b$가 실수이고 $m,n$이 자연수 일 때, </b>

 - $a^{m}a^{n} = a^{m+n}$
 - $(a^m)^n = a^{mn}$   
 - $(ab)^{n} = a^{n}b^{m}$
 - $(\frac{a}{b})^n = \frac{a^n}{b^n}  (b  \neq 0 ) $
 - $a^m\div a^n = \begin{cases} a^{m-n} &(m>n)\\\ 1 &(m=n)\\\ \frac{1}{a^{n-m}} &(m<n) \end{cases}$
</div>

### 거듭제곱근 
제곱하여 실수 $a$ 가 되는 수를 $a$의 제곱근이라 하고, 세제곱하여 실수 a가 되는 수를 a의 세제곱근이라고 한다. 
 일반적으로 n이 2이상인 자연수일 때, n제곱하여 실수 a가 되는 수, 즉 방정식 $x^{n}=a$의 근 $x$를 $a$의 $n$제곱근이라고 한다. 
$a$의 제곱근, 세제곱근, 네제곱근, ...을 통틀어 $a$의 거듭제곱근이라고 한다. 

<div class="code-example" markdown="1">
<b>거듭제곱근 성질 a>0, b>0이고 m,n이 2이상인 자연수 일 때</b>

 - $\sqrt[n]{a} \sqrt[n]{b} = \sqrt[n]{ab}$
 - $\frac{\sqrt[n]{a}}{\sqrt[n]{b}} = \sqrt[n]{a \over b}$
 - $(\sqrt[n]{a})^m = \sqrt[n]{a^m}$
 - $\sqrt[m]{\sqrt[n]{a}} = \sqrt[mn]{a}$
</div>

<div class="code-example" markdown="1">
<b>0 또는 음의 정수인 지수 </b>

 - $a \ne 0$이고 $n$이 양의 정수일 때, $a^0 = 1, a^{-n} = a\over a^{n}$ 
</div>


<div class="code-example" markdown="1">
<b>지수가 정수일 때의 지수 법칙  $a\ne0, b\ne0$ 이고 $m,n$이 정수일 때,  </b>

 - $a^{m}a^{n} = a^{m+n}$
 - $a^m \div a^n = a^{m-n}$
 - $(a^m)^n = a^{nm}$
 - $(ab)^n = a^nb^n$ 
</div>

<div class="code-example" markdown="1">
<b>유리수인 지수 $a>0$ 이고 $m,n (n>=2)$ 이 정수일 때,  </b>

 - $a^{\frac{m}{n}} = \sqrt[n]{m}$ , $a^{\frac{1}{n}} = =sqrt[n]{a}$
</div>

## Log Function 

### Log 
$a>0, a\ne1 일때, 양수 N에 대하여 a^x=N을 만족시키는 실수 x는 오직 하나 존재한다. 이 수 x를 \log_{x} N 으로 나태내고, a를 밑으로 하는 N의 로그라고 한다. 
이때 N을 \log_{a} N의 진수라고 한다. $

<div class="code-example" markdown="1">
<b>Log  </b>

 $a>0 , a\ne1, N>0일 때,$ 
 $a^x=N \Leftrightarrow x=\log_{a} N $
</div>













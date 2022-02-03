---
layout: default
title: DataSet
parent: Deep Learning
nav_order: 25
has_children: true
permalink: /docs/deeplearning/temp
use_math: true
---

# Typography
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
## DataSet
- 가상의 함수(ground-truth)를 통해 데이터 쌍 $(x,y)$을 수집해 데이터셋을 만들고,<br>
  우리의 함수(e.g. 신경망)가 데이터를 통해 가상의 함수를 모방하도록 한다. <br>
$D =  \left\[(x_i , y_i) \right\]^n_{i=1}$ <br>

- 이때 $x$와 $y$는 각각 n차원과 m차원의 백터로 표현될 수 있다. <br>
$x \in \mathbb{R}^n, y \in \mathbb{R}^m  $

## Tensor 
TODO : 텐서 정의 

3차원 k n m 구성시 
$x \in \mathbb{R}^{k \times n \times m} \Leftrightarrow \left\vert X\right\vert = (k \times n \times m)$

2차원 n m 구성시 
$x \in \mathbb{R}^{n \times m} \Leftrightarrow \left\vert X\right\vert = (n \times m)$

백터 구성시 
$\left\vert X\right\vert = (n,)$ 
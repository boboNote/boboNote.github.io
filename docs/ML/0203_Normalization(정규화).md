---
layout: default
title: normalization[정규화]
parent: Scikit learn
grand_parent: Machine learning
permalink: /docs/ml/scikit/normalization
nav_order: 1003
use_math: true
---

# Noramlization(정규화)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

정규화는 feature의 스케일이 심하게 차이가 나는 경우 문제가 된다. 

예를 들어 ‘주택’에 관한 정보가 담긴 데이터를 생각해보자. 그 안에 feature로 방의 개수(개), 얼마나 오래 전에 지어졌는지(년) 같은 것들이 포함될 수 있을 거다. 그리고 여기서 머신러닝 알고리즘을 통해 어느 집이 가장 적합한지 예측을 시도한다고 해보자. 그러면 각 데이터 포인트를 비교할 때 더 큰 스케일을 가진 feature, 즉 얼마나 오래 전에 지어졌는지(년)에 따라 그 데이터가 완전히 좌지우지 되는 꼴이다.


TODO : Image capture 

모든 데이터 포인트가 동일한 정도의 스케일(중요도)로 반영되도록 해주는 게 정규화(Normalization)의 목표


## 정규화 하는 두 가지 방법 
 * Min-Max Normalization (최소 - 최대 정규화)
 * Z-score Normalization ( Z-점수 정규화)

각각의 장단점이 있기 때문에 정확히 이해하고 언제 어떤 방식으로 정규화를 할지 결정할 수 있어야 한다. 

## Min-Max Normalization 
최소-최대 정규화는 데이터를 정규화하는 가장 일반적인 방법이다. 모든 feature에 대해 각각의 최소값 0, 최대값 1로, 그리고 다른 값들은 0과 1 사이의 값으로 변환하는 거다.
(X-Min) / (Max-Min)

``` python
def min_max_normalize(lst):
    normalized = []
    
    for value in lst:
        normalized_num = (value - min(lst)) / (max(lst) - min(lst))
        normalized.append(normalized_num)
    
    return normalized
```
여기서 중요한것은 이상치이다. Min-Max 단점은 이상치로 인한 전체 대이터에 영향이 미친다는 것이다. 

## Z-Score Normalization (Z-점수 정규화)
Z-점수 정규화는 이상치(outlier) 문제를 피하는 데이터 정규화 전략이다.
X라는 값을 Z-점수로 바꿔주는 식은 아래와 같다. (사실 고등학교 수학 시간에 정규분포, 표준점수 등의 개념으로 다루는 내용이다.)

(X - 평균) / 표준편차
만약 feature의 값이 평균과 일치하면 0으로 정규화되겠지만, 평균보다 작으면 음수, 평균보다 크면 양수로 나타난다. 이 때 계산되는 음수와 양수의 크기는 그 feature의 표준편차에 의해 결정되는 거다. 그래서 만약 데이터의 표준편차가 크면(값이 넓게 퍼져있으면) 정규화되는 값이 0에 가까워진다.

리스트에 포함된 값들을 모두 Z-점수로 정규화 하는 방법을 파이썬의 함수로 작성하면 이렇게 될 거다.
``` python
def z_score_normalize(lst):
    normalized = []
    for value in lst:
        normalized_num = (value - np.mean(lst)) / np.std(lst)
        normalized.append(normalized_num)
    return normalized
```


## 평균 분산 표준편차 

* 평균 (mean, average) : 주어진 수의 합을 측정개수로 나눈 값으로, 대표값 중 하나 
* 분산(Variance) : 변량들이 퍼져있는 정도, 분산이 크면 들죽날죽 불안정하다는 의미 
* 표준편차 : 분산은 수치가 너무 커서, 제곱근으로 적당하게 줄인 값 

5명의 키를 측정하여, 평균과 분산 그리고 표준편차를 구해보자. 

| Start                 | First | Second| third | fourth| fivth |
|:-------------         |:------:|:------:|:------:|:------:|:------:|
| 변량                   |  175  | 177   |  179  | 181   | 183   |
| 평귱(변화량 합계/인원수)   |  $179  = \frac{175 + 177 + 179 + 181 + 183}{5} $        |||||
| 편차                   | 175-179 = -4   | 177-179 = -2  | 179-179 = 0 | 181-179 = 2 | 183-179 = 3 |
| 편차제곱               | 16      | 4   |0 |4| 16|
| 분산                   | $8 = \frac{16+4+0+4+16}{5}$   ||||| 
| 표준편차                | $2.828 = \sqrt{8} $ |||||


## 정규분포 와 표준정규분포
* 정규 분포 : 평균과 분산을 이용한 그래프
* 표준 정규 분포 : 평균이 0이고 표준 편차가 1 인 종모양  ``확률밀도함수`` 를 이용해 통계계산이 가능하다. 

<div class="code-example" markdown="1">
Z-Score Normalization을 표준 정규 분포로 변경하는것이다. 
표준 정규 분포로 변경한다는 의미는 무엇일까? 직관적으로 그래프를 보고 이야기 해봐야 할부분이다. 
그래프 상으로 보면 x 축은 수평이동 하고 y축 높이를 변경하지는 않는다. 
즉 평균이 0으로 수평이동하고 분산(데이터 간극)을 1로 마춰준 과정이다. 

수식 

$ \mathrm{Z}=\frac{x-\upsilon}{\sigma} $


![표준 정규 분포](../img/normalization_1.png)

</div>

| 표준편차       | 데이터 포함 한 범주          |
|:------------:|:-------------------------:|
| $ \pm 1 $    |  68%  |
| $ \pm 2 $    |  95%  |
| $ \pm 3 $    |  99%  |




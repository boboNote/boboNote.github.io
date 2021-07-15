---
layout: default
title: Regularization
parent: regression
grand_parent: Machine learning
permalink: /docs/ml/regression/Regularization
nav_order: 5003
use_math: true
---

# Regulation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Regularization
선형 모델의 비용 함수는 RSS를 최소화하는, 즉 실제값과 예측값의 차이를 최소화하는 것만 고려했다. 이것은 학습데이터에 지나치게 맞추게 되고, 회귀 계수 또한 입력된 Feature에 상응해 증가하게 된다. 이렇게 될 경우 변동성이 오히려 심해져서 테스트 데이터 셋에서는 예측 성능이 저하되기 쉽다. 이를 반영해 비용 함수는 학습 데이터의 전차 오류 값을 최소로 하는 RSS최소화 방법과 과적합을 방지하기 위해 회귀 계수 값이 커지지 않도록 하는 방법이 서로 균형을 이뤄야 한다. 

``회귀 계수가 커지면 예측값에 편차가 크게 발생한다. 회귀 계수를 줄이는것 또한 비용함수로 선정이 핵심``


$비용함수목표 = MIN(RSS(W) + \alpha*\|\|W\|\|_2^2 )$

|조건|결과|
|:-|:-|
|Alpha값을 0에 가까우면|RSS만을 대상으로 최적화 진행|
|Alpha값을 무한데에 가까우면| 회귀 계수 편차를 죽이는 최적화 진행 |



### L2 Regularization Ridge
$alpha*\|\|W\|\|_2^2$을 이용하여 회귀 계수에 편차를 계산하여 RSS를 도출 해낸다.

### L1 Regularization Lasso
$alpha*\|\|W\|\|_1$을 이용하여 절대값에 대해 패널티를 부여 한다. 불필요한 회귀 계수를 0으로 만들어 버린다. ``즉 0으로 만든다는것은 회귀값을 제거 한다는것이다.(픽처 샐랙션)``

### ElasticNet Regression
ElasticNet에서 주목할 부분은 비용함수 산출 공식이다. 엘라스틱넷은 라쏘 회귀가 서로 상관관계가 높은 피처들의 경우에 이들 중에서 중요 피처만을 선택하고 다른 피처들은 모두 회귀 계수를 0으로 만드는 성향이 강해다. 특히 이러한 특징으로 인해 alpha 값에 따라 회귀 계수의 값이 급격히 변동할 수도 있는데, 엘라스틱넷 회귀는 이를 완화하기 위해 L2 구제를 라쏘 회귀에 추가한 것이다. 반대로 엘라스틱넷 회귀의 단점은 L1과 L2규제가 결합된 규제로 인해 수행시간이 상대적으로 오래걸린다는 것이다. 

엘라스틱넷 회귀는 사이킷런 ElasticNet 클래스를 통해 구현할 수 있으며 해당 클래스의 주요 파라미터인 alpha와 l1_ratio중 alpha는 Ridge와 Lasso 클래스에서 사용되는 alpha 값과는 다르다. 엘라스틱넷 규제는 a * L1 + b * L2로 정의될 수 있으며, 이 때 a는 L1 규제의 alpha값, b는 L2 규제의 alpha 값이다. 따라서 ElasticNet 클래스의 alpha 파라미터 값은 a + b 값이다. ElasticNet 클래스의 l1_ratio 파라미터 값은 a / (a + b)이다. l1_ratio가 0이면 a가 0이므로 L2 규제와 동일하고, l1_ratio가 1이면 b가 0이므로 L1 규제와 동일하다.


$RSS(W)+ \alpha*\|\|W\|\|_2^2$ 

$+\alpha*\|\|W\|\|_1$

ElasticNet 주요 파라미터 


|Name|Desc|
|:-|:-|
|alpha| L2 $\alpha$ + L1 $\alpha$ 값에 총합입니다.|
|l1_ratio| alpha 값중 L1 규제 값입니다. Default 0.5  |


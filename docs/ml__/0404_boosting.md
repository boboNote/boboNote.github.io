---
layout: default
title: boosting
parent: classfication
grand_parent: Machine learning
permalink: /docs/ml/classification/gradientboosting
nav_order: 4004
use_math: true
---


# boosting
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## AdaBoost-Boosting
이번엔 부스팅이라는 앙상블의 유형을 알아보겠습니다. 

부스팅 알고리즘은 여러개의 약한 학습기(week learner)를 순차적으로 학습-예측하면서 잘못 예측한 데이터에 가중치 부여를 통해 오류를 개선해 나가면서 학습하는 방식입니다. 부스틴의 대표적인 구현으로 AdaBoost와 그레디언트부스트(Gradient Boost)가 있습니다. 
에이다 부스팅의 학습-예측 프로세스 그림을 보며 부스팅 방식이 어떻게 학습하는지 대략적으로 살펴 봅시다. 

![decisiontree](../img/04_AdaBoost.png)

다음과 같이 하나의 약한 학습기가 학습-예측을 마치면 잘못 예측한 데이터에 가중치를 부여하면서 다음 약한 학습기가 이를 토대로 학습을 진행합니다. 그리고 이 분류 1,2,3을 예측한 결과는 약한 학습기 하나가 예측한 결과보다 훨씬 정교하게 예측되었음을 알 수 있습니다. 
하지만 이 순차적으로 학습한다는 것에서 알 수 있듯이 다른 앙상블 기법에 비해 부스팅은 학습시간이 오래 걸린다는 단점이 있습니다. 

## GBM-Gradient Boosting Machine
GBM은 에이다부스트와 비슷하나, 가중치 업데이트를 경사 하강법(Gradient Descent)을 이용하는 것이 큰 차이입니다.분류의 결과값을 y 피처를 $X_{1}$,$X_{2}$,...,$X_{n}$
 그리고 피처에 기반한 예측함수를 F(x)라고 하면 오류식 h(x)=y−F(x)가 됩니다.
이 오류식을 최소화하는 방향성을 가지고 반복적으로 가중치 값을 업데이트하는 것이 경사하강법입니다.


### GBM hiper params
사이킷런은 GBM 분류를 위해 GradientBoostingClassifier 클래스를 제공합니다.

| 하이퍼파라미터 | Desc |
|:-----------|:-----|
|loss | 경사 하강법에서 사용할 비용함수를 지정합니다. 기본값은 'deviance'입니다.|
|learning_rate|GBM이 학습을 진행할 때 마다 적용하는 학습률입니다.범주 [0 ~ 1]|
|n_estimators|weak learner 의 개수입니다.|
|subsample|weak learner가 학습에 사용하는 데이터 샘플링 비율입니다.**(overfitting관련 이슈를 막고자 전체 샘플에 수를 줄일수 있다.)**|

learning rate는 0~1사이의 값으로 지정하며 디폴트 값은 0.1입니다. 이 값을 너무 작게 설정하면 모든 Weak learner가 반복이 완료되어도 최소 오류값을 찾지 못할수도 있습니다. 또 큰 값을 설정하면 최소 오류값을 찾지 못하고 지나쳐 버려서 예측 성능이 떨어지지만 빠른 수행이 가능합니다. 그렇기 때문에 n_estimators 값과 조합하여 보완해 나가며 사용합니다.


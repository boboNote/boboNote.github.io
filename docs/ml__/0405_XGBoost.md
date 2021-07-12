---
layout: default
title: XGBoost_LiteGBM
parent: classfication
grand_parent: Machine learning
permalink: /docs/ml/classification/XGBoost
nav_order: 4005
use_math: true
---


# XGBoost
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## XGBoost (extra Gradient Boost)

주요 장점

* 뛰어난 예측 성능 
* GBM 대비 빠른 수행 시간 
  * CPU 병렬 처리, GPU 지원 
* 다양한 성능 향상 기능 
  * 규제(Regularization) 기능 탑재  Overfitting 방지 
  * Tree Pruning        
* 다양한 편의 기능 
  * 조기 중단(Early Stopping) 
  * 자체 내장된 교차 검증 
  * 결손값 자체 처리 

## scikit-learn XGBoost hyper parameter 

| 사이킷런 Wrapper | DESC |
|:---------------|:-----|
| learning_rate | 0~1 사이의 값을 지정 GBM의 학습률과 같은 파라미터 |
| n_estimators| 사이킷런 앙상블의 n_estimators와 동일 약한 학습기의 개수(반복 수행 회수) |
| min_child_weight | 결정트리의 min_child_leaf와 유사, 과적합 조절용 용
| max_depth | 결정트리의 max_depth와 동일, 트리의 최대 깊이 |
| subsample | GBM의 subsample과 동일, 트리가 커져서 과적합되는 것을 제어하기 위해 데이터를 샘플링하는 비율을 지정합니다. 가능 범주[0 ~ 1] 활용범주[0.5 ~ 1] |
| reg_lambda | L2 규제 적용 값. 기본값은 1임. 값이 클 수록 규제 값이 커짐. 과적합 제어|
| reg_alpha | L1 규제 적용 값. 기본값은 0임. 값이 클수록 규제 값이 커짐  |
| colsample_bytree | GMB의 max_features와 유사합니다. 트리 생성에 필요한 피처(컬럼)을 임의로 샘플링 하는데 사용 됩니다. 매우 많은 피처가 있는 경우 과적합을 조정하는데 적용 합니다.  |
| scale_pos_weight | 특정 값으로 치우친 비대칭한 클래스로 구성된 데이터 세트의 균형을 유지하기 위한 파라미터 입니다. 기본값은 1 |
| gamma | 트리의 리프 노드를 추가적으로 나눌지를 결정할 최소 손실 감소 값입니다. 해당 값보다 큰 손실(loss)이 감소된 경우에 리프 노드를 분리합니다. 값이 클수록 과적합 감소 효과가 있습니다.  |


## Early stopping 

![decisiontree](../img/04_early_stopping.png)

XGBoost는 특정 반복 횟수 만큼 더이상 비용함수가 감소하지 않으면 지정된 반복횟수를 다 완료하지 않고 수행을 종료할 수 있음. 

* early_stopping_rounds: 더이상 비용 평가 지표가 감소하지 않는 최대 반복횟수 
* eval_metric : 반복 수행 시 사용하는 비용 평가 지표. 
* eval_set : 평가를 수행하는 별도의 검증 데이터 세트, 일반적으로 검증 데이터 세트에서 반복저으로 비용 감소 성능 평가 


## LightGBM 
XGBoost 대비 장점
* 더 빠른 학습과 예측 수행 시간 
* 더 작은 메모리 사용량. 
* 카테고리형 피처의 자동 변환과 최적 분할(원-핫 인코딩 등을 사용하지 않고도 카테고리형 피처를 최적으로 변환하고 이에 따른 노드 분할 수행)


## 트리 분할 방식 

![decisiontree](../img/04_leaf_wise.png)

균형 트리분할 방식은 일정합 Depth를 고르게 노드에 분할 하는 방식이다(XGBoost,.. GBM 계열)
리프 중심 트리 분할 방식은 한쪽 노드에 집중을 허용하는 방식이다.(LightGBM)
LightGMB Overfitting으로 성능 향상 ? 

## LightGBM hyper parameter 

| 사이킷런 Wrapper | DESC |
|:---------------|:-----|
| n_estimators| 사이킷런 앙상블의 n_estimators와 동일 약한 학습기의 개수(반복 수행 회수) |
| learning_rate | 0~1 사이의 값을 지정 GBM의 학습률과 같은 파라미터 |
| max_depth | 결정트리의 max_depth와 동일, 트리의 최대 깊이 |
| min_child_samples | 리프 노드가 될 수 있는 최소 데이터 건수(Sample 수) |
| subsample | 트리가 커져서 과적합되는 것을 제어하기 위해 데이터를 샘플링하는 비율을 지정합니다. sub_sampe=0.5로 지정하면 전체 데이터의 절반을 트리를 생성하는 데 사용합니다. |
| colsample_bytree | GMB의 max_features와 유사합니다. 트리 생성에 필요한 피처(컬럼)을 임의로 샘플링 하는 데 사용됩니다. 매우 많은 피처가 있는 경우 과접합을 조정하는 데 적용합니다. |
| reg_lambda | L2 규제 적용 값. 기본값은 1임. 값이 클 수록 규제 값이 커짐. 과적합 제어|
| reg_alpha | L1 규제 적용 값. 기본값은 0임. 값이 클수록 규제 값이 커짐  |
|early_stopping_rounds| 학습 조기 종료를 위한 early stopping interval 값 |
| num_leaves | 최대 리프노드 갯수 |
| min_child_weight | 결정트리의 min_child_leaf와 유사 |

## 하이퍼 파라미터 튜닝방안

num_leaves의 개수를 중심으로 min_child_sampes(min_data_in_leaf), max_depth를
함께 조절하면서 모델의 복잡도를 줄이는 것이 기본 튜닝 방안입니다.

 * num_leaves를 늘리면 정확도가 높아지지만 트리가 깊어지고 과접합되기 쉬움 
 * min_child_samples(min_data_in_leaf)를 크게 설정하면 트리가 깊어지는 것을 방지
 * max_depth는 명시적으로 깊이를 제한. 위의 두 파라미터와 함꼐 과적합을 개선하는데 사용
 
또한, learning_rate을 줄이면서 n_estimator를 크게하는 것은 부스팅에서의 기본적인 튜닝 방안
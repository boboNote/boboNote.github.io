---
layout: default
title: regression
parent: Machine learning
nav_order: 4
has_children: true
permalink: /docs/ml/regression
use_math: true
---

# ML regression
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 회귀 분석 
통계학에서, 회귀 분석(回歸 分析, 영어: regression analysis)은 관찰된 연속형 변수들에 대해 두 변수 사이의 모형을 구한뒤 적합도를 측정해 내는 분석 방법이다.

회귀분석은 시간에 따라 변화하는 데이터나 어떤 영향, 가설적 실험, 인과 관계의 모델링등의 통계적 예측에 이용될 수 있다. 그러나 많은 경우 가정이 맞는지 아닌지 적절하게 밝혀지지 않은 채로 이용되어 그 결과가 오용되는 경우도 있다. 특히 통계 소프트웨어의 발달로 분석이 용이해져서 결과를 쉽게 얻을 수 있지만 분석 방법의 선택이 적절했는지 또한 정보 분석이 정확한지 판단하는 것은 연구자에 달려 있다.

## 전개 
하나의 종속변수와 하나의 독립변수 사이의 관계를 분석할 경우를 단순회귀분석(영어: simple regression analysis), 하나의 종속변수와 여러 독립변수 사이의 관계를 규명하고자 할 경우를 다중회귀분석(영어: multiple regression analysis)이라고 한다.

### 회귀분석의 표준 가정 
회귀분석은 다음의 가정을 바탕으로 한다.

* 오차항은 모든 독립변수 값에 대하여 동일한 분산을 갖는다. (선형성)
* 오차항의 평균(기대값)은 0이다. (정규성)
* 수집된 데이터의 확률 분포는 정규분포를 이루고 있다.
* 독립변수 상호간에는 상관관계가 없어야 한다. (독립성)
* 시간에 따라 수집한 데이터들은 잡음의 영향을 받지 않아야 한다.
* 독립변수들간에 상관관계가 나타나는 경우 다중공선성문제라고 한다.

### 회귀 모형 적합도
회귀모형의 적합도는 잔차(Cost) 검정을 통해 확인한다. 잔차 검정은 정규성과 등분산성 가정을 만족하는지에 대한 검토 과정이다. 잔차의 정규성은 Shaprio-Wilk 검정 또는 Kolmogolov-Smirnov 검정을 통해서 실시하며, 회귀분석에서도 등분산성 방법으로는 Breusch-Pagan test, Goldfeld-Quandt test, Cook-Weisberg test 그리고 White test가 있다.

### 회귀 방정식 
$\hat{y} = w_0 + w_1*X$
회계계수 $w_0$ , $w_1$을 찾아 내는 과정이라 보면 된다. 

## 오차 값을 구하는 방법 
데이터를 가장 잘 표현하는 직선을 찾아내기 위해서 오차가 가장 작도록 $w_0 , w_1$을 찾아내야 한다. 그럼 직선과 점들의 오차를 단순히 다 더하면 되는 것일까 하는 궁금증이 생긴다. 

데이터 중에서는 직선의 예측값보다 큰 값도 존재하고, 작은 값도 존재한다. 이로 인해 (실제값 - 예측값) 혹은 (예측값 - 실제값)을 하는 경우에 양의 값과 음의 값이 둘다 나오게 된다.(통상적으로 오차는 실제값에서 예측값을 뺀 값을 사용한다.) 음수 양수 $\pm$ 모두 존재하기에 오차를 정확하게 반영하지 못하는 현상이 일어나게 된다. 2가지 방법으로 오차값을 산출한다. 

- 절대값을 취해서 더한다 (Mean Absolute Error)
- 제곰을 구해서 더한다. (RSS , Residual sum of square)

## RSS
앞에서 왜 오차의 제곱을 더한 값들이 오차값의 기준이 되는지에 대해서 알아보았다. 

$RSS(w_0,w_1)= \frac{1}{N}\sum_{i=1}^n(y_i - (w_0 + w_1 * x_i))^2$


## 경사하강법 
오차의 기준인 비용함수를 알아보았다. 비용 함수의 최소를 구하는 방법으로는 경사하강법이 있다. 위에서 예를 단순 선형 회귀 예를 들어서 $w_i$를 쉽게 구할수도 있을 것이라고 생각할 수도 있다. 하지만, 피처가 여러개, 즉 독립변수 X가 여러개가 된다면, 좌표를 통해서 표현하는 것이 복잡해지고, 최소값을 찾아내기도 매우 어려워진다. 경사하강법은 오차를 통해서 각 회귀 계수($w_i$)들을 오차를 줄이는 방향으로 업데이트 하는 것을 의미한다. 이로 인해 오차를 줄이는 방향과 업데이트의 양이 경사하강법에서 중요하게 작용한다. 

### 오차를 줄이는 방향 (미분)
독립변수 $w_0$,$w_1$ 이 주어졌을때 회귀를 통한 예측을 위에서 확인하였다. 학습을 이어나갈수록 $w_0$,$w_1$ 을 찾는 과정이 반복되는데 이때 편미분을 수행하여 회귀변수를 최적화 할수 있다. 

W1을 이용한 편미분 ($w_1$에 변화에 따른 RSS 변화율)

$\frac{\partial R(w)}{\partial{w_1}} = \frac{2}{N}\sum_{i=1}^N - x_t*(y_i(w_0 + w_1 \cdot x_i))=-\frac{2}{N}\sum_{i=0}^Nx_i*(실제값_i-예측값_i)$

W0을 이용한 편미분 ($w_0$에 변화에 따른 RSS 변화율)
$\frac{\partial R(w)}{\partial{w_0}} = \frac{2}{N}\sum_{i=1}^N-(y_i - (w_0 + w_1 \cdot x_i))=-\frac{2}{N}(실제값_i - 예측값_i)$


## 학습률(learning_rate)
회귀계수에서 편미분 한 값 그대로를 빼주면 비용함수의 최소값을 구할수 있는 것일까? 회귀 계수를 업데이트 하는 양에 대해서도 고민을 해봐야 한다. 만약 미분값이 너무 크거나 작다면 회귀 계수를 업데이트 했을 때, 최적 지점을 넘어가게 되거나 아니면 너무 느리게 진행될 것이다. 이것을 위해서 하이퍼 파라미터로 직접 지정해주는 학습률이라는 보정 계수를 업데이트 하는 미분 값에 곱해주게 된다. 


[![학습률 변화에 따른 회귀계수 영향도](https://www.deeplearningwizard.com/deep_learning/boosting_models_pytorch/images/lr1.png)](http://google.com.au/)


### 경사하강법의 단점 
일반적으로 경사하강법은 모든 학습데이터에 대해서 반복적으로 비용함수를 최소화하기 위한 값을 업데이트 하여 수행시간이 매우 오래 걸린다는 단점을 가지고 있다. 이런 단점을 보완하기 위해서 확률적 경사하강법(Stochasic Gradient Descent)을 사용한다. 일반 경사하강법은 한번 학습하는 과정에서 모든 데이터를 가지고 편미분한 값과 학습값의 곱을 반복하여 업데이트하는 과정이라면, 확률적 경사하강법은 한번 학습할 때 하나의 데이터만 가지고 계수 업데이트를 한다. 

한번 학습할때 모든 데이터를 다루는 것이 아니라 하나의 데이터만 가지고 계산을 수행하여 확률적 경사하강법이 수행시간은 짧다. 일반 경사하강법과 확률적 경사하강법 모두 학습을 반복할수록 최적해에 수렴하는 모습을 보이지만, 일반 경사하강법은 일정하게 최적해에 접근하는 반법, 확률적 경사하강법은 노이즈가 심하다. 

대부분은 둘의 절출안인 미니배치 확률적 경사하강법을 사용한다. 사용자가 직접 미니 배치의 개수를 정하고, 한번 학습할때 지정한 수만큼의 데이터를 사용하여 가중치를 업데이트 한다. 

## 회귀 평가 지표 

|평가지표|설명|수식|
|:-|:-|:-|
|MAE|Mean Absulute Error(MAE) 이며 실제 값과 예측값의 차이를 절댓값으로 변환해 평균한 것입니다.| $\displaystyle\frac{1}{n}\sum_{t=1}^{n}\|y_i-\hat{\hat{y}_i}\|$ |
|MSE|Mean Squared Error(MSE) 이며 실제 값과 예측값의 차이를 제곱해 평균한 것입니다. | $\displaystyle\frac{1}{n}\sum_{t=1}^{n}(y_i-\hat{\hat{y}_i})^2$ |
|MSLE|MSE에 로그를 적용한 것입니다. 결정값이 클 수록 오류값도 커지기 때문에 일부 큰 오류값들로 인해 전체 오류값이 커지는 것을 막아줍니다. |Log(MSE)|
|RMSE|MSE 값은 오류의 제곱을 구하므로 실제 오류 평균보다 더 커지는 특성이 있으므로 MSE에 루트를 씌운 것이 RMSE(Root Mean Squared Error)입니다. |  $\displaystyle\sqrt{\frac{1}{n}\sum_{t=1}^{n}(y_i-\hat{\hat{y}_i})^2}$|
|RMSLE||Log(RMSE)|
|$R^2$|분산 기반으로 예측 성능을 평가합니다. 실제 값의 분산 대비 예측값의 분산 대비 예측값의 분산 비율을 지표로 하며, 1에 가까울소록 예측 정확도(이게 마자) 높습니다. |$\frac{예측값 Variance}{실제값 Variance}$|

## 사이킷런 회귀 평가 API 
사이킷런은 아쉽게도 RMSE를 제공하지 않습니다. RMSE를 구하기 위해서는 MSE에 제곱근을 씌워서 계산하는 함수를 직접 만들어야 합니다. 

### cross_val_score,GridSearchCV에서 평가 시 사용되는 scoring 파라미터의 적용 값 


|평가방법|사이킷런 평가 지표 API | Scoring 함수 적용 값 |
|:-|:-|:-|
|MAE|metric.mean_absolute_error|'neg_mean_absolute_error'|
|MSE|metrics.mean_squared_error|'neg_mean_squared_error'|
|$R^2$|metrics.r2_score |'r2'|

주의 사항 
* RMSE를 제공하지 않아 MSE에 제곱근을 씌워서 계산하는 함수를 직접 만들어야 한다. 
* ``'neg'`` 'neg_mean_absolute_error' 일반적인 Scorring 함수는 결과가 커져야 좋게 인지 하는 경향이 있다. 따라서 회귀 평가시에는 비용함수를 작게 하는게 좋아 'neg' prefix 가 붙게 된다. 

## 사이킷런의 회귀 트리지원 

| 알고리즘 | 회귀Estimator |분류 Estimator 클래스|
|:-|:-|:-|
|Decision Tree|DecisionTreeRegressor|DecisionTreeClassifier|
|Gradient Boosting|GradientBoostingRegressor|GradientBoostingClassifier|
|XGBoost|XGBRegressor|XGBClassfier|
|LightGBM|LGBMRegressor|LGBMClassifier|









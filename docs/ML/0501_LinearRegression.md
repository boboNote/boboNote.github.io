---
layout: default
title: LinearRegression
parent: regression
grand_parent: Machine learning
permalink: /docs/ml/regression/LinearRegression
nav_order: 5001
use_math: true
---

# LinearRegression
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## LinearRegression 
LinearRegression 클래스는 예측 값과 실제 값의 RSS(Residual Sum of Squeares)를 최소화해 OLS(Ordinart Least Squares) 추정 방식으로 구현한 클래스 입니다. 

| Type | Name | Desc |
|:-|:-|:-|
| Input | fit_intercept | boolean 값으로 디폴트 True, Intercept(절편) 값을 계산할 것인지 말지를 지정합니다. 만일 False로 지정하면 intercept가 사용되지 않고 0으로 지정됩니다. |  
|Input|normalize| boolean 값으로 디폴트 False, fit_intercept가 False인 경우에는 이 파라미터가 무시됩니다. 만일 True이면 회귀를 수행하기 전에 입력 데이터 세트를 정규화 합니다.  | 
|Attr|coef_| fit()메소드를 수행했을 때 회귀 계수가 배열 형태로 저장하는 속성 |
|Attr|intercept_| 절편(Intercept) 값|

### coef_ 를 이용한 회귀계수 확인 

<div class="code-example" markdown="1">
모델 학습에 따른 반영된 회귀계수를 가시적으로 확인 할수 있다. 

```python 
lr = LinearRegression()
lr.fit(X_train ,y_train )
y_preds = lr.predict(X_test)
mse = mean_squared_error(y_test, y_preds)
rmse = np.sqrt(mse)

coeff = pd.Series(data=np.round(lr.coef_, 1), index=X_data.columns )
coeff.sort_values(ascending=False)
```
</div>



### cross_val_score scoring사용시 주의 사항 

 cross_val_score,GridSearchCV에서 평가 시 사용되는 scoring 파라미터의 적용 값)
 


<div class="code-example" markdown="1">
비용함수 관련된 사용법 예시 

``` python 
... 
neg_mse_scores = cross_val_score(lr, X_data, y_target, scoring="neg_mean_squared_error", cv = 5)
rmse_scores  = np.sqrt(-1 * neg_mse_scores)
avg_rmse = np.mean(rmse_scores)

# cross_val_score(scoring="neg_mean_squared_error")로 반환된 값은 모두 음수 
print(' 5 folds 의 개별 Negative MSE scores: ', np.round(neg_mse_scores, 2))
print(' 5 folds 의 개별 RMSE scores : ', np.round(rmse_scores, 2))
print(' 5 folds 의 평균 RMSE : {0:.3f} '.format(avg_rmse))
```
</div>

## 다중 공선성 문제 
일반적으로 선형 회귀는 입력 피처의 독립성에 많은 영향을 받습니다. 피처간의 상관관계가 매우 높은 경우 분산이 매우 커져서 오류에 매우 민감해집니다. 이러한 현상을 다중 공선선(multi-collinearity) 문제라고 합니다. 일반적으로 상관관계가 높은 피처가 많은 경우 독립적인 중요한 피처만 남기고 제거하거나 ``규제``를 적용합니다. 




---
layout: default
title: division dataset
parent: Scikit learn
grand_parent: Machine learning
permalink: /docs/ml/scikit/divisiondataset
nav_order: 1001
---


# 학습과 테스트 데이터 세트의 분리 
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Model Selection - 학습 데이터 테스트 데이터 
학습데이터 세트 
* 머신러닝 알고리즘의 학습을 위해 사용
* 데이터의 속성들과 결정값(레이블)값 모두를 가지고 있음 
* 학습 데이터를 기반으로 머신러닝 알고리즘이 데이터속성과 결정값의 패턴을 인지하고 학습 
  
테스트 데이터 세트 
* 테스트 데이터 세트에서 학습된 머신러닝 알고리즘을 테스트 
* 테스트 데이터는 속성 데이터만 머신러닝 알고리즘에 제공하며, 머신러닝 알고리즘은 제공된 데이터를 기반으로 결정값을 예측 
* 테스트 데이터는 학습 데이터와 별도의 데이터 세트로 제공되어야 함. 
  

## train_test_split()
sklearn.model_selection의 train_test_split()함수 

<div class="code-example" markdown="1">
학습 데이터와 테스트 데이터 분리를 관장 

### API `train_test_split` 
</div>
```markdown 
* test_size: 전체 데이터에서 테스트 데이터 세트 크기를 얼마로 샘플링할 것인가를 결정합니다. 디폴트는 `0.25`, 즉 25%입니다. 
* train_size: 전체 데이터에서 학습용 데이터 세트 크기를 얼마로 샘플링할 것인가를 결정합니다.  *통상적으로 잘사용되지 않음*
* shuffle: 데이터를 분리하기 전에 데이터를 미리 섞을지를 결정합니다. 디폴트는 True 입니다. 데이터를 분산시켜서 좀 효율적인 학습 및 테스트 데이터 세트를 만드는데 사용됩니다. 
* tandom_state : random_state는 호출할 때마다 동일한 학습/테스트용 데이터 세트를 생성하기 위해 주어지는 난수 값입니다. 
```

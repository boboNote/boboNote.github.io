---
layout: default
title: matplotlib
parent: Machine learning
nav_order: 100
has_children: false
permalink: /docs/ml/matplotlib
use_math: true
---



# matplotlib
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## matplotlib 
matplotlib은 다양한 데이터를 많은 방법으로 도식화 할 수 있도록 하는 파이썬 라이브러리로써, 우리는 matplotlib의 pyplot을 이용하게 됩니다. 이는 mathworks에서 개발한 매트랩(MATLAB)과 비슷한 형태를 가지고 있습니다. matplotlib을 이용하면 우리가 이전에 알아본 numpy나 pandas에서 사용되는 자료구조를 쉽게 시각화 할 수 있습니다.

## 회귀에 픽처 상관계수 
<div class="code-example" markdown="1">
회귀모델 구현에 앞서 픽처와 결과값과에 상관계를 알수 간단하게 알수 있다. 
아래 예제는 싸이킷런에서 재공해주는 암진단 데이트를 기준으로 구성되어 있다. 

```python 
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
from scipy import stats
from sklearn.datasets import load_boston
%matplotlib inline


boston = load_boston()                                                   # boston 데이타셋 로드 
bostonDF = pd.DataFrame(boston.data , columns = boston.feature_names)    # boston 데이타셋 DataFrame 변환 
bostonDF.head()                                                          # print Pandas DataFrame 

result 
    CRIM	ZN	INDUS	CHAS	NOX	RM	AGE	DIS	RAD	TAX	PTRATIO	B	LSTAT	PRICE
    0	0.00632	18.0	2.31	0.0	0.538	6.575	65.2	4.0900	1.0	296.0	15.3	396.90	4.98	24.0
    1	0.02731	0.0	7.07	0.0	0.469	6.421	78.9	4.9671	2.0	242.0	17.8	396.90	9.14	21.6
    2	0.02729	0.0	7.07	0.0	0.469	7.185	61.1	4.9671	2.0	242.0	17.8	392.83	4.03	34.7
    3	0.03237	0.0	2.18	0.0	0.458	6.998	45.8	6.0622	3.0	222.0	18.7	394.63	2.94	33.4
    4	0.06905	0.0	2.18	0.0	0.458	7.147	54.2	6.0622	3.0	222.0	18.7	396.90	5.33	36.2

# 2개의 행과 4개의 열을 가진 subplots를 이용. axs는 4x2개의 ax를 가짐.
fig, axs = plt.subplots(figsize=(16,8) , ncols=4 , nrows=2)
lm_features = ['RM','ZN','INDUS','NOX','AGE','PTRATIO','LSTAT','RAD']
for i , feature in enumerate(lm_features):                                # Ops 0/4 는 문제가 되지 않나 ? 암튼 
    row = int(i/4)
    col = i%4
    # 시본의 regplot을 이용해 산점도와 선형 회귀 직선을 함께 표현
    sns.regplot(x=feature , y='PRICE',data=bostonDF , ax=axs[row][col])
```

[![Feature 상관관계](https://media.vlpt.us/images/sset2323/post/afa83c81-969e-4952-90e7-d8e539ba467a/image.png)](http://google.com.au/)

</div>




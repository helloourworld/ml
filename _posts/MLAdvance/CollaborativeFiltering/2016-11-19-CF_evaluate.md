---
layout: post
title: 推荐系统中的评测指标
category: MLAdvance
catalog: yes
description: 推荐系统中的评测指标
tags:
    - Machine Learning
    - Python
---

$$ \textrm{RMSE} = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2} $$

```
from sklearn.metrics import mean_squared_error
RMSE = mean_squared_error(y, y_pred)**0.5
```

---
layout:     post
title:      "机器学习算法中的样本权重"
subtitle:   "sample_weight"
date:       2018-03-08 10:00:00
author:     "Nova"
header-img: "img/post/post-bg-rainbow.jpg"
catalog: true
tags:
    - 机器学习
---

## 样本权重参数在机器学习算法中的使用效果

以Logistic regression的二分类为例，python代码如下。

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn import linear_model, preprocessing
%matplotlib inline
```


```python
np.random.seed(314)
size_0 = 100
size_1 = 100
sample_size = size_0 + size_1
X_0 = np.random.randn(size_0, 3) * 3.5 + [2.0, 3.0, 5.0]
y_0 = np.array([0]*size_0)
X_1 = np.random.randn(size_0, 3) * 3.5 + [10.0, 10.0, 10.0]
y_1 = np.array([1]*size_1)
X = np.concatenate([X_0, X_1])
y = np.concatenate([y_0, y_1])
```


```python
plt.scatter(X[:,0], X[:,1], c=y)
```

<matplotlib.collections.PathCollection at 0x961bb70>

![png](https://novasky.top/img/article/2018-03-08-机器学习算法中的样本权重/output_2_1.png)

```python
np.random.seed(314)
sample_weight = 100 * np.abs(np.random.randn(sample_size))
sample_weight[:100] *= 10
plt.hist(sample_weight)
```


    (array([ 120.,   20.,   13.,   20.,   13.,    6.,    4.,    2.,    1.,    1.]),
     array([  3.89518682e-01,   2.66755872e+02,   5.33122226e+02,
              7.99488580e+02,   1.06585493e+03,   1.33222129e+03,
              1.59858764e+03,   1.86495399e+03,   2.13132035e+03,
              2.39768670e+03,   2.66405306e+03]),
     <a list of 10 Patch objects>)


![png](https://novasky.top/img/article/2018-03-08-机器学习算法中的样本权重/output_3_1.png)

```python
model_lr_noweight = linear_model.LogisticRegression()
model_lr_noweight.fit(X, y)
model_lr_noweight.intercept_
b = -model_lr_noweight.intercept_ / model_lr_noweight.coef_[0][1]
w = -model_lr_noweight.coef_[0][0] / model_lr_noweight.coef_[0][1]
xx = np.arange(-5, 20)
yy = xx * w + b
plt.scatter(X[:,0], X[:,1], c=y)
plt.plot(xx, yy)
```

<matplotlib.lines.Line2D at 0x9766e10>

![png](https://novasky.top/img/article/2018-03-08-机器学习算法中的样本权重/output_4_1.png)

```python
model_lr_weighted = linear_model.LogisticRegression()
model_lr_weighted.fit(X, y, sample_weight)
model_lr_weighted.intercept_
b = -model_lr_weighted.intercept_ / model_lr_weighted.coef_[0][1]
w = -model_lr_weighted.coef_[0][0] / model_lr_weighted.coef_[0][1]
xx = np.arange(-5, 20)
yy = xx * w + b
plt.scatter(X[:,0], X[:,1], c=y)
plt.plot(xx, yy, 'r')
```

<matplotlib.lines.Line2D at 0x979a198>

![png](https://novasky.top/img/article/2018-03-08-机器学习算法中的样本权重/output_5_1.png)

```python
b = -model_lr_noweight.intercept_ / model_lr_noweight.coef_[0][1]
w = -model_lr_noweight.coef_[0][0] / model_lr_noweight.coef_[0][1]
xx_noweight = np.arange(-5, 20)
yy_noweight = xx_noweight * w + b
b = -model_lr_weighted.intercept_ / model_lr_weighted.coef_[0][1]
w = -model_lr_weighted.coef_[0][0] / model_lr_weighted.coef_[0][1]
xx_weighted = np.arange(-5, 20)
yy_weighted = xx_weighted * w + b
plt.scatter(X[:,0], X[:,1], c=y)
plt.plot(xx_noweight, yy_noweight, 'b--')
plt.plot(xx_weighted, yy_weighted, 'r-')
```

<matplotlib.lines.Line2D at 0xa8f1748>

![png](https://novasky.top/img/article/2018-03-08-机器学习算法中的样本权重/output_6_1.png)

经过上述实验过程，可以发现，分类器会更加注重于将权重高的样本分类正确，从而牺牲权重低的样本错分。
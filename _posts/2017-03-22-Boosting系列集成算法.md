---
layout:     post
title:      "Boosting系列集成学习算法"
subtitle:   " \"序列提升集成学习的威力\""
date:       2017-03-22 12:00:00
author:     "Nova"
header-img: "img/post/post-bg-2.jpg"
catalog: true
tags:
    - 机器学习
    - 集成学习
    - boosting
---

### 1 AdaBoost

#### 1.1 AdaBoost 算法思路

算法的设计流程：

设 $$u^{(t)}$$ 为第 $$t$$ 步时每个样本的权重向量，其初始化值可表达如下：

$$u^{(1)} = [\frac{1}{N}, \frac{1}{N}, \cdots,\frac{1}{N}]$$

for $$t=1,2,\dots,T$$

1. 根据预设好的算法 $$\mathcal{A}$$ （如决策树桩 decision stump），依据 $$\mathcal{A}(\mathcal{D},u^{(t)})$$ 得到 $$g_t$$，算法 $$\mathcal{A}$$ 的目标是得到在权重 $$u^{(t)}$$ 基础上的最小训练误差 error.

2. 更新 $$u^{(t)}$$ 到 $$u^{(t+1)}$$，更新规则如下：

   计算 $$\epsilon_{t} = P_{x \sim \mathcal{D}} \left(y \neq g_t(x)\right) = \frac{\sum_{n=1}^N u_n^{(t)} [ y_n \neq g_t(x_n)]}{\sum_{n=1}^Nu_n^{(t)}}$$

   计算 $$\Delta t = \sqrt[]{\frac{1-\epsilon_t}{\epsilon_t}}$$

   若 $$y_n \neq g_t(x_n)$$ （错误样本）：$$u_n^{t+1} = u_n^{t} \times \Delta t$$

   若 $$y_n = g_t(x_n)$$ （正确样本）：$$u_n^{t+1} = u_n^{t} / \Delta t$$

3. 计算 $$\alpha_t = \ln(\Delta t)$$

最终，得到集成的模型：$$G(x)=sign \left( \sum_{t=1}^T \alpha_t g_t(x) \right)$$

不难发现，在该算法中有两个重要的计算值：$$\Delta t$$ 和 $$\alpha_t$$，当 $$\epsilon_t$$ 越小时，$$\Delta t$$ 会越大，若 $$\epsilon_t = 0$$，也就是分类器完全正确，那么 $$\Delta t$$ 将会是正无穷 $$\infty$$，显然，这样的设计是合理的。再看 $$\alpha_t$$，若 $$\Delta t = 1$$，此时 $$\epsilon_t = 0.5$$ （此时的分类器等效于完全随机猜测的分类精度），那么 $$\alpha_t = 0$$，该分类器在最终模型中的权重为0，也就是说，$$\alpha_t $$ 的大小对应于分类器的精度，显然，这样的设计也是非常合理的。

#### 1.2 AdaBoost 深入理解

在上述算法循环体内的步骤2中，$$u^{(t+1)}$$ 的更新值可以用一个统一的表达式来计算：

$$u_n^{t+1} = u_n^{t} \times \Delta t^{-y_n g_t(x_n)} = u_n^{(t)}\exp(-\alpha_t y_n g_t(x_n))$$

即：$$y_n$$ 与 $$g_t(x_n)$$ 异号，则 $$u_n^{t+1} = u_n^{t} \times \Delta t$$，$$y_n$$ 与 $$g_t(x_n)$$ 同号，则 $$u_n^{t+1} = u_n^{t} / \Delta t$$

同时，还可将 $$u_n^{T+1}$$ 的计算公式写成如下形式：

$$u_n^{(T+1)} = u_n^{(1)} \cdot \prod_{t=1}^{T} \exp{\left( -y_n \alpha_t g_t(x_n) \right)}$$

$$u_n^{(T+1)} = \frac{1}{N} \cdot \exp \left( -y_n {\sum_{t=1}^T \alpha_t g_t(x_n)} \right)$$

对照之前得到的最终模型的公式：$$G(x)=sign \left( \sum_{t=1}^T \alpha_t g_t(x) \right)$$，如果把 $$\sum_{t=1}^T \alpha_t g_t(x)$$ 看做模型推测的投票分数 (voting score) ，可以发现在 AdaBoost 算法中：$$u_n^{(T+1)} \propto \exp \left( -y_n \space ('voting\_score\_on \space x_n \space ') \right)$$

现在，继续看 voting score 这个公式：$$\sum_{t=1}^T \alpha_t g_t(x)$$ 看成是一种线性组合的话，即类似于：$$w_i \cdot \phi_i(x_n)$$ 的形式。那么此时，我们可大胆联想一下 SVM 中的间隔的计算方式：SVM margin = $$\frac{y_n\left( w^T x_n + b \right)}{\| w \|}$$.

其实，$$y_n \space ('voting\_score\_on \space x_n \space ')$$ 相当于是一种有符号，没有标准化的间隔距离，对于分类问题来说，我们都希望这种间隔距离是正值且越大越好。那么对于 $$u_n^{(T+1)}$$ 来说，就希望该值越小越好。这里我们还能够引申出一个论断：在 AdaBoost 中，$$\sum_{i=1}^{N}u_i^{(t)}$$ 是随着迭代次数 $$t$$ 单调递减的（这里不做进一步证明，但直觉上是 make sense 的）。

---

**有关 GBDT 和 xgboost，可参考：[pdf](http://novasky.top/ref/gbdt算法原理简介.pdf)**

## 2 xgboost

### 2.1 xgboost 特性   

**xgboost 相比传统 gbdt 有何不同？xgboost 为什么快？xgboost 如何支持并行？**

- 传统 GBDT 以 CART 作为基分类器，xgboost 还支持线性分类器，这个时候 xgboost 相当于带 L1 和 L2 正则化项的逻辑斯蒂回归（分类问题）或者线性回归（回归问题）。
- 传统 GBDT 在优化时只用到一阶导数信息，xgboost 则对代价函数进行了二阶泰勒展开，同时用到了一阶和二阶导数。顺便提一下，xgboost 工具支持自定义代价函数，只要函数可一阶和二阶求导。
- xgboost 在代价函数里加入了正则项，用于控制模型的复杂度。正则项里包含了树的叶子节点个数、每个叶子节点上输出的 score 的 L2 范数的平方和。从 bias-variance tradeoff  角度来讲，正则项降低了模型的variance，使学习出来的模型更加简单，防止过拟合，这也是 xgboost 优于传统 GBDT 的一个特性。
- Shrinkage（缩减），相当于学习速率（xgboost 中的 eta）。xgboost 在进行完一次迭代后，会将叶子节点的权重乘上该系数，主要是为了削弱每棵树的影响，让后面有更大的学习空间。实际应用中，一般把 eta 设置得小一点，然后迭代次数设置得大一点。（补充：传统GBDT的实现也有学习速率）
- 列抽样（column subsampling）。xgboost 借鉴了随机森林的做法，支持列抽样，不仅能降低过拟合，还能减少计算，这也是 xgboost 异于传统 gbdt 的一个特性。


- 对缺失值的处理。对于特征的值有缺失的样本，xgboost 可以自动学习出它的分裂方向。
- xgboost 工具支持并行。boosting 不是一种串行的结构吗?怎么并行的？注意 xgboost 的并行不是 tree 粒度的并行，xgboost 也是一次迭代完才能进行下一次迭代的（第 t 次迭代的代价函数里包含了前面 t-1 次迭代的预测值）。xgboost 的并行是在特征粒度上的。我们知道，决策树的学习最耗时的一个步骤就是对特征的值进行排序（因为要确定最佳分割点），xgboost 在训练之前，预先对数据进行了排序，然后保存为 block 结构，后面的迭代中重复地使用这个结构，大大减小计算量。这个 block 结构也使得并行成为了可能，在进行节点的分裂时，需要计算每个特征的增益，最终选增益最大的那个特征去做分裂，那么各个特征的增益计算就可以开多线程进行。


- 可并行的近似直方图算法。树节点在进行分裂时，我们需要计算每个特征的每个分割点对应的增益，即用贪心法枚举所有可能的分割点。当数据无法一次载入内存或者在分布式情况下，贪心算法效率就会变得很低，所以 xgboost 还提出了一种可并行的近似直方图算法，用于高效地生成候选的分割点。

### 2.2 xgboost 参数

- General Parameters :
  - booster : 所使用的模型，gbtree 或 bgliner
  - silent : 1 则不打印提示信息，0 则打印，默认为 0
  - nthread : 所使用的线程数量，默认为最大可用数量
- Booster Parameters (gbtree) :
  - eta : 学习率，默认初始化为 0.3，经多轮迭代后一般衰减到 0.01 至 0.2
  - min_child_weight : 每个子节点所需的最小权重之和，默认为 1
  - max_depth : 树的最大深度，默认为 6，一般为 3 至 10
  - max_leaf_nodes : 叶节点最大数量，默认为 2^6
  - gamma : 拆分节点时所需的最小损失衰减，默认为 0
  - max_delta_step : 默认为 0
  - subsample : 每棵树采样的样本数量比例，默认为 1，一般取 0.5 至 1
  - colsample_bytree : 每棵树采样的特征数量比例，默认为 1，一般取 0.5 至 1
  - colsample_bylevel : 默认为 1
  - lambda : L2 正则化项，默认为 1
  - alpha : L1 正则化项，默认为 1
  - scale_pos_weight : 加快收敛速度，默认为 1
- Learning Task Parameter :
  - objective : 目标函数，默认为 reg:linear，还可取 binary:logistic, multi:softmax, multi:softprob
  - eval_metric : 误差函数，回归默认为 rmse，分类默认为 error，其他可取值包括 rmse, mae, logloss, merror, mlogloss, auc
  - seed : 随机数种子，默认为 0


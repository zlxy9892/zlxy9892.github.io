---
layout:     post
title:      "决策树"
subtitle:   " \"最符合人类思维的机器学习方法\""
date:       2017-02-28 22:00:00
author:     "Nova"
header-img: "img/post/post-bg-decisiontree.jpg"
catalog: true
tags:
    - 机器学习
    - 决策树
---

## 1 决策树的基本模型 

​	在很多初学者开始学习机器学习或者数据挖掘时，大多数教程都会从决策树 (decision tree) 开始，决策树是一类常见的机器学习方法，可以帮助我们解决分类与回归两类问题。以决策树的作为学习的起点原因很简单，因为它非常符合人类处理问题的方式，所以决策方式显得极为自然，且模型的解释性很好，能够让使用者知道为什么这个决策结果是我所希望的那样 (树形结构简单易懂)。

​	分类决策数模型是一种描述对实例进行分类的树形结构。决策树由结点 (node) 和有向边 (directed edge) 组成。结点包含了一个根结点 (root node)、若干个内部结点 (internal node) 和若干个叶结点 (leaf node)。内部结点表示一个特征或属性，叶结点表示一个类别。

​	那么，最为关键的问题就是，如何根据已知的数据集，来进行决策树的学习，构造一颗泛化能力强，即处理未见示例能力强的决策树。为此，决策树学习通常包括 3 个步骤：**特征选择**、**决策树的生成**、**决策树的修剪**。现有的关于决策树学习的主要思想主要包含以下 3 个研究成果：

- 由 *Quinlan* 在 1986 年提出的 **ID3** 算法
- 由 *Quinlan* 在 1993 年提出的 **C4.5** 算法
- 由 *Breiman* 等人在 1984 年提出的 **CART** 算法

## 2 划分选择

​	决策树学习的过程中，最为关键的就是如何**选择最优划分属性**。一般而言，随着划分过程不断进行，我们希望决策树的分支结点所包含的样本尽可能属于同一类别，即**结点的 “纯度” (purity)** 越来越高。

### 2.1 信息增益

​	“信息熵” (information entropy) 是度量样本集合纯度最常用的一种指标。假定当前样本集合 $$D$$ 中第 $$k$$ 类样本所占的比例为  $$p_k\;(k=1,2,\dots, \mid \mathcal{Y} \mid)$$，则 $$D$$ 的信息熵定义为：

​	 $$\displaystyle{ Ent(D) = -\sum_{k=1}^{\mid \mathcal{Y} \mid} p_k\log_2{p_k} }$$ 

 $$Ent(D)$$ 的值越小，则 $$D$$ 的纯度越高。信息熵表达一个数据所包含的信息量，或者说是信息的复杂程度、不确定性程度。例如当某一随机变量只取 0, 1 两个值时，当取 0 的概率 = 取 1 的概率 = 0.5 时，信息熵达到最大值，值为 1，可以看做此时系统的不确定性最高；当某一取值的概率为 0，另一取值概率为 1 时，此时可以认为系统是完全确定的，因为只能取到 0 或 1，相应的，信息熵取值为 0。

​	假设样本集 $$D$$ 中某一离散属性 $$a$$ 有 $$V$$ 个可能的取值 $${a^1, a^2, \dots, a^V}$$，若使用 $$a$$ 来对样本集 $$D$$ 进行划分，则会产生 $$V$$ 个分支结点 (注意：这里是默认把离散属性的每个可能的取值都进行划分)，其中第 $$v$$ 个分支结点包含了 $$D$$ 中所有在属性 $$a$$ 上取值为 $$a^v$$ 的样本，记为 $$D^v$$。我们可根据信息熵的定义计算出 $$D^v$$ 的信息熵。再考虑到不同分支结点所包含的样本数不同，给分支结点赋予权重 $$\frac{\mid D^v \mid}{\mid D \mid}$$，即样本数越多的分支结点的影响越大，于是可以计算出用属性 $$a$$ 对样本集 $$D$$ 进行划分所获得的 **“信息增益“** (information gain)：

​	$$\displaystyle{ Gain(D,a) = Ent(D) - \sum_{v=1}^{V} \frac{\mid D^v \mid}{\mid D \mid} Ent(D^v) }$$

​	一般而言，信息增益越大，则意味着使用属性 $$a$$ 来进行划分所获得的 "纯度提升" 越大。因此，我们可以用信息增益来进行决策树的划分属性选择。

​	**著名的 ID3 决策树学习算法就是以信息增益为准则来选择划分属性。**

### 2.2 信息增益率

​	使用上述的信息增益来作为划分准则是否就一定是最优策略呢？我们试想这样的情况，若我们获取的样本集数据中包含了一条属性是每个样本的 ID 号，那么根据信息增益的计算公式可以得到，当使用 ID 号作为划分属性时，它的信息增益率极高，这很容易理解：“ID 号” 代表了对每个样本的唯一标识，通过它来划分样本集，那么每个分支结点仅包含一个样本，这些分支结点的纯度达到了最大。然而，这样的决策树显然并非我们所希望得到的，因为它不具备泛化能力，无法对新样本进行有效预测。

​	仔细分析来看，信息增益准则对可取值数目较多的属性有所偏好，为减少这种可能带来的不利影响，**著名的 C4.5 决策树算法不直接使用信息增益，而是使用 “增益率” (gain ratio) 来选择最优划分属性**。在信息增益计算公式的基础上，增益率定义为：

​	$$\displaystyle{ Gain\_ratio(D,a) = \frac{Gain(D,a)}{IV(a)} }$$

其中，

​	$$\displaystyle{ IV(a) = - \sum_{v=1}^{V} \frac{\mid D^v \mid}{\mid D \mid} \log_2{\frac{\mid D^v \mid}{\mid D \mid}} }$$

$$IV(a)$$ 称为属性 $$a$$ 的 “固定值” (intrinsic value)，对比信息熵公式可以发现，其实 $$IV(a)$$ 就是等同于特征 $$a$$ 的信息熵 (有些书籍将其记为 $$H_a(D)$$)，其中的 $$\frac{\mid D^v \mid}{\mid D \mid}$$ 正是对属性 $$a$$ 取值为 $$a^v$$ 的概率的极大似然估计。那么，若属性 $$a$$ 的可能取值数目越多 (即 $$V$$ 越大)，则 $$IV(a)$$ 的值通常会越大。

​	**需要注意的是，增益率准则对可能取值数目较少的属性有所偏好，因此，C4.5 算法并不是直接选择增益率最大的候选划分属性，而是使用了一个启发式策略：先从候选划分属性中找出信息增益高于平均水平的属性，再从中选择增益率最高的。**

### 2.3 基尼指数

​	分类与回归树 (classification and regression tree, CART) 模型，是应用很广泛的决策树学习方法，从其命名可以看出，它可以解决分类和回归两类问题。与 ID3 和 C4.5 不同之处在于，CART 使用 “基尼指数” (Gini index) 来选择划分属性。数据集 $$D$$ 的纯度可用基尼值来度量：

​	 $$\displaystyle{ \begin{align*} Gini(D) &= \sum_{k=1}^{\mid \mathcal{Y} \mid} \sum_{k'\neq k} p_k p_{k'} \\ &= 1 - \sum_{k=1}^{\mid \mathcal{Y} \mid} p_k^2 \end{align*} }$$

也可以写成：

​	$$\displaystyle{ Gini(D) = \sum_{k=1}^{\mid \mathcal{Y} \mid} p_k (1-p_k) = 1 - \sum_{k=1}^{\mid \mathcal{Y} \mid} p_k^2 }$$

其中，$$p_k$$ 为数据集 $$D$$ 中输出值为 $$k$$ 的概率，根据实际数据，可采用 $$\frac{\mid D^k \mid}{\mid D \mid}$$ 来进行估计。

​	直观来说，$$Gini(D)$$ 反映了从数据集 $$D$$ 中随机抽取两个样本，其类别标记不一致的概率。因此， $$Gini(D)$$ 越小，则数据集 $$D$$ 的纯度越高。

​	基于此，对于属性 $$a$$ 的基尼指数定义为：

​	$$\displaystyle{ Gini\_index(D,a) = \sum_{v=1}^{V} \frac{\mid D^v \mid}{\mid D \mid} Gini(D^v) }$$

​	于是，我们在候选属性集合 $$A$$ 中，选择那个使得划分后基尼指数最小的属性作为最优划分属性，即：

​	$$\displaystyle{ a_* = \arg \min_{a \in A} Gini\_index(D,a) }$$

### 2.4 连续值处理

​	到目前为止，我们仅讨论了基于离散属性来生成决策树。现实学习任务中常会遇到连续属性，因此，有必要讨论如何在决策树学习中使用连续属性。

​	借助连续属性离散化技术，最简单的策略是采用**二分法 (bi-partition)** 对连续属性进行处理，这正是 C4.5 决策树算法中采用的机制，CART 也采用该策略。

​	给定样本集 $$D$$ 和连续属性 $$a$$，假定 $$a$$ 在 $$D$$ 上出现了 $$n$$ 个不同的取值，将这些取值从小到大进行排序，记为 $$\{ a^1, a^2, \dots, a^n \}$$。基于划分点 $$t$$ 可将 $$D$$ 分为两个子集，记为 $$\{-, +\}$$。显然，对于属性取值 $$a^i$$ 与 $$a^{i+1}$$ 来说，$$t$$ 在区间 $$[a^i, a^{i+1})$$ 中取任意值所产生的划分结果相同。因此，对连续属性 $$a$$，我们可考察包含 $$n-1$$ 个元素的候选划分点集合：

​	$$\displaystyle{ T_a = \{ \frac{a^i + a^{i+1}}{2} \mid 1 \leqslant i \leqslant n-1 \} }$$

即把每个区间的中位点作为候选划分点。然后，我们就可像离散属性值一样来考察这些划分点，选取最优的划分点进行样本集合的划分。

​	在 CART 应用于回归问题时，同样也是采用了二分法，将划分的两个子集分别求得输出量 $$y$$ 的均值，以此作为输出值的估计，记为 $$\hat{y}$$。在寻找最优切分点时，通过计算两个子区域的估计值与每个输出的真值之间的平方误差，选取使得平方误差总和最小的作为最优切分点。具体的，求解：

​	$$\displaystyle{ \arg \min_t \left[ \sum_{i \in -}(y_i - \hat{y_i})^2 + \sum_{i \in +}(y_i - \hat{y_i})^2 \right]}$$

​	使用该方法构建的回归树也称为**最小二乘回归树**。

## 3 关于剪枝处理

​	**剪枝 (pruning) 是决策树学习算法对付 “过拟合” 的主要手段**。在决策树学习中，为了尽可能正确分类训练样本，结点划分过程将不断重复，有时会造成决策树分支过多，这时就可能因针对训练样本学得 “太好” 了，以至于把训练集自身的一些特点当作所有数据都具有的一般性质而导致过拟合。因此，可通过主动去掉一些分支来降低过拟合的风险。

​	决策树剪枝的基本策略有 “预剪枝” (prepruning) 和 “后剪枝” (postpruning)。

- **预剪枝** 是指在决策树生成过程中，对每个结点在划分前先进行估计，若当前节点的划分不能带来决策树泛化性能的提升，则停止划分并将当前结点标记为叶结点；
- **后剪枝** 则是先从训练集生成一颗完整的决策树，然后自底向上地对非叶结点进行考察，若将该结点的子树替换为叶结点能带来决策树泛化性能的提升，则将该子树替换为叶结点。

## 4 参考文献与相关资料

1. [Quinlan, J. R. (1986). Induction of decision trees. Machine Learning, 1 (1), 81-106.](http://xueshu.baidu.com/s?wd=paperuri%3A%28cb2ed3f15dd5fe5a175c9c138dfc45fb%29&filter=sc_long_sign&tn=SE_xueshusource_2kduw22v&sc_vurl=http%3A%2F%2Flink.springer.com%2Farticle%2F10.1007%2FBF00116251&ie=utf-8&sc_us=16280436131839849759)
2. [Quinlan, J. R. (1992). C4.5: programs for machine learning. ,1.](https://books.google.co.jp/books?hl=zh-CN&lr=&id=b3ujBQAAQBAJ&oi=fnd&pg=PP1&dq=c4.5&ots=sQ1pYNJnF8&sig=NYlCTBTeNw2MNgwDEIal7H-ESVw&redir_esc=y#v=onepage&q=c4.5&f=false)
3. [Breiman, L., Friedman, J. H., Olshen, R. A., & Stone, C. J. (1984). Classification and regression trees.](http://www.gbv.de/dms/ilmenau/toc/150194293.PDF)


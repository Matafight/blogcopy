title: XGBoost 笔记
date: 2017-03-14 20:49:24
categories: 机器学习
tags: XGBoost
---
##  简介
  XGBoost是一种基于决策树（CART）的分布式的高效的梯度提升算法，它可被应用到分类、回归、排序等任务中，与一般的GBDT算法相比，XGBoost主要有以下几个优点：
  1.    对叶节点的权重进行了惩罚，相当于添加了正则项，防止过拟合
  2.    XGBoost的目标函数优化利用了损失函数关于待求函数的二阶导数，而GBDT只利用了一阶信息
  3.    XGBoost支持列采样，类似于随机森林，构建每棵树时对属性进行采样，训练速度快，效果好
  4.    类似于学习率，学习到一棵树后，对其权重进行缩减，从而降低该棵树的作用，提升可学习空间
  5.    构建树的算法包括精确的算法和近似的算法，近似的算法对每维特征加权分位进行分桶，具体的算法利用到了损失函数关于待求树的二阶导数。
  6.    添加了对于稀疏数据的支持，当数据的某个特征缺失时，将该数据划分到默认的子节点，本文提出了一个算法来求解这个默认方向。
  7.    可并行的近似直方图算法，分裂节点时，数据在block中按列存放，而且已经经过了预排序，因此可以并行计算，即同时对各个属性遍历最优分裂点


## 目标函数
  boosting是属于串行的集成方法，其预测函数为多个基分类器的集成，其学习过程也是先学习前(t-1)个基分类器，再学习第t个基分类器。XGBoost中最主要的基学习器为CART（分类与回归树），因此，其预测函数为:
  ![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_1.PNG)
  其中$K$表示有$K$个决策树![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_2.PNG)表示决策树空间，$q(x)$表示将输入样本$x$映射到树的叶子节点$q(x)$，其对应叶子节点的标号为$\omega_{q(x)}$。
  因此，正则化的目标函数可以写成：
  ![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_3.PNG)
  上述目标函数的参数是决策树，因此不能在一般的欧式空间优化，这里类似于adaboost算法的优化过程，也是利用加性模型的特点来训练。假设前$t-1$步迭代优化得到的模型为$f^{t-1}(x)$,在第$t$步中，待求参数为$f^t$，则第$t$步的目标函数为:
  ![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_4.PNG)
将上式进行二阶泰勒展开可以得到：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_5.PNG)
其中![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_6.PNG)
去掉与待求参数无关的常数项，从而得到新的优化目标为：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_7.PNG)
将上式变形，将关于样本迭代转换为关于树的叶子节点迭代：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_8.PNG)

这样，对于给定的树结构，可以直接计算得到叶子节点的标号：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_9.PNG)
以及相对应的最优目标函数值：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_10.PNG)

但是,由于树的结构是未知的，而且也不可能去遍历所有的树结构。因此，本文采用贪婪算法来分裂节点，从根节点开始，遍历所有属性，遍历属性的可能取值，记分到左子树的样本集为$I_L$,分到右子树的样本集为$I_R$，则分裂该节点导致的损失减少值为：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_11.PNG)
我们希望找到一个属性以及其对应的大小，使得上式取值最大。

## 分裂节点算法(SPLIT FINDING ALGORITHMS)
因为树结构未知，只能采用贪婪的算法，从根节点出发，每次选择一个属性及其对应的值，使得损失函数减少最多，根据选择的属性分裂节点。论文中给出了精确的和近似的算法，当数据量非常大时，采用近似的算法可以有效减少计算量。
### 精确贪婪算法(Basic Exact Greedy Algorithms)
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_12.PNG)
这里的$m$值通常小于样本维度$d$,表示列采样得到的属性个数，值得注意的是，由于要遍历所有的属性的所有取值，因此，通常需要在训练之前对所有样本做一个预排序(pre-sort)，从而避免每次选择属性都要重新排序。

### 近似算法(Approximate Algorithm for Split Finding)
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_13.PNG)
对于值为连续值的特征，当样本数非常大时，该特征取值过多，遍历所有取值复杂度较高，而且容易过拟合。因此，考虑将特征值分桶，即找到$l$个分位点，将位于相邻分位点之间的样本分在一个桶中，在遍历该特征的时候，只需要遍历各个分位点，从而计算最优划分。注意到上面算法流程中说明了有全局的近似(global)和局部(local)的近似，所谓全局就是在新生成一棵树之前就对各个特征计算分位点并划分样本，之后在每次分裂过程中都采用近似划分，而局部就是在具体的某一次分裂节点的过程中采用近似算法。

### Weighted Quantile Sketch
**带权重直方图算法**
主要用于近似算法中分位点的计算，假设分位点为 ${s_{k1},s_{k2},..,s_{kl}}$，假设 $(x_{1k},h_1),(x_{2k},h_2),..,(x_{nk},h_n)$ 表示所有样本的第 $k$ 个特征值及二阶导数，定义一个rank function 如下：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_14.PNG)
希望得到的分位点满足如下条件：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_15.PNG)
这意味着大概有$\frac{1}{\epsilon}$个分位点。

**为什么用二阶导数作为权重？**
*因为目标函数还可以写成带权的形式：*
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_16.PNG)
上式相当于带权重的损失函数，权重为$h_i$。
为了优化该问题，本文还提出了分布式 *weighted quantile sketch algorithm* ，该算法的优点是解决了带权重的直方图算法问题，以及有理论保证。

### Sparsity-aware Split Finding
XGBoost还特别设计了针对稀疏数据的算法，假设样本的第$i$个特征缺失时，无法利用该特征对样本进行划分，这里的做法是将该样本默认地分到指定的子节点，至于具体地分到哪个节点还需要下面的算法来计算：
![](http://7xiegr.com1.z0.glb.clouddn.com/xgboost_17.PNG)
该算法的主要思想是，分别假设特征缺失的样本属于右子树和左子树，而且只在不缺失的样本上迭代，分别计算缺失样本属于右子树和左子树的增益，选择增益最大的方向为缺失数据的默认方向。


## 系统设计
### Column Block for Parallel Learning
算法中最耗时的部分就是预排序，为了节省排序的时间，XGBoost将数据存在内存单元block中(??)，同时在block采用CSC 格式存放(Compressed Column format)，每一列(一个属性列)均升序存放，这样，一次读入数据并排好序后，以后均可使用。在精确贪心算法中，将所有数据均导入内存，算法只要在数据中线性扫描已经预排序过的特征就可以。对于近似算法，可以用多个block(Multiple blocks)分别存储不同的样本集，多个block可以并行计算，
重要的是，由于将数据按列存储，可以同时访问所有列，那么可以对所有属性同时执行split finding算法，从而并行化split finding.

### Cache-aware Access
由于样本按特征进行了预排序，样本对应的统计量(一阶和二阶梯度)需要根据行索引来查找，这导致了内存的不连续访问，容易导致cpu cache命中率降低。---这块没太搞懂

### Blocks for Out-of-core Computation
为了更好地利用计算机的磁盘空间，对于不能一次性导入到内存的数据，我们将数据分成多个block存在磁盘上，在计算过程中，用另外的线程读取数据，但是由于磁盘IO速度太慢，通常更不上计算的速度。所以，我们采用了下面两种方法有优化速度和存储：
1. Block compression
   将block按列压缩，对于行索引，只保存第一个索引值，然后只保存该数据与第一个索引值之差(offset)，一共用16个bits来保存 offset,因此，一个block一般有$2^{16}$个样本。
2. Block sharding
   提高磁盘的吞吐量(??)
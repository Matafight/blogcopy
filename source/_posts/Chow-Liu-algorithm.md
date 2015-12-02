title: Chow-Liu algorithm
date: 2015-10-10 15:49:50
categories: 机器学习
tags: Multi-output
---
#Chow-liu algorithm#

**Learning tree structure**

学习一个Bayesian network Classifier ，本质上就是要根据图模型的结构来学习联合概率分布，所以基本上要分为两步走，先学习模型本身，然后学习联合概率分布的参数。

现在假设学习树结构的图模型，可以证明结构相同的有向树和无向树有相同的联合概率分布，它们的参数个数相同，学习的复杂度相同，但是无向树的对称性在学习结构的时候很有用

![](http://i.imgur.com/AwrYtX1.png)

上式是无向树所对应的联合概率分布，V表示所有的顶点，E表示所有的边

**Chow-Liu algorithm for finding the ML tree structure**

利用上面的联合概率分布，可以得到下面的对数似然函数：
![](http://i.imgur.com/D2jBRxx.png)


其中Ntk 表示第t个节点正在状态k的个数，Nstjk 表示第s个节点处在状态j 的同时 第t个节点处在状态k的个数。所以有：
![](http://i.imgur.com/SXogAvr.png)


其中最后一项是给定的经验分布中的Xs 和 Xt 之间的互信息：

![](http://i.imgur.com/iF0FSFD.png)

因为最大似然函数中第一项与该树的拓扑结构没有关系，所以，最大化似然就相当于最大化互信息的和，给定了各个点之间的互信息，希望求得一棵树使得它们权重的和最大，因此，最大生成树的结构就是使得似然最大的结构，可以利用Kruskal 算法或 Prim 算法来求最大生成树，点与点之间的权重就是它们之间的互信息，这就是Chow-Liu algorithm 的主要思想。
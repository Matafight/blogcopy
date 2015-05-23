title: "bias vs variance"
date: 2015-03-11 09:49:10
categories: 机器学习
tags: Model-Selection
---
**频率学派vs贝叶斯学派**
频率学派把要推断的参数 θ当作固定且未知的常数，而样本x是随机的，其着眼点在样本空间，有关的概率计算都是针对x的分布。另一派叫做贝叶斯学派，他们把参数θ视作随机变量，而样本x是固定的，其着眼点在参数空间，重视参数θ的分布，固定的操作模式是通过参数的先验分布结合样本信息得到参数的后验分布。

频率学派把参数当作一个待确认系数，贝叶斯把参数当作随机变量：**p(X;W) vs p(X,W)**

bias-variance tradeoff：

expected loss = (bias)^2 + variance + noise

bias 指 预测的**均值**与真实值之间的差，variance 是指预测的值的波动情况，模型越复杂，bias会越小，但是variance 会变大。相反地，模型简单，bias 会大，variance 会小，所以要选择bias-variance balanced的模型。

对于最小二乘来说，正则项越大，模型就越简单，正则项越小，控制over-fitting的能力就弱，模型就复杂。

[http://mlnotes.com/2013/08/20/bv.html](http://mlnotes.com/2013/08/20/bv.html)

[http://scott.fortmann-roe.com/docs/BiasVariance.html](http://scott.fortmann-roe.com/docs/BiasVariance.html)
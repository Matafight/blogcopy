title: "KKT条件与拉格朗日对偶性"
date: 2015-05-13 14:17:40
categories: 机器学习
tags:
---

# 拉格朗日对偶问题求解#
一般的约束最优化问题都可以写成以下这种格式：


![](http://i.imgur.com/ZZp3wJM.png)


其中，f_0(x) f_1(x)... f_m(x) 全是是凸函数，h(x)全为仿射函数
我们希望将原始的约束最优化问题转变成新的无约束最优化问题，因此，定义拉格朗日函数如下:

![](http://i.imgur.com/nXN7YNr.png)

它通过一些系数把目标函数和约束条件结合在一起，现在让他们针对\lambda 和 \nu 最大化，令

![](http://i.imgur.com/ehQgvP7.png)

可以看出原始的带约束的最优化问题其实等价于如下的无约束优化问题
![](http://i.imgur.com/aE9x30Z.png)

到这里我们只是把原来的问题通过拉格朗日变成如下形式：
![](http://i.imgur.com/nIJLDb5.png)

这个问题（或者说是原始的带约束的形式）称作原始问题(prime problem)，其相应的对偶问题（即将MAX和min交换一下）为：
![](http://i.imgur.com/ZuHYJmt.png)

交换后的对偶问题与原始问题的解并不相等，但此时对偶问题最优解是原始问题最优解的下界
d^*<=p^*

这个性质叫做weak duality,对于所有的优化问题都成立，其中p^*-d^* 称作 duality gap.需要注意的是无论原始问题是什么形式
其对偶问题都是一个凸优化问题-它的极值是唯一的（如果存在的话）。这样一来对于一些难解的原始问题，我们可以求解其对偶问题，通过求解对偶问题得到原始问题的一个**下界估计**。

上面说的是weak duality ，还有strong duality
就是 d^*=p^*
当strong duality 成立的时候，我们可以通过求解dual problem 来解 prime problem,那么strong duality 成立的条件是什么呢?
首先任何满足strong duality 的问题（并不一定是凸优化问题）都满足KKT条件，换句话说，这是满足strong duality 的一个必要条件，不过当原始问题是凸优化问题的时候，且满足slater条件，KKT条件就升级为充要条件。如果原始问题是凸优化问题，并且存在x 和 (\lambda,\nu) 满足KKT条件，那么它们分别是prime problem 和 dual problem 的极值点，并且满足strong duality。

这里还要介绍一下slater条件，因为只有当原始问题是凸的，并且满足slater条件，才能保证强对偶性，也只有此时kkt条件才是强对偶的充要条件。

假设原始优化问题为：

![](http://i.imgur.com/HkYa1OF.png)

**Slater条件：存在一点x，满足 f_i(x)<0,i=1,..,m,且Ax=b,**即存在一点x，存在一个约束f_i,f_i(x)严格满足不等关系。这样的点也称为严格可行的。可以证明当原问题是凸的，且满足Slater条件，则强对偶一定满足。

如果还有一些约束是仿射的，Slater条件还可以被弱化，假设前k个不等约束是仿射的，则Slater条件可以被转化成：

**Refined Slater 条件：存在一点x满足
fi(x)≤0,i=1,…,k,fi(x)<0,i=k+1,…,m,Ax=b**

即仿射的不等式约束不必严格小于。

# KKT条件 #
总结一下上面的内容：
对于一般的优化问题，不管其是不是凸的，其对偶问题一定是凸优化问题，此时对偶问题是原始问题的一个下界，当满足强对偶时，一定满足KKT条件，此时kkt条件是强对偶的必要条件。
当原始问题是凸的，且满足slater 条件，kkt条件是强对偶的充要条件。

![](http://i.imgur.com/JOx0ru0.png)

前两条是原始问题约束条件，第三条是拉格朗日函数的要求（结合图像理解）第四条是互补松弛条件，最后一条是拉格朗日取极值时的必要条件。
容易看出，由于最后一个条件的限制，对于任意优化问题，只要x和(λ,ν)是原始问题和对偶问题的最优解，则其一定满足KKT条件。即KKT条件是一组解成为最优解的必要条件。而当原始问题是凸的时候，最后一条变成了充要条件。

参考：
[http://blog.pluskid.org/?p=702](http://blog.pluskid.org/?p=702)
[http://xuehy.github.io/%E4%BC%98%E5%8C%96/2014/04/13/KKT/](http://xuehy.github.io/%E4%BC%98%E5%8C%96/2014/04/13/KKT/)
[http://www.moozhi.com/topic/show/54a8a261c555c08b3d59d996](http://www.moozhi.com/topic/show/54a8a261c555c08b3d59d996)
[http://blog.csdn.net/xianlingmao/article/details/7919597](http://blog.csdn.net/xianlingmao/article/details/7919597)
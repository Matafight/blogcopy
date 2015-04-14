title: "Prtools 笔记"
date: 2015-04-11 10:05:51
categories: 机器学习
tags: Prtools
---
**help mapping
**
按照帮助文档的解释：
![](http://i.imgur.com/zWSjeEv.png)
**A=gendatd([50,50]);
w=knnc([],3); %untrained
V=A*w;
实际上等价于:
V=knnc(A,3);**

经过实验确实等价。（利用struct(V)来查看mapping信息）
![](http://i.imgur.com/1t6qGd8.png)

v的类型是prmapping
![](http://i.imgur.com/Gf2UN2e.png)

w的类型是untrained mapping -->knnc

然后第二行gendatd()生成了两类各有五十个样本的数据，乘上W的意思就是说这些样本要用knnc来分类，分类产生的结果V就是trained mapping 了。

这里v的类型是prmapping 中的 trained mapping
实际上v 是由下面这个函数产生的 V=prmapping(mapping_fule,'trained'....);
![](http://i.imgur.com/s5TtyZQ.png)

也就是说mapping 有 untrained ,trained,fixed,combiner 四种类型。
mapping 都是由prmapping 这个函数返回的，上面四种类型分别对应这prmapping 这个函数的四种参数
但是有一个参数是它们所共有的，就是 MAPPNIG_FILE ，其实就是分类器的文件名，比如说knnc 等。

上图中的例子里面:
A=gendatd([50,50],10);
B=gendatd([50,50],10);
W=knnc([],3);%untrained
V=A*W; %trained
D=B*V; %fix mapping
貌似投影的话没有上面对应的等价？

![](http://i.imgur.com/WoI2lho.png)

说明fixed mapping 与 trained mapping 其实差不多。只不过trained 是从数据中训练出来的.fixed 是由参数直接指定的

untrained mapping 可以用prmapping（参数） 来产生，也可以用MAPPING_FILE(参数)来产生

**help datasets**

新建一个prdataset
A=gendatb([50,50]);
![](http://i.imgur.com/0YwXlDl.png)

A的结构如上图所示，我感到疑惑的是lablist 为什么会是2乘4的cell类型，按照帮助文档中的解释 lablist应该是个2乘1 的矩阵，因为一共就两个类。
getlablist(A)返回如下值：
![](http://i.imgur.com/uOVoJLx.png)

这说明返回值跟说明是相符的。但是通过A.lablist方法得到与struct(A)相对应的结果:
![](http://i.imgur.com/F7KOXJA.png)
继续查看
![](http://i.imgur.com/uAikSm8.png)
![](http://i.imgur.com/5LzttAG.png)

说明了A.lablist 里面存了不止有classname 还有 prior 和 labtype。只不过getlablist(A)只返回了classname

gettargets(A)返回了一大串：
![](http://i.imgur.com/fiT1rnU.png)

可是既然labtype是crisp，那么target应该是[]才对，于是继续用A.targets
![](http://i.imgur.com/vlWn27q.png)

得到了与文档相符的结果。我已经晕了！
这两种返回的方式到底用哪个。。
**附录**
![](http://i.imgur.com/zZECfyt.png)
dataset的每个元素都可以通过类似于类调用的方式来获得。
![](http://i.imgur.com/sMl566w.png)


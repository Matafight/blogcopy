title: python numpy中array 与 Matrix 的区别
date: 2015-10-31 09:31:53
categories: 机器学习	
tags: python
---
**两种类型**
ndarray 与 matrix 是python中常用的存储数据的类型

numpy 中matrix 类型是2维的，而array可以是多维的，所以matrix可以看作是array的一个子集matrix支持的操作array 都支持，最常见的就是转置，numpy.transpose()
numpy中ones 和 zeros 返回的是narray类型的数据，值得注意的是当两个matrix 类型的数据相乘时是做的矩阵乘法，或者这样说：两个操作数中至少有一个是matrix类型时，做的是矩阵乘法，返回的是matrix类型，当两个操作数都是array类型时做的是element-wise 的乘法，即对应元素相乘，返回的类型还是array。

还有一种常见的存储数据的类型是list,使用list需要注意的是:当用list类型存储labels的时候，如果想用labels做矩阵运算,需要先把它转化为matrix 或 array 类型，转换后labels还是行向量，一般都要先转置变成列向量来处理


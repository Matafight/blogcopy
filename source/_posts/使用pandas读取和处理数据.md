title: 使用pandas读取和处理数据
date: 2016-08-04 10:36:47
categories: machine learning
tags: pandas
---
**1.介绍**
=========
pandas 是python 中一个常用的开源的进行数据处理的package，它有很多功能，但是我常用的就是使用pandas 来读取和处理数据.
```bash
import pandas as pd
```
pandas中数据存储的基本单位是data_frame，不同于numpy 中的array，dataframe 有很多种对数据操作的method。

读入数据:
```bash
df = pd.read_csv(filename,sep=' ',header=None)
```
read_csv方法有很多参数,参见：http://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html
常用参数：

file_path:文件路径和名字
sep : 一行的数据之间使用什么符号分割的,一般是空格，csv文件是逗号
header: 用来当列名的行号，如果没有传入names参数，则header = 0 ,如果传入了names，则为None
names:列名，默认为None
如果传入header =None ，则列名为[0,1,..]这样的整数。
如果仅仅传入上述参数，行的索引也为从0开始的整数。

<!--more-->

**2.data_frame 选择数据**
======================

官方文档：

http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-label
Pandas 中选择数据有好几种方法，最简单的分别是:

Selection by label
=================

围绕的方法是df.loc[row_indexer,column_indexer]
---------------------------------------
1.选择行:
```bash
df.loc['a']
df.loc['a':'c']
df.loc['a':]
```
2.选择列:
```bash
df.loc[:,'col_name']
```

Selection by position
==================

围绕的方法是df.iloc[row,column]
---------------------------

1.选择行：
```bash
df.iloc[1:3]
df.iloc[:3]
```
选择指定行，指定列
```bash
df.iloc[1:3,2:4]
df.iloc[:,1:3]
```
2.选择列：
```bash
df.iloc[:,3]
```
除了用df.loc和df.iloc来选择行和列外，还有其他方法
===========

使用Attribute access 来选择列：
------------------------
前提是知道列名:
```bash
df.a
df[a]
```

上述两种方法的区别是df[a]可以新增一列，列名为a,df.a只能读取

使用[]operator来选择行：
---------------------
```bash
df[:5]
选择前五行
```
**data_frame其他相关用法**
=====================

df.values
返回一个多维数组

df.drop("row_label")
df.drop("column_label",axis=1)
分别删除行和列

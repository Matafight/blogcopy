title: "数据预处理 方法"
date: 2015-03-09 14:58:32
categories: 机器学习
tags: normalization
---
The family of pre-processing techniques to which your question is directed is actually comprised of three groups of techniques: rescaling, normalizing, and standardizing. Of course, in practice, these terms are used almost interchangeably.

Use or don't use these terms however you wish, though separating the three
might help reconcile all of those techniques you see in the literature or that are used in practice.

**缩放****Rescaling**: for instance, to change the unit of measure; to rescale you add/subtract a constant then multiply/divide by another constant. This is easier to show than describe, e.g., to convert from Celsius to Fahrenheit, you add 32 to the Celsius temperature then multiply that value by 9/5.

比如说 x-min/(max-min) 将feature 范围控制在0-1

**正则化**
**Normalizing**: dividing by the norm. So for instance, if one of the rows in your data set is [1.23, 2.21, 0.84, 3.54, 1.90], then diving it element-wise by its norm (which is about 4.8 in this case). When you do that, the normalized row you get is [0.255, 0.458, 0.174, 0.734, 0.39]. If you use Python+NumPy, then the expression is normalized_row1 = row1 / LA.norm(row1), with the predicate import statement import numpy.linalg as LA')

正则化的过程是将每个样本缩放到单位范数（每个样本的范数为1），如果后面要使用如二次型（点积）或者其它核方法计算两个样本之间的相似性这个方法会很有用。

Normalization主要思想是对每个样本计算其p-范数，然后对该样本中每个元素除以该范数，这样处理的结果是使得每个处理后样本的p-范数（l1-norm,l2-norm）等于1。

             p-范数的计算公式：||X||p=(|x1|^p+|x2|^p+...+|xn|^p)^1/p
该方法主要应用于文本分类和聚类中。例如，对于两个TF-IDF向量的l2-norm进行点积，就可以得到这两个向量的余弦相似性。

**标准化**
**Standardizing**: refers to a two-step process of subtraction and division, e.g., to get a variable in 'standard normal' form, you subtract the mean and divide by the standard deviation, after which your random variable has a mean of 0 and
an SD of 1.

A few 'rules of thumb':

in my experience, i have found the most useful normalizing/scaling/standardizing technique is standardization

In particular, the majority of the time, the ML algorithm imposes one of two constraints that i need to satisfy in this pre-processing step. The first is zero mean/unit variance. For that calculate the mean and standard deviation for each row in your data set; so for a given row, subtract that row's mean from each element in the row, then divide each element by the standard deviation

The second constraint that arises quite of often is a precisely bounded range, e.g., -1 to 1. For that, first calculate the range and the midrange. The latter is calculated by summing the min and max and dividing by 2; the former is just max minus min. The standardized data row is two times the original data row minus the midrange divided by the range.

Everybody is working with big data these days. In a lot of those instance, the data matrix is highly sparse. If so then the pre-processing technique you choose must preserve that sparsity, i.e., make sure the 0s remain 0s. An example: if you intend to avail yourself of that sparsity for instance by compressing the data matrix.

The best example i can think at the moment: the data matrices inside most recommendation engines. For these, you don't want to preserve that sparsity but rather to get rid of it by some sort of 'reconstruction' technique, which is nearly always some form of matrix factorization. In those instances, i suggest either using a pre-processing technique that preserves the 0s or even better, mask' those missing values (which are often, though not always represented as 0s) prior to applying your pre-processing script (for instance, using NumPy's mask class; R, regrettably, does not appear to have such a class).


**quora**  http://www.quora.com/What-are-the-ways-to-normalize-the-features-for-statistics-or-machine-learning-software
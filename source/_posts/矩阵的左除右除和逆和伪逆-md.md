title: "矩阵的左除右除和逆和伪逆.md"
date: 2015-04-02 08:52:04
categories: 机器学习
tags: 矩阵
---
**矩阵的左右逆和伪逆** http://blog.csdn.net/xdfyoga1/article/details/38407395
通常我们所说的逆是在矩阵两边都能成立的逆


矩阵的左除与右除
A*X=B
X=inv(A)*B 

这里求矩阵X 通常的做法是求A的逆然后用A的逆左乘B,但是有很多情况下A的矩阵很接近奇异,甚至就是奇异的,这时候求 A的逆经常会求不出来,所以,这时可以使用左除.

```bash
X=A\B // 左除对应的式子是 A*X=B  等价于 inv(A)*B
X=B/A // 右除对应的式子是 X*A=B  等价于 B*inv(A)
```


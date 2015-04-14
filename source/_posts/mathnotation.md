title: "latex 常用数学符号"
date: 2015-04-03 10:15:05
categories: 工具
tags: latex
---

\int_a^b f(x)\, \mathrm{d}x
积分
\sum \nolimits _1^100
求和 且 \nolimits 强调上下限位于符号的右侧 \limits 表示上下限位于符号上下

\stackrel{}{} 堆叠符号
\dots \vdots  \ldots

{upper \choose  lower}
类似于二项式系数的效果

\left.\frac{\partial f(x,y)}{\partial x}\right|_{x=0} 
/left 和 /right 左侧可用.代替 

矩阵
\begin{array}{列格式}
第一行\\
第二行\\
....\\
\end{array}
每一行中各个列之间用&隔开

自动编号的单行公式环境是:
\begin{equation}
公式
\end{equation}
不参与编号的单行公式环境是:
\begin{displaymath}
公式
\end{displaymath}
可以简写成 \[公式\]
人工编写的单行公式可以使用TEX原有的行间公式标记
$$公式 \eqno 编号$$ 或 $$公式 \leqno 编号$$


align 环境
\begin{align}
\pi & \approx 3.141592653589
\me & \approx 2.71828
\end{align}
可以用&指定各行公式上下对齐的位置,通常在等号或关系运算符处对齐


title: Cython简单介绍
date: 2017-05-12 15:01:33
categories: 机器学习
tags: Cython
---

### 简介
前一段时间想自己实现并改造因子分解机，libfm的源码是用C++写的，虽然高效但是代码的可读性较差，而python虽然有较好的可读性，而且python有方便的科学计算工具包，但是python本身的运行效率不太高。幸运的是，由于python的胶水语言的特点，有很多将python与C等相对效率高的语言结合起来的方法，由python做前端计算，而C等做后台计算。既实现了提升速度的目的，写起来又简单。

Cython 全称是 C-extension for python,是一种用于编译python和cython语法的编译器。cython的语法与python很相似，Cython的编译器能够将用Cython语法编写的代码转换成C代码，然后执行高效的C代码。用户只需编写类似python语法的Cython代码就可以。

### 基本用法

**基本流程**
Cython文件的后缀为.pyx, 该文件先由Cython编译器编译为C文件，之后C文件再由系统的C编译器编译成.so文件(windows平台是.pyd文件)，之后python就能import该模块了.
**安装与导入**
python中通常使用distutils来构建或安装新的模块，这需要编写setup.py文件。文件的内容挺简单，一个例子如下，
```python
from distutils.core import setup
from Cython.Build import cythonize
import numpy
setup(
    name = 'higher-order-fm',
    ext_modules = cythonize('higher_fm_cython.pyx'),
    include_dirs=[numpy.get_include()]
)
```
```python
#发布模块
python setup.py build_ext --inplace
#之后可以使用 import class_name 在python代码中使用模块，class_name是在Pyx文件中定义的类名。
```
**其他注意事项**
函数定义可以采用 def和cdef两种形式，采用cdef定义的函数对于导入 在pyx中定义的 模块的python代码是不可见的，只有def定义的函数可见！！而且cdef定义的函数内所有的变量都要显示声明其类型，看上去在写python，但实际上是在写c。


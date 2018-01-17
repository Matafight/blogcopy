title: "python 安装第三方模块"
date: 2015-05-07 22:08:19
categories: python
tags: python
---
python 下安装第三方模块有三种方式：

1. 源码安装
一般的安装方式都是 cmd cd 到目标目录执行 python setup.py install

2. 脚本安装

比如安装pip 的安装脚本getpip.py [https://raw.github.com/pypa/pip/master/contrib/get-pip.py](https://raw.github.com/pypa/pip/master/contrib/get-pip.py),直接执行 python get-pip.py执行安装

3. 包管理器安装

常用的包管理器有easy_install 和 pip ，其中pip是easy_install的升级版。
easy_install在python2.7中自带了，目录是
![](http://i.imgur.com/8VzX7fO.png)，安装后的pip也是在这个目录，所以，安装其他模块时要cd到这个目录 执行 pip install XXX安装。

.whl 格式的包也可以用pip离线安装，执行 pip install path

[http://jiayanjujyj.iteye.com/blog/1409819](http://jiayanjujyj.iteye.com/blog/1409819)
[http://www.cnblogs.com/skynet/p/4135477.html](http://www.cnblogs.com/skynet/p/4135477.html)
title: "使用vim编写c++"
date: 2015-05-24 16:31:47
categories: vim
tags: c++
---
**before**

首选编译器当然是vs2013了，但是受不了它的打开速度，也受不了每想写一个小程序都要新建一个项目。太冗余了！so！编辑器之神vim当然要闪亮登场了。

vim只是一个编辑器，不能编译程序，所以需要额外的编译工具，除了GUN的g++外，微软的cl工具也可以用，对于我这种已经装好了vs的人，使用cl就很方便了。
首先配置一下在命令行中使用cl工具

**step1:**添加环境变量:PATH:C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin
				  INCLUDE:C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\include
				  LIB:C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\lib

**step2:**直接在cmd里面输入cl查看能不能显示版本信息,应该就可以使用了。

出现的问题：
执行 cl命令时遇到提示  LINK : fatal error LNK1104: 无法打开文件“kernel32.lib”， 则把C:\Program Files\Microsoft SDKs\Windows\v7.0A\Lib目录下的kernel32.lib拷贝到D:\Program 
Files\Microsoft Visual Studio 12.0\VC\lib目录下即可。如果出现其他的lib文件无法打开，道理同理。

配置好就可以了。

之后直接新建一个cpp文件并用gvim编辑，在gvim里面可以直接输入命令
```bash
:!cl test.cpp
```
就可以编译生成可执行文件了。其中感叹号表示在cmd中执行后面的命令，与在cmd中直接执行 cl test.cpp 效果一样。
cl 命令还有很多其他的选项，以后有需要再学吧

PS:意外发现windows的powershell 已经很强大了，至少比cmd强大很多
![](http://i.imgur.com/uSZ1K6B.png)

附录：
[http://www.cnblogs.com/mizhongqin/archive/2013/03/11/cmd_cl-exe_vs2010.html](http://www.cnblogs.com/mizhongqin/archive/2013/03/11/cmd_cl-exe_vs2010.html)

[ "cl命令选项"](http://www.lellansin.com/%E5%BE%AE%E8%BD%AF-cl-exe-%E7%BC%96%E8%AF%91%E5%99%A8.html)
title: "python 中文乱码"
date: 2015-04-29 15:55:59
categories:
tags: python
---
python中的编码:
1. unicode
2. utf-8
3. gbk

python中可以定义unicode 对象，字符串可以以unicode编码存储，也可以以utf-8 编码存储。 输出的时候，如果是unicode 编码存储的字符串，系统自动将unicode 编码转换为系统编码，一般为gbk，如果是以utf-8存储的字符串，系统则按照utf-8的格式输出，所以会输出乱码，解决办法就是先将字符串decode 为unicode,然后encode 为gbk，或者直接输出就行。

unicode编码应该更底层一点，可以自适应系统的编码，utf-8和gbk编码的字符串如果遇到不同编码的系统则会乱码。
![](http://i.imgur.com/qD4Ugv0.png)

[更详细的说明](http://www.jb51.net/article/26543.htm)

title: python中的编码问题
date: 2017-06-30 11:08:55
categories: python
tags: 编码
---
在写python程序的时候常常会因为字符编码问题而出错，尤其是在读取文件的时候，一般需要按照文件本身的编码来读取，否则非常容易出错，一般我们在python代码的第一行都声明编码方式为utf-8，
```python
# _*_ coding:utf-8_*_
```
如果没有申明编码方式的话，程序默认编码为ascii，而ascii不支持中文显示。当然这只是众多编码问题中的一种，实际运行时还会发生很多意想不到的问题，因此，搞懂python的编码规则很有必要。

python2和python3的编码总的来说是不同的。下面分开介绍python2和python3中的编码。

## python2中的编码
刚开始发明计算机的时候，计算机只有ascii这一种编码方式，而ascii是一种单字节编码系统，也就是说每个字符只需要用一个字节来存储，计算机也很好识别，因为一个字节就对应着一个字符。但是随着计算机在全世界的发展，世界各地的人们对于其他字符有迫切的需求，比如中文输入，那么就需要对中文编码，因此发展出来了GBK，这种编码需要多个字节存储一个字符，不同的编码也对计算机处理字符串带来了困难。因此，后来人们发明了unicode编码来一统江湖，unicode本质上来说与ascii，gbk这种编码方式不同，因为unicode本质上存储的是字符串，而不是ascii这种字节串，所有的编码方式都可以通过decode方法转换成unicode字符串，而unicode也可以通过encode方法编码成别的字节串。

在python2中，关于字符串的主要有两个类，一个是str，一个是unicode，str可以有不同的编码，如ascii，utf-8等，我理解的unicode就是字符串，而str是字节串
```python
>>> a=u"中文"
>>> len(a)
2
>>> b=a.encode('utf-8')
>>> len(b)
6
```
可以看出"中文"的unicode长度就是2，而utf-8编码的长度是6，表示占用了6个字节。
将字符串存储到文件中时一般要存储经过编码的字符串，而不是直接存储unicode，如果没有显式编码，程序会采用默认的编码方式存储字符串。而读取文件内容时，也要显式指定文件的编码方式读取，读进来的是对应编码的字符串，一般为了操作方便，需要将输入内容decode成unicode。


## python3中的编码
python3默认编码方式为unicode，python3中字符串也是有两个类，一个是bytes,另一个是str，不过python3中的str与python2中的str不一样，python3中的str默认就是unicode编码的，也就是说在python3中
```python
a="中文"
```
a就是默认的unicode编码，这样极大地方便了我们处理字符串，如果在python2中的话，a就是utf-8编码了(需要在第一行声明编码方式)，而在python2程序中处理的字符串一般都需要先decode成unicode，在python3中默认读入的就是unicode，因此会减少很多麻烦。bytes就是对应与python2中的str，是指采用别的编码的字符串如utf-8等，声明bytes类型的字符串需要显式声明。
```python
a=b"中文"
```
不过这行代码是错的，bytes类型只能接受ascii字符串，不能接受非ascii字符串，如果非要用这种声明方式的话，需要将中文转换成对应编码的十六进制，bytes中存储的就是各个字节。

参考：
[https://www.crifan.com/summary_python_string_encoding_decoding_difference_and_comparation_python_2_x_str_unicode_vs_python_3_x_bytes_str/](https://www.crifan.com/summary_python_string_encoding_decoding_difference_and_comparation_python_2_x_str_unicode_vs_python_3_x_bytes_str/)
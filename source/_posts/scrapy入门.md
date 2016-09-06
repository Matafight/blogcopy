title: 爬虫入门
date: 2016-09-06 16:33:23
categories: 爬虫
tags: spider
---
最近一直在做试验，实验跑得特别慢，经常一跑就要两三天，没有实验结果也不想写论文，所以，这两天就看了一点爬虫的基础知识。

主要是跟着这个教程来学：http://cuiqingcai.com/1052.html

最简单的爬虫只需要会使用urllib2库和正则表达式就可以了，不用去考虑多线程，登陆，验证码等问题。爬虫一般是用来在网络上搜集数据的，我们平时用浏览器向特定网站请求数据时，网站会返回html格式的文档，我们所需要的信息一般都包含在html文档中。
所以，爬虫首先需要模拟浏览器的行为向网站请求数据，网站会返回html格式的信息，然后用正则表达式从html中提取出我们需要的信息，这就是最简单的爬虫的所需要实现的功能。

urllib2 库是python中用来进行网络通信的基本库，利用它我们可以与特定网站进行通信和传输数据。

```bash
import urllib2
url = 'http://www.baidu.com'
#user_agent 用来伪装浏览器，header中还可以有其他key-value 对
user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'
headers = {'User-Agent':user_agent}
#构建一个请求，包含url 和 header 等相关信息
request  = urllib2.Request(url,headers = headers)
#使用urlopen 执行请求，返回值存在response中
response = urllib2.urlopen(request)
#html 内容存在content中
content = response.read().decode('utf-8')
#接下来从content中利用正则表达式提取自己需要的信息就可以了
```
上述使用方法在应对不需登陆的网站时是可以的，但是大部分网站是需要登陆的，所以需要输入用户名和密码进行登陆。也可以从文件中读取保存的cookie 进行登陆。实例代码如下：

```bash

import urllib2
import urllib
import cookielib

#将cookie读取到这个opener中，之后可以直接用这个opener去访问需要登录的网站
#也可以将cookie 保存到文件中，之后就可以直接从文件中读取
cj = cookielib.CookieJar()
opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cj))
opener.addheaders.append(('User-Agent','Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.76 Mobile Safari/537.36'))
opener.addheaders.append(('Referer','http://gsmis.nuaa.edu.cn/nuaapyxx/login.aspx'))
values = {
"__VIEWSTATE":"dDwyMTQxMjc4NDIxOztsPF9jdGwwOkltYWdlQnV0dG9uMTtfY3RsMDpJbWFnZUJ1dHRvbjI7Pj5P2nwDe6vBtb+hohKJ5E+WPJkXzQ==",
"_ctl0:ImageButton1.x":"25",
"_ctl0:ImageButton1.y":"25",
"_ctl0:txtusername":"xxx",
"_ctl0:txtpassword":"xxx"}
data = urllib.urlencode(values)
url = 'http://gsmis.nuaa.edu.cn/nuaapyxx/login.aspx'
resp  =opener.open(url,data)
print(resp.read())
```
上述代码中以post的方式传递数据，这里的键值对的值需要通过浏览器的开发者模式来查看,其他与服务端通讯的信息也可以查看：
![](http://7xiegr.com1.z0.glb.clouddn.com/login_nuaa.PNG)




参考：https://www.daniweb.com/programming/software-development/threads/296783/can-we-use-python-to-get-pass-password-protection-of-a-aspx-website

http://cuiqingcai.com/968.html

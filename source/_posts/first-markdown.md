title: "first markdown"
date: 2015-03-06 15:24:06
tags:
---
##感想
 github博客终于搭成了，很久之前就想搭建一个博客了，可是当时搜索了一下，感觉有点难，什么都不懂的样子，搞了一会儿就不想搞了，今天决定建这个博客是因为作为一名大四狗，感觉自己的技术实在是太渣了，以后找工作的时候连点像样的代码都拿不出来怎么办？？我真是感到深深地忧虑！所以，我想搭个博客，没事写点技术性的东西，也算是自己学习的历程吧！！希望自己能坚持地写下去！
 ##过程
  搭这个博客花了一上午的时间，github主要是提供了静态页面的存储，想写博客还要有一个静态页面生成器吧？而这个软件有好几种呢，我随便选了一个hexo，也没有跟其他的比如说jekyll 做太多的比较，就直接上了。	找了好几个教程对照着看，主要的是 [inruce](http://ibruce.info/2013/11/22/hexo-your-blog/) 和 [zipper](http://zipperary.com/2013/05/28/hexo-guide-2/)对照着做确实可以，可是我对完发现 发布的时候，也就是 hexo deploy 的时候提示错误，
  ```bash
  ERROR:deploy not found git 
  ```
  google 了一下，找到了官方的教程 http://hexo.io/docs/deployment.html 原来是因为没有 安装 hexo-deployer-git,ok安装完就可以了。
	![](http://ww2.sinaimg.cn/large/5e8cb366jw1e62o63tkv3j20dh078q5a.jpg)
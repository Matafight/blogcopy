title: "装个ubuntu"
date: 2015-03-20 15:34:46
categories: 工具
tags: 工具
---

先码晚上装。
一. 分配给linux系统空间（上一个win8.1C盘有100G 准备格了win8.1全分给linux），下载ubuntu最新版本 http://www.ubuntu.org.cn/download/desktop
二. 安装软碟通刻录系统到U盘 [软碟通](http://cn.ezbsystems.com/ultraiso/) ，打开ISO文件，写入方式选择USB-HDD+。
三. 安装EasyBCD [EasyBCD](http://www.onlinedown.net/soft/58174.htm)用于系统配置多重启动。
四. 插入U盘选择从u盘启动
五. 按照提示操作即可
七. 对预留的空间进行分区操作，分配四个空间 [参考](http://blog.sina.com.cn/s/blog_6cc181070101rwa0.html)
/ :20G 主分区，如果电脑上已经装了其他系统，这里可以设置为逻辑分区 设置为ext4日志文件系统
swap:与内存相同大小  设置为交换空间
/boot :300M 格式ext4 包含了操作系统的内核和在启动系统过程中所需要的文件 ext4日志文件系统
/home:剩下的全部空间 ext4文件系统

八. 分区完成之后会有一个“安装启动引导器的选择的设备”，记住这里的选择很重要，因为我们要从windowns引导下启动ubuntu，所以设备的选择取决你的 /boot 所在的盘符，所以看清楚在第八步下 /boot 所在的盘符。之后的步骤就很简单了。

九.安装完之后重启，会看不到Ubuntu的引导项，直接进入win7.此时需要进入win7在EasyBcd中添加开机引导项。
如图：
![](http://img.blog.csdn.net/20140302041430437?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamsxMjN2aXA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


后记：
本来是双系统win7 和 win8.1 ，我想把win8.1删掉装Ubuntu，于是在win7下格式化win8.1的c盘（系统自带的磁盘管理不能删除主分区，所以必须要下载一个分区软件来进行），这是我觉得最好的分区软件[DiskGenius](http://www.diskgenius.cn/),至少下载下来没有给我绑定一大票流氓软件，其他的从XX软件园下载下来的分区工具给我带来了各种垃圾，无力吐槽！ 好吧，用diskgenius强行格式化之后，在win7删除了对win8.1 的引导项。我以为可以了。重启一下，竟然进不去系统了，不知道怎么搞的win7的引导也没了 ==！！！ 然后我拿着刻好的U盘装Ubuntu ，想着没了就没了吧，以后就用Ubuntu了。哪知道Ubuntu装好之后重启一下也不能进去，黑屏！还是找不到引导项，大概是因为系统是靠win7来引导的，必须要在win7里面设置好引导Ubuntu才能行，可是现在连win7都进不去不是什么都不行了吗？？这时我想起了PE ，于是拿U盘用大白菜刻录个PE系统，以前装系统的时候一直没出过什么问题，所以听说过pe但是没用过。OK，开机从u盘启动进入PE，PE里面有各种工具,包括引导项修复，磁盘分区工具等等，真是灰常强大，直接就给我修复好了！真是碉堡了！！PE真是装系统的神器啊。
修复好了之后能进入win7，之后在win7里面设置一下Ubuntu就可以了！这下大功告成了！！

**参考:**
1. [CSDNBLOG](http://blog.csdn.net/jk123vip/article/details/20255681)
2. [linux公社](http://www.linuxidc.com/Linux/2014-10/108430p3.htm)
3. [安装Ubuntu硬盘分区方案](http://www.cnblogs.com/shenliang123/p/3196743.html)
4. [安装Ubuntu新手分区建议](http://blog.sina.com.cn/s/blog_6cc181070101rwa0.html)
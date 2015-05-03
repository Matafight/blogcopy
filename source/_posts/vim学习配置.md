title: "vim学习配置"
date: 2015-03-22 09:34:46
categories: 工具
tags: 工具
---
1. 在软件中心搜索gvim装了图形界面版的vim
2. vim 的配置文件路径是 ／etc/vim/vimrc 或 /etc/vim/gvimrc 这里的配置文件是针对所有用户的,如果想只配置当前用户的配置文件在目录 ~/ 下新建.vimrc文件
3. vim每次启动的时候都会读取配置文件对程序进行配置.
4. vim有很多[插件](http://www.vim.org/scripts/script_search_results.php?order_by=downloads) 解压到$home/.vim 目录下
5. 插件的安装方法:
			a.在home下 新建目录.vim
			b.在 .vim 目录下解压插件
			
		http://easwy.com/blog/archives/advanced-vim-skills-taglist-plugin/

6. vim 常用命令
	    a. 光标移动初级版 h,j,k,l 分别对用 左,下,上,右
     	b. 光标移动高级版: ^:到一行的第一个不是blank字符的行头. 
					   0: y移动到一行的开头.
					   $:移动到行尾.
					   g_ : 移动到行尾第一个不是blank的字符.     
					   gg: 移动到第一行
					   NG :移动到本文的第N行
					   G:移动到最后一行
					   w:到下一个单词的开头
					   e:到下一个单词的结尾(对于中文来说vim把连在一起的中文当做一个单词)
   	                   b:到前一个单词的开头.
        c.插入 删除 复制 粘贴
                		i:在光标前插入
                		a:在光标后插入
                		o:在下一行插入
                		O:在前一行插入
                		dd:删除当前行
                		dw:删除当前单词
                		x:删除当前字母
                		yy:复制当前行
                		p:粘贴
						u:撤消(undo)
	                    ctrl+r : redo
                
        d.块操作
	                  v:可视化选择,可配合复制粘贴
                      ctrl+v:同上
					  可用块操作来进行块注释:
					  ^ + <C-V> + j or k + I + "注释符" + esc

        e.自动提示
					ctrl+n or ctrl+p

7. windows下Gvim配置python开发环境 [超详细](http://www.furion.info/24.html)

8. windows 下配置ctags+taglist
         
   先去下载[taglists](http://www.vim.org/scripts/script.php?script_id=273)，解压文件得到doc和plugin两个目录，两个目录下的文件分别是taglist的提示文件和taglist的插件，将doc下的taglist.txt复制到vim安装目录下的doc文件夹中，将plugin目录下的taglist.vim 复制到plugin文件夹。

   之后下载[ctags](http://ctags.sourceforge.net/)，解压，将其中的ctags.exe 直接复制到gvim安装目录下（与vim.exe在同一个目录）
   之后在_vimrc 配置文件中加入如下两句：
	set tags=tags：
	set autochdir
   这个命令让vim首先在当前目录寻找tags 文件，如果没有找到，使用第二条命令自动递归父目录继续寻找。
	
   配置完毕，在gvim中输入 :TlistToggle 切换taglist窗口

   切换到代码所在文件夹，在cmd 中输入 ctags -R （要配置gvim安装目录的环境变量）生成tags文件

9. gvim 下字符的查找替换:

   语法为 :[addr]s/源字符串/目的字符串/[option]
   全局替换命令为：:%s/源字符串/目的字符串/g
   [addr] 表示检索范围，省略时表示当前行。
   如：“1，20” ：表示从第1行到20行；
   “%” ：表示整个文件，同“1,$”；
   “. ,$” ：从当前行到文件尾；
   s : 表示替换操作
   [option] : 表示操作类型
   如：g 表示全局替换; 
   c 表示进行确认
   p 表示替代结果逐行显示（Ctrl + L恢复屏幕）；
   省略option时仅对每行第一个匹配串进行替换；
   如果在源字符串和目的字符串中出现特殊字符，需要用**”\”**转义

   e.g.  
```bash
:%s/perm/pm/g  
表示将perm替换为pm，但是这个命令会将含有perm作为子串的字符串中的perm替换为pm，如果要全字符匹配，命令为
:%s/\<perm\>/pm/g
```
**参考**：[http://wdicc.com/search-in-vim/](http://wdicc.com/search-in-vim/)
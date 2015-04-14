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
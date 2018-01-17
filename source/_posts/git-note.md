title: git note
date: 2018-01-12 14:00:24
categories: git
tags: git
---
## git环境中的三个重要概念
- 工作目录
- 本地仓库
- 远程仓库
用户在工作目录中更改的文件通过stage 和 commit 提交到本地仓库，本地仓库通过push命令同步到远程仓库

## checkout命令
- 新建分支: git checkout -b 'branch-name'
- 切换分支： git checkout 'branckname'
- 查看分支： git branch
- 如果commit了错误的代码怎么办？ 首先通过git log 命令查看历史版本，之后通过 git checkout \<hash-name\>切换到指定位置。
![](http://7xiegr.com1.z0.glb.clouddn.com/git1.png)
![](http://7xiegr.com1.z0.glb.clouddn.com/git2.png)
HEAD指针指向新的位置后，如果在此处新建一个提交会产生一个新的未命名的分支
![](http://7xiegr.com1.z0.glb.clouddn.com/git3.png)
如果此时通过 git checkout master命令回到主分支的话，该新的提交会丢失，怎么做才能不丢失呢？通过git checkout -b newbranchname 在该新的提交的基础上新建一个分支。这样即使回到了主分支，newbranchname这个分支也不会丢失。在master分支通过执行 git merge newbranchename 合并分支。
![](http://7xiegr.com1.z0.glb.clouddn.com/git4.png)

## 删除本地和远程库上的master 分支？（主要是因为已经在dev分支上做了很多改变，merge 到 master 分支上会产生很多麻烦，不如直接删除master 分支）
- git checkout -b dev //新建并切换到 dev分支

- 先git checkout dev 到 dev分支

- 在github网页端设置 default branch 为 dev 分支
- git branch -D  master //删除本地master分支
- git push origin :master //删除远程master 分支

- 然后可以重新新建一个master 分支，push当前代码到master 分支上，再设置default branch为master 分支

## 如何只clone 除了master分支之外的其他分支？
- 先使用标准的clone 命令，git clone XXX.git

- 之后 git branch -a 查看所有分支，会显示:
    - origin/master
    - origin/dev

- 然后直接： git checkout origin/dev 切换到dev分支

## 如果不小心 commit 了一个错误的代码，或者merge出现了错误怎么恢复之前的状态？
- 先用git -reflog 查看仓库commit状态，会看到这样的状态：
![](http://7xiegr.com1.z0.glb.clouddn.com/git5.png)
- 如果想恢复到某个具体地时刻点，可以强制转换:
- git reset --hard @{2}，表示恢复到 HEAD@{2}时的状态

## git fetch,git merge 与 git pull 的区别与联系
- git fetch <远程主机名><分支名> 用来获得远程主机上的分支的内容
    - git fetch origin master 取回origin 主机上的master 分支
- git branch -r ,可以用来查看远程分支
- git branch -a 查看所有分支，包括本地分支和远程分支

- 当 使用命令 git fetch origin master 之后，可以 git checkout -b newBran origin/master 创建基于 origin/master分支的本地分支

- 注意，fetch下来的分支并没有与本地分支合并，

- 需要使用 git merge origin/master 将其与本地分支合并

- 而git pull origin master 命令，则是上述两个命令的合体版本，先获得origin/master 上的内容然后与本地分支合并

## git push 命令
- git push <远程主机名> <本地分支名>：<远程分支名>

    - 如果省略远程分支名，则表示将本地分支推送到与之有追踪关系的远程分支，如果该远程分支不存在，就会新建一个远程分支。

    - 如果省略本地分支名，表示删除远程分支
        - git push origin :master

## 如何将git add 与 git commit 缩成一步写？

   - 使用git commit  -am "message"
## git 查看远程其他分支，并切换到其他分支
   - git branch -r 查看
   - git check origin/otherbranch 切换
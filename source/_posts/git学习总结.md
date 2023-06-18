---
title: git学习总结
date: 2017-11-17 09:54:33
update: 2017-11-17 09:54:33
categories: 开发
tags: 
 - git
---

对于Git学习的一些整理，包含常用命令整理

<!--more-->

个人觉得，对于一些开源工具，最好的学习资源还是其官网，我们就先来分析一波Git究竟是做什么的。

### 概念解析

git官网的定义是：Git是一种免费并且开源的分布式版本控制系统，被设计用来快速高效地处理堆积成大工程的每一块小部分。（个人翻译，不喜勿喷）

Git简单易用、占用空间小并且性能优越。它远超过一些伴有类似廉价的本地分支、方便的阶段区域和多工作流特点的配置管理工具（SCM，Software Configuration Management），比如SVN、CVS、Perforce和ClearCase这些。

Git允许同一组下的人们同一时刻在相同的文档上工作（通常是代码），并且不会踩到其他人的脚趾（形容两个人同时在相同的文档上工作也不会发生冲突）

### 特性

- 小而快速（Small and Fast）

- 分布式（Distributed,这也是它比SVN优势明显的地方）

- 数据保证（Data Assurance）

- 分阶段区域（Staging Area）

- 免费并开源（Free and Open Source）

### 教程

初级教程：<a href="https://try.github.io/levels/1/challenges/1">Try Git</a>

高级教程：<a href="http://gitreal.codeschool.com/levels/1/challenges/1">gitreal</a>

官方书籍：<a href="https://git-scm.com/book/zh/v2">Pro Git</a>

### 使用步骤（包含常见命令）

1.初始化
```
git init
```
init命令会创建一系列git结构的文件

仅对一个制定目录创建git结构时使用，用于将某个目录交给git管理

2.查看git状态
```
git status
```
可在任何时间阶段使用， 以查看当前git管理下的文件状态

3.将文件交予git管理
```
git add filename
```
对于在git init后的目录里的每一个文件都有tracked和untracked两种状态，也就是**是否被git追溯（管理）**

对于每一个文件，要么通过git add交由git管理，要么加入ignore行列，不然新创建的文件就会处于不受git管理的“游离”状态。

当对于新创建的文件操作git add之后，文件就会处于Staging Area（阶段区域），在commit之前还尚未放进git的仓库（repository），所以在文件到仓库之前，我们可以将文件添加（add）进或是移出Staging Area（暂存区）

当然我们也可以对文件进行批量添加到暂存区，比如git add '*.txt'来将所有后缀为txt的文件添加进暂存区（注意引号）

4.将暂存区的文件提交到仓库
```
git commit -m "提交注释"
```

5.查看提交历史
```
git log
```

6.将本地仓库与远端仓库建立关联
为了将本地仓库推到远端Github服务器中（Push local Repo to Github Server），我们需要添加一个远端仓库（add a remote repository）
```
git remote add origin `Remote Repository URL`
```
这条命令初学者看起来晦涩难懂，我也是研究了很多遍才大致理解，首先这与普通的git add不同的是，git add是将本地新建的文件添加到git的暂存区，而git remote add是表示添加一个远端仓库，与本地的仓库建立关联。

默认取名origin，直观易懂，后面的URL也就是远端仓库的地址，虽然在远端（Github）创建仓库的时候我们创建了名字，但现在毕竟本地还没有那个仓库，所以相当于先在本地创建一个空名字，以备和远端的实体仓库对应。

> 当你使用git remote命令时，可以看到你add的“虚拟”远端仓库，它们的地址就是你add时最后的URL

7.将本地仓库内容推到远端
```
git push -u origin master
```
我们表示远端仓库的代号是origin，默认的本地分支名是master，-u是告诉git去记住这两个参数（origin master），这样下次提交就可以简化为git push

-------------------------------------------------------
经过上面的7个步骤，我们就完成了在本地创建git仓库，并创建文件交由git管理，并将本地仓库与远端仓库进行关联，再将本地仓库的文件上推到远端仓库

-------------------------------------------------------

接下来是关于远端发生变更，本地来查看（比如其他人fork了你的仓库，并且commit了内容，并pull request）

8.将远端仓库的最新文件拉到本地
```
git pull origin master
```
看懂git push origin master再来看这条命令就好理解一些，git pull是拉（下载）代码，也就是从远端往本地拉，然而其实本地的origin其实就是一个关联了远端仓库的钩子，你从origin拉代码就是从远端仓库拉代码，而拉下来的代码要放在哪个分支，这里选择了master主干分支

9.对比拉下来的远端仓库最新代码和本地的区别
```
git diff HEAD
```
HEAD是一个指针，指向我们最新commit的地方，不加HEAD，默认也是与HEAD对比

#### 其他命令

10.使用diff命令对比暂存区文件之间的区别
```
git diff --staged
```
前面提到过git add命令是将文件添加入暂存区，即add to stage

11.回撤add行为
```
git reset
```
我们可以通过git add将文件交由git管理，同样也可以通过git reset将文件脱离git管理，即移出暂存区（unstage file）。
当执行完git reset somefile后，somefile会脱离git管理，但文件依旧会在那里。

12.Undo
```
git checkout -- somefile（注意破折号与文件名之间有一个空格，否则会提示“未定义的选项‘--文件名’”）
```
可以通过git checkout命令将文件恢复成它们上一次提交前的样子

13.创建分支（branch out）
```
git branch branchname
```
当需要在主分支之外开发新功能或者修改bug，需要拉另外的分支以与主分支分离，来进行提交，当工作完成后可以再将其分支合并回主分支（merge back to master）
当直接输入git branch命令是查看当前分支情况，当git branch后面加任意名字即为创建新的分支

14.切换分支
```
git checkout branchname
```

15.清除文件
```
git rm filename
```
git rm命令不仅会删除你本地的实体文件，还会同时将这些删除行为追溯到git（stage these removal）
删除时同样可以使用通配符（wildcard）来一次清扫多个文件，例如git rm '*.txt'
当然这些删除行为也需要通过commit来通知仓库，尽管是删除文件，但对于git来说都是新增一次操作
此时在新建的分支，将所有的文件删除并提交，仅是对本分支的操作，当切换到主分支时，主分支依然是原样（分支相当于对主分支的拷贝，然而实际实现并不是通过拷贝实现）

16.合并分支
```
git merge branchname
```
git merge是将输入的分支合并到当前分支，一般是切回到主分支，执行合并命令，来将其他某一个分支合并到主分支

17.删除分支
```
git branch -d branchname
```
当分支合并回主分支后，就没用了，可以进行删除

### 使用场景

1.先在本地通过git创建工程，然后上传到远程新建的空仓库(Github)

2.在远端仓库（Github）创建文件或工程，克隆到本地，进行修改后再提交到远端仓库

3.fork其他人的仓库，若符合工程结构，则下载到本地可直接运行，修改，提交

参考链接：

- <a href="https://git-scm.com/">git官网</a>

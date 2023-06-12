---
title: 使用分支备份hexo博客
date: 2017-11-17 17:39:36
update: 2017-11-17 17:39:36
categories: 其他
tags: 
 - 其他
---

### 背景

使用一台电脑使用hexo创建博文、生成静态文件、发布，没毛病，但由于hexo在本地生成静态文件的模式，如果你换一台电脑呢？很明显你需要把原始电脑上的hexo文件夹拷贝到新电脑。这样带来的问题就是多台电脑上的hexo不能总保持同步，需要通过物理拷贝的方式，很不方便。

<!--more-->

### 正文

实现方式可能有多种，但我看到的主流方式是通过在远端仓库添加分支来保存hexo原始文件来将你的整个博客工程交由git管理。

hexo本身的确是通过git发布到远端的，也就是借助hexo-deployer-git这个nodejs模块，但其功能是将hexo发布目录（public）里的静态文件上传至远端仓库，因为远端仓库是通过pages服务直接访问仓库文件，需要保证仓库文件符合web可访问结构。

假如你把hexo原始工程目录上传到你的同名仓库来实现pages服务，是无法正常访问的。

那么我们可以再建一个仓库来管理hexo原始工程文件，但没必要，这里通过在原仓库上添加分支来保存hexo原始工程文件，达到管理的目的。

### 操作步骤

hexo主目录默认有一个.gitignore文件，暗示可以通过git管理hexo主目录

可以在hexo主目录通过git bash here唤出git bash，然后git初始化hexo主目录

```
git init
```

这样hexo主目录会多一个隐藏目录.git

创建本地分支，与远端的分支对应
```
git checkout -b source
```

然后将hexo主目录的所有文件按照.gitignore文件的配置交由git管理（hexo原主目录中的.gitignore配置即可，过滤了node_modules目录、public目录、deploy目录，node_modules目录是hexo使用到的相关node模块，不必上传，否则会出错，后面两个目录会在hexo g -d时发生变化，也不需要上传）

```
git add .
git commmit -m "commit hexo files to remote"
git push
```

添加远程仓库

```
git remote add origin URL
```

将本地当前分支（source）的文件上传到远端仓库的source分支（此命令会自动在远端仓库创建source分支）

这样就实现了将hexo工程交由git管理，并在远端仓库进行备份的操作

接下来就是异地恢复

首先，我们要下载的是hexo原工程目录的文件，而不是发布后的。当前远端仓库有两个分支，主干分支存储着hexo发布后的文件，source分支存储hexo原始工程文件。所以我们需要将远端仓库的默认分支设置为source（master仅用于访问，而source用于下载）

然后就是克隆source分支的文件
```
git clone URL 本地仓库名（任意起）
```

然后进入本地下载下来的仓库
```
cd 本地仓库名
```

这个时候能看到hexo原始工程的大部分文件，但由于上传的时候.gitignore过滤了一些文件没上传，所以需要在本地自行生成那些文件，方式如下：

```
npm install hexo
```
这个命令可以通过node的包管理器在当前目录下载hexo所需要的模块，即会生成一个node_modules目录

然后生成一些hexo初始配置文件
```
hexo init
```
因为当前目录已经包含一些hexo初始文件，所以敲该命令时会提示有些重复文件未生成，没有问题，我们只需要补全那些没有的初始文件，已存在的当然就用从远端下载下来的，比如主配置文件_config.yml

然后更新npm并下载发布工具
```
npm install
npm install hexo-deployer-git
```

这样就完成了本地恢复博客所有结构的操作。

最后，可以创建新的博文，然后更新到远端，然后在本地发布。

每次使用前只需要拉下最新的文件，在其基础上操作即可。

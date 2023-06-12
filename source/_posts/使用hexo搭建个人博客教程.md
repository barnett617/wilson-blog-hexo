---
title: 使用hexo搭建个人博客教程
date: 2017-11-08 10:25:17
update: 2017-11-08 10:25:17
categories: 其他
tags: 其他
---

用了一段时间的hexo，本着“授人以鱼，不如授人以渔”的理念，决定整理一篇hexo使用教程，分享大家。

hexo是一种快速、简单并且强大的博客框架，你可以通过使用markdown或者其他语言撰写post（帖子），然后hexo引擎将其生成静态网页文件（当然，官网的说法是“在数秒内生成伴有漂亮主题的静态文件”）并以博文的形式显现出来。

<!--more-->

### 写在前面

本文定位于面向非专业人士角度撰写的科普式教程，有专业基础的人可跳过大多步骤，仅阅读标*部分

### 背景

应@JXY邀，要我教他搭建个人博客，于是也就找了一个足够的理由来回顾一波如何使用hexo搭建个人博客。

既然给别人讲东西，那自然要先对讲的东西深入了解，不然就是误人子弟了。于是打开<a href="https://hexo.io">hexo官网</a>，发现！竟然！被！墙！了！好吧，后来发现是9点多被墙了一段时间（貌似是hexo使用的CDN服务Cloudflare被block了），介于避免日后再想参考官网文档时无法及时访问，索性这次就把官网的一些精华翻译下来，留作日后参考。

### *核心步骤

先看官方给出的安装步骤

![](http://trigolds.com/hexo0.png)

### 准备篇

首先你要做好准备掌握以下几项东西：

- git
- nodejs
- bash命令

好吧，这样讲未免显得门槛略高，其实hexo是一种非常零门槛的工具，只要你想学，任何没有专业知识的人都能用起来，只要照着做就可以了

### 环境搭建篇

#### 第一步：在电脑安装<a href="https://git-scm.com/download/win">git</a>（如果下载不了,可以通过我的网盘下载<a href="http://pan.baidu.com/s/1bpJapbx">git</a>）

git是一个伟大的版本控制软件，它的作用在于作为一个代码提交工具，将你编写的每一行代码（或文字）存放到一个代码仓库，它在其中起到记录你每一次提交历史的作用，这样你就可以对自己的每一次提交历史有直观的查看，并且可以对比不同版本之间的区别，还可以回退到某一个提交历史节点，还有balabala很多好处。

#### 第二步：安装nodejs

当你安装好了git以后，可以鼠标右键唤出git

![](http://trigolds.com/hexo1.png)

##### 方式一

然后你就可以很方便的安装nodejs了，通过如下命令先安装nvm（此方式适用于专业人士，非专业人士请参考后面下载安装包的方式二）

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```

nvm是nodejs version manager，nodejs版本管理器，用以在机器上安装并维护多个node的版本

![](http://trigolds.com/hexo2.png)

如果执行失败（看到很多error）,也可以通过我网盘的安装包进行<a href="http://pan.baidu.com/s/1c2KunAC">下载</a>

当安装完成后，通过cmd窗口键入nvm，如果如下图所示，就证明安装成功了

![](http://trigolds.com/hexo4.png)

##### 方式二


如果你想在电脑尚未安装git的情况下安装nodejs，或者安装nvm失败，你也可以通过传统的方式安装nodejs，如下：

访问<a href="https://nodejs.org/en/">nodejs官网</a>下载，或者通过我的网盘<a href="http://pan.baidu.com/s/1kV5SIR9">下载</a>安装包，安装方式如同安装其他软件一样

安装成功后打开cmd窗口，键入node --version，若如下图所示则证明安装成功

![](http://trigolds.com/hexo5.png)

nodejs是一个基于谷歌的**V8** Javascript引擎的Javascript运行环境。它利用一个事件驱动的、非阻塞I/O的模型使得其轻量并且高效。另外，nodejs的包生态系统<a href="https://www.npmjs.com/">npm</a>是世界上最大的开源库生态系统。

（包生态系统通俗来讲就是，一个会编程的你，某天写了一段代码，觉得其功能可被大家重复使用，并且很好用，于是你就把这段代码打了个包丢到网上，于是大家就都可以通过下载你这个包来使用这个功能了）

> V8是谷歌的一个开源高性能Javascript引擎，使用C++编写，并应用于Google Chrome浏览器（来自谷歌的开源浏览器）、Node.js等等。它按照ECMA-262标准实现了ECMAScript，并可运行在windows7及更高版本、macOS10.5及更高版本和使用IA-32、ARM或者MIPS处理器的linux系统。V8可以单独运行，或者被嵌入任何C++程序。（以上内容由本人翻译自<a href="https://developers.google.com/v8/">V8官网首页</a>，如有不准确之处请指正）

p.s. 关于nodejs和Javascript的关联，个人理解就是Javascript本来是运行在客户端（浏览器）的脚本语言，如果想要Javascript脚本在服务器端运行怎么办呢，此时就有了nodejs，由于V8引擎的存在，使得通过Nodejs可以在服务器端模拟出客户端的环境，从而来运行Javascript脚本

### 安装篇

在你的电脑有了git和nodejs以后，就可以正式安装hexo了

接下来你只需要在任何一个地方（比如桌面）右键鼠标唤出git bash

![](http://trigolds.com/hexo1.png)

然后粘贴这段命令

```
npm install -g hexo-cli

```

> 解释：这就是通过你刚才安装的nodejs里自带的包管理器npm安装了一个叫做hexo-cli的包（在npm里管这叫模块module）

> npm意思是使用npm执行命令

> install意思是要通过npm进行安装模块行为

> -g是对于install动作添加的参数，意思是global，全局安装

> hexo-cli是你已知需要安装的模块名称

安装成功后你就可以正式进行使用了

### 使用篇

此时你可以选电脑上的任何一个目录（文件夹）作为自己博客的存放地，比如

![](http://trigolds.com/hexo6.png)

然后在这里右键唤出git bash

![](http://trigolds.com/hexo7.png)

这个时候就用到**准备篇**里讲的git和bash命令的知识了，不过不要怕，其实经常用的也就那么几个命令，比如ls（就是list，列出当前目录的所有文件）、cd(change directory，切换目录)

首先，我们通过

```
hexo init 任意名称（比如 hexo init blog）
```
这就是通过刚才安装的hexo里的hexo init（初始化）命令创建了一个hexo式的文件夹，点进去看看都有什么

当然，你鼠标点进去只是在你的windows界面系统你这个当前用户进入到blog这个目录了，但git bash还停留在刚才的myblog那个目录，所以你需要在git bash里键入cd blog，来切换到blog目录，你可以再键入pwd（print working directory，打印出当前工作目录）来确认自己是否进到了blog目录

上述操作如下图：

![](http://trigolds.com/hexo8.png)

然后输入npm install，会根据博客既定的依赖安装依赖包

> Tip: 既然步入程序世界，理应准备一个好用（高效）的文本编辑器，总不能使用windows自带记事本来编辑这些东西吧，此处我推荐<a href="https://atom.io/">Atom</a>、<a href="https://code.visualstudio.com/">Visual Studio Code、<a href="http://www.sublimetext.com/">Sublime Text</a>都是主流的文本编辑器，具体下载使用请自行查阅

### 结构解释篇

_config.yml：主配置文件（博客写到一半时突然获知YAML原来是一种新型的配置文件语言，是一种比JSON还简洁和强大的格式，而yml则是这种配置文件的后缀）

package.json：应用数据，EJS、Stylus和Markdown渲染器已默认安装，当然你也可以卸载掉它们，如果你想

scaffolds（脚手架）：当你新建一篇帖子时，hexo会基于这个里面的模板生成一个文件

source（源）：这是你放置网站内容的地方，hexo会忽略隐藏文件或者以下划线 _ 开头的文件夹，除了_post这个目录。可渲染的文件，例如markdown文件、HTML文件会被hexo处理并放入public（发布）目录，其他文件将会被原样拷贝过去。

themes（主题）：Hexo会结合主题的网页内容和你的博文生成静态站点

当你第一次hexo init之后生成的一系列文件其实包含了hexo最核心的一些东西，其中包含如上所介绍的，包括主配置文件_config.yml、source目录里的_post（帖子）里放了一篇示例博文“hello-world.md”、themes里提供了默认主题landscape、scaffolds里提供了post文件的初始模子。这一切就足以让你快速体验如何部署一个博客在放在网上提供别人来访问。

### 博客部署

那么博文已经有了，怎么把它放到网上让所有人都能访问呢，这时候你需要把它部署到服务器，并提供访问地址，理论上来讲，你可能需要租服务器，并把博客丢上去，然后还要配域名，因为你总不可能让别人通过IP地址访问你的博客，这样看来，还有balabala很多事情要做，涉及到web应用部署发布的一系列知识。

但是一般hexo的使用都是联合<a href="https://pages.github.com/">Github Pages</a>或者<a href="https://pages.coding.net/">Coding Pages</a>一起使用的，Github和Coding都是在线代码仓库，（二者的区别在于前者是国际的，后者是国内的）可以远程寄存你的代码或文件，他们都各自提供了一种叫Pages的服务，可以使得你的**用户名同名仓库**可以作为域名对外公布访问，这样你相当于拥有了一个免费个人网站，并且还不需要注册域名和备案。

有了这个技术，我们就可以通过git把本地生成的博客静态文件（public目录里的）上传到这个仓库里，然后别人就可以通过访问这个仓库的方式来访问你的博客（只要你的代码结构符合网站基本结构）

### 快速上手篇

所以一切都配置好之后（按照个性化配置好你的_config.yml文件），你发布一篇博客到网上只需要三步：

#### 第一步：创建博文

在你的博客主目录下通过git bash键入hexo new 博客标题

#### 第二步：按照hexo的渲染方式将你的博文渲染成静态文件

继上一步之后，hexo g（完整命令是hexo generate，即生成文件的意思）

#### 第三步：按照_config.yml里配置的发布方式，将public里的静态文件上传至你的线上代码仓库（Github或Coding）

hexo d（完整命令是hexo deploy，即发布的意思）

仅此而已，这时打开浏览器，输入符合你pages的域名，看看自己的博客是不是显现出来了。

### 后记

其实hexo本身还是有不少的东西，如果想要成为一名高级玩家，需要你自己去进一步了解学习，本文仅初步安装做如此介绍，后续如果有精力的话，我会再继续出其他关于hexo其他配置的使用教程。

不过，`大道至简`，看穿事物的本质，即能快速掌握其核心。hexo说白了就是一个基于nodejs的工程，它定义了自己的一套文件格式，你只要按照一系列模板进行个性化编辑，并添加个人用户内容，然后使用它的一套命令，就可以把这些文件渲染成浏览器可访问的格式。剩下的就是关乎WEB原理的东西了。

本文中后部可能略去了一些详细的操作步骤，不过整体性上应该达到了教学的目的，希望读者都可以借助互联网自己完成剩余的部分。


### END

过程中由于`天时·地利·人和`都不同，所以可能遇到与我教程中所写不同的情况，但作为一个非专业人士，只要你肯耐着性子折腾，这些东西你也玩得转，而如果作为一个专业人士，钻研，是你的天职。比如博主写这篇教程也是通过先学习很多知识点，才“负责任”地整理出这篇博文。

参考链接：

- <a href="https://hexo.io/docs/setup.html">hexo官方文档</a>

- <a href="https://nodejs.org/en/">nodejs官网</a>

- <a href="https://developers.google.com/v8/">V8引擎官网</a>

- <a href="https://www.npmjs.com/">npm官网</a>

- <a href="https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434501436552e03ec6cc152b4c84959f14d0ea278488000">廖雪峰Javascript教程</a>

- <a href="http://www.ruanyifeng.com/blog/2016/01/npm-install.html">阮一峰的网络日志</a>

- <a href="http://www.ruanyifeng.com/blog/2016/07/yaml.html?f=tt">YAML语言教程</a>

- <a href="https://github.com/hexojs/hexo">Hexo源码</a>

`知识是理应无私分享的，而智慧是分享不了的。`

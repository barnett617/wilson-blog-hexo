---
title: 关于hexo阅读次数统计及访问次数插件使用
date: 2018-02-13 14:46:31
update: 2018-02-13 14:46:31
categories: 瞎鼓捣
tags: 
 - 其他
---

前一段时间换域名，发现博客的访问次数不显示了，一开始没时间关注，最近闲了，研究一下到底是怎么回事，发现原来当初这里不是基于本地配置，而是使用leancloud进行统计，查阅到一篇很易懂的教程，顺便整理了一下PV和UV的统计

<!--more-->

### 核心原理

hexo很多配置基于配置文件和第三方插件，而配置文件的格式又是类似大多数模板语言的格式，比如javaweb中的jsp标签，还有basic中的if和endif，仔细研究其中的一些配置，其实都可以很好的定制化和hackable

### 参考链接

- <a href="http://www.jeyzhang.com/hexo-next-add-post-views.html">http://www.jeyzhang.com/hexo-next-add-post-views.html</a>

- <a href="https://leancloud.cn/">https://leancloud.cn/</a>

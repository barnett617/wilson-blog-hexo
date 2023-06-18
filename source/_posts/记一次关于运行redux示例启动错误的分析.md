---
title: 记一次关于运行redux示例启动错误的分析
date: 2018-02-25 17:43:24
update: 2018-02-25 17:43:24
categories: 前端
tags: [react]
---

记录在运行redux官方文档中给出的基础实例运行时报错的解决思路

<!--more-->

## 核心

**进程占用**

## 背景

在阅读redux文档http://cn.redux.js.org/时，遇到推荐示例<a href="http://cn.redux.js.org/">simple-redux-example</a>

于是便按照readme说明实操一把

```
git clone
```

```
npm install
```

```
npm start
```

一切顺理成章，然而boom!

一脸懵逼

什么原因呢？先是仔细看了报错信息，里面讲的还算清楚，告诉我不是npm的问题，于是按照推荐，打开具体的日志文件

也没看出什么明显的问题

于是在error附近的日志信息里找几个关键字拉近google搜索一下

在stackoverflow的<a href="https://stackoverflow.com/questions/30744964/what-does-the-elifecycle-node-js-error-mean">一篇帖子</a>中的找到灵感

没错，进程占用！经过上面几个人说的情况都不匹配后，这个原因我是感觉特别可能的

于是赶紧检查8000端口是不是已经被占用

```
netstat -ano | findstr 8000
```

(进程ID之前是3260，这是后来写本文时补的)

果然被占用了，看一下哪个进程的PID是3260

之前这里有一个进程PID是3260，发现是一个应用程序，没什么用，kill掉

```
taskkill /PID 3260 	
```

再次启动

```
npm start
```

Nice!搞定

## 总结

解决问题的能力很重要，程序的世界不可能一帆风顺，耐心看日志，多借助`有效`途径（google not baidu, stackoverflow not zhidao），带上自己分析问题的思路和经验，问题总是能解决的。

写此篇博客的目的在于加深此类问题的印象，对于一些常用操作做一个记录，避免下次遇到类似问题时把时间花在查阅搜索和问题定位上

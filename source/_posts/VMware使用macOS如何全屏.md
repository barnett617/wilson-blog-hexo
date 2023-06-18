---
title: VMware使用macOS如何全屏
date: 2018-02-07 14:10:45
update: 2018-02-07 14:10:45
categories: 操作系统
tags: 
  - macOS
---

如何解决VMware安装VMware Tools后仍不能全屏显示的问题

<!--more-->

### 原因

macOS10.11上启动了一个SIP(System Integrity Protection，系统集成保护)

它防止/library/perferences/systemconfiguration/com.apple.Boot.plist文件被修改

### 解决

启动macOS过程中进入recovery console（启动系统时按住command+R，windows系统则按住开始键+R，直至看到苹果标志即可松手）

选择实用工具，打开终端

关闭SIP，重启

Bingo！

参考链接：<a href="http://trigolds.com/mac0.jpg">http://trigolds.com/mac0.jpg</a>

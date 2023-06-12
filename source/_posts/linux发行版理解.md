---
title: linux发行版理解
date: 2018-01-25 10:51:20
update: 2018-1-25 14:15:05
categories: 操作系统
tags: 
 - linux
---

linux作为开源系统，有着众多的发行版本（毕竟有着庞大的社区和狂热的爱好者），不同的发行版本(Linux Distribution)随着发展，在使用方式上也有一些不同，例如各自的包管理器、已经适用场景等等。借此整理一下linux的不同发行版本，以便在使用不同版linux时不至混乱。

<!--more-->

### 大致分类

1. 商业发行版

	- Ubuntu(Canonical公司)

	- Fedora(RedHat)

	- openSUSE(Novell)

	- Mandriva Linux

2. 社区发行版

	- Debian

	- Gentoo

3. 既不是商业发行版也不是社区发行版

<a href="https://upload.wikimedia.org/wikipedia/commons/1/1b/Linux_Distribution_Timeline.svg">Linux Distrubutions</a>

### Linux桌面发行版组成

- Linux内核

- GNU工具&库

- 附加软件、文档

- 窗口系统

- 窗口管理器

- 桌面环境

### 开源软件包

- 二进制可执行文件

- 源代码发布方式（支持修改和重新编译）

### 定制发行版镜像

- Gentoo Linux内核

- T2

- Linux From Scratch

提供：

- 所有软件的源代码

- 基本内核

- 编译器

- 定制工具

- 安装工具

### 软件包管理系统

发行版通常被分区成**软件包**，每个软件包包含一个特定的软件或服务

#### 软件包

软件包通常是**已编译**的**机器码**，由**软件包管理器**安装和卸载

#### 软件包组成

- 元数据：软件描述、版本、依赖（提供给软件包管理器以使用元数据进行搜索、自动更新到最新版本、自动解决依赖）

### 常见软件包格式

- deb——Debian

- rpm——Fedora(Red Hat)

### 流行的发行版

#### 基于Dpkg(Debian系)

##### 商业发行版

- Ubuntu（流行的桌面发行版，由Canonical维护）

##### 社区发行版

- Debian（强烈信奉自由软件，由志愿者维护）

- Kubuntu（使用KDE桌面的Ubuntu）

- Linux Mint（由Ubuntu派生，与Ubuntu兼容）

- OpenGEU（Ubuntu派生）

- Elementary OS（基于Ubuntu，形似Mac OS X）

- gOS及其他

#### 基于RPM（Red Hat系）

##### 商业发行版

- Red Hat Enterprise Linux（Fedora的商业版，由Red Hat维护）

- Mandriva（最初派生自Red Hat，现由法国同名公司维护）

- openSUSE（最初由Slackware分离出，现由Novell维护）

##### 社区发行版

- Fedora（Red Hat社区版，会引入新特性测试）

- PCLinuxOS（Mandriva派生版）

- CentOS（Red Hat发展而来，由志愿者维护，旨在提供开源，与Red Hat完全兼容）

#### 基于其他包格式

- ArchLinux（基于KISS——Keep It Simple and Stupid的滚动更新的操作系统）

- Chakra（由ArchLinux派生，只是用KDE桌面的半滚动更新发行版）

- Gentoo（面向高级用户，所有软件源代码需自行编译）

- Slackware（最早发行版之一，1993年创建，由<a href="https://en.wikipedia.org/wiki/Patrick_Volkerding">Patrick Volkerding</a>维护）

参考链接：

<a href="https://zh.wikipedia.org/wiki/Linux%E5%8F%91%E8%A1%8C%E7%89%88">https://zh.wikipedia.org/wiki/Linux%E5%8F%91%E8%A1%8C%E7%89%88</a>

<a href="https://www.distrowatch.com/">distrowatch</a>

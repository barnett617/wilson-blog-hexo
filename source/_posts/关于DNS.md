---
title: 关于DNS
date: 2018-01-25 14:46:04
update: 2018-1-29 19:23:11
categories: 网络
tags: 网络
---

探析DNS

<!--more-->

### DNS

#### 概念

Domain Name System，域名系统

#### 本质

**整理**和**识别**各个域名的网络电话簿

将域名转换为托管该网站的计算机的物理IP

### MX记录

#### 概念

Mail Exchange，邮件交换记录

#### 作用

将一个网域的电子邮件定向到托管该网域用户账号的服务器

#### 设置

一个网域可定义多条MX记录，每条记录有不同的优先级。

如果邮件通过最高优先级记录无法递送，则采用第二优先级，以此类推

### TXT记录

#### 作用

为网域之外的来源提供文本信息的一种DNS记录

##### 文本类型

- 人工可读文本

- 机器可读文本

### CNAME记录

规范名称记录

#### 作用

将一个别名关联到另一个**真正的**或**规范的**域名

### A记录

地址记录（主机记录），Address

#### 作用

将一个域名关联到托管该网域服务的计算机的物理IP

### NS记录

#### 作用

用于确定哪些服务器为一个网域传达DNS信息

网域通常有主要的和辅助的域名服务器记录

### TTL

生存时间，Time To Live

#### 作用

决定对该记录所做的后续更改需要多少秒才会生效

#### 推荐值

3600（即1个小时）

让整个互联网中的服务器每小时检查一次该记录的更新情况

##### 原因

较短的TTL在之前的有效期到期后才会生效

若想快速还原一项更改，则可设置较短的TTL值（如300,5分钟）

##### 合理配置

正确配置后，建议设置TTL为86400，即让整个互联网中的服务器每24小时检查一次记录的更新情况

### 注意

不要在DNS设置中使用实际域名，而是使用@符号表示域名

参考链接：<a href="https://support.google.com/a/answer/48090?hl=zh-Hans">https://support.google.com/a/answer/48090?hl=zh-Hans</a>

<a href="https://support.dnsimple.com/articles/differences-between-a-cname-alias-url/">https://support.dnsimple.com/articles/differences-between-a-cname-alias-url/</a>

---
title: mysql远端数据库与本地数据库间导入导出
tags:
  - mysql
date: 2017-08-12 16:35:16
update: 2017-08-12 16:35:16
summary:
categories: 数据库
---

### 远程数据库导出

 1. mysqldump -hxxx -uxxx -pxxx 数据库名 > 脚本名.sql
 2. sz 脚本名.sql（SecureCRT将文件下载到本地）

### 本地数据库导入
 3. 若直接用navicat运行本脚本，失败
 4. 打开cmd，进入本地数据库，mysql -uxxxx -pxxxx，use 创建好的数据库
 5. source 脚本名.sql，可以将2MB以上的sql脚本导入
 6. 成功执行，完成远端数据库到本地的克隆


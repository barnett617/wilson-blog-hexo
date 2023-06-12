---
title: linux忘记密码
date: 2018-01-23 09:48:20
update: 2018-01-23 09:48:20
categories: 操作系统
tags: 
 - linux
---

这算不上一篇博客，只是不想再重复在解决这个问题上花费时间。另外对于解决方案的原理有待深究。

<!--more-->

### 核心操作

进入**GRUB**修改kernel镜像**启动参数**

### 具体步骤

1. 启动系统时长按shift，进入GRUB引导模式

2. 进入高级模式

3. 选择恢复模式（recovery mode）按E键（这里最容易错，是按e键编辑启动项，而不是按回车进入）

4. 找到recovery nomodeset改为quiet splash rw init=/bin/bash（挂载硬盘为可读写模式）

5. 按Ctrl+x或F10进行引导

6. 进入root@(none):/#

7. 输入passwd进行root的密码设置

8. 重启

> Tip：此方案用于解决root密码重置，但如果是ubuntu，默认是以普通用户登录或游客访问，在登录界面是看不到root用户的入口，需要登录普通用户需要sudo su来切换root，但如果普通用户忘记密码，就进不到这一步，这时解决方案则是在第7步为passwd加上参数，即要设置密码的用户名


文献： <a href="https://books.google.com/books?id=X3tZDgAAQBAJ&pg=PA191&lpg=PA191&dq=recovery+nomodeset&source=bl&ots=HOI9ga8YBl&sig=J8_HlXL2v9V9BnpNpvXKoPbW2MM&hl=zh-CN&sa=X&ved=0ahUKEwjh0MWQ--zYAhVBl5QKHSfvBd0Q6AEIVDAE#v=onepage&q=recovery%20nomodeset&f=false">https://books.google.com/books?id=X3tZDgAAQBAJ&pg=PA191&lpg=PA191&dq=recovery+nomodeset&source=bl&ots=HOI9ga8YBl&sig=J8_HlXL2v9V9BnpNpvXKoPbW2MM&hl=zh-CN&sa=X&ved=0ahUKEwjh0MWQ--zYAhVBl5QKHSfvBd0Q6AEIVDAE#v=onepage&q=recovery%20nomodeset&f=false</a>

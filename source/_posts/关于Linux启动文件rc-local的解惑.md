---
title: 关于Linux启动文件rc.local的解惑
date: 2017-08-16 20:12:38
update: 2017-08-22 10:00:00
tags: [linux]
categories: 操作系统
---
### 背景
首先，rc.local是Linux启动程序在login程序前执行的最后一个脚本，有的服务器中在rc.local中可能会有一句touch /var/lock/subsys/local，这是干什么的呢，在百度中没找到，最终在[Linuxquestions.org论坛](http://www.linuxquestions.org/questions/linux-newbie-8/touch-var-lock-subsys-local-why-287719/)成功找到满意的解答。
<!--more-->
### touch
首先要了解touch这个命令是做什么用的，在此用于创建一个不存在的文件，详细了解请见[Linux touch命令](http://www.jb51.net/LINUXjishu/257946.html)
### 解释
/var/lock/subsys/local这个文件的存在证明rc.local这个脚本已经执行过了，目的在于避免这个脚本重复执行，除非这个文件不存在时，它才失效，也就是当系统关闭（shut down）时会发生，翻译的不是很好，原文是

> What this does is create a lock file that tells the system that 'local' is up and running already. It prevents the script from being run twice, as it will fail until the lockfile is removed, which will happen when you shut down.
> 
> Typically this is used with bigger services such as database servers and so forth to make sure they are not started twice.

这样做的目的是用于一些大型服务，例如数据库服务器，以确保它不会启动两次的情况出现。
至于touch出的这个文件什么时候被读取以避免再次生成，论坛两哥们是这么解释的

> To tell the truth I don't think anything looks there. I have never seen a lock mechanism in place for the rc.local script. Like I was saying, usually it is only used for bigger services. However, it is usually checked by the script itself.
> If you have a look at one of your more complicated rc scripts you may see in the 'start' function something like (pseudo code):
Code:


```
start() {
    if /var/lock/subsys/myapp exists; then
        echo "myapp already started"
        exit
    else
         touch /var/lock/subsys/myapp
         /command/to/start/myapp
    fi
}
```

也就是会在一个地方，会有类似与上面start()这样的方法会去判断是否已经存在一个local文件，如果有就不重复创建，如果没有就创建一个，后面再读取的时候就不会去创建，以此避免系统重复启动。
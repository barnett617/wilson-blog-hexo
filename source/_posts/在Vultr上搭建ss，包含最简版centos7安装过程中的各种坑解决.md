---
title: 在Vultr上搭建ss，包含最简版centos7安装过程中的各种坑解决
date: 2017-08-16 20:14:53
update: 2017-8-17 21:20:30
tags: [linux]
categories: 操作系统
---

 1. 检查是否安装python
 2. 安装pip
 3. pip install shadowsocks
 4. pip install M2Crypto
 5. M2Crypto安装前置条件：安装pcre，然后安装swig(直接安装pcre，去安装swig会提示pcre未安装，即使pcre已安装->解决方案，安装pcre-devel，在发现这个问题之前卡住很久)
 <!--more-->
 6. 安装pcre-devel后安装swig，并配置环境变量（注意！）
 7. 安装M2Crypto，再次失败，需要安装openssl（[openssl下载](https://www.openssl.org/source/openssl-1.0.1t.tar.gz)）下载安装
 8. 再次失败，缺少perl，至少perl5，make时间略长，make test时间更长
 9. 安装openssl，make时间略长
 10. 在M2Crypto文件夹中拷贝openssl头文件(根据Linux架构位数区分，lsb_release -a查看，若未安装，则需要yum install -y redhat-lsb进行安装)失败，需要yum install openssl-devel
 11. 再次失败，提示
 /usr/include/openssl/opensslconf.h:36: Error: CPP #error ""This openssl-devel package does not work your architecture?"". Use the -cpperraswarn option to continue swig processing.
 解决方案：env SWIG_FEATURES="-cpperraswarn -includeall -D__`uname -m`__ -I/usr/include/openssl" python setup.py build
 直接执行提示python.h无法找到，解决->yum install python-devel（解决方案均来自stackoverflow，国内博客很多都没有建设性意见，纯属浪费时间）

 12. pip setup.py install->pip list|grep M2Crypto（终于成功！）
 13. 在/etc下新增ss.json代理配置文件
 {
    "server":"serverip",
    "server_port":8388,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"password",
    "timeout":300,
    "method":"aes-256-cfb"
}
 14. ssserver -c /etc/ss.json启动代理

> devel包主要是头文件之类的，共开发用，而且一般会依赖相应的包，安装的时候会自动装上去。
>
> devel包含普通包，只比普通包多了头文件。动态链接库的话两种包都有。编译的时候如果需要用到这个库，那么需要安装这个库的devel，因为需要头文件
>
> 在Linux系统中都存在类似的问题，在安装特定安装包的过程中，其依赖某些第三方开发包，会曝出某些文件找不到的错误，一般情况下是需要安装依赖包的开发版本的。 这个规则应该是通用的。

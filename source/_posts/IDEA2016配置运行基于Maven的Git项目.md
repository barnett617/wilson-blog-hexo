---
title: IDEA2016配置运行基于Maven的Git项目
tags: 
  - git
categories: 笔记
date: 2017-08-16 20:21:00
update: 2017-05-19 20:20:20
---

### 一、IDEA&Maven&Git作用

 1. IDEA（项目容器）
 2. Maven（管理Jar和项目打包）
 3. Git（版本控制）

 <!--more-->

### 二、从远程clone git项目
使用工具clone下git项目

> 可能问题： 直接通过git bash上git clone时可能失败
> 
> 问题解决：使用TortoiseGit（有时候TortoiseGit也可能失败，可能和gitblit服务器有关）。
> 
> 可能项目由多个module相互依赖
> 
> 从Git直接clone下的项目，未编译，即不含target或out文件夹（gitignore中限制编译后的文件被Git管理）
> 
> 但有pom.xml文件（Idea导入基于Maven的项目依赖该文件，即导入项目时需找到pom.xml文件）
> 
### 三、将clone到本地的Maven项目使用IDEA编辑（导入或打开）

> 如果一个项目有多个相互依赖的module组成（各自module分别有pom.xml），则在Idea中依次导入已存在module（不是新建module）
> 
> 由多个module相互依赖组成的项目，会有一个是主module
> 
> 或者通过open的方式（更快）找到pom.xml（多个module使用同一个module）
> 
> ！！！对于Maven项目，使用IDEA导入可能出现的情况：
> 
> 如果直接打开的module文件夹，而不是pom.xml文件，则不会生成Maven项目，这是remove了module，会残余文件，若delete后，则从git上clone下的module文件夹中将没有了pom.xml（因为刚才delete掉了），此时再看该module文件夹，git已提示有文件被删除了（原因：IDEA只是管理代码的IDE，对于打开项目之后的操作都是对本地文件的操作，从IDE里删除了文件，也就是对本地文件进行删除）

**方式二**：直接使用Idea，project from Git

### 四、IDEA初始化配置

> 第零步：jdk&maven配置
> 
> 第一步：Project Structure
> 
 1. project name
 2. SDK
 3. project compiler output

> 第二步：服务器配置
> 热部署（JRebel可通过VM配置，给运行的容器VM参数 ）
> 第三部：fix artifacts，选择war exploded
 
###  五、启动项目
 
 1. 编译（build）
 2. 启动服务器

### 六、问题情况

> 导入module后，提示is registered as a Git root, but no Git repositories were found there.点击config，将项目添加git，之后正常配置，即为正常
> 
> 启动tomcat，debugger端口被占用
>  java.net.SocketException "socket closed"

### 七、问题情况

> 如果端口被占用情况反复出现，且已确认自己正确配置，那么重启电脑是最好的解决办法，其他的端口被占用情况类似（因为端口占用的问题浪费了几个小时都找不到原因，查了stackoverflow，segementFault，看了各种博客，解决方法都不行，原来重启就解决了。。。俗话说：没有什么是重启电脑解决不了的，重启真是神技啊！）


 





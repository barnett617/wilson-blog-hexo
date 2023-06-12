---
title: python零碎知识点整理——注释
tags:
  - python
date: 2017-02-07 22:05:20
update: 2017-02-07 22:05:20
summary:
categories: 笔记
---


#### 单行注释

> python的单行注释用#，可在任意位置添加（单独一行或语句/表达式后面，python是解释型语言，逐行按序解释代码）
#### 多行注释
> 
> 多行注释使用三个单引号或双引号
> 这实际上是**多行字符串**的书写方式，并非python本身提倡的多行注释

#### 编码声明注释

> 出现在python脚本第一行或第二行（其他行则无效）的含有coding:xxx的注释被认为是对编码方式的声明，详见[python官方文档](https://docs.python.org/3/reference/lexical_analysis.html#encoding-declarations)
> 
> 从python3开始，python默认使用utf-8编码（python3以前使用ascii编码）

#### 平台注释

> 使python程序运行在windows平台上，需要在python文件的最前面加上#!/usr/bin/python，这说明了程序用的环境的路径

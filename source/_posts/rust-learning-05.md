---
title: rust-learning-05
date: 2023-08-31 22:54:51
tags: [rust]
---

使用包、预置库和模块管理不断增长的项目

<!--more-->

# 包和库

库是 Rust 编译器一次考虑的最小数量的代码，即使使用 rustc 运行一个源码文件，也称为库。

库可以包含模块，模块可能定义在其他文件。

## 库形态

库有两种形态，可以是一个`二进制库`或一个`源码库`。

二进制库需要有 main 函数定义。

源码库可以没有 main 函数，不会被编译为可执行文件，只定义用于多个项目使用的函数，比如 rand，一般 rust 使用者提到的库都是源码库。

## 包

包是由一个或多个库组成的，有 Cargo.toml 文件描述如果构建其中的库。

Cargo就是一个包，它包含二进制库用于命令行工具，同时也包含一些源码库用于命令行工具。

一个包可以有任意多个二进制库，但只能有一个源码库。一个库至少要有一个库。

# 定义模块以控制作用域和私有性

use 关键字用于将一个路径引入作用域，pub 关键字用于将内容公开。

## 模块技巧

- 从源码库根开始：当编译一个库时，编译器首先会寻找根库文件（源码库一般是src/lib.rs，二进制库一般为src/main.rs）
- 声明模块：在根库文件，可以声明新的模块，比如 mod garden; 编译器会从以下地方寻找该模块
    - 行内
    - src/garden.rs
    - src/garden/mod.rs
- 声明子模块：在根库以外的任意文件，可以声明子模块。比如 mod vegetables; 编译器会从以下地方寻找子模块
   - 行内
   - src/garden/vegetables.rs
   - src/garden/vegetables/mod.rs
- 模块内的代码路径：一旦模块成为库的一部分，在同一个库内的任意地方都可以引用那个模块内的代码。比如 crate::garden::vegetables::Asparagus
- 私有性和公有性：从父模块访问子模块内的代码默认是私有的。要声明公有性需要使用 pub mod 声明模块
- use关键字：在一个作用域内，use关键字可以代替模块的完整路径。比如 crate::garden::vegetables::Asparagus，可以先 use crate::garden::vegetables::Asparagus，然后就可以在作用域内任意地方直接使用 Asparagus

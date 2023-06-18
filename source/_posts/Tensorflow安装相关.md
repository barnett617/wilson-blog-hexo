---
title: 谷歌机器学习开源框架Tensorflow安装相关
date: 2018-01-25 10:17:43
update: 2018-01-25 10:17:43
categories: 机器学习
tags: 
 - tensorflow
---

关于安装TensorFlow的一系列准备

<!--more-->

### 前期准备

tensorflow官方推荐安装是通过pip安装的，先来看看pip是什么

### pip

#### 本质

包安装器

#### 类似工具

- linux的rpm(RPM Package Manager，递归写法，类似于GNU——GNU's Not Unix)

- nodejs的npm(node package manager)

- java的Maven(严谨来说，PyPI相当于Maven，包社区)

- Conda(由Python编写，语言无关的跨平台包管理器)

- Anaconda(本身一个python数据科学平台，同时是一个平台无关的包管理器、环境管理器)

#### 名称解释

pip的解释应该是Python Install Package或者Package Index for Python(个人猜测，暂未找到官方解释)

因为通过pip为python安装包时的用法是

```
pip install some-package
```

#### 具体行为

去PyPI（Python Package Index）下载相关包

#### 安装

##### 预安装情况

- 装有大于等于2.7.9或3.4版本的python，已预装

- 使用通过virtualenv或者pyvenv创建的虚拟环境，已预装

##### 手动安装

下载<a href="https://bootstrap.pypa.io/get-pip.py">get-pip.py</a>文件并如下安装

```
python get-pip.py
```

这种方式需要**注意**和其他包管理器的冲突，导致不一致情况

###### 附加下载

get-pip.py同时会安装setuptools和wheel

如果不需要这两者，可通过添加参数来跳过其安装

```
python get-pip.py --no-index --no-wheel
```

###### linux包安装方式

- Fedora

Fedora21

```
sudo yum install python-pip
```

Fedora22

```
sudo dnf install python-pip
```

- CentOS/RHEL

EPEL 6 and EPEL 7

```
sudo yum install python-pip
```

- openSUSE

```
sudo zypper install python-pip
```

- Debian/Ubuntu

```
sudo apt-get install python-pip
```

- Arch linux

```
sudo pacman -S python2-pip
```

```
sudo pacman -S python-pip
```

#### 升级

##### linux or macOS

```
pip install -U pip
```

##### Windows

```
python -m pip install -U pip
```

### 通过虚拟机安装运行tensorflow

#### 安装

```
pip install tf-nightly
```

#### 运行

参考链接:

- <a href="https://packaging.python.org/guides/installing-using-linux-tools/#installing-pip-setuptools-wheel-with-linux-package-managers">Installing pip/setuptools/wheel with Linux Package Managers</a>

- <a href="https://pip.pypa.io/en/stable/installing/">pip installation</a>

- <a href="https://pypi.python.org/pypi">PyPI</a>

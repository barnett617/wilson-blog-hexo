---
title: 如何使用github进行持续集成
tags:
  - github
date: 2020-08-19 22:31:55
update: 2020-08-20 17:16:49
summary:
categories:
---

利用github的actions为你的前端项目加上CI(continuous integration，持续集成)

<!--more-->

## 一、创建前端项目

> 这里以angular项目为例，其他框架同理

前端的三大框架（Angular、React和Vue）目前均有自己比较成熟的脚手架（cli）可用于项目从**创建**、**开发**到**构建**、**部署**一系列的工作，从而使得前端部分的工作从开发到交付保持比较高的效率。但仅仅如此还不够，因为在部署环节，其实不同开发者有这不同的“套路”，有使用jenkins的，也有在本地编译再通过scp传输到服务器的。但这些在CI面前都显得不够“自动化”。

我们稍微总结一下：

前端有三大框架

- Angular
- React
- Vue

分别对应有自己的脚手架工具

- @angular/cli
- create-react-app
- @vue/cli

前端项目从0到1到上线主要环节有

- 创建
- 开发
- 构建
- 部署

主流部署方式有

- jenkins
- scp

接下来进入今天的主要环节，如何利用github的actions功能实现项目CI

## 二、github actions

按照github actions的指示操作后，会创建一个.github/workflows/blank.yml文件

这个文件承载着整个github actions的配置，也就意味着当你在github某个仓库中包含.github/workflow/xxx.yml文件后，你在操作这个仓库时就会触发对应的action。比如我们如下配置可使得每次在对smartshop这个项目的master分支上进行push时触发如下动作：

1. 在github所提供的某台云服务器A上安装node和npm
2. 在服务器A上安装@angular/cli
3. 利用安装好的@angular/cli对smartshop项目进行编译
4. 将编译生成的目录通过scp发送到指定的服务器B

```yml
# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the master branch
on:
  push:
    branches: [ master ]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - uses: actions/checkout@v2
    # this installs node and npm for us
    - uses: actions/setup-node@v1 
      with:
        node-version: '10.x'
    # this allows for re-using node_modules caching, making build a bit faster  
    - uses: actions/cache@v1
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-
        
    # Runs a single command using the runners shell
    - name: npm install
      run: npm install
    
    - name: install ng-cli
      run: npm install -g @angular/cli > /dev/null
    
    - name: build
      run: npm run build
      # run: ng build --prod
    
    - name: copy file via scp
      uses: appleboy/scp-action@master
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        port: ${{ secrets.PORT }}
        password: ${{ secrets.PASSWORD }}
        source: "./dist/znsc-ng"
        target: "/home/webapp/"
```

其中比较关键的也是开发者比较关心的是自己要部署的服务器信息，这部分内容当然是不会直接明文写在配置文件中的，所以在这里github actions利用了变量进行处理。而这个变量值的设置则是通过另一个地方进行保存

### 插曲一

初始化的阿里云ECS由于控制台配置，默认的80端口可能不是nginx服务器，也有可能被其他程序占用，可通过以下命令查看

```shell
netstat -anop | grep 0.0.0.0:80
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      1905/java            off (0.00/0/0)
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      24066/httpd          off (0.00/0/0)
tcp        0      0 0.0.0.0:8009            0.0.0.0:*               LISTEN      1905/java            off (0.00/0/0)
```

可发现80端口被apache服务器的httpd进程所占用，如果使用apache服务器，可通过配置apache所占80端口默认访问的静态文件入口，操作如下：

```shell
whereis httpd
httpd: /etc/httpd /usr/local/apache/bin/httpd

cd /etc/httpd

vi httpd.conf

/80

/DocumentRoot
```

如果使用nginx服务器，可通过以下命令查找nginx服务器当前使用的配置文件

```shell
whereis nginx
nginx: /usr/local/nginx

cd /usr/local/nginx
cd sbin

./nginx -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```

### 插曲二

angular项目中可能会使用到样式预处理器，当依赖node-sass包后可能会下载失败。此时可使用node-sass官网所提供的另一个包提供可供下载该包的源配置，如下

```
npm install -g mirror-config-china --registry=https://registry.npm.taobao.org
```

[参考](https://www.npmjs.com/package/node-sass#install-from-mirror-in-china)

## 结论

这样我们就可以愉快地在功能分支进行开发和提交，在master分支进行push到远程仓库，并自动触发部署动作，完成更新上线了。这样既省去了每次手动部署的操作，也无需对服务器私密信息进行本地保存，实属github actions为我们提供的一种CI最佳实践

## 参考链接

- [Deploy Angular to Production with Github Actions](https://blog.khophi.co/deploy-angular-with-github-actions/)
- [Managing deploy keys](https://docs.github.com/en/developers/overview/managing-deploy-keys#deploy-keys)
- [httpd配置文件httpd.conf规则说明和一些基本指令](https://www.cnblogs.com/f-ck-need-u/p/7636836.html#1-1-httpd-apachectl-)
- [Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#)
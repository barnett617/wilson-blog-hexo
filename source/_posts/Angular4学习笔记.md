---
title: Angular学习笔记
date: 2018-02-22 22:36:27
update: 2018-02-22 22:36:27
categories: 笔记
tags: 
 - angular
---

关于Angular学习整理

<!--more-->

## 简介

Angular是谷歌开发和维护的一个开发**跨平台**应用程序的框架，适用于手机和PC

### 特点

- 跨平台：Web、移动、PC

- 渲染快：Web Worker+SSR

- 可伸缩：基于RxJS、Immutable.js及其他推送模型，可适应海量数据需求

### 特性

- DHTML

- 表单系统（模型驱动）

- 试图引擎

- 事件处理

- 快速渲染

- 灵活路由

- HTTP服务

- 视图封装

- AOT、Tree Shaking

### Angular与AngularJS区别

- 不再有Controller和Scope

- 更好的**组件化**及**代码复用**

- 更好的移动端支持

- 引入RxJS和Observable

- 引入Zone.js，提供更智能的**变化检测**

### 开发配置

- 基于QuickStart

拷贝quickstart项目

```
git clone https://github.com/angular/quickstart
```

安装所需依赖

```
npm i
```

启动

```
npm start
```

- 基于Angular CLI

```
npm install -g @angular/cli
```

```
npm install --save-dev @angular/cli@latest
```

```
ng --version
```

```
ng new projectname
```

切换到刚创建的工程目录

浏览器访问localhost:4200

参考链接：

- <a href="https://segmentfault.com/a/1190000009733649">https://segmentfault.com/a/1190000009733649</a>

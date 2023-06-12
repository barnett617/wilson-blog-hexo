---
title: js异步编程
date: 2018-03-06 19:10:33
update: 2018-03-06 19:10:33
categories: 前端
tags: 
 - javascript
---

js异步编程的**4**种方式

<!--more-->

## 背景

js语言执行环境——单线程（Single Thread）

## 单线程

一次只能执行一个任务，超过一个的就只能**排队**

### 单线程优势

- 实现简单

- 执行环境单纯

### 单线程劣势

存在**耗时很长**的任务会拖慢**整个程序**的运行

### 单线程带来的问题

浏览器无响应（假死）

#### 浏览器假死原因分析

某段js代码（某个任务）长时间运行（比如死循环）

#### js对于单线程劣势的解决方案

执行模式分为：同步（Synchronous）和异步（Asynchronus）

##### 同步模式

- 后一个任务等待前一个任务结束

- 程序执行顺序同任务排列顺序

##### 异步模式

- 每个任务有1个或多个回调函数（callback）

- 前一个任务结束，不是继续执行后一个任务，而是执行回调

- 后一个任务不等前一个任务结束就执行

- 程序的执行顺序和任务排列顺序不一致

#### 异步模式使用

浏览器端**耗时长**的任务都应该**异步执行**

避免浏览器失去效应

#### 异步模式实践

Ajax

## 异步编程的4种方式（实践）

### 1.回调函数（最基本）

#### 实际场景

两个函数f1()、f2()，f1()耗时长

##### 解决方案

把f2()函数写成f1()函数的回调函数

```javascript
function f1(callback) {
	setTimeout(function() {
		// code of f1
		callback();
	}, 1000);
}

f1(f2);
```

- f1不会阻塞程序执行

- 先执行程序主逻辑，耗时操作推迟执行

###### 回调函数存在的问题

- 不利于代码阅读和维护

- 各部分间耦合高（Coupling）

- 流程变乱

- 每个任务仅支持一个回调函数

### 2.事件监听（信号）

使用**事件驱动**模式——任务的执行不取决于代码顺序，而是事件的发生

为f1绑定一个事件

```javascript
f1.on('done', f2);
```

解释：当f1发生done事件时执行f2

```javascript
function f1() {
	setTimeout(function() {
		f1.trigger('done');
	}, 1000);
}
```

f1.trigger('done')表示当执行完其上的内容，立即触发done事件（即执行f2）

#### 优势

- 容易理解

- 每个事件可指定多个回调函数

- 可以去耦合（decoupling）

- 有利于实现模块化

#### 缺陷

- 整个程序变成**事件驱动**的

- 运行流程不清晰

### 3.发布/订阅模式 或 观察者模式（publish/subscribe pattern or observer pattern）

制造一个信号中心，某任务完成后向信号中心**发布（publish）**一个信号，其他任务可向信号中心**订阅（subscribe）**这个信号，从而得知自己什么时候执行

#### 该模式的多种实现

- Ben Alman's gist https://gist.github.com/661855 (recommended)
- Rick Waldron's jQuery-core style take on the above https://gist.github.com/705311
- Peter Higgins' plugin (http://github.com/phiggins42/bloody-jquery-plugins/blob/master/pubsub.js).
- Ben Truyman's gist https://gist.github.com/826794

参考：https://msdn.microsoft.com/en-us/magazine/hh201955.aspx

这里举例第一种，是jQuery的一个插件

1. f2向jQuery（信号中心）订阅done信号

```javascript
jQuery.subscribe("done", f2);
```

2. f1向jQuery（信号中心）发布done信号

```javascript
function f1() {
	setTimeout(function() {
			jQuery.publish("done");
	});
}
```

如果f2只要执行一次，可以在执行后取消订阅

```javascript
jQuery.unsubscribe("done", f2);
```

#### 发布订阅模式优劣分析

与事件监听类似，但可通过信号中心清晰了解到存在多少信号（发布者发布了多少信号）、每个信号有多少订阅者，从而监控程序的执行

### 4.Promises对象

Promises对象是CommonJS工作组提出的一种规范，目的旨在为**异步编程**提供统一的接口

#### Promises实现异步编程的思想

- 每一个异步任务**返回**一个Promise对象

- 该对象有一个**then**方法，允许指定回调函数

##### 实例操作

为f1指定回调函数为f2

```javascript
f1().then(f2);
```

jQuery的实现

```javascript
function f1() {
	var dfd = $.Deferred();
	setTimeout(function() {
		dfd.resolve();
	}, 1000);
	return dfd.promise
}
```

##### 优点分析

- 回调函数变成了**链式**写法，程序流程清晰

- 有配套的[整套方法](http://api.jquery.com/category/deferred-object/)，可实现强大的功能

##### 应用场景

###### 指定多个回调函数

```javascript
f1().then(f2).then(f3);
```

###### 指定发生错误时的回调函数

```javascript
f1().then(f2).fail(f3);
```

##### Promises使用的优缺点

###### 优点

- 一个任务已完成，再添加回调函数，该回调函数会立刻执行

- 不用担心是否错过某个**事件**或**信号**

###### 缺点

- 难以理解和编写

参考链接：

- <a href="http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html">http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html</a>

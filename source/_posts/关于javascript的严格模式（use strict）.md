---
title: 关于javascript的严格模式（use strict）
date: 2018-02-12 20:55:06
update: 2018-2-13 14:17:25
categories: 前端
tags: javascript
---

探析javascript中的严格模式

<!--more-->

### 背景

出现于ES5

- 消除js语言一些**不合理**、**不严谨**的地方，减少一些**怪异**行为

- 消除代码运行**不安全**之处

- 提高**编译**效率，提高**运行**速度

- 未新版本js做铺垫

### 本质

第二种运行模式：严格模式

#### 严格模式

##### 转换严格模式

ES5中引入了严格模式，现已被所有的主流浏览器实现（包括IE10）

如果想要浏览器按照严格模式解释代码只需要在代码前（代码顶部）加上‘use strict’

如果想将已存在的代码转换为严格模式需要多做一点工作

##### 逐步转换

严格模式被设计来可以逐步转换，可以独立地改变每一个文件，甚至可以逐步地将函数转换为严格模式

##### 对比严格模式和非严格模式

实例：

如下代码在添加了‘use strict’后抛出语言错误

- 八进制语法：var n = 023;

- with语句

- 在变量名中使用delete：delete myVariable;

- 使用eval或者arguments作为变量或者函数的参数名

- 使用新版本的保留字：implements、interface、let、package、private、protected、public、static和yield

- 在块级域内声明函数：if (a < b) { function f() {} }

- 明显的错误：

	- 在一个对象字内对一个属性声明两次相同的名称：{a: 1. b: 3. a: 7}，这已经不再是ES2015中的情况（<a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1041128">bug</a>）

	- 使用相同的名称声明两个函数参数：function f(a, b, b) {}

这些错误还算好，因为它们能够明显地暴露出错误或者糟糕的实践，它们会在代码运行前发生

##### 新的运行时错误

javascript过去常在代码发生错误的地方静默地运行失败，严格模式会在这种情况抛出异常。如果你的代码包含这种情况，测试会变得很有必要以确保没有东西坏掉。然而再一次，会在函数粒度级别再一次发生

实例：给一个未定义的变量赋值

```JavaScript
function f(x) {
	var a = 12;
	b = a + x * 35;
}
f(42);
undefined
```

```JavaScript
function f(x) {
	'use strict';
	var a = 12;
	b = a + x * 35;		// 报错
}
f(42);
```

这可能会改变一个全局对象的值，这常常并不是所期望的。如果真的想给一个全局对象设置值，将其作为参数传给它并明确地将其声明为一个属性

```javascript
function f(x) {
	'use strict';
	var a = 12;
	b = a + x * 35;
}
f(42);
```

实例：尝试删除一个非可配置属性

这种操作会在非严格模式静默地失败，与用户所期望所违背

```JavaScript
delete Object.prototype;
false
```

```javascript
'use strict';
delete Object.prototype;
VM361:2 Uncaught TypeError: Cannot delete property 'prototype' of function Object() { [native code] }
    at <anonymous>:2:1
```

实例：有毒害的参数和函数属性

访问arguments.callee、arguments.caller、anyFunction.caller或者anyFunction.arguments都会在严格模式中抛出异常错误

唯一合法的用例会是重用一个函数如下：

```javascript
var s = document.getElementById('thing').style;
s.opacity = 1;
(function() {
	if ((s.opacity-=.1) < 0)
		s.display = 'none';
	else
		setTimeout(arguments.callee, 40);
})();
```

上述实例见<a href="http://vanilla-js.com/">http://vanilla-js.com/</a>

如果在严格模式中，可以将上述函数重写为如下：

```javascript
'use strict';
var s = document.getElementById('thing').style;
s.opacity = 1;
(function fadeOut() { // name the function
  if((s.opacity-=.1) < 0)
    s.display = 'none';
  else
    setTimeout(fadeOut, 40); // use the name of the function
})();
```

##### 语义上的不同

这些都是很微妙的不同，可能测试用例不会捕获这种微妙的不同。仔细回看你的代码可能显得必要以确保这些不同不会影响你代码的语义。幸运的是，这种仔细的回看可以变成逐步地应用于函数粒度级别的回看

##### 函数调用中的this

在函数调用中，例如f()，this值是全局对象。在严格模式中，现在是undefined。

当一个函数被使用call或者apply调用时，如果值是原始值，这个值会被装配进一个对象（或者对于undefined或者null的对象）。

在严格模式中，这个值会被直接传递而不被转换或替换。

##### arguments没有被函数参数命名别名

在非严格模式中，修改arguments对象中的一个值会修改相应的命名参数

这使得对于javascript引擎的优化变得复杂，同时令代码更难阅读或理解

在严格模式中，arguments对象被创建并被初始化为同名值而非命名参数，但是对于arguments对象或是命名参数的改变不会彼此影响

##### 更改为eval

在严格模式中，eval不会在它被调用的域内创建一个新的变量。当然，在严格模式中，字符串也会被按照严格模式的规则进行评估

测试需要被彻底地执行以确保一切正常（nothing breaks）

不要使用eval，除非你真的需要它，这会是一个更务实的解决方案

##### 严格中立（strictness-neutral）代码

移动严格代码到严格模式的一个潜在的弊端是可能在那些没有实现严格模式的传统浏览器中语义上存在不同

在某些稀有场合（比如糟糕的联结或者缩小），你的代码也可能不能运行在你编写和测试的模式中，以下一些规则可以使得你的代码变得严格中立：

1. 以严格模式书写代码并确保没有在严格模式下会抛出的错误

2. 原理语义不同

	1. eval：只有在你明白你在做什么的情况下使用它

	2. arguments：永远通过名称访问函数的参数或执行一份参数对象的拷贝，比如如下，将这样的声明添加在你函数的第一行

	```JavaScript
	var args = Array.prototype.slice.call(arguments)
	```

	3. this：仅在涉及到你创建的对象时使用this






参考链接：

- <a href="http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html">http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html</a>

- <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode">https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode</a>

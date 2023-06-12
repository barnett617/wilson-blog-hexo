---
title: js学习笔记——局部变量和全局变量
date: 2018-01-19 16:34:02
update: 2018-1-20 09:28:35
categories: 前端
tags: 
 - javascript
---

js的局部变量和全局变量

<!--more-->

### 大纲

- 如何声明局部变量和全局变量

- js变量作用域基于特有的作用域链

- js没有块级作用域（ES5-）

- 函数中声明的变量在整个函数中都有定义

### 声明局部变量和全局变量

- 全局变量——定义在函数外的变量

- 局部变量——函数内部声明的变量

```javascript
var a = 'global';

function getLocal()	{
	var a = 'local';
	console.log(a);
}

getLocal();
console.log(a);
```

```
local
global
```

```javascript
a = 'global';

function getLocal() {
	a = 'local';
	console.log(a);
	b = 'local';
	console.log(b);
}

getLocal();

console.log(a);
console.log(b);
```

```
local
local
local
local
```

疑问：

函数外的a为什么变成local？

函数外为什么可以访问b？

解析：

**函数体内部，局部变量优先级比同名全局变量高**

**函数体内声明变量不使用var，会暴露在全局上下文中，和现有变量冲突，并覆盖同名全局变量**

### 变量作用域链

每当js执行时会有一个对应的执行环境被创建（每一个方法在自己独有的执行环境中执行）

执行环境中的重要部分——函数的调用对象（存储相应函数的局部变量）

> 函数的执行环境包含了调用对象，调用对象的属性就是函数的局部变量，每个函数在这样的执行环境中执行。函数之外的代码在另一个环境中执行，包含了全局变量。

#### 作用域链

对象组成的列表或链

```javascript
var rain = 1;

function rainMan() {
	var man = 2;

	function inner() {
		var innerVar = 4;

		console.log(rain);
	}

	inner();
}

rainMan();
```

```
1
```

解析：

1. rainMain()
2. man和inner()分别是rainMain定义的变量和函数
3. 执行inner()
4. 寻找rain
5. 先在inner内部找，没找到
6. 继续在rainMain中找，没找到
7. 在全局对象找，找到，输出1
8. 若全局对象中还未找到则报错：Uncaught ReferenceError: rain is not defined

在全局的js执行环境中，作用域链只包含一个对象——全局对象

在函数的执行环境中，同时包含函数的调用对象

js的函数可嵌套——每个函数执行环境的作用域链可能包含不同数目个对象

一个非嵌套的函数的执行环境中，作用域链包含这个函数的调用对象和全局对象

嵌套的函数的执行环境中，作用域链包含嵌套的每一层函数的调用对象以及全局变量

### JS没有块级作用域

```javascript
function checkVariable(obj) {
	var i = 0;
	if (typeof obj == 'object') {
		var j = 0;
		for (var k = 0; k < 3; k++) {
			console.log(k);
		}
		console.log(k);
	}
	console.log(j);
}

checkVariable(new Object());
```

```
0
1
2
3
0
```

i,j,k作用域相同，都在checkVariable()函数内都是全局的

函数中声明的所有变量，无论在哪里声明，在整个函数中都有定义

### 函数中声明的变量在整个函数中都有定义（即使在声明之前）

```javascript
function checkVariable() {
	var a = 1;
	function inner() {
		a = 100;
	}
	inner();
	console.log(a);
}
checkVariable();
```

```
100
```

```javascript
var a = 1;
function checkVariable() {
	console.log(a);
	var a = 2;
	console.log(a);
}
checkVariable();
```

```
undefined
2
```

原因：checkVariable()内部变量a在整个函数内部有定义（var a = 2进行了声明）

于是在**整个函数内隐藏了同名全局变量a**

相当于
```javascript
var a = 1;

function checkVariable() {
	var a;
	console.log(a);
	a = 2;
	console.log(a);
}

checkVariable();
```

### 总结

js变量的域根据方法块划分，而非for、while、if块（因为没有块作用域）

```javascript
function checkVariable() {
	console.log(i);

	for (var i = 0; i < 3; i++) {
		console.log(i);
	}

	console.log(i);

	while (true) {
		var j = 1;
		break;
	}

	console.log(j);

	if (true) {
		var k = 1;
	}

	console.log(k);
}

checkVariable();

console.log(i);
console.log(j);
console.log(k);
```

```
0
1
2
3
1
1
Uncaught ReferenceError: i is not defined
```

js在执行前对整个脚本做分析（包括局部变量）以确定变量是局部变量还是全局变量

```javascript
var a = 1;
function checkVariable () {
	console.log(a);
	a = 2;
	console.log(a);
	var a;
	console.log(a);
}
checkVariable();
console.log(a);
```

```
undefined
2
2
1
```

函数中声明的变量在整个函数中都有定义（即使在声明之前）

这里虽然a一开始未使用var声明，但在js一开始的扫描时发现后面有var对a进行声明，所以a不会暴露在全局，覆盖同名全局变量a，所以函数外面的输出1

由于a在函数内部后面是使用var声明的，所以在前面的输出中能够输出2，此时其实a已经被声明了，整个函数内部是整体分析的，所以才会出现函数中的变量在声明之前被定义（a = 2）

参考：<a href="https://www.w3cplus.com/javascript/the-basics-of-variable-scope-in-javascript.html">https://www.w3cplus.com/javascript/the-basics-of-variable-scope-in-javascript.html</a>

---
title: js学习笔记——变量作用域
date: 2018-01-20 10:53:32
update: 2018-1-20 12:08:15
categories: 前端
tags: 
 - javascript
---

js变量作用域探析

<!--more-->

### 背景

```javascript
var o = {};
o.fun = test();
o.fun();
function test() {
	var fn = function() {console.log(this)};
	return fn;
}
```

```
{fun: ƒ}
```

由于对这个程序的输出结果感到不解，于是决定深入了解js作用域究竟是什么个东西

参考：<a href="https://msdn.microsoft.com/zh-cn/library/bzt2dkta(v=vs.94).aspx">https://msdn.microsoft.com/zh-cn/library/bzt2dkta(v=vs.94).aspx</a>

### 变量作用域

又要谈到js的两个范围：全局和局部

**函数之外**声明的变量就是全局变量，可在整个程序中访问和修改

**函数内**声明的变量是局部变量，每当执行程序时都会创建和销毁该变量（所以称临时变量），并且无法通过函数之外的任何代码访问该变量（MSDN如是说）

js不支持块范围（通过一组大括号定义新范围），于是就只有全局（函数外）和局部（函数内），以函数划分的这两种范围

### js范围

```javascript
var aNumber = 100;
tweak();

function tweak() {
  document.write(aNumber);

	if (false) {
		var aNumber = 123;
	}
}
```

```
undefined
```

> 虽然if（false），var aNumber = 123不会执行，但是js在执行函数tweak()时，会查找所有**变量声明**，var aNumber被扫描到，js使用初始值**undefined**创建变量，即使使用一个值声明变量var aNumber = 123，但aNumber变量的**初始值**仍为**undefined**，仅当**执行包含声明的行**（var aNumber = 123）时才会使用已声明的值。


js会在执行任何代码之前处理所有变量声明，无论是在条件块中声明还是在其他构造中声明

js一旦找到所有变量，就会执行函数中的代码。如果函数内部**隐式声明**变量，即变量出现在赋值表达式左侧，但尚未使用**var**进行声明，则它将创建为**全局变量**

js中，内部（嵌套）函数将存储对局部变量的引用（即使在函数返回之后），这些局部变量存在于与函数本身**相同的范围**，这一组引用称为**闭包**。

```javascript
function send(name) {
  return function() {
		sendHi(name);
	}
}

function sendHi(msg) {
	console.log('Hello' + msg);
}

var func = send('Bill');
func();
sendHi('Pete');
func();
```

```
HelloBill
HelloPete
HelloBill
```

对内部函数的第二次调用输出与第一次调用相同，因为**外部函数**的输入参数name存储在**内部函数闭包**中的局部变量

### 块范围变量

IE11引入对let和const这两个块范围变量的支持

```javascript
let x = 10;
var y = 10;
{
    let x = 5;
    var y = 5;
    {
        let x = 2;
        var y = 2;
        document.write("x: " + x + "<br/>");
        document.write("y: " + y + "<br/>");
        // Output:
        // x: 2
        // y: 2
    }
    document.write("x: " + x + "<br/>");
    document.write("y: " + y + "<br/>");
    // Output:
    // x: 5
    // y: 2
}

document.write("x: " + x + "<br/>");
document.write("y: " + y + "<br/>");
// Output:
// x: 10
// y: 2
```

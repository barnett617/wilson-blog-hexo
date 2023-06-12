---
title: javascript常见问题整理
date: 2018-02-15 15:16:31
update: 2018-02-15 15:16:31
categories: 前端
tags: 
 - javascript
---

25个最基本的javascript问题整理

<!--more-->

1. 使用typeof bar === "object"判定bar是否是对象的**潜在**陷阱是什么？**如何避免**该陷阱？

> 虽然typeof bar === "object"是检查bar是否为对象的可靠方法，但是javascript中**null**也被认为是**对象**


要注意类似以下的typeof使用场景

```javascript
var bar2;
typeof bar2;
"undefined"
typeof undefined
"undefined"
typeof "undefined"
"string"
typeof null
"object"
```

要避免此类问题，可以同时检查bar是否为null，如下：

```javascript
var bar = null;
console.log((bar !== null) && (typeof bar === "object"));
VM489:2 false
```

当bar为函数时，大多数情况上述值返回false是期望行为（因为函数不同于对象），但如果想要对函数返回true的话，可以修改为：

```javascript
function bar() {};
console.log((typeof bar !== null) && (typeof bar === "object") || (typeof bar === "function"));
VM757:2 true
```

当bar为一个数组时，返回值为true为期望行为（因为数组也是对象）

```JavaScript
var bar = [];
console.log((typeof bar !== null) && (typeof bar === "object") || (typeof bar === "function"));
VM926:2 true
```

但如果想让数组返回false，可修改为：

```javascript
var bar = [];
console.log((typeof bar !== null) && (typeof bar === "object") && (toString.call(bar) !== "[object Array]"));
VM1133:2 false
```

如果使用jQuery可为：

```JavaScript
var bar = [];
console.log((typeof bar !== null) && (typeof bar === "object") && (! $.isArray(bar)));
```

拓展：call方法

2. 以下输出什么？为什么？

```javascript
(function() {
	var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));
console.log("b defined? " + (typeof b !== 'undefined'));
```

> 由于a和b都定义在函数的封闭范围内，并且都始于var关键字，大多数情况期望上面结果都是undefined，大多数情况上述的var a = b = 3；等同于var b = 3; var a = b；但**实际上**是b = 3; var a = b；所以如果不使用严格模式，会输出如下：

```javascript
(function() {
	var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));
console.log("b defined? " + (typeof b !== 'undefined'));
VM1296:6 a defined? false
VM1296:7 b defined? true
```

> 因为var a = b = 3;相当于b = 3; var a = b，b没有使用var声明，所以变成全局变量

3. 以下输出什么？为什么

```javascript
var myObject = {
	foo: "bar",
	func: function() {
		var self = this;
		console.log("outer func: this.foo = " + this.foo);
		console.log("outer func: self.foo = " + self.foo);
		(function() {
			console.log("inner func: this.foo = " + this.foo);
			console.log("inner func: self.foo = " + self.foo);
		}());
	}
};
myObject.func();
```

```javascript
outer func: this.foo = bar
VM1353:6 outer func: self.foo = bar
VM1353:8 inner func: this.foo = undefined
VM1353:9 inner func: self.foo = bar
```

> 在外部函数中，this和self都指向了myObject，所以两者都能正确地引用和访问foo；内部函数中，this不再指向myObject，所以此时this.foo未在内部函数中定义，相反指向到本地的变量self保持在范围内，且可以访问（**ES5以前**，内部函数中的this将指向全局的**window**对象，**ES5**内部函数中的**this**是**未定义**的）

4. 封装javascript源文件的全部内容到一个函数块有什么意义及理由？

> 这是越来越普遍的做法，被许多流行js库所采用（例如jQuery、Node.js）。这种技术创建了一个围绕**文件全部内容**的闭包，并且很重要的是创建了一个**私有**的**命名空间**，从而避免不同javascript模块和库之间潜在的**名称冲突**。

> 这种技术的另一个特点是允许一个**易于引用**的**别名**用于**全局变量**

> 例如jQuery插件中允许使用jQuery.noConflict()来禁用$引用到jQuery命名空间，在此之后仍可使用$利用这种闭包技术，如下：

```javascript
(function($) { /* jQuery plugin code referencing $ */ })(jQuery);
```

5. javascript源文件开头包含use strict有什么意义和好处？

严格模式的一些优点：

- 使调试更加容易——那些被忽略或默默失败的代码错误会产生错误或抛出异常

- 防止意外的全局变量——非严格模式下将值分配一个未声明的变量会自动创建该名称的全局变量，但严格模式下会报错

- 消除this强制——非严格模式下，引用**null**或**未定义的值**到**this值**会**自动强制**到**全局变量**

- 不允许重复的属性名称或参数值——当检测到**对象**中**重复命名属性**（var object = {foo: 'bar', foo: 'baz'};）或函数中重复命名参数（function foo(val1, val2, val1) {}）会报错，因此可捕获代码中的bug以避免大量的跟踪时间

- 使用eval()更安全——严格模式下，**变量**和**声明在eval()语句内部的函数**不会在**包含范围内**创建，而会在非严格模式下创建

- 在delete使用无效时报错——delete不能用在对象不可配置的属性上，非严格模式时会静默失败，严格模式下会报错

6. 以下两个函数会返回相同的东西吗？为什么相同或不同？

```javascript
function foo1() { return {
	bar: "hello"
	};
}

function foo2() { return
	{
		bar: "hello"
	};
}
```

```javascript
function foo1() { return {
	bar: "hello"
	};
}
console.log(foo1());
VM1405:5 {bar: "hello"}
undefined
function foo2() { return
	{
		bar: "hello"
	};
}
console.log(foo2());
VM1441:6 undefined
```

> 原因在于javascript中的分号是一个可选项，但省略其将会是非常糟糕的形式。当碰到foo2()中包含return语句的代码行（代码行上没有任何代码），分号会立即**自动插入**到**返回语句**后。但不会报错，因为后面的代码是有效的，相当于是一个未使用的代码块，定义了等同于字符串"hello"的属性bar

7. NaN是什么？它的类型是什么？如何可靠地测试一个值是否等于NaN？

> NaN表示一个“不是数字”的值，因为运算不能执行而得。运算不能执行的原因有：其中的运算对象之一不是数字或运算结果不是数字。

> 虽然NaN表示“不是数字”，但是其类型是Number

```javascript
typeof NaN
"number"
```

> NaN和**任何东西**（包括其自身）都是false

```javascript
NaN == NaN
false
NaN === NaN
false
NaN === "hello"
false
NaN == "hello"
false
```

一种比较好的测试一个数字是否等于NaN的方式是使用内建函数isNaN()，但不是最好的方案

更好的解决方案是使用value !== value

```javascript
4 !== 4
false
4 !== "4"
true
```

但如果value就是NaN，那么仍然遵守“NaN和任何东西比较都是false”

```javascript
1 / "s" !== 1 / "s"
true
```

ES6提供一个新的Number.isNaN()函数，比isNaN()更可靠

8. 下列输出什么？为什么？

```javascript
console.log(0.1 + 0.2);
console.log(0.1 + 0.2 == 0.3);
```

```javascript
console.log(0.1 + 0.2);
console.log(0.1 + 0.2 == 0.3);
VM1624:1 0.30000000000000004
VM1624:2 false
```

JavaScript中的数字和浮点精度的处理相同，因此可能不会总是产生预期的结果

9. 讨论写函数isInteger(x)的可能方法，用于确定x是否是整数

ES6中提供了解决方案：Number.isInteger()

但ES规格说明中，整数只是概念上存在，即：数字值**总是**存储为**浮点值**

ES6之前的做法：

```javascript
function isInteger(x) {
	return (x^0) === x;
}
```

^为位运算符中的XOR异或运算，二者（二进制形式）不同则为真，二者相同则为假，任何值和0求异或都是其本身，但浮点数例外，浮点数只会求其整数部分

```javascript
0^0
0
12^0
12
0^12
12
```

```javascript
1.1^0
1
1.5^0
1
1.9^0
1
```

这种方式可以当输入非数字值，例如null或字符串时稳健地返回false

或者没有上面方法优雅的方案：

```javascript
function isInteger(x) {
	return Math.round(x) === x;
}
```

Math.round()为四舍五入，相当于Math.ceil()进一法，Math.floor()退一法的集合

```javascript
Math.round(3.2);
3
Math.round(3.5);
4
Math.round(3.9);
4
Math.ceil(3.2);
4
Math.floor(3.9);
3
```

或者

```javascript
function isInteger(x) {
	return (typeof x === 'number') && (x % 1 === 0);
}
```

是数字的同时模1为0

```javascript
3 % 1
0
3.1 % 1
0.10000000000000009
NaN % 1
NaN
```

不正确的一个方案是：

```javascript
function isInteger(x) {
	return parseInt(x, 10) === x;
}
```

> 这种方案在大多数情况不暴露问题，但当x相当大的时候无法正常工作，因为parseInt在解析数字之前强制其第一个参数到字符串，所以当数字十分大时，其字符串表达形式为指数形式（1e+21），此时parseInt去解析1e+21，当达到e时则停止，会只返回1

```javascript
String(10000000000000000000000000)
parseInt(10000000000000000000000000)
1
```

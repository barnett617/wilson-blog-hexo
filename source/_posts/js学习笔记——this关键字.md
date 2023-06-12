---
title: js学习笔记——this关键字
date: 2018-01-19 18:22:06
update: 2018-1-20 09:31:10
categories: 前端
tags: 
 - javascript
---

js关键字this用法解析

<!--more-->

### 解释

函数运行时自动生成的一个内部对象，只能在函数内部使用（想起了Python函数的self）

```javascript
function test() {
    this.x = 1;
}
```

函数使用场合不同，this的值会发生变化

但this总指的是**调用函数**的那个**对象**

### 使用场景

1. 纯粹函数调用（全局调用）

2. 函数作为对象方法的调用

3. 作为构造函数调用

4. apply调用


#### 1.纯粹函数调用

全局性调用，this代表全局对象Global

```javascript
function test() {
  this.x = 1;
  alert(this.x);		
}

test();
```

```
1
```

```javascript
var x = 1;
function test() {
	alert(this.x);		
}

test();
```

```
1
```

不会报错，因为相当于alert(Global.x)

```javascript
var x = 1;
function test() {
	this.x = 0;
}
test();
alert(x);		
```

```
0
```

test()之后相当于将全局变量的x重新赋值0，Global.x = 0

#### 2.作为对象方法的调用

函数作为某个对象o的方法，this指的是这个上级对象o

```javascript
function test() {
	alert(this.x);
}

var o = {};
o.x = 1;
o.m = test;
o.m();		
```

```
1
```

此时test()函数作为对象o的方法，则test方法中的this指的是o这个对象

#### 3.作为构造函数调用

构造函数：通过这个函数生成一个新对象（object），此时this指这个新对象

```javascript
function test() {
	this.x = 1;
}

var o = new test();
alert(o.x);		
```

```
1
```

this就是当前通过构造函数生成的对象o

```javascript
var x = 2;
function test() {
	this.x = 1;
}
var o = new test();
alert(x);		
alert(o.x);
```

```
2
1
```

构造函数赋值的x是o的变量x，而不是全局变量x

以上三个很明显都是围绕**调用函数**的**对象**

#### 4.apply调用

apply是**函数对象**的一个方法，作用是**改变函数的调用对象**

第一个参数表示改变后的调用这个函数的对象，this指的就是这第一个参数

```javascript
var x = 0;

function test() {
	alert(this.x);
}

var o = {};

o.x = 1;
o.m = test;
o.m.apply();		
```

```
0
```

apply()参数为空时，默认调用全局对象（此时this指的是全局对象）

全局对象的x是0

其实还是实际调用函数的那个对象

```javascript
var x = 0;

function test() {
	alert(this.x);
}

var o = {};

o.x = 1;
o.m = test;
o.m.apply(o);		
```

```
1
```

改变调用这个函数的对象，虽然本来调用test函数的对象就是o

但是如果调用apply函数不填参数，就会变成全局对象去调用test方法

### 总结

Is that clear?

一句话总结：this就是调用函数的那个对象

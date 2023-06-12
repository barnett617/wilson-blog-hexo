---
title: js学习笔记——一些常用的高级属性
date: 2018-01-20 14:11:34
update: 2018-1-20 20:27:14
categories: 前端
tags: 
 - javascript
---

MSDN学习js高级教程note

<!--more-->

## 构造函数

### 使用构造函数定义类型（type）

#### 概念

构造函数是一个为Object初始化一个特定类型的函数

使用new关键字调用构造函数

这里有一些js内建对象和自定义对象的例子

```javascript
var myObject = new Object();
var myBirthday = new Date(1993, 11, 12);
var myCar = new Car();
```

构造函数包含this关键字，它是对新创建的**空对象**的**引用**（reference）

它通过**创建属性**并为他们**赋初始值**来**初始化新对象**

构造函数将返回对**所创建对象**的引用

```javascript
function Circle (xPoint, yPoint, radius) {
  this.x = xPoint;
	this.y = yPoint;
	this.radius = radius;
}
```

```javascript
var aCircle = new Circle(5, 11, 99);
```

使用自定义构造函数创建的所有对象的类型都是object

js只有6中类型：object、function、string、number、boolean、undefined

typeof**操作符**以**字符串**形式返回**类型**信息

```javascript
var index = 5;
var result = (typeof index === 'number');

var description = "abc";
var result = (typeof description === 'string');
```

```
true
true
```

为声明和未声明的变量测试undefined数据类型

```javascript
var declared;
var result1 = (typeof declared === 'undefined');
console.log(result1);

var result2 = (typeof notDeclared === 'undefined');
console.log(result2);

var obj = {};
var result3 = (typeof obj.propNotDeclared === 'undefined');
console.log(result3);
```

```
true
true
true
```

```javascript
var result = (notDeclared === 'undefined');
console.log(result);
```

```
Uncaught ReferenceError: notDeclared is not defined
```

未声明的变量不能用于比较

### 数组

Array.isArray(object)判断一个对象是否是一个数组

如果对象是一个数组则返回true，如果对象不是一个数组或参数不是一个对象（object）则返回false

创建数组方式：

arrayObj = new Array()
arrayObj = new Array(size)
arrayObj = new Array()

arrayObj必需，Array对象分配到的变量
size可选，从0开始（包括0）的整数，数组索引范围为[0,size-1]
element可选，数组内所放置的元素，这样会创建n+1个元素（n为数组索引最大值），这种数组创建方式必需提供至少一个元素

```javascript
var d = new Array("test", 3);
undefined
d
["test", 3]
```

```javascript
var ar = [];
var result1 = Array.isArray(ar);
console.log(result1);

var ar = new Array();
var result2 = Array.isArray(ar);
console.log(result2);

var ar = [1, 2, 3];
var result3 = Array.isArray(ar);
console.log(result3);

var result = Array.isArray("an array");
console.log(result);
```

```
true
true
true
false
```

### 数组对象

Array支持创建**任何数据类型**的数组

数组创建后，可通过[]表示法来访问每个独立的元素

js数组从0开始

```javascript
var my_array = new Array();
for (i = 0; i < 10; i++) {
	my_array[i] = i;
}
x = my_array[4];
console.log(x);
console.log(my_array[10]);
```

```
4
undefined
```

可以传递一个无符号32位整数给Array构造函数类指定数组的大小

如果值为负或者非整数，会出现运行时错误

```javascript
var arr1 = new Array(10);
console.log(arr1.length);

var arr2 = new Array(-1);
console.log(arr2);
arr3 = new Array(1.50);
console.log(arr3);
```

```
10
Uncaught RangeError: Invalid array length
Uncaught RangeError: Invalid array length
```

### 原型和原型继承

prototype是**函数**的一个**属性**，同时也是由**构造函数**创建的**对象**（instance）的一个属性

函数的原型为对象，主要在函数用作构造函数时使用


#### 使用原型添加属性和方法

可使用prototype属性向对象添加属性和方法，甚至于已创建的对象是也是如此

```javascript
function Vehicle(wheels, engine) {
	this.wheels = wheels;
	this.engine = engine;
}
var testVehicle = new Vehicle(2, false);
Vehicle.prototype.color = "red";
var testColor = testVehicle.color;
console.log(testColor);
```

```
red
```

向预定义的对象添加属性和方法，例如：在String原型上定义一个Trim方法，脚本中所有的字符串都将继承该方法

```javascript
String.prototype.trim = function() {
	return this.replace(/(^\s*)|(\s*$)/g, "");
}
var s = "   leading and trailing spaces   ";
window.alert(s + "(" + s.length + ")");
s = s.trim();
window.alert(s + "(" + s.length + ")");
```

```
leading and trailing spaces(33)
leading and trailing spaces(27)
```

Nice, isn't it?

##### 使用原型通过Object.create从一个对象派生另一个对象

例如：；利用Object.create函将使之前定义的Vehicle对象的原型（以及所需的任何新属性）派生出一个新的对象Bicycle

```javascript
var Bicycle = Object.create(Object.getPrototypeOf(Vehicle), {
    "pedals" : {value: true}
});
```

参考：<a href="https://msdn.microsoft.com/zh-cn/library/b9w25k6f(v=vs.94).aspx">https://msdn.microsoft.com/zh-cn/library/b9w25k6f(v=vs.94).aspx</a>

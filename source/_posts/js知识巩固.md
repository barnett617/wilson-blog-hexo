---
title: JavaScript知识巩固
date: 2018-02-01 21:28:22
update: 2018-2-6 11:03:28
categories: 前端
tags: 
 - javascript
---

Refresh your JavaScript Knowledge

<!--more-->

### JavaScript知识巩固

#### 前言

##### 诞生

JavaScript于1995年被网景的一名工程师Brendan Eich所创造

第一次发布于1996年早期的Netscape 2

最初叫LiveScript

但由于不幸的营销决策，为了利用Java的热度，而改名为JavaScript（但与Java毫无联系）

##### 发展历程

几个月后，微软在IE 3中发布了JScript

几个月后，网景向ECMA（欧洲标准组织）国际组织提交了JavaScript

于是在那一年诞生了ECMAScript标准的第一个版本

这个标准在1999年收到了一个具有象征意义的更新，定为ECMAScript第三版本，从那以后版本趋于稳定

由于有关语言复杂性的政治分歧，第四个版本被废弃掉

随后第四版本的许多部分成为了第五个版本的基础，发版于2009年12月

第六版发布于2015年6月

##### 应用场景

不像大多数语言，JavaScript没有输入和输出的概念

它被设计用来在宿主环境作为一种脚本语言运行，并且由宿主环境决定其与外部世界沟通的机制

最普遍的宿主环境是**浏览器**

但JavaScript解释器还能在以下地方找到：

- Adobe Acrobat

- Adobe Photoshop

- SVG images（矢量图）

- Yahoo's Widget engine（雅虎组件引擎）

- 服务端环境，例如Node.js

- 非关系型数据库，例如开源的Apache CouchDB

- 嵌入式计算机

- 复杂的桌面环境，例如GNOME（GNU/Linux操作系统最著名的图形界面系统之一）

- 其他

#### 概述

JavaScript是一种多范式、动态语言，拥有类型、操作符、标准内建对象和方法

它的语法基于Java和C（许多这二者语言的结构都被应用于JavaScript）

JavaScript支持面向对象编程通过使用对象原型（object prototypes）取代类（更多见**<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain">原型继承</a>**和ES2015**<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes">类</a>**概念）

JavaScript也支持函数式编程——函数是对象，给予函数容纳可执行代码的能力并将像其他对象一样进行传递

##### JavaScript类型

1. Number

2. String

3. Boolean

4. Function

5. Object

6. Symbol（ES2015新加）

技术上更严谨的分类如下：

1. Number

2. String

3. Boolean

4. Symbol（ES2015新加）

5. Object

	- Function

	- Array

	- Date

	- RegExp

	- Math

6. null

7. undefined

还有一些内建的Error类型

###### Number

JavaScript的Numbers是double-precision 64-bit format IEEE 754 values

由于这个特点，JavaScript中没有整型，所以在C或Java中使用到的算数运算要在JavaScript中留意

比如：

```
0.1+0.2
0.30000000000000004
```

实践中，整型值会被当做32位整数，并且甚至有些实现以这种方式存储，直到被要求去执行一条在Number上有效但在32位整型上无效的指令，这对于位运算来说很重要

> 原文：In practice, integer values are treated as 32-bit ints, and some implementations even store it that way until they are asked to perform an instruction that's valid on a Number but not on a 32-bit integer. This can be important for bit-wise operations.

标准的算数运算符被支持，包括加、减、取模、取余等等

内建对象Math提供了高级数学运算函数和常量

```
Math.sin(3.5);
-0.35078322768961984
```

```
r = 2;
var circumference = 2 * Math.PI * r;
console.log(circumference)
```

使用内建函数parseInt()可以将一个字符串转换为整型数，但是要注意给该函数指定第二个参数（要转换的进制），如果不填会得到意想不到的结果

```
parseInt('010')
10
parseInt('0x10')
16
parseInt('010', 8)
8
parseInt('0x10', 16)
16
parseInt('11',2)
3
```

转换为八进制省略第二个参数的方式在2013年后被废除，但十六进制忽略第二个参数的用法仍存在，因为可以识别到十六进制前缀`0x`

还有内建函数parseFloat用于将字符串转换为浮点数，但不同于parseInt()，它总是默认以十进制方式转换

另外，还可以通过一元运算符`+`将值转换为数值

```
parseFloat('12.34')
12.34
+ '56.78';
56.78
+ '0x10'
16
+ '42'
42
```

如果字符串是非数值，转换会返回一个特殊值NaN（Not a Number）

```
parseInt('hello', 10)
NaN
```

如果将NaN作为输入，做任何算数运算所得都是NaN

```
parseInt('hello', 10) + 5
NaN
```

可通过内建函数isNaN()判定是否为NaN

```
isNaN(parseInt('hello', 10) + 5)
true
```

JavaScript还提供了特殊值：Infinity和-Infinity

```
1 / 0;
Infinity
-1 / 0;
-Infinity
```

可以使用内建函数isFinite()判断Infinity、-Infinity和NaN

```
isFinite(1 / 0);
false
isFinite(-1 / 0);
false
isFinite(NaN);
false
```

> parseInt()、parseFloat()和`+`的区别：前两者会将字符串转换，直到遇到不是有效的数字止，而`+`会直接将字符串转换为NaN如果字符串内包含无效字符

```
parseInt('10.2abc');
10
+ '10.2abc'
NaN
```

###### String

JavaScript中的字符串是Unicode字符序列，这对于处理国家化问题来说很方便，更准确地讲，是sequences of UTF-16 code units，每一个码单元通过一个16位数字呈现，每一个Unicode字符通过1个或2个码单元呈现

如果想呈现一个单字符，只需要使用一个包含单个字符的字符串

如果想知道一个（码单元中的）字符串的长度，访问其length属性

```
'hello'.length
5
```

字符串也可当做对象，并通过方法来操作字符串的信息

```
'hello'.charAt(0);
"h"
'hello, world'.replace('hello', 'hola');
"hola, world"
'hello'.toUpperCase();
"HELLO"
```

###### 其他类型

JavaScript用null表示non-value（并且也仅能通过`null`访问）

还有undefined表示一个尚未初始化的值（表示一个还未被赋值的变量）

如果你声明一个变量，但没对其赋值（assign），这个变量的类型就是undefined，但undefined实际上是一个常量

JavaScript还有一个布尔类型，只有两个值，true和false

任何值都能被转换成一个布尔值通过以下方式：

1. false、0、空字符串（""）、NaN、null和undefined

2. 所以其他都被判定为布尔中的true

使用Boolean()函数来具体实现

```
Boolean('');
false
Boolean(234);
true
```

这很少情况需要这样处理，当JavaScript期望一个布尔值时，会静默执行布尔转换，例如`if`statement

布尔操作符，例如&&（逻辑与）、||（逻辑或）和！（逻辑非）都被支持

###### 变量

JavaScript通过三个关键字声明新的变量：let、const和var

let允许声明块级变量，其声明的变量仅在封闭块中有效

```
for (let i = 1; i < 5; i++) {
  console.log(i);
}
console.log(i);
VM959:2 1
VM959:2 2
VM959:2 3
VM959:2 4
VM959:4 Uncaught ReferenceError: i is not defined
    at <anonymous>:4:13
```

const允许声明永远不会企图改变的变量，并且也仅在所声明的块范围内有效

```
for (const j = 2; j < 4; j++) {
  console.log(j);
}
console.log(j);
VM1175:2 2
VM1175:1 Uncaught TypeError: Assignment to constant variable.
    at <anonymous>:1:27
```

var是最通用的声明关键字，它没有let和const的限制

它是传统JavaScript唯一的声明变量的关键字

```
for (var k = 3; k < 7; k ++) {
  console.log(k);
}
console.log(k);
VM1257:2 3
VM1257:2 4
VM1257:2 5
VM1257:2 6
VM1257:4 7
```

JavaScript和其他语言（比如Java）很重要的一个区别是代码块并没有域，只有函数才有域

所以如果在一个compound statement（例如在if控制结构范围内）内使用var声明的变量在整个函数范围内都可见，如上例

然而，从ES2015开始，let和const的声明允许创建块级域变量

###### 运算符

JavaScript数值运算符有`+`、`-`、`*`、`/`和`%`

通过`=`赋值

还有一些复合赋值操作，例如`+=`和`-=`，这相当于x = x + y或x = x - y

你可以使用`++`和`--`分别表示递增和递减，这些都可以被用作运算符前缀或后缀

`+`运算符还可用作字符串连接符

```
'hello' + 'world';
"helloworld"
```

如果你把一个字符串追加于一个数字或其他值，都会首先被转化为一个字符串

```javascript
'3' + 4 + 5;
"345"
3 + 4 + '5';
"75"
```

为某个值追加一个空字符串是一个将其转换为字符串的方式

JavaScript使用`<`、`>`、`<=`和`>=`进行比较操作

这些既对字符串有效，也对数字有效

判断两个值相等并不是那么直接，如果给双等运算符`==`两个不同类型的值，会表现出类型约制

```javascript
123 == '123';
true
1 == true;
true
```

为避免约制，使用三等运算符

```javascript
123 === '123';
false
1 === true;
false
```

还有!=和!==

JavaScript还有**<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators">位运算符</a>**

###### 控制结构

JavaScript有一套类似C语言家族的控制结构

条件语句通过if和else支持

```javascript
var name = 'hello';
if (name == "test") {
  name += 'test';
} else if (name == 'hello') {
  name += 'world';
} else {
  name += '!';
}
name == 'helloworld';
true
```

JavaScript拥有while循环和do-while循环，前者用于基本循环，后者用于你想确保循环至少执行一次

```javascript
while (true) {
  // an infinite loop!
}

var input;
do {
  input = get_input();
} while (inputIsNotValid(input));
```

JavaScript的for循环和C还有Java的一样，使你能够在一行内提供控制信息

```javascript
for (var i = 0; i < 5; i++) {
  // Will execute 5 times
}
```

JavaScript还有两个高级for循环

- for of

```javascript
for (let value of array) {
  // do something with value
}
```

- for in
```javascript
for (let property in object) {
  // do something with object property
}
```

&&和||运算符有短路逻辑，意味着第二个运算值的是否执行决定于第一个运算值

这有助于检查空对象在访问其属性之前

```javascript
o = null;
var name = o && o.getName();
null
```

或进行缓存值（当假值无效时）??

```
var name = cachedName || (cachedName = getName());
```

JavaScript对条件语句拥有一个三元运算符

```javascript
age = 19;
var allowed = (age > 18) ? 'yes' : 'no';
console.log(allowed);
VM1682:3 yes
```

`switch`语句可被用于基于一个数字或字符串的多分支判断

```javascript
switch (action) {
  case 'draw':
    drawIt();
    break;
  case 'eat':
    eatIt();
    break;
  default:
    doNothing();
}
```

如果你没有添加break语句，将会在该条件下的内容执行后继续向下执行，这可能并不是你想要的，如果你的确想这么做用于调试，请添加注释表明

```javascript
switch (a) {
  case 1: // fallthrough
  case 2:
    eatIt();
    break;
  default:
    doNothing();
}
```

默认条款是可选的，你在switch部分和case部分都可以有**表达式**，比较会在二者使用了===运算符时发生

```javascript
switch (1 + 3) {
    case 2 + 2:
      console.log('execute 4');
      break;
    default:
      neverhappens();
}
VM1831:3 execute 4
```

###### 对象

JavaScript的对象可以理解为一个键值对集合，类似于：

- Python中的字典

- Perl和Ruby中的Hashes

- C和C++中的哈希表

- Java中的HashMap

- PHP中的关联数组

事实上这个数据结构被如此广泛的使用，是其多才多艺的一个佐证

因为JavaScript中的一切都是对象

任何JavaScript程序自然包含着一个强大的哈希表查找，这是个好事，因为很快

JavaScript对象的“键”部分是一个字符串，“值”部分可以是任何值

这允许你可以构造任意复杂的数据结构

有两种基本的创建对象的方法：

```javascript
var obj = new Object();
```

和

```javascript
var obj = {};
```

这二者语义上相等，后者称为object literal syntax，并且更方便

这种语法也是JSON格式的核心并总被偏爱

文字对象语法可以用来完整初始化一个对象：

```javascript
var obj = {
  name: 'test',
  _for: 'max',
  details: {
    color: 'orange',
    size: 12
  }
};
obj

{name: "test", _for: "max", details: {…}}
```

属性可被链接到一起

```javascript
obj.details.color;
"orange"
obj['details']["color"]
"orange"
```

下面的例子创建了一个对象原型Person和一个原型实例You

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

var you = new Person('You', 24);
console.log(you);
VM2069:7 Person {name: "You", age: 24}
```

一经创建，一个对象的属性可被再次访问用以下两种方式：

```javascript
obj.name = 'magi';
var name = obj.name;
"magi"
```

```javascript
obj["name"] = 'igma';
var name = obj["name"];
"igma"
var user = prompt('what is your key?');
obj[user] = prompt('what is your value?');
"111"
```

这些也语义上相等，后者优势在于name属性作为一个字符串被提供，意味着可以在运行时被计算

然而，使用这种方式可以防止了有些JavaScript引擎和优化器被应用

另外，也因此可以使用关键字来设置和获得属性

```javascript
obj.for = 'Simon'; // Syntax error, because 'for' is a reserved word
obj['for'] = 'Simon'; // works fine
```

> 从ECMAScript 5开始，保留字可以用作对象属性名in the buff。这意味着定义对象时不再需要引号来包裹，详情见<a href="http://es5.github.io/#x7.6.1">the ES5 Spec</a>

更多关于对象和原型，见<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype">Object.prototype</a>

关于对象原型和对象原型链，见<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain">继承和原型链</a>

从ECMAScript2015开始，对象的键可以被使用括号符的变量定义

```javascript
var userPhone = {['phoneType']: 12345};
console.log(userPhone);
VM2470:2 {phoneType: 12345}
```

可以代替

```javascript
var userPhone = {};
userPhone['phoneType'] = 12345;
console.log(userPhone);
VM2505:3 {phoneType: 12345}
```

###### 数组

JavaScript中的数组其实一种特殊类型的对象

和常规的对象非常像（数值属性只能使用[]语法访问）

但有一个神奇的属性叫做“length”

其总是比数组最大索引值多一位

创建数组的方式如下：

```javascript
var a = new Array();
a[0] = 'dog';
a[1] = 'cat';
a[2] = 'hen';
a.length;
3
```

一个更方便的表示方式是使用数组文字（array literal）

```javascript
var a = ['dog', 'cat', 'hen'];
a.length;
3
```

注意array.length不必是数组项目的个数，考虑下面一种情况

```javascript
var a = ['dog', 'cat', 'hen'];
a[100] = 'fox';
a.length;
101
```

谨记：数组的长度总比数组索引最大值大一位

如果访问一个不存在的数组索引，会得到一个值为undefined的返回值

```javascript
typeof a[90];
"undefined"
```

如果把上面的[]和length纳入考虑，你可以使用for循环迭代一个数组

```javascript
for (var i = 0; i < a.length; i++) {
  console.log(a[i]);
}
VM302:2 dog
VM302:2 cat
VM302:2 hen
97VM302:2 undefined
VM302:2 fox
```

ECMAScript介绍了一种更加简明的循环for of，用来迭代对象，比如数组：

```javascript
for (const currentValue of a) {
	console.log(currentValue);
}
VM394:2 dog
VM394:2 cat
VM394:2 hen
97VM394:2 undefined
VM394:2 fox
```

你也可以使用for in循环来迭代一个数组，但如果有人向Array.prototype添加了新的属性，它（新加的属性）在本次循环也会被迭代。所以这种循环类型不被推荐用于数组迭代

另一种在ECMAScript5中添加的用来迭代数组的方式是forEach()

```javascript
['dog', 'cat', 'hen'].forEach(function(currentValue, index, array) {
	console.log('currentIndex: ' + index + ' currentValue: ' + currentValue + ' array: ' + array);
});
VM486:2 currentIndex: 0 currentValue: dog array: dog,cat,hen
VM486:2 currentIndex: 1 currentValue: cat array: dog,cat,hen
VM486:2 currentIndex: 2 currentValue: hen array: dog,cat,hen
```

如果想要为一个数组追加元素，只需要简单地：

```javascript
a.push(item);
```

数组方法列表如下：

| Method name | Description | Example |
| ----------- | ----------- | ------- |
| a.toString | 返回数组的每一个元素以逗号分隔的字符串 |dog,cat,hen,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,fox |
| a.toLocaleString() | 同toString，不过先判断指定语言环境，没指定则使用默认语言环境，主要用于Date类型 | - |
| a.concat(item1[, item2[, ...[,itemN]]]) | 返回一个追加于其后的一个新的数组 | a.concat('panda','seal')-> [empty × 97, "hen", "cat", "dog", "panda", "seal"] |
| a.join(sep) | 转换数组为一个字符串，伴随着以sep参数分隔的值 |dog-cat-hen--------------------------------------------------------------------------------------------------fox |
| a.pop | 移除并返回最后一项 | "fox" |
| a.push(item1, ..., itemN) | 向数组末端追加元素，并返回追加后的数组长度 | 101 |
| a.reverse() | 倒置数组 | (101) ["fox", empty × 97, "hen", "cat", "dog"] |
| a.shift | 移除并返回第一个元素 | "fox" |
| a.slice(start[, end]) | 返回子数组 | a.slice(98,100) -> ["cat", "dog"] |
| a.sort([cmpfn]) | 采用可选比较函数进行排序 | a.sort()->["cat", "dog", "hen", empty × 97] |
| a.splice(start, delcount[, item1[, ...[, itemN]]]) | 让你修改一个数组，通过删除一部分并使用更多条目替换之 |a.splice(0, 2, 'another cat', 'another dog')->["cat", "dog"]->a->["another cat", "another dog", "hen", empty × 97] |
| a.unshift(item1[, item2[, ...[, itemN]]]) | 预先考虑数组开头的条目 | a.unshift('prepand item1', 'prepand item2')->102->a->["prepand item1", "prepand item2", "another cat", "another dog", "hen", empty × 97]

更多详见<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array">array methods</a> & <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleString">toLocaleString</a>

###### 函数

同对象一起，函数也是理解JavaScript的核心组件

最基本的函数不能再简单

```javascript
function add(x, y) {
	var total = x + y;
	return tatal;
}
```

这演示了一个基本的函数

一个JavaScript函数可以有0个或更多的命名参数

函数体可以包含任意多你想要的语句并且可以声明它对于它本身，自己的变量

return语句可以被用来在任何时候返回一个值，中断函数

如果没有返回语句（或者一个空的return而不包含任何值），JavaScript会返回undefined

命名参数比起其他任何事物更像是指导方针，只要你想，你可以调用一个函数而不用传递任何参数，这种情况它会传递undefined作为参数

所以直接运行上面定义的函数，会报错，因为你给函数传递了参数undefined

```JavaScript
function add(x, y) {
	var total = x + y;
	return total;
}
add();
NaN
```

你也可以传递函数期望的更多参数

```JavaScript
function add(x, y) {
	var total = x + y;
	return total;
}
add(2, 3, 4);
5
```

参数“4”会被忽略

这可能会看起来有点蠢，但函数会访问其函数体内部的附加名为arguments的变量，它是一个类数组对象，承载了所有传递给函数的参数

让我们重写这个add函数来取到和我们想要的参数一样多的参数

```JavaScript
function add() {
	var sum = 0;
	for (var i = 0, j = arguments.length; i < j; i++) {
		sum += arguments[i];
	}
	return sum;
}
add(2, 3, 4, 5);
14
```

再写一个平均值函数

```JavaScript
function avg() {
	var sum = 0;
	for (var i = 0, j = arguments.length; i < j; i++) {
		sum += arguments[i];
	}
	return sum / arguments.length;
}
avg(2, 3, 4, 5);
3.5
```

这非常有用，但有一点啰嗦。要再减少一点这份代码，我们可以考虑参数数组作取代，通过<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters">Rest parameter syntax</a>

用这种方式，我们可以保持代码最小化的同时传递任意数量的参数给函数

rest参数运算符用于函数参数列表使用"...variable"格式，它会包含进调用函数时整个未捕获参数列表

我们也可以使用for...of循环取代for循环来返回变量中的值

```javascript
function avg(...args) {
	var sum = 0;
	for (let value of args) {
		sum += value;
	}
	return sum / args.length;
}
avg(2, 3, 4, 5);
3.5
```

在上述的代码中，args变量拥有我们传递进函数的所有函数

很重要需要注意无论何时rest参数运算符被放置在一个函数声明，它都会在它声明后存储所有的参数，但不会在声明之前（存储参数），例如：function avg(firstValue, ...args)将存储被传递进函数的第一个值
在firstValue中，剩余参数存储在args中。另一个有用的函数但也的确给我们带来一个新问题。avg()函数接收一个逗号分隔的参数列表——但是要是你想要得到一个**数组**的平均值？你只能重写这个函数如下：

```javascript
function avgArray(arr) {
	var sum = 0;
	for (var i = 0, j = arr.length; i < j; i++) {
		sum += arr[i];
	}
	return sum / arr.length;
}
avgArray([2, 3, 4, 5]);
3.5
```

但使得这个我们创建的函数可被重用会更好。

幸运的是，JavaScript可以让你使用一个任意的参数数组来调用一个函数，通过使用任何函数对象的apply()方法

```javascript
avg.apply(null, [2, 3, 4, 5]);
3.5
```

apply()的第二个参数是用作参数的数组；第一个参数后面再讨论，这强调了一个事实——函数也是对象

> 你可以在函数调用中使用spread操作符达到相同的结果，例如avg(...numbers)

jJavaScript让你可以创建匿名函数

```JavaScript
var arg = function() {
	var sum = 0;
	for (var i = 0, j = arguments.length; i < j; i++) {
		sum += arguments[i];
    }
	return sum / arguments.length;
};
```

这在语义上等同于function avg()形式

这非常强大，因为它可以让你把一个函数定义放在任何你通常放置表达式的地方

这使得所有种类的聪明的诀窍

这是一种“隐藏”一些本地变量的方式——像C语言中的块级域

```JavaScript
var a = 1;
var b = 2;

(function() {
	var b = 3;
	a += b;
})();

console.log(a);
console.log(b);
VM2347:9 4
VM2347:10 2
```

JavaScript允许你递归调用函数。

这对于处理树结构尤其有用，比如那些浏览器中的DOM

```javascript
function countChars(elm) {
	if (elm.nodeType == 3) {  // TEXT_NODE
		return elm.nodeValue.length;
	}
	var count = 0;
	for (var i = 0, child; child = elm.childNodes[i]; i++) {
		count += countChars(child);
    }
	return count;
}
```

这强调了匿名函数的一个潜在问题：如果它没有名字，你怎么递归地调用它？

JavaScript对于此让你可以给函数表达式命名

你可以使用命名了的IIFEs（Immediately Invoked Function Expression）如下面所示：

```JavaScript
var charsInBody = (function counter(elm) {
	if (elm.nodeType == 3) {  // TEXT_NODE
		return elm.nodeValue.length;
	}
	var count = 0;
	for (var i = 0, child; child = elm.childNodes[i]; i++) {
		count += counter(child);
	}
	return count;
})(document.body);
undefined
charsInBody
58670
```

如上提供给一个函数表达式的名字仅对这个函数自己的域内可用

这允许更多的引擎优化并给出更多的可读代码

这个名称也出现在调试器和一些堆栈信息中，这会让你在调试时节省更多时间

注意JavaScript函数本身就是对象——像JavaScript中其他的一切一样——并且你可以添加或改变他们的属性，就像我们在对象部分所见过的

###### 自定义对象

更多JavaScript面向对象编程见<a href="https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects">Object-Oriented JavaScript</a>

在传统的面向对象语言编程中，对象是运算在数据上的数据和方法的集合

JavaScript是基于原型的语言，没有像是在C++或Java中的类语句（这有时会使得习惯于使用类语句的语言的编程者感到困惑）

取而代之，JavaScript使用函数作为类

让我们考虑一个有名有姓的人作为对象

姓名可能会以两种方式展示：如“名 姓”或“姓 名”

使用我们前面讨论的函数和对象，我们就能展示数据如下：

```javascript
function makePerson(first, last) {
	return {
		first: first,
		last: last
	};
}
function personFullName(person) {
	return person.first + ' ' + person.last;
}
function personFullNameReversed(person) {
	return person.last + ', ' + person.first;
}

s = makePerson('Simon', 'Willison');
console.log(personFullName(s));
console.log(personFullNameReversed(s));
VM1137:15 Simon Willison
VM1137:16 Willison, Simon
```

这有效，但很丑

这样最后你会在全局域有很多函数

我们真正需要的是一种把一个函数依附于一个对象的方式

因为函数也是对象，所以这很容易：

```javascript
function makePerson(first, last) {
	return {
		first: first,
		last: last,
		fullName: function() {
			return this.first + ' ' + this.last;
        },
		fullNameReversed: function() {
			return this.last + ', ' + this.first;
		}
 	};
}

s = makePerson('Simon', 'Willison');
console.log(s.fullName());
console.log(s.fullNameReversed());
VM1572:15 Simon Willison
VM1572:16 Willison, Simon
```

这里有一些我们前面没见过的东西：this关键字

使用内部函数，this指向当前对象

这实际上意味着你调用函数的方式来指定

如果你使用一个对象上的点符或括号符来调用，那么那个对象就是this

如果调用没用点符，this指向全局对象

注意this是一个频繁导致错误的东西，例如：

```javascript
s = makePerson('Simon', 'Willison');
var fullName = s.fullName;
fullName();
"undefined undefined"
```

当我们单独调用fullName()，而不用s.fullName()，this被绑定在全局对象

因为没有全局变量叫first或者last，所以我们对于二者都得到了undefined

我们可以利用this关键字来提高我们的makePerson函数

```javascript
function Person(first, last) {
	this.first = first;
	this.last = last;
	this.fullName = function() {
		return this.first + ' ' + this.last;
	};
	this.fullNameReversed = function() {
		return this.last + ' ' + this.first;
	};
}
var s = new Person('Simon', 'Willison');
```

我们介绍了另一个关键字new

new和this强度关联

它创造一个新的空对象，然后调用指定的函数，并使用this设置给那个新建的对象

注意通过this指定的那个函数不返回一个值，但很少修改this对象

是new返回了this对象到调用的地方

被设计为通过new调用的函数称为构造函数

常见的做法是利用这些函数作为一个使用new调用它们的提醒


提高后的函数仍有和单独调用fullName相同的陷阱


我们的person对象变得更好了，但对于它们仍有一些丑陋边缘

每次我们创建一个person对象，我们都创建了其内的两个崭新的函数对象——如果这代码被分享不会变得更好吗？

```javascript
function personFullName() {
	return this.first + ' ' + this.last;
}
function personFullNameReversed() {
	return this.last + ', ' + this.first;
}
function Person(first, last) {
	this.first = first;
	this.last = last;
	this.fullName = personFullName;
	this.fullNameReversed = personFullNameReversed;
}
```

这变得更好了，我们只创建了函数一次，并且在构造器里给它们的引用赋值

我们能做的更好吗？答案是可以：

```javascript
function Person(first, last) {
	this.first = first;
	this.last = last;
}
Person.prototype.fullName = function() {
	return this.first + ' ' + this.last;
};
Person.prototype.fullNameReversed = function() {
	return this.last + ', ' + this.first;
};
ƒ () {
	return this.last + ', ' + this.first;
}
```

Person.prototype是一个分享自Person所有实例的对象

它形成一个查找链的一部分（有一个特殊的名字，原型链）：当任何时候你尝试去访问Person的一个属性时，JavaScript会检查Person.prototype去看是否那个属性存在。

然后任何赋值给Person.prototype的东西对于构造器的所有实例经this对象变得可用

这是一个令人难以置信的强大工具

JavaScript让你可以修改某个东西的原型在任何时候，在你的程序里，意味着你可以在运行时对已存在的对象添加额外的方法

```javascript
s = new Person('Simon', 'Willison');
console.log(s.firstNameCaps());
VM587:2 Uncaught TypeError: s.firstNameCaps is not a function
```

```javascript
Person.prototype.firstNameCaps = function() {
	return this.first.toUpperCase();
};
console.log(s.firstNameCaps());
VM588:4 SIMON
```

有趣的是，你也可以向JavaScript内建对象添加东西

让我们给String添加一个方法以返回字符串的倒转字符串：

```javascript
var s = 'Simon';
s.reversed();
VM620:2 Uncaught TypeError: s.reversed is not a function
```

```javascript
String.prototype.reversed = function() {
	var r = '';
	for (var i = this.length - 1; i >= 0; i--) {
		r += this[i];
	}
	return r;
};

s.reversed();
"nomiS"
```

我们的新方法甚至在字符串文字上有效！

```javascript
'This can now be reversed.'.reversed();
".desrever eb won nac sihT"
```

像之前提到的，原型形成链的一部分

链根是Object.prototype，它的方法包括toString()——是这个方法被调用当你试图呈现一个对象为一个字符串时

这对于调试我们的Person对象很有用

```javascript
var s = new Person('Simon', 'Willison');
s.toString();
[object, object]
```

```javascript
Person.prototype.toString = function() {
	return '<Person: ' + this.fullName() + '>';
};

s.toString();
"<Person: Simon Willison>"
```

记得avg.apply是如何获得一个null作为第一个参数的吗？

我们可以现在回看

apply()的第一个参数是应该被视为this的对象

例如：这里有一个粗糙的new实现

```javascript
function trivialNew(constructor, ...args) {
	var o = {};
	constructor.apply(o, args);
	return o;
}
```

这不是一个准确的new的复制品，因为没有建立原型链（很难说明这一点）

这不是你经常使用的东西，但知道这很有用

在这个片段，...args（包括省略号）被称为rest参数——正如其名暗示，这包含了参数余下的部分

```JavaScript
var bill = trivialNew(Person, 'William', 'Orange');
undefined
bill
{first: "William", last: "Orange"}
```

所以这几乎等同于

```javascript
var bill = new Person('William', 'Orange');
```

apply()有一个姐妹叫做call，再一次让你设置this，但用一个不同于数组的拓展参数列表

```javascript
function lastNameCaps() {
	return this.last.toUpperCase();
}
var s = new Person('Simon', 'Willison');
lastNameCaps.call(s);
"WILLISON"
```

这等同于：

```javascript
s.lastNameCaps = lastNameCaps;
s.lastNameCaps();
"WILLISON"
```

内部函数：

JavaScript函数声明允许在其他函数内部

我们之前见过一次，一个更早版本的makePerson()函数

JavaScript的嵌套函数的一个重要细节是他们可以访问它们父函数域的变量

```javascript
function parentFunc() {
	var a = 1;

	function nestedFunc() {
		var b = 4;
		return a + b;
	}
	return nestedFunc();
}
```

这为写可维护代码提供了一个强大的处理工具

如果一个函数依赖一个或两个对你代码其他部分没有用的其他函数，你可以嵌套那些工具函数在函数内部，以被其他任何地方调用

这保持了全局域范围内的函数数量，总会是件好事

这也是一个强大的全局变量诱饵的计数器（原文：This is also a great counter to the lure of global variables.）

当写复杂代码时，经常尝试使用全局变量去在多个函数间分享——导致难以维护的代码

嵌套函数可以在它们的父内分享，所以你可以将那种机制用于对函数，当不污染你的全局命名空间讲得通时——“本地全局变量”

这个技术应当被小心使用，但它的确是一个有用能力

###### 闭包

这带领我们走向JavaScript提供的最强大的抽象——但也是最潜在令人迷惑的

这是什么呢？

```javascript
function makeAdder(a) {
	return function(b) {
		return a + b;
	};
}
var x = makeAdder(5);
var y = makeAdder(20);
console.log(x(6));
console.log(y(7));
VM1955:8 11
VM1955:9 27
```

makeAdder函数的名字应该放弃：它创造新的adder函数，每一个伴随着一个参数调用的函数，都将其添加到创建它的参数（原文：it creates new 'adder' functions, each of which, when called with one argument, adds it to the argument that it was created with.）

这里发生的事和内部函数非常相同：一个定义在另一个函数内部的函数访问了外部函数的变量

这里唯一不同的是外部函数有返回值，因此常识似乎指明它的局部变量不再存在

但它们仍存在——否则adder函数将无法工作

更重要的是，有makeAdder()本地变量的两份不同的“拷贝”——一个在a中是5，另一个a是20

所以函数调用的结果是11和27

这是真实正在发生的

无论何时JavaScript执行一个函数，“域”对象被创建来承载那个函数内部创建的本地变量

任何被传递进函数作为函数参数的变量将其初始化

这和承载全局变量和函数的全局对象类似，但一组不同的地方是：首先，一个崭新的域对象每次函数开始执行时被创建，其次，不像全局对象（类似通过this访问或浏览器中的window），这些域对象在你的JavaScript代码中不能直接被访问到，比如没有机制被用来迭代当前域对象属性


所以当makeAdder()被调用，一个域对象被创建，伴随一个属性a，也就是被传给makeAdder()的参数

makeAdder()然后返回一个新创建的函数

通常JavaScript的垃圾回收器会在这个点清除掉makeAdder()创建的域对象，但返回的函数维护了一个引用到域对象

结果，域对象不会被垃圾回收器回收直到不再有makeAdder()返回的函数对象的引用


域对象形成一个叫做域链的链，和JavaScript对象系统使用的原型链类似


闭包就是一个函数和其创造的域对象的集合体

闭包使你保持状态——如此，你会发现它们被用在对象的地方

see more closure@ <a href="https://stackoverflow.com/questions/111102/how-do-javascript-closures-work">closures</a>

参考链接：

- <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript">https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript</a>

- <a href="http://crockford.com/javascript/">http://crockford.com/javascript/</a>

- <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Unicode">https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Unicode</a>

---
title: js中的“拷贝”（第一篇）——js数据类型
date: 2019-01-21 15:44:00
tags:
---

# js中的“拷贝”（第一篇）——js数据类型

## 赋值

- 基本类型变量赋值
- 引用类型变量赋值

### 前言

在es6出现以前，js有六大`数据类型`，由五大`基本类型`+一个`引用类型`所组成，五大基本类型分别为`undefined`、`null`、`boolean`、`number`、`string`，一大引用类型为`object`

但随着es6的普及（从新知识变成基本知识），新添一位成员名叫symbol，js的数据类型格局变为六大`基本类型`+一大`引用类型`，symbol的出现被列入`基本类型`队列，而引用类型并无变化

### 基本类型变量（primitive value）

1. undefined
2. null
3. boolean
4. number
5. string
6. symbol ~~object~~

另附以上六种基本类型在`chrome71`控制台中实际输出

#### undefined

```js
typeof undefined
"undefined"
```

`undefined`首字母不大写

#### null

```js
typeof null
"object"
```

空对象的类型当然是对象

#### boolean

```js
typeof Boolean(true)
"boolean"
```

`boolean`首字母不大写

`Boolean`是`window`对象提供的js全局方法，用于将一个值转换为布尔类型，以下是我能想到比较有代表的转换case:

```js
Boolean()
false
Boolean(false)
false
Boolean(0)
false
Boolean(111)
true
Boolean('')
false
Boolean(undefined)
false
Boolean(null)
false
Boolean('false')
true
```

#### number

```js
typeof Number()
"number"
```

同`Boolean()`，`Number()`是将变量转换为number类型的内置方法，以下为示例:

```js
Number()
0
Number(1)
1
Number(false)
0
Number(true)
1
Number('')
0
Number('false')
NaN
Number('true')
NaN
Number('1')
1
Number(undefined)
NaN
Number(null)
0
Number('1str')
NaN
Number('str1')
NaN
```

以上看出，Number()方法在转换字符串时，会尽量把数字字符串顺利转换为number类型，但包含字符串或干脆就是是纯字符串的字符串类型变量显得无能为力，经过Number()方法处理的返回值是得到一个特殊的变量`NaN`，它是js内置的一个用以表示非数字的number类型常量，为什么说它是变量，而不是类型呢，因为：


```js
typeof NaN
"number"
```

#### string

```js
typeof String()
"string"
```

同样，`string`类型首字母不大写，首字母大写的是js内置的用以将某个值转换为该类型的方法，同`Boolean()`、`Number()`，也有`String()`，case如下:

```js
String()
""
String('boolean')
"boolean"
String('number')
"number"
String(Number('false'))
"NaN"
String(1)
"1"
String(undefined)
"undefined"
String(null)
"null"
typeof String(undefined)
"string"
typeof String(null)
"string"
```

以上看出，“万物皆可字符串化”，`NaN`、`undefined`、`null`也难逃于此，它们被String()大法转变后，都乖乖套上了双引号，用`typeof`一照都是字符串类型

#### symbol

```js
typeof Symbol(1)
"symbol"
typeof Symbol()
"symbol"
```

以上就是传统主流js(es5标准实现下的js)的六大基本类型

### 引用类型变量（reference value）

首先所有的`引用类型`都隶属于object类型，细分有`array`、`function`、`object`、`date`、`regexp`等

> 注意：引用类型无法再通过typeof进行判断，因为引用类型都衍生与object，typeof引用于判断基本类型的区分

```js
typeof Array()
"object"
typeof new Date()
"object"
typeof function a(){}
"function"
typeof []
"object"
```

#### object

```js
typeof Object()
"object"
```

同样`object`首字母也不大写，同样js内置了`Object()`方法将一切转换为js对象，但这和`String()`、`Boolean()`、`Number()`不可同日而语，因为`Object`可以说是js的根、源头，一切生自`Object`，没有它就没有js的其他一切

```js
typeof Object()
"object"
Object()
{}
typeof new Date()
"object"
typeof Object()
"object"
Object(1)
Number {1}
typeof Number(1)
"number"
Object(null)
{}
typeof Object(null)
"object"
typeof Object(false)
"object"
Object(false)
Boolean {false}
```

#### 特性

引用类型的值是`可变的`，区分于基本类型`不可变`，所以对于基本类型的数据进行拷贝，就真的是拷贝出了一份类型相同的一个新值。但引用类型的变量只是指向一片内存空间的指针，所以对引用类型的直接拷贝得到的只是一个指向同一片内存空间的另一个指针而已，但拷贝前后所表示的值是相同的，所以此时如果改变原引用变量的值，会同时改变拷贝出的变量的值，举个例子:

```js
var x = {name: 'hello'}
undefined
var y = x;
undefined
x
{name: "hello"}
y
{name: "hello"}
x.name = 'world'
"world"
x
{name: "world"}
y
{name: "world"}
```

#### 补充

`instanceof`见名知意，判断某个对象实例是否是某种对象的实例，（晦涩版：用来判断某个构造函数的 prototype 属性所指向的对象是否存在于另外一个要检测对象的原型链上，这里牵扯到`构造函数`、`ptototype属性`、`原型链`重要概念，需要后面一一详解）

```js
({}) instanceof Object
true
([]) instanceof Array
true
(/a/) instanceof RegExp
true
(function() {}) instanceof Function


true
```

本篇系整合各篇优质内容并加以个人理解整理而成，参考文章如下：

- [JavaScript 深入了解基本类型和引用类型的值](https://segmentfault.com/a/1190000006752076)

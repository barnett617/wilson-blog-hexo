---
title: JavaScript高级程序设计第3版笔记（一）
date: 2018-09-03 18:54:18
update: 2018-09-03 18:54:18
categories: 前端
tags: 
 - javascript
---

## 第七章 函数表达式

<!--more-->

### 定义函数方式

1. 函数声明

```js
function fun () {

}
```

2. 函数表达式

```js
var fun = function () {

}
```

### 关键词

#### 函数声明提升

> 执行代码前会先读取函数声明，所以在函数声明前也可以调用函数

```javascript
test();
function test () {
  alert('test')
}
// ok
```

#### 匿名函数（lambda函数）

> 函数表达式同其他表达式，使用前需先声明，否则报错

```js
test();
function test () {
  alert('test')
}
// 报错
```

#### 递归

> 经典阶乘

```js
function factorial (num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * factorial(num - 1);
  }
}
```

> 如下会使这个函数报错

```js
// 把factorial()函数保存在another变量中
var another = factorial;
// 把变量factorial置空
factorial = null;
// 调用another()必须执行factorial(),而factorial不再是函数
alert(another(4));
```

> 解决：使用`arguments.callee`(指向正在执行的函数的指针)

```js
function factorial (num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * arguments.callee(num - 1);
  }
}
```

> 结论：使用递归函数，通过`arguments.callee`替代函数名更保险

> 特例：严格模式下无法通过脚本访问`arguments.callee`

> 方案：使用命名表达式（适用于严格和非严格模式）

```js
var factorial = (
  function f (num) {
    if (num <= 1) {
      return 1;
    } else {
      return num * f(num - 1);
    }
  }
);
```

> 对比不适用命名表达式的情况

```js
var factorial = function (num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * factorial(num - 1);
  }
}
```

> 创建了名为f()的命名函数表达式，并将其赋值给factorial，这样尽管把函数赋值给其他变量，函数名f仍有效

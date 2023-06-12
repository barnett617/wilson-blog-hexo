---
title: ECMAScript6(ECMAScript2015)学习笔记
date: 2017-08-16 20:20:19
top: true
cover: true
update: 2017-06-23 20:20:20
summary: ECMAScript 6（以下简称ES6）是JavaScript语言的下一代标准。因为当前版本的ES6是在**2015年**发布的，所以又称ECMAScript 2015。
categories: 笔记
tags: 
 - es6
---

### 一、ES6简介

> ECMAScript 6（以下简称ES6）是JavaScript语言的下一代标准。因为当前版本的ES6是在**2015年**发布的，所以又称ECMAScript 2015。

> 即ES6 === ES2015

### 二、ES6转码器

> [Babel](https://babeljs.io/)是一个广泛使用的ES6转码器，可以将ES6代码转为ES5代码，从而在现有环境执行。（chrome已支持ES6解释，亲测可用）大家可以选择自己习惯的工具来使用使用Babel，具体过程可直接在[Babel官网](https://babeljs.io/)查看：
>

### 三、常用特性

 1. let / const（与传统var对比）
 3. class / extends / super（面向对象）
 6. arrow functions（箭头函数）
 7. template string（模板字符串）
 9. destructing（解构）
 10. default（默认值）
 11. rest / arguments（函数参数）

### 四、特性详解
#### （1）let

> 与var类似，都是用来**声明变量**的，但在实际运用中他俩都有各自的特殊用途。

```
var name = 'tom'

while(true) {
	var name = 'bar'
	console.log(name)
	break
}

console.log(name)
```

> bar
> bar
>
> 使用var 两次输出都是bar，内层变量覆盖外层变量。这是因为ES5只有**全局作用域**和**函数作用域**，没有**块级作用域**，这带来很多不合理的场景。
>
> 而let则实际上为JavaScript新增了**块级作用域**。用它所声明的变量，只在let命令所在的代码块内有效。



```
let name = 'tom'

while (true) {
	let name = 'bar'
	console.log(name)
	break
}

console.log(name)
```

> bar
> tom
>
> 另外一个var带来的不合理场景就是用来计数的**循环变量**泄露为**全局变量**，如下

```
var a = [];

for(var i=0; i<10; i++) {
	a[i] = function() {
		console.log(i);
	};
}

a[5]();
```

> 10
>
> 上面代码中，**变量i是var声明的，在全局范围内都有效**。所以每一次循环，**新的i值**都会**覆盖旧值**，导致最后**输出**的是**最后一轮**的i的值。而使用let则不会出现这个问题。
>

```
var a = [];

for(let i=0; i<10; i++) {
	a[i] = function() {
		console.log(i);
	};
}

a[5]();
```

> 5
#### （2）const
> const也用来声明变量，但是声明的是**常量**。一旦声明，常量的值就不能改变。

```
const PI = Math.PI
PI = 23
VM1600:2 Uncaught TypeError: Assignment to constant variable.(…)(anonymous function) @ VM1600:2
```

> 当我们尝试去改变用const声明的常量时，浏览器就会报错。const有一个很好的应用场景，就是当我们引用**第三方库**时声明的变量，用const来声明可以避免未来不小心重命名而导致出现bug：
>
> const monent = require('moment')

#### （3）class/extends/super

> ES6提供了更接近**传统语言**的写法，引入了Class（类）这个概念。新的class写法让**对象原型**的写法更加清晰、更像**面向对象编程**的语法，也更加通俗易懂。

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		console.log(this.type + 'says' +say)
	}
}

let animal = new Animal()
animal.says('hello')
```

> animalsayshello

```
class Cat extends Animal {
	constructor() {
		super()
		this.type = 'cat'
	}
}

let cat = new Cat()
cat.says('hello')
```

> catsayshello
>
> 上面代码首先用**class**定义了一个“类”，可以看到里面有一个constructor方法，这就是构造方法。
>
> 而**this**关键字则代表**实例对象**。简单地说，constructor内定义的方法和属性是实例对象自己的，而constructor外定义的方法和属性则是所有实例对象可以共享的。
>
> Class之间可以通过**extends**关键字实现继承，这比ES5的通过修改原型链实现继承，要清晰和方便很多。上面定义了一个Cat类，该类通过extends关键字，继承了Animal类的所有属性和方法。
>
> **super**关键字，它指代父类的**实例**（即父类的**this对象**）。子类必须在constructor方法中调用**super**方法，否则新建实例时会报错。这是因为**子类没有自己的this对象**，而是继承父类的this对象，然后对其进行加工。如果不调用super方法，子类就得不到this对象。
>
> ES6的继承机制，实质是先创造父类的实例对象this（所以必须先调用super方法），然后再用子类的构造函数修改this。


#### （4）arrow function

> ES6最最常用的一个新特性了，用它来写function比原来的写法要简洁清晰很多

```
var f = (i) => i+1
var result = f(3)
console.log(result)
```


> 4
>
> 其实声明函数只需要（i）=>i + 1这一句
> ES6的箭头函数功能强大，详情见[Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

```
// ES5函数使用
function test(x, y) {
	x++;
	y--;
	return x + y;
}

var f1 = test(6, 7)
console.log(f1)

```

> 13
>
> 这里实际为7+6，return的时候x已经为7，y为6，可自行调试观察

```
// ES6 arrow functions
var arrow = (x, y) => {x++;y--;return x+y}
var result2 = arrow(3, 9)
console.log(result2)
```

> Arrow functions还可以解决一个ES5中关于this的问题，JavaScript语言的this对象一直是一个令人头痛的问题，在**对象方法**中使用this，例如：

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		setTimeout(function() {
			console.log(this.type + ' says ' + say)
		}, 1000)
	}
}

var animal = new Animal()
animal.says('hi')
```

> undefined says hi
>
> 原因：setTimeout中的this指向的是全局对象
>
> 传统解决方法1：将this传给self,再用self来指代this

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		var self = this
		setTimeout(function() {
			console.log(self.type + ' says ' + say)
		}, 1000)
	}
}

var animal = new Animal()
animal.says('hi')
```

> 传统解决方法2：使用bind(this)

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		setTimeout(function() {
			console.log(this.type + ' says ' + say)
		}.bind(this), 1000)
	}
}

var animal = new Animal()
animal.says('hi')
```

> 使用arrow functions解决

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		setTimeout( () => {
			console.log(this.type + ' says ' + say)
		}, 1000)
	}
}

var animal = new Animal()
animal.says('hi')
```



> 当我们使用箭头函数时，函数体内的this对象，就是**定义时**所在的对象，而不是**使用时**所在的对象。并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本**没有自己的this**，它的this是**继承外面的**，因此内部的this就是外层代码块的this。

#### （5）template string

> 当我们要插入**大段**的**html内容**到文档中时，传统的写法非常麻烦，所以之前我们通常会引用一些**模板工具库**，比如**mustache**等等。

```
<body>
	<div id="result">test</div>
</body>
```

```
$(function() {
	var count = 5
	var onSale = 3

	$("#result").append(
		"There are <b>" + count + "</b>" +
		" items in your basket," +
		"<em>" + onSale +
		"</em> are on sale!"
	);
})
```

> testThere are 5 items in your basket,3 are on sale!
>
> 要用一堆的'+'号来连接**文本**与**变量**，而使用ES6的新特性**模板字符串``**后，我们可以直接这么来写

```
$(function() {
	var count = 5
	var onSale = 3

	$("#result").append(`
		There are <b>${count}</b>
		items in your basket,
		<em>${onSale}</em> are on sale!
	`);
})
```

> test There are 5 items in your basket, 3 are on sale!
>
> 非一般的简洁
>
> 用**反引号**（\）（键盘ESC下方）来标识起始，用${}（类似于JavaEE的EL表达式）来引用变量，而且所有的**空格**和**缩进**都会被**保留**在输出之中

#### （6）destructing

> ES6允许按照一定模式，从**数组**和**对象**中**提取值**，对**变量**进行**赋值**，这被称为**解构**（Destructuring）

```
// ES5
let cat = 'tom'
let mouse = 'terry'
let zoo = {cat : cat, mouse : mouse}
console.log(zoo)
```

> Object {cat: "tom", mouse: "terry"}

```
// ES6
let zoo2 = {cat, mouse}
console.log(zoo2)
```

> Object {cat: "tom", mouse: "terry"}

```
let dog = {type : 'animal', num : 6}
let {type, num} = dog
console.log(type, num)

```

> animal 6

#### （7）default

```
// ES5
function animal(type) {
	type = type || 'cat'
	console.log(type)
}

animal()
animal('dog')
```

> cat
> dog
>
> 调用方法时若不加参数则使用默认值

```
// ES6
function animal(type = 'cat') {
	console.log(type)
}

animal()
animal('dog');

```

> cat
> dog

#### （8）rest

```
// ES6 rest
function animals(...types) {
	console.log(types)
}

animals('cat', 'dog', 'fish')

```


> ["cat", "dog", "fish"]

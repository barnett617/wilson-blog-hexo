---
title: javascript-puzzlers
date: 2018-01-20 09:22:05
update: 2018-01-20 09:22:05
categories: 前端
tags: 
 - javascript
---

关于深入理解js的44道wired题目，经实际验证及分析的学习笔记。

<!--more-->

```
["1", "2", "3"].map(parseInt)
```

```
[1, NaN, NaN]
```

map()
map passes 3 params (element, index, array)
parseInt
take two parameters (val, radix)

```
typeof null
```

```
"object"
```

```
null instanceof object
```

```
false
```

typeof always return "object" for **native non callable objects**

```
[ [3, 2, 1].reduce(Math.pow), [].reduce(Math.pow) ]
```

```
reduce on an empty array without an initial value throws TypeError
```

array.reduce()
Math.pow

```javascript
var val = 'smtg';
console.log('Value is ' + (val === 'smtg') ? 'Something' : 'Nothing');
```

```
Something
```

the + operator has higher precedence than the ternary(三元) one

```javascript
var name = 'World!';
(function () {
	if (typeof name === 'undefined') {
		var name = 'Jack';
		console.log('Goodbye ' + name);
	} else {
		console.log('Hello ' + name);
	}
})();
```

```
Goodbye Jack
```

The var declaration is hoisted to the function scope, but the initialization is not.
声明被隔离，初始化没有
declaration
initialization
闭包

```javascript
var END = Math.pow(2, 53);
var START = END - 100;
var count = 0;
for (var i = START; i <= END; i++) {
	count++;
}
console.log(count);
```

```
boom
```

it goes into a infinite loop
2^53 is the highest possible number in JS
2^53+1 gives 2^53(never become larger than it)

```javascript
var ary = [0, 1, 2];
ary[10] = 10;
ary.filter(function(x) { return x === undefined; });
```

```
[]
```

Array.prototype.filter is not invoked for the missing elements
filter()

```javascript
var two = 0.2;
var one = 0.1;
var eight = 0.8;
var six = 0.6;
[two - one == one, eight - six == two]
```

```
[true, false]
```

```
eight - six
0.20000000000000007
two - one
0.1
```

JS does not have precision(精确的) math, even though sometimes it work correctly

```javascript
function showCase(value) {
	switch(value) {
        case 'A':
			console.log('Case A');
			break;
        case 'B':
			console.log('Case B');
			break;
        case undefined:
			console.log('undefined');
        default:
			console.log('Do not know!');
	}
}
showCase(new String('A'));
```

```
Do not know!
```

switch uses === internally
new String(x) !== x

```javascript
function showCase2(value) {
	switch(value) {
        case 'A':
			console.log('Case A');
			break;
        case 'B':
			console.log('Case B');
        case undefined:
			console.log('undefined');
			break;
        default:
			console.log('Do not know!');
    }
}
showCase2(String('A'))
```

```
Case A
```

```
String("B")
"B"
String(1)
"1"
```

String does not create an object but does return a string
typeof String(1) === "string"

```javascript
function isOdd(num) {
	return num % 2 == 1;
}
function isEven(num) {
	return num % 2 == 0;
}
function isSane(num) {
	return isEven(num) || isOdd(num);
}
var values = [7, 4, '13', -9, Infinity];
values.map(isSane);
```

```
[true, true, true, false, false]
```

Infinity % 2 gives NaN
-9 % 2 gives -1
modulo operator(模运算符) keeps sign so it's result is only reliable compared to 0

```
parseInt(3, 8)
parseInt(3, 2)
parseInt(3, 0)
```

```
3
```

```
parseInt(3, 8)
3
parseInt(3, 2)
NaN
parseInt(3, 0)
3
```

3 does not exist in base 2, so NaN
parseInt(3, 0) parseInt will consider a bogus radix(考虑伪造一个基数) and assume you meant 10

```
Array.isArray( Array.prototype )
```

```
true
```

Array.prototype is an Array

```javascript
var a = [0];
if ([0]) {
	console.log(a == true);
} else {
	console.log("wut");
}
```

```
false
```

[0] as a boolean is considered true
Alas
using it in the comparisions it gets converted in a different way and all goes to hell

```
[] == []
```

```
false
```

== is the spawn of satan(==是恶魔之卵？？？)

```
'5' + 3
'5' - 3
```

```
"53", 2
```

Strings know about + and will use it
but they are ignoreant of -
int that case the strings get converted to numbers

```
1 + - + + + - + 1
2
1 + - 1
0
1 + - + 1
0
1 + - + + 1
0
1 + - + + + - 1
2
1 + - + + + - + 1
2
```

???

```javascript
var ary = Array(3);
ary[0] = 2;
ary.map(function(elem) { return '1'; });
```

```
["1", empty × 2]
```

map is only invoked for elements of the Array which have been initialiazed

```javascript
function sideEffecting(ary) {
	ary[0] = ary[2];
}
function bar(a, b, c) {
	c = 10;
	sideEffecting(arguments);
	return a + b + c;
}
bar(1, 1, 1);
```

```
21
```

variables are tied to the arguments object
even not in the same scope
changing the variables changes arguments
changing arguments changes the local variables even

```javascript
var a = 111111111111111110000,
	b = 1111;
a + b
```

```
111111111111111110000
```

lack of precision for numbers in js affects both **small** and **big** numbers

```javascript
var x = [].reverse;
x();
```

```
???
```

[].reverse will return this
when invoked without an explicit(明确的) receiver object it will default to the default this AKA window

ES5???

```javascript
Number.MIN_VALUE
5e-324
Number.MIN_VALUE > 0
true
-Number.MAX_VALUE
-1.7976931348623157e+308
-Number.MAX_VALUE > 0
false
```

```
1 < 2 < 3
true
3 < 2 < 1
true
```

true gets intified and is 1
false gets intified and become 0

```
// the most classic wtf
2 == [[[2]]]
```

```
true
```

both objects get converted to strings and in both cases the resulting string is "2"

```javascript
3.toString()
3..toString()
3...toString()
```

```
error
"3"
error
```

3.x is a valid syntax to define "3" with a mantissa(尾数) of x
toString is not a valid number, but the empty string is
???

```javascript
(function() {
	var x = y = 1;
})();
console.log(y);
console.log(x);
```

```
1
error
```

y is an automatic global, not a function local one

```javascript
var a = /123/,
	b = /123/;
a == b;
a === b;
```

```
false
false
```

Per spec Two regular expression literals in a program evaluate to regular expression objects that never compare as === to each other even if the two literals' contents are identical.
同一个程序内的正则表达式永不使用===相互比较，尽管二者内容一样

```javascript
var a = [1, 2, 3],
    b = [1, 2, 3],
    c = [1, 2, 4]
a ==  b
a === b
a >   c
a <   c
```

```
false
false
false
true
```

Arrays are compared lexicographically with > and <, but not with == and ===

```javascript
var a = {}, b = Object.prototype;
[a.prototype === b, Object.getPrototypeOf(a) === b]
```

```
false
true
```

Functions have a prototype property but other objects don't
a.prototype is undefined
Every Object instead has an internal property accessible via Object.getPrototypeOf

```javascript
function f() {}
var a = f.prototype, b = Object.getPrototypeOf(f);
a === b
```

```
false
```

f.prototype is the object that will become the parent of any objects created with new f
Object.getPrototypeOf returns the parent in the inheritance hierarchy

```javascript
function foo() { }
var oldName = foo.name;
foo.name = "bar";
[oldName, foo.name]
```

```
["foo", "foo"]
```

name is a read only property

```
1 2 3".replace(/\d/g, parseInt)
```

```
"1 NaN 3"
```

String.prototype.replace invokes the callback function with multiple arguments where the first is the match
then there is one argument for each capturing group
then there is the offset of the matched substring
finally the original string itself
parseInt will be invoked with arguments [1, 0], [2, 2], [3, 4]
???

```javascript
function f() {}
var parent = Object.getPrototypeOf(f);
f.name
parent.name
typeof eval(f.name)
typeof eval(parent.name)
```

```
"f"
"Empty"
"function"
error
```

The function prototype object is defined somewhere, has a name, can be invoked
but it is not in the current scope

```javascript
var lowerCaseOnly = /^[a-z]+$/;
[lowerCaseOnly.test(null), lowerCaseOnly.test()]
```

```
[true, true]
```

the argument is converted to a string with the abstract ToString operation
it is "null" and "undefined"

```javascript
[,,,].join(", ")
```

```
", , "
```

JS allows a trailing comma(尾随分隔符) when defining arrays
It is an array of three undefined
join()

```
var a = {class: "Animal", name: 'Fido'};
a.class
```

It depends on brower
class is a reserved word
accepted as a property name by Chrome,Firefox and Opera
fail in IE
accept most reserved words (int, private, throws etc) as variable names
class is verboten(禁止的)

```javascript
var a = new Date("epoch")
```

a instanceof Date is true, but invalid
time is internally kept as a Number
in this case it is a NaN

```javascript
var a = Function.length,
	b = new Function().length
a === b
```

```
false
```

Function.length is defined to be 1
the length property of the Function prototype object is defined to be 0

```javascript
var a = Date(0);
var b = new Date(0);
var c = new Date();
[a === b, b === c, a === c]
```

```
[false, false, false]
```

```
a
"Thu Jan 18 2018 18:25:18 GMT+0800 (中国标准时间)"
b
Thu Jan 01 1970 08:00:00 GMT+0800 (中国标准时间)
c
Thu Jan 18 2018 18:25:18 GMT+0800 (中国标准时间)
```

When it is invoked as a function, it returns a String representation of the current time
When Date is invoked as a constructor it returns an object relative to the epoch(Jan 01 1970)
When the argument is missing it returns the current date

```javascript
var min = Math.min(), max = Math.max()
min < max
```

```
false
```

```
min
Infinity
max
-Infinity
```

Math.min returns +Infinity when supplied an empty argument list

```javascript
function captureOne(re, str) {
  var match = re.exec(str);
  return match && match[1];
}
var numRe  = /num=(\d+)/ig,
    wordRe = /word=(\w+)/i,
    a1 = captureOne(numRe,  "num=1"),
    a2 = captureOne(wordRe, "word=1"),
    a3 = captureOne(numRe,  "NUM=2"),
    a4 = captureOne(wordRe,  "WORD=2");
[a1 === a2, a3 === a4]
```

Regular expressions in JavaScript if defined using the /g flag will carry a state across matches, even if they are actually used on different strings (the lastIndex property). This means a3 will be null as the regular expression was applied starting from the index of the last matched string, even if it was a different one.

```javascript
var a = new Date("2014-03-19"),
    b = new Date(2014, 03, 19);
[a.getDay() === b.getDay(), a.getMonth() === b.getMonth()]
```

```
[false, false]
```

JavaScript inherits 40 years old design from C: days are 1-indexed in C's struct tm, but months are 0 indexed. In addition to that, getDay returns the 0-indexed day of the week, to get the 1-indexed day of the month you have to use getDate, which doesn't return a Date object.

```javascript
if ('http://giftwrapped.com/picture.jpg'.match('.gif')) {
  'a gif file'
} else {
  'not a gif file'
}
```

```
"a gif file"
```

String.prototype.match silently converts the string into a regular expression, without escaping it, thus the '.' becomes a metacharacter matching '/'.

```javascript
function foo(a) {
    var a;
    return a;
}
function bar(a) {
    var a = 'bye';
    return a;
}
[foo('hello'), bar('hello')]
```

```
["hello", "bye"]
```

Variabled declarations are hoisted, but in this case since the variable exists already in the scope, they are removed altogether. In bar() the variable declaration is removed but the assignment remains, so it has effect.

参考：<a href="http://javascript-puzzlers.herokuapp.com/">http://javascript-puzzlers.herokuapp.com/</a>

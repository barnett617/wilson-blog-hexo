---
title: 浅拷贝和深拷贝
date: 2019-01-22 16:47:00
tags:
---

# 浅拷贝和深拷贝

## 浅拷贝

### 典型案例

- Object.assign()
- 展开赋值
- Array.prototype.slice()
-  Array.prototype.concat()

### 定义

浅拷贝对于js基本类型进行正确拷贝，对引用类型只拷贝指针，但指针指向的堆内存相同，所以对于原对象的基本类型属性进行改变不会影响到新拷贝出的对象对应的属性，但对原对象的引用类型属性改变，新拷贝的对象对应的属性也会随之变化

### Object.assign实例

```js
var obj = {name: 'hello', child: {name: 'tom', age: 10}};
var copy = Object.assign({}, obj)

obj
child:
  age: 20
  name: "jerry"
name: "world"

copy
child:
  age: 20
  name: "jerry"
name: "hello"

obj.name = 'world';
obj.child.name = 'jerry';
obj.child.age = 20;

obj
child:
  age: 20
  name: "jerry"
name: "world"
copy
child:
  age: 20
  name: "jerry"
name: "hello"
```

### rest实例

```js
var rest = {name: 'iamrest', child: {childname: 'iamchild', age: 30}};
undefined
var restcopy = {...rest}
undefined
rest
{name: "iamrest", child: {…}}
child: {childname: "iamchild", age: 30}
name: "iamrest"
restcopy
{name: "iamrest", child: {…}}
child: {childname: "iamchild", age: 30}
name: "iamrest"

rest.name = 'imnotrest';
rest.child.age = 40
40
rest
{name: "imnotrest", child: {…}}
child: {childname: "iamchild", age: 40}
name: "imnotrest"
__proto__: Object
restcopy
{name: "iamrest", child: {…}}
child: {childname: "iamchild", age: 40}
name: "iamrest"
__proto__: Object
```

### Array.prototype.slice实例

> MDN官方定义slice方法用于数组对象浅拷贝`slice() 方法返回一个新的数组对象，这一对象是一个由 begin和 end（不包括end）决定的原数组的浅拷贝。原始数组不会被改变。`，详见[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)

#### slice方法用法（核心：对数组进行浅拷贝）

```
arr.slice();
// [0, end]

arr.slice(begin);
// [begin, end]

arr.slice(begin, end);
// [begin, end)
```

##### 实例

```js
var arr = [3, 4, [5, 6]];
undefined

var slice0 = arr.slice();
undefined

slice0;
(3) [3, 4, Array(2)]
0: 3
1: 4
2: (2) [5, 6]
length: 3
__proto__: Array(0)
```

结论：如果不传任何参数，则对原数组进行完整浅拷贝

```js
var slice1 = arr.slice(1);
undefined

slice1;
(2) [4, Array(2)]
0: 4
1: (2) [5, 6]
length: 2
__proto__: Array(0)
```

结论：如果传一个参数，则从数组的该索引位置（包含索引位置的元素）浅拷贝到数组最后一个元素

```js
var slice2 = arr.slice(0, 2);
undefined

slice2
(2) [3, 4]
0: 3
1: 4
length: 2
__proto__: Array(0)
```

如果传两个参数，则对原数组进行从第一个参数的索引位置（包含该元素）到第二个参数位置止（不包含该元素）进行浅拷贝

#### 插播数组splice方法的使用（核心：对数组进行切片，同时增删元素，直接对原数组进行操作）

`array.splice(start[, deleteCount[, item1[, item2[, ...]]]])`

初看这种方法api抽象的表达方式可能会很晕，这里start表示为必填的参数，而后面的中括号内的部分表示可选参数，也就是如果在start后面的第二个参数会被认为是deleteCount，后面不要看嵌套多少层，其实都是从第三个参数开始也都是可填可不填的，如果填了就依次累加，表示要新添加进数组的元素

##### 方法注意点

1. 方法返回值是`被删除`元素所组成的数组
2. 方法对于元素的删除和新增都基于原数组，即对数组进行直接操作（也就相当于对数组进行区别于栈结构和队列结构的顺序操作，可以像对链表操作一样一样操作数组，修改内部元素）

##### 参数注意

- start 操作起点
- deleteCount 删除个数
- item 新增元素

###### start可以是任意整数，分正整数、负整数和0

- 如果是0，则表示从数组第一个元素开始（并且包括start索引所在元素）
- 如果是数组长度范围内的负整数，则表示从数组尾部往前数
- 如果是数组长度范围内的正整数，则表示从数组头部往后数

整体看来splice方法属于对数组的高级切片操作，可以对数组同时进行删除、新增元素操作

###### deleteCount表示要删除的元素个数，如果为0或者负数时不会删除数组元素

##### 实例

```js
[3, 4, 7, 8].splice('s')
(4) [3, 4, 7, 8]
[3, 4, 7, 8].splice(false)
(4) [3, 4, 7, 8]
[3, 4, 7, 8].splice(true)
(3) [4, 7, 8]
[3, 4, 7, 8].splice(1000)
[]
[3, 4, 7, 8].splice(-1000)
(4) [3, 4, 7, 8]
[3, 4, 7, 8].splice(0)
(4) [3, 4, 7, 8]
[3, 4, 7, 8].splice(1)
(3) [4, 7, 8]
[3, 4, 7, 8].splice(3.1)
[8]
[3, 4, 7, 8].splice(3.6)
[8]
```

以上为只传一个参数的情况，此时 deleteCount = arr.length - start

- 当start传奇怪的参数，比如字符串，splice会试着把字符串转换为数字，如果转换不成功即变成NaN，那么就会当做0处理，会清空数组；
- 对于布尔类型数值同样会转换为数字，true是1，false是0；
- 对于浮点数，splice会对浮点数做取整处理，即只保留整数部分；
- 所以当传一个大于数组长度的正整数，不会删除数组元素，当传一个绝对值大于数组长度的负整数时，deleteCount = arr.length - start会得到一个大于数组长度的数，所以会清空数组

```js
[3, 4, 7, 8].splice('s', 3)
(3) [3, 4, 7]
[3, 4, 7, 8].splice(false, 3)
(3) [3, 4, 7]
[3, 4, 7, 8].splice(true, 3)
(3) [4, 7, 8]
[3, 4, 7, 8].splice(-1000, 3)
(3) [3, 4, 7]
[3, 4, 7, 8].splice(1000, 3)
[]
[3, 4, 7, 8].splice(0, 3)
(3) [3, 4, 7]
[3, 4, 7, 8].splice('1', 3)
(3) [4, 7, 8]

Number('s')
NaN

[3, 4, 7, 8].splice(NaN, 3)
(3) [3, 4, 7]

[3, 4, 7, 8].splice(3.6, 3)
[8]
[3, 4, 7, 8].splice(3.2, 3)
[8]

Number(3.6)
3.6
parseInt(3.6)
3
parseInt(3.2)
3
```

以上为传两个参数的情况，这个时候显性地指定了要删除的元素个数，对于start不合理的传参情况会按照上面的处理方式，唯一特别的就是当start为绝对值大于数组长度的整数时，由于删除起点已超出数组范围，所以无论deleteCount为多少，都不会怼数组进行删除操作，而当start为绝对值大于数组长度的负整数时，start会按0处理

展开剖析反而会显得更复杂，上述仅为理解过程，附上官方原定义：

> start​指定修改的开始位置（从0计数）。如果超出了数组的长度，则从数组末尾开始添加内容；如果是负值，则表示从数组末位开始的第几位（从-1计数）；如果负数的绝对值大于数组的长度，则表示开始位置为第0位。

> deleteCount 可选整数，表示要移除的数组元素的个数。如果 deleteCount 是 0或者负数，则不移除元素。这种情况下，至少应添加一个新元素。
如果 deleteCount 大于start 之后的元素的总数，则从 start 后面的元素都将被删除（含第 start 位）。
如果deleteCount被省略，则其相当于(arr.length - start)。

> item1, item2, ... 可选
要添加进数组的元素,从start 位置开始。如果不指定，则 splice() 将只删除数组元素。

## 深拷贝

### 定义

深拷贝即对被拷贝对象进行绝对拷贝，拷贝前后的两个对象绝对独立，互不影响

### 实现方式

```js
var tom = {name: 'iamtom', child: {age: 20, name: 'imtomchild'}};
undefined

var tomcopy = JSON.parse(JSON.stringify(tom));
undefined

tomcopy
{name: "iamtom", child: {…}}
child: {age: 20, name: "imtomchild"}
name: "iamtom"
__proto__: Object

tom
{name: "iamtom", child: {…}}
child: {age: 20, name: "imtomchild"}
name: "iamtom"
__proto__: Object

tom.child.age = 30;
30

tom
{name: "iamtom", child: {…}}
child: {age: 30, name: "imtomchild"}
name: "iamtom"
__proto__: Object

tomcopy
{name: "iamtom", child: {…}}
child: {age: 20, name: "imtomchild"}
name: "iamtom"
__proto__: Object
```

结论：使用JSON对象的stringify方法对被拷贝对象进行字符串化，再对生成的字符串对象化，会生成一个和原对象完全相同，但内存空间不同的对象

```js
var arr = [2, 3, 5, 11, 49];
undefined
var arrcopy = JSON.parse(JSON.stringify(arr));
undefined

arr
(5) [2, 3, 5, 11, 49]
arrcopy
(5) [2, 3, 5, 11, 49]

arr[2] = 6;
6

arr
(5) [2, 3, 6, 11, 49]
arrcopy
(5) [2, 3, 5, 11, 49]
```

结论：数组同样适用（因为数组本质也是对象）

### 存在问题

对于对象的特殊属性无法拷贝，包括：

- undefined
- symbol值
- 函数
- 日期类型
- 正则类型
- ……

```js
var special = {
	name: 'normal',
	a: undefined,
	b: function test() {
		console.log('hello')
	},
	c: Symbol('world'),
}
undefined

var specialcopy = JSON.parse(JSON.stringify(special))
undefined

special
{name: "normal", a: undefined, b: ƒ, c: Symbol(world)}a: undefinedb: ƒ test()c: Symbol(world)name: "normal"__proto__: Object
a: undefined
b: ƒ test()
c: Symbol(world)
name: "normal"
__proto__: Object

specialcopy
{name: "normal"}
```

---
title: Vue.js技术回顾之实例
date: 2018-02-22 21:33:38
update: 2018-02-22 21:33:38
categories: 前端
tags: 
 - vue
---

Vue.js教程之Vue实例

<!--more-->

### 创建实例

每个Vue应用都是通过用`Vue`函数创建一个新的**Vue实例**开始

```javascript
var vm = new Vue({
	// options
})
```

虽然没有遵守<a href="https://zh.wikipedia.org/wiki/MVVM">MVVM</a>模型，但Vue的设计也受MVVM启发，故示例中常使用vm(viewmodel缩写)表示Vue实例

一个Vue应用的组成：

- 一个通过new Vue创建的根Vue实例

- 可选的嵌套的、可复用的组件树

所有的Vue组件都是Vue实例，并且接受相同的选项对象（一些根实例特有的选项除外）

#### MVVM

##### 简介

MVVM（model-view-viewmodel）是一种**软件架构**，有助于将**图形用户界面开发**与**业务逻辑**或**后端逻辑**开发分离开来，通过置标语言或GUI代码实现

MVVM的viewmodel是一个值转换器，意味着负责从模型中暴露（转换）数据对象，以便轻松管理和呈现对象。在这方面viewmodel比view做的更多，且处理**大部分视图**的**显示逻辑**。

viewmodel可实现中介者模式，组织对view所支持的用例集的后端逻辑的访问


#### 标记语言

##### 简介

置标语言也称标记语言、标志语言、标识语言、markup language，是一种将**文本**以及**文本相关**的**其他信息**结合起来，展现出关于**文档结构**和**数据处理细节**的**计算机文字编码**。

与文本相关的其他信息（文本结构和表示信息等）与原文本结合在一起，但使用标记进行标识

##### 现状及历史

如今广泛使用的标记语言是HTML和XML，广泛用于网页和网络应用程序，最早用于出版业，是作者、编辑及出版商之间用于描述出版作品的排版格式所使用的

##### 分类

标记语言通常分为三种：表示性、过程性、描述性

1. 表示性：编码过程中标记文档的**结构**信息

例如文本文件中，文件标题可能需要特定格式（居中、放大等），则需要标记文件的标题。

字处理及桌面出版产品有时能自动推断出这类**结构信息**，但绝大多数，像Wiki这样的纯文本编辑器还不能解决这个问题

2. 过程性：一般专门于文字表达，但通常对于文本编辑者可见，且能够被软件依其出现顺序依次解读

为格式化一个标题，在标题文本之前，会紧接着插入一系列格式标识符，以指示计算机转到居中的**显示模式**，同时加大及加粗字体。在标题文本后，会紧接缀上**格式中止标识**

对于更高级的**系统宏命令**或者**堆栈模式**会让这一过程的实现方式更加丰富

大多数情况下，过程性标志能力包含有一个图灵完备编程语言

> 图灵完备：当一组数据操作的规则（一组指令集、编程语言、元胞自动机）满足任意数据按照一定的顺序可以计算出果，称为图灵完备

过程性标志语言范例：nroff、troff、TeX、Lout、PostScript

过程性标志语言广泛用于**专业出版领域**，专业的出版商会根据要求使用不同的标志语言达到出版要求

3. 描述性

又称通用标识，描述文件的**内容或结构**，而非文件的显示**外观或样式**

指定SGML的基本思想就是把文档的内容与样式分开，XML、SGML都是典型的通用标识语言

###### 应用

- GenCode

- TeX：功能强大的排版软件

- SGML：被用作编写《牛津英语词典》电子版本，由于复杂而难以普及

- HTML

- XML

- XHTML：（eXtensible HyperText Markup Language），表现方式与HTML类似，语法上更严格

- 基于XML的应用：RDF、XForms、DocBook、SOAP、Web Ontology Language(WOL)

### 数据与方法

当一个Vue实例被创建时，即向Vue的**响应式系统**中假如其data对象中能找到的所有的属性

当这些属性改变时，试图将随之响应，即匹配更新的新值

```javascript
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 它们引用相同的对象！
vm.a === data.a // => true

// 设置属性也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```

> 注意：只有当**实例被创建时**data中存在的属性才是响应式的，如果再添加一个属性，那么对其改变不会触发试图更新，若想解决此问题，可在起初设置一些属性，初始化为空

```javascript
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

> 特殊: 使用Object.freeze()会阻止修改现有属性，即响应系统无法再追踪变化

```javascript
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data () {
    return {
      obj
    }
  }
})
```

```HTML
<div id="app">
  <p>{{ obj.foo }}</p>
  <!-- 这里的 `obj.foo` 不会更新！ -->
  <button @click="obj.foo = 'baz'">Change it</button>
</div>
```

除**数据属性**外，Vue实例还暴露了一些有用的**实例属性**和**方法**，都有前缀`$`，以便用户定义的属性区分开

```javascript
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 这个回调将在 `vm.a` 改变后调用
})
```

### 实例生命周期钩子

每个Vue实例创建时都将经历一系列**初始化过程**，例如设置数据监听、编译模板、将实例挂载到DOM并在数据变化时更新DOM等

这个过程中也会运行一些叫做的**生命周期钩子**的函数，给用户在**不同阶段**添加自己代码的机会

例如：created钩子可用来在一个实例创建后执行代码

```javascript
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

还有一些钩子，在实例生命周期**不同阶段**被调用，如mounted、updated、destroyed

生命周期钩子的this上下文指向调用它的Vue实例

> 注意！不要在**选项属性**或**回调**上使用箭头函数，比如created: () => console.log(this.a)或vm.$watch('a', newValue => this.myMethod())，因为箭头函数和**父级**上下文绑定在一起，this不会是所预期的Vue实例，会导致Uncaught TypeError: Cannot read property of undefined或Uncaught TypeError: this.myMethod is not a function之类错误

### 生命周期图

![vue lifecycle](/img/post/vue_lifecycle.png)

参考链接：

- <a href="https://cn.vuejs.org/v2/guide/instance.html">https://cn.vuejs.org/v2/guide/instance.html</a>

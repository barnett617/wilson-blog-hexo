---
title: Vue.js技术回顾——概述
date: 2018-02-22 12:34:42
update: 2018-02-22 12:34:42
categories: 前端
tags: 
 - vue
---

初探Vue.js

<!--more-->

## 简介

Vue是一套用于构建用户界面的**渐进式**框架，与其他大型框架不同在于，Vue被设计为可以**自底向上**逐层应用。

Vue的核心只关注**视图层**，即View。不仅易于上手，还便于与**第三方库**或**既有项目**整合

与**现代化工具链**及各种**<a href="https://github.com/vuejs/awesome-vue#libraries--plugins">支持类库</a>**结合使用时，能够为复杂的**单页应用**提供驱动

### 快速上手

<a href="https://jsfiddle.net/chrisvfritz/50wL7mdz/">JSFiddle在线演示</a>

或按照上例在本地引入Vue

```javascript
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

### 声明式渲染

vue.js核心是一个允许采用简洁的**模板语法**来**声明式地**将数据渲染进DOM的系统

> 解读：vue是这样一个系统：允许你使用**简洁**的**模板语法**来**声明式**地将**数据**渲染进**DOM**

```HTML
<div id="app">
	{{ message }}
</div>
```
这是一个DOM结构，div有唯一标识(id)——app

```JavaScript
var app = new Vue({
	el: '#app',
	data: {
		message: 'Hello Vue!'
	}
})
```

声明一个VUE对象，来给id为app的这个元素声明式地将'Hello Vue!'（字符串数据）渲染进其DOM

这样就已经创建了一个Vue应用，并且**数据**和**DOM**建立起**关联**，所有东西都是**响应式**的

测试：在浏览器控制台中修改app.message的值，会看到相应更新

本地实例操作如下：

![](http://trigolds.com/vue1-1.jpg)

![](http://trigolds.com/vue0.jpg)

![](http://trigolds.com/vue1.jpg)

除了上面的**文本插值**，还可以如下这样绑定元素特性：

```HTML
<div id="app-2">
	<span v-bind:title="message">
		鼠标悬停几秒钟查看此处动态绑定的提示信息！
	</span>
</div>
```

```javascript
var app2 = new Vue({
	el: '#app-2',
	data: {
		message: '页面加载于 ' + new Date().toLocaleString()
	}
})
```

`v-bind`称为**指令**，vue的指令都以`v-`为前缀，它们会在渲染的DOM上应用特殊的**响应式行为**

此处指令v-bind:title="message"意指“将这个元素节点的title属性和Vue实例（new Vue()）的message属性保持一致”

同样，在Vue中数据和DOM存在响应式关联，即修改app2.message = '其他消息'会看到绑定了title特性的HTML发生更新

That is really interesting!

### 条件与循环

控制一个元素是否显示：

```HTML
<div id="app-3">
	<p v-if="seen">现在你看到我了</p>
</div>
```

```javascript
var app3 = new Vue({
	el: '#app-3',
	data: {
		seen: true
	}
})
```

![](http://trigolds.com/vue3.jpg)

在控制台修改app3.seen为false

![](http://trigolds.com/vue3-2.jpg)

本例证明不仅可以把数据绑定到DOM文本（例1）或特性（例2），还可以绑定到DOM结构（例3）

此外，Vue还提供强大的**过渡效果**系统，可以在Vue**插入/更新/移除**元素时自动应用**过渡效果**

每个Vue指令都有特殊的功能，例如v-for指令可以绑定**数组的数据**来渲染一个**项目列表**

```HTML
<div id="app-4">
	<ol>
		<li v-for="todo in todos">
			{{ todo.text }}
		</li>
	</ol>
</div>
```

```javascript
var app4 = new Vue({
	el: '#app-4',
	data: {
		todos: [
			{ text: '元素1' },
			{ text: '元素2' },
			{ text: '元素3' }
		]
	}
})
```

在控制台内向app4这个Vue实例中的数组再添加一个对象

### 处理用户输入

v-on指令可添加一个**事件监听器**，通过其**调用**Vue实例中定义的**方法**，可与用户进行交互

```HTML
<div id="app-5">
	<p>{{ message }}</p>
	<button v-on:click="reverseMessage">反转消息</button>
</div>
```

```javascript
var app5 = new Vue({
	el: '#app-5',
	data: {
		message: 'Hello Vue!'
	},
	methods: {
		reverseMessage: function () {
			this.message = this.message.split('').reverse().join('')
		}
	}
})
```

注意点：

1. 选择器是#app-5而非app-5

2. 中英文符号{}

3. methods而非method

![](http://trigolds.com/vue5.jpg)

本例中更新应用状态却没有触碰DOM，DOM操作都有Vue处理，只需关注逻辑层面

Vue还有v-model指令用于实现**表单输入**和**应用状态**之间的**双向绑定**

```HTML
<div id="app-6">
	<p>{{ message }}</p>
	<input v-model="message">
</div>
```

```javascript
var app6 = new Vue({
	el: '#app-6',
	data: {
		message: 'Hello Vue!'
	}
})
```

该双向绑定对比Angular最简示例更加轻便

### 组件化应用构建

**组件系统**是Vue另一重要概念（区别于前面过渡系统）

因为组件系统是一种**抽象**，允许使用**小型（tiny）**、**独立（independent）**和可复用（reusable）的组件构建大型应用（类似React）

一个组件的本质是一个拥有**预定义选项**的一个Vue实例

在Vue中注册组件：

```
Vue.component('todo-item', {
	template: '<li>待办项</li>'
})
```

现在可以有用它构建另一个组件模板

```
<ol>
	<todo-item></todo-item>
</ol>
```

但这样为每个待办项渲染同样的文本很差劲

应能从**父作用域**将**数据**传到**子组件**

```
Vue.component('todo-item', {
		props: ['todo'].
		template: '<li>{{ todo.text }}</li>'
})
```

使用v-bind指令将待办项传到循环输出的每个组件中

```html
<div id="app-7">
	<ol>
		<todo-item
			v-for="item in groceryList"
			v-bind:todo="item"
			v-bind:key="item.id">
		<todo-item>
	</ol>
</div>
```

```javascript
Vue.component('todo-item', {
	props: ['todo'],
	template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
	el: '#app-7',
	data: {
		groceryList: [
			{ id: 0, text: '待办1' },
			{ id: 1, text: '待办2' },
			{ id: 2, text: '待办3' }
		]
	}
})
```

这就像是在前端使用面向对象思想声明了了类，并实例化一个实例来处理数组

这个例子将应用分割成两个更小的单元。

子单元通过prop接口与父单元进行了良好的解耦

可进一步改进<todo-item>组件，提供更为复杂的模板和逻辑，而不影响父单元

在一个**大型应用**中有必要将整个应用**划分**为**组件**，以使开发更易管理

如下例展示了使用了**组件**的应用模板是什么样：

```HTML
<div id="app">
	<app-nav></app-nav>
	<app-view>
		<app-siderbar></app-sidebar>
		<app-content></app-content>
	</app-view>
</div>
```

### 与自定义元素的关系

Vue的组件非常类似于**自定义元素**——它是Web组件规范的一部分，因为Vue的组件语法部分参考了该规范，如Vue组件实现了<a href="https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md">Slot API</a>与is特性

但是，有几个关键差别：

1. Web组件规范仍处于草案阶段，并未被所有浏览器原生实现。而Vue组件不需要任何polyfill，且在所有支持的浏览器（IE9及以上）中表现一致。必要时Vue组件也可包装于**原生自定义元素**之内

2. Vue组件提供了纯自定义元素所不具备的一些重要功能，最突出的是**跨组件数据流**、**自定义事件通信**和**构建工具集成**


参考链接：

- <a href="https://cn.vuejs.org/v2/guide/">https://cn.vuejs.org/v2/guide/</a>

---
title: HTML5语义提升篇
date: 2018-02-16 15:54:37
update: 2018-02-16 15:54:37
categories: 前端
tags: 
 - HTML5
---

关于HTML5中语义方面的改变

<!--more-->

## 使用HTML的section和outline

> 重要点：现在不再有在图形浏览器或辅助技术用户代理的已知的提纲算法，虽然这个算法在其他软件，比如一致性检验器中被实现。所以<a href="https://www.w3.org/TR/html5/sections.html#outline">提纲</a>算法不能再被依赖于传递文档结构给用户。作者被建议去使用标题头<a href="https://www.w3.org/TR/html5/sections.html#rank">秩</a>(h1-h6)去传递文档结构

HTML5特征带来了许多新元素给web开发者，允许他们去描述一个web文档的结构以一种标准的语义。这个文档描述了这些元素和如何使用它们去定义所想要的任何文档内容的轮廓。

### HTML4中的文档结构

一个文档的结构，比如，\<body>和\</body>之间的语义结构，是呈现网页给用户的最基本。HTML4使用一个文档的部分概念（notion of section）和子部分来描述它的结构。一个部分被一个\<div>元素定义，伴随着其内的标题元素（\<h1>,\<h2>,\<h3>,\<h4>,\<h5>或者\<h6>）定义了它的标题。这些元素的关系引领了文档的结构和它的轮廓

所以如下的标记：

```html
<div class="section" id="forest-elephants">
	<h1>Forest elephants<h1>
	<p>In this section, we discuss the lesser known forest elephants.
		...this section continues...
	<div class="subsection" id="forest-habitat">
		<h2>Habitat</h2>
		<p>Forest elephants do not live in trees but among them.
		...this subsection continues...
	</div>d
</div>
```

引导如下的轮廓：（不含隐式等级号展示）

```
1. Forest elephants
   1.1 Habitat
```

并非一定需要\<div>元素去定义一个新的部分块。仅需存在一个标题元素就足以意味着一个新的部分块。所以：

```html5
<h1>Forest elephants<h1>
	<p>In this section, we discuss the lesser known forest elephants.
		...this section continues...
	<h2>Habitat</h2>
	<p>Forest elephants do not live in trees but among them.
	...this subsection continues...
	<h2>Diet</h2>
<h1>Mongolian gerbils</h1>
```

引领出如下的轮廓：

```
1. Forest elephants
   1.1 Habitat
   1.2 Diet
2. Mongolian gerbils
```

### HTML5所解决的问题

HTML4定义了文档的结构与其暗示轮廓算法非常粗糙并带来了大量的问题：

1. 对于定义语义块的\<div>用法，未给class参数定义特定的值使轮廓算法自动化变得不可能（那个\<div>部分究竟是页的轮廓，定义了一个块还是一个块？或是它只是呈现了一个\<div>，仅用来编辑样式）。在其他方面，HTML4规格在**什么是一个部分块**和它的**域是如何定义**上非常不准确。轮廓的自动生成是重要的，尤其对于<a href="https://en.wikipedia.org/wiki/Assistive_technology">辅助技术</a>，可能会适应这种根据文档结构呈现信息给用户的方式。HTML5移除了来自轮廓算法的\<div>元素的需要，通过引入一个新的元素\<section>。HTML Section Element

2. 合并多个文档是困难的：包含一个子文档进一个主文档意味着改变HTML头元素的等级，所以轮廓会保持。这在HTML5中被解决，以其新介绍的块元素(\<article>,\<section>,\<nav>和\<aside>)，总会是其相邻最近祖先块的子块，不管通过内部的头元素定义了什么块

3. 在HTML4中，每一个块都是文档轮廓的一部分。但文档通常不是那么线性。一个文档可以有特殊的部分，包含着不属于其一部分的信息，虽然和主线关联，比如一个广告块或者一个解释说明盒子。HTML5介绍了\<aside>元素允许这样的块元素不成为主轮廓的部分

4. 再一次的，在HTML4中，因为每一个部分都是文档轮廓的一部分，没可能会有块包含与文档无关的信息，除了对于整个站点的元素，比如图标、菜单、内容表格或是版权信息和合法通知。为了这个目的，HTML5引入了三个新元素：\<nav>用来表示链接集合，比如一个内容表格，\<footer>和\<header>用来表示站点相关信息。注意\<header>和\<footer>不是像\<section>一样在为内容划分块，而是它们存在用来语义地标记块的部分（头部分和尾部分）

更通常的，HTML5带来了块和头部的**预测**特征，允许文档轮廓被预测，并被浏览器用来提高**用户体验**

### HTML5轮廓算法

让我们考虑下潜藏在HTML处理部分和轮廓的方式下的算法

#### 定义部分

所有的内容都躺在\<body>元素中的内容都是一个块部分。HTML5中的块可以嵌套。除了主块，被\<body>所定义的元素，块限定被定义地或明确或含蓄。明确定义的块部分是\<body>,\<section>,\<article>,\<aside>和\<nav>标签内的内容

> 每个部分可以有其自己的头层级。所以，甚至一个嵌套的部分也可以有一个\<h1>，见后续实例

让我们看一个例子——这里我们有一个拥有一个顶层部分和一个尾部定义的文档。在顶级部分我们有三个子块，以两个\<section>和一个\<aside>元素定义：

```html5
<section>

	<h1>Forest elephants</h1>

	<section>
		<h1>Introduction</h1>
		<p>In this section, we discuss the lesser known forest elephants.</p>
	</section>

	<section>
		<h1>Habitat</h1>
		<p>Forest elephants do not live in trees but among them.</p>
	</section>

	<aside>
		<p>advertising block</p>
	</aide>

</section>

<footer>
	<p>(c) 2010 The Example company</p>
</footer>
```

这导致了如下的轮廓：

```
1. Forest elephants
   1.1 Introduction
   1.2 Habitat
```

#### 定义头部

#### 明确的定义块

#### 块根

#### 轮廓外的块

#### 头部和尾部




### 部分块元素中的地址

### 在不支持HTML5的浏览器中使用HTML5元素

### 结论

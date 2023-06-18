---
title: Markdown语法学习整理
date: 2017-11-17 10:09:16
categories: 其他
tags: 
 - 其他
---

虽说markdown用了一段时间，但还未曾专门地阅读文档对其进行过相对系统的学习。刚才使用分割线时发现分割线下面的文字变成标题般字体，专门查了一波，发现还是有必要系统学习一下md语法，以节省日后使用时的查阅时间。

<!--more-->

### 宗旨

易读易写

一份使用markdown格式撰写的文档理应可以直接以纯文本发布，也就意味着markdown的一系列标签语法对于纯文本来说应该是**低浸入**、无感知的，用户阅读不会因为增加了md标签而变得晦涩。

这点区别于HTML语言至少还需要掌握HTML编程语法，才能理解部分标签的使用规则。

### 灵感

markdown最大灵感源自纯文本电子邮件的格式（可能人们对于纯文本的电子邮件格式觉得太过单调，于是通过添加一些小标记，来增加丰富的格式）

### 目标

- 成为一种适用于网络的书写语言

> markdown并非要取代HTML，甚至也没有要和它相近。

> 相比HTML，markdown语法种类少，仅对应HTML标记的一小部分

- 让文档更容易读、写、随意改

> 易读的前提就是md的标记需要尽量的没有侵入感，比如一篇纯文本文档增加md标记后不会影响原始阅读感受

> 易写易改的要求则是md的语法标记需要同HTML一样“语义化”，能够见标知意

### 兼容HTML

#### 区块标签

一些HTML区块元素比如<code>&lt;div&gt;</code>、<code>&lt;table&gt;</code>、<code>&lt;pre&gt;</code>、<code>&lt;p&gt;</code>等，**必须**在其前后加上空行与其他内容隔开，**并且开始标签与结束标签不能用制表符（Tab）或空格来进行缩进**。
（下面实例的缩进实现方式是通过HTML的pre标签，使其内部的markdown语法失效）

> 比如这里的HTML标签如果我想要以标签的样子显示在屏幕上，那我其实需要在文本里通过md标记来编辑，类似使用code标签、转义字符（通过转义字符来“画出”尖括号，code标签可以使其成为块状文本）

另外，markdown生成器不会在HTML区块标签外加上不必要的<code>&lt;p&gt;</code>标签，区别于对于HTML文本进行编辑时，若你写了大段内容而未加任何标签的话，HTML生成器会为你在内容前后加上<code>&lt;p&gt;</code>标签

实例如下：

<pre><code>段落A

&lt;table&gt;
		&lt;tr&gt;
				&lt;td&gt;Foo&lt;/td&gt;
		&lt;/tr&gt;
&lt;/table&gt;

段落B
</code></pre>

> 注意！在HTML区块标签区间的Markdown语法将不会被处理

#### 区段（行内）标签

HTML行内标签比如&lt;span&gt;、&lt;cite&gt;、&lt;del&gt;可以在Markdown的段落、列表或是标题内随意使用，比如直接使用&lt;a&gt;标签撰写超链接、使用&lt;img&gt;标签取代md的

<pre><code>
![](图片地址)
</code></pre>

（当想要md标签直接显示在屏幕上，而不被markdown生成器处理，可以将其写在HTML区块标签内）

总结：HTML区块内的md标签不生效，HTML区段内的md标签生效

### 特殊字符自动转换

<a href="https://balabala&balabala">balabala</a>

上面这个a标签超链接href中的https://balabala&balabala会被解析成https://balabala%26balabala/

（这一点我看到的时候，瞬间感觉到头皮发麻，因为我之前博客里的参考链接都是使用HTML的a标签，也就意味着如果连接内包含&字符的话，其实应该要改写成<code>&amp;amp;</code>才能被正确的解析）转义还真是门学问-_-||

如果你下在页面显示出AT&amp;T，其实需要在md文件中写成AT&amp;amp;T，而想要显示AT&amp;amp;T，则要在md中写成AT**&amp;amp;**apm;T，别绕晕，总之需要通过<code>&amp;amp;</code>这个转义符来“渲染”&符，即遇到想要显示<code>&amp;</code>的地方使用<code>&amp;amp;</code>来转义，理清之后会觉得转义很有意思 : ）

那么究竟markdown对于<code>&amp;</code>的处理方式是如何的呢？答案是：视情况而定

- 不做转换

> 当<code>&amp;</code>是HTML字符实体的一部分时，它会不做干涉

> 比如你在md内写到&amp;copy;

> 会被解析成&copy;

（注意：当想你的md某段内容包含md标签时，请不要在外部套上HTML区块标签，因为那会使md标签失效）

- 转换

> 而如果你在md中写的<code>&amp;</code>不是HTML字符实体的一部分的话，它就会被转换成&amp;amp;

markdown这么做充分体现其兼容HTML的做法

（当多行引用标记连续使用时，若其中某一行进行缩进，会在引用的基础上增加层次）

4 < 5

4 &lt; 5

在code范围内，不论是行内还是区块，<和&两个符号都**一定**会被转换成HTML实体，这项特性可以使你轻易地使用md写HTML code，相比在HTML文档中需要把所有的<和&转换为HTML实体才能在HTML文件内写HTML code

上面这段话的意思就是在html文件里，遇到&或是<，需要以&amp;amp;或是&amp;lt;这样的HTML实体来表示之后，才能正常地编写html code，但是在markdown里不需要这一步，

`<span><code>4 &</code></span>`

`<code>4 < 5</code>`

`<code><code>`

参考链接：

- <a href="http://www.appinn.com/markdown/">http://www.appinn.com/markdown/</a>

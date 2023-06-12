---
title: 前端基础知识整理——jQuery选择器
date: 2018-02-23 20:01:25
update: 2018-02-23 20:01:25
categories: 前端
tags: [jQuery]
---

关于jQuery选择器的整理

<!--more-->

## jQuery选择器

### 第一种分类规则

#### 第一类——基本

1. 通配：$("*")——所有元素

2. id唯一选择：$("#name")——匹配id为name的元素

3. class匹配：$(".name")——匹配所有class="name"的元素

4. 标签元素：$("p")——所有<p>元素

5. 且关系匹配：$(".a.b")——所有class="a"且class="b"的元素

#### 第二类——首尾、奇偶

1. 某种标签的第一个：$("p:first")——第一个<p>元素

2. 某种标签的最后一个：$("p:last")——最后一个<p>元素

3. 某种标签的偶数元素：$("tr:even")——所有偶数<tr>元素

4. 某种标签的奇数元素：$("tr:odd")——所有奇数<tr>元素

#### 第三类

1. 第n个元素：$("ul li:eq(3)")——列表中的第四个元素（索引从0起）

2. 大于某个索引值的元素：$("ul li:gt(3)")——索引大于3的元素

3. 小于某个索引值的元素：$("ul li:lt(3)")——索引小于3的元素

4. 非某个条件的元素：$("input:not(:empty)")——所有不为空的input元素

#### 第四类——指定标签

1. 标题元素：$(":header")——所有标题元素（h1-h6）

2. 动画元素：$(":animated")——所有动画元素

#### 第五类

1. 包含特定字符串的元素：$(":contains('someString')")——包含字符串someString的元素

2. 无子节点的所有元素：$(":empty")——没有子元素节点的所有元素

3. 隐藏元素：$("p:hidden")——所有隐藏的<p>元素

4. 可见元素：$("table:visible")——所有可见的表格

#### 第六类——或关系多匹配

逗号分隔的多匹配元素：$("th, td, .intro")——所有th、td和class="intro"的元素

#### 第七类——元素属性

1. 含有某个属性的元素：$("[href]")——所有包含href属性的元素

2. 属性为特定值的元素：$("[href='#']")——所有href属性为#的元素

3. 属性不为特定值的元素：$("[href!='#']")——所有href属性不为#的元素

4. 符合属性值通配的元素：$("[href$='.jpg']")——所有href属性以.jpg结尾的元素

#### 第八类——不同type的input元素

text、password、radio、checkbox、submit、reset、button、image、file

![](http://trigolds.com/jq0.jpg)

#### 第九类——input不同元素状态

1. 可用元素：$(":enabled")——所有可用的input元素

2. 不可用元素：$(":disabled")——所有不可用的input元素

3. 选取的元素：$("selected")

4. 多选被选中的：$("checked")

### 第二种分类规则

1. 基本选择器

	1. id（指定元素）

	2. class（css类）

	3. element（遍历html元素）

	4. *（所有元素）

	5. ,（并列）

2. 层次选择器

	1. parent > child（直系子元素）

	2. prev + next（下一个兄弟，等同于next()方法）

	```javascript
	$('.item + div').css('color', '#FF0000');
	```

	等价于

	```javascript
	$('.item').next('div').css('color', '#FF0000');
	```

	3. prev ~ siblings（所有兄弟，等同于nextAll()方法）

	```javascript
	$('.inside ~ div').css('color', '#FF0000');
	```

	等价于

	```javascript
	$('.inside').nextAll('div').css('color', '#FF0000');
	```

3. 过滤选择器

	1. 基本过滤

		1. 首尾元素

			- :first

			- :last

		2. 取非

		```javascript
		$('div:not(.wrap)').css('color', '#FF0000');
		```

		```HTML
		<div>a</div>
		<div class="wrap">b</div>
		```

		a着色，b不着色

		特别地，当非wrap元素包含子元素时，即是其子元素class为wrap，仍会被选择到（因为父影响子）

		```HTML
		<div>a
			<div class="wrap">b</div>
		</div>
		```

		a、b都着色

		3. 奇偶（索引从0开始）

			- :even

			- :odd

		4. 取指定元素（:eq(index)）

		5. 大于或小于某个索引值

		- :gt(index)

		- :lt(index)

		6. 标题(:header)

	2. 内容过滤

		1. 包含（:contains(text)）

		2. 不包含子元素或文本内容为空（:empty）

		3. 取选择器匹配的元素（:has(selector)）

		```javascript
		$('div:has(span)').css('border', '1px solid #000');
		```

		```HTML
		<div>
			<h2>
				A
				<span>B</span>
			</h2>
		</div>
		```

		因为此处的div子元素中的确含有span，所以此处的div被选中，其子元素也会同样生效，所以A和B都会被加边框

		4. 包含子元素或文本的元素（:parent）

		某个元素因包含子元素或文本，而成为父元素，即被选中

		```javascript
		$('ol li:parent').css('border', '1px solid #000');
		```

		```HTML
		<ol>
	    <li></li>
	    <li>A</li>
	    <li></li>
	    <li>D</li>
		</ol>
		```

		A和D因为包含文本而晋升为父元素，所以被加上边框

	3. 可见性过滤

		1. 隐藏元素（不占空间）——:hidden

			1.3.2版本后:hidden仅匹配**隐藏**且**不占空间**的元素（display:none、<input type="hidden">），对于visibility:hidden、opacity:0这样隐藏但占据空间的元素无效

		2. 可见元素——:visible（凡不可见的元素均不选中）

	4. 属性过滤

		1. [attribute]（选择拥有attrbute属性的元素）

		2. [attribute = value]、[attribute != value]（属性等于或不等于某值的元素）

		3. [attribute ^= value]、[attribute $= value]、[attribute *= value]（attribute以value开头、以value结束、包含value的元素）

		4. [selector1][selector2]（复合型属性过滤，同时满足多个条件）

		```javascript
		$('a[title^=jQuery][class=item]').hide();
		```

		title属性以jQuery开头并且class='item'的a元素选中并隐藏

		5. 子元素过滤

			1. :first-child、:last-child

			:first和:last返回单个元素

			:first-child和:last-child返回集合元素

			2. :only-child（有且只有一个子元素的元素）

			3. :nth-child（第n个元素，n从1开始）

		6. 表单对象属性过滤

			1. 可用或不可用元素（:enabled、:disabled）

			2. 选中的单选框或多选框（:checked）

			3. 下拉框中选中的元素（:selected）

4. 表单选择器

总结：

1. 基本选择器

2. 层次选择器

3. 过滤选择器

4. 表单选择器

参考链接：

- <a href="http://www.w3school.com.cn/jquery/jquery_ref_selectors.asp">http://www.w3school.com.cn/jquery/jquery_ref_selectors.asp</a>

- <a href="http://www.cnblogs.com/keepfool/archive/2012/06/02/2532203.html">http://www.cnblogs.com/keepfool/archive/2012/06/02/2532203.html</a>

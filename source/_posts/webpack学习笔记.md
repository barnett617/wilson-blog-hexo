---
title: webpack学习笔记
date: 2018-01-17 11:41:10
update: 2018-01-17 11:41:10
categories: 前端
tags: 
 - webpack
---

关于Webpack基础使用的整理

<!--more-->

### 背景

- 网站由网页模式进化成Webapp模式

- 网站运行在高级浏览器中，使用HTML5、CSS3、ES6等新技术

- webapp通常是单页面应用（每一个视图通过异步方式加载，导致页面初始化和使用过程会加载更多的js代码）

- 前端开发基于多语言、多层次编码和组织工作，交付基于浏览器，需要保证代码和资源在浏览器端快速优雅的加载和更新，亟需**模块化系统**

### 传统方式

```
<script src="module1.js"></scrpti>
<script src="module2.js"></scrpti>
<script src="module3.js"></scrpti>
...
```

#### 弊端

- 全局作用域（定义在window对象下）下易造成变量冲突

- 文件只能按照脚本引入的顺序加载

- 需要主观解决模块和代码库的依赖关系

- 大型项目中资源难以管理，长期积累导致代码库混乱不堪

### CommonJS

服务端的Node.js遵循CommonJS规范

#### 核心思想

1. 允许模块通过require方法来**同步加载**要依赖的其他模块
2. 通过exports或module.exports导出需要暴露的接口

```
require("module");
require("../file.js");
exports.doStuff = function() {};
module.exports = someValue;
```

#### 优势

- 服务端模块便于重用

- NPM中已有大量可用模块包（20w）

- 简单易用

#### 缺陷

- 同步的模块加载方式不适合在浏览器环境中，同步意味着阻塞加载，浏览器资源是异步加载的

- 不能非阻塞的并行加载多个模块

### ES6模块

ES6标准增加了js语言层面的模块体系定义

ES6的设计思想是尽量的**静态化**，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。

#### 优势

- 易于进行静态分析

- 面向未来ES标准

#### 缺陷

- 原生浏览器未实现该标准

- 全新命令字，新版Node.js才支持

### 静态分析

编译时对整个代码进行静态分析，分析出各模块的**类型**和**依赖关系**，将不同类型的模块交由相应的加载器处理。

### Webpack概念

Webpack是一个模块打包器。

根据模块的依赖关系进行静态分析，然后将模块按照指定的规则生成对应的静态资源

### 现有模块化工具无法实现

- 将依赖树拆分成按需加载的块

- 初始化加载的耗时尽量少

- 各种静态资源都可视作模块

- 将第三方库整合成模块

- 自定义打包逻辑

- 适合无论单页或多页的Web应用大项目

### Webpack特点

- 代码拆分

- Loader

- 智能解析

- 插件系统

- 快速运行

#### 代码拆分

- 两种组织模块依赖的方式：同步、异步、

- 异步依赖作为分割点，形成新的块

- 优化了依赖树后，每一个异步区块都作为一个文件被打包

#### Loader

- Webpack本身只能处理原生js模块

- loader转换器可将各种类型的资源转换成js模块

#### 智能解析

Webpack有一个智能解析器，几乎可以处理任何第三方库

无论模块形式是CommonJS或是普通js文件

加载依赖时甚至允许动态表达式

```
require("./templates/" + name + ".jade")
```

#### 插件系统

Webpack有一个功能丰富的插件系统

可开发和使用开源插件满足各式需求

#### 快速运行

Webpack使用异步I/O和多级缓存提高运行效率

### 安装

Webpack需要Node.js v0.6以上支持

使用npm全局安装

```
npm install webpack -g
```

将Webpack安装到项目的依赖中

```
npm install webpack --save-dev
```

### 使用

index.html

```
<html>
<head>
	<meta charset="utf-8">
</head>
<body>
	<scritp src="bundle.js"></script>
</body>
</html>
```

entry.js

```
document.write('Hello world')
```

编译entry.js并打包到bundle.js

```
webpack entry.js bundle.js
```

访问index.html即可

#### 添加模块

module.js

```
module.exports = 'It works from module.js.'
```

entry.js

```
document.write('Hello world')
document.write(require('./module.js'))
```

打包

```
webpack entry.js bundle.js
```

#### 分析

webpack分析入口文件，解析包含依赖关系的各个文件

这些文件（模块）都打包到bundle.js

webpack给每个模块分配一个唯一ID并通过ID索引和访问模块

页面启动时先执行entry.js，其他模块在运行require时再执行


### loader

#### 背景

webpack本身只能处理js模块，其他类型文件需要loader进行转换

loader本身一个函数，接受源文件为参数，返回转换结果

#### loader特性

- 可通过管道方式链式调用，每个loader可将资源转换成任意格式并传递给下一个loader，最后一个loader必须返回js

- 可同步或异步执行

- 运行在node.js环境中，可做任何可能的事情

- 可接受参数，以此传递配置给loader

- 可通过文件拓展名（或正则表达式）绑定给不同类型的文件

- 可通过npm发布和安装

- 除了通过package.json和main指定，通常的模块也可导出一个loader来使用

- 可访问配置

- 支持插件

- 可分发出附件的任意文件

#### loader命名

一般为xxx-loader，xxx为转换的功能，例如json-loader

引用loader时可通过全名（json-loader）或短名(json)

命名规则和搜索优先级顺序在resolveLoader.moduleTemplates API中定义

```
Default: ["*-webpack-loader", "*-web-loader", "*-loader", "*"]
```

#### 添加方式

- 在require()引用模块时添加

- webpack全局配置中绑定

- 命令行方式

#### 场景

在页面引入一个CSS文件style.css

```
body { background: yellow; }
```

entry.js

```
// 载入style.css
require("!style-loader!css-loader!./style.css")
document.write('Hello world.')
document.write(require('./module.js'))
```

#### 处理

将style.css看作一个模块

用css-loader读取

用style-loader把它插入页面

#### 安装loader

```
npm install css-loader style-loader
```

即可

#### 改进

根据模块类型（拓展名）自动绑定需要的loader，避免每次require CSS文件时都写loader前缀

entry.js

```
require("!style!css!./style.css")
```

修改为

```
require("./style.css")
```

#### 打包

```
webpack entry.js bundle.js --module-bind "css=style-loader!css-loader"
```

Bingo! 效果一样

只不过是在打包环节根据模块类型绑定需要的loader，不需要在require中写loader前缀

### 配置文件

上面通过在命令行在执行webpack时传入参数，可通过指定配置文件来执行

默认搜索当前目录webpack.config.js

该文件是一个node.js模块，返回一个json格式的配置信息对象

可通过--config选项指定配置文件

#### 具体操作

创建package.json添加webpack需要的依赖

```
{
	"name": "my-webpack",
	"version": "1.0.0",
	"description": "A simple webpack example",
	"main": "bundle.js",
	"scripts": {
		"test": "echo \"Error: no test specified\" && exit 1"
	},
	"keywords": [
		"webpack"
	],
	"author": "igam",
	"license": "MIT",
	"devDependencies": {
		"css-loader": "^0.21.0",
		"style-loader": "^0.13.0",
		"webpack": "^1.12.2"
	}
}
```

根据package.json下载依赖模块
```
npm install
```

创建配置文件webpack.config.js

```
var webpack = require('webpack')

module.exports = {
	entry: './entry.js',
	output: {
		path: __dirname,
		filename: 'bundle.js'
	},
	module: {
		loaders: [
			{test: /\.css$/, loader: 'style-loader!css-loader'}
		]
	}
}
```

Bingo again!

### 插件

一般在配置文件plugins选项中指定

webpack内置一些常用插件，还可通过npm安装第三方插件

#### 实例

内置插件BannerPlugin：给输出的文件头部添加注释

webpack.config.js

```
var webpack = require('webpack')

module.exports = {
	entry: './entry.js',
	output: {
		path: __dirname,
		filename: 'bundle.js'
	},
	module: {
		loaders: [
			{test: /\.css$/, loader: 'style-loader!css-loader'}
		]
	},
	plugins: [
		new webpack.BannerPlugin('This file is created by igam')
	]
}
```

在bundle.js文件头可看见效果

### 开发环境

编译时间长，可以通过参数让编译输出内容带有**进度**和**颜色**

```
webpack --progress --colors
```

若不想每次修改模块后重新编译，可启动**监听模式**

开启后，没有变化的模块会在编译后缓存到内存中，而非每次重新编译

```
webpack --progress --colors --watch
```

#### 更好选择

使用webpack-dev-server开发服务

将在localhost:8080启动一个express静态资源服务器

以监听模式自动运行webpack

访问http://localhost:8080/或http://localhost:8080/webpack-dev-server/可浏览项目中的页面和编译后的资源输出，并通过一个socket.io服务实时监听变化并自动刷新

#### 安装

```
npm install webpack-dev-server -g
```

#### 运行

```
webpack -dev-server --progress --colors
```

### 故障处理

可通过参数--display-error-deatils打印错误详情

webpack的配置提供了resovle和resolveLoader参数设置模块解析的处理细节

resolve配置应用层模块（被打包的模块）解析

resolveLoader配置loader模块的解析

> 当引入通过npm安装的node.js模块时，可能出现找不到依赖的错误

Node.js模块的依赖解析算法是通过查看模块的每一层父目录中的node-modules文件夹来查询依赖

当出现Node.js模块依赖查找失败时可尝试设置resolve.fallback和resolveLoader.fallback解决问题

```
module.exports = {
	resolve: { fallback: path.join(__dirname, "node_modules") },
	resolveLoader: { fallback: path.join(__dirname, "node_modules") }
}
```

webpack中设计路径配置最好使用绝对路径，建议使用path.resolve(__dirname, "app/folder")或path.join(__dirname, "app", "folder")方式配置，以兼容Windows环境

参考： <a href="http://zhaoda.net/webpack-handbook/index.html">http://zhaoda.net/webpack-handbook/index.html</a>

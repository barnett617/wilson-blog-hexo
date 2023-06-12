---
title: React教程——安装篇
date: 2018-02-07 14:59:31
update: 2018-02-07 14:59:31
categories: 前端
tags: react
---

React官网教程系列之前期准备

<!--more-->

## 初尝React

在线尝试React或在本地开发环境安装React

### 在线版

如果你对React的乐趣在于只是随便玩玩，那你可以使用在线编程环境。在<a href="https://codepen.io/pen?&editors=0010">CodePen</a>或<a href="https://codesandbox.io/s/new">CodeSandbox</a>尝试Hello World模板

### 最小HTML模板

如果你更想使用你自己的文本编辑器，你也可以用下载<a href="https://raw.githubusercontent.com/reactjs/reactjs.org/master/static/html/single-file-example.html">这个</a>HTML文件，编辑它并从你本地浏览器中的文件系统打开它。它会有一个缓慢的代码转换，所以不要在生产中使用它。

### 接下来

#### 快速上手

- 前往<a href="https://reactjs.org/docs/hello-world.html">快速上手</a>部分来一步一步按照指引学习React的概念

- 尝试<a href="https://reactjs.org/tutorial/tutorial.html">教程</a>作为一个动手实践机会

#### 完整开发环境

如果你初次接触React或者仅仅用于实验，上面的轻量解决方案很适合你

当你准备好使用React去构建你第一个程序时，查阅下面的安装指引。这些安装被设计用来帮助你并获得良好的开发体验，并且可用于生产。它们包括代码检验（linting）、测试和最佳构建（optimizations built-in)；然而它们需要更多的时间和空间来安装

- 使用React<a href="https://reactjs.org/docs/add-react-to-a-new-app.html">创建一个新的应用</a>：使用一个包含完整特性的开始工具套件创建一个新的应用

- 将React<a href="https://reactjs.org/docs/add-react-to-an-existing-app.html">添加进一个已存在的应用</a>：将React添加进一个构建系统或一个更大的应用

##### 将React添加一个新的应用

在一个新的应用上使用React启动的最简单方式就是使用一个开始工具套件（strater kit）

> 这页描述了创建一个你所需要的单页应用，以提供一个舒服的工作流，包含代码检查（linting）、测试和最佳生产以及更多。

> 全特性工具需要一些时间和空间来安装。如果你想要一个轻量环境来用React做实验，查看上面的初尝React方式，一个单一的HTML文件足够开始。

> 最后，如果你不是构建单页应用，你可以将React<a href="https://reactjs.org/docs/add-react-to-an-existing-app.html">添加进已存在的构建管道</a>或者从<a href="https://reactjs.org/docs/cdn-links.html">CDN</a>使用它而<a href="https://reactjs.org/docs/react-without-jsx.html">无需构建</a>

###### 创建React应用

<a href="https://github.com/facebook/create-react-app">Create React App</a>是开始构建一个新的React单页应用最好的方式。它建立了你的开发环境，这样你就可以使用到最新的JavaScript特性，提供你一个良好的开发体验，为你最佳化你的生产应用。你将需要机器上安装大于版本6的Node

```
npm install -g create-react-app时
create-react-app my-app

cd my-app
npm start
```

如果你安装了5.2.0以上版本的npm，你可能使用m<a href="https://www.npmjs.com/package/npx">npx</a>代替

```
npx create-react-app my-app

cd my-app
npm start
```

创建React应用不会处理后端逻辑和数据库；它只是创建了一个前端构建管道，所以你可以把它用作任何后端。它底层使用比如<a href="http://babeljs.io/">Babel</a>和<a href="https://webpack.js.org/">Webpack</a>的构建工具，但是只需要零配置就能工作。

当你准备好部署你的产品，运行npm run build将会在build目录为你的应用创建一个最佳构建。你可以通过它的<a href="https://github.com/facebook/create-react-app#create-react-app-">README</a>和<a href="https://github.com/facebook/create-react-app#create-react-app-">用户手册</a>学习更多关于Create React App

###### 其他启动工具

我们创建了一份官方推荐的<a href="https://reactjs.org/community/starter-kits.html">第三方启动工具列表</a>

它们在各自侧重点有些细微不同，但都是生产就绪的、良好维护的并且不需要任何配置就能开始的。

你也可以查看一个由社区贡献的<a href="https://reactjs.org/community/starter-kits.html#other-starter-kits">其他工具列表</a>

###### 高级使用

如果你倾向于手动配置一个工程，查阅下一部分的<a href="https://reactjs.org/docs/add-react-to-an-existing-app.html#installing-react">安装React</a>

##### 将React添加进一个已存在的应用

你无需重写你的应用以开始使用React

我们推荐添加React进你应用的一块小的部分，比如一个独立的组件，这样你可以在你的用例中看到它是否工作

虽然React可以被使用于<a href="https://reactjs.org/docs/react-without-es6.html">没有构建管道的情况</a>，但我们推荐建立构建管道，这样你可以提高生产。一个现代的构建管道通常组成：

- 一个包管理器，比如<a href="https://yarnpkg.com">Yarn</a>或者<a href="https://www.npmjs.com/">npm</a>。这使你能够利用第三方包的浩瀚生态，并轻易地安装或者更新它们

- 一个打包器，比如<a href="https://webpack.js.org/">webpack</a>或者<a href="http://browserify.org/">Browserify</a>。这让你书写模块化的代码并把它们打包到一起成小的包，以使得加载时最优

- 一个编译器，例如<a href="http://babeljs.io/">Babel</a>。它让你书写现代化化JavaScript代码，却仍能工作在旧的浏览器

###### 安装React

> 一旦安装，我们强烈推荐建立一个<a href="https://reactjs.org/docs/optimizing-performance.html#use-the-production-build">生产构建进程</a>以确保你在生产中使用React的快速版本

我们推荐使用Yarn或者npm来管理前端依赖。如果你初次接触包管理器，<a href="https://yarnpkg.com/en/docs/getting-started">Yarn文档</a>是一个良好开始的地方。

使用Yarn安装React并运行：

```
yarn init
yarn add react react-dom
```

使用npm安装React并运行：

```
npm init
npm install --save react react-dom
```

Yarn和npm都是从npm注册中心下载

> 为避免潜在的不兼容问题，所有的react包应该使用相同的版本。（这包括：react、react-dom、react-test-renderer等等）

###### 使ES6和JSX生效

我们推荐使用伴随Babel的React以使得你使用ES6和JSX在你的JavaScript代码中。ES6是一个现代JavaScript代码的集合，使得开发更简单，JSX是一个使得JavaScript语言和React一起良好工作的拓展

<a href="https://babeljs.io/docs/setup/">Babel安装指引</a>解释了如何配置Babel在许多不同的构建环境。确保你安装<a href="http://babeljs.io/docs/plugins/preset-react/#basic-setup-with-the-cli-">babel-preset-react</a>和<a href="http://babeljs.io/docs/plugins/preset-env/">babel-preset-env</a>并确保它们在你的<a href="http://babeljs.io/docs/usage/babelrc/">.babelrc</a>配置中，你就可以良好工作了。

###### 使用ES6和JSX的Hello World

我们推荐使用像webpack或Browserify这样的打包器，这样你可以书写模块化代码并它们打包到一起进小的包裹以在加载时最优化

最小的React例子：

```JavaScript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
	<h1>Hello, world!</h1>,
	document.getElementById('root')
);
```

这份代码将id为root的元素渲染成一个DOM元素，所以你需要在你的HTML文件的某处有<div id="root"></div>

类似的，你可以将你使用任何其他JavaScriptUI库写的已存在的应用中某处的DOM元素内渲染一个React组件

了解更多关于<a href="https://reactjs.org/docs/integrating-with-other-libraries.html#integrating-with-other-view-libraries">集成React和已存在代码</a>

###### 开发和生产版本

默认的，React包含许多有帮助的警告。这些警告在开发中非常有用。

然而，它使得开发版本变得更大更慢，所以你应该当你部署你的应用时使用生产版本。

学习<a href=""https://reactjs.org/docs/optimizing-performance.html#use-the-production-build>如何辨别你的网站是否正在服务正确版本的React</a>，还有如何最高效地配置生产构建进程：

- <a href="https://reactjs.org/docs/optimizing-performance.html#create-react-app">使用Create React App创建一个生产构建</a>

- 使用单文件构建创建一个生产构建

- 使用Brunch创建一个生产构建

- 使用Browserify创建一个生产构建

- 使用Rollup创建一个生产构建

- 使用webpack创建一个生产构建

###### 使用CDN

如果你不想用npm管理客户端包，react和react-dom的npm包也提供了单文件版本在umd目录。详情见<a href="https://reactjs.org/docs/cdn-links.html">CDN页</a>

##### CDN链接

React的UMD构建和ReactDOM在CDN之间可用

```javascript
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
```

上面的版本仅对开发而言，对于生产不适用。简化和优化生产版本如下：

```javascript
<script crossorigin src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
```

加载react和react-dom一个特定的版本，用版本号替换16即可

###### 为什么有crossorigin这个属性

如果你从CDN提供React服务，我们推荐你保持crossorigin这个属性：

```JavaScript
<script crossorigin src="..."></script>
```

我们也推荐去确认你正在使用的CDN集的Access-Control-Allow-Origin: *的HTTP头（在HTTP相应头包含）。这使得一个更好的<a href="https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html">错误处理体验</a>在React16和以后的版本

参考链接：<a href="https://reactjs.org/docs/try-react.html">https://reactjs.org/docs/try-react.html</a>

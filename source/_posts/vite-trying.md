---
title: vite体验
date: 2023-06-29 23:50:58
tags:
---

学习 Pinia 源码时尝试运行[Pinia 官网](https://pinia.vuejs.org/core-concepts/)提供的样例，基于 [Vite](https://vitejs.dev/guide/)初始化了项目，在初始化 Pinia Store 中遇到的一些问题记录

<!-- more -->

# import alias "@" 的问题

由于 Vite 被设计为框架无关的的开发工具，因此原本属于 [Vue CLI](https://cli.vuejs.org/guide/installation.html) 的一些预置配置在 Vite 初始化的 Vue 项目中是没有的，比如引用模块时使用的“@”

```js
import { useCounterStore } from "@/stores/counter";
```

这样的用法在一个基于 Vue CLI维护的项目中是很常见的，其中“@”表示项目的“src”目录，但这其实是 Vue CLI 服务内部基于 webpack 的 [别名解析](https://webpack.js.org/configuration/resolve/#resolvealias) 为我们实现的能力

```js
// @vue/cli-service/lib/config/base.js

webpackConfig.resolve
  .alias.set("@", api.resolve("src"));
```

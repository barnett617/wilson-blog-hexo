---
title: 如何用vscode调试node程序和vue源码
date: 2019-12-17 19:46
tags: [vue, vscode, debug]
---

# 如何用vscode调试node程序和vue源码

## 第一部分：如何使用 `vscode` 调试 `node` 程序

### 一、准备待调试代码

```js
const Koa = require('koa');
const app = new Koa();

// logger
app.use(async (ctx, next) => {
  debugger
  await next();
  const rt = ctx.response.get('X-Response-Time');
  console.log(`${ctx.method} ${ctx.url} - ${rt}`);
});

// x-response-time

app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  ctx.set('X-Response-Time', `${ms}ms`);
});

// response

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```

### 二、配置 `vscode` node 调试自动关联开关

1. 打开 `vscode` 设置（mac下可通过command + , 快捷打开）
2. 搜索 `Auto Attach`
3. 设置为开（on）

### 三、运行程序携带`检查`参数


```js
node --inspect=0.0.0.0:9229 index.js
```

```js
node --inspect-brk index.js
```

### 四、`Chrome` 配置

1. 在 `Chrome` 地址栏输入 `chrome://inspect`
2. 点击 `Open dedicated DevTools for Node`
3. 将端口号修改为启动程序时指定的端口号
4. 在 `Chrome` inspect 菜单中找到 `Target` 下找到运行的 `node` 程序
5. 点击 `inspect`

### 五、Chrome 作为调试客户端与程序建立关联

可在 `vscode` 中看到程序会在断点处停住

## 参考链接

- [Debug Your Node.js App in 60 Seconds](https://medium.com/the-node-js-collection/debug-your-node-js-app-in-60-seconds-9ee942a453f0)
- [Node.js debugging in VS Code](https://code.visualstudio.com/docs/nodejs/nodejs-debugging?wt.mc_id=medium-blog-jopapa)
- [Debugging Guide](https://nodejs.org/en/docs/guides/debugging-getting-started/#enable-inspector)

## 第二部分：如何调试 `vue`

### 一、为开发模式启动脚本添加参数

为 `package.json` 中 script 组内的 `dev` 脚本添加 `--sourcemap` 参数

修改后为(随 `vue` 的更新可能会有稍许不同)：

```js
"dev": "rollup -w -c scripts/config.js --environment TARGET:web-full-dev --sourcemap"
```

### 二、修改 `index.html` 中关于 `vue` 编译包的引用

选取 `examples` 中任一 `index.html`, 修改其原本对于 `vue` 包的引用为 `<script src="../../dist/vue.js"></script>`

修改对比：

修改前
```html
<script src="../../dist/vue.min.js"></script>
```

修改后
```html
<script src="../../dist/vue.js"></script>
```

### 三、在修改过 `vue` 包引入的 `index.html` 文件所引入的 `app.js` 中增加 `debugger`

```js
created: function () {
  debugger
  this.fetchData()
},
```

### 四、安装简易服务器工具并启动

```js
npm i -g http-server
hs -p 8100
```

### 五、使用 `Chrome` 访问被 `http-server` 代理的文件目录

```
http://localhost:8100/examples/
```

### 按需调试

打开控制面板 `source` 在左侧找到 `src` 目录 即 `vue.js` 源码文件，根据自己需求断点调试即可

## 参考链接

- [vuex 源码学习](https://mp.weixin.qq.com/s/6TGSWEsMu4lzoXTUshGrEg)

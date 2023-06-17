---
title: mpvue刷新问题
date: 2020-01-10 18:16
tags: ['小程序', 'vue']
---

### 背景介绍

`mpvue`小程序迁移为`uniapp`项目`A`(用于标识项目)，同时编译部署微信小程序和微信服务号`H5`

### 问题描述

页面刷新时会导致`vuex`内状态失效，但不准备在页面刷新前将`vuex`备份到`localStorage`等临时存储方式

由于小程序使用开发者工具刷新时会回到项目首页（pages中的第一个索引），于是将`H5`交互保持与小程序一致，使用微信内置刷新时项目跳转回首页。

处理方式为：

在App.vue的`onLaunch`（项目生命周期）中增加以下逻辑

```js
// #ifdef H5
const visit = sessionStorage.getItem('appvue');
if (visit === 'true') {
  wx.reLaunch({
    url: '/pages/home/home'
  });
} else {
  sessionStorage.setItem('appvue', true);
}
// #endif
```

代码解释：

条件编译控制此逻辑仅在H5编译方式下生效

使用`sessionStorage`判断页面是否在进行刷新操作（在一次会话中，第一次进入项目时存储一个状态`appvue`，当使用微信浏览器内置刷新操作时，会话未过时，但项目会重新触发项目生命周期`onLaunch`，此时发现`sessionStorage`中包含`appvue`的状态，则执行页面跳回首页的交互）

但项目中存在`webview`组件

当项目中某个页面通过`webview`组件打开部署在另一个地方的`H5`项目B页面时，存在以下交互：

`B` (webview)某页面点击按钮跳转到 `A` (小程序/H5)的页面，点击返回，会直接以访问 `A` 项目公网地址的方式进行跳转（类似`https://www.xxx.com/A`）

此时跳回 `A` 会触发项目 `A` 的 `App.vue` 中的 `onLaunch` （项目生命周期）执行，但会话未过时，会触发跳转首页的逻辑，导致因为从 `webview` 跳转到项目 `A` 而跳回项目首页，而不是指定的路由，故存在问题。

### 解决方案

#### 方案一

在 `webview` 组件加载时去掉 `sessionStorage` 中的标识，这样当从 `webview` 访问项目 `A` 时不会刷新回首页

web-view.vue
```js
onLoad(query) {
    this.url = decodeURIComponent(query.url)
    const visit = sessionStorage.getItem('appvue');
    if (visit === 'true') {
      sessionStorage.removeItem('appvue');
    } 
}
```

App.vue
```js
onLaunch(e) {
  // #ifdef H5
  const visit = sessionStorage.getItem('appvue');
  if (visit === 'true') {
      wx.reLaunch({
        url: '/pages/home/home'
      });
  } else {
    sessionStorage.setItem('appvue', true);
  }
  // #endif
}
```


方案问题

从 `webview` 访问项目 `A` ，`App.vue` 执行 `onLaunch` 时不会跳转首页，但会记录 `appvue` ，此时页面返回到 `webview` 后再次进入，`sessionStorage` 检测到 `appvue` 有值，则仅第一次进入 `A` 时不会跳转首页，返回再进入时还是会跳转

#### 方案二

维护一个白名单，内容是在 `webview` 中访问的 `A` 项目路由，放行不进行跳转首页

App.vue
```js
methods: {
  /**
    * 是否是从webview进行跳转的页面
    * @return 是否进行跳转
    */
  pagesFromWebview(e) {
    let skipFlag = false;
    // 维护白名单控制在webview中访问的页面不做回到首页的跳转
    const notFreshPages = [
      'iou-reason', 
      'upload-reason-imgs', 
      'info-fill', 
      'iou-info',
    ];
    if (e && e.path) {
      const list = e.path.split('/');
      if (list && list.length > 0) {
        // 取最后一个作为路由标识
        const flag = list[list.length - 1];
        console.log('==========flag: ' + flag);
        // 如果该路由在不刷新的名单中则不进行回到首页的跳转
        if (notFreshPages.includes(flag)) {
          skipFlag = true;
        }
      }
    }
    return skipFlag
  }
},

onLaunch(e) {
  // #ifdef H5
  const visit = sessionStorage.getItem('appvue');
  // TODO: 弃用维护白名单的方式，改用更好的方式
  if (visit === 'true' && !this.pagesFromWebview(e)) {
      wx.reLaunch({
        url: '/pages/home/home'
      });
  } else {
    sessionStorage.setItem('appvue', true);
  }
  // #endif
}
```

方案问题：需要维护白名单，当白名单内的路由变更时可能会因为遗漏维护而导致问题再次出现

### 方案三

弃用白名单，增加第二个 `sessionStorage` 状态，当进入 `webview` 时禁用跳回首页的开关，离开 `webview` 时关闭禁用

web-view.vue
```js
onLoad(query) {
  this.url = decodeURIComponent(query.url)
  // 进入webview后禁止【页面刷新回首页】
  const notfresh = sessionStorage.getItem('notfresh');
  if (!notfresh) {
    sessionStorage.setItem('notfresh', true);
  }
},
onUnload() {
  // 从webview页返回到本项目时触发
  this.url = ''
  // 离开webview时解禁【页面刷新回首页】
  const notfresh = sessionStorage.getItem('notfresh');
  if (notfresh === 'true') {
    sessionStorage.removeItem('notfresh');
  }
},
```

App.vue
```js
onLaunch(e) {
  // #ifdef H5
  const visit = sessionStorage.getItem('appvue');
  const notfresh = sessionStorage.getItem('notfresh');
  if (visit === 'true' && notfresh !== 'true') {
    wx.reLaunch({
      url: '/pages/home/home'
    });
  } else {
    sessionStorage.setItem('appvue', true);
  }
  // #endif
}
```

存在问题，无法对 `webview` 跳去的 `A` 项目页面做刷新后跳转首页的控制
---
title: vuex对比pinia
date: 2023-06-28 21:38:46
tags:
---

vuex@3对比pinia@v2

<!-- more -->

# 版本信息

| 包名  | 版本号  |
| -- | -- |
| vuex | 3.6.2 |
| pinia | 2.1.3 |

# API解惑

为什么mutations内的对象第一个参数是当前模块的state，第二个是payload，并且mutation内部的this可以访问到store实例

因为对于mutations的注册实现如下，mutations是一个数组，每个元素是一个函数，也就是mutation，当mutation被触发时，执行的即是`handler.call(store, local.state, payload);`，这里call的第一个参数是执行handler的上下文，也就是其内部访问的this，后面的参数会是handler执行时的入参，local.state对应一个mutation方法的第一个参数，payload是第二个参数

```js
function registerMutation (store, type, handler, local) {
  var entry = store._mutations[type] || (store._mutations[type] = []);
  entry.push(function wrappedMutationHandler (payload) {
    handler.call(store, local.state, payload);
  });
}
```

为什么actions内的对象第一个参数可以解构出dispatch方法用于调用其他actions，可以解构出commit用于调用mutation，可以解构出getters和state用于访问当前模块的状态，可以解构出rootGetters和rootState用于访问store根模块下的getters和state

为什么action一定返回Promise

```js
function registerAction (store, type, handler, local) {
  var entry = store._actions[type] || (store._actions[type] = []);
  entry.push(function wrappedActionHandler (payload) {
    var res = handler.call(store, {
      dispatch: local.dispatch,
      commit: local.commit,
      getters: local.getters,
      state: local.state,
      rootGetters: store.getters,
      rootState: store.state
    }, payload);
    if (!isPromise(res)) {
      res = Promise.resolve(res);
    }
    if (store._devtoolHook) {
      return res.catch(function (err) {
        store._devtoolHook.emit('vuex:error', err);
        throw err
      })
    } else {
      return res
    }
  });
}
```

如上，actions一个是数组，而且每个元素是一个方法，方法执行时的上下文是store实例，第一个参数是一个对象，里面包括dispatch、commit、getters、state、rootGetters和rootState，可按照需要解构出其中的内容进行调用。如果需要调用其他action，则使用dispatch，如果调用mutation，则使用commit，如果需要访问当前模块的状态则使用getters或state，如果需要访问根模块的状态则使用rootGetters或rootState

最终action的执行结果如果不是Promise，也会经过Promise.resolve来生成一个Promise用作返回，保持返回值是Promise的一致性

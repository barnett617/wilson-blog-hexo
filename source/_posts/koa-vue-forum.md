---
title: 一周时间使用Vue+Koa全栈开发论坛项目
date: 2018-08-27 09:30:00
tags:
---

# 一周时间使用Vue+Koa全栈开发论坛项目

## 使用vue-cli3脚手架初始化前端项目

### 1. 安装vue-cli3

```
npm install -g @vue/cli
```

### 2. 创建项目

```
vue create code-monkeys-fe-vue
```

### 3. 核心配置文件

> vue-cli3采用`约定优于配置`的思想，去除了很多配置项，转而都由项目根目录下的`vue.config.js`中既定的格式进行配置

### 4. 示例配置

```js
const express = require('express');
const app = express();
const fs = require('fs');
const path = require('path');

var apiRoutes = express.Router();
app.use('/mock',apiRoutes)

const responseFunction = (req, res) => {
  const fileName = req.params.fileName;
  let obj = null;
  try {
    // require 有缓存问题
    let objStr = fs.readFileSync(path.join(__dirname, '/mock/data/' + fileName)).toString();
    if (objStr) {
      obj = JSON.parse(objStr);
    }
  } catch (e) {
    // console.log('无法打开 => ' + fileName + ' 文件');
  }
  if (!obj || obj == undefined) {
    res.json({
      err: 'json文件无数据'
    });
  } else {
    res.json(obj);
  }
};

module.exports = {
  devServer: {
    // 中间加一道代理，根据识别到的路由规则拦截请求（此函数相当于拦截器）
    before(app) {
      app.get('/mock/data/:fileName', responseFunction);
      app.post('/mock/data/:fileName', responseFunction);
    },
    // 解决跨域问题，对`/api`请求代理跨域
    proxy: {
      '/api': {
        // target: 'http://127.0.0.1:3000/',//设置你调用的接口域名和端口号 
        target: 'http://xx.xx.xx.xx:3000/',//设置你调用的接口域名和端口号 
        changeOrigin: true,     //跨域
        // pathRewrite: {
        //   '^/api': '/'          //这里理解成用‘/api’代替target里面的地址，后面组件中我们掉接口时直接用api代替 比如我要调用'http://10.1.5.11:8080/xxx/duty?time=2017-07-07 14:57:22'，直接写‘/api/xxx/duty?time=2017-07-07 14:57:22’即可
        // }
      }
    }
  },
  configureWebpack: {
    devtool: 'source-map'
  },
}
```

### 5. package.json

```json
{
  "name": "code-monkeys-fe-vue",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "cross-env NODE_ENV=dev vue-cli-service serve --inspect --open",
    "build": "cross-env NODE_ENV=prod vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "axios": "^0.18.0",
    "vue": "^2.5.17",
    "vue-router": "^3.0.1"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "^3.0.0",
    "@vue/cli-plugin-eslint": "^3.0.0",
    "@vue/cli-service": "^3.0.0",
    "cross-env": "^5.2.0",
    "vue-template-compiler": "^2.5.17"
  },
  "eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
      "plugin:vue/essential",
      "eslint:recommended"
    ],
    "rules": {},
    "parserOptions": {
      "parser": "babel-eslint"
    }
  },
  "postcss": {
    "plugins": {
      "autoprefixer": {}
    }
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not ie <= 8"
  ]
}

```

### 6. 加入router

> 修改入口文件

```js
import Vue from 'vue'
import App from './App.vue'
import './utils/global'
import router from './router'

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App)
}).$mount('#app')

router.push('/');

// 相当于以下完整写法，见：https://segmentfault.com/a/1190000014254740
// const app=new Vue({
//   el:'#app',
//   router,
//   compoments:{
//       App
//   },
//   template:"<App/>"
// });
```

> 增加路由配置

```js
import Vue from 'vue'
import Router from 'vue-router'
import HomeContent from '@/views/HomeContent';
import SinglePostContent from '@/components/ForumPost/SinglePostContent.vue'

Vue.use(Router)

export default new Router({
  mode: 'history',
  routes: [
    {
      path: '/',
      name: 'homeContent',
      component: HomeContent
    },
    {
      path: '/single-post/:id',
      name: 'single-post-content',
      component: SinglePostContent
    },
  ]
})
```

### 7. 开发主页面

```css
<template>
  <div id="app">
    <ForumHome />
  </div>
</template>

<script>
import ForumHome from './components/ForumHome.vue'

export default {
  name: 'app',
  components: {
    ForumHome
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  height: 100%;
  /* margin-top: 60px; */
}
html, body {
  height: 100%;
}
a {
  text-decoration: none;
  cursor: pointer;
}
</style>

```

### 8. 封装ajax请求对象

```js
// export 时使用default，则import时不需要加大括号，否则需要加大括号引入export导出的变量
import apiConfig from './apiConfig';
import axios from 'axios';
/**
 * 使用立即执行函数表达式（IIFE）声明一些全局性（window对象的）方法或属性
 */
(function(win) {
    /**
     * 为 String 添加 endsWith 方法，此方法在ES6中实现，以下为向下兼容处理（Polyfill）
     */
    if (!String.prototype.endsWith) {
        String.prototype.endsWith = function(search, this_len) {
            if (this_len === undefined || this_len > this.length) {
                this_len = this.length;
            }
            return this.substring(this_len - search.length, this_len) === search;
        };
    }
    /**
     * 判断是否是字符串
     * @param {*} obj 
     */
    function isStr(obj) {
        return (typeof obj === 'string')
    }
    /**
     * ajax请求
     * @param {*} obj 
     */
    const ajax = function (obj) {
        // 最终ajax请求的地址
        let url;
        const ajaxUrl = obj.url;
        // 如果地址以Api结尾
        if (isStr(ajaxUrl) && ajaxUrl.endsWith('Api')) {
            // 判断环境是否为开发环境
            const env = process.env.NODE_ENV;
            // 存在环境变量设置并且在枚举范围内
            if (env && apiConfig[ajaxUrl] && apiConfig[ajaxUrl][env]) {
                url = apiConfig[ajaxUrl][env];
            }
        } else {
            url = ajaxUrl;
        }
        /* eslint-disable */
        console.log(obj);
        console.log(url);
        if (obj.type == 'get') {
            axios.get(url)
            .then(function (response) {
                obj.callback(response);
            })
            .catch(function (error) {
                obj.err(error);
            });
        } else {
            console.log(url, obj.params)
            axios.post(url, obj.params)
            .then(function (response) {
                obj.callback(response);
            })
            .catch(function (error) {
                obj.err(error);
            });
        }
    }
    win.ajax = ajax;
    win.isStr = isStr;
})(window)
```

### 9. 路由使用

```css
<template>
  <div class="home-center">
    <div class="home-center-nav">
      <router-link class="home-router" to="/">
        {{ page_title }}
      </router-link>
    </div>
    <!-- 以下为路由所决定显示的部分 -->
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'HomeCenter',
  data() {
    return {
      page_title: 'Home',
    }
  },
  components: {
  },
  props: {
    msg: String
  },
  methods: {
  },
  created() {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
body {
  height: 100%;
}
.home-router {
  color: rgb(119, 119, 119);
}
.home-center {
  display: flex;
  flex-direction: column;
  height: 100%;
  background-color: #EEEEEE;
  padding: 0 143px;
}
.home-center-nav {
  padding: 15px 0;
}
.home-center-content {
  display: flex;
  justify-content: flex-start;
}
</style>

```

### 10. jenkins自动化打包发布

> 拉取指定git仓库指定分支或指定tag代码并执行以下脚本完成编译打包

```sh
npm config set registry http://registry.npm.taobao.org/ &&
npm install &&
npm run build &&
cd dist &&
tar -zcvf dist.tar.gz *
```

> 指定服务器地址，通过ssh方式发送项目包并解压到指定目录

```sh
cd /opt/www/codemonkeys-fe
tar -zxvf dist.tar.gz
rm -rf dist.tar.gz
```

### 11. 配置Nginx

```
server {
    listen       80;
    server_name  xxx.com;

    #charset koi8-r;
    access_log  /var/log/nginx/xxx.com.access.log  main;

    location / {
        root    /opt/www/xxx;
        index   index.html index.htm;
    }

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

### 12. 使用Koa开发后端服务器提供接口

> 初始化package.json

```
npm init
```

> 配置package.json

```json
{
  "name": "code-monkeys-fe-node",
  "version": "1.0.0",
  "description": "#### 项目介绍 前段项目node代理服务",
  "main": "index.js",
  "scripts": {
    "dev": "export NODE_ENV=development && nodemon --harmony ./app.js",
    "test": "echo \"Error: no test specified\" && exit 1",
    "deploy_setup": "pm2 deploy ecosystem.config.js production setup",
    "deploy": "pm2 deploy ecosystem.config.js production",
    "start": "export NODE_ENV=production && pm2 start ./app.js --max-memory-restart 300M -n codemonkeys-node --watch",
    "stop": "export NODE_ENV=production && pm2 stop ./app.js -n codemonkeys-node"
  },
  "repository": {
    "type": "git",
    "url": "git@xxx.git"
  },
  "author": "codemonkeys@wangxin",
  "license": "ISC",
  "dependencies": {
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-stage-0": "^6.24.1",
    "koa": "^2.5.3",
    "koa-bodyparser": "^4.2.1",
    "koa-logger": "^3.2.0",
    "koa-mysql-session": "0.0.2",
    "koa-redis": "^3.1.2",
    "koa-router": "^7.4.0",
    "koa-session-minimal": "^3.0.4",
    "koa2-cors": "^2.0.6",
    "mysql": "^2.16.0"
  },
  "devDependencies": {
    "nodemon": "^1.18.4",
    "pm2": "^3.2.2"
  }
}

```

> 入口主文件`app.js`

```js
const Koa = require('koa');
const app = new Koa();
const routers = require('./router/index');
const koaLogger = require('koa-logger');
const session = require('koa-session-minimal');
const redisStore = require('koa-redis');
const cors = require('koa2-cors');
const bodyParser = require('koa-bodyparser');

const { HOST, PORT } = require('./config/server-info');

const env = process.env.NODE_ENV;

// cookie
// 存放sessionId的cookie配置
/* session cookie */
let cookieConfig = {
  maxAge: 86400000,      // cookie有效时长
  expires: '',        // cookie失效时间
  path: '',          // 写cookie所在的路径
  domain: HOST,        // 写cookie所在的域名
  httpOnly: true,        // 是否只用于http请求中获取
  overwrite: true,      // 是否允许重写
  secure: '',
  sameSite: '',
  signed: '',
};

// 使用koa2-cors中间件解决跨域请求
app.use(cors());

app.use(bodyParser());

// 配置session中间件
app.use(session({
  key: 'USER_SID',
  store: redisStore(),
  cookie: cookieConfig
}));

app.use(koaLogger());

// 初始化路由中间件
app.use(routers.routes()).use(routers.allowedMethods());
app.use(async ctx => {
  if (ctx.status === 404) {
    ctx.redirect('/not-found');
  }
});

// 监听启动端口
app.listen(PORT);
console.log(`the server is start at port ${PORT}`);
```

### 13. 使用pm2部署启动后端项目，核心配置文件

```js
module.exports = {
  apps : [{
    script: 'app.js',
    interpreter : 'node@10.0.0',
    // Options reference: https://pm2.io/doc/en/runtime/reference/ecosystem-file/
    // args: 'one two',
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '300M',
    env: {
      NODE_ENV: 'development'
    },
    env_production: {
      NODE_ENV: 'production'
    }
  }],

  deploy : {
    production : {
      user : 'root',
      host : 'xx.xx.xx.xx',
      ref  : 'origin/master',
      repo : 'git@xx.git',
      path : '/opt/www/xxx',
      "ssh_options":"StrictHostKeyChecking=no",
      'post-deploy' : 'npm install && pm2 reload ecosystem.config.js --env production'
    }
  }
};

```

### 14. 增加后端请求路由配置

```js
const router = require('koa-router')();
const api = require('../controller/api');

module.exports = router
  .get('/', api.homepage)
  .get('/api/forum-post/list', api.getPosts)
  .post('/api/forum-post/getById', api.getPostById);
```

### 15. 接收请求并处理

```js
const forumPost = require('../models/forum-post');
const timeFormat = require('../utils/time-format');

module.exports = {
  async homepage(ctx) {
    return ctx.body = 'server running'
  },
  /**
   * 分页查询列表
   * @param {*} ctx 
   */
  async getPosts(ctx) {
    const currentPage = ctx.request.query.currentPage || 1;
    let pageSize = ctx.request.query.pageSize || 10;
    const page = {
      currentPage: currentPage,
      pageSize: pageSize
    };
    let result = await forumPost.getPostList(page);
    result = result.map(item => {
      item.create_time = timeFormat(item.create_time);
      return item;
    });
    return ctx.body = {
      status: 200,
      data: {
        result
      }
    };
  },
  /**
   * 根据ID查询
   * @param {*} ctx 
   */
  async getPostById(ctx) {
    const postId = ctx.request.body.postId;
    let result = await forumPost.getPostById(postId);
    return ctx.body = {
      status: 200,
      data: {
        result
      }
    };
  },
}
```

### 16. 操纵数据库做数据获取

```js
const dbUtils = require('../utils/db-utils');

const ForumPost = {

  /**
   * 分页查询文章列表
   * @param {*} page 
   */
  async getPostList(page) {
    const _sql = `
      SELECT * FROM forum_post
      WHERE is_delete = 0 AND is_publish = 1
      ORDER BY forum_post.create_time DESC
      LIMIT ${page.pageSize} OFFSET ${page.currentPage - 1}`;
    const result = await dbUtils.query(_sql);
    return result;
  },

  /**
   * 获取指定文章
   * @param {string} id 文章id
   */
  async getPostById(id) {
    const _sql = `
      SELECT forum_post.*
      FROM forum_post
      WHERE forum_post.id = ${id}`;
    const result = await dbUtils.query(_sql);

    if (result.length) {
      return result[0];
    }
    return null;
  },
}

module.exports = ForumPost;
```

### 17. 新建数据库

```sql

/** 建库 **/
CREATE DATABASE IF NOT EXISTS `codemonkeys-node` DEFAULT CHARSET utf8 COLLATE utf8_general_ci;

/** 建帖子表 **/
CREATE TABLE IF NOT EXISTS `forum_post`(
   `id` INT NOT NULL AUTO_INCREMENT,
   `avatar` VARCHAR(100) NULL,
   `title` VARCHAR(40) NOT NULL,
   `author` VARCHAR(40) NOT NULL,
   `date` datetime,
   `create_time` datetime DEFAULT CURRENT_TIMESTAMP,
   `category` VARCHAR(40) NULL,
   `content` VARCHAR(20000) NOT NULL,
   `commentNum` INT NULL,
   `is_delete` INT DEFAULT 0,
   `is_publish` INT DEFAULT 1,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

/** 原始数据 **/
INSERT INTO forum_post 
(avatar, title, author, date, category, content, commentNum) 
VALUES 
('http://ourcia6f4.bkt.clouddn.com/avatar3.jpg','基于Spring Cloud的微服务设计','wangzhenzhong','2018-08-27','微服务','微服务这个词大家应该都不陌生，是最近几年技术发展的热门词汇之一。在当前系统需求越来越复杂，实现和维护成本越来越高的背景下，微服务确实是未来的发展趋势之一。由于工作的需要，最近花了半个月左右的时间研究了基于Spring Cloud的微服务设计与实现，颇有收获，本文就来对这半个月的成果进行一个总结，并聊一聊在我对微服务的一些思考。','64'
),
('http://ourcia6f4.bkt.clouddn.com/avatar4.jpg','再品Git, 深度解读','Taikoo','2018-08-23','git','和一个长期使用IDE(eclipse)集成git进行代码版本管理的人交流项目，博文内容将采用类似《大话设计模式》的对话体进行，场景真实，有代入感，正文部门前面是对方参照我写的readme搭建基于vue-cli的demo项目，安装node并通过npm安装项目依赖并本地启动项目等等，由于对方为后端Java开发，对新前端技术栈了解比较少，在搭建环境和启动项目过程中滋生一些趣事，比如使用npm启动项目后如何在浏览器访问项目，我为了让其醒目意识到我们的项目首页就是未经改动的Vue-cli首页，于是我将首页改动并推到远程master分支，后面便是我们的搞笑对话。','60'
);

```

### 18. 后端接口服务nginx配置

```

server {
     listen 80;
     server_name xxx.com;
     access_log /var/log/nginx/xxx.com-access.log;
     error_log  /var/log/nginx/xxx.com-error.log;

     location / {
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header Host $http_host;
             proxy_set_header X-NginX-Proxy true;
             proxy_pass http://xx.xx.xx.xx:3000/;
             proxy_redirect off;
     }
}
```

### 19. 发布后端服务到服务器指定目录

```
npm run deploy_setup
```

> 会在指定目录生成三个目录

```
npm run deploy
```

> 会将后端项目部署到指定目录

### 20. 启动后端项目在服务器后端运行

> 进入服务器，在后端项目目录执行

```
npm run start
```

### 21. 即可通过浏览器访问前端项目，调用后端接口返回数据进行显示
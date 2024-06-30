---
title: Vue3项目搭建的4种方式
date: 2024/04/14 22:26:48
updated: 2024/04/14 22:26:54
categories:
- [技术, Vue]
tags:
- Vue
- Vite
- NPM
- Webpack
---


## Vue介绍

- 官方文档：https://vuejs.org
- 地址：https://github.com/vuejs



## 特性

- 渐进式框架

  能够自动追踪依赖的模板表达式和计算属性，提供 MVVM 数据绑定和一个可组合的组件系统，具有简单、灵活的 API，使读者更加容易理解，能够更快上手。

- 双向数据绑定

  声明式渲染是数据双向绑定的主要体现，同样也是 Vue.js 的核心，它允许采用简洁的模板语法将数据声明式渲染整合进 DOM。

- 指令

  Vue.js 与页面进行交互，主要就是通过内置指令来完成的，指令的作用是当其表达式的值改变时相应地将某些行为应用到 DOM 上。

- 组件化

  组件可以扩展 HTML 元素，封装可重用的代码。

- Vue 路由管理

  Vue-Router 是 Vue 全家桶中的路由管理插件，与 Vue.js 深度集成，用于构建单页面应用。Vue 单页面应用是基于路由和组件的，路由用于设定访问路径，并将路径和组件映射起来，传统的页面是通过超链接实现页面的切换和跳转的。

- Vue 状态管理

  Vuex 是 Vue 全家桶中的状态管理插件，与 Vue.js 深度集成，用于管理应用的状态。

- Vue 脚手架

  Vue-Cli 是 Vue 全家桶中的脚手架工具，给 Vue 应用提供了开箱即用的功能，让开发者能够快速创建、开发、构建一个完整的 Vue 项目。



## DOM操作

网页是由 DOM 组合的视图结构，再加上 CSS 样式的修饰，使用 JavaScript 接受用户的交互请求，并通过事件机制来响应用户交互操作而形成的。

`Jquery`是一个经典的DOM操作库。

```js
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>jquery Dom测试</title>
    <!-- 引入 jquery 框架 -->
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
  </head>
  <body>
    <div id="root">
      hello world
    </div>
    <script>
      $("#root").on("click", function(event) {
        event.target.innerText = "hello DOM";
      });
    </script>
  </body>
</html>
```



## 数据绑定

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Vue MVVM测试</title>
    <!-- 引入 Vue.js 框架 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/3.4.21/vue.global.prod.min.js"></script>
  </head>
  <body>
    <div id="root">
      <div @click="handleClick">{{msg}}</div>
    </div>
    <script>
      Vue.createApp({
        data() {
          return {
            msg: "hello world",
          };
        },
        methods: {
          handleClick() {
            this.msg = "hello MVVM";
          },
        },
      }).mount("#root");
    </script>
  </body>
</html>
```



## 项目搭建



### 通过CDN引入

```js
<script src="https://unpkg.com/vue@3.0.11/dist/vue.global.js"></script>
```

你可以使用官方提供的资源包（这里以 3.0.11 版本为例）：

- https://unpkg.com/vue@3.0.11/dist/vue.global.js

  浏览器环境中的全量 Vue.js 代码。

- https://unpkg.com/vue@3.0.11/dist/vue.global.prod.js

  浏览器环境中的全量并且压缩过后的 Vue.js 代码。

- https://unpkg.com/vue@3.0.11/dist/vue.runtime.global.js

  浏览器环境中的不带编译功能的 Vue.js 代码。

- https://unpkg.com/vue@3.0.11/dist/vue.runtime.global.prod.js

  浏览器环境中的不带编译功能并且压缩过后的 Vue.js 代码。

- https://unpkg.com/vue@3.0.11/dist/vue.esm-browser.js

  `EsModule` 规范的 Vue.js 全量代码，直接在浏览器通过 `ES modules` 方式引入（`<script type="module">`）。

- https://unpkg.com/vue@3.0.11/dist/vue.runtime.esm-browser.js

  `EsModule` 规范的不带编译功能 Vue.js 代码，直接在浏览器通过 `ES modules` 方式引入（`<script type="module">`）。

带 `runtime` 跟不带 `runtime` 的资源包，比如：vue.runtime.global.js 跟 vue.global.js：

- vue.runtime.global.js：除了编译 template 功能以外的 Vue 代码。
- vue.global.js：包含了编译 template 功能完整的 Vue 代码。

如果你的项目需要用到编译 template 属性的功能，比如：

```js
createApp({
  template: "<app></app>",
});
```

Vue 最后是需要将 template 编译成 `render` 方法进行渲染的，所以你就需要用到 Vue 的编译功能，因此就需要引入带有编译功能的 Vue 代码（vue.global.js）。

`prod` 和 `prod` 后缀的资源包

比如：`vue.global.js` 跟 `vue.global.prod.js`。

带 `prod` 的意思就是 “是否经过压缩混淆过后的代码”，`prod` 的全称是 `production`，也就是生产环境的意思。

> 当项目处在开发阶段的时候，用没有 `prod` 后缀的代码，当项目需要发布上线的时候，用带 `prod` 后缀的代码。



CDN引入示例：

```js
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Vue use CDN</title>
    <!-- 引入 全量 Vue.js 代码 -->
    <script src="https://unpkg.com/vue@3.0.11/dist/vue.global.js"></script>
  </head>
  <body>
    <div id="root">
      <div>{{msg}}</div>
    </div>
    <script>
      Vue.createApp({
        data() {
          return {
            msg: "hello CDN",
          };
        },
      }).mount("#root");
    </script>
  </body>
</html>
```



### 通过NPM安装

```npm
# 创建工程目录
mkdir ./vue-npm-demo 
# 进入目录并初始化npm项目
cd vue-npm-demo && npm init -y
# 安装webpack依赖
npm install -D webpack@4.46.0 webpack-cli@4.10 webpack-dev-server@3.11.2 vue-loader@16.1.2 @vue/compiler-sfc@3.0.11 html-webpack-plugin@3.2.0
# 安装vue
npm install vue@3.0.11
# 创建webpack配置文件
touch ./webpack.config.js
```

添加`webpack.config.js`内容：

```js
const path = require("path");
module.exports = {
  target: "web",
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: "vue-loader",
      },
    ],
  },
  plugins: [
    new (require("vue-loader").VueLoaderPlugin)(),
    new (require("html-webpack-plugin"))({
      template: path.resolve(__dirname, "./public/index.html"), // 指定模版文件
    }),
  ],
  devServer: {
    host: "0.0.0.0",
    disableHostCheck: true,
    contentBase: path.resolve(__dirname, "./public"), // 设置一个 express 静态目录
    port: 8080,
    hot: true,
    sockPort: "location",
  },
};
```

配置完Webpack后，创建webpack的入口文件。

在 `vue-npm-demo` 工程目录下创建一个 `src` 目录，并且在 `src` 目录下创建一个 `index.js` 文件：

```sh
mkdir ./src && touch ./src/index.js
```

将以下内容写入到 `vue-npm-demo/src/index.js` 文件：

```js
import { createApp } from "vue";
// 引入 App.vue 组件
import App from "./App.vue";
// 渲染 app
createApp(App).mount("#app");
```

然后创建一个 `App.vue` 组件。

在 `vue-npm-demo/src` 目录下创建一个 `App.vue` 文件：

```vue
<template>
  <div>{{ msg }}</div>
</template>
<script>
export default {
  setup() {
    return {
      msg: "hello world",
    };
  },
};
</script>
```

为了能让项目跑起来，我们还需要创建应用的入口文件 `index.html`。

我们在 `vue-npm-demo` 目录下创建一个 `public` 目录，并且在 `vue-npm-demo/public` 目录下创建一个 `index.html` 文件：

```sh
mkdir ./public && touch ./public/index.html
```

将以下内容写入到 `vue-npm-demo/public/index.html` 文件：

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body>
    <noscript>your browser should support javascript!</noscript>
    <div id="app"></div>
    <!-- html-webpack-plugin 将自动引入入口文件 -->
  </body>
</html>
```

在 `vue-npm-demo` 工程目录下执行以下命令开启 `webpack` 服务：

```sh
# 设置环境变量
export NODE_OPTIONS=--openssl-legacy-provider
# 开启 webpack 服务
npx webpack serve --mode=development 
```

 `Webpack` 搭建的 `Vue` 项目就搞定了。



### 通过Vue-cli安装

执行以下命令安装

```sh
npm install -g @vue/cli@4.5.12 && vue create vue-cli-demo
# 安装完根据提示选择(选择：Vue3和npm)
```

在`vue-cli-demo` 工程目录下创建一个 `vue.config.js` 文件：

```sh
touch ./vue-cli-demo/vue.config.js
```

将以下内容写入到 `vue-cli-demo/vue.config.js` 文件：

```js
module.exports = {
  configureWebpack: {
    devServer: {
      host: "0.0.0.0",
      port: 8080, 
      disableHostCheck: true, 
    },
  },
};
```

进入到 `vue-cli-demo` 工程目录，并且在 `vue-cli-demo` 目录下执行以下命令启动项目：

```sh
# 设置环境变量
export NODE_OPTIONS=--openssl-legacy-provider 
cd vue-cli-demo && npm run serve
```

 `Vue-Cli` 脚手架项目就搭建完了。 



### 通过Vite安装

```sh
npm init vite vue-vite-demo
# 在后续的选择中选择Vue项目和JavaScript
```

进入到 `vue-vite-demo` 项目目录并执行 `npm` 初始化：

```sh
cd vue-vite-demo && npm install
```

修改 `vue-vite-demo` 目录下的 `vite.config.js` 文件：

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  server: {
    host: "0.0.0.0", 
    port: 8080, 
  },
});
```

执行命令启动项目：

```sh
npm run dev
```

这样就完成了通过Vite搭建项目。



（完）
---
title: 初识渐进式框架Vue
date: 2019-12-18 11:51:53
categories: vue
tags: vue
---

> 成功的花，人们只惊羡它的明艳，然而当初的芽儿浸透了奋斗的泪泉，洒遍了牺牲的血雨。

### Vue.js是什么？
Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。
官方提供的视频:![](https://player.youku.com/embed/XMzMwMTYyODMyNA==?autoplay=true&client_id=37ae6144009e277d)
官方提供的学习地址:![](https://vuejs.bootcss.com/v2/guide/)

### 起步
在这篇博客中只是简单的介绍vue的相关基础用法，我们采用npm的方式进行vue项目的安装和运行，前提条件是安装node.js具体的安装步骤参考node官方网站
**node.js的下载地址:**![]( https://nodejs.org/en/download/)

### 初始化Vue项目
在这里采用淘宝镜像进行安装
**可以通过node -v 和 npm -v 命令来查看对应的版本号**
1.全局安装vue的脚手架，类似于Java的开发环境一样。vue的运行也同样需要环境来支持
**npm install vue-cli -g**
2.初始化一个vue项目
**vue init webpack '项目名'**
3.进入创建好得项目中
**npm install**
4.运行环境
**npm run dev**

### 安装前端组件element-ui
vue项目中组件化是vue的核心，常用的前端框架ElementUI
1.安装element-ui
**npm i element-ui -S**
当element-ui安装完成后再main.js中进行引用。
```
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI);
```

### 安装路由组件Router
vue项目中常用到页面之间的相互跳转
**npm install vue-router --save**
安装完成后再main.js中进行引用
```
import router from './router'
Vue.use(router);
```
### 新建.vue文件
vue项目初始化完成后运行显示的App.vue但是在实际的开发过程中我们需要新建一个.vue文件
一般来说在component文件夹中新建或者自建一个新的文件夹来存放改文件
新建Home.vue文件代码如下：
```
<template>
<div>
    <h1>{{message}}</h1>
</div>
</template>

<script>
export default{
    data(){
        return{
            message: 'welcome'
        }
    }
}
</script>
```
完成后需要在router中进行路由的配置：
在router的index.js中代码如下：
```
import Home from '@/views/Home'

export default new Router({
  mode: 'history',
  routes: [
    {
      name: 'Homes',
      path: '/home',
      component: Home
    }
  ]
})
```

### 页面间进行跳转
```
//点击跳转新的页面
    newPage(){
     const vm = this;
     vm.$router.push({name: 'Homes', params: { id: 10 }})
    }
  }
```

### 参数的接受
```
<span>获取参数ID为:{{this.$route.params.id}}</span>
```



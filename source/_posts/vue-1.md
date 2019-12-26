---
title: 掌上书城-mobile
date: 2019-12-25 15:00:48
categories: vue
tags: vue
---

> 希望,是一种甜蜜的等待;想念,是一份温馨的心情;朋友,是一生修来的福分;爱情;是一世难解的缘分。祝你在人生的道路上多点快乐!多点开心!

### 每日一笑
遇见两个法国人。一个可能是教汉语的老师，另一个应该是他学生。老师高兴地指着中国日历对学生说：看，这两个字念'雷锋'。这是雷锋纪念日。他在中国非常有名，因为他生前帮助过很多人）学生佩服地说：啊，你真是见多识广！说完俩人高兴地走了。我凑过去一看，见日历上写的是："霜降

### 概要
在上篇文章中介绍了Vue的安装和基本的入门级操作。这篇文章主要通过一个手机端掌上书城的简单案例来带大家深入的了解Vue和了解在实际开发中的项目的搭建和部署。


### 效果图
**首页**
![](https://img-blog.csdnimg.cn/20191225163023262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)

**列表页**
![](https://img-blog.csdnimg.cn/20191225163154578.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)

**新增页**
![](https://img-blog.csdnimg.cn/20191225163238823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)

### 初始化Mobile项目
- 项目结构如下：
![](https://img-blog.csdnimg.cn/20191225160737799.png#pic_center)

**dist:**是我们项目打包完成后的目录
**mock:**是本地服务端测试数据
**node-modules:**项目所需要的插件安装位置
**src:**项目的核心目录
**api:**所有请求数据的接口存放的位置
**assets:**静态资源存放的位置包括css,images等
**base:**存放我们的基础组件
**components:**存放我们的页面组件
**router:**存放路由

### 项目所需要的插件

```
前端UI框架：element-ui   -- npm install element-ui --save-dev
发送请求axios： --npm install axios --save-dev
路由组件router:  --npm install vue-router
轮播图插件：  --npm install vue-awesome-swiper
实现图片的懒加载：  --npm install vue-lazyload
阿里巴巴图标库地址:   https://www.iconfont.cn/search/index?q=%E9%A6%96%E9%A1%B5
```

### 基础组件的使用
一个完整的页面组件并不是一个独立的整体，它是由很多的基础组件的组合形成的。那么在一个组件中如何引入其他组件呢？例如Home.vue中包括Top.vue,Tab.vue,Swiper.vue,Loading.vue等。
以Home.vue组件中引入Tab.vue为例进行说明。其他进行参考即可！
具体的步骤如下：
- 新建Home.vue和Tab.vue文件
- 在Home.vue中导入Tab.vue
```
 import Tab from './base/Tab.vue'
```
- 注册组件
```
export default {
    name: 'App',
    components: {
      Tab
    }
  }
```
- 使用组件
```
<Tab></Tab>
```

### 轮播图插件的使用
- 轮播图插件安装完成后再main.js文件中进行导入并使用
```
import VueAwesomeSwiper from 'vue-awesome-swiper'   //轮播图插件
import 'swiper/dist/css/swiper.css'
Vue.use(VueAwesomeSwiper);
```
- 新建轮播图基础组件
```
<template>
  <swiper :options="swiperOption">
    <swiper-slide v-for="(slide, index) in swiperSlides" :key="index">
      <img :src="slide" alt="轮播图">
    </swiper-slide>
    <div class="swiper-pagination" slot="pagination"></div>
  </swiper>
</template>

<script>
  export default {
    name: 'carousel',
    props:['swiperSlides'],
    data() {
      return {
        swiperOption: {
          autoplay:1200,
          pagination: '.swiper-pagination',
          mousewheelControl : true
        },
      }
    }
  }
</script>

<style scoped lang="less">
  img{
    width: 100%;
    height: 200px;
  }
</style>
```

### 数据请求Axios
上述轮播图组件新建完成，在Home.vue中引入轮播图组件
轮播图的测试数据sliders.js
```
module.exports = [
  'https://img12.360buyimg.com/babel/s590x470_jfs/t1/85934/6/7983/82595/5e008712E0e3b9c12/7cfc1ce9f03369f3.jpg.webp',
  'https://img20.360buyimg.com/pop/s590x470_jfs/t1/106050/5/7033/72549/5df99388Ed5c1018a/6eb231965d4b0432.jpg.webp',
  'https://imgcps.jd.com/ling/100010137716/55S16ISR5pWw56CB5Zyj6K-e5a2j/5pyA6auYNuacn-WFjeaBrw/p-5bd8253082acdd181d02f9d8/baf73efa.jpg',
  'https://img14.360buyimg.com/pop/s590x470_jfs/t1/98586/8/2744/73547/5dd6443fE552a8e86/2e055c6666d0c336.jpg.webp'
];
```

### 服务端代码

```
//服务端代码
let http = require('http');
let fs = require('fs');
let url = require('url');


//获取热门图书数据
function readBook(cb) {
  fs.readFile('./book.json', 'utf8', function (err, data) {
    if (err || data.length == 0) {
      cb([]);
    } else {
      cb(JSON.parse(data));   //将内容转化为对象
    }
  })
}

function writeBook(data, cb) {   //写入内容
  fs.writeFile('./book.json', JSON.stringify(data), cb);
}


let pageSize = 5;  //每页显示5个

//获取轮播图数据/sliders
let sliders = require('./sliders.js');
http.createServer((req, res) => {

  //解决跨域问题
  res.setHeader("Access-Control-Allow-Origin", "*");
  res.setHeader("Access-Control-Allow-Headers", "Content-Type,Content-Length, Authorization, Accept,X-Requested-With");
  res.setHeader("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
  res.setHeader("X-Powered-By", ' 3.2.1')
  if (req.method == "OPTIONS") {
    return res.end("200");
  }
  /*让options请求快速返回*/

  let {pathname, query} = url.parse(req.url, true);
  if (pathname === '/sliders') {
    res.setHeader('Content-Type', 'application/json;charset=utf8')
    return res.end(JSON.stringify(sliders))
  }


  if (pathname === '/hot') {
    readBook(function (books) {
      let hot = books.reverse().slice(0, 6);
      res.setHeader('Content-Type', 'application/json;charset=utf8');
      //设置加载延迟
      setTimeout(() => {
        res.end(JSON.stringify(hot));
      }, 500);

    });
    return
  }


  //对图书数据进行查询下拉(就是分页查询数据)
  if (pathname === '/page') {
    let offset = parseInt(query.offset) || 0;
    //拿到前端传递的值
    readBook(function (books) {
      let result = books.reverse().slice(offset, pageSize+offset);
      let hasMore = true;
      if (books.length <= pageSize + offset) {
        hasMore = false;
      }
      res.setHeader('Content-Type', 'application/json;charset=utf8');
      res.end(JSON.stringify({hasMore,books:result}));
    });
    return;
  }

  if (pathname === '/book') {  //对书的增删改查
    let id = parseInt(query.id);
    switch (req.method) {
      case 'GET':
        if (id) {//查询单个图书
          readBook(function (books) {
            let book = books.find(item => item.id === id);
            if (!book) {
              book = {};  //如果没有数据则返回undifined
            }
            res.setHeader('Content-Type', 'application/json;charset=utf8');
            res.end(JSON.stringify(book));
          });

        } else {//获取所有图书
          readBook(function (books) {
            res.setHeader('Content-Type', 'application/json;charset=utf8');
            res.end(JSON.stringify(books));
          })
        }
        break;
      case 'POST':
        let str = '';
        req.on('data', chunk => {
          str += chunk
        });
        req.on('end', () => {
          let book = JSON.parse(str);
          readBook(function (books) {
            book.id = books.length ? books[books.length - 1].id + 1 : 1;
            books.push(book);
            writeBook(books, function () {
              res.end(JSON.stringify(book));
            });
          });
        });
        break;
      case 'PUT':
        if (id) {//获取当前修改的ID
          let str = '';
          req.on('data', (chunk) => {
            str += chunk;
          });
          req.on('end', () => {
            let book = JSON.parse(str);
            readBook(function (books) {
              books = books.map(item => {
                if (item.id === id) {//找到ID相同的修改返回
                  return book;
                }
                return item;  //ID不同的正常返回其他的数据
              });
              writeBook(books, function () {  //将数据写回JSON文件中
                res.end(JSON.stringify(book));
              })
            });
          });
        }
        break;
      case 'DELETE':
        readBook(function (books) {
          books = books.filter(item => item.id !== id);
          writeBook(books, function () {
            res.end(JSON.stringify({}));   //删除成功返回空对象
          });
        });
        break;
    }
    return
  }



  //运行静态文件读取一个路径
  fs.stat('.' + pathname,function (err,stats) {
    if(err){
      res.statusCode = 404;
      res.end('NOT FOUND');
    }else{
      if(stats.isDirectory()){
        let path = require('path').join('.' + pathname,'./index.html');
        fs.createReadStream(path).pipe(res);
      }else{
        fs.createReadStream('.' + pathname).pipe(res);
      }

    }
  })


}).listen(3000);
```

### 对应接口代码
```
import axios from 'axios'
//默认url
axios.defaults.baseURL = 'http://localhost:3000';

//获取轮播图方法
export let getSliders = ()=>{
  return axios.get('/sliders')
};

//获取热门图书数据
export let getHotBook = ()=>{
  return axios.get('/hot');
};

//获取所有图书
export let getAllBook = ()=>{
  return axios.get('/book');
}

//删除某一本图书
export let removeBook=(id)=>{
  return axios.delete(`/book?id=${id}`)
}

//查询图书详情信息
export let findBookInfoById=(id)=>{
  return axios.get(`/book?id=${id}`);
}

/**
 * 修改图书
 * @param id
 * @param data 数据
 * @returns {Promise<AxiosResponse<T>>}
 */
export let editBookInfo=(id,data)=>{
 return axios.put(`/book?id=${id}`,data);
}


//新增图书
export let addBookInfo=(data)=>{
  return axios.post('/book',data);
}


//实现全局加载
// export let getAll=()=>{
//   return axios.all([getSliders(),getHotBook()]);
// }

//数据下拉
export let moreData =(offset)=>{
  return axios.get(`/page?offset=${offset}`);
}

```

### 路由元信息实现页面缓存和路由动画

对于数据量较大的网页，我们采用缓存机制，这样下次在进行加载时速度会得到明显的提升
在首页进行数据缓存在router.js下找到要进行缓存的路由地址添加 meta:{keepAlive: true} 即可
```
 {
   path: '/home',
   name: 'Home',
   component: ()=>import('../components/Home.vue'),
   meta:{keepAlive: true}   //是否需要缓存   取得时候:this.$route.meta.keepAlive
 }
```
然后在App.vue中使用
```
<template>
  <div id="app">
    <!--实现路由动画-->
    <transition name="fadeIn">
      <keep-alive>
        <!--需要进行缓存-->
        <router-view v-if="$route.meta.keepAlive"></router-view>
      </keep-alive>
    </transition>
    <!--正常的访问走下面-->
    <transition name="fadeIn">
      <router-view v-if="!$route.meta.keepAlive"></router-view>
    </transition>
    <Tab></Tab>

  </div>
</template>
```

### 图片的懒加载

使用图片的懒加载，当访问时才进行图片资源的访问，减少流量，提升资源的使用率。
代码如下：
```
//实现图片懒加载
import VueLazyload from 'vue-lazyload'
Vue.use(VueLazyload,{
  preLoad:1.3,
  error:'http://bpic.588ku.com/element_origin_min_pic/00/56/29/4556d9f8022935e.jpg',
  loading:'http://www.17qq.com/img_qqtouxiang/79912467.jpeg',
  attempt:1
});
```
在图片标签中使用
```
<img v-lazy="hot.bookCover" alt="">
```
### 代码分割 coding split 
为减少资源的使用率，提升项目的运行速度。每当我们新建一个组件时就在router.js中导入。原来的处理形式为：
```
import Home from '@/components/Home'
import Add from '@/components/Add'
import Collect from '@/components/Collect'
import Detail from '@/components/Detail'
import List from '@/components/List
```
这样项目启东时会加载所有的组件，在最后项目打包时也会占用大量的资源和生成的文件较大。所以采用下述的形式进行代码分割：
```
routes: [
    {
      path: '/',
      redirect: '/home'
    },
    {
      path: '*',
      redirect: '/home'
    },
    {
      path: '/add',
      name: 'Add',
      component: ()=>import('../components/Add.vue')
    },
    {
      path: '/home',
      name: 'Home',
      component: ()=>import('../components/Home.vue'),
      meta:{keepAlive: true}   //是否需要缓存   取得时候:this.$route.meta.keepAlive
    },
    {
      path: '/detail/:bid',
      name: 'detail',
      component: ()=>import('../components/Detail.vue')
    },
    {
      path: '/list',
      name: 'List',
      component: ()=>import('../components/List.vue')
    },
    {
      path: '/collect',
      name: 'Collect',
      //将组件按照函数的方法进行动态加载组件
      component:()=>import('../components/Collect.vue')
    }
  ]
```
这样请求那个路由，对应的组件才会加载出来。节省资源

### 项目打包
**npm run build**
右键启动server.js文件

### 源代码
**github地址：** 
```
https://github.com/vanicesun/vue-mobil.git
```



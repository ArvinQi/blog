---
title: vue学习笔记
date: 2017-05-04 16:58:50
tags: [框架, vue2.0, vue]
---
本身也是一个新手，做个学习笔记巩固下知识，希望也能帮助到同样在学习Vue的你。
<!-- more -->
### 首先-怎么开始

要学习一个新的框架首先肯定是从官方文档（[https://cn.vuejs.org/v2/guide/](https://cn.vuejs.org/v2/guide/))了解它,我只看了基础的部分，感觉用不到看了也会忘，等用到的时候再说吧。说实话，刚看完文档有点晕晕乎乎，毕竟纯理论的东西。于是在[codepen](http://codepen.io/)上写一些基础代码，实践是最好的老师，从代码里感受Vue的具体表现。可能是之前有React和Angular的经验，接受起Vue的代码书写方式比较容易，很多概念也比较好理解。说实话，总体感觉Vue就是做的很好，让用的人可以很傻瓜的用，这一点是React和Angular比不了的。

### 练手-TodoMVC

想要学习一个框架肯定要有Demo做出来才能去应用到实践中吧，因为之前Redux-cli开发项目所以对node和webpack也有一定的了解，所以直接用了Vue的Vue-cli来开发。
这是官方提供的脚本(yarn处理依赖比较好，所以也可以用它来安装）：
```bash
# 全局安装 vue-cli
$ npm install --global vue-cli
# 创建一个基于 webpack 模板的新项目
$ vue init webpack my-project
# 安装依赖
$ cd my-project
$ npm install
$ npm run dev
```
TodoMVC的样式也有个项目可以调用github上这个项目[todomvc-app-css](https://github.com/tastejs/todomvc-app-css)，这样只要关注Vue就好了。
build目录下webpack.base.conf.js的配置需要改下,extensions添加'.less', '.css', '.scss'，具体细节就不再描述了。
```js
resolve: {
    extensions: ['', '.js', '.vue', '.json', '.less', '.css', '.scss'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src')
    }
  },
```
### 实践-项目开发

近期刚好要做一个移动端的项目，实践是最好的学习。毕竟刚开始使用Vue框架，构建上并不轻车熟路，所以项目起始框架借鉴了项目[elm-vue2](https://github.com/bailicangdu/vue2-elm)（这里也要感谢作者bailicangdu的慷慨）。然后项目的UI Kit用的是饿了吗的前端[Element](http://element.eleme.io/#/zh-CN)。使用开始会遇到各种各样的问题，去github上找issue基本上是最快的办法了吧。然后API方便并没有使用Vue-source（当然，作者也不推荐），而是采用的axios来实现API的调用，所以也推荐使用axios。这里说一个小经历吧：有个头像上传的需求，后台需要post提交formData数据，前台把FormData数据配好以后提交的时候发现axios.post并没有把我的FormData携带过去，请求的payload一直为空。找了好久终于发现是这段代码的错误：
```js
axios.interceptors.request.use((config) => {
  if (config.data instanceof FormData) {
    
  }else{
    config.data = qs.stringify(config.data);
  }
  return config;
}, (error) => {
  console.log("错误的传参", error);
  return Promise.reject(error);
});
```
axios支持对请求参数进行序列化，而FormData进行序列化以后是空的（因为对象本来就是空的），所以这里当遇到FormData对象的时候不做序列化就不会出现参数丢失的问题。

### 结束

这就是我目前学习Vue的一点点经验了，希望能够对你有所帮助。
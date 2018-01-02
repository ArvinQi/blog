---
title: nuxtjs学习笔记
date: 2018-01-02 11:16:51
tags: [框架, vue, nuxtjs]
---
这是一个vue系的框架，核心还是vue，通过vue-server-renderer来实现了服务器端渲染，对webpack也做了封装，通过一个nuxt.config.js的配置文件来实现各种配置，当然对webpack有一定了解也可以通过build的属性中的extend方法来实现对webpack配置的扩展。
服务器端渲染，近几年虽然很热门，但是成熟的解决方案还是处于完善阶段，敢于挑战才能对服务器端渲染有所理解，可以参考php、jsp、.NET实现的web来理解服务器端渲染，不过我们是借助nodejs来实现自建server等，只需要javascript就可以实现。
<!-- more -->

### 准备
这里可以通过Vue-cli命令行工具直接创建nuxtjs项目，通过一些代码可以得到一个具有服务器端渲染特性的vue项目脚手架（这是一个很基础的，当然也可以直接使用[nuxtjs.org](https://zh.nuxtjs.org/)项目的源码作为基础架构）
```bash
$ vue init nuxt-community/starter-template <project-name>
```
### 开发
整体体验还好，基本都是使用的vue的语法。不过有些概念还是要理解，服务器端渲染跟客户端渲染的区别主要在哪里
#### 数据加载
可以在服务器端请求api获取数据，直接在客户端进行渲染（可以用过store中的nuxtServerInit方法或者单独组件里的asyncData方法），这样可以减少客户端请求的时间，也可以保证数据的安全性
#### store初始化
既然是服务器端渲染，也就是store第一次初始化，并不是在客户端，所以没有cookie或者localstorage等客户端存储方案，只能用过nodejs（express、hapi、koa等）实现缓存session，nuxtServerInit方法可以拿到请求的上下文，也就是nodejs服务器的上下文，这样就可以访问nodejs里面的session缓存了
#### 第三方库
尽量模块化引入，通过nuxt提供的plugins属性来实现模块导入，后期优化可以考虑均衡一下app.js和common.js的大小，以更好的提高渲染速度，官方也提供了分析模式nuxt build --analyze帮助后期优化

### 部署
页面如果只是静态的，可以考虑nuxt generate之后静态部署，如果有动态页面，就需要通过nodejs来部署，这个比较麻烦，可以上传源码，build，然后通过pm2来启动服务，如果发布比较严谨的，也可以考虑使用docker部署（ps：docker 的nodejs官方镜像600+太大了，我是采用了alpine3.7 build的镜像安装nodejs来实现发布的，发布包也就200+）

### 感触
整体考法体验还不错，就是部署和优化上比较伤脑筋，可能也是第一次部署nodejs吧，之前本地自己玩没考虑没考虑那么多，毕竟发布需要走流程，我们不能直接去操作服务器，所以最后搞了docker。另外，服务器端渲染有很多地方需要从想法的根本上变通，最初考虑使用react系的nextjs，种种原因吧，希望以后能有机会尝试。
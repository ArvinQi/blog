---
title: hapi学习笔记
date: 2017-07-12 14:59:55
tags: [框架, hapi]
---
说起这个框架也是因为要做D O，本来打算采用koa来实现的，后来发现了这个hapi(happy读音)更易上手，而且基本都是通过配置对象即可完成后台开发，数据库就采用了nosql的mongodb。
<!-- more -->

### Start
既然不是工具，安装就很简单了，采用yarn add hapi就可以在项目里使用了。推荐使用vscode调试开发，很是方便。

### Usage
官网上提供了很多现成的框架。我是结合了hapi-api和hapi-struct两个框架，实现的D O的框架，然后加入了mongoose实现连接mongodb，整体来说也没有什么太大的难度吧
登录注册都是hapi-struct提供的api代码，只不过我把他们集成到hapi-api里面了，然后自己实现了task模块，email使用了阿里的服务

### End
整体开发体验不错，目前就写这么多吧，后面会持续更新的。
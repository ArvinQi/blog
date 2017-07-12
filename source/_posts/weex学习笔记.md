---
title: weex学习笔记
date: 2017-07-12 14:00:39
tags: [框架, weex]
---
weex是2016年中旬从alibaba团队开源出来的，现在已经能够采用vuejs来开发weex跨平台app。通过weex开发了一个基于艾森豪威尔识字法则的todolist(取名DO)，代码github上已经上传了，有兴趣的可以下载看一看。
<!-- more -->
### Start
安装前提是已经安装node => 6.0,然后安装weex的toolkit

```bash
$ npm install -g weex-toolkit
```
这里如果想要做跨平台的app就需要使用了create创建weexpack项目而不是通过init创建vue项目
```bash
$ weex create new-project
```
ios app需要mac环境并且安装xcode并安装ios开发环境和cocoapods
android app需要jdk >=1.7和android sdk推荐安装Android studio
通过weex-toolkit添加platform，就可以build项目，不过最好还是用xcode和android studio来调试，weex自带的调试似乎并不好用。

### Usage
基础知识就是vue全家桶，结合weex官方提供的语法结构。

这里提一下，weex的语法并不是完全符合web规范的，比如没有select标签，只能使用官方手册提供的标签，样式css也有限制，比如padding，margin，border不支持缩写，background设置颜色值无效等，必须background-color才可以。

然后web跟ios也好，android也好都还是有很多差异的，这个要一点点慢慢调了。

遇到问题多去github看看issue，基本都能解决，比如之前遇到weex-picker的android闪退的问题，结果是因为android的配置引入的theme不对，改一下就好了。

### end
总的来说，weex虽然开发上都是坑，但是毕竟让web开发者能够开发原生app，这也是一个很大的进步了。
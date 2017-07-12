---
title: gulp学习笔记
date: 2017-06-07 15:43:29
tags: [工具, gulp]
---
最开始接触gulp是因为使用less和sass, 通过gulp来自动编译, 后来用gulp来做构建, 发现也挺方便的。纯jquery的老项目可以考虑使用fis快速实现版本化, 新项目不能采用webpack的模块化的, 可以考虑gulp来实现, js和css的压缩合并, 混淆等构建操作来实现版本化。下面会简单的介绍一下我是如何用gulp构建一个angular旧项目的。
<!-- more -->
### 首先简单的介绍一下gulp

和grunt类似, 但是比grunt更易使用。grunt配置很复杂, 当然大牛可以忽略我的屁话。总体来说, gulp是一个基于流的自动化构建工具。它是在node.js的基础上实现的, 所以可以很方便的安装：
```bash
$ npm install --global gulp
```
然后就可以愉快的使用了, 只要在根目录下创建一个gulpfile.js的文件就可以在里面使用gulp了, 像这样：
```js
var gulp = require('gulp');

gulp.task('default', function() {
  // 将你的默认的任务代码放在这
});
```
### 然后我们来看一个如何一步步实现项目构建的

构建需要借助gulp和nodejs的插件来实现, 有：browser-sync(自动刷新浏览器), gulp-load-plugins(gulp插件加载工具), run-sequence(顺序执行任务), gulp-rev-collector(gulp-rev的辅助插件), gulp-rev(为文件添加md5戳实现版本化), del(文件删除插件), gulp-concat(文件合并), gulp-rename(重命名), gulp-ngAnnotate(解决Angular代码压缩之后依赖注入的小问题), gulp-ngmin(pre-minify anuglarjs), gulp-strip-debug(去除js中的console和debugger), gulp-uglify(压缩js), gulp-minify-css(压缩css), gulp-replace(代码中的正则替换), gulp-less(less编译), gulp-autoprefixer(浏览器前缀添加), gulp-imagemin(图片压缩), 差不多就这些了。

## 首先copy图片,fonts,一些js等不能处理的文件

```js
gulp.task('copy:base', function (cb) {
    return gulp.src(option.copybase.src, { base: option.copybase.base })
        .pipe(revCollector())
        .pipe(plugins.rev())                                            //- 文件名加MD5后缀        
        .pipe(plugins.replace(/loadOption\['dev'\]/, "loadOption['demo']"))
        .pipe(gulp.dest(option.copybase.dest))                               //- 输出文件本地
        .pipe(plugins.rev.manifest('copybase.json'))                                   //- 生成一个rev-manifest.json
        .pipe(gulp.dest(option.release + '/rev'));                              //- 将 rev-manifest.json 保存到 rev 目录内

});
gulp.task('copy:js', function (cb) {
    return gulp.src(option.copyjs.src, { base: option.copyjs.base })
        .pipe(revCollector())
        .pipe(plugins.ngAnnotate())
        .pipe(plugins.ngmin({ dynamic: false }))//Pre-minify AngularJS apps with ngmin
        .pipe(plugins.stripDebug())//除去js代码中的console和debugger输出
        // .pipe(gulp.dest(option.minjs.dest))                               //- 输出文件本地
        .pipe(plugins.uglify({ outSourceMap: false }))    //压缩
        .pipe(plugins.rev())                                            //- 文件名加MD5后缀
        // .pipe(plugins.rename({suffix: '.min'}))   //rename压缩后的文件名
        .pipe(gulp.dest(option.copyjs.dest))                               //- 输出文件本地
        .pipe(plugins.rev.manifest('copyjs.json'))                                   //- 生成一个rev-manifest.json
        .pipe(gulp.dest(option.release + '/rev'));                              //- 将 rev-manifest.json 保存到 rev 目录内

});
gulp.task('justcopy', function (cb) {
    return gulp.src(option.justcopy.src, { base: option.justcopy.base })
        .pipe(gulp.dest(option.justcopy.dest));
});
```
## 然后压缩合并js

```js
gulp.task('minjs', function () {
    return gulp.src(option.minjs.src)      //需要操作的文件
        .pipe(plugins.concat('main.js'))    //合并所有js到main.js
        .pipe(gulp.dest(option.minjs.dest))       //输出到文件夹
        .pipe(plugins.rename({ suffix: '.min' }))   //rename压缩后的文件名
        .pipe(plugins.ngAnnotate())
        .pipe(plugins.ngmin({ dynamic: false }))//Pre-minify AngularJS apps with ngmin
        .pipe(plugins.stripDebug())//除去js代码中的console和debugger输出
        .pipe(gulp.dest(option.minjs.dest))                               //- 输出文件本地
        .pipe(plugins.uglify({ outSourceMap: false }))    //压缩
        .pipe(plugins.rev())
        .pipe(gulp.dest(option.minjs.dest))                               //- 输出文件本地
        .pipe(plugins.rev.manifest('minjs.json'))                                   //- 生成一个rev-manifest.json
        .pipe(gulp.dest(option.release + '/rev'));  //输出
});
```
## 然后合并css
```js
gulp.task('mincss', function (cb) {                                //- 创建一个名为 concat 的 task
    return gulp.src(option.mincss.src)    //- 需要处理的css文件，放到一个字符串数组里
        .pipe(plugins.concat('main.min.css'))                            //- 合并后的文件名
        .pipe(plugins.minifyCss())                                      //- 压缩处理成一行
        .pipe(gulp.dest(option.mincss.dest))                               //- 输出文件本地
        .pipe(plugins.rev())                                            //- 文件名加MD5后缀
        .pipe(gulp.dest(option.mincss.dest))                               //- 输出文件本地
        .pipe(plugins.rev.manifest('mincss.json'))                                   //- 生成一个rev-manifest.json
        .pipe(gulp.dest(option.release + '/rev'));                              //- 将 rev-manifest.json 保存到 rev 目录内

});
```
## 最后执行文件替换，通过gulp-rev插件生成的json文件记录了，文件名对应的加了MD5后缀的文件名，然后把相应文件里的引入路径改成带MD5的路径就可以了
```js
gulp.task('rev', function (cb) {
    return gulp.src(option.rev.src)   //- 读取 rev-manifest.json 文件以及需要进行css名替换的文件
        .pipe(revCollector())                       //- 执行文件内js,img,css名的替换
        .pipe(gulp.dest(option.release));           //- 替换后的文件输出的目录

});
```
## 这里有个顺序执行的主要任务, 这样gulp deploy就会执行打包程序，生成需要的的发布包dist
```js
gulp.task('deploy', function (cb) {
    runSequence('clean', 'copy:images', 'minjs', 'mincss',
        'fonts', 'copy:css', 'copy:json',
        'copy:js', 'copy:base', 'rev', cb);
});
```
## 另外通过gulp启动了一个开发服务器，每次文件改变执行相应的程序。
```js
//开发服务器
gulp.task('dev', function () {
    browserSync.init({
        // server: {
        //     baseDir: './'
        // }
        proxy: '127.0.0.1:7000',
        notify: true,
        ghostMode: {
            clicks: true,
            location: true,
            forms: false,
            scroll: true
        }
    });
    var root = 'dist/';
    var complieLess = function (path) {
        gulp.src([path])
            .pipe(plugins.sourcemaps.init())
            .pipe(plugins.less())
            .pipe(plugins.autoprefixer({
                browsers: ['last 2 versions'],
                cascade: false
            }))
            .pipe(plugins.sourcemaps.write('../maps'))
            .pipe(gulp.dest('./dist/assets/css'))
            .pipe(plugins.notify({
                message: path + ' complie finished!'
            }));
    }
    complieLess(root + 'assets/less/**/*.less');
    gulp.watch([root + 'assets/less/**/*.less']).on('change', function (event) {
        complieLess(event.path);
    });
    gulp.watch([root + '**/*.css']).on('change', function (event) {
        gulp.src(event.path)
            .pipe(browserSync.reload({ stream: true }));
    });
    gulp.watch([root + '**/*.html']).on('change', function (event) {
        gulp.src(event.path)
            .pipe(browserSync.reload({ stream: true }));
    });
    gulp.watch([root + '**/*.js', '!node_modules/**/*']).on('change', function (event) {
        gulp.src(event.path)
            .pipe(browserSync.reload({ stream: true }));
    });
});
```
到这里gulp构建angular项目就结束了。

### 事实证明

只要肯折腾, 没有完不成的事。
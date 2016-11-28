---
title: 前端构建工具Gulp
tag:
- Gulp
categories:
- 前端构建
---
### 安装
全局安装

```
npm install gulp -g
```

项目开发安装

```
npm install gulp -dev --save
```

插件安装

```
npm install gulp-concat gulp-minify-html gulp-minify-css gulp-rename  -dev --save
```
### 配置文件

在项目根目录下需要创建一个名为gulpfile.js的文件,例如

```
var gulp       = require('gulp');
var minifyHTML = require('gulp-minify-html');
var concat     = require('gulp-concat');
var minifyCSS  = require('gulp-minify-css');
var rename     = require('gulp-rename');
var minifyJS   = require('gulp-jsmin');
var babel      = require('gulp-babel');
var connect    = require('gulp-connect');
var open       = require('gulp-open');

gulp.task('html',function(){
    gulp.src('./src/html/*.html')
        .pipe(minifyHTML())
        .pipe(gulp.dest('./'))
});

gulp.task('css',function(){
    gulp.src('./src/css/*.css')
        .pipe(concat('style.css'))
        .pipe((minifyCSS()))
        .pipe(rename({ suffix : '.min'}))
        .pipe(gulp.dest('./assets/'))
});

gulp.task('js',function(){
    gulp.src('./src/js/*.js')
        .pipe(concat('main.js'))
        .pipe(rename({ suffix : '.min'}))
        .pipe(gulp.dest('./assets/'))
});

gulp.task('babel',function(){
    gulp.src('./src/exercise/**/*.js')
        .pipe(babel())
        .pipe(gulp.dest('./exercise/'))
});

gulp.task('watch',function(){
    gulp.watch('./src/html/*.html',['html']);
    gulp.watch('./src/css/*.css',['css']);
    gulp.watch('./src/js/*.js',['js']);
    gulp.watch('./src/exercise/**/*.js',['babel']);
});

gulp.task('connect',function(){
    connect.server({
        root:'./',
        ip:'127.0.0.0',
        port:'2000',
        livereload:true
    })
});


gulp.task('default',['html','css','js','babel','watch','connect'],function(){
    gulp.src(__filename)
        .pipe(open({uri:'http://localhost:2000'}))
});
```

### API

#### gulp.src(globs)
输出符合glob匹配模式的文件，返回Vinyl files的stream,可以pipe到别的文件或插件中。
#### gulp.dest(path)
stream能被pipe进来写入一个文件，文件的上级路径在path下，path下文件的路径根据base路径和相对于base的路径计算，如果文件夹不存在会自动创建。
#### gulp.task(name,function)
定义一个任务，接收两个参数。第一个参数是任务的名字，第二个参数是要做的事情。如：

```
gulp.task('html',function(){
    gulp.src('./src/html/*.html')
        .pipe(minifyHTML())
        .pipe(gulp.dest('./'))
});
```
任务的名字叫html,要做的事情就是把./src/html/目录下已.html结尾的文件进行压缩后，放到./目录下。
如果要执行多个任务，可以这样定义：

```
gulp.task('dev',['html','css','js','babel','watch']，function(){});
```
任务dev就是执行任务html，css，js，babel，watch
css，js，babel，watch都要像任务html一样被定义

默认情况下，这些task会以最大的并发数执行
#### gulp.watch(glob,tasks)
监听文件，当文件改动时，做一些事情。

### 常用插件

- [gulp-open](https://github.com/stevelacy/gulp-open)    --打开一个文件
- [gulp-rename](https://github.com/hparra/gulp-rename)  --重命名文件
- [gulp-replace](https://github.com/lazd/gulp-replace) --替换路径
- [gulp-rimraf](https://github.com/robrich/gulp-rimraf) --删除目录
- [gulp-minify-html](https://github.com/sanfords/gulp-minify-html) --压缩html文件
- [gulp-minify-css](https://github.com/nfroidure/gulp-minify-css) --压缩css文件
- [gulp-jsmin](https://github.com/chilijung/gulp-jsmin) --压缩js文件
- [gulp-uglify](https://github.com/terinjokes/gulp-uglify) --压缩javascript文件
- [gulp-concat](https://github.com/contra/gulp-concat)  --合并文件
- [gulp-babel](https://github.com/babel/gulp-babel) --把es6编译成es5
- [gulp-less](https://github.com/plus3network/gulp-less) --将less文件编译成css文件
- [gulp-util](https://github.com/gulpjs/gulp-util) --gulp插件的工具方法

### Gulp插件开发
待研究。。。


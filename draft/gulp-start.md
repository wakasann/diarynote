gulp 项目初始配置笔记


## Node环境

通过 [node.js](https://nodejs.org/en/) 网站下载了安装包进行安装 node.js, npm也会一起安装

```
node --version # 查看node.js版本
npm --version #查看npm版本
npx --version 
```

通过命令配置 npm 淘宝镜像

```
npm config set registry https://registry.npm.taobao.org
npm config get registry #验证命令
npm config list #列出当前npm的配置列表
```

## 定位到项目目录

通过命令行工具进入到 项目的工作目录，如 windows系统下，我的项目目录是`D:/workspace/nodejs/gulpstart`下

cmd 命令是:

```
cd D:
cd workspace/nodejs/gulpstart
dir # 列出当前目录的文件
```

成功进入到项目目录后，我们开始安装 gulp。

## 安装gulp

> NPM是基于命令行的node包管理工具，它可以将node的程序模块安装到项目中，在它的官网中可以查看和搜索所有可用的程序模块。

如果在项目目录下没有 名为`package.json` 文件，可通过

```
npm init
```
命令来初始化一个 `package.json` 文件的信息，如已有`package.json`文件，可略过`npm init` 这个命令。

安装 gulp，作为开发时依赖项:

```
npm install --global gulp-cli # 安装
gulp -v #查看安装的 gulp版本
npm install —-save-dev gulp
```

> 上面命令中的`--save-dev`的包名来更新`package.json`文件中，更新`devDependencies`键的值，以表明项目需要依赖gulp。

> Dependencies可以向其他参与项目的人指明项目在开发环境和生产环境中的node模块依懒关系，想要更加深入的了解它可以看看[Specifying dependencies and devDependencies in a package.json file](https://docs.npmjs.com/specifying-dependencies-and-devdependencies-in-a-package-json-file)文档。

## 创建Gulpfile文件，运行gulp

安装依赖

```
npm install gulp-jshint node-sass gulp-sass gulp-concat gulp-uglify gulp-rename --save-dev 
```

安装的插件说明:

* gulp-jshint : js语法检查
* gulp-sass :  sass
* gulp-concat :  文件合并(Concatenates files)
* gulp-uglify : js 文件压缩(Minify JavaScript with UglifyJS3.)
* gulp-rename : 重命名文件

如果需要其它的gulp 插件，可到 [npmjs](https://www.npmjs.com/) 查找`gulp-`开头的包(package),如:  `gulp-less` less 


**新建gulpfile文件**

现在，需要使用的gulp组件都安装完毕，我们需要新建一个gulpfile文件以指定gulp需要为我们完成什么任务。

gulp 有五个方法： task，run，watch，src，和dest，在项目根目录新建一个名为`gulpfile.js` （或者首字母大写 `Gulpfile.js`，就像 Makefile 一样命名） 的文件,

* task: 每个 gulp 任务（task）都是一个异步的 JavaScript 函数
* run:  执行任务(task)
* watch:  gulp api 中的 `watch()` 方法利用文件系统的监控程序（file system watcher）将 globs 与 任务（task） 进行关联。
* src和dest gulp 暴露了 src() 和 dest() 方法用于处理计算机上存放的文件

**说明:** 上面内容都是抄自 [gulp 中文网文档](https://www.gulpjs.com.cn/docs/getting-started/quick-start/) 网站

把下面的代码粘贴进去：

```
// 引入 gulp
var gulp = require('gulp'); 

// 引入组件
var jshint = require('gulp-jshint');
var sass = require('gulp-sass');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var rename = require('gulp-rename');

sass.compiler = require('node-sass');

// 检查js脚本
// Link任务会检查js/目录下得js文件有没有报错或警告。
gulp.task('lint', function() {
    gulp.src('./js/*.js')
        .pipe(jshint())
        .pipe(jshint.reporter('default'));
});

// 编译Sass
//Sass任务会编译scss/目录下的scss文件，并把编译完成的css文件保存到/css目录中。
gulp.task('sass', function() {
    gulp.src('./sass/*.scss')
        .pipe(sass().on('error', sass.logError))
        .pipe(gulp.dest('./css'));
});
gulp.task('sass:watch', function () {
  gulp.watch('./sass/**/*.scss', ['sass']);
});

// 合并，压缩文件
//scripts任务会合并js/目录下得所有得js文件并输出到dist/目录，然后gulp会重命名、压缩合并的文件，也输出到dist/目录。
gulp.task('scripts', function() {
    gulp.src('./js/*.js')
        .pipe(concat('all.js'))
        .pipe(gulp.dest('./dist'))
        .pipe(rename('all.min.js'))
        .pipe(uglify())
        .pipe(gulp.dest('./dist'));
});

// 默认任务
gulp.task('default', function(){
    gulp.run('lint', 'sass', 'scripts');

    // 监听文件变化
    gulp.watch('./js/*.js', function(){
        gulp.run('lint', 'sass', 'scripts');
    });
});
//这时，我们创建了一个基于其他任务的default任务。使用.run()方法关联和运行我们上面定义的任务，使用.watch()方法去监听指定目录的文件变化，当有文件变化时，会运行回调定义的其他任务。
```



上面的gulp 配置可按照实际的情况进行修改，添加，删除。



在项目目录运行命令:

```
gulp
```

执行 `default` 任务(task)

```
gulp default
gulp sass
```



## References

1.[前端构建工具gulp入门教程](https://segmentfault.com/a/1190000000372547)
2.[gulp中文网](https://www.gulpjs.com.cn/)
3.[npm使用国内淘宝镜像的方法](https://www.cnblogs.com/bonelee/p/9995550.html)


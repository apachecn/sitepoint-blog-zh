# 如何迁移到 Gulp.js 4.0

> 原文：<https://www.sitepoint.com/how-to-migrate-to-gulp-4/>

尽管有来自 [webpack](https://webpack.js.org/) 和 [Parcel](https://parceljs.org/) 的竞争， [Gulp.js](https://gulpjs.com/) 仍然是最受欢迎的 JavaScript 任务运行程序之一。Gulp.js 是使用代码配置的，这使它成为一个多用途的通用选项。除了常见的传输文件、捆绑和实时重新加载，Gulp.js 还可以分析数据库、呈现静态站点、推送 Git 提交，以及用一个命令发布 Slack 消息。了解如何迁移到 Gulp.js 4.0。

关于 Gulp 的介绍，请看下面的内容:

*   [gulp . js 简介](https://www.sitepoint.com/introduction-gulp-js/)
*   [如何使用 Gulp.js 自动化你的 CSS 任务](https://www.sitepoint.com/automate-css-tasks-gulp/)
*   [使用 Gulp 更快地开发 WordPress 主题](https://www.sitepoint.com/fast-gulp-wordpress-theme-development-workflow/)

## Gulp.js 4.0

Gulp.js 3.x 已经默认使用了五年左右。直到最近，`npm install gulp`才安装了 3 . 9 . 1——上面教程中提到的版本。

Gulp.js 4.0 在这段时间里一直可用，但必须用`npm install gulp@next`显式安装。这部分是由于正在进行的开发，因为 Gulp.js 4 `gulpfile.js`配置文件与那些为版本 3 开发的不兼容。

2018 年 12 月 10 日， [Gulp.js 4.0 宣布默认](https://medium.com/gulpjs/version-4-now-default-92c6cd4beb45)，[发布到 npm](https://www.npmjs.com/package/gulp) 。任何在新项目中使用`npm install gulp`的人都会收到版本 4。

## 有必要迁移到 Gulp.js 4 吗？

不会。Gulp.js 3 已被弃用，不太可能收到进一步的更新，但仍然可以使用。现有项目不会更新，除非在`package.json`的`dependencies`部分明确更改版本。例如:

```
"dependencies": {
  "gulp": "^4.0.0"
} 
```

您也可以在新项目中安装 Gulp.js 3，使用:

```
npm install gulp@^3.9.1 
```

如果您有一个特别复杂的关键任务构建系统，最好坚持使用 Gulp.js 3.x。

然而，现有的 Gulp.js 插件应该是兼容的，大多数`gulpfile.js`配置可以在一两个小时内迁移。升级有几个好处，这在本教程中将变得显而易见。

## 升级至 Gulp.js 4.0

如上所示更新您的`package.json`依赖项，然后运行`npm install`进行升级。您还可以使用`npm i gulp-cli -g`更新命令行界面，尽管在撰写本文时这一点还没有改变。

要检查安装，请在命令行输入`gulp -v`:

```
$ gulp -v
[15:15:04] CLI version 2.0.1
[15:15:04] Local version 4.0.0 
```

## 迁移 gulpfile.js

运行任何任务现在都有可能引发可怕的错误。例如:

```
AssertionError [ERR_ASSERTION]: Task function must be specified
  at Gulp.set [as _setTask] (/node_modules/undertaker/lib/set-task.js:10:3)
  at Gulp.task (/node_modules/undertaker/lib/task.js:13:8)
  at /gulpfile.js:102:8
  at Object.<anonymous> (/gulpfile.js:146:3)
  at Module._compile (internal/modules/cjs/loader.js:688:30)
  at Object.Module._extensions..js (internal/modules/cjs/loader.js:699:10)
  at Module.load (internal/modules/cjs/loader.js:598:32)
  at tryModuleLoad (internal/modules/cjs/loader.js:537:12)
  at Function.Module._load (internal/modules/cjs/loader.js:529:3)
  at Module.require (internal/modules/cjs/loader.js:636:17)
  at require (internal/modules/cjs/helpers.js:20:18)
  at execute (/gulp-cli/lib/versioned/^4.0.0/index.js:36:18)
  at Liftoff.handleArguments (/gulp-cli/index.js:175:63)
  at Liftoff.execute (/gulp-cli/node_modules/liftoff/index.js:203:12)
  at module.exports (/gulp-cli/node_modules/flagged-respawn/index.js:51:3)
  at Liftoff.<anonymous> (/gulp-cli/node_modules/liftoff/index.js:195:5) 
```

这令人望而生畏，但是您可以忽略除了第一个引用`gulpfile.js`之外的所有内容，它显示了遇到错误的那一行(本例中为 102)。

幸运的是，这些错误大多是由同一类型的问题引起的。下面几节以 [CSS 任务教程](https://www.sitepoint.com/automate-css-tasks-gulp/)代码为例。代码[在 GitHub](https://github.com/craigbuckler/gulp-css/) 上可用，并提供了[原 Gulp.js 3 gulpfile.js](https://github.com/craigbuckler/gulp-css/blob/master/gulp3file.js) 和[迁移后的 Gulp.js 4 等价物](https://github.com/craigbuckler/gulp-css/blob/master/gulpfile.js)。

## 将任务数组转换为 series()调用

Gulp.js 3 允许指定同步任务的数组。这通常在`watch`事件被触发或任务有依赖关系时使用。例如，在`css`任务之前运行`images`任务:

```
gulp.task('css', ['images'], () =>

  gulp.src(cssConfig.src)
    // .other plugins
    .pipe(gulp.dest(cssConfig.build));

); 
```

Gulp.js 4.0 引入了`series()`和`parallel()`方法来组合任务:

*   [`series(...)`](https://gulpjs.com/docs/en/api/series) 按照指定的顺序一次运行一个任务，并且
*   [`parallel(...)`](https://gulpjs.com/docs/en/api/parallel) 以任意顺序同时运行任务。

复杂的任务可以嵌套到任何层次——这在 Gulp.js 3 中是很难实现的。例如，并行运行任务`a`和`b`，然后，一旦两者都完成，并行运行任务`c`和`d`:

```
gulp.series( gulp.parallel(a, b), gulp.parallel(c, d) ) 
```

通过将数组改为一个`series()`方法调用，上面的`css`任务可以迁移到 Gulp.js 4:

```
gulp.task('css', gulp.series('images', () =>

  gulp.src(cssConfig.src)
    // .other plugins
    .pipe(gulp.dest(cssConfig.build));

)); // remember the extra closing bracket! 
```

## 异步完成

Gulp.js 3 允许同步函数，但是这可能会引入难以调试的错误。考虑不返回`gulp`流值的任务。例如，一个`clean`任务删除了`build`文件夹:

```
const
  gulp = require('gulp'),
  del = require('del'),
  dir = {
    src         : 'src/',
    build       : 'build/'
  };

gulp.task('clean', () => {

  del([ dir.build ]);

}); 
```

Gulp.js 4.0 抛出警告，因为它需要知道任务何时完成，以便管理`series()`和`parallel()`序列:

```
[15:57:27] Using gulpfile gulpfile.js
[15:57:27] Starting 'clean'...
[15:57:27] The following tasks did not complete: clean
[15:57:27] Did you forget to signal async completion? 
```

为了解决这个问题，Gulp.js 4 将接受一个返回的承诺*(由 [`del`包](https://www.npmjs.com/package/del)支持)*:

```
gulp.task('clean', () => {

  return del([ dir.build ]);

});

// or use the simpler implicit return:
// gulp.task('clean', () => del([ dir.build ]) ); 
```

或者，传递一个在完成时执行的回调函数 *( `del`也提供了一个同步的`sync()`方法)*:

```
gulp.task('clean', (done) => {

  del.sync([ dir.build ]);
  done();

}); 
```

下面是一个更复杂的带有观察任务的 Gulp.js 3 示例:

```
gulp.task('default', ['css', 'server'], () => {

  // image changes
  gulp.watch(imgConfig.src, ['images']);

  // CSS changes
  gulp.watch(cssConfig.watch, ['css']);

}); 
```

这些可以迁移到 Gulp.js 4 `series()`方法和一个`done()`回调:

```
gulp.task('default', gulp.series('css', 'server', (done) => {

  // image changes
  gulp.watch(imgConfig.src, gulp.series('images'));

  // CSS changes
  gulp.watch(cssConfig.watch, gulp.series('css'));

  done();

})); 
```

## 额外提示:将任务方法转换为 ES6 模块

一旦任务数组被转换为`series()`调用并且异步函数发出完成信号，大多数`gulpfile.js`配置将在 Gulp.js 4.0 中工作。

虽然仍然支持`task()`定义方法，但是较新的 ES6 模块`exports`模式提供了几个好处:

1.  可以定义私有任务，这些任务可以在`gulpfile.js`中调用，但不能从`gulp`命令中调用。
2.  函数可以通过引用而不是字符串名来传递，这样语法错误可以被编辑器突出显示。
3.  可以使用任意数量的任务名称来引用同一个函数。
4.  在`series()`和/或`parallel()`中定义复杂的依赖关系更容易。

接以下 Gulp.js 3 `images`和`css`任务:

```
gulp.task('images', () =>

  gulp.src(imgConfig.src)
    // .other plugins
    .pipe(gulp.dest(imgConfig.build))

);

gulp.task('css', ['images'], () =>

  gulp.src(cssConfig.src)
    // .other plugins
    .pipe(gulp.dest(cssConfig.build))

); 
```

这些可以转换成使用 Gulp.js 4 模块模式:

```
function images() {

  return gulp.src(imgConfig.src)
    // .other plugins
    .pipe(gulp.dest(imgConfig.build));

}
exports.images = images;
exports.pics = images;

function css() {

  return gulp.src(cssConfig.src)
    // .other plugins
    .pipe(gulp.dest(cssConfig.build));

}
exports.css = gulp.series(images, css);
exports.styles = exports.css; 
```

*注意:`return`语句必须添加，因为带有隐式返回的 ES6 arrow `=>`函数已经被更改为标准函数定义。*

在这个 Gulp.js 4 示例中:

*   可以使用`gulp images`或`gulp pics`来运行`images()`任务
*   `gulp css`或`gulp styles`将运行`images()`，随后是`css()`。

设置`exports.default`以定义在没有特定任务的情况下从命令行执行`gulp`时运行的默认任务。

## 天啊

用于优化图像、编译 Sass、缩小 CSS 和实时重新加载变化的 [CSS 任务示例](https://www.sitepoint.com/automate-css-tasks-gulp/)只花了不到一个小时就完成了转换。检查 GitHub 上的[代码，查看:](https://github.com/craigbuckler/gulp-css/)

*   [原 Gulp.js 3 gulpfile.js](https://github.com/craigbuckler/gulp-css/blob/master/gulp3file.js) ，以及
*   [迁移 Gulp.js 4 gulpfile.js](https://github.com/craigbuckler/gulp-css/blob/master/gulpfile.js) 。

这需要一段时间才能(恰当地)实现，但是 Gulp.js 4 提供了定义任务的机会，这在版本 3 中是不切实际的。更新软件看起来像是浪费开发工作，但是你会得到一组更快、更健壮的任务，从长远来看，这将节省时间。

## 分享这篇文章
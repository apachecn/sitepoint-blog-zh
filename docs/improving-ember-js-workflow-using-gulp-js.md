# 使用 Gulp.js 改进您的 Ember.js 工作流

> 原文：<https://www.sitepoint.com/improving-ember-js-workflow-using-gulp-js/>

我非常支持 Ember.js 框架。我在网上看过很多 Ember 教程，但是大多数都没有解释如何设置一个构建系统来使用它。有鉴于此，我决定由自己来解释这部分工作流程。自从它的到来，Gulp.js 已经成为 JavaScript 任务运行者讨论的热门话题。当提到 JavaScript 项目时，Gulp.js 会出现在讨论中，这并非巧合。所以，这篇文章将展示如何将 Gulp 用于 Ember 项目。

我将在这篇文章中做一些假设。我假设您已经对 Gulp.js 的工作原理有了基本的了解，并且您之前已经自己建立了一个 Gulp.js 项目。如果没有，请访问 SitePoint 的[对 Gulp.js 的介绍](https://www.sitepoint.com/introduction-gulp-js/)进行复习。本文的其余部分将教您如何使用 Ember 项目创建和配置常见的 Gulp.js 任务。

## 典型的 Ember.js 工作流

我参与过几个 Ember.js 项目，我注意到它们之间有一些共同的需求。这些需求包括操作 SCSS、CSS、JavaScript 和手柄代码的需要。下面是要求和对每个要求的简要说明。

### SCSS 汇编

这涉及到 SCSS 代码到 CSS 的转换。

### JavaScript 和 CSS 小型化

缩小是通过删除不必要的空白来减小文件大小的过程。对于 CSS，这通常是在将 SCSS 代码转换成 CSS 之后完成的。

### JavaScript 和 CSS 串联

串联是将许多文件合并成一个文件的过程。这样做通常是为了减少对服务器的 HTTP 请求数量。更多的文件意味着更多的 HTTP 请求，这会导致更长的下载时间。当连接在一起时，您只需要一个 HTTP 请求。这在通过慢速网络连接(如移动设备)提供文件时尤其有用。

### 文件/文件夹监视

缩小、连接和编译等任务可以手动运行。然而，它们是重复的任务，很快就会变得令人厌倦和乏味。使用 Gulp.js 观察器任务，您可以设置一个文件观察器来观察所需文件的变化。当它检测到变化时，它将运行一个或多个任务来响应该变化。

### 车把汇编

Handlebars 是 Ember.js 的默认模板语言，但是，浏览器不能解析 Handlebars 代码。因此，我们需要一种将手柄代码转换成 HTML 的方法。这就是车把编译发挥作用的地方。首先，把手代码被转换成一个 JavaScript 函数。然后，当 Ember 运行时需要将适当的 HTML 附加到 DOM 时，该函数开始运行。

### JavaScript 丑化

丑化 JavaScript 是一个两步的过程。第一步是通过缩小消除空白。第二步是尽可能将 JavaScript 函数名和变量简化为单个字符。原因是较短的变量名需要较少的字节，从而导致更快的下载。

## Gulp.js 插件细分

这一部分将强调我们需要的插件，并描述它们的功能。

### `gulp`

这是安装 Gulp.js 的基础插件。

### `gulp-compass`

这个插件将 SCSS 代码编译成 CSS。要使用它，你必须安装[红宝石](https://www.ruby-lang.org/en/)和[罗盘宝石](https://rubygems.org/gems/compass)。

### `gulp-uglify`

这个插件丑化了 JavaScript 代码。您可以设置一个选项，在遇到问题时不更改函数名。

### `gulp-watch`

这个插件可以让你的项目观察一个或多个文件的变化。

### `gulp-concat`

这个插件可以让你把几个 CSS 或者 JavaScript 文件合并成一个。当然，这些文件必须是相同的类型。

### `gulp-ember-handlebars`

这个插件可以让你把把手转换成 JavaScript。

## 安装插件

首先创建一个包含空 JavaScript 对象`{}`的`package.json`文件。接下来，我们将安装前面列出的插件。使用终端，导航到项目的根目录。在您的终端中使用以下命令安装并添加上述插件作为依赖项。

```
npm install gulp -g
```

这将全局安装 gulp。接下来，使用以下命令将插件保存到本地项目中:

```
npm install gulp --save-dev
```

命令的`--save-dev`部分将插件作为依赖项添加到您的`package.json`中。移除它只会安装插件，但不会将其添加到您的文件中。

对其他插件重复第二个命令。在每种情况下，用您希望安装的插件的名称替换`gulp`。比如`gulp-compass`、`gulp-concat`等。这样做之后，看看您的`package.json`，您应该会看到这样的内容:

```
{
  "devDependencies": {
    "gulp": "^3.8.0",
    "gulp-compass": "^1.1.9",
    "gulp-concat": "^2.2.0",
    "gulp-ember-handlebars": "^0.6.0",
    "gulp-uglify": "^0.3.0",
    "gulp-watch": "^0.6.5"
  }
}
```

## 需要插件

在与您的`package.json`相同的目录中创建一个`gulpfile.js`。在它里面，添加下面的代码，导入插件。查看变量名应该是一个很好的指示器，可以看出它们代表了哪个插件。

```
var gulp = require('gulp'),
  compass = require('gulp-compass'),
  watch = require('gulp-watch'),
  handlebars = require('gulp-ember-handlebars'),
  uglify = require('gulp-uglify'),
  concat = require('gulp-concat');
```

## 为插件配置任务

在本节中，我们将通过使用各种组合的插件来配置任务。在适用的地方，我会说明一个任务是否使用了一个以上的插件。注意，不同任务的所有文件路径都是相对于`gulpfile.js`文件的。

### CSS 任务

这项任务完成了三件事。它使用了三个插件，`compass`、`concat`和`gulp`。它将 SCSS 文件编译成 CSS，连接它们，并将结果输出到您选择的文件中。

```
gulp.task('css', function() {
  return gulp.src('scss/*.scss')
    .pipe(compass({ sass: 'scss' }))
    .pipe(concat('main.min.css'))
    .pipe(gulp.dest('dist/css'));
});
```

### 模板任务

这个任务使用了`handlebars`、`concat`和`gulp`插件来完成两件事。它将一系列手柄文件作为输入，将它们编译成 JavaScript，然后连接成一个文件。然后，它将输出文件存储到您想要的位置。

```
gulp.task('templates', function() {
  gulp.src(['js/templates/**/*.hbs'])
    .pipe(handlebars({
      outputType: 'browser',
      namespace: 'Ember.TEMPLATES'
    }))
    .pipe(concat('templates.js'))
    .pipe(gulp.dest('js/'));
});
```

### 脚本任务

这个任务做两件事，并使用三个插件，`gulp`、`concat`和`uglify`。它接受一个 JavaScript 文件列表作为输入。然后，它对内容进行丑化，将它们连接成一个文件，并存储在所需的位置。

```
gulp.task('scripts', function() {
  var scriptSrc = [
    'js/vendor/jquery-1.10.2.js',
    'js/vendor/jquery-ui.custom.min.js',
    'js/vendor/moment.min.js',
    'js/vendor/handlebars.runtime-v1.3.0.js',
    'js/vendor/ember-1.3.2.js',
    'js/vendor/ember-data.js',
    'js/vendor/local-storage-adapter.js',
    'js/helpers.js',
    'js/main.js',
    'js/templates.js',
    'js/components.js',
    'js/models/*.js',
    'js/controllers/*.js',
    'js/router.js',
    'js/views/*.js',
    'js/fixtures.js',
    'js/routes/*.js'
  ];

  return gulp.src(scriptSrc)
    .pipe(uglify({ mangle: false }))
    .pipe(concat('main.min.js'))
    .pipe(gulp.dest('dist/js'));
});
```

### 文件监视器任务

这将创建一个监视任务，其中包含几个子任务。子任务各自观察几种文件类型。当任何被监视的文件改变时，相应的任务被触发以响应该改变。

```
gulp.task('watch', function() {
  //watches SCSS files for changes
  gulp.watch('scss/*.scss', ['css']);

  //watches handlebars files for changes
  gulp.watch('js/templates/**/*.hbs', ['templates']);

  //watches JavaScript files for changes
  gulp.watch('js/**/*.js', ['scripts']);
});
```

## 如何使用任务

为了展示如何使用上述任务，我将向您展示我在开发 Ember.js 应用程序时使用它们的两种方式。

### 开发用途

在开发期间，我需要能够编译 SCSS，编译手柄，连接 CSS，连接 JavaScript，并将其丑化。需要观察文件的变化。因此，我设置了下面的任务在命令行上运行。

```
gulp.task('default', ['css', 'templates', 'scripts', 'watch']);
```

在这里，我为 Gulp.js 设置了一个默认任务。由于`watch`任务的依赖性，这将启动一个后台任务。每当一个文件被改变时，`watch`任务中的任务(`css`、`templates`和`scripts`)就会运行。

### 生产用途

在生产过程中，当我准备好发布应用程序时，我需要默认任务的依赖任务，除了监视任务。这是因为我不会对文件做任何更改，并且我只需要构建工具运行一次。这是使用命令`gulp production`运行的，如下所示:

```
gulp.task('production', ['css', 'templates', 'scripts']);
```

## 结论

我们关于如何在 Ember.js 项目中使用 Gulp 的教程到此结束。我们已经定义了几项任务来完成一系列常见的工作。请注意，你可以混合搭配工作。例如，您可以在开发过程中关闭 JavaScript 代码的丑化，只在生产任务中打开它。一种方法是定义两个独立的任务，一个用于开发，一个用于生产。此外，这些任务只针对我的项目。我强烈建议您查看每个插件的文档页面。这些有更深入的信息，可以进一步定制它们来满足您的需求。一个好的起点是 Gulp.js [插件页面](http://gulpjs.com/plugins/)。

我希望这是有用的，和往常一样，如果您有任何问题或对以上几点有任何贡献，请让我们知道。

## 分享这篇文章
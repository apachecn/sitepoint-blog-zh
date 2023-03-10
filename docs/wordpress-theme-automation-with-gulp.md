# WordPress 主题自动化与吞咽

> 原文：<https://www.sitepoint.com/wordpress-theme-automation-with-gulp/>

随着网站代码变得越来越复杂，只求优化的重复步骤变得越来越常见，应该有一个更好、更有效的开发过程。

在本教程中，我将介绍 [Gulp](http://gulpjs.com/) ，以及如何将其与 [WordPress](https://wordpress.org/) theming 集成，通过构建一个自动化的工作流程来自动化和增强主题开发过程。

## 为什么您需要自动化开发工作流程

工作流优化对于您的开发过程来说是非常有益的。以下是尝试一下的一些理由:

*   它删除了所有那些重复和无聊的任务，用定制工具来代替它们。
*   为做其他重要的核心开发工作节省了大量时间。
*   它有助于优化您的网站的性能，缩小和优化所有资产。

## 你需要什么

*   WordPress 安装在你的开发机器上。
*   [Node.js](https://nodejs.org/en/) 和 [npm](https://www.npmjs.com/) 安装完毕。
*   命令行基础知识。

## 吞咽介绍

Gulp 是一个 JavaScript 任务运行器，它将帮助自动化耗时的任务，如 CSS 压缩、Sass 编译、图像优化和浏览器重装。

Gulp 为您提供了在某些触发事件发生后自动执行各种操作的工具。例如，考虑以下场景:

*   每次保存一个 Sass 文件，Gulp 都会编译 Sass 并输出一个缩小的 CSS 文件。
*   当你添加一个新的图像到一个文件夹，Gulp 会优化这个图像，并把它移到一个新的专用文件夹。
*   当你保存一个 PHP 或 Sass 文件时，Gulp 会自动重新加载浏览器。

### 吞咽设置

首先，你需要在你的系统中全局安装 [Gulp](http://gulpjs.com/) 。稍后，我将向您展示如何将它作为一个包安装在您的主题中。

假设安装了 Node.js，打开命令行工具，然后通过以下方式使用 npm 安装 Gulp:

```
npm install gulp -g
```

现在，运行`gulp -v` (Gulp 的 version 命令)来测试 Gulp 安装是否正确。您应该得到类似于以下内容的输出:

```
➜  ~ gulp -v
[09:33:59] CLI version 3.9.1
```

## 主题设置

在本教程中，我将使用[下划线](http://underscores.me/)作为基本主题。要下载它，导航到[下划线。我](http://underscores.me/)，生成一个新的主题，并给它一个名字，如“吞咽-wordpress”，下载到 wordpress 主题目录，然后从仪表板激活它。

从命令行，导航到您已经添加了主题的`gulp-wordpress`目录，例如在我的例子中:

```
cd ~/www/wordpress/wp-content/themes/gulp-wordpress
```

接下来，运行`npm init`命令并遵循一些简单的步骤来创建一个`package.json`文件，该文件将包含一些关于主题和稍后将安装的包的信息。

完成这些步骤后，您将得到一个类似于以下内容的启动文件:

```
{
  "name": "gulp-wordpress",
  "version": "1.0.0",
  "description": "WordPress Theme Development Automation with Gulp",
  "author": "Name"
} 
```

接下来，安装 Gulp 作为开发依赖项:

```
npm install gulp --save-dev
```

现在已经创建了一个包含 Gulp 包源文件的`node_modules`目录，并且您的`package.json`文件已经更新为包含 Gulp 作为开发依赖项。

```
{
  "name": "gulp-wordpress",
  "version": "1.0.0",
  "description": "WordPress Theme Development Automation with Gulp",
  "author": "Author Name",
  "devDependencies": {
    "gulp": "^3.9.1"
  }
} 
```

像 [gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer) 这样的一些 Gulp 任务需要 ES6 风格的 Promises 支持，这样你就可以安装 [es6-promise](https://github.com/jakearchibald/es6-promise) polyfill，然后在`gulpfile.js`的顶部需要它，我们接下来会这么做。

```
npm install es6-promise --save-dev
```

配置 Gulp 的最后一步是创建一个空的`gulpfile.js`配置文件，它将用于定义 Gulp 任务，比如 JavaScript 和 Sass。

`gulpfile.js`起始文件将如下所示:

```
require('es6-promise').polyfill();

var gulp = require('gulp');

// default task
gulp.task('default'); 
```

以上我们所做的是:

*   需要在文件顶部填充聚合填充，然后我们一口气导入。
*   创建了一个`default`任务。

为了确保 Gulp 正在运行并且一切都完美完成，在命令行中运行`gulp`来执行在`gulpfile.js`文件中创建的`default`任务。输出应该类似于:

```
[09:48:23] Using gulpfile ~/www/wordpress/wp-content/themes/gulp-wordpress/gulpfile.js
[16:33:13] Starting 'default'...
[16:33:13] Finished 'default' after 58 μs
```

## 通过大量任务加速开发

此时，主题已经为新任务做好了准备，是时候完成一些常见的任务了，您可以使用这些任务来加速您的主题开发。

### 使用 CSS (Sass)

如果你使用 [Sass](http://sass-lang.com/) 来编写 CSS，两件主要的事情需要自动化，第一件是将 Sass 编译成 CSS，第二件是使用 autoprefixer 将厂商前缀添加到你的 CSS 中。还要注意，我用 Sass 作为例子，如果你喜欢另一个选项，比如少了的[，你也可以为它找到一个 Gulp 插件。](http://lesscss.org/)

首先安装 [`gulp-sass`](https://www.npmjs.com/package/gulp-sass) 和 [`gulp-autoprefixer`](https://www.npmjs.com/package/gulp-autoprefixer) 。

```
npm install gulp-sass gulp-autoprefixer --save-dev
```

下一步是创建一个具有基本结构的 Sass 目录。

```
├── sass
│   └── style.scss 
```

`style.scss`文件是主要的起点，你可以自由地创建你的 Sass 架构，并根据你的喜好导入其他组件、模块、函数。

前几行将是 WordPress 需要的 [*样式表头*](https://codex.wordpress.org/Theme_Development#Theme_Stylesheet) 。

```
/*
  Theme Name: Gulp WordPress
  Theme URI: http://example.com/
  Author: Author Name
  Author URI: http://example.com/
  Description: Description
  Version: 1.0.0
  License:
  License URI:
  Text Domain: gulp-wordpress
*/

body {
  color: #333;
  background-color: #fff;
} 
```

下一步是创建一个`sass`任务，它将做以下事情:

*   编译并自动修复 Sass。
*   构建一个`style.css`文件，这是 WordPress 最终使用的 CSS 文件。

```
require('es6-promise').polyfill();
var gulp          = require('gulp');
var sass          = require('gulp-sass');
var autoprefixer  = require('gulp-autoprefixer');

gulp.task('sass', function() {
  return gulp.src('./sass/**/*.scss')
  .pipe(sass())
  .pipe(autoprefixer())
  .pipe(gulp.dest('./'))
});

gulp.task('default', ['sass']); 
```

现在，直接从命令行运行`gulp sass`任务，这将编译`style.scss`文件，并在主题根中构建一个新的`style.css`文件。

运行`sass`的另一种方式是将任务名称作为第二个参数传递给`default`任务，就像我上面做的那样，因此通过运行`gulp`，将会执行`sass`任务。

一个好的 WordPress 实践是将 CSS 目录包含在最终 CSS 文件的顶部，就在样式表标题之后，然后在与该部分相关的任何代码或导入之前添加 CSS 注释。

请注意，该注释是标准 CSS 样式的注释，如(`/*----- 1.0 Normalize -----*/`)，而不是 Sass 注释，如(`//----- 1.0 Normalize -----`)。这很重要，因为这个注释需要存在于最终的 CSS 文件中，但是对于 Sass 样式的注释，它将被 Sass 编译器隐藏。另外，请注意，这是在样式表标题和目录中使用的。

下面是一个包含目录的`style.scss`文件的例子，以及一些到外部 sass 文件的导入。

```
/*
  Stylesheet Header ...
*/

/*--------------------
>>> TABLE OF CONTENTS:
----------------------
1.0 Normalize
2.0 Typography
3.0 Icons
4.0 Components
--------------------*/

/*----- 1.0 Normalize -----*/
@import 'normalize';

/*----- 2.0 Typography -----*/
@import 'typography'; 
```

为了自动生成`rtl.css`文件，可以使用 [`gulp-rtlcss`](https://www.npmjs.com/package/gulp-rtlcss) 插件将 LTR(从左到右)CSS 自动转换为 RTL(从右到左)，因此您可以在一个文件中编写 Sass，然后 Gulp 将生成两个 CSS 文件，第一个是`style.css`文件，第二个是`rtl.css`文件。

`gulp-rtlcss`背后的想法是将所有 CSS 属性从左向右转换，比如浮动、文本对齐、文本方向和其他属性。

第二个插件是 [`gulp-rename`](https://www.npmjs.com/package/gulp-rename) ，它会自动将文件重命名为`rtl.css`。

```
npm install gulp-rtlcss gulp-rename --save-dev
```

下一步是在`gulpfile.js`文件的顶部包含新安装的插件，并在`style.css`生成后修改`sass`任务以使用`rtlcss()`进行转换。

在这一步中，插件将从左到右转换所有 CSS 属性，如浮动和文本方向，然后将文件重命名为`rtl.css`，然后将文件输出到主题根:

```
var rtlcss       = require('gulp-rtlcss');
var rename       = require('gulp-rename');

gulp.task('sass', function() {
  return gulp.src('./sass/*.scss')
  .pipe(sass())
  .pipe(autoprefixer())
  .pipe(gulp.dest('./'))              // Output LTR stylesheets (style.css)

  .pipe(rtlcss())                     // Convert to RTL
  .pipe(rename({ basename: 'rtl' }))  // Rename to rtl.css
  .pipe(gulp.dest('./'));             // Output RTL stylesheets (rtl.css)
}); 
```

运行`gulp sass`，将会生成`style.css`和`rtl.css`文件。

要了解关于 Sass 结构的更多信息，以及如何与 Gulp 一起使用，请阅读以下内容:

*   [Sass 项目的架构](https://www.sitepoint.com/architecture-sass-project/)
*   【Sass 的简单工作流程

### 观看文件

每当您更改 Sass 文件时，不需要运行`gulp sass`，而是需要一个新的任务来为您自动完成这项工作。

任务`watch`将用于查看对文件所做的任何更改，因此一旦文件发生更改，它将运行另一个操作。例如，当您保存一个 Sass 文件时，那么`sass`任务应该会自动运行。

在我们的`gulpfile.js`文件中，添加一个新的`watch`任务来观察`/sass`目录中的任何变化，然后运行`sass`任务。下一步是用监视任务更新`default`任务。

```
gulp.task('watch', function() {
  gulp.watch('./sass/**/*.scss', ['sass']);
});

gulp.task('default', ['sass', 'watch']); 
```

现在你可以在命令行中运行`gulp`来首先执行`sass`任务，然后`watch`任务将继续工作。

### 错误处理

有时候当你在写代码的时候，你可能会在工作当中写一个未定义的 Sass 变量。当你在看文件时，Gulp 会中断，因为特定的任务不能编译那个变量，这很烦人，因为你必须再次启动 Gulp 才能继续工作。

你可以通过使用 [gulp-plumber](https://www.npmjs.com/package/gulp-plumber) 插件来解决这个问题，这将防止由于错误导致的 gulp 中断。

为了改进错误处理，安装 [gulp-util](https://github.com/gulpjs/gulp-util) 实用功能包来定制错误消息，在错误发生时添加蜂鸣声，并为错误消息添加颜色，这有助于识别错误。

```
npm install gulp-plumber gulp-util --save-dev
```

```
var plumber = require('gulp-plumber');
var gutil = require('gulp-util');

var onError = function (err) {
  console.log('An error occurred:', gutil.colors.magenta(err.message));
  gutil.beep();
  this.emit('end');
};

gulp.task('sass', function() {
  return gulp.src('./sass/*.scss')
  .pipe(plumber({ errorHandler: onError }))
  //
  //
}); 
```

上面做了什么:

*   增加了一个`onError`功能来记录错误信息，并产生嘟嘟声。
*   更新`sass`任务以使用`plumber`函数，然后将`onError`函数传递给`errorHandler`对象属性。

通过这样做，您可以确切地知道发生了什么错误以及在哪个文件中。最重要的是，它还会阻止 Gulp 停下来！

一个未定义的 Sass 变量`$color`的例子。

![Undefined Sass Variable Error Handling](img/84e169e74f79a5013545e1faa7a76276.png)

### Java Script 语言

对于使用 JavaScript，有不同的工具可以加速和改进您的 JavaScript 开发工作流程，例如:

*   将许多 JavaScript 文件连接成一个文件。
*   用 JSHint 检查代码错误。
*   缩小代码以获得更小的文件大小。

您可以按如下方式安装这些插件:

要连接文件:

```
npm install gulp-concat --save-dev
```

要验证 JavaScript:

```
npm install gulp-jshint --save-dev
```

要缩减代码:

```
npm install gulp-uglify --save-dev
```

接下来，在`gulpfile.js`文件中，我们需要新安装的插件并添加一个新的`js`任务。

```
var concat = require('gulp-concat');
var jshint = require('gulp-jshint');
var uglify = require('gulp-uglify');

gulp.task('js', function() {
  return gulp.src(['./js/*.js'])
    .pipe(jshint())
    .pipe(jshint.reporter('default'))
    .pipe(concat('app.js'))
    .pipe(rename({suffix: '.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('./js'))
}); 
```

这个任务获取`./js`目录中任何以`.js`结尾的文件，用 jshint 检查代码错误，然后将它们连接成`app.js`，由于我们需要一个缩小的输出，现在是时候将文件重命名为`app.min.js`。然后我们缩小代码，最后一步，将文件输出到`./js`目录。

此时，您将希望在主题根中创建一个`.jshintrc`配置文件，这是一个简单的 JSON 文件，指定要打开或关闭哪些 JSHint 选项，例如:

```
{
  "undef": true,
  "unused": true,
  "browser": true
} 
```

*   使用未声明的变量时发出警告。
*   `unused`:当定义了一个变量却从未使用它时发出警告。
*   `browser`:定义现代浏览器公开的全局，比如`navigator`和`document`。

JSHint 附带了一系列的[选项](http://jshint.com/docs/options/)，您可以根据自己的喜好使用它们。

现在，从命令行(或终端)运行`gulp js`。将生成一个新的`app.min.js`文件，稍后将在主题中使用。

默认情况下，`_underscore`主题包括`/js`目录下的`customizer.js`、`navigation.js`和`skip-link-focus-fix.js`文件。

如果您只需要包含特定的文件，您可以将它们添加到`gulp.src`数组中，如下所示:

```
gulp.task('js', function() {
  return gulp.src([
    './js/navigation.js', 
    './js/skip-link-focus-fix.js'
  ])
}); 
```

上面的代码将对这两个文件执行所有操作。如果需要添加另一个新文件，可以将其附加到数组中。

您还可以更新`watch`任务来观察对任何 JavaScipt 文件的更改，并且还可以更新`default`任务来在运行`gulp`时自动运行`js`动作，就像我们对 Sass 所做的那样。

```
gulp.task('watch', function() {
  gulp.watch('./sass/**/*.scss', ['sass']);
  gulp.watch('./js/*.js', ['js']);
});

gulp.task('default', ['sass', 'js', 'watch']); 
```

在`functions.php`主题文件中，将生成的`app.min.js`文件排队为:

```
wp_enqueue_script( 'gulp-wordpress-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20151215', true ); 
```

您可以删除其他排队的 JavaScript 文件，因为它们已经连接并缩小为一个文件。在 HTTP 的当前状态下，包含一个文件而不是多个文件将会加快和改善网站性能。

`functions.php`文件中的入队脚本函数将看起来类似于:

```
/**
 * Enqueue scripts and styles.
 */
function gulp_wordpress_scripts() {
  wp_enqueue_style( 'gulp-wordpress-style', get_stylesheet_uri() );

  wp_enqueue_script( 'gulp-wordpress-javascript', get_template_directory_uri() . '/js/app.min.js', array(), '20151215', true );
}
add_action( 'wp_enqueue_scripts', 'gulp_wordpress_scripts' ); 
```

### 形象

现在让我们试着用 Gulp 优化图片，通过这样做，我们将确保主题中所有使用的图片都自动优化速度。为了使这种自动化更容易，您可以设置一个 gulp 任务来查看图像目录，一旦您将图像拖到那里，Gulp 就会优化它，并将其移动到另一个文件夹中，以便优化和准备使用图像。

创建两个文件夹:

1. img/src`:源文件夹包含原始图像。
2. img/dest`:目标文件夹包含优化后的图像。

安装 [gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin) 来缩小 PNG、JPEG、GIF 和 SVG 图像。

```
npm install gulp-imagemin --save-dev 
```

创建一个新任务(`images`)，该任务将观察位于源文件夹img/src`)中的图像，优化它们，并将它们移动到优化的文件夹img/dest`)。

```
var imagemin = require('gulp-imagemin');

gulp.task('images', function() {
  return gulp.src(img/src/*')
    .pipe(plumber({errorHandler: onError}))
    .pipe(imagemin({optimizationLevel: 7, progressive: true}))
    .pipe(gulp.dest(img/dist'));
}); 
```

您还可以查img/src`文件夹，这样每当您将新图像拖动到那里时，`images`任务就会运行。同样，更新`default`任务来运行`images`动作。

```
gulp.task('watch', function() {
  //
  gulp.watch('images/src/*', ['images']);
});

gulp.task('default', ['sass', 'js', 'images', 'watch']); 
```

### 使用 BrowserSync 刷新浏览器

如果您希望在任何代码更改后刷新浏览器，无论代码是 PHP、Sass 还是 JavaScript，该怎么办？如果您使用不止一个浏览器或移动设备进行测试，该怎么办？你必须在每个屏幕上点击刷新按钮。BrowserSync 将自动有效地帮助您完成这项工作！

首先，您需要安装 BrowserSync 作为开发依赖项。

```
npm install browser-sync --save-dev
```

接下来，在您的`gulpfile.js`文件中要求 BrowserSync，并更新`watch`任务以包含 BrowserSync:

```
var browserSync = require('browser-sync').create();
var reload = browserSync.reload;

gulp.task('watch', function() {
  browserSync.init({
    files: ['./**/*.php'],
    proxy: 'http://localhost:8888/wordpress/',
  });
  gulp.watch('./sass/**/*.scss', ['sass', reload]);
  gulp.watch('./js/*.js', ['js', reload]);
  gulp.watch('images/src/*', ['images', reload]);
}); 
```

注意，您需要将[代理](https://www.browsersync.io/docs/options/#option-proxy)选项更新为本地开发 URL。例如，如果您的本地 URL 是`localhost:8888/wordpress`，用它更新上面的代理值。

例如，由于 BrowserSync 可以在您工作时监视您的文件，所以我将该任务配置为监视 PHP 文件的任何更改。

我还更新了`sass`、`js`和`images`观察任务，以便在文件发生任何变化或者图像文件夹的内容发生变化时重新加载页面。

现在，您可以运行`gulp`，浏览器中会自动打开一个指向本地主机 URL 的新标签，您的控制台将如下所示:

```
[BS] Access URLs:
 --------------------------------------------
       Local: http://localhost:3000/wordpress/
    External: http://192.168.1.2:3000/wordpress/
 --------------------------------------------
          UI: http://localhost:3001
 UI External: http://192.168.1.2:3001
 --------------------------------------------
```

您可以在连接到同一网络的任何设备中使用*外部* URL，因此在每次更改浏览器时，Sync 都会重新加载所有浏览器。

*UI 外部* URL 用于浏览器同步控制面板，允许您更改同步选项，管理设备。

## 结论

正如您所看到的，使用自动化工具非常有价值，并且可以提高开发过程的速度。有很多 [Gulp 插件](http://gulpjs.com/plugins/)你可以根据自己的需求来尝试和使用，所以有任何可以自动节省时间的东西，去做吧！

我为这篇文章的 WordPress 主题创建了一个 GitHub repo，你可以在这里查看代码。如果您在开发过程中使用了任何 Gulp 插件，让我知道这让您的生活变得更加轻松。

## 分享这篇文章
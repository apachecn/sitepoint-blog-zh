# 使用 Gulp 更快地开发 WordPress 主题

> 原文：<https://www.sitepoint.com/fast-gulp-wordpress-theme-development-workflow/>

*本文是与 [SiteGround](https://www.siteground.com/sitepoint-recommended?afcode=97a975da3502771c04e59cbae092b1dd&campaign=gulp) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

WordPress 简单的主题开发是其成功的部分原因。一个具有前端和 PHP 知识的开发者可以参考优秀的 [Codex](https://codex.wordpress.org/) 并开始他们的下一个杰作。

主题开发只需要一个文本编辑器和图形包，但是现代工具集可以彻底改变你的工作流程。在本教程中，我们将使用 [Gulp](http://gulpjs.com/) 来运行任务，包括:

*   复制较新的 PHP 主题文件
*   优化图像
*   将[萨斯 SCSS 文件](http://sass-lang.com/)编译成一个单一的、缩小的 CSS 文件
*   合并有序的 JavaScript 文件，删除调试语句并缩小
*   文件更新时自动刷新浏览器。

## 什么是吞咽？

Gulp 是一个基于 JavaScript 的构建系统，它将你的源文件转换成优化的版本。如果你是 Gulp 新手，请参考[Gulp . js 简介](https://www.sitepoint.com/introduction-gulp-js/)获取完整的安装和使用说明。初始步骤的总结:

1.  安装 [Node.js](https://nodejs.org/en/) 。
2.  全局安装 Gulp:`npm install gulp-cli -g`
3.  创建一个项目文件夹并导航到其中:`mkdir mytheme`后跟`cd mytheme`。
4.  用 npm 初始化您的项目:`npm init`

## 您的项目文件

Gulp(或任何)构建过程需要一组原始的**源**文件，其中包含您未修改的代码和图像。这些被处理、操作和缩小以创建**构建**文件。

WordPress 被安装到一个 web 服务器文件夹中，可能是 Linux/Apache 系统上的`/var/www/`。你的 WordPress 主题必须在`/wp-content/themes/`的子文件夹中定义。因此，包含我们构建的文件的文件夹可能是`/var/www/wp-content/themes/mytheme/`。主题至少需要两个文件:

*   顶部注释中包含元数据的`style.css`样式表，以及
*   一个`index.php`模板文件。

大多数主题包含更多的文件，用于呈现文章、页面、索引、类别、标签和错误。像页眉和页脚这样的跨页部分通常被定义为图像和 JavaScript 文件。

您可以将源文件放在`mytheme`文件夹中的某个地方。如果你发布一个主题供他人下载、修改和构建，这可能是有用的。然而，出于本教程的目的，我们将使用一个无法从 web 服务器访问的源文件夹，例如`~/mytheme/`。这种方法的好处是:

1.  你的主题源文件可以在一个单独的文件夹和存储库中进行管理，而不会污染构建或 WordPress 文件夹。
2.  最终构建的主题文件夹只包含它需要的文件。
3.  Gulp，它的插件和其他应用都不包含在主题文件夹里。它们不能被意外地复制到生产服务器上，这是不必要的，可能会有安全隐患。

源项目文件夹还需要四个子文件夹:

*   `template`–WordPress PHP 主题文件
*   `images`–您的主题使用的图像
*   `scss`–萨斯 SCSS 源文件
*   `js`–任意数量的独立客户端 JavaScript 源文件。

## 安装依赖项

从源文件夹(`~/mytheme/`)中，运行下面的`npm`命令，将 Gulp 和所有插件安装为开发依赖项:

```
npm install --save-dev autoprefixer browser-sync css-mqpacker cssnano gulp gulp-concat gulp-deporder gulp-imagemin gulp-newer gulp-postcss gulp-sass gulp-strip-debug gulp-uglify gulp-util postcss-assets
```

将创建一个包含模块代码的`node_modules`文件夹。这应该从您的源代码控制系统中省略掉(对于 Git 用户，将`node_modules`添加到您的`.gitignore`文件中)。

## 创建一个 Gulp 配置文件

在源文件夹的根目录下创建一个新的`gulpfile.js`配置文件。添加以下代码开始:

```
// Gulp.js configuration
'use strict';

const

  // source and build folders
  dir = {
    src         : 'src/',
    build       : '/var/www/wp-content/themes/mytheme/'
  },

  // Gulp and plugins
  gulp          = require('gulp'),
  gutil         = require('gulp-util'),
  newer         = require('gulp-newer'),
  imagemin      = require('gulp-imagemin'),
  sass          = require('gulp-sass'),
  postcss       = require('gulp-postcss'),
  deporder      = require('gulp-deporder'),
  concat        = require('gulp-concat'),
  stripdebug    = require('gulp-strip-debug'),
  uglify        = require('gulp-uglify')
;

// Browser-sync
var browsersync = false;

// PHP settings
const php = {
  src           : dir.src + 'template/**/*.php',
  build         : dir.build
};

// copy PHP files
gulp.task('php', () => {
  return gulp.src(php.src)
    .pipe(newer(php.build))
    .pipe(gulp.dest(php.build));
});
```

我们定义默认文件夹，加载模块，然后创建一个`php`任务，将新的和更新的文件复制到主题文件夹。这个任务有意保持简单，按原样复制 PHP 源文件。

保存`gulpfile.js`并在源`template`文件夹中创建几个`.php`文件。然后输入以下命令:

```
gulp php
```

所有文件将被复制到主题文件夹(`/var/www/wp-content/themes/mytheme/`)。

## 图像处理

图像文件通常可以使用诸如 [imagemin](https://github.com/imagemin/imagemin) 之类的工具进一步压缩。将以下代码添加到`gulpfile.js`:

```
// image settings
const images = {
  src         : dir.src + 'images/**/*',
  build       : dir.build + 'images/'
};

// image processing
gulp.task('images', () => {
  return gulp.src(images.src)
    .pipe(newer(images.build))
    .pipe(imagemin())
    .pipe(gulp.dest(images.build));
});
```

保存然后运行`gulp images`。源`images`文件夹中任何新的或更新的图像的压缩版本被复制到`/var/www/wp-content/themes/mytheimg/`。

## Sass 汇编

WordPress 不能直接使用 Sass 文件；您必须编译成一个单独的`style.css`文件。将以下代码添加到`gulpfile.js`:

```
// CSS settings
var css = {
  src         : dir.src + 'scss/style.scss',
  watch       : dir.src + 'scss/**/*',
  build       : dir.build,
  sassOpts: {
    outputStyle     : 'nested',
    imagePath       : images.build,
    precision       : 3,
    errLogToConsole : true
  },
  processors: [
    require('postcss-assets')({
      loadPaths: ['images/'],
      basePath: dir.build,
      baseUrl: '/wp-content/themes/wptheme/'
    }),
    require('autoprefixer')({
      browsers: ['last 2 versions', '> 2%']
    }),
    require('css-mqpacker'),
    require('cssnano')
  ]
};

// CSS processing
gulp.task('css', ['images'], () => {
  return gulp.src(css.src)
    .pipe(sass(css.sassOpts))
    .pipe(postcss(css.processors))
    .pipe(gulp.dest(css.build))
    .pipe(browsersync ? browsersync.reload({ stream: true }) : gutil.noop());
});
```

使用`gulp css`启动这个新任务，以便:

*   首先运行 Gulp `images`任务(CSS 中可能需要图像)
*   使用快速 [LibSass](http://sass-lang.com/libsass) 编译器编译源文件`scss/style.scss`中的 Sass 代码
*   使用 [PostCSS](https://github.com/postcss) 来自动添加资产引用、应用供应商前缀、打包媒体查询，并缩减生成的 CSS 代码
*   将样式表输出到`/var/www/wp-content/themes/mytheme/style.css`。
*   强制一个 [Browsersync](https://browsersync.io/) CSS 重新加载(稍后会详细介绍)。

源文件必须在顶部包含 WordPress 主题元数据，例如

```
/*!
    Theme Name: My Theme
    Theme URI: https://www.sitepoint.com/
    Description: Demonstration theme
    Version: 1.0.0
    Author: Craig Buckler (@craigbuckler)
    Author URI: https://www.sitepoint.com/
    Tags: Gulp

    License: MIT
    License URI: http://opensource.org/licenses/mit-license.php
*/

@import '_base';
@import '_forms';
@import '_tables';
@import 'components/_widget1';
// etc...
```

使用 **`/*!`** 作为第一行很重要。这确保了 [cssnano](http://cssnano.co/) minifier 不会删除评论并使你的主题不可用。

[postcss-assets](https://github.com/borodean/postcss-assets) 插件允许您使用如下代码来引用图像资源:

```
.widget1 {
  width: width('myimage.jpg');
  height: height('myimage.jpg');
  background-image: resolve('myimage.jpg');
}
```

您还可以使用自动 Base64 编码嵌入图像:

```
.widget2 {
  background-image: inline('myimage.jpg');
}
```

## JavaScript 处理

将以下代码添加到`gulpfile.js`:

```
// JavaScript settings
const js = {
  src         : dir.src + 'js/**/*',
  build       : dir.build + 'js/',
  filename    : 'scripts.js'
};

// JavaScript processing
gulp.task('js', () => {

  return gulp.src(js.src)
    .pipe(deporder())
    .pipe(concat(js.filename))
    .pipe(stripdebug())
    .pipe(uglify())
    .pipe(gulp.dest(js.build))
    .pipe(browsersync ? browsersync.reload({ stream: true }) : gutil.noop());

});
```

使用`gulp js`运行这个新任务，以便:

*   处理源`js`文件夹中的所有 JavaScript 文件
*   对文件进行适当的排序。在 JavaScript 文件的顶部添加注释来声明依赖关系，例如`// requires: lib1.js`或`// requires: config.js lib1.js`。
*   连接成一个文件
*   剥离所有调试和控制台日志记录语句
*   缩小代码
*   将结果代码输出到`/var/www/wp-content/themes/mytheme/js/scripts.js`。
*   强制一个 [Browsersync](https://browsersync.io/) CSS 重新加载(稍后会详细介绍)。

## 运行一切

我们可以将下面的代码添加到`gulpfile.js`中，而不是分别调用每个任务:

```
// run all tasks
gulp.task('build', ['php', 'css', 'js']);
```

您现在可以使用`gulp build`来并行运行`php`、`js`、`css`和`images`任务。(注意`images`是`css`任务的依赖项，所以我们不需要直接调用它。)

## 启用文件监视和浏览器同步

您的工作流程可以通过以下方式得到根本改善:

1.  让 Gulp 在启动适当的任务之前观察文件的变化。
2.  当 CSS 和 JavaScript 文件改变时自动重新加载它们(不刷新页面)。
3.  当模板文件改变时自动刷新页面。

首先，我们需要在`gulpfile.js`中定义一个`browsersync`任务。这将为你在`localhost`上运行 WordPress 的 web 服务器创建一个代理服务器(根据需要改变这个域或使用一个 IP 地址):

```
// Browsersync options
const syncOpts = {
  proxy       : 'localhost',
  files       : dir.build + '**/*',
  open        : false,
  notify      : false,
  ghostMode   : false,
  ui: {
    port: 8001
  }
};

// browser-sync
gulp.task('browsersync', () => {
  if (browsersync === false) {
    browsersync = require('browser-sync').create();
    browsersync.init(syncOpts);
  }
});
```

现在添加一个`watch`任务来运行 Browsersync，观察文件更改并运行适当的任务:

```
// watch for file changes
gulp.task('watch', ['browsersync'], () => {

  // page changes
  gulp.watch(php.src, ['php'], browsersync ? browsersync.reload : {});

  // image changes
  gulp.watch(images.src, ['images']);

    // CSS changes
  gulp.watch(css.watch, ['css']);

  // JavaScript main changes
  gulp.watch(js.src, ['js']);

});
```

最后，添加一个运行初始构建并启动`watch`任务的`default` Gulp 任务:

```
// default task
gulp.task('default', ['build', 'watch']);
```

现在从命令行运行`gulp`。控制台将显示包含类似以下内容的输出:

```
[BS] Proxying: http://localhost
[BS] Access URLs:
 -------------------------------------
       Local: http://localhost:3000
    External: http://192.168.1.99:3000
 -------------------------------------
          UI: http://localhost:8001
 UI External: http://192.168.1.99:8001
 -------------------------------------
[BS] Watching files... 
```

不要从`http://localhost/`加载你的开发网站，输入地址`http://localhost:3000/`或者**外部** URL，如果你是从另一个设备查看的话。你的 WordPress 站点会像以前一样加载，但是 Gulp 会观察变化并立即应用更新。您将永远不需要切换到您的浏览器，并再次点击刷新！

当您想要停止吞咽处理时，点击 **Ctrl/Cmd + C** 。

## 进一步增强

我们已经用 Gulp 介绍了 WordPress 主题开发的基础，但是还有几千个插件来帮助你的工作流程。您可以考虑其他任务来:

*   lint 你的 [PHP](https://www.npmjs.com/package/phplint/) 和 [JavaScript](https://www.npmjs.com/package/gulp-jslint/) 代码
*   从`package.json`信息创建 [WordPress 主题样式](https://www.npmjs.com/package/gulp-wpstylecss/)
*   [缓存崩溃](https://github.com/tylercraft/gulp-wp-cache-bust) WordPress 资产
*   自动将您的主题部署到生产服务器。

从长远来看，花几个小时编写大量任务可以节省许多天的手工处理时间。

## 分享这篇文章
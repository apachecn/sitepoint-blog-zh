# Gulp.js 简介

> 原文：<https://www.sitepoint.com/introduction-gulp-js/>

开发人员花在编码上的时间很少。即使我们忽略恼人的会议，大部分工作都涉及到基本任务，这些任务会影响你的工作日:

*   从模板和内容文件生成 HTML
*   压缩新的和修改过的图像
*   将 Sass 编译成 CSS 代码
*   从脚本中删除`console`和`debugger`语句
*   将 ES6 转换成跨浏览器兼容的 ES5 代码
*   代码林挺和验证
*   连接和缩小 CSS 和 JavaScript 文件
*   将文件部署到开发、暂存和生产服务器

每次做出改变时，任务都必须重复。您可能从良好的意图开始，但是最可靠的开发人员会忘记压缩一两张图像。随着时间的推移，前期制作任务变得越来越艰巨和耗时；你会害怕不可避免的内容和模板变化。这是令人麻木的重复性工作。把时间花在更赚钱的工作上会不会更好？

如果是这样，你需要一个**任务运行器**或者**构建进程**。

## 这听起来非常复杂！

创建一个构建过程需要时间。这比手动执行每项任务更复杂，但从长远来看，您将节省数小时的精力，减少人为错误，并保持理智。采取务实的方法:

*   首先自动化最令人沮丧的任务。
*   尽量不要让你的构建过程过于复杂；一两个小时对于初始设置来说已经足够了。
*   选择任务运行软件，坚持一段时间。不要心血来潮换了另一个选项。

有些工具和概念对你来说可能是新的，但是深呼吸，一次集中精力做一件事。

## 任务运行器:选项

像 GNU Make T1 这样的构建工具已经存在几十年了，但是特定于 web 的任务运行程序还是一个相对较新的现象。第一个达到临界质量的是[Grunt](http://gruntjs.com/)——一个 Node.js 任务运行程序，它使用了由 JSON 配置文件配置的插件。Grunt 取得了巨大的成功，但却遭遇了速度和复杂的定制问题。许多问题在后来的版本中得到了解决，但 [Gulp](http://gulpjs.com/) 已经到来，并提供了许多改进:

1.  文件监控等功能是内置的。
2.  Gulp 插件很简单，被设计来完成一项工作。
3.  Gulp 使用 JavaScript 配置代码，它不那么冗长，更容易阅读，更容易修改，并且提供了更好的灵活性。
4.  Gulp 速度更快，因为它使用 [Node.js 流](https://www.sitepoint.com/basics-node-js-streams/)通过一系列管道插件传递数据。文件仅在每个任务结束时写入。

从那以后，出现了几个竞争对手:

*   [npm 是简单项目的一个选项](https://www.sitepoint.com/guide-to-npm-as-a-build-tool/)
*   网络包和[包裹](https://parceljs.org/)越来越受欢迎。这些模块捆绑器理解 JavaScript 语法，只需很少的配置就可以执行简单的任务。

然而，Gulp 仍然提供了灵活性和效率，吸引了三分之一的网络开发者。如果你能写 JavaScript，Gulp 允许通用任务做任何事情。

Gulp 需要 Node.js. JavaScript 知识是有益的，但所有 web 编程信仰的开发人员都会发现它很有用。

## 吞咽版本

Gulp.js 3.9.1 多年来一直是默认版本，虽然 Gulp 4 可用，但它必须与`npm install gulp@next`一起显式安装。这是必要的，因为尽管插件仍然兼容，Gulp 4 使用了新的配置语法。

2018 年 12 月 10 日， [Gulp.js 4.0 被宣布为默认](https://medium.com/gulpjs/version-4-now-default-92c6cd4beb45)，并发布到 npm。任何在新项目中使用`npm install gulp`的人都会收到版本 4。这将贯穿本指南。

## 步骤 1:安装 Node.js

可以从[nodejs.org/download/](https://nodejs.org/download/)下载适用于 Windows、macOS 和 Linux 的 Node.js。从二进制文件、包管理器和 docker 镜像安装有各种选项；完整的说明是可用的。

*注意:Node.js 和 Gulp 在 Windows 上运行，但是如果它们依赖于原生 Linux 二进制文件，一些插件可能无法正常工作。Windows 10 用户的一个选择是用于 Linux 的 [Windows 子系统；这可以解决问题，但可能会引入其他问题。](https://docs.microsoft.com/en-gb/windows/wsl/about)*

安装后，打开命令提示符并输入以下内容以显示版本号:

```
node -v 
```

您将大量使用`npm`——用于安装模块的 Node.js 包管理器。检查其版本号:

```
npm -v 
```

*注意:Node.js 模块可以全局安装，因此它们在您的整个系统中都可用。然而，大多数用户没有权限写入全局目录，除非`npm`命令带有前缀`sudo`。有许多[选项来修复 npm 权限](https://docs.npmjs.com/getting-started/fixing-npm-permissions)和工具，如 [nvm 可以帮助](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)，但你也可以改变默认目录，例如在基于 Ubuntu/Debian 的平台上:*

```
cd ~
  mkdir .node_modules_global
  npm config set prefix=$HOME/.node_modules_global
  npm install npm -g
```

*然后在`~/.bashrc`的末尾加上下面一行:*

```
export PATH="$HOME/.node_modules_global/bin:$PATH"
```

*然后用这个更新:*

```
source ~/.bashrc
```

## 第二步:全球安装 Gulp

全局安装 Gulp 命令行界面，这样`gulp`命令可以从任何项目文件夹运行:

```
npm install gulp-cli -g 
```

验证 Gulp 已安装了以下组件:

```
gulp -v 
```

## 步骤 3:配置您的项目

*注意:如果您已经有一个`package.json`配置文件，您可以跳过这一步。*

让我们假设您在文件夹`project1`中有一个新的或预先存在的项目。导航到此文件夹，并使用 npm 对其进行初始化:

```
cd project1
npm init 
```

你会被问一系列问题:输入一个值或点击`Return`接受默认值。完成后将创建一个保存您的`npm`配置设置的`package.json`文件。

*注意:Node.js 将模块安装到一个`node_modules`文件夹中。您应该将它添加到您的`.gitignore`文件中，以确保它们不会被提交到您的存储库中。当将项目部署到另一个系统时，您可以运行`npm install`来恢复它们。*

对于本指南的其余部分，我们将假设您的项目文件夹包含下面列出的子文件夹。

### `src`文件夹:预处理源文件

这包含更多的子文件夹:

*   `html` — HTML 源文件和模板
*   `images` —原始的未压缩图像
*   `js` —多个预处理脚本文件
*   `scss` —多个预处理的 Sass `.scss`文件

### `build`文件夹:编译/处理过的文件

Gulp 将根据需要创建文件和子文件夹:

*   `html` —编译的静态 HTML 文件
*   `images` —压缩图像
*   `js` —单个串联和缩小的 JavaScript 文件
*   `css` —单个编译和缩小的 CSS 文件

您的项目几乎肯定会有所不同，但是下面的例子使用了这种结构。

*注意:如果您在基于 Unix 的系统上，并且您只想按照本指南进行操作，那么您可以使用下面的命令重新创建源文件夹结构:*

```
mkdir -p src/{html,images,js,scss}
```

## 步骤 4:在本地安装 Gulp

现在，您可以使用以下命令在项目文件夹中安装 Gulp:

```
npm install gulp --save-dev 
```

这将安装 Gulp 作为开发依赖项，并且相应地更新`package.json`的`"devDependencies"`部分。在本指南的剩余部分，我们将假设 Gulp 和所有插件都是开发依赖项。

### 替代部署选项

当操作系统上的`NODE_ENV`环境变量设置为`production`时，不会安装开发依赖项。您通常会在您的活动服务器上使用 macOS/Linux 命令来完成此操作:

```
export NODE_ENV=production 
```

或者在 Windows 上:

```
set NODE_ENV=production 
```

本指南假设您的资产将被编译到`build`文件夹，并提交到您的 Git 存储库或直接上传到服务器。然而，如果您想改变创建资源的方式，那么在活动服务器上构建资源可能更好——例如，HTML、CSS 和 JavaScript 文件在生产环境中被缩小，而不是在开发环境中。在这种情况下，在安装 Gulp 及其插件时，请移除`--save-dev`开关。例如:

```
npm install gulp 
```

这将 Gulp 设置为`package.json`的`"dependencies"`部分中的应用程序依赖项。它将在您进入`npm install`时安装，并且可以在项目部署的任何地方运行。您可以从存储库中删除`build`文件夹，因为需要时可以在任何平台上创建文件。

## 步骤 4:创建一个 Gulp 配置文件

在项目文件夹的根目录下创建一个新的`gulpfile.js`配置文件。添加一些基本代码开始:

```
// Gulp.js configuration

const
  // modules
  gulp = require('gulp'),

  // development mode?
  devBuild = (process.env.NODE_ENV !== 'production'),

  // folders
  src = 'src/',
  build = 'build/'
  ; 
```

它引用 Gulp 模块，在开发(或非生产模式)中运行时将一个`devBuild`变量设置为`true`,并定义源代码和构建文件夹的位置。

*注:本指南中提供了 ES6 代码。这将在 6.0 及更高版本的 Node.js 中工作。*

`gulpfile.js`我不会做任何事情，因为你需要按照下面描述的下一步操作。

## 第五步:创建吞咽任务

就其本身而言，Gulp 什么也做不了。您必须:

1.  安装 Gulp 插件，以及
2.  编写利用这些插件做一些有用的事情的任务

编写自己的插件是可能的，但是，由于有近 3000 个可用的插件，你不太可能需要这样做。你可以在[gulpjs.com/plugins/](http://gulpjs.com/plugins/)，在[npmjs.com](https://www.npmjs.com/)使用 Gulp 自己的目录进行搜索，或者利用谷歌的强大功能搜索“gulp *什么的*。

大多数任务将使用:

*   `gulp.src(folder)`从源文件夹中的文件创建流，以及
*   `gulp.dest(folder)`将流作为文件输出到目标构建文件夹

在`.src`和`.dest`之间可以用`.pipe(plugin)`调用任意数量的插件方法。

### 图像任务

最好用一个例子来演示，所以让我们创建一个基本任务，压缩图像并将它们复制到适当的`build`文件夹。由于这个过程需要时间，我们将只压缩新的和修改过的文件。两个插件可以帮助我们: [gulp-newer](https://www.npmjs.com/package/gulp-newer) 和 [gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin) 。从命令行安装它们:

```
npm install gulp-newer gulp-imagemin --save-dev 
```

我们现在可以在`gulpfile.js`的顶部引用这两个模块:

```
// Gulp.js configuration

const
  // modules
  gulp = require('gulp'),
  newer = require('gulp-newer'),
  imagemin = require('gulp-imagemin'), 
```

我们现在可以在`gulpfile.js`的末尾定义一个图像处理函数:

```
// image processing
function images() {

  const out = build + 'images/';

  return gulp.src(src + 'images/**/*')
    .pipe(newer(out))
    .pipe(imagemin({ optimizationLevel: 5 }))
    .pipe(gulp.dest(out));

});
exports.images = images; 
```

所有的任务在语法上都是相似的。该代码执行以下操作:

1.  它创建了一个名为`images`的新任务函数。
2.  它定义了一个`out`文件夹，构建文件将位于其中。
3.  它从`simg/`源文件夹中读取文件流。`**/*`确保子文件夹中的图像也被处理。
4.  它通过管道将所有文件传送到`gulp-newer`模块。比相应的目标文件更新的源文件被传递。其他的都被删除了。
5.  它通过`gulp-imagemin`管道传输剩余的新的/更改的文件，这设置了一个可选的`optimizationLevel`参数。
6.  它将压缩图像输出到 Gulp `deimg/`文件夹。
7.  它导出一个调用`images`函数的公共`images`任务。

在从命令行运行任务之前，保存`gulpfile.js`并在项目的`simg/`文件夹中放置一些图像:

```
gulp images 
```

所有图像都相应地进行了压缩，您将看到如下输出:

```
Using file gulpfile.js
Running 'imagemin'...
Finished 'imagemin' in 5.71 ms
gulp-imagemin: image1.png (saved 48.7 kB)
gulp-imagemin: image2.jpg (saved 36.2 kB)
gulp-imagemin: image3.svg (saved 12.8 kB) 
```

尝试再次运行`gulp images`；没有文件被处理，因为没有图像文件被更改。

### HTML 任务

我们现在可以创建一个类似的任务，从源 HTML 文件夹中复制文件。我们可以使用 [gulp-htmlclean](https://www.npmjs.com/package/gulp-htmlclean) 插件安全地缩小 HTML 代码，删除不必要的空白和属性。

还将安装 [gulp-noop](https://www.npmjs.com/package/gulp-noop) 插件。这不执行任何操作，对于简单的开发/生产处理决策非常有用:

```
npm install gulp-htmlclean gulp-noop --save-dev 
```

这些模块加载在`gulpfile.js`的顶部:

```
const
  // modules
  gulp = require('gulp'),
  noop = require('gulp-noop'),
  newer = require('gulp-newer'),
  imagemin = require('gulp-imagemin'),
  htmlclean = require('gulp-htmlclean'), 
```

我们现在可以在`gulpfile.js`的末尾导出一个`html`函数:

```
// HTML processing
function html() {
  const out = build + 'html/';

  return gulp.src(src + 'html/**/*')
    .pipe(newer(out));
    .pipe(devBuild ? noop() : htmlclean())
    .pipe(gulp.dest(out));
}
exports.html = gulp.series(images, html); 
```

这重用了`gulp-newer`并引入了几个概念:

1.  只有当`NODE_ENV`被设置为`production`时，我们才通过`gulp-htmlclean`传输 HTML。因此，HTML 在开发过程中保持未压缩状态，这对于调试可能很有用。
2.  导出的`html`任务使用`gulp.series()`来组合一个接一个执行的任务。在这种情况下，`images()`函数在`html()`之前运行，这确保了 HTML 文件可以引用图像。

保存`gulpfile.js`并从命令行运行`gulp html`。`html`和`images`任务都将运行。

### JavaScript 任务

对你来说太容易了？让我们通过构建一个基本的模块捆绑器来处理 JavaScript 文件。它将:

1.  确保首先使用 [gulp-deporder](https://www.npmjs.com/package/gulp-deporder) 插件加载依赖项。这将分析每个脚本顶部的注释，以确保正确的排序——比如`// requires: defaults.js lib.js`。
2.  使用 [gulp-concat](https://www.npmjs.com/package/gulp-concat) 将所有脚本文件连接成一个单独的`main.js`文件。
3.  在生产模式下运行时，用[删除所有`console`和`debugging`语句。](https://www.npmjs.com/package/gulp-strip-debug)
4.  使用与 ES6 兼容的 [gulp-terser](https://www.npmjs.com/package/gulp-terser) 最小化代码。
5.  使用 [gulp-sourcemaps](https://www.npmjs.com/package/gulp-sourcemaps) 在开发模式下运行时追加一个源地图。

安装插件模块:

```
npm install gulp-deporder gulp-concat gulp-strip-debug gulp-terser gulp-sourcemaps --save-dev 
```

然后将它们加载到`gulpfile.js`的顶部:

```
const
  ...
  concat = require('gulp-concat'),
  deporder = require('gulp-deporder'),
  terser = require('gulp-terser'),
  stripdebug = devBuild ? null : require('gulp-strip-debug'),
  sourcemaps = devBuild ? require('gulp-sourcemaps') : null, 
```

*注:`gulp-strip-debug`和`gulp-sourcemaps`模块只在开发模式下加载，以提高效率。*

接下来，导出一个新的`js`任务函数:

```
// JavaScript processing
function js() {

  return gulp.src(src + 'js/**/*')
    .pipe(sourcemaps ? sourcemaps.init() : noop())
    .pipe(deporder())
    .pipe(concat('main.js'))
    .pipe(stripdebug ? stripdebug() : noop())
    .pipe(terser())
    .pipe(sourcemaps ? sourcemaps.write() : noop())
    .pipe(gulp.dest(build + 'js/'));

}
exports.js = js; 
```

该函数与其他任务非常相似，但是在代码转换之前调用`sourcemap.init()`函数，在代码转换完成之后调用`sourcemaps.write()`。

保存，在`src/js/`文件夹中添加几个 JavaScript 文件，然后运行`gulp js`来观看神奇的事情发生！

### CSS 任务

最后，让我们创建一个 CSS 任务，使用 [gulp-sass](https://www.npmjs.com/package/gulp-sass) 将 Sass `.scss`文件编译成一个单独的`.css`文件。这是一个 [node-sass](https://www.npmjs.com/package/node-sass) 的 Gulp 插件，绑定了 sass 引擎的超快 [LibSass C/C++端口。我们假设你的主 Sass 文件`scss/main.scss`负责加载所有部分。](http://sass-lang.com/libsass)

我们的任务还将通过 [gulp-postcss](https://github.com/postcss/gulp-postcss) 插件利用精彩的 [PostCSS](http://postcss.org/) 。PostCSS 需要自己的插件集，我们将安装这些插件:

*   [postcss-assets](https://www.npmjs.com/package/postcss-assets) 管理资产。这允许我们使用属性，如`background: resolve('image.png');`来解析文件路径或`background: inline('image.png');`来内联数据编码的图像。
*   [autoprefixer](https://www.npmjs.com/package/autoprefixer) 自动给 CSS 属性添加厂商前缀。
*   [css-mqpacker](https://www.npmjs.com/package/css-mqpacker) 将对同一 css 媒体查询的多个引用打包到一个规则中。
*   [cssnano](https://www.npmjs.com/package/cssnano) 在生产模式下运行时缩减 CSS 代码。

这些插件大多接受参数；有关更多信息，请参考他们的文档。

最后，当再次使用 [gulp-sourcemaps](https://www.npmjs.com/package/gulp-sourcemaps) 在开发模式下运行时，一个源地图将被附加到 CSS 文件中。

安装所有模块:

```
npm install gulp-sass gulp-postcss postcss-assets autoprefixer css-mqpacker cssnano --save-dev 
```

将它们加载到`gulpfile.js`的顶部:

```
const
  ...
  sass = require('gulp-sass'),
  postcss = require('gulp-postcss'),
  assets = require('postcss-assets'),
  autoprefixer = require('autoprefixer'),
  mqpacker = require('css-mqpacker'),
  cssnano = require('cssnano'), 
```

我们现在可以在`gulpfile.js`结束时导出一个新的`css`任务。注意，`images`任务被设置为依赖项，因为`postcss-assets`插件可能在构建过程中引用图像:

```
// CSS processing
function css() {

  return gulp.src(src + 'scss/main.scss')
    .pipe(sourcemaps ? sourcemaps.init() : noop())
    .pipe(sass({
      outputStyle: 'nested',
      imagePath:img/',
      precision: 3,
      errLogToConsole: true
    }).on('error', sass.logError))
    .pipe(postcss([
      assets({ loadPaths: ['images/'] }),
      autoprefixer({ browsers: ['last 2 versions', '> 2%'] }),
      mqpacker,
      cssnano
    ]))
    .pipe(sourcemaps ? sourcemaps.write() : noop())
    .pipe(gulp.dest(build + 'css/'));

}
exports.css = gulp.series(images, css); 
```

注意`.on('error', sass.logError)`确保 Sass 在不停止 Gulp 任务的情况下向控制台输出语法错误。

保存文件，添加适当的 Sass `.scss`文件，并从命令行运行任务:

```
gulp css 
```

## 第六步:自动化任务

我们一直在一次运行一个任务。我们可以通过在`gulpfile.js`中导出一个`build`任务，在一个命令中运行它们:

```
// run all tasks
exports.build = gulp.parallel(exports.html, exports.css, exports.js); 
```

`gulp.parallel()`方法同时运行任务。它可以与`gulp.series()`结合来创建复杂的依赖链。在这个例子中，`exports.html`、`exports.css`和`exports.js`是并行运行的，但是它们中的每一个都可能有依赖关系序列——包括`images`任务。

保存并在命令行输入`gulp build`执行所有任务。

这是不是太辛苦了？Gulp 提供了一个 [`.watch()`](https://gulpjs.com/docs/en/api/watch) 方法，可以监控源文件，并在文件发生变化时运行适当的任务。它传递了一组要监控的文件/文件夹、任何选项(此处未使用)和要运行的任务函数(可选地在`gulp.series()`和/或`gulp.parallel()`方法中)。

让我们在`gulpfile.js`结束时导出一个新的`watch`任务:

```
// watch for file changes
function watch(done) {

  // image changes
  gulp.watch(src + 'images/**/*', images);

  // html changes
  gulp.watch(src + 'html/**/*', html);

  // css changes
  gulp.watch(src + 'scss/**/*', css);

  // js changes
  gulp.watch(src + 'js/**/*', js);

  done();

}
exports.watch = watch; 
```

[Gulp 需要知道任务功能何时完成](https://gulpjs.com/docs/en/getting-started/async-completion)。这通常是通过返回一个 Gulp 流来处理的，但是也可以是一个 JavaScript Promise、事件发射器、observable、子进程或回调。这里，我们使用一个名为`done()`的回调来表示所有的`watch()`任务都已经配置好了。

与其立即运行`gulp watch`,不如让我们添加一个默认任务，该任务可以通过运行`gulp`来执行，无需更多参数:

```
// default task
exports.default = gulp.series(exports.build, exports.watch); 
```

保存`gulpfile.js`并在命令行输入`gulp`。您的图像、HTML、CSS 和 JavaScript 将被处理，然后 Gulp 将保持活跃，监视更新并在必要时重新运行任务。点击`Ctrl/Cmd + C`中止监控并返回命令行。

## 第七步:盈利！

您可能会发现有用的其他插件:

*   [gulp-load-plugins](https://www.npmjs.com/package/gulp-load-plugins) :加载所有 gulp 插件模块，不需要`require`声明
*   [吞咽预处理](https://www.npmjs.com/package/gulp-preprocess):一个简单的 HTML 和 JavaScript [预处理](https://github.com/jsoverson/preprocess)
*   或者[无吞咽](https://www.npmjs.com/package/gulp-less):无[CSS 预处理器](http://lesscss.org/)插件
*   [gulp-stylus](https://www.npmjs.com/package/gulp-stylus) :手写笔 CSS 前处理器插件
*   [gulp-size](https://www.npmjs.com/package/gulp-size) :显示文件大小和节省
*   [gulp-nodemon](https://www.npmjs.com/package/gulp-nodemon) :当发生变化时，使用 [nodemon](https://www.npmjs.com/package/nodemon) 自动重启 Node.js 应用程序

Gulp 任务可以运行任何 JavaScript 代码或 Node.js 模块。它们不一定是插件，例如:

*   [浏览器同步](https://www.npmjs.com/package/browser-sync):发生变化时自动重新加载资产或刷新浏览器
*   [del](https://www.npmjs.com/package/del) :删除文件和文件夹(也许在每次运行开始时清理你的`build`文件夹)

投入一点时间，Gulp 可以节省许多开发时间。优点:

*   [外挂丰富](https://npmjs.org/browse/keyword/gulpplugin/)
*   使用管道的配置可读性强，易于理解
*   `gulpfile.js`可以在其他项目中修改和重复使用
*   您的总页面权重可以减少，以提高性能
*   您可以简化您的部署
*   编码`gulpfile.js`很有趣(嗯，比其他任务运行者使用的 JSON 配置更有趣)

有用的链接:

*   [大口首页](http://gulpjs.com/)
*   [Gulp 插件](https://npmjs.org/browse/keyword/gulpplugin/)
*   [npm 主页](https://www.npmjs.com/)

将上面的过程应用到一个简单的网站上，总重量减少了 50%以上。你可以使用[页面权重分析工具](https://www.sitepoint.com/10-best-webpage-weight-analysis-tools/)来测试你自己的结果。

Gulp 仍然是自动任务运行的一个很好的选择，可以彻底改变你的工作流程。我希望你觉得这个指南有用，并考虑你的生产过程吞咽。

## 分享这篇文章
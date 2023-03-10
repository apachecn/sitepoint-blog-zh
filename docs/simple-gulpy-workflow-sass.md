# Sass 的简单工作流程

> 原文：<https://www.sitepoint.com/simple-gulpy-workflow-sass/>

我最近负责优化一个相当大的 Rails 项目的 Sass 部分，要做的最重要的事情之一就是改善编译时间。由于现有的 Sass 体系结构，以及 Ruby Sass(在本例中通过 Rails 资产管道)在处理大量文件时速度较慢的事实，编译样式表可能需要 40 秒。谈论一个快速的开发过程。:)

我的想法是远离资产管道，拥抱 [LibSass](https://github.com/sass/libsass) 的速度。为了让事情更简单，我决定采用简单的[大口](http://gulpjs.com/)工作流程。这是我第一次使用 Gulp，我必须说这是一次非常愉快的经历(就我而言，Grunt 不是这种情况)。

在这篇短文中，让我们快速浏览一下如何建立一个使用 Sass 的工作流。以下是我们将包括的内容:

*   不出所料，用 LibSass 编译 Sass
*   生成易于调试的源地图
*   在 CSS 前面加上[自动前缀](https://github.com/postcss/autoprefixer)
*   使用 [SassDoc](http://sassdoc.com) 生成 Sass 文档

## 编译 Sass

首先要做的是安装依赖项并创建一个`Gulpfile.js`。我们将需要 Gulp(没错，夏洛克)，但也需要 [gulp-sass](https://github.com/dlmanning/gulp-sass) 来编译我们的样式表:

```
$ npm install gulp gulp-sass --save-dev
```

这一行告诉 [npm](http://npmjs.com) 安装`gulp`和`gulp-sass`包作为开发依赖。你现在可以在你的`package.json`的`devDependencies`对象中找到它们。而`Gulpfile.js`:

```
var gulp = require('gulp');
var sass = require('gulp-sass');
```

哇，真短。我们现在需要的是一个*任务*来在我们的样式表文件夹上运行 Sass(实际上是`gulp-sass`)。

```
var input = './stylesheets/**/*.scss';
var output = './public/css';

gulp.task('sass', function () {
  return gulp
    // Find all `.scss` files from the `stylesheets/` folder
    .src(input)
    // Run Sass on those files
    .pipe(sass())
    // Write the resulting CSS in the output folder
    .pipe(gulp.dest(output));
});
```

就是这样！多亏了一个非常小的 Gulp 任务，我们现在可以使用 LibSass 编译我们的样式表了。那又怎么样？我们可以将选项传递给`gulp-sass`以在扩展模式下编译样式表，并在控制台中打印错误:

```
var sassOptions = {
  errLogToConsole: true,
  outputStyle: 'expanded'
};

gulp.task('sass', function () {
  return gulp
    .src(input)
    .pipe(sass(sassOptions).on('error', sass.logError))
    .pipe(gulp.dest(output));
});
```

## 添加源地图

到目前为止，一切顺利。现在，如何生成源地图呢？如果您不知道什么是 sourcemaps，它基本上是一种将压缩的生产源与扩展的开发源进行映射的方法，以便更容易地调试实时代码。它们完全不局限于 CSS，sourcemaps 也可以用在 JavaScript 中。

我们在 SitePoint 有一篇关于 sourcemaps [的好文章。如果你对 sourcemaps 的理解有所欠缺，请在继续之前随意阅读。](https://www.sitepoint.com/using-source-maps-debug-sass-chrome/)

好的，所以要将 sourcemaps 生成添加到我们的任务中，我们需要安装 [gulp-sourcemaps](https://github.com/floridoo/gulp-sourcemaps) :

```
$ npm install gulp-sourcemaps --save-dev
```

现在让我们优化我们的任务:

```
var gulp = require('gulp');
var sass = require('gulp-sass');
var sourcemaps = require('gulp-sourcemaps');

// ... variables

gulp.task('sass', function () {
  return gulp
    .src(input)
    .pipe(sourcemaps.init())
    .pipe(sass(sassOptions).on('error', sass.logError))
    .pipe(sourcemaps.write())
    .pipe(gulp.dest(output));
});
```

默认情况下，`gulp-sourcemaps`在编译后的 CSS 文件中以内联方式写入源地图。根据项目设置，我们可能希望将它们写在单独的文件中，在这种情况下，我们可以在`sourcemaps.write()`函数中指定相对于`gulp.dest()`目的地的路径，如下所示:

```
gulp.task('sass', function () {
  return gulp
    .src(input)
    .pipe(sourcemaps.init())
    .pipe(sass(sassOptions).on('error', sass.logError))
    .pipe(sourcemaps.write('./stylesheets/maps'))
    .pipe(gulp.dest(output));
});
```

## 把自动修复机带到派对上

我不会详细说明为什么使用  比手工编写 vendor(或者使用 mixin，基本上是一样的东西)更好，但大致上 Autoprefixer 是一个后处理步骤，意味着它实际上更新了已经编译的样式表，以根据最新的数据库和给定的配置添加相关的前缀。换句话说，您告诉 Autoprefixer 您想要支持哪些浏览器，它只向样式表添加相关的前缀。零努力，完美支持(请提醒我给这个口头禅申请专利)。

要将 Autoprefixer 包含在我们的 Gulp 工作流中，我们只需要它在 Sass 完成它的工作后*通过管道*传递它。然后 Autoprefixer 更新样式表来添加前缀。

首先，让我们安装它(现在您已经了解了要点):

```
$ npm install gulp-autoprefixer --save-dev
```

然后我们将它添加到我们的任务中:

```
var gulp = require('gulp');
var sass = require('gulp-sass');
var sourcemaps = require('gulp-sourcemaps');
var autoprefixer = require('gulp-autoprefixer');

// ... variables

gulp.task('sass', function () {
  return gulp
    .src(input)
    .pipe(sourcemaps.init())
    .pipe(sass(sassOptions).on('error', sass.logError))
    .pipe(sourcemaps.write())
    .pipe(autoprefixer())
    .pipe(gulp.dest(output));
});
```

现在，我们使用 Autoprefixer 的默认配置运行，即

*   市场份额超过 1%的浏览器，
*   所有浏览器的最后两个版本，
*   火狐 ESR，
*   Opera 12.1

我们可以像这样使用我们自己的配置:

```
var gulp = require('gulp');
var sass = require('gulp-sass');
var sourcemaps = require('gulp-sourcemaps');
var autoprefixer = require('gulp-autoprefixer');

// ... variables
var autoprefixerOptions = {
  browsers: ['last 2 versions', '> 5%', 'Firefox ESR']
};

gulp.task('sass', function () {
  return gulp
    .src(input)
    .pipe(sourcemaps.init())
    .pipe(sass(sassOptions).on('error', sass.logError))
    .pipe(sourcemaps.write())
    .pipe(autoprefixer(autoprefixerOptions))
    .pipe(gulp.dest(output));
});
```

## 放开文件。

最后但同样重要的是，添加到我们工作流中的工具，使用 [SassDoc](http://sassdoc.com) 生成 Sass 文档。SassDoc 之于 Sass 就像 JSDoc 之于 JavaScript:一个文档工具。它解析你的样式表，寻找记录变量、混合、函数和占位符的注释块。

如果您的项目使用 SassDoc(它应该！)，您可以在 Gulp 工作流中添加自动文档生成。

SassDoc 很酷的一点是，它可以直接通过管道传输到 Gulp 中，因为它的 API 是 Gulp 兼容的。所以你实际上没有一个`gulp-sassdoc`插件。

```
npm install sassdoc --save-dev
```

```
var gulp = require('gulp');
var sass = require('gulp-sass');
var sourcemaps = require('gulp-sourcemaps');
var autoprefixer = require('gulp-autoprefixer');
var sassdoc = require('sassdoc');

// ... variables

gulp.task('sass', function () {
  return gulp
    .src(input)
    .pipe(sourcemaps.init())
    .pipe(sass(sassOptions).on('error', sass.logError))
    .pipe(sourcemaps.write())
    .pipe(autoprefixer(autoprefixerOptions))
    .pipe(gulp.dest(output))
    .pipe(sassdoc())
    // Release the pressure back and trigger flowing mode (drain)
    // See: http://sassdoc.com/gulp/#drain-event
    .resume();
});
```

注意，根据项目的大小和文档项的数量，运行 SassDoc 可能需要几秒钟的时间(据我所知很少超过 3 秒)，因此您可能希望为此创建一个单独的任务。

```
gulp.task('sassdoc', function () {
  return gulp
    .src(input)
    .pipe(sassdoc())
    .resume();
});
```

同样，我们使用默认配置，但是如果我们愿意，也可以使用我们自己的。

```
var sassdocOptions = {
  dest: './public/sassdoc'
};

gulp.task('sassdoc', function () {
  return gulp
    .src(input)
    .pipe(sassdoc(sassdocOptions))
    .resume();
});
```

## 我在看着你

在离开之前，我们还可以做一件事:创建一个`watch`任务。这项任务的重点是观察样式表的变化，以便再次重新编译它们。当在项目的 Sass 端工作时，这是非常方便的，因此您不必在每次保存文件时手动运行`sass`任务。

```
gulp.task('watch', function() {
  return gulp
    // Watch the input folder for change,
    // and run `sass` task when something happens
    .watch(input, ['sass'])
    // When there is a change,
    // log a message in the console
    .on('change', function(event) {
      console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
    });
});
```

我建议不要在`sass`任务中包含 SassDoc 的另一个原因是:您可能不想每次接触样式表时都重新生成文档。这可能是您希望在构建或推送时做的事情，可能需要使用预提交挂钩。

## 添加最后一笔

最后，也是最重要的，要考虑的事情是:在默认任务中运行`sass`。

```
gulp.task('default', ['sass', 'watch' /*, possible other tasks... */]);
```

作为`task(..)`函数的第二个参数传递的数组是一个依赖任务列表。基本上，它告诉 Gulp 在运行作为第三个参数指定的任务(如果有的话)之前运行这些任务。

此外，我们可能会创建一个`prod`任务，它可以在部署到生产环境之前运行(可能使用 git 挂钩)。这项任务应该:

*   以压缩模式编译 Sass
*   用 Autoprefixer 作为 CSS 的前缀
*   重新生成 SassDoc 文档
*   避免任何源地图

```
gulp.task('prod', ['sassdoc'], function () {
  return gulp
    .src(input)
    .pipe(sass({ outputStyle: 'compressed' }))
    .pipe(autoprefixer(autoprefixerOptions))
    .pipe(gulp.dest(output));
});
```

## 最后的想法

就是这样，伙计们！仅用几分钟时间和几行 JavaScript，我们就成功创建了一个强大的小 Gulp 工作流。你可以在这里找到完整的文件。你会给它添加什么？

## 分享这篇文章
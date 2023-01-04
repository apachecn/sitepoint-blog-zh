# 如何使用 Metalsmith 创建静态站点

> 原文：<https://www.sitepoint.com/create-static-site-metalsmith/>

我之前的帖子讨论了为什么你[应该](https://www.sitepoint.com/7-reasons-use-static-site-generator/)或者[不应该](https://www.sitepoint.com/7-reasons-not-use-static-site-generator/)考虑静态站点生成器。总之，静态站点生成器从模板和通常包含在降价文件中的原始数据构建纯 HTML 页面文件。它提供了 CMS 的一些好处，而没有托管、性能和安全开销。

静态站点可能适用于一系列项目，包括:

*   小型网站或个人博客。拥有几十个页面、不常见的帖子和一两个作者的网站可能是理想的。
*   REST API 之类的技术文档。
*   需要一系列网页视图的应用程序原型。
*   电子书——Markdown 文件可以转换成 PDF 或其他格式以及 HTML 格式。

本质上，静态站点生成器是一个构建工具。你可以用它来运行任务或搭建项目，就像你用[咕噜](http://gruntjs.com/)或[吞咽](http://gulpjs.com/)一样。

## 为什么选择 Metalsmith？

无可争议的静态网站冠军是 Jekyll，一个 2008 年启动的 Ruby 项目。使用 Jekyll 不一定需要 Ruby 专业知识，但它会有所帮助。幸运的是，大多数流行语言都有一个[范围广泛的开源静态站点生成器](http://www.staticgen.com/)。JavaScript 选项包括 [Hexo](http://hexo.io/) 、 [Harp](http://harpjs.com/) 和 [Assemble](http://assemble.io/) 。对于更简单的项目，你也可以使用像[gupp](http://gulpjs.com/)这样的构建工具。

我选择 [Metalsmith](http://metalsmith.io/) 学习本教程，因为它:

1.  不是针对特定的项目类型，如博客
2.  支持广泛的模板和数据格式选项
3.  轻量级
4.  几乎没有依赖性
5.  使用模块化结构
6.  提供简单的插件架构，并且
7.  很容易上手。

为该教程建立了一个演示网站。它不会赢得任何设计奖项，但它阐明了基本概念。可以从 [GitHub 库](https://github.com/sitepoint-editors/metalsmith-demo)检查和安装 Metalsmith 构建代码。或者，您可以按照这里的说明创建自己的基本站点。

我使用过几次 metal Smith——请不要认为这是构建每个静态站点的最终方法！

## 安装金属史密斯

确保您已经安装了 [Node.js](https://nodejs.org/) (例如使用 nvm 的[)，然后创建一个新的项目目录，例如`project`并初始化您的`package.json`文件:](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)

```
cd project && cd project
npm init -y 
```

现在安装 Metalsmith 和我们将用来建立我们的网站的各种插件。这些是:

*   [metalsmith-资产](https://www.npmjs.com/package/metalsmith-assets)-包括您的 metal Smith 版本中的静态资产
*   [metal Smith-浏览器同步](https://www.npmjs.com/package/metalsmith-browser-sync)-将浏览器同步整合到您的工作流程中
*   [metalsmith-collections](https://www.npmjs.com/package/metalsmith-collections) —将文件集合添加到全局元数据中
*   [metalsmith-feed](https://www.npmjs.com/package/metalsmith-feed) —为集合生成一个 RSS feed
*   [metal Smith-html-minifier](https://www.npmjs.com/package/metalsmith-html-minifier)—使用 kangax/html-minifier 缩小 HTML 文件
*   [metal Smith-in-place](https://www.npmjs.com/package/metalsmith-in-place)-在源文件中呈现模板语法
*   [metal Smith-布局](https://www.npmjs.com/package/metalsmith-layouts)-将布局应用于源文件
*   [metalsmith-mapsite](https://www.npmjs.com/package/metalsmith-mapsite) —生成一个 sitemap.xml 文件
*   [metal Smith-降价](https://www.npmjs.com/package/metalsmith-markdown)-转换降价文件
*   [metalsmith-permalinks](https://www.npmjs.com/package/metalsmith-permalinks) —对文件应用自定义的 permalink 模式
*   [metalsmith-publish](https://www.npmjs.com/package/metalsmith-publish) —添加对草稿、私人和未来日期帖子的支持
*   [metalsmith-word-count](https://www.npmjs.com/package/metalsmith-word-count) —计算 HTML 文件中所有段落的字数/平均阅读时间

```
npm install --save-dev metalsmith metalsmith-assets metalsmith-browser-sync metalsmith-collections metalsmith-feed metalsmith-html-minifier metalsmith-in-place metalsmith-layouts metalsmith-mapsite metalsmith-markdown metalsmith-permalinks metalsmith-publish metalsmith-word-count handlebars 
```

## 项目结构

我们将对项目中的源(`src`)和构建(`build`)目录使用以下结构。

您可以如下所述创建您的示例文件，或者直接从[演示 src 目录](https://github.com/sitepoint-editors/metalsmith-demo/tree/master/src)中复制它们。

### 页

页面降价文件包含在`src/html`中。这可以包含每个网站部分的一级子目录，即

*   `src/html/start` —以特定顺序描述项目的页面
*   `src/html/article` —按时间倒序排列的各类文章
*   `src/html/contact` —单个联系人页面

每个目录包含一个单独的`index.md`文件，它是该部分的默认页面。其他页面可以使用任何唯一的名称。

构建过程会将这些文件转换成基于目录的永久链接，例如

*   `src/html/start/index.md`变成了`/start/index.html`
*   `src/html/start/installation.md`变成了`/start/installation/index.html`

每个降价文件都在顶部的`---`标记之间提供了内容和元信息，即所谓的“前沿信息”,例如

```
---
title: My page title
description: A description of this page.
layout: page.html
priority: 0.9
date: 2016-04-19
publish: draft
---

This is a demonstration page.

## Example title
Body text. 
```

大多数前置事项是可选的，但您可以设置:

*   `priority`:一个介于 0(低)和 1(高)之间的数字，我们将使用它来排列菜单和定义 XML 站点地图。
*   `publish`:可设置为`draft`、`private`或未来日期，以确保在需要时才发布。
*   `date`:文章的日期。如果没有设置，我们将使用任何未来的发布日期或文件创建日期。
*   `layout`:要使用的 HTML 模板。

### 模板

HTML 页面模板包含在`src/template`中。定义了两个模板:

*   `src/html/template/page.html`[默认布局](https://github.com/sitepoint-editors/metalsmith-demo/blob/master/src/template/page.html)
*   `src/html/template/article.md`一个[文章布局](https://github.com/sitepoint-editors/metalsmith-demo/blob/master/src/template/article.html)显示日期、下一页/上一页链接等。

使用[手柄](http://handlebarsjs.com/)模板系统，尽管支持替代选项。一个典型的模板需要一个`{{{ contents }}}`标签来包含页面内容以及任何前置值，比如`{{ title }}`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    {{> meta }}
  </head>
  <body>

  {{> header }}

  <main>
    <article>

      {{#if title}}
        <h1>{{ title }}</h1>
      {{/if}}

      {{{ contents }}}

    </article>
  </main>

  {{> footer }}

</body>
</html> 
```

对`{{> meta }}`、`{{> header }}`和`{{> footer }}`的引用是部分的…

### 部分的

片段——或 HTML 片段文件——包含在`src/partials`中。这些通常在模板中使用，但也可以使用代码包含在内容页面中:

```
{{> partialname }} 
```

其中`partialname`是`src/partials`目录中文件的名称。

### 静态资产

图像、CSS 和 JavaScript 文件等静态资产包含在`src/assets`中。所有文件和子目录将被复制到网站的根目录。

### 自定义插件

构建网站所需的定制插件包含在`lib`目录中。

### 构建目录

网站将建在`build`目录下。我们将以两种方式构建网站:

*   **开发模式:**不缩小 HTML，启动测试 web 服务器。
*   **生产模式:**如果`NODE_ENV`设置为`production`，则`build`目录被擦除，生成最终的缩小文件。

## 定义您的第一个构建文件

可以在项目目录的根目录下创建一个名为`build.js`的基本示例:

```
// basic build

'use strict';

var
  metalsmith = require('metalsmith'),
  markdown   = require('metalsmith-markdown'),

  ms = metalsmith(__dirname) // the working directory
    .clean(true)            // clean the build directory
    .source('src/html/')    // the page source directory
    .destination('build/')  // the destination directory
    .use(markdown())        // convert markdown to HTML
    .build(function(err) {  // build the site
      if (err) throw err;   // and throw errors
    }); 
```

使用`node ./build.js`运行它，一个静态站点将在`build`目录中创建。降价将被解析成 HTML，但它将不可用，因为我们还没有在我们的构建过程中包括模板。

## Metalsmith 插件

从表面上看，Metalsmith 构建文件与 Gulp 中使用的文件相似(尽管它不使用流)。通过用任何适当的参数将插件传递给 Metalsmith `use`方法来调用插件。插件本身必须返回另一个接受三个参数的函数:

*   一个包含每页信息的`files`数组
*   包含元数据等全局信息的`metalsmith`对象，以及
*   插件完成工作时必须调用的一个`done`函数

这个简单的例子将所有元和页面信息记录到控制台(可以在`build.js`中定义):

```
function debug(logToConsole) {
  return function(files, metalsmith, done) {
    if (logToConsole) {
      console.log('\nMETADATA:');
      console.log(metalsmith.metadata());

      for (var f in files) {
        console.log('\nFILE:');
        console.log(files[f]);
      }
    }

    done();
  };
}; 
```

可以更新 Metalsmith 构建代码以使用此插件:

```
ms = metalsmith(__dirname) // the working directory
  .clean(true)             // clean the build directory
  .source('src/html/')     // the page source directory
  .destination('build/')   // the destination directory
  .use(markdown())         // convert Markdown to HTML
  .use(debug(true))        // *** NEW *** output debug information
  .build(function(err) {   // build the site
    if (err) throw err;    // and throw errors
  }); 
```

这个调试功能可能会帮助你创建你自己的定制插件，但你可能需要的大多数功能都已经写好了——在 [Metalsmith 网站](http://www.metalsmith.io)上有一个很长的插件列表。

## 打造更好的造型

下面解释了[示范站点构建文件](https://github.com/sitepoint-editors/metalsmith-demo/blob/master/build.js)的关键部分。

如果`NODE_ENV`环境变量已经被设置为`production`(Mac/Linux 上的`export NODE_ENV=production`或 Windows 上的`set NODE_ENV=production`)，则名为`devBuild`的变量被设置为`true`:

```
devBuild = ((process.env.NODE_ENV || '').trim().toLowerCase() !== 'production') 
```

主目录是在一个`dir`对象中定义的，所以我们可以重用它们:

```
dir = {
  base:   __dirname + '/',
  lib:    __dirname + '/lib/',
  source: './src/',
  dest:   './build/'
} 
```

将加载 Metalsmith 和插件模块。注意:

*   优秀的 [Browsersync](https://www.browsersync.io/) 测试服务器只在创建开发构建时需要
*   只有在创建生产版本时，才需要由`htmlmin`引用的 HTML 缩小器模块
*   已经定义了三个定制插件:`setdate`、`moremeta`和`debug`(下面会有更详细的解释)

```
metalsmith  = require('metalsmith'),
markdown    = require('metalsmith-markdown'),
publish     = require('metalsmith-publish'),
wordcount   = require("metalsmith-word-count"),
collections = require('metalsmith-collections'),
permalinks  = require('metalsmith-permalinks'),
inplace     = require('metalsmith-in-place'),
layouts     = require('metalsmith-layouts'),
sitemap     = require('metalsmith-mapsite'),
rssfeed     = require('metalsmith-feed'),
assets      = require('metalsmith-assets'),
htmlmin     = devBuild ? null : require('metalsmith-html-minifier'),
browsersync = devBuild ? require('metalsmith-browser-sync') : null,

// custom plugins
setdate     = require(dir.lib + 'metalsmith-setdate'),
moremeta    = require(dir.lib + 'metalsmith-moremeta'),
debug       = consoleLog ? require(dir.lib + 'metalsmith-debug') : null, 
```

用适用于每一页的信息定义一个`siteMeta`对象。重要的值是根据开发或生产版本设置的`domain`和`rootpath`:

```
siteMeta = {
  devBuild: devBuild,
  version:  pkg.version,
  name:     'Static site',
  desc:     'A demonstration static site built using Metalsmith',
  author:   'Craig Buckler',
  contact:  'https://twitter.com/craigbuckler',
  domain:    devBuild ? 'http://127.0.0.1' : 'https://rawgit.com',            // set domain
  rootpath:  devBuild ? null  : '/sitepoint-editors/metalsmith-demo/master/build/' // set absolute path (null for relative)
} 
```

还定义了一个`templateConfig`对象来设置模板默认值。这将被`metalsmith-in-place`和`metalsmith-layouts`插件使用，这两个插件支持使用手柄进行页面内和模板渲染:

```
templateConfig = {
  engine:     'handlebars',
  directory:  dir.source + 'template/',
  partials:   dir.source + 'partials/',
  default:    'page.html'
} 
```

Metalsmith 对象现在像以前一样被初始化，但是我们还将我们的`siteMeta`对象传递给`metadata`方法，以确保信息在每个页面上都可用。因此，我们可以在任何页面中引用`{{ name }}`等项目来获取站点名称。

```
var ms = metalsmith(dir.base)
  .clean(!devBuild)               // clean build before a production build
  .source(dir.source + 'html/')   // source directory (src/html/)
  .destination(dir.dest)          // build directory (build/)
  .metadata(siteMeta)             // add meta data to every page 
```

我们的第一个插件调用调用了`metalsmith-publish`,它删除了所有将前置`publish`值设置为`draft`、`private`或未来日期的文件:

```
.use(publish())                    // draft, private, future-dated 
```

`setdate`是一个自定义插件，包含在[lib/metal Smith-set date . js](https://github.com/sitepoint-editors/metalsmith-demo/blob/master/lib/metalsmith-setdate.js)中。它确保每个文件都有一个“日期”值集，即使没有在前端定义，只要有可能，通过回退到`publish`日期或文件创建时间:

```
.use(setdate())                    // set date on every page if not set in front-matter 
```

是最重要的插件之一，因为它根据每个页面在源目录中的位置或其他因素将每个页面分配到一个类别或分类。它可以使用前面的东西，如`date`或`priority`来重新排序文件，并允许你为该集合设置自定义元数据。该准则定义如下:

*   一个**开始对`src/html/start`目录中的每个文件进行**收集。它根据文件前端设置的`priority`值对它们进行排序。
*   一个针对`src/html/article`目录中每个文件的**文章**集合。它按照`date`倒序排列它们
*   每个名为`index.*`的默认页面的**页面**集合。它根据文件前端设置的`priority`值对它们进行排序。

```
 .use(collections({                  // determine page collection/taxonomy
   page: {
     pattern:    '**/index.*',
     sortBy:     'priority',
     reverse:    true,
     refer:      false
   },
   start: {
     pattern:    'start/**/*',
     sortBy:     'priority',
     reverse:    true,
     refer:      true,
     metadata: {
       layout:   'article.html'
     }
   },
   article: {
     pattern:    'article/**/*',
     sortBy:     'date',
     reverse:    true,
     refer:      true,
     limit:      50,
     metadata: {
       layout:   'article.html'
     }
   }
 })) 
```

接下来是 Markdown 到 HTML 的转换，接着是定义构建目录结构的`metalsmith-permalinks`插件。注意`:mainCollection`是由下面的`moremeta`为每个文件设置的:

```
 .use(markdown())                        // convert Markdown
 .use(permalinks({                       // generate permalinks
   pattern: ':mainCollection/:title'
 })) 
```

统计一篇文章的字数，并计算阅读大约需要多长时间。参数`{ raw: true }`只输出数字:

```
 .use(wordcount({ raw: true }))          // word count 
```

`moremeta`是包含在[lib/metal Smith-more meta . js](https://github.com/sitepoint-editors/metalsmith-demo/blob/master/lib/metalsmith-moremeta.js)中的另一个自定义插件。它向每个文件追加额外的元数据:

*   `root`:根目录的绝对或计算的相对文件路径
*   `isPage`:为名为`index.*`的默认节页面设置 true
*   `mainCollection`:主收藏名称，`start`或`article`
*   `layout`:如果没有设置，可以从主集合的元数据中确定布局模板
*   `navmain`:顶级导航对象的数组
*   `navsub`:二级导航对象数组

插件代码相对复杂，因为它处理导航。如果您需要一个更简单的层次结构，还有更简单的选项。

```
.use(moremeta())                          // determine root paths and navigation 
```

`metalsmith-in-place`和`metalsmith-layouts`插件分别控制页面内和模板布局。上面定义的同一个`templateConfig`对象被传递:

```
.use(inplace(templateConfig))             // in-page templating
.use(layouts(templateConfig));            // layout templating 
```

如果设置了`htmlmin`(在生产版本中)，我们可以缩小 HTML:

```
if (htmlmin) ms.use(htmlmin());           // minify production HTML 
```

`debug`是我们最终的自定义插件，包含在 [lib/metalsmith-debug.js](https://github.com/sitepoint-editors/metalsmith-demo/blob/master/lib/metalsmith-debug.js) 中。它类似于上面描述的`debug`功能:

```
if (debug) ms.use(debug());               // output page debugging information 
```

启动 [Browsersync](https://www.browsersync.io/) 测试服务器，这样我们就可以测试开发构建了。如果你以前没有使用过它，它看起来就像魔术一样:你的网站会在你每次做改变时神奇地刷新，当你在网站上滚动或导航时，两个或更多浏览器中的视图会同步:

```
if (browsersync) ms.use(browsersync({     // start test server
  server: dir.dest,
  files:  [dir.source + '**/*']
})); 
```

最后，我们可以使用:

*   `metalsmith-mapsite`生成 XML 站点地图
*   `metalsmith-feed`生成包含**文章**集合中页面的 RSS 提要
*   `metalsmith-assets`将文件和目录从`src/assets`直接复制到`build`而不做修改。

```
ms
  .use(sitemap({                          // generate sitemap.xml
    hostname:     siteMeta.domain + (siteMeta.rootpath || ''),
    omitIndex:    true
  }))
  .use(rssfeed({                          // generate RSS feed for articles
    collection:   'article',
    site_url:     siteMeta.domain + (siteMeta.rootpath || ''),
    title:        siteMeta.name,
    description:  siteMeta.desc
  }))
  .use(assets({                            // copy assets: CSS, images etc.
    source:       dir.source + 'assets/',
    destination:  './'
  })) 
```

剩下的就是创建网站的最后一步:

```
 .build(function(err) {                   // build
   if (err) throw err;
 }); 
```

一旦完成，您可以运行`node ./build.js`来再次构建您的静态站点。

## 抓住你了

我学到了很多建立一个简单的 Metalsmith 网站，但要注意以下问题:

### 不兼容的插件

插件可能会与其他插件冲突。例如，计算相对根路径的 [metalsmith-rootpath](https://github.com/radiovisual/metalsmith-rootpath) 与创建定制构建目录结构的 [metalsmith-permalinks](https://github.com/segmentio/metalsmith-permalinks) 不能很好地配合。我通过在[lib/metal Smith-more meta . js](https://github.com/sitepoint-editors/metalsmith-demo/blob/master/lib/metalsmith-moremeta.js)插件中编写自定义的`root`路径计算代码解决了这个问题。

### 插件顺序至关重要

插件可能会相互依赖，如果顺序不对，可能会发生冲突。例如，生成 RSS 的 [metalsmith-feed](https://github.com/hurrymaplelad/metalsmith-feed) 插件必须在 [metalsmith-layouts](https://github.com/superwolff/metalsmith-layouts) 之后调用，以确保 RSS XML 不会在页面模板中生成。

### 浏览器同步重建问题

当 [Browsersync](https://www.browsersync.io/) 运行并且文件被编辑时，集合被重新解析，但是旧的数据似乎仍然存在。这可能是自定义[lib/metal Smith-more meta . js](https://github.com/sitepoint-editors/metalsmith-demo/blob/master/lib/metalsmith-moremeta.js)插件的问题，但菜单和下一页/上一页链接会失去同步。要修复它，请用 Ctrl/Cmd + C 停止构建，然后重新启动构建。

## 你还需要吞咽吗？

那些使用任务管理器如 [Gulp](https://www.sitepoint.com/introduction-gulp-js/) 的人会注意到 Metalsmith 提供了一个熟悉的构建过程。有 [CSS 预处理用 Sass](https://github.com/stevenschobert/metalsmith-sass) 、[图像缩小](https://github.com/ahmadnassri/metalsmith-imagemin)、[文件拼接](https://github.com/aymericbeaumet/metalsmith-concat)、[丑化](https://github.com/ksmithut/metalsmith-uglify)等插件。对于更简单的工作流来说，这可能就足够了。

然而，Gulp 拥有更广泛的插件，并允许复杂的构建活动，如林挺、部署和使用[自动前缀器](https://github.com/postcss/autoprefixer)的 [PostCSS](http://postcss.org/) 处理。有几个 Gulp/Metalsmith 集成插件，虽然我遇到了一些问题，但它们应该不是必需的，因为 Gulp 任务可以直接运行 Metalsmith，例如

```
var
  gulp       = require('gulp'),
  metalsmith = require('metalsmith'),
  publish    = require('metalsmith-publish'),
  markdown   = require('metalsmith-markdown');

// build HTML files using Metalsmith
gulp.task('html', function() {

  var ms = metalsmith(dir.base)
    .clean(false)
    .source('src/html/')
    .destination('build')
    .use(publish())
    .use(markdown())
    .build(function(err) {
      if (err) throw err;
    });

}); 
```

这个过程防止了上面提到的 Browsersync 重建问题。记住使用`.clean(false)`确保 Metalsmith 在其他任务处于活动状态时不会清除构建文件夹。

## Metalsmith 适合你吗？

如果您有简单或高度定制的网站需求，Metalsmith 是理想的选择。也许可以在一个文档项目中尝试一下，一次添加一个特性。Metalsmith 不像 Jekyll 等替代产品那样功能齐全，但也不打算如此。你可能不得不编写自己的插件，但是这样做的容易程度对 JavaScript 开发者来说是一个巨大的好处。

创建一个 Metalsmith 构建系统需要时间，我们还没有考虑 HTML 模板和部署所涉及的工作。然而，一旦你有了一个工作流程，添加、编辑和删除降价文件就会变得非常简单。这比使用 CMS 更容易，而且你拥有静态站点的所有好处。

## 分享这篇文章
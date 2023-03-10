# 7 个 PostCSS 插件让你轻松进入 PostCSS

> 原文：<https://www.sitepoint.com/7-postcss-plugins-to-ease-you-into-postcss/>

在 SitePoint 上的之前，我们已经介绍过[postscs](https://www.sitepoint.com/an-introduction-to-postcss/)[许多](https://www.sitepoint.com/how-to-use-postcss-with-gulp/) [times](https://www.sitepoint.com/postcss-mythbusting/) [了，然而它仍然让许多人感到困惑。用一句话概括:](https://www.sitepoint.com/the-postcss-guide-to-improving-selectors-and-media-queries/)

> PostCSS 处理繁琐的工作，所以你不必。

它与预处理器(如 Sass、Less 和 Stylus)略有不同，后者提供了一种可编译为 CSS 的更简洁的编程语言。混乱的部分原因是:

*   它的名字。PostCSS 可以在预处理器将源代码编译成真正的 CSS 之前和/或之后对文件执行操作。

*   PostCSS *可以代替你的预处理器吗？有一些插件实现了诸如[变量](https://github.com/postcss/postcss-simple-vars)、[嵌套](https://github.com/postcss/postcss-nested)、[混合](https://github.com/postcss/postcss-mixins)和[扩展](https://github.com/davidtheclark/postcss-simple-extend)的构造。*

然而，虽然您可以[构建自己的预处理器](https://www.sitepoint.com/build-css-preprocessor-postcss/)，但除非您想限制功能并提高编译速度，否则没有理由这么做。就我个人而言，我使用 Sass 后面加上少量的 PostCSS 调料来增强我的 CSS。

## 你如何使用 PostCSS？

PostCSS 可以在独立的 JavaScript 文件中使用，Gulp、Grunt、花椰菜、早午餐和[我从未听说过的各种任务运行器](https://github.com/postcss/postcss#usage)！

就其本身而言， [PostCSS](http://postcss.org/) 除了将 CSS 文件解析成 JavaScript 对象和令牌之外什么也不做。真正神奇的事情发生在插件上，它们在最终的 CSS 文件编写之前检查、操作、添加或更改属性和值。

要使用 PostCSS，您需要设置您的项目，然后安装两个模块:

```
npm init
npm install --save-dev gulp gulp-postcss
```

然后你可以添加你需要的插件，例如 [autoprefixer](https://github.com/postcss/autoprefixer) 和 [cssnano](https://github.com/ben-eb/cssnano) :

```
npm install --save-dev autoprefixer cssnano
```

可以创建一个`gulpfile.js`。它定义了一个任务，该任务加载 CSS 源代码并通过 PostCSS 传输它。插件和任何必需的选项都以数组的形式传递给 PostCSS。最后，CSS 被输出到一个目标文件:

```
// Gulp.js configuration
var gulp = require('gulp'),
    postcss = require('gulp-postcss');

// apply PostCSS plugins
gulp.task('css', function() {
  return gulp.src('src/main.css')
    .pipe(postcss([
      require('autoprefixer')({}),
      require('cssnano')
    ]))
    .pipe(gulp.dest('dest/main.css'));
});
```

可以使用以下命令从控制台运行该任务:

```
gulp css
```

我们现在所需要的是一个方便的 PostCSS 插件列表…

## 1.[自动修复程序](https://github.com/postcss/autoprefixer)

如果你不用其他的，安装 [Autoprefixer](https://github.com/postcss/autoprefixer) :

```
npm install --save-dev autoprefixer
```

随着标准得到更好的支持，供应商选择基于标志的属性启用等替代方式，特定于浏览器的前缀如`-webkit-`、`-moz-`和`-ms-`正在逐渐消失。不幸的是，你不能避免厂商前缀，但是很难知道它们什么时候总是需要的(例如对于[用户选择](http://caniuse.com/#feat=user-select-none))，有时需要的(例如对于 [3D 转换](http://caniuse.com/#feat=transforms3d))，或者从不需要的(例如[边界半径](http://caniuse.com/#feat=border-radius))。

您再也不用担心使用 Autoprefixer 添加前缀了。你只需要定义无前缀的 CSS，然后声明你想要支持哪些浏览器。Autoprefixer 会在添加必要的前缀之前检查[caniuse.com](http://caniuse.com)——下面的代码指定了任何主流浏览器的最后两个版本，或者任何超过 2%市场份额的:

```
.pipe(postcss([
  require('autoprefixer')({
    browsers: ['last 2 versions', '> 2%']
  })
]))
```

这是一个优于预处理器库函数的选项，预处理器库函数通常需要特殊编码并应用厂商前缀。随着浏览器标准的发展，您的前缀代码将在以后的 PostCSS 运行中被删除。

Autoprefixer 是如此有用和广泛，可能你已经在使用它而没有意识到它是一个 PostCSS 插件。

## 2. [PostCSS 资产](https://github.com/assetsjs/postcss-assets)

PostCSS 资产提供了许多有用的图像处理功能:

```
npm install --save-dev postcss-assets
```

这些选项包括:

*   **URL 解析:**给定一个文件名，PostCSS Assets 用一个根路径或完全限定的 URL 替换`resolve(image)`。
*   **维度处理:** PostCSS 资产用一个像素等价物替换对`width(image)`、`height(image)`或`size(image)`的引用。
*   **图像内联:** PostCSS 资产用 Base64 编码的字符串替换`inline(image)`。
*   **缓存破坏:** PostCSS 资产可以将一个随机查询字符串附加到一个图像引用中，以确保加载最新的文件。

## 3\. [cssnext](http://cssnext.io/)

cssnext 允许您使用当今最新的 CSS 语法。

```
npm install --save-dev postcss-cssnext
```

该插件有一个很长的特性列表，包括支持:

*   `var`变量
*   `#rrggbbaa`颜色
*   颜色函数
*   过滤
*   后退

当它执行时，cssnext 将代码翻译成当今浏览器普遍支持的语法。

## 4.帆布背包

Rucksack 提供了一系列的功能，开发者声称这些功能让 CSS 开发再次变得有趣！

```
npm install --save-dev rucksack-css
```

选项包括:

*   响应式排版，通过一个`font-size: responsive`声明调整字体大小和行高。
*   Quantity 伪选择器，如`li:at-least(4)`，它针对任何包含四个或更多项目的列表。
*   属性别名，比如将`bg`定义为`background`，可以在整个 CSS 中使用。
*   一组预定义的缓动函数。

## 5\. [Stylelint](https://github.com/stylelint/stylelint)

Stylelint 基于 140 条规则报告 CSS 错误，这些规则旨在捕捉常见错误、实现样式约定并实施最佳实践。有许多选项可以根据您的喜好配置 Stylelint——Pavels Jelisejevs 的文章[使用 PostCSS](https://www.sitepoint.com/improving-the-quality-of-your-css-with-postcss/) 提高 CSS 的质量将带您完成设置过程。

## 6. [CSS MQPacker](https://github.com/hail2u/node-css-mqpacker)

MQPacker 尽可能将您的媒体查询优化为单一规则:

```
npm install --save-dev css-mqpacker
```

像 Sass 这样的预处理程序使得在声明中使用媒体查询变得很容易，例如

```
.widget1 {
  width: 100%;

  @media (min-width: 30em) {
    width: 50%;
  }

  @media (min-width: 60em) {
    width: 25%;
  }
}

.widget2 {
  width: 100px;

  @media (min-width: 30em) {
    width: 200px;
  }
} 
```

这将编译为:

```
.widget1 { width: 100%; }

@media (min-width: 30em) {
  .widget1 { width: 50%; }
}

@media (min-width: 60em) {
  .widget1 { width: 25%; }
}

.widget2 { width: 100px; }

@media (min-width: 30em) {
  .widget2 { width: 200px; }
} 
```

为了减小文件大小和(可能)缩短解析时间，MQPacker 将多个声明打包到一个`@media`规则中，即

```
.widget1 { width: 100%; }
.widget2 { width: 100px; }

@media (min-width: 30em) {
  .widget1 { width: 50%; }
  .widget2 { width: 200px; }
}

@media (min-width: 60em) {
  .widget1 { width: 25%; }
} 
```

***热点提示**:确保代码中的第一个媒体查询声明按照您想要的顺序定义了所有可能的选项，即使实际上没有区别。这保证 MQPacker 将以正确的顺序定义规则。*

MQPacker 还提供了对媒体查询进行排序和输出源映射的选项。

## 7. [cssnano](http://cssnano.co/)

cssnano 压缩您的 CSS 文件，以确保在您的生产环境中下载尽可能小。通过以下方式安装:

```
npm install --save-dev cssnano
```

该插件通过移除注释、空白、重复规则、过时的供应商前缀以及进行其他优化来节省至少 50%的空间。还有其他选择，但 cssnano 是最好的选择之一。用它！

## 想要更多吗？

postscs 插件的可搜索索引可从[postscs . parts](http://postcss.parts/)获得，而[postscs 使用文档](https://github.com/postcss/postcss#usage)提供了推荐选项列表。你会发现几乎任何你能想到的 CSS 任务的插件，尽管要注意有一些交叉和重复——例如，cssnext 也包括 Autoprefixer。

如果这还不够，您可以用 JavaScript 开发自己的 PostCSS 插件。PostCSS 文档解释了[如何编写插件](https://github.com/postcss/postcss/blob/master/docs/writing-a-plugin.md)，并提供了一个 [API 参考](https://github.com/postcss/postcss/blob/master/docs/api.md)。

无论您是否使用预处理器，PostCSS 都使 CSS 开发变得更加容易。它节省的 CSS 开发时间远远超过最初的安装和配置工作。

## 分享这篇文章
# 编写 CSS3 不用担心前缀

> 原文：<https://www.sitepoint.com/write-css3-without-worrying-prefixes/>

使用 CSS3 属性可以让你的网站在吸引力和交互性方面有一个巨大的提升。3D 变换、关键帧动画、背景渐变和列都是很好的工具，但是它们需要开发人员做额外的工作。各种浏览器实验性地支持它们，并要求这些属性具有特定于供应商的前缀。这里有一个例子来说明我的意思:

```
.rotated-element {
  -webkit-transform: rotate(45deg);
      -ms-transform: rotate(45deg);
          transform: rotate(45deg);
}
```

为了在现代浏览器(即 IE8 之后的任何浏览器)中使用这个`transform`属性，需要另外两个规则副本:`-webkit-`(用于 Safari、Mobile Safari 和最近的 Chrome)和`-ms-`(用于 IE9)。Firefox (16+)和 Chrome (36+)读取无前缀属性正确。

因此，使用大量 CSS3 属性的站点需要大量前缀。这引发了两个问题。首先，您如何知道使用哪些前缀？有时候，你会看到一个什么都用的网站(`-webkit-`、`-o-`、`-moz-`、`-ms-`和无前缀)。那只是膨胀。你可以在[上查找每一处房产，我能用](http://caniuse.com)吗，但那很费时间。第二个问题很简单，没有人想写所有这些前缀。

我们需要自动化这一点。让我们看看一些帮助我们自动化供应商前缀的工具。

## 预处理混合

如果你使用一个预处理器来编写你的 CSS，你可以使用一个 mixin 库来管理你的前缀。对于萨斯来说，主要的图书馆是[波旁](http://bourbon.io/)和[指南针](http://compass-style.org/)。对于少，有[少帽子](http://lesshat.madebysource.com/)和[少元素](http://lesselements.com/)。手写笔似乎有自己的一套[mixin 内置](https://learnboost.github.io/stylus/docs/mixins.html)。

虽然这些 mixin 库比手工添加前缀要好得多，但是有一个问题你应该注意:它们可能会给你比你需要的更多的前缀。浏览器不断更新(火狐和 Chrome 好像几乎每周都会发布新版本！)，并且这些更新可以包括对各种无前缀的 CSS3 属性的完全支持。你的 mixin 库如何跟踪新添加的支持，决定何时停止输出不必要的前缀？

一些图书馆不太注意，你会在不再需要前缀的几年后，用上不必要的前缀。如果你的牌库仍在`border-radius`或`box-shadow` 加上[前缀，它在这点上是有罪的。](http://jamessteinbach.com/css/sass/stop-using-worthless-sass-mixins/)

一些库只是每隔几个月推一次更新，并根据他们开发团队的决定删除前缀。

最好的 mixin 库允许你配置你需要支持的浏览器，并输出符合你的规格的前缀。指南针就是这种库。它依赖于来自[的数据，我能使用](http://caniuse.com)吗，并允许你用一些全局变量设置最小浏览器支持。查看他们关于这个[配置](http://compass-style.org/reference/compass/support/)的文档。使用[的`$supported-browsers`变量](http://compass-style.org/reference/compass/support/#const-supported-browsers)，你可以定义一个支持的浏览器列表。[`$critical-usage-threshold`变量](http://compass-style.org/reference/compass/support/#const-critical-usage-threshold)允许您设置浏览器支持的最小全局使用百分比(例如，如果浏览器包含至少 1%的全局使用，则添加它需要的任何前缀)。

### Mixin 库的问题

我个人并不使用 mixin 库，因为我有几个额外的顾虑。首先，库仍然让你编写比你需要的更多的代码。你需要写`@include transform(translateX(25%));`而不是`transform: translateX(25%);`。这是一个额外的单词和几个额外的符号。不算大，但是一天下来，它确实会让你慢下来。

我也不喜欢我必须学习一个库的语法而不是真正的 CSS 语法。我使用一个库已经几个月了，这时我突然意识到我不能马上写出某些 CSS3 属性的规范版本。我宁愿了解真正的 CSS，也不愿了解一个预处理器的 mixin 库。

## "后处理器"

这些工具包括[-无前缀](https://leaverou.github.io/prefixfree/au)和[自动前缀](https://github.com/postcss/autoprefixer)。有了这些，您就可以编写所有 CSS3 属性的标准规范版本，该工具稍后会为您添加前缀。这两个工具都不需要预处理程序，但是都非常适合预处理程序工作流。

-prefix-free 是由 [Lea Verou](http://lea.verou.me/) 开发的 JavaScript 工具。您可以像其他 JS 文件一样将它包含在页面中(gzip 压缩时只有 2KB，所以根本不是最重的文件！)并且它在客户端运行。-prefix-free 检测用户的浏览器，在`<link>`、`<style>`和 inline 中查找 CSS，并准确添加浏览器需要的前缀。这将节省您的时间，并使您的样式表更轻便。这种方法的缺点是它在客户端运行，所以你的用户可能会在重页面上看到渲染延迟或 <abbr title="Flash of Unstyled Content">FOUC</abbr> 。在样式表之后立即包含-prefix-free 有助于最小化延迟。

我用的工具是 Autoprefixer。Autoprefixer 在本地机器上运行，在样式表到达浏览器之前纠正前缀。你可以[把它作为红宝石](https://github.com/ai/autoprefixer-rails)安装，在[咕哝工作流程](https://github.com/postcss/autoprefixer#grunt)或者[大口工作流程](https://github.com/postcss/autoprefixer#gulp)中运行。我通常在终端中使用`compass watch`来编译 Sass，所以我将下面的 require 块添加到我的`config.rb`文件中，告诉 Compass 的编译器在 Sass 编译完成后立即运行 Autoprefixer (Ruby gem ):

```
require 'autoprefixer-rails'

on_stylesheet_saved do |file|
  css = File.read(file)
  File.open(file, 'w') do |io|
    io << AutoprefixerRails.process(css, browsers: ["last 3 versions"])
  end
end
```

如果您使用 Grunt 或 Gulp，我将假设您熟悉添加新任务，并继续讨论 Autoprefixer 的细节。

Autoprefixer 在一个`browsers`配置列表上运行:在这个列表中，你可以指定最近的版本号(`"last 3 versions"`)、特定的浏览器版本(`"Firefox > 20"`)或者全局使用(`"> 4%"`)。全球使用统计数据基于来自*的实时数据，我可以使用*吗？默认值为`"> 1%, last 2 versions, Firefox ESR, Opera 12.1"`。与-prefix-free 不同，Autoprefixer 会添加符合您的配置要求的每个前缀。这确实会导致样式表稍微重一点，但是因为不需要在客户端运行脚本而获得了速度，所以这可能是值得的。

Autoprefixer 还会清理不必要的前缀。如果您从对前缀有某种狂热的开发人员那里继承了一个项目，Autoprefixer 将删除您的`browsers`配置需求所不需要的任何前缀，使您的样式表自动变得更简洁！

如果你想对 Autoprefixer 有一个很好的介绍，看看这篇关于 CSS-Tricks 的文章，作者是该工具的创造者。

## 结论

CSS3 属性非常棒，你不需要浪费时间为它们写前缀。无论是 mixin 库还是后处理工具更适合您的工作流程，您都可以自动化这部分编码并完成更多工作！

## 分享这篇文章
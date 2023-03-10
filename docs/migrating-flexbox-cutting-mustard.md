# 通过去除芥菜迁移到 Flexbox

> 原文：<https://www.sitepoint.com/migrating-flexbox-cutting-mustard/>

作为前端开发人员，是时候放弃使用 CSS floats，投入到令人兴奋的 flexbox 新世界中了。CSS 浮动是一种过时的样式布局方法；从 4.0 版本开始，它们就在 Internet Explorer 中可用了，需要一些变通方法来使它们具有延展性(包括[clearfix hack](https://www.sitepoint.com/clearing-floats-overview-different-clearfix-methods/)和[使用第 n 个子伪类](https://www.sitepoint.com/getting-to-know-css3-selectors-structural-pseudo-classes/)来包装列)。

本文的主题是考虑到它的碎片化，如何跨多个浏览器实现 flexbox。如果您想更熟悉 flexbox，有许多好的资源可供使用，我强烈推荐以下资源:

*   我们准备好使用 Flexbox 了吗？由 Nick Salloum 在 SitePoint 上发布
*   Chris Coyier 关于 CSS-Tricks 的 Flexbox 完全指南
*   [MSDN 上 Internet Explorer 10](https://msdn.microsoft.com/en-us/library/hh673531%28v=vs.85%29.aspx) 中的灵活框(“Flexbox”)布局(可选)

完成本文后，您应该能够:

*   了解哪个版本的 flexbox 是响应式网站的目标。
*   通过功能检测利用 flexbox(切芥末),并为传统浏览器提供后备。
*   在大多数情况下，不要使用 IE 条件注释。
*   通过创建一个带有传统回退功能的基本 2×2 网格来演示 flexbox 的实际用途。

## Flexbox 的简史

[灵活的盒子布局模块](https://www.w3.org/TR/css3-flexbox/)(又名 flexbox)是一种在 CSS 中构造布局的新方法。它经历了多次修订，并在其相对较短的存在时间内发生了重大变化。在撰写本文时，flexbox 仍然是 W3C 的工作草案，但是，像其他标准一样，这不应该使它在生产环境中失去吸引力。

该标准有三个版本，通常被称为[旧语法](https://www.w3.org/TR/2009/WD-css3-flexbox-20090723)、 [tweener 语法](https://www.w3.org/TR/2012/WD-css3-flexbox-20120322/)和[新语法](https://www.w3.org/TR/css3-flexbox/)。

flexbox 的[限制是有据可查的:](http://caniuse.com/#feat=flexbox)

*   旧语法不支持`flex-wrap`。
*   tweener 语法仅在 IE 10 中受支持(包括移动)。
*   新语法在 Firefox (22-27)中没有完全实现，因为它缺少了`flex-wrap`和`flex-flow`属性。

Wrapping ( `flex-wrap`)是规范的一个重要特性，是创建一个响应式网格所必需的。因此，最好只针对完全实现新语法的 tweener 语法和浏览器版本。

这给我们留下了以下浏览器版本:

*   Internet Explorer 10(带`-ms-`前缀的 tweener 语法)
*   Internet Explorer 11 和 Edge(新语法)
*   Firefox 28+(新语法)
*   Chrome 21-28(带有`-webkit-`前缀的新语法)
*   Chrome 29+(新语法)
*   Safari 6.1+(带`-webkit-`前缀的新语法)
*   iOS Safari 7.0+(带`-webkit-`前缀的新语法)

由于有一些占有很大市场份额的浏览器不支持 flexbox，这些浏览器应该退回到使用 CSS 浮动。但是这如何用代码来表达呢？区别应该用 floats 而不是 flexbox 接收 CSS 的浏览器版本的最好方法是什么？可以使用什么策略来确保支持新语法但不支持包装的 Firefox 版本被识别为遗留版本？

介绍:**切芥末**。

## 切芥末(特征检测)

如果你以前没有听说过它是一个技术术语，“切芥末”[是由 BBC 新闻](http://responsivenews.co.uk/post/18948466399/cutting-the-mustard)的开发团队创造的。该术语源于这样一个事实，即 BBC 网站必须迎合广大的国际观众，而专门针对浏览器版本和设备将是一个繁琐的解决方案。

这个概念的关键是识别哪个浏览器/设备/用户代理正在被使用，并提供 polyfills 以使站点工作。特定特征的存在在客户端被检测到，因此针对可用技术的最合适的解决方案被交付。

特征检测并不新鲜。前面提到的 BBC 文章发表于 2012 年 3 月，虽然它越来越受欢迎，但令人惊讶的是，网站仍然在实现保罗·爱尔兰在 2008 年普及的特定于 IE 的条件类。

Modernizr(Paul Irish 供稿)完全是关于特征检测:

> 利用很酷的新网络技术非常有趣，直到你不得不支持落后的浏览器。Modernizr 使您可以轻松地编写条件 JavaScript 和 CSS 来处理各种情况，不管浏览器是否支持某个特性。它非常适合轻松地进行渐进增强。

虽然 [CSS 现在有了原生特征检测](https://www.sitepoint.com/supports-native-css-feature-detection/)，但它目前还没有足够的[市场份额](http://caniuse.com/#feat=css-featurequeries)用于现实世界。本文的剩余部分将讨论如何抛弃 IE 条件注释，支持 JavaScript 中的特性检测。

## 识别功能和浏览器

每个项目都需要一组不同的功能才能运行。有多种方法可以实现特征检测，其中最简单的方法包括:

*   使用[普通 JavaScript](http://vanilla-js.com/) (如 BBC 所用)
*   使用 [Modernizr](http://modernizr.com/) (将在本文中使用)

最有效的方法是普通的 JavaScript 实现。它很快(因为它不需要客户端下载任何额外的库),也不需要任何额外的处理。这种方法远非完美，因为存在已知的问题；然而，有[种方法可以克服常见的特征检测问题](https://www.sitepoint.com/javascript-feature-detection-fails/)。

> [B]浏览器检测已经成为一个不可能解决的问题，并且已经基本上不再使用，被更好的东西所取代——特征检测。
> 
> [……]功能检测也不完全可靠，有时会失败。
> 
> –<cite>[詹姆斯·爱德华兹](https://www.sitepoint.com/javascript-feature-detection-fails/)</cite>

选择 Modernizr 可能效率不高(因为它需要下载和客户端处理)，但是手动检测`flex-wrap`支持并不是一项简单的任务。同样需要注意的是，虽然 Modernizr [版本 2](http://modernizr.com/download/) 没有检测到`flex-wrap`，但是[版本 3](http://v3.modernizr.com/download/) 检测到了！该特征被标记为[挠曲线缠绕](http://v3.modernizr.com/download/#-flexwrap)。

尽管可以选择使用由 Modernizr 生成的附加到文档根的 CSS 类(例如:`html.flexwrap`)，但最好为每个体验提供单独的 CSS 文件，以减少网站的下载量。

BBC 新闻开发人员提到了两种浏览器:

> 团队中有人开始称它们为“HTML4 浏览器”和“HTML5 浏览器”，我们发现这更容易向非技术人员传达这种情感。
> 
> –<cite>[BBC 响应式新闻](http://responsivenews.co.uk/post/18948466399/cutting-the-mustard)</cite>

当你考虑 2012 年浏览器市场的气候时，这一理论是完全正确的；然而，随着新功能的出现，这种划分就不一定那么清晰了。例如，并非所有的“HTML5”浏览器都完全支持 Flexbox。

一种可靠的方法是区分“传统”和“现代”浏览器版本。此外，一些项目可能需要多个分部，在那里可以识别中途(或“过渡”)浏览器。

## 实施该方法

首先创建以下文件:

*   `index.html`–主 HTML 文件
*   `stylesheets/modern.css`–现代浏览器的风格(媒体查询、带包装的 flexbox)
*   `stylesheets/legacy.css`–传统浏览器的样式(无媒体查询，无 flexbox)
*   `scripts/dependencies.js`–执行特征检测

以下是我们的 index.html 文件的外观:

```
<!DOCTYPE html>
<html class="no-js">
  <head>
    <title>Cutting the mustard</title>
    <script src="javascripts/dependencies.js"></script>
    <noscript>
    <link rel="stylesheet" href="stylesheets/legacy.css">
    </noscript>
    <!-- ... -->
  </head>
  <body>
    <div class="container">
      <div class="cell cell-1">Cell 1</div>
      <div class="cell cell-2">Cell 2</div>
      <div class="cell cell-3">Cell 3</div>
      <div class="cell cell-4">Cell 4</div>
    </div>
  </body>
</html>
```

注意没有 IE 条件注释？只是干净有效的 HTML 代码。如果浏览器没有启用 JavaScript，它将回退到使用`legacy.css`而不管它的支持级别。

您可能还会注意到脚本标签位于 HTML 页面的顶部。这是因为 Modernizr 应该在浏览器第一次绘制之前处理并注入样式表。这减少了重绘，并有助于避免无样式内容(FOUC) 的[闪烁。但是请记住，大多数脚本标签会在页面的底部。](https://community.sitepoint.com/t/prevent-flash-of-unstyled-content-fouc/31888)

我们的 legacy.css 文件将包含以下内容:

```
.container {
}

/* clearfix */
.container:after {
  content: "";
  display: table;
  clear: both;
}

.cell {
  width: 50%;
  float: left;
}

/* wrapping */
.cell:nth-child(2n+1) {
  clear: left;
}

/* for visiblity */
.cell-1 { background-color: #000; color: #fff; }
.cell-2 { background-color: #666; color: #fff; }
.cell-3 { background-color: #ccc; color: #000; }
.cell-4 { background-color: #fff; color: #000; }
```

这个实现包括一个 clearfix hack 和用于包装的`:nth-child`伪类。它适用于大多数浏览器；然而，Internet Explorer 8 需要[selectivitizr](http://selectivizr.com)或等效的解决方案来使选择器工作。

接下来，我们的 modern.css 文件:

```
.container {
  /* Internet Explorer 10
   */
  display: -ms-flexbox;
  -ms-flex-wrap: wrap;

  /* Chrome 21-28
   * Safari 6.1+
   * Opera 15-16
   * iOS 7.0+
   */
  display: -webkit-flex;
  -webkit-flex-wrap: wrap;

  /* Chrome 29+
   * Firefox 28+
   * Internet Explorer 11+
   * Opera 12.1 & 17+
   * Android 4.4+
   */
  display: flex;
  flex-wrap: wrap;
}

.cell {
  -webkit-flex: 1 0 50%;
      -ms-flex: 1 0 50%;
          flex: 1 0 50%;
}

/* for visiblity */
.cell-1 { background-color: #000; color: #fff; }
.cell-2 { background-color: #666; color: #fff; }
.cell-3 { background-color: #ccc; color: #000; }
.cell-4 { background-color: #fff; color: #000; }
```

不要被这个文件的大小吓倒。注释使它看起来更大，但是这使得在开发中更容易理解每个部分的目标。

接下来，我们将为 dependencies.js 编写代码。

如前所述，我们需要生成一个版本的 Modernizr(版本 3)，该版本的[检测对 flex-wrap 属性](http://v3.modernizr.com/download/#-flexwrap)的支持。在 JavaScript 文件的顶部包含代码。

```
/* Include Modernizr v3 with 'Flex line wrapping' here */

(function() {
  var isModern = Modernizr.flexwrap;

  var link = document.createElement('link');
  link.rel = 'stylesheet';
  link.type = 'text/css';
  link.href = 'stylesheets/' + (isModern ? 'modern' : 'legacy') + '.css';

  document.getElementsByTagName('head')[0].appendChild(link);
})();
```

您可以通过添加到`isModern`布尔值来增加对现代体验的要求。例如:

```
var isModern = Modernizr.flexwrap && 'querySelector' in document;
```

## Sass 解决方案

您可以使用 Sass 来抽象实现 flexbox 的方法。这减小了 CSS 输出的大小，并使其更易于维护:

```
%flexbox {
 display: -ms-flexbox;
 -ms-flex-wrap: wrap;
 display: -webkit-flex;
 -webkit-flex-wrap: wrap;
 display: flex;
 flex-wrap: wrap;
}

.container1 {
 @extend %flexbox;
}

.container2 {
 @extend %flexbox;
}
```

## 渐进式增强和浏览器测试

理解 flexbox 和 CSS 浮动之间的区别很重要。在每种体验中，您的实现看起来并不完全相同——但是渐进增强的概念意味着并不一定如此。

例如，默认情况下，flexbox 将拉伸同一行上的所有单元格，使其具有相同的高度。因此，如果一个单元格长 3 行，相邻行长 10 行，背景将在两个单元格上延伸到 10 行。CSS 浮动的回退不会做到这一点，两个单元格将具有不均匀的高度。

在多种浏览器中测试布局仍然是一项要求，但是请记住，在 JavaScript 中将`isModern`的值强制设为 false 有助于在任何浏览器中测试遗留解决方案:

```
var isModern = false; // Modernizr.flexwrap;
```

## 结论

在本文中，我提供了使用特性检测在同一个 HTML 代码基础上为两个不同的样式表服务的基础知识。这是从 CSS 浮动开始升级过程并减少对 IE 条件注释依赖的非常有效的方法。

尽管人们非常关注检测对 flexbox 的支持，但重要的是要注意，随着浏览器新功能的开发，这种切中要害的方法可以进行调整和发展，以适应未来的需求。

一旦 Internet Explorer 10 在您的目标领域的浏览器市场份额中不再受欢迎，您也许可以放弃 tweener 语法，只通过使用新语法来提供更精简的代码。

既然你已经有了所有的理论，为什么不在你的下一个项目中熟悉 flexbox 呢？

## 分享这篇文章
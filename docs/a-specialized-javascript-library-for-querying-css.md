# 用于查询 CSS 的专用 JavaScript 库

> 原文：<https://www.sitepoint.com/a-specialized-javascript-library-for-querying-css/>

![Some faintly-written CSS on leaf-patterned paper, tied with string in the corner.](img/0e761e0f033c63f9ff77ae429a9d3adf.png)

在过去的几个月里，我一直在努力完成一个令人着迷的项目，现在它终于问世了！它叫做 [**CSSUtilities**](http://www.brothercake.com/site/resources/scripts/cssutilities/) ，是一个专门用于查询 <abbr title="Cascading Style Sheets">CSS</abbr> 样式表的 JavaScript 库。

也许它最独特的地方在于，它可以为您提供**非规范化属性信息** —换句话说，您可以获得应用于*中元素的颜色和尺寸，它们是用*指定的原始单位，而不是您从内置查询方法(如 [`getComputedStyle`](https://developer.mozilla.org/en/DOM/window.getComputedStyle) )或甚至从 [`CSSStyleSheet`](https://www.w3.org/TR/DOM-Level-2-Style/css.html) 接口获得的规范化值。

在所有的浏览器中有很多这样的例子，但仅举几个例子:Firefox 将大多数颜色值标准化为 <abbr title="Red Green Blue">RGB</abbr> ，而你可能已经用<abbr title="hexadecimal">十六进制</abbr>或者颜色名称定义了它们；Opera 将颜色规格化为 6 位数<abbr title="hexadecimal">十六进制</abbr>；Internet Explorer 拆分了`margin`属性，因此即使您定义了一个简写，它也会为每个方向返回单独的值。

但是当你使用 CSSUtilities 库时，你得到的*正是指定的*，这使得以一种以前不可能的方式使用动态样式成为可能——例如，当你读取一个元素的宽度时，你总是在`px`中得到一个值，但是现在你可以在`em`或`%`中得到它的宽度，然后你可以将它应用于一个克隆体，给它与原始体完全相同的缩放或大小调整特征。

它可以做的一些其他事情包括:列出适用于指定元素的所有规则，告诉您它继承的属性，或者它们适用的媒体，或者任何选择器的特殊性。它可以告诉你基本上所有的事情，关于每一个样式表中的每一条规则。

我希望许多人会发现它很有用，并且它催生了一些新的有趣的应用程序和工具的开发；我已经做了一些演示，你可以在[http://www.brothercake.com/scripts/cssutilities](http://www.brothercake.com/scripts/cssutilities/)查看……也许它们会激励你去尝试一些东西！

事实上，你已经可以在野外看到 CSSUtilities，因为一个预发布版本为 [Adobe AIR](https://getfirebug.com/ "CodeBurner for Adobe AIR") 、 [Opera Widgets](https://getfirebug.com/ "CodeBurner for Opera Widgets") 和 [Mac OS X Dashboard](https://getfirebug.com/ "CodeBurner for Mac OS X Dashboard Widgets") 的 [CodeBurner](https://getfirebug.com/ "CodeBurner is a suite of tools providing reference material for HTML and CSS") 中的 [CSS 检查界面](https://i2.sitepoint.cimg/codeburner/air/screenshot-cssview.jpg "Screenshot of the CSS inspection interface in CodeBurner for Adobe AIR")提供了动力。它们能够提取和分析样式表，列出它们的规则，并向您提供关于每个样式表的属性和选择器的信息——所有这些功能都是由 CSSUtilities 提供的，并且是唯一能够做到这一点的东西——对于非特权代码来说，没有任何其他东西可以做同样的工作。

Firebug 能给你这些信息的唯一原因是，它可以调用 Firefox 的 dom-utilities 类，告诉它适用于你正在检查的元素的规则，但当然这在 Firefox 中只对像附加组件这样的特权脚本可用(而且它仍然是*规范化数据，不一定是作者最初指定的)。我做的一个演示是一个 [Live CSS Inspector](http://www.brothercake.com/scripts/cssutilities/demos/inspector.html "CSSUtilities demo: Live CSS Inspector") ，它以类似的格式展示规则；它可以在所有的浏览器上运行——甚至是 <abbr title="Internet Explorer 6">IE6</abbr> ！*

因此，请[查看一下](http://www.brothercake.com/site/resources/scripts/cssutilities/)……[浏览一下演示](http://www.brothercake.com/scripts/cssutilities/)……让我们知道你的想法(或者如果你发现任何错误……希望不太可能，但总是有可能的！).但最重要的是，我真的很想听听你对任何可以使用它的工具或应用程序的想法。我承认我对这些可能性感到非常兴奋；自从`querySelectorAll`出现以来，我还没有对 <abbr title="Document Object Model Cascading Style Sheets">DOM CSS</abbr> 这么感兴趣过:-)

## 分享这篇文章
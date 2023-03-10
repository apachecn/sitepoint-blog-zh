# AtoZ CSS 快速提示:对文本使用 OpenType

> 原文：<https://www.sitepoint.com/atoz-css-quick-tip-text/>

本文是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。
你可以在这里](https://www.sitepoint.com/blog/)查看文本和排版的完整脚本和截屏。

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),每个值都以字母表中不同的字母开始。我们知道有时仅仅截屏是不够的，所以在本文中，我们添加了使用 OpenType 特性来设计文本样式的快速技巧。

![T2-01 (1)](img/ebafb6e5f40c9a4cce2f84e2d5fb9954.png)

## t 代表文本和 OpenType

本周的 CSS 技巧都是关于文本和排版的。在最初的截屏视频中，我们看到了各种不同的 CSS 属性来处理文本样式。但是在这个系列中我们还没有深入讨论的是 OpenType 特性。

这一系列属性为您的文本添加了各种各样的印刷优点，只要您能够访问提供这些功能的字体，它们就能真正提升用户的阅读体验。

让我们从你在哪里可以找到这些漂亮的字体开始。

### 使用高级字体服务

使用 CSS 中的`@font-face`指令，我们可以将任何自定义字体加载到我们的项目中。这可能来自像谷歌字体(T2)这样的服务，或者你可以使用像字体库(T4)这样的生成器来创建你自己的网络字体。这两项服务都提供了大量优秀的免费字体，但有时你可能希望从付费服务中获得一些更专业、优质的字体，如 Adobe Typekit、Fonts.com 或其他一些服务。

这些优质服务不仅提供了高质量的字体，它们还在幕后施展了许多魔法来提高易读性、交付和加载字体本身的性能。我很想在未来的某个时候深入探讨这个问题，但目前它有点超出了我的理解范围！

我目前使用 Adobe Typekit 为客户项目提供字体，到目前为止，我对这项服务非常满意。我唯一的抱怨是，我不能下载所有的字体供本地使用，当需要在 Sketch 或 Adobe Photoshop 中使用这些字体时，这有点痛苦。

但是一个主要的好处是它们提供了许多具有 OpenType 特性的字体。拥有一种支持 OpenType 特性的字体是能够使用下面几个技巧的第一步。

### 打开连字

连字是一种 OpenType 功能，它用单个字符替换多个字符的常见序列，以提高易读性和更优雅的视觉外观。连字的例子可以在[这里](http://help.typekit.com/customer/en/portal/articles/1789736-syntax-for-opentype-features-in-css#liga)找到。

典型的例子是字母组合，如`ff`或`fi`、`fl`、`ffi`或`th`。当被视为多个字符时，字母之间似乎有太多的空间。结扎线更流畅。这是一个微优化，但它看起来很经典，你会给你所有的书呆子朋友留下深刻印象。

要在 CSS 中使用连字(如果您的字体支持的话)，您可以使用以下代码片段:

```
body {
  font-variant-ligatures: common-ligatures;
  font-feature-settings: "liga", "clig"; /* for IE */
}
```

有另一种设置`font-feature-settings`的语法，它允许一次设置一个逗号分隔的 OpenType 特性列表——有点像`margin`或`padding`的简写。

如果你使用 Safari，连字是默认打开的，但为了确保 Firefox、Opera、Chrome 和 IE (10+)获得最佳的阅读体验，你需要显式设置这一点。

### 使用花饰来增添光彩

如果你正在做一个项目，需要一点额外的印刷字体，你可以使用 swashes OpenType 特性。这将获取文本中的关键字符，并用花体符号替换它们；这些是同样的信，但更加奢侈。

要获得 CSS 中 OpenType 特性的列表，请查看这个有用的[资源](https://helpx.adobe.com/typekit/using/open-type-syntax.html#swsh)。

要打开 swashes，请使用以下代码片段:

```
.fancy-title {
  font-feature-settings: "swsh";
}
```

### 使用字距调整改进字母间距

在[文本截屏](https://www.sitepoint.com/atoz-css-screencast-text)中，我们讨论了类似`letter-spacing`和`word-spacing`的属性，用于控制字符和单词之间的间距。但是为了额外的控制和更好的易读性，我们可以使用`font-kerning`的 OpenType 特性。

字距调整是调整某些字符组合之间的间距，使它们最适合读者的过程。在 Illustrator 或 Photoshop 等图形软件包中，设计师可能会沉迷于手工操作，但往往不得不在文本到达浏览器时放弃控制。

此功能可通过如下设置`font-kerning`或`font-feature-settings`属性来启用:

```
body {
  font-kerning: normal;
  font-feature-settings: "kern";
}
```

### 一次设置多个 OpenType 功能

我们已经看到了三个最常见、最令人印象深刻的 OpenType 特性，但还有更多。如果你想利用这些强大的特性，很可能你会想一次设置很多。我们可以用`font-variant`或`font-feature-settings`的人手来做这件事。

讨论这两个属性的原因是浏览器支持问题。在撰写本文时，IE10 支持`font-feature-settings`，但不支持其他 OpenType 属性，如`font-kerning`或`font-variant`属性。Safari 确实支持像`font-kerning`这样的个人属性，但是不能识别`font-feature-settings`。

因此，要打开刚才讨论的所有功能，我们需要使用以下一组属性来覆盖我们的基础:

```
body {
  font-variant: common-ligatures;
  font-kerning: "kern";
  font-feature-settings: "liga", "clig", "swsh", "kern";
}
```

### 使用样板文件启动您的 OpenType 体验

如果这一切让你感到有些力不从心，而你想要一些快速简单的东西来让你开始运行，我完全理解，幸运的是，有一些东西可以帮助你

你可能对 [normalize.css](https://necolas.github.io/normalize.css/) 很熟悉，它是一个 css“重置”,用于跨浏览器标准化用户代理样式表。

我最近偶然发现了 [normalise-opentype.css](http://kennethormandy.com/journal/normalize-opentype-css) ,它添加了 opentype 特性，具有所有必要的属性，并支持深度浏览器，同时给你所有 OpenType 的优点。[normalize-opentype 项目在
github](https://github.com/kennethormandy/normalize-opentype.css) 上，使用它就像下载和链接一个额外的样式表或者添加到你的预处理程序`@imports`中一样简单。

这是一个看起来很棒的工具，我肯定会在我的下一个项目中试用它！

## 分享这篇文章
# 充分利用谷歌字体的 4 个专家提示

> 原文：<https://www.sitepoint.com/4-expert-tips-google-fonts/>

谷歌字体已经伴随我们五年了。如果你从事网络前端工作，你很有可能在你开发的至少一个网站上使用过这项服务。

虽然许可成本保持为零，但谷歌的字体库在数量和质量上都稳步增长，谷歌为你接管了版权和基础设施的责任。更重要的是，实现谷歌字体只需要你做最少的工作，因为他们的神奇服务器可以处理许多我们过去必须自己处理的棘手问题。

今天我们可能认为这是理所当然的，但是我们很容易忘记在你的网站上使用定制字体曾经是多么的困难、麻烦和昂贵。如果你想快速回忆一下我们曾经经历过的事情，请重新熟悉一下' [sIFR](https://en.wikipedia.org/wiki/Scalable_Inman_Flash_Replacement) '这个词。

拥有如此有用的资源，充分利用它是有意义的。在这篇文章中，我想介绍四个技巧来帮助你像专业人士一样使用谷歌字体。

## 技巧 1:在一个请求中请求多种字体

在标题和段落中使用不同的字体通常可以提高可读性。然而，我们也知道从服务器请求许多小文件比请求一个大文件要慢得多。

因此，与其分别请求每种字体，不如将多个字体请求合并成一个。例如，假设您希望加载两种字体— Lato 和 Raleway。加载字体的正确方法是:

```
<link href='https://fonts.googleapis.com/css?family=Lato|Raleway' 
rel='stylesheet' 
type='text/css'>
```

虽然这对下载的总位数没有影响，但是 HTTP 请求的数量减少了，并且这已经被证明可以改善页面加载时间。你使用的字体越多，节省的潜在成本就越大——尽管有其他很好的理由来限制你使用的外部字体的数量。

另外，这种方法使得代码更加简洁，可读性更强。你只需要看一行就能理解你的网页所要求的所有字体。

## 技巧 2:指定字体样式和脚本子集

有时候，你可能需要斜体的 T4 字体或者粗体的 T6 字体。在这种情况下，您可以在字体名称后分别使用`i`和`b`指定字体样式，用`:`隔开。

这里有一个例子:

```
<link href='https://fonts.googleapis.com/css?family=Lato:i|Raleway:b' 
rel='stylesheet' 
type='text/css'>
```

如果可用，您也可以使用`bi`加载粗斜体字体样式。

如果你想要一种不同的粗体字体，你可以使用数字粗细。例如，罗威拥有重量为 600、700、800 和 900 的四个版本的 bold。要请求超粗体字体，您需要拨打:

```
<link href='https://fonts.googleapis.com/css?family=Raleway:900' 
rel='stylesheet' 
type='text/css'>
```

如果您不喜欢缩写，您可以自由指定字体样式的全名，就像我在下一个示例中所做的那样:

```
<link href='https://fonts.googleapis.com/css?family=Lato:300italic' 
rel='stylesheet' 
type='text/css'>
```

在这种情况下，我们加载的不是普通的斜体字体，而是轻型字体。

目录中的一些字体支持多种文字，如拉丁语和希腊语。这种字体的一个例子是 Roboto Mono。要请求该字体的希腊语子集，您可以使用以下 URL:

```
<link 
href='https://fonts.googleapis.com/css?family=Roboto+Mono&subset=greek' 
rel='stylesheet' 
type='text/css'>
```

## 技巧 3:优化只发送你需要的字符

在特定情况下，您可能只需要使用给定字体中严格限定的字符集。当您只需要给定字体中的数字时，也可能是这种情况。

在这种情况下，很容易通过使用字体 URL 中的`text=`值来挑选我们需要的特定字符，从而减少请求的大小。通过这种方式，谷歌将返回你所要求的字体文件版本，并根据你的具体需求进行优化。这可以显著减小所提供字体的文件大小，尤其是对于复杂的显示字体和精致的书法字体。

考虑下面的例子:

```
<link href='https://fonts.googleapis.com/css?family=Raleway&text=SitePoint' 
rel='stylesheet' type='text/css'>
```

我们可以选择只加载单词“SitePoint”所需的字符，而不是加载完整的 Raleway 字体文件。这将字体文件大小减少了 90%。

这些优化累积起来会对网页的整体加载时间产生重大影响。请记住，您需要对`text`的值进行 URL 编码。

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [dYmZzM](http://codepen.io/SitePoint/pen/dYmZzM/) 。
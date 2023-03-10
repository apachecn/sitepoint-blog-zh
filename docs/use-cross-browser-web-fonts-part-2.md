# 如何使用跨浏览器 Web 字体，第 2 部分

> 原文：<https://www.sitepoint.com/use-cross-browser-web-fonts-part-2/>

## 介绍

在我的文章的第 1 部分([https://www . site point . com/how-to-use-cross-browser-we b-fonts-part-1/](https://www.sitepoint.com/how-to-use-cross-browser-web-fonts-part-1/))中，我介绍了 web 字体语法，讨论了基本的@font-face 和 font-family 语法如何在 web 页面上嵌入定制字体，以及稍微复杂一些的跨浏览器现实。

在这一部分，我们将更进一步，看看与 web 字体相关的常见现实问题，并比较托管字体的免费和付费选项。

第三部分将着眼于一些更先进的 CSS 字体功能，如断字和字体功能设置。

## 跨浏览器网络字体-问题

因此，您已经在服务器上安装了字体，并准备好了跨浏览器@font-face 代码。检查。但是你的问题可能还没有结束。在这一节中，我将进一步探讨您可能会遇到的一些问题，以及建议的解决方案。

### 糟糕的渲染

跨浏览器测试是至关重要的:即使你没有遇到任何其他问题，你的字体渲染也可能很糟糕，尤其是在 IE 和 Windows 上基于 Chrome/Blink 的 Opera 中。这可能有多种原因，但主要有:

*   ClearType 的使用:ClearType 是一种 Windows 技术，它使用亚像素渲染来使字体看起来更平滑。如果不启用，字体看起来会很糟糕，尽管它在大多数 Windows 系统(比 XP 更高)上是默认打开的。)它通常仍不如 Mac OSX 抗锯齿有效。请注意，Windows 上的其他浏览器不会像它们一样有自己的子像素渲染系统。更多信息，请阅读 Jon Tan 的“显示类型和光栅战争”([http://v1 . Jon tangerie . com/log/2008/11/Display-type-and-the-raster-wars](http://v1.jontangerine.com/log/2008/11/display-type-and-the-raster-wars)

*   由于各种原因，谷歌网页字体渲染已经在 Blink 渲染引擎中混乱了一段时间:查看下面的错误报告

*   一些字体(很可能是免费的)的可用字形数量很少，这意味着当您在内容中使用一个字符，而该字符的字形在您正在使用的字体中不可用时，您将会得到一个令人讨厌的渲染惊喜，例如可怕的丑陋的末日方块的空格，而不是您期望的字符。为了减轻这种负面影响，您应该确保在堆栈中始终包含备用字体，例如，即使您使用的是 web 字体

```
font-family: 'Abril Fatface Regular', arial, sans-serif;
```

### FOUT

FOUT 代表无样式文本的 Flash，虽然它不再是一个大问题，只影响早期的 Firefox 和 Opera 版本以及 IE < 10，但你可能仍然会遇到它，并想做点什么。这是当 HTML 已经加载，但 web 字体还没有加载时，页面上出现的未样式化的文本。

有许多与字体相关的库和技术可以用来处理 FOUT，其中许多都在保罗·爱尔兰的《与@font-face FOUT 战斗》([http://www.paulirish.com/2009/fighting-the-font-face-fout/](http://www.paulirish.com/2009/fighting-the-font-face-fout/))中提到过。我个人认为最可靠的技术是谷歌的网页字体加载器(【https://developers.google.com/fonts/docs/webfont_loader】T2)。这包含了 JavaScript，它检测你的 web 字体是否已经加载，并将类应用到文档的标签来指示它们的状态。您可以使用这些来隐藏元素，直到加载了它们的字体，例如:

```
.wf-loading h1 {
  visibility: hidden;
}
```

然后，当加载了所需的字体时，会将一个特定的类添加到标记中以表明这一点，您可以根据需要更新元素的显示:

```
.wf-lobster-n4-active h1 {
  visibility: visible;
  font-family: lobster;
}
```

### 高带宽

向页面添加 web 字体显然会影响性能，这在很多情况下可以忽略不计，因为 web 字体可能会有 50-100KB 左右(粗略估计！)出于带宽和性能的原因，您应该限制在每个页面上使用的字体数量，但即使是单个字体也可能非常大，尤其是如果它是一种可以包含数千个字形的 CJK 字体([http://en.wikipedia.org/wiki/CJK](http://en.wikipedia.org/wiki/CJK))。

要缓解这个问题，您可以:

*   创建一个修改后的字体文件，只包含您需要的字符。为此，你需要一个字体编辑器，比如 Font Forge([http://fontforge.org/](http://fontforge.org/))。
*   通过使用@font-face 块中的 unicode-range 属性，只指定您想在字体文件中使用的字符，我们在本系列的上一部分中已经看到了。例如:

```
@font-face {
  font-family: 'Abril Fatface';
  src: url("AbrilFatface.otf");
  unicode-range: U+0026;
}
```

这指定只从字体加载&字符(参见[https://www.w3.org/TR/css3-fonts/#unicode-range-desc](https://www.w3.org/TR/css3-fonts/#unicode-range-desc)了解更多关于指定多个字符的信息，包括范围)。除了 Firefox(我们正在开发中)之外，它在各种现代浏览器中都得到了很好的支持。).如果您想在 Firefox 或更老的浏览器上使用 unicode-range，您可以使用一个 polyfill，比如 jquery-unicode-range([https://github.com/infojunkie/jquery-unicode-range](https://github.com/infojunkie/jquery-unicode-range))。

### 浏览器缺陷和特性

出于兴趣，我还想提一下几个浏览器漏洞。

#### 下载 EOT 时的 IE9 错误

如果您使用我们在上一篇文章中探索的防弹@font-face 语法，IE9 和更高版本将下载该字体的 EOT 版本并使用它，这有点麻烦。

#### 跨域字体失败

根据规范，你不能将不同域的字体加载到你的 CSS 中(尽管 Chrome、Safari 和 Opera 不正确地允许这样做。)为了绕过这个限制，你可以考虑使用 CORS(见[http://dev . opera . com/articles/view/DOM-access-control-using-cross-origin-resource-sharing/](http://dev.opera.com/articles/view/dom-access-control-using-cross-origin-resource-sharing/))

#### IE 6-8 下载已声明但未使用的字体

您不应该为您的页面上不会用到的字体声明@font-face 规则；IE6-8 无论如何都会全部下载。

#### 从 IIS 服务器提供字体

如果您从 IIS 服务器提供字体，您需要添加。即使 woff 没有 MIME-type，也要将 MIME 类型为“application/x-font-woff”的“woff”安装到您的 IIS 安装中。IIS 拒绝提供任何它没有 MIME 类型的东西。

## 自托管与托管服务

在讨论了@font-face 的怪癖和缺陷之后，让我们简要地看一下获取和托管字体的一些不同选项，从而结束本文。就带宽而言，自托管字体的成本更高，设置起来也更复杂，但你可以更精确地控制如何在网站上实现字体。另一方面，使用托管服务设置起来更快(该服务处理所有的跨浏览器问题，等等)。为您)和其他人为您托管字体。

注意:这里的机制有些是免费的，有些是付费的。有许多高质量的免费字体资源可用，但你会得到你所支付的:即使是高质量的免费字体也往往在某种程度上受到影响，要么是可用的字符集或变体有限，要么就是在网络上被过度使用。如果你想让你的出版物有深度和真正的原创性，你最好看看付费解决方案。

我推荐的免费字库来源有:字体松鼠()、大字体(【http://www.dafont.com/】)、活字联盟()和丢失字体([【http://www.losttype.com/browse/】](http://www.losttype.com/browse/))。Jad lim CaCO([http://www . smashingmagazine . com/2011/07/19/best-Free-Fonts-Designers/](http://www.smashingmagazine.com/2011/07/19/best-free-fonts-designers/))的《设计师最佳免费字体》有更多的建议和好的忠告。

我推荐付费字体来源:霍夫勒和弗莱尔-琼斯([http://www.typography.com/](http://www.typography.com/))，利诺特([http://www.linotype.com/](http://www.linotype.com/))和豪斯工业([http://www.houseind.com/](http://www.houseind.com/))。还有许多其他好的来源！

### 谷歌字体

谷歌字体是一项免费的托管字体服务，可在 http://www.google.com/fonts 使用。这是一个很好的服务，字体质量合理，而且非常容易使用；非常适合编写快速和肮脏的演示，并把一个网站的预算。它和任何免费的字体资源一样，都有同样的问题:一些较好的字体被过度使用，而你能得到的选择非常有限。

付费托管字体服务

现在有许多付费托管字体服务，它们都以类似的方式工作。您通常:

1.  注册这项服务
2.  指定要使用字体的域
3.  指定您想要使用的字体
4.  将一些定制的 CSS 和 JavaScript 粘贴到您的站点中，以处理@font-face 和 font-family 编码
5.  以某种方式测试你的字体选择
6.  当你的网站上线时，为字体付费，通常是按字体、按网站、按年付费。

我已经尝试了许多这样的服务，但我最喜欢的两个如下:

1.  typekit([https://typekit.com/](https://typekit.com/)):Adobe 拥有的一项服务，其使用计划略贵，涵盖多个网站和字体使用，适用于大规模运营。你也可以得到一个有限的免费计划，足够好的测试，但一次只有两种字体。
2.  font Deck([http://fontdeck.com/](http://fontdeck.com/)):一家来自英国的独立字体公司，提供免费计划，允许每个网站测试多种字体，但只能在有限数量的 IP 地址访问时使用。生产字体每年每种字体的成本约为 7.50 美元。我更喜欢这项服务，因为我发现测试功能更有用，而且它们还提供了一些不错的 opentype 字体功能。

注意:Dan Eden 的《web 字体服务:好的、坏的和丑陋的》([http://Web design . tuts plus . com/articles/typhotomy-articles/we b-Font-Services-the-Good-the-Bad-and-the-Ugly/](http://webdesign.tutsplus.com/articles/typography-articles/web-font-services-the-good-the-bad-and-the-ugly/))是一篇很好的文章，提供了一些关于 Web 字体服务的进一步信息。

当你开始使用这种服务时，好处通常是显而易见的:你将获得高质量的字体，提供完整的字形集和多种粗细和变体。您还可以找到具有 opentype 功能的 opentype 字体，如风格化的连字和连字，这些字体可以通过新生的字体功能设置属性在 Web 上使用。这是我们将在本系列的第 3 部分中讨论的一个问题。

## 分享这篇文章
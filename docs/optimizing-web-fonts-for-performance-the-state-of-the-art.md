# 优化网页字体的性能:艺术的状态

> 原文：<https://www.sitepoint.com/optimizing-web-fonts-for-performance-the-state-of-the-art/>

*本文是与 [SiteGround](https://www.siteground.com/sitepoint-recommended?afcode=97a975da3502771c04e59cbae092b1dd&campaign=web-fonts) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

**[现在 67%的网页使用自定义字体](http://httparchive.org/interesting.php#fonts)。然而，流行和广泛采用并没有带来一些性能和用户体验相关的问题。**

在本文中，我将介绍 web 字体的常用和加载方式有哪些不尽如人意的地方，并向您介绍经过充分测试的变通方法和未来基于标准的解决方案。

## 为什么要自定义 Web 字体？

用户来你的网站是为了内容。因此，优秀的版式在网页上至关重要:可读性、易读性和精心制作的版式设计是品牌识别和信息成功的必要条件。

实现漂亮排版的最佳方式是加载自定义 web 字体，即用户设备上尚未安装的字体文件。

自从浏览器对 CSS `@font-face`规则的支持变得广泛以来，在网站中使用定制的网络字体已经有了突飞猛进的发展。然而，字体可能会有很大的文件大小，在网站上加载额外的资源会对性能产生一些负面影响。

由于文件大小肯定是一个问题，所以关注自定义 web 字体是如何加载的就成了首要问题。

## 隐形文字的闪现(FOIT)到底是怎么回事？

使用自定义 web 字体最常见的方式是指定 CSS `@font-face`声明中使用的字体，并让浏览器保持默认行为。这并不理想。由于字体信息位于 CSS 中，默认情况下，浏览器会延迟加载字体，直到 CSS 被解析。这还不是全部。正如[扎克·莱泽曼](https://www.zachleat.com/web/lazy-loading-webfonts/)已经非常清楚地表明的那样，要触发字体下载，除了有效的`@font-face`声明之外，还必须满足许多条件:

*   使用在`@font-face`中指定的相同`font-family`的 HTML 节点
*   在 Webkit 和 Blink 浏览器中，该节点不能为空
*   在支持`@font-face`中的 unicode 范围描述符的[浏览器中，内容也必须匹配指定的 unicode 范围。](http://caniuse.com/#feat=font-unicode-range)

如果以上几点都满足，浏览器开始下载字体。这意味着浏览器在触发字体下载之前，不仅需要解析 CSS 代码，还需要解析网站的文本内容。然而，就在**字体开始加载时，网络用户很可能会体验到可怕的隐形文字闪现**，或简称为**字体**。

![FOIT: Page at http://blog.instagram.com/ while fonts are loading on Firefox v.52\. Text is invisible.](img/ae087ba2b5616b5db1669266db213fff.png)

FOIT:当字体加载到 Firefox v.52 时，页面在 http://blog.instagram.com/，文本是不可见的。

![FOIT: Page at http://blog.instagram.com/ after fonts have been loaded on Firefox v.52\. Text has become visible](img/b235e99bd546e45407e85443d1fe885a.png)

FOIT:字体加载到 Firefox v.52 后，http://blog.instagram.com/页面上的文字变得清晰可见。

换句话说，一旦浏览器开始下载一种字体，所有的文本都变得不可见。因此，用户看一段时间没有文字的屏幕，在低于标准的网络条件下，感觉就像永远看不到一样。此外，不同的浏览器处理 FOIT 的方式也各不相同:

*   Blink 和 Firefox 浏览器通过引入三秒钟的超时来解决 FOIT:在字体加载期间，文本会消失三秒钟。如果字体没有在这个时间范围内加载，网站会显示一个后备字体，一旦完全加载，该字体随后会被替换为自定义字体。这种行为导致了所谓的 **FOUT** (另一个缩写词):**无样式文本的闪现**。
*   Safari，默认的 Android 浏览器和 Blackberry 不使用超时，但在自定义字体加载之前不显示任何文本。如果出现任何问题，字体没有被加载，用户就会在屏幕上看到不可见的文本。
*   IE/Edge 浏览器不隐藏文本，而是马上显示回退字体，这似乎是微软更好的设计决策。

虽然在网站上阅读文本时突然改变字体不是最好的用户体验，但在字体加载时盯着空白屏幕听起来更糟糕。理想情况下，工作方法应该克服 FOUT 和福伊特。然而，虽然这是一个公开的问题，但许多专家一致认为 FOUT 比 FOIT 好得多。

解决与加载字体相关的问题包括优化文件大小和控制默认浏览器行为，以消除 FOIT 并最小化 FOUT 的不和谐影响。

让我们一次深入了解一项任务。

## 优化自定义字体文件的提示

你可以采取几个步骤来确保你的字体文件不会过重。

### #1 尽量减少项目中的字体数量

不要以为你可以从大量漂亮的字体中选择，你就需要用大量不同的字体来拖累你的网站。情况正好相反。

在大多数情况下，几个明智配对的字体可以为你的设计创造奇迹，并对网站性能产生较轻的影响。

### #2 提供基于浏览器支持的网络字体格式

有四种主要的字体格式:

*   **True Type 字体(TTF)** ，这是一种常见的字体格式，自 80 年代末就已出现
*   **Web 开放字体格式(WOFF)** ，2009 年开发的一种格式，是开放类型或真实类型，只有压缩和进一步的元数据
*   Web 开放字体格式(WOFF2) ，比 WOFF 更好的压缩格式
*   **嵌入式开放字体(EOT)** ，微软设计的一种格式，用于网络上的嵌入式字体。

虽然您可以在您的`@font-face`声明中指定所有这些，但是您可以只使用其中的两个。方法如下:

```
@font-face {
  font-family: 'Open Sans';
  src: local('Open Sans'), local('OpenSans'),
       url('fonts/open-sans.woff2') format('woff2'),
       url('fonts/open-sans.woff') format('woff');
}
```

您向浏览器推荐的第一种格式是 woff2，它具有额外压缩的优势。如果你的浏览器不支持 woff2，那就选择 woff，woff 压缩性好，享受各大浏览器最新版本的[支持。只有 Opera Mini 缺乏对它的支持，再加上 IE8 和更老的 Android 手机浏览器。如果你在 web 开发中使用一种渐进的增强方法，你可以简单地让这些旧的浏览器退回到一种系统字体，例如 Arial、Verdana 等。](http://caniuse.com/#search=woff)

### #3 只加载您需要的样式

字体通常有不同的变体——斜体、粗体等。每种字体变化都会增加文件大小的权重，所以如果你不打算在网站上使用的话，尽量避免在你的`@font-face`代码中添加字体变化。请记住，如果您使用`<i>`标签来添加图标，这是非常常见的，这足以让浏览器加载斜体字体变体(Zack Leatherman 的 2015 年圣克拉拉速度演讲中的[幻灯片 77)。如果这是页面中唯一的`<i>`实例，那么使用一个`<span>`标签作为图标，或者将`<i>`元素的`font-style`设置为`normal`，这样浏览器就不必下载不必要的资源了。](https://speakerdeck.com/zachleat/the-performance-and-usability-of-font-loading-velocity-santa-clara-2015)

### #4 保持你的字符集

避免加载您选择的字体提供的所有字符、数字和符号。相反，只选择您在网页上实际使用的字符集。

你可以在 Dudley Storey 的[用 CSS 字体子集化减少页面加载时间](http://thenewcode.com/878/Slash-Page-Load-Times-With-CSS-Font-Subsetting)中了解关于字体子集化的一切。

## 处理 FOIT

让我们来看看目前处理 FOIT 的一些替代方案。我在有效和推荐的实践中挑选了最简单的方法来实现。然而，你可以在 Zach Leatherman 的[字体加载策略综合指南](https://www.zachleat.com/web/comprehensive-webfonts/)中了解到更复杂的内容。

### 不要使用自定义字体

如果你不能忍受你的用户盯着看不可见的文本或字体变换，最简单的选择就是放弃自定义字体，只依赖网页安全字体。

这些字体预先安装在用户的设备上，因此浏览器不需要加载它们。即使决定使用自定义字体，网页安全字体也通常被添加到[字体堆栈](http://www.cssfontstack.com/)中作为备用。

为你的设计放弃完美的自定义字体并不是一个令人兴奋的方法，但它是完全可行的——特别是如果你认为这是一个过渡的姿态，直到尖端的解决方案，已经在地平线上，获得广泛的浏览器支持。

选择这种方法并不要求您使用`@font-face`。您只需将您的字体堆栈添加到 CSS `font-family`属性中。现在你可以这样做:

```
body {
  font-family: -apple-system,
    BlinkMacSystemFont,
    "Segoe UI",
    Roboto,
    Oxygen-Sans,
    Ubuntu,
    Cantarell,
    "Helvetica Neue",
    sans-serif;
}
```

[新的系统字体堆栈？Aderinokun 的《T1》提供了更多关于现代字体堆栈的信息。](https://bitsofco.de/the-new-system-font-stack/)

### 网络字体加载器

一个长期存在的解决方案是 [Web 字体加载器](https://github.com/typekit/webfontloader)，这是一个由谷歌和 [Typekit](https://typekit.com/) 开发的 JavaScript 库，它可以处理来自多个来源的字体，如谷歌字体、Typekit、你的自托管字体文件等。

Web 字体加载器在后台加载自定义字体，同时向用户显示备用字体，从而避免 FOIT。您可以设置字体必须加载的时间限制。如果请求超过此限制，用户将只能看到使用后备字体样式的文本。加载自定义字体后，脚本会将页面上的备用字体与自定义字体进行交换。

这里有两个很棒的教程来学习如何使用网络字体加载器:

*   [如何使用字体加载器提高页面性能](https://www.sitepoint.com/improve-page-performance-font-loader/)
*   [用网络字体加载器加载网络字体](https://css-tricks.com/loading-web-fonts-with-the-web-font-loader/)

### CSS 字体加载 API

CSS 字体加载 API 是一种基于标准的加载和控制网络字体的方式。

总的建议方法仍然是避免 FOIT 并尽可能好地管理 FOUT。这个 API 可以跟踪字体加载的每个阶段。当字体不可用时，您可以使用此信息使用 web 安全字体来设置文本样式，当字体完全加载后，您可以使用自定义字体来设置文本样式。

在撰写本文时， [Chrome、Firefox、Safari 和 Opera 的最新版本都支持这个 API](http://caniuse.com/#search=css%20font%20loading) ，而 IE/Edge 还不提供支持。但是，您可以使用一个小的 JavaScript 库，即[字体外观观察器](https://github.com/bramstein/fontfaceobserver)，来填充原生 CSS 字体加载 API，或者简单地让不支持的浏览器降级为 web 安全字体。

关于如何使用 CSS 字体加载 API 的简短教程，请阅读 Manuel Matuzovic 的[CSS 字体加载入门](https://medium.com/@matuzo/getting-started-with-css-font-loading-e24e7ffaa791)，并看看他的 [CodePen 演示](http://codepen.io/matuzo/pen/jrzyYA?editors=0011)。

## 未来:CSS `font-display`属性

自从它们第一次出现以来，您已经使用 CSS `@font-face`和`font-family`处理了自定义字体。期待 CSS 解决任何与下载自定义字体相关的问题是公平的。但是目前，只有基于 JavaScript 的解决方案是可用的。

然而，这不会是无限期的。一个智能 CSS 属性即将出现，`font-display`，它是 [CSS 字体渲染控件模块级别 1](https://tabatkins.github.io/specs/css-font-display/) 的一部分。

`font-display`让您:

*   决定是要使用后备字体显示文本，还是在字体加载时隐藏文本。
*   控制字体加载后的操作，即继续显示备用字体或用自定义字体替换它。您也可以在每个元素的基础上这样做。
*   为每种字体甚至特定元素指定您自己的超时值。

该属性的代码如下所示:

```
@font-face {
  font-family: Lato;
  src: url('/web/css/fonts/lato/lato-regular-webfont.woff2') format('woff2'),
  url('/web/css/fonts/lato/lato-regular-webfont.woff') format('woff');
  font-weight: 400;
  font-style: normal;

  /* This value replaces fallback when font has loaded */
  font-display: swap;
}

body {
  font-family: Lato, sans-serif;
  font-weight: 400;
  font-style: normal;
}
```

该属性的可接受值为:

*   **auto** :浏览器只是实现它们的默认行为。
*   **block** :浏览器在字体加载时绘制不可见的文本，并在加载后立即替换为所需的字体。
*   **交换**:当自定义字体不可用时，浏览器使用备用字体，但是当自定义字体加载后，立即将其交换进来。
*   **回退**:类似于`swap`的行为。但是，如果自定义字体加载时间过长，浏览器会在页面的生命周期内使用后备字体。
*   **可选的**:这个超级智能的值让浏览器只使用已经可用的自定义字体。如果不是，浏览器会在页面的生存期内使用回退。自定义字体仍然可以在后台下载，以便在后续的页面加载中使用。但是，如果浏览器检测到用户设备上的带宽有限，他们可能根本不会加载自定义字体。

从这些简短的描述中，你已经可以看出`swap`和`optional`是多么的有用。

[浏览器对`font-display`的支持](http://caniuse.com/#search=font-display)目前还不存在，但可以在 Chrome(通过实验性网络平台特性“标志”)和 Firefox(通过`layout.css.font-display.enabled`标志)中启用。

## FOUT 怎么样？

虽然上面概述的所有方法都可以消除 FOIT，无论是在以后交换自定义字体还是只显示备用字体，但它们对 FOUT 几乎无能为力。

虽然 FOUT 让用户仍然可以使用文本，但它很可能很烦人，特别是当备用字体比它临时替换的自定义字体更宽和/或更高时，这通常会导致页面内容发生变化。

您可以通过调整后备字体的 x 高度和宽度来减轻交换的不协调效果，以便尽可能地匹配所需的自定义字体的 x 高度和宽度。

你可以用很多方法做到这一点，但是我发现 Monica Dinculescu 的这个小小的[字体样式匹配工具](https://meowni.ca/font-style-matcher/)真的很有帮助。

## 结论

在这篇文章中，我概述了与在网站上使用自定义字体相关的文件大小和不可见文本闪烁的问题。

我展示了最新的方法，并为您提供了更多的参考资料，您可以从中了解更多信息，并学习如何在项目中实现这些技术。

## 分享这篇文章
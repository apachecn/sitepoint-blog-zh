# 网络性能技巧–超越基础

> 原文：<https://www.sitepoint.com/web-performance-tricks-beyond-basics/>

*本文由[新遗迹](http://newrelic.com/?r=phpm)赞助。感谢您对使 SitePoint 成为可能的赞助商的支持！*

这些年来，我们已经在 SitePoint 上讨论了很多关于性能的话题，我们认为现在是时候从更高级的角度重新讨论这个话题了。本文中提到的方法并不完全与 PHP 相关，但是如果使用得当，可以肯定它们会将您的应用程序带到一个全新的水平。请注意，我们不会涵盖通常的东西——对 CSS、JS 和图像的更少请求意味着更快的网站和[类似的提示](http://browserdiet.com/)是众所周知的。相反，我们将专注于一些不太为人知/使用的升级。

## 超文本标记语言

*   #### 删除不必要的标签

    元素越少越好。删除不必要的 HTML。

    ```
    <div>
        <div>
            <p>Some of my<span>text</span>.</p>
        </div>
    </div>
    ```

    和...相对

    ```
    <div>
        <p>Some of my<span>text</span>.</p>
    </div>
    ```

    当然，这并不适用于所有的场景，但是以一种让你尽可能多地移除 DOM 元素的方式来构建你的 HTML。

    你也可以通过省略一些不需要的标签来减少 HTML 文档的文件大小。这确实看起来很粗糙，而且似乎违反了标准，所以只有在部署到生产环境中时才应该这样做——这样你就不会混淆其他开发相同代码的开发人员。

*   #### 预取

    预取是当你预先告诉浏览器一个资源将被需要时。该资源可以是一个域的 IP(DNS 预取)，一个静态资源，如图像或 CSS 文件，甚至是整个页面。

    当您希望用户在访问您的网站后转到另一个域时，或者，例如，您将您的静态资源托管在像`images.example.com`这样的子域上，DNS 预取可以帮助消除 DNS 服务器将`images.example.com`解析为 IP 地址所需的几毫秒时间。收益不多，但是累积起来，它可以从用户浏览器的请求中节省一些加载时间。DNS 预取是通过`<head>`中的`<link>`完成的，比如:`<link href="//images.example.com" rel="dns-prefetch" />`，并且在所有主流浏览器中都得到支持。如果你有*任何*子域名，你希望当前访问者在完成他们当前所在的页面后加载，没有理由不使用 DNS 预取。

    当您知道下次访问将需要一些资源时，您可以预取它们并将其存储在浏览器缓存中。例如，如果你有一个博客，博客上有一篇由两部分组成的文章，你可以确保第二部分的静态资源(如图片)已经预加载。这是这样做的:`<link href="//images.example.com/sept/mypic.jpg" rel="prefetch" />`。Picasa 网络相册广泛使用这一功能将以下两张图片预取到您当前正在查看的图片中。在较旧的浏览器上，您可以通过在 JavaScript 中加载一个幻影图像元素来实现这一点:

    ```
    var i = new Image();
    i.src = 'http://images.example.com/sept/mypic.jpg';
    ```

    这会将图像加载到缓存中，但不会在任何地方使用它。不过，这种方法对 CSS 和 JS 文件不起作用，所以如果您想在传统的浏览器上预取这些资源，您必须有所创新。XMLHttpRequest 跃入脑海——通过 ajax 加载它们，不要在任何地方使用它们。见[此处](http://orip.org/2009/03/prefetching-javascript-or-anything-with.html)如何实现这一点。

    需要注意的一点是，只预取我们确定或几乎确定用户会需要的资源。如果用户正在阅读分页的博客文章，当然，预取。如果用户在表单提交屏幕上，那么一定要预取他们可以在提交后重定向到的屏幕上看到的资源。不要预取整个站点，也不要随机预取——考虑带宽，谨慎使用预取，记住移动设备。移动设备通常带宽有限，预先下载 2MB 的图像可能对用户不太友好。您可以通过选择性预取来避免这些问题——检测用户何时使用移动设备或[使用有限带宽连接](https://www.sitepoint.com/use-network-information-api-improve-responsive-websites/),并在这些情况下不使用预取。更好的是，在您的网站上添加设置，并要求人们同意预取-将他们的偏好保存到 localStorage 中，并与用户代理字符串进行哈希运算，允许他们分别在每个设备上允许或不允许预取。

    您还可以预取和预呈现整个页面。预取页面意味着获取它们的 DOM 内容 HTML。这通常不会提供很大的速度提升，因为大多数内容实际上是在 JavaScript、CSS 和图像中——这些内容不是通过页面预取获取的。目前只有 Firefox 完全支持这种抓取方式。预渲染则是另一回事——预渲染只存在于 Chrome 中，它不仅获取幕后的 DOM，还以 CSS、JS 和 images 的形式获取所有相关内容。事实上，它已经在后台呈现了整个页面——页面位于 RAM 中，完全打开并呈现，等待访问。这使得当用户点击预先呈现的链接时，改变是即时的，但是引入了与上一段中描述的相同的问题——带宽会受到影响。此外，您的服务器将这个预呈现器注册为访问，因此如果用户实际上改变了主意，没有最终打开预呈现的网站，您可能会得到一些扭曲的分析。先决条件语法是:`<link rel="prerender" href="http://example.com/sept/my-post-part-2">`。

    目前，只有一种合适的方法来检测你的页面被预渲染或预取，那就是使用[页面可见性 API](https://developers.google.com/chrome/whitepapers/pagevisibility) ，这是[目前在除 Android 浏览器和 Opera Mini](http://caniuse.com/#feat=pagevisibility) 之外的所有主流浏览器中支持的。您使用这个 API 来确保页面确实被查看，然后启动您可能正在进行的任何分析跟踪。

## 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

*   #### CSS 功能区

    使用 [CSSLint](https://github.com/stubbornella/csslint) 来验证你的 CSS 并指出错误和潜在的性能问题。阅读并遵守 CSSLint wiki 中提出的[规则](https://github.com/stubbornella/csslint/wiki/Rules)，尽可能写出最高效的 CSS。

*   #### CSS 解释

    很像数据库世界中的 SQL Explain，也有一个漂亮的 CSS Explain 工具——[https://github.com/josh/css-explain](https://github.com/josh/css-explain)。你可以用它来分析你的 CSS 选择器。如果你想立即测试它，把这个文件的[内容粘贴到你的浏览器控制台，然后发出一个类似`cssExplain('.item.subclass.anotherclass')`的命令。](https://github.com/josh/css-explain/blob/master/css-explain.js)

    [![](img/0105c7855a089afa0302edfcba3f1750.png)](https://uploads.sitepoint.com/wp-content/uploads/2014/01/Capture01.png)

    目标是在 1 到 10 的范围内得到尽可能低的分数。你也可以在我的 [jsFiddle](https://jsfiddle.net/dULHy/) 里试试。虽然不要把结果看得太重(你最好遵循 CSSLint 的建议)，但它们仍然很好地解释了选择器的复杂性，至少暗示了可能的问题。

*   #### 平移与顶部/左侧

    使用 CSS 2D 翻译移动对象，而不是顶部/左侧。当保罗·爱尔兰人和 T2·克里斯·科伊尔做了如此出色的工作时，试图详细解释这一点是没有意义的。确保你阅读/观看了他们的材料，并将这些知识融入到—*尽可能使用左上角的翻译*。

*   #### 平滑滚动

    你可能已经注意到，像脸书和 Google+这样的网站在打开时需要花费*时间*才能滚动。就好像他们需要时间热身。这是当今许多网站的一个问题，也是一个巨大的 UX 难题。让你的页面平滑滚动并不像看起来那么困难——尤其是当你知道要注意什么的时候。减少滚动延迟的关键是最大限度地减少绘画——绘画是当屏幕上的内容逐帧变化时发生的事情，浏览器需要在屏幕上重新绘制它——它需要计算一个新的外观，并将这个新的外观应用到渲染网站所包含的一个层上。关于这些问题的更多信息，以及如何诊断油漆问题，请看 [Paul Lewis 的精彩文章](http://www.html5rocks.com/en/tutorials/speed/scrolling/)。

## 计算机网络服务器

*   #### 用一些简单的胜利优化你的 PHP

    从 PHP 方面来说，你可以做很多事情来加速你的应用程序。要想轻松取胜，请参见 [Fredric Mitchell 的上一篇文章](https://www.sitepoint.com/easy-wins-performant-php/)或 SitePoint 上任何其他与[性能相关的文章。](https://www.sitepoint.com/php/performance/)

*   #### 使用谷歌的页面速度模块

    Google 的 [PageSpeed 模块](https://developers.google.com/speed/pagespeed/module)是一个你可以安装到 Nginx 和 Apache 中的模块，它会自动实现一些[网站优化的最佳实践](https://developers.google.com/speed/docs/best-practices/rules_intro)。该模块评估客户端所感知的网站性能，确保尽可能遵守所有规则，特别是改善静态资源的服务。它将为您缩小、优化和压缩 CSS 和 JavaScript，通过删除不使用的元数据来减小图像大小，正确设置 Expires 头以更好地利用浏览器缓存，等等。最重要的是，它不需要您进行任何架构更改。只要把它插入你的服务器，它就能工作。为了安装这个模块，[遵循这些指令](https://developers.google.com/speed/pagespeed/module/download)——你需要从 Nginx 的源代码中编译，但这仅仅是几个命令的工作。要正确地了解 Pagespeed，请观看下面的视频——它现在有点长且陈旧，但仍然是一个非常有价值的资源:

    [//www.youtube.com/embed/6uCAdQSHhmA](//www.youtube.com/embed/6uCAdQSHhmA)

*   #### 使用 SPDY

    在类似于 PageSpeed 的努力中，谷歌还领导了 [SPDY](https://developers.google.com/speed/spdy/) 的开发。mod_spdy 是另一个 Apache 模块，旨在为您的网站提供更快的服务。安装它[并不像人们希望的那样简单](https://code.google.com/p/mod-spdy/wiki/GettingStarted)，它也需要浏览器支持，但这一天[看起来更好](http://caniuse.com/spdy)。SPDY 实际上是一种协议(很像 HTTP 是一种协议),它在可能的情况下拦截和替换 HTTP 请求，从而更快地为站点服务。查看此[高级概述](https://code.google.com/p/mod-spdy/wiki/HowItWorks)了解更多信息，或者更好的是，此[新手友好分解](http://www.rudebaguette.com/2012/06/20/spdy/)。虽然使用 SPDY 可能有风险，因为我们仍在等待更广泛的采用，但收益似乎远远大于风险。

*   #### 对图像使用 WebP

    WebP 是一种图像格式，旨在取代所有其他格式——JPG、PNG 和 GIF。它支持阿尔法层(透明度)，动画，无损和有损压缩，等等。浏览器的采用[非常缓慢](http://caniuse.com/webp)，但是现在使用自动化 WebP 转换的工具很容易支持所有的图像类型，比如前面提到的 PageSpeed 模块(它[可以在运行中自动将](https://developers.google.com/speed/pagespeed/module/filter-image-optimize#webp)图像转换为 WebP)。关于 WebP 的深入介绍和讨论，请参见[这本综合指南](http://www.jonathanklein.net/2013/02/a-comprehensive-guide-to-webp.html)。

*   #### 用佐普夫利敷布

    使用 [Zopfli](http://googledevelopers.blogspot.com/2013/02/compress-data-more-densely-with-zopfli.html) 压缩来预压缩您的静态资源。这是一种开源的压缩算法，也是由谷歌倡导的，与网上常用的压缩方法相比，它将压缩率提高了 3-8%。在较小的网站上，这几乎没有什么不同，但是如果你正在[扩展你的应用](https://www.sitepoint.com/horizontal-scaling-php-apps/)或者为许多客户提供你的静态内容，这肯定会产生显著的不同，正如谷歌网络字体团队[所报告的](https://plus.google.com/u/0/114552443805676710515/posts/1sxencNkbNS):
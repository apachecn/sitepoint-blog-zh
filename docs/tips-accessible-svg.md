# 创建可访问 SVG 的技巧

> 原文：<https://www.sitepoint.com/tips-accessible-svg/>

可缩放矢量图形(SVG)以量子可访问状态存在。也就是说，SVG 同时是可访问的和不可访问的。令人高兴的是，没有 SVG 不确定性原理。我们知道 SVG 的哪些方面适合哪些人，哪些不适合，以及我们可以做些什么来改善这两个方面。SVG 1.0 由 W3C 于 2001 年发布。 [SVG 1.1](https://www.w3.org/TR/2003/REC-SVG11-20030114/) 于 2003 年问世， [SVG 1.1 第二版](https://www.w3.org/TR/SVG/)于 2011 年问世。

本文着眼于使 SVG 内容尽可能易于访问的方法。关于这种格式以及何时使用它的介绍，请阅读我们的 [SVG 指南](https://www.sitepoint.com/svg-101-what-is-svg)。

## Tip #1:美国 SVG

这听起来可能有点奇怪，尤其是在一篇以 SVG 开始的文章中。

事实上，SVG 最引人注目的特性之一也是最容易理解的特性之一。SVG 内容是可伸缩的，更重要的是，它的伸缩不会降低视觉质量。

从开发的角度来看，这使得它非常通用，但这也意味着视力低于 20/20 的人无论使用什么屏幕尺寸和分辨率，都可以享受清晰清晰的图像。

## Tip #2: Use inline SVG

让辅助技术(AT)如屏幕阅读器和语音识别工具能够访问 SVG 的最好方法是直接将它放入 HTML 中。用 HTML5 比用 HTML4.x 更简洁(就代码而言),因为 HTML 解析器自动切换模式并将 SVG 元素和属性放入正确的名称空间。

换句话说，如果您的 HTML 使用 HTML5 doctype，您不需要在<svg>标签中包含名称空间声明。</svg>

```
 <svg version="1.1" width="300" height="200">...</svg> 
```

使用其他方法来合并 SVG 会产生不太可靠的可访问性结果，或者会限制 AT 可用的信息。

例如，通过<embed>或<object>元素拉入的 SVG 内容不像内联 SVG 那样受浏览器支持，并且通过元素拉入的 SVG 在可以暴露给 at 的信息方面受到限制。</object>

SVG 支持 [DOM 2](https://www.w3.org/TR/DOM-Level-2-Core/) ，所以关于 SVG 的信息可以通过编程来检查和操作。不过，抓取 DOM 并不是 AT 获取信息的有效方式。当关于 DOM 的信息由浏览器的[可访问性 API](http://en.wikipedia.org/wiki/Category:Accessibility_API) 公开时，效率会高得多。所有现代浏览器都有一个可访问性 API，提供 at 可以查询的属性和方法。

出于本文的目的，我们将重点关注以下浏览器/屏幕阅读器组合:Windows 上的 Internet Explorer 11、Firefox 28 和 Chrome 30，以及 Jaws 15 和 NVDA 2014.1。iOS/OSX 上的 Safari 6 带 VoiceOver，安卓上的 Chrome 30 带 TalkBack。

在这些浏览器中，只有 IE 和 Firefox 此时通过它们的可访问性 API 公开了 [< svg >](https://www.w3.org/TR/SVG11/struct.html#SVGElement) 元素的作用。IE 将其识别为图形，Firefox 识别为图表。在这些浏览器使用的屏幕阅读器中，只有带有 IE 的 Jaws 利用这些信息来指示页面上有图形内容。

好消息是我们可以为此做些什么。保持这种想法，我们以后再来讨论它。

## 提示 3:提供一个标题

SVG [< title >](https://www.w3.org/TR/SVG11/struct.html#__svg__SVGDocument__title) 元素为 SVG 内容或其中的组件提供了一个人类可读的名称。< title >元素必须是其父元素的第一个子元素，父元素可以是 SVG [容器元素](https://www.w3.org/TR/SVG11/intro.html#TermContainerElement)或[图形元素](https://www.w3.org/TR/SVG11/intro.html#TermGraphicsElement)中的一个，或者是< svg >元素本身。SVG 1.1 规范特别提到了为< svg >元素提供标题的重要性。

```
 <svg version="1.1" width="300" height="200">
<title>Green rectangle</title>
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
</svg> 
```

默认情况下,<title>元素的内容不会以可视方式呈现(尽管可以设计成这样)。它应该通过浏览器的可访问性 API 公开，特别是给它的父元素一个可访问的名称。</title>

在前面的例子中，<title>元素的内容为<svg>元素提供了一个可访问的名称，使屏幕阅读器能够将这部分 SVG 内容与页面上的其他内容区分开来。</svg></title>

简单地说，<title>元素提供了 SVG 内容的可选文本描述。就像对待 HTML <img/>元素的 alt 属性一样。</title>

目前只有 IE 和 Firefox 正确地公开了这些信息，只有 IE 中的 Jaws 使用了这些信息。不过，我们可以提高这些几率。

当 [ARIA 1.0](https://www.w3.org/TR/wai-aria/) 出现在 HTML 代码中时，它会使浏览器更新通过其可访问性 API 提供的信息。ARIA 具有跨浏览器和屏幕阅读器的良好支持(尽管目前还没有其他支持),因此它可以帮助盲人和弱视者提高 SVG 内容的可访问性。

ARIA 支持是在 SVG 2.0 规范中正式引入的，但是仍然可以在 SVG 1.1 内容中使用 ARIA。本文中的一些例子会在您验证 HTML 时产生一个标志，但这是一个合理的例外。

```
 <svg version="1.1" width="300" height="200" aria-labelledby="title">
<title id="title">Green rectangle</title>
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
</svg> 
```

在这个例子中,[aria-label edby 属性](https://www.w3.org/TR/wai-aria/states_and_properties#aria-labelledby)加强了< svg >和< title >元素之间的关系。

换句话说，它确保了<title>元素的内容被识别为 SVG 内容的可访问名称。</title>

结果是，现在 Chrome 和 Safari 也将“绿色矩形”作为 SVG 内容的可访问名称，包括 VoiceOver、TalkBack、Jaws 和 NVDA 在内的屏幕阅读器都在至少一个浏览器中使用它。

## 提示 4:提供描述

SVG[<>](https://www.w3.org/TR/SVG11/struct.html#DescElement)元素在许多方面与< title >元素相似。默认情况下，它不进行可视化渲染，但它是供人们使用的。它不是为其父元素提供名称，而是提供描述。就可访问性而言，< desc >元素提供了比<标题>所允许的更详细的关于 SVG 内容的信息。

```
 <svg version="1.1" width="300" height="200">
<title>Green rectangle</title>
<desc>A light green rectangle with rounded corners and a dark green border</desc>
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
</svg> 
```

对<desc>元素的浏览器可访问性支持与对<title>元素的支持大致相同。<desc>元素的内容应该作为包含元素的可访问描述公开，但是同样只有 IE 和 Firefox 才这么做。</desc></title></desc>

艾瑞亚也来帮忙了，但不是你想的那样。显而易见的解决方案应该是使用 [aria-describedby 属性](https://www.w3.org/TR/wai-aria/states_and_properties#aria-describedby)来强化< svg >和< desc >元素之间的关系。

```
 <svg version="1.1" width="300" height="200" aria-labelledby="title" aria-describedby="desc">
<title id="title">Green rectangle</title>
<desc id="desc">A light green rectangle with rounded corners and a dark green border</desc>
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
</svg> 
```

尽管浏览器现在通过其可访问性 API 公开了正确的信息，但屏幕阅读器支持却不太一致。带有 IE 浏览器的大白鲨和 NVDA，带有 Safari 浏览器的 VoiceOver 和带有 Chrome 浏览器的 TalkBack 都使用了无障碍描述，但是带有 Chrome 浏览器和 Firefox 浏览器的大白鲨和 NVDA 却没有。

目前的一个解决方法是让 aria-labelledby 属性工作起来稍微困难一点。

```
 <svg version="1.1" width="300" height="200" aria-labelledby="title desc">
<title id="title">Green rectangle</title>
<desc id="desc">A light green rectangle with rounded corners and a dark green border</desc>
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
</svg> 
```

这并不理想，因为名称和描述在概念上是不同的东西，并且服务于不同的目的。为了尽可能广泛地提供相关信息，这种方法确实奏效了，尽管有些困难。

## 技巧 5:使用文本

除了光栅图像(png、jpg 等)之外，文本通常是可访问的。).屏幕阅读器(或搜索引擎)无法检测到这些图像中的文本，也无法调整其大小以方便阅读。即使在缩放时，图像中文本的质量也会明显下降，弱视人群的 UX 也会随之恶化。

```
 <svg version="1.1" width="300" height="200" aria-labelledby="title desc">
<title id="title">Green rectangle</title>
<desc id="desc">A light green rectangle with rounded corners and a dark green border.</desc>
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
<text x="35" y="30" font-size="1em" text-anchor="middle" fill="#000000">Website</text>
</svg> 
```

SVG [<文本>](https://www.w3.org/TR/SVG11/text.html#TextElement) 元素使得图形内容中的文本具有高度的可访问性。浏览器将<文本>元素的内容呈现为纯文本，因此它可供屏幕阅读器使用，并且因为它是 SVG，所以它可以根据图形内容成比例地调整大小，以方便视力低于 20/20 的人。

您仍然希望使默认的文本大小便于阅读，但是除此之外，人们可以调整 SVG 以适合自己。

## 技巧 6:让它有焦点

如果您的 SVG 包含交互式内容，您需要确保它能够获得键盘焦点。和 HTML 一样，最好的方法是首先使用一个为获得焦点而设计的元素。

```
 <svg version="1.1" width="300" height="200" aria-labelledby="title desc">
<title id="title">Green rectangle</title>
<desc id="desc">A light green rectangle with rounded corners and a dark green border.</desc>
<a xlink:href="[http://example.com](http://example.com)<wbr>">
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
<text x="35" y="30" font-size="1em" text-anchor="middle" fill="#000000">Website</text>
</a>
</svg> 
```

SVG [< a >](https://www.w3.org/TR/SVG11/linking.html#AElement) 元素应该意味着键盘用户可以在使用 tab 键浏览内容时聚焦在链接上。本文中提到的所有浏览器/屏幕阅读器组合都支持这种行为。

## 技巧 7:给它一个角色

还记得你当时的想法吗？我们已经到了可以解决<svg>元素角色不能被所有浏览器正确公开的问题的时候了。</svg>

所有元素都有一个角色——它确定元素实现的目的。元素的角色应该通过浏览器的可访问性 API 来公开，但是您会记得只有 IE 和 Firefox 正确地向 at 公开了<svg>元素，并且只有带有 IE 的 Jaws 利用了这些信息。</svg>

ARIA [角色属性](https://www.w3.org/TR/wai-aria/roles)可用于强制向屏幕阅读器公开正确的角色。在< svg >元素的情况下，我们可以应用 [img 角色](https://www.w3.org/TR/wai-aria/roles#img)。

```
 <svg version="1.1" width="300" height="200" aria-labelledby="title desc" role="img">
<title id="title">Green rectangle</title>
<desc id="desc">A light green rectangle with rounded corners and a dark green border.</desc>
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
</svg> 
```

这大大提高了浏览器的赔率，IE，Firefox，Chrome 和 Safari 都正确地暴露了角色。屏幕阅读器支持仍然有点不一致，但也有所改善，除了 Chrome 中的 Jaws 和 NVDA，以及 Firefox 中的 Jaws。Safari 的 VoiceOver、Chrome 和 Jaws 的 TalkBack 以及 IE 的 NVDA 现在都可以识别页面上的图形内容。

尽管在<svg>元素上使用 img 角色并不总是合适的。如果您的 SVG 内容是纯图形的，这没问题，但是如果它包含交互式内容，可能需要一种不同的方法。</svg>

```
 <svg version="1.1" width="300" height="200" aria-labelledby="title desc">
<title id="title">Green rectangle</title>
<desc id="desc">A light green rectangle with rounded corners and a dark green border.</desc>
<a xlink:href="[http://example.com](http://example.com)<wbr>" tabindex="0" role="link">
<rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
<text x="35" y="30" font-size="1em" text-anchor="middle" fill="#000000">Website</text>
</a>
</svg> 
```

在这个例子中，[链接角色](https://www.w3.org/TR/wai-aria/roles#link)已经被应用到 SVG <元素>中。现在，我们在本文中看到的所有浏览器/屏幕阅读器组合都可以识别出这个链接是一个链接，除了 IE 中的 NVDA。

## 技巧 8:创造一个替代方案

虽然使用 ARIA 可以增强 SVG 的可访问性，但浏览器和 AT 对 SVG 的本地可访问性支持仍在发展中。SVG 2.0 将会改变这一点，但是现在我们需要对当前的事态持现实态度。

当您在 SVG 中创建一些东西时，以一种具有更好的可访问性支持的格式提供相同的信息和/或功能是一个好主意。如果您的 SVG 内容本质上是纯图形的，那么使用 ARIA 来增强<title>和<desc>元素将会达到目的。</desc></title>

如果您的 SVG 包含任何更复杂或更具交互性的东西，那么您需要考虑用不同的方式来表示它。例如，SVG 图中呈现的信息也可以在 HTML 表中提供，也许可以通过[选项卡式界面](http://hanshillen.github.io/jqtest/#goto_tabs)选择视图。

在某些方面，SVG 是被遗忘的网络技术。尽管它有巨大的实践和创造潜力，它的可访问性潜力也同样强大。本文给出了使 SVG 1.1 内容更易访问的建议，但是真正令人兴奋的是 SVG 2.0。

如果你有兴趣帮助我们实现这一点，你可以加入 W3C [无障碍 SVG 社区组](https://www.w3.org/community/svga11y/)，或者加入 W3C [SVG 工作组](https://www.w3.org/Graphics/SVG/WG/wiki/Main_Page)。

## 分享这篇文章
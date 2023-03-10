# 延迟加载图像:如何不真正惹恼你的用户

> 原文：<https://www.sitepoint.com/lazy-loading-images-not-really-annoy-users/>

![Lazy loading](img/b8c6ac8eee67fcfb7b085c265c2d157e.png)

网络上几乎到处都是图片。它们在长长的文本中为用户提供关键信息、视觉趣味和重要的“断点”。通常图像是内容的核心焦点。这可能是由于网站的意图——例如 Imgur 或[Flickr](http://www.flickr.com/)——或者仅仅是因为文字可能无法像图像一样充分地解释事情。

本教程将讨论当你必须加载大量图片时，如何延迟加载图片。为什么延迟加载图像很重要，以及如何以不影响用户体验的方式加载图像。

我还建议你阅读关于图像优化的 SitePoint 教程。这两个教程有相似的目标，当一起使用时，可以显著改善用户体验和网页的加载时间。

## 为什么要延迟加载图像？

在我们深入挖掘之前，也许我们应该先问一个问题，为什么你要经历这么多麻烦？

一个重要的原因是它应该大大减少页面加载时间。通常脚本在运行之前需要完全加载 DOM。如果需要这些脚本向用户提供一些信息或者提供一个重要的功能，那么等待 8 到 10 秒钟会让用户感到沮丧。大多数用户不会等那么久，他们会为了更快的网站而放弃你的网站。

另一个好处是为您和用户节省带宽。如果用户不打算滚动前 10 张图片，那么加载所有图片是没有用的。假设一个网页上有 30 张图片。即使这些图像的大小都只有 30KB。每次点击网页可以节省 600KB 的带宽。对于一个每月有 200 万页面浏览量的高流量网站来说，仅仅延迟加载图片就可以节省大约 100 万兆字节的带宽。

## 设置插件

有很多插件可以用来加载图片。对于本教程，我将使用 [jQuery 惰性加载插件](http://www.appelsiini.net/projects/lazyload)。这个插件大约 3.3KB，有很多有用的功能。你可以在你的代码中直接包含这个缩小的版本来保存一个额外的请求，或者你可以[从一个 CDN](https://cdnjs.cloudflare.com/ajax/libs/jquery.lazyload/1.9.1/jquery.lazyload.min.js) 中加载它。

无论你使用哪个插件，你都必须在标记中做一些改变。您需要为所有图像添加高度和宽度属性以及一个`.lazy`类。此外，您需要将`src`属性更改为`data-original`。这是您的图像标签更改后的样子:

```
<img class="lazy" data-original="image.jpg" width="640" heigh="480">
```

现在，在您的 JavaScript 代码中包含以下代码片段:

```
$("img.lazy").lazyload();
```

这就是你需要做的。现在，你所有的图片都将缓慢加载。这里有一个 CodePen 演示:[http://codepen.io/SitePoint/pen/WrXjmQ](http://codepen.io/SitePoint/pen/WrXjmQ)

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen[Lazy Loading Demo-by Monty Shokeen](http://codepen.io/SitePoint/pen/WrXjmQ/)。
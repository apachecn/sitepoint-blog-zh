# Google 托管 jQuery 的优势

> 原文：<https://www.sitepoint.com/benefits-google-hosted-jquery/>

基本上，您应该在网站的 header 标签中看到这种代码:

这是在你的网站中包含 jQuery 库的正常方式。这样做没有问题，但是有更好的方法。

Google AJAX Libraries 为直接来自 Google 数据中心网络的用户提供 jQuery。与在您自己的服务器上托管 jQuery 相比，这有几个优点:

1.  **潜伏期缩短**
2.  **并行度增加**
3.  **更好的缓存**

我将解释谷歌 CDN 的这三个好处，并向您展示一些使用这项服务的例子。

## 减少延迟

CDN 是[内容交付网络](https://en.wikipedia.org/wiki/Content_Delivery_Network)的简称——它将您的静态内容分发到不同物理位置的服务器上。当浏览器设法解析这些文件的 URL 时，它们的下载将自动来自网络中最近的可用服务器。

在 Google AJAX Libraries CDN 中，不在服务器附近的用户仍然可以下载 jQuery，速度比强迫他们从服务器下载要快。

## 并行度提高

为了帮助避免不必要的服务器过载，可以同时限制浏览器的连接数。根据浏览器的不同，该限制可能会最小化为每个主机名两个连接。

在 Google AJAX Libraries CDN 的帮助下，它将消除对您网站的一个请求，允许您的本地内容被并行下载。然而，对于使用六连接浏览器的用户来说，这并没有太大的区别，但对于那些仍然使用只使用两个连接浏览器的用户来说，区别是显而易见的。

## 更好的缓存

理想情况下，为您的 jQuery 使用 Google AJAX Libraries CDN 的最大但最少提及的好处是，您的用户根本不需要下载 jQuery。

你的缓存有多激进并不重要。但是如果你在本地托管 jQuery，用户至少需要下载一次。即使他们的缓存中有大量相同的 jQuery 副本，当用户第一次访问您的网站时，这些也没有用。

## 怎么做

现在，你可能已经被谷歌 AJAX 库 CDN 说服了。是时候告诉你如何做这些事情了。

在两种可用的方法中，这个方法是 Google 推荐的:

google.load()方法提供了最多的功能和性能。

*举例:*

这没有什么错，实际上这是对自己托管 jQuery 的一个改进，尽管它并没有提供最好的性能。

![](img/7c611a95d625f2ac17ee583edd8810bc.png)

上图清楚地显示了加载、解析和执行 jsapi 会对实际的 jQuery 请求产生负面影响。数额不算大，但是耽误了。

最糟糕的是你不能可靠地使用一个$(文档)。ready()处理程序与这种加载方法结合使用。它需要 setOnLoadCallback()。

## 回到要点

在 google.load()方法的所有缺点中，我推荐使用这种老方法

示例:

这避免了 jsapi 延迟，并消除了三个无用的 HTTP 请求。我会推荐和喜欢这一个。

## 结论

归根结底，决定权在你手中。您可以选择在服务器上本地托管 jQuery，或者使用 Google AJAX 库 CDN。但是我希望上面的解释完全展示了通过 Google AJAX Libraries CDN 托管 jQuery 比自己托管 jQuery 的优势。理想情况下，Google AJAX Libraries CDN 显然更好。

## 分享这篇文章
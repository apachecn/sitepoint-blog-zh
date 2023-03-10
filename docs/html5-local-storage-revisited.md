# HTML5 本地存储再探

> 原文：<https://www.sitepoint.com/html5-local-storage-revisited/>

本地存储是 [HTML5 Web 存储 API](https://www.w3.org/TR/webstorage/) 的一部分，它允许你在浏览器中存储数据。与 cookies 不同，使用本地存储器存储的数据不会发送回服务器。所有数据都保存在客户端，您目前可以存储 2MB 到 10MB 的数据。这一限制与具体的浏览器、协议(HTTP 或 HTTPS)、端口和正在使用的顶级域名有关。

在本文中，我们将讨论如何使用这个 API 来提高网站的性能。我假设您知道什么是本地存储以及公开的方法，但是如果您需要复习，我建议您阅读 Colin Ihrig 的文章[Web 存储 API 的概述](https://www.sitepoint.com/an-overview-of-the-web-storage-api/)。

## 可用磁盘空间

在我们开始讨论本地存储之前，我想给你一个主流移动和桌面浏览器中可用磁盘空间的概述。以下表格基于文章[“在移动浏览器上使用配额”](http://www.html5rocks.com/en/tutorials/offline/quota-research/)。

### 移动浏览器:

| 浏览器 | 铬 | 安卓浏览器 | 火狐浏览器 | iOS Safari |
| --- | --- | --- | --- | --- |
| **版本** | Forty | Four point three | Thirty-four | 6-8 |
| **可用空间** | 10MB | 2MB | 10MB | 5MB |

### 桌面浏览器:

| 浏览器 | 铬 | 歌剧 | 火狐浏览器 | 旅行队 | 微软公司出品的 web 浏览器 |
| --- | --- | --- | --- | --- | --- |
| **版本** | Forty | Twenty-seven | Thirty-four | 6-8 | 9-11 |
| **可用空间** | 10MB | 10MB | 10MB | 5MB | 10MB |

## 本机缓存与本地存储

使用本地存储时，您的数据将保留在客户端上，并在会话和设备重新启动时保持不变。正如我在介绍中提到的，本地存储 API 的限制与特定的浏览器(如前面的表格所示)、协议、端口和正在使用的顶级域相关联。相比之下，浏览器本机缓存上的可用空间是跨网站共享的，在移动设备上要小得多。它经常被冲洗，有时甚至在同一次就诊中。移动设备还有一个额外的问题:它们不如桌面设备强大，所以实现良好的性能是必须的。

关于本地存储性能已经有了很多讨论。例如，曾供职于 Mozilla 的 Christian Heilmann 写道[“没有简单的本地存储解决方案”](https://hacks.mozilla.org/2012/03/there-is-no-simple-solution-for-local-storage/)。如果不小心使用，本地存储可能会影响性能。你需要考虑的第一件事是，它是一个同步 API，因此它阻塞了主 UI 线程。本地存储从硬盘驱动器写入和读取数据，这可能是比从内存读取更昂贵的操作。为了让您能够访问数据，本地存储需要从磁盘读取数据，这就是性能受到影响的地方。对于少量数据来说，这种性能影响不是主要问题，但是在使用完全存储限制时，这种影响会很明显。

作为一个好的习惯，你应该尽可能少的阅读。此外，因为我们处理的是同步 API，所以您应该尝试只在触发了`window.onload`事件之后从本地存储读取数据，以避免阻塞 UI 线程。

## 事情已经改变了

但是事情正在好转。Mobify 的 [Peter McLachlan](http://www.mobify.com/blog/author/peter-mclachlan/) 发表的一篇文章解释说[本地存储可以比移动设备上的本地缓存快 5 倍](http://www.mobify.com/blog/smartphone-localstorage-outperforms-browser-cache/)。

![Smartphone local storage outperforms browser cache](img/be89fb73f4c809a305f20f236ce91948.png)

在同一篇文章的[附录中，您可以看到移动浏览器上本地存储性能的演变以及改进了多少。您还可以看到，本地存储一直比本机缓存快。](http://www.mobify.com/blog/smartphone-localstorage-outperforms-browser-cache/appendix/)

## 谁在使用本地存储？

最近有一些网站使用本地存储来缓存资产的案例，例如 [The Guardian](http://www.theguardian.com/) 使用本地存储来保存关键路径 CSS。您可以查看[在 Velocity conference 2014 上的演示文稿](https://speakerdeck.com/patrickhamann/css-and-the-critical-path-cssconfeu-september-2014)，了解他们如何做到这一点的更多信息。

Smashing Magazine 最近也开始在本地存储中缓存网络字体。在这篇关于最近在他们的网站上实现的一些性能改进的文章中，他们报告了延迟 web 字体并将它们缓存在本地存储中，这是导致最有效改进的变化之一。

## 关于隐私浏览的一个注记

据[caniuse.com](http://caniuse.com/#search=localstorage)报道，在“已知问题”标签下，当在私密或匿名模式下运行时，Safari、iOS Safari 和 Android 浏览器不支持在本地存储中设置项目。

Chrome 和 Firefox 等其他浏览器允许在隐私模式下将数据存储在本地存储中，但当您退出隐私模式时，数据会被清除。这是由于隐私问题，因为有人可能会使用持久数据来了解用户在隐私模式下的行为。

如果在先前会话中设置的值在后续访问中应该存在，则此问题可能会破坏您的应用程序的行为。因此，为了安全地使用本地存储，不仅要测试支持，还要测试获取和设置项目的能力，这是一个很好的实践。

有关隐私模式下本地存储行为以及如何在不同浏览器中检查本地存储内容的更多信息，您可以使用文章[“测试时不要忘记检查隐私浏览模式”](http://arbitraryreason.com/dont-forget-to-check-private-browsing-mode-when-testing/)作为参考。

## 结论

也许是时候我们开始重新审视本地存储及其潜在用途了，尤其是在移动设备上，我们可以使用它来避免延迟瓶颈。我们可以开始考虑缓存资产的新方法，然后将它们立即提供给我们的用户。我们已经看到了一些以非常规方式成功实现本地存储使用的例子。

## 分享这篇文章
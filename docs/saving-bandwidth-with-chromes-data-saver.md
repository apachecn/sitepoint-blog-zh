# 使用 Chrome 的数据保护程序节省带宽

> 原文：<https://www.sitepoint.com/saving-bandwidth-with-chromes-data-saver/>

![Chrome Data Saver](img/c8523ef502f2a4ba5ee2c179098614c1.png)

你可能会认为，随着手机越来越重要，开发人员会迎合他们，创建令人敬畏的低带宽网站，当你外出时加载速度很快。

总的来说，你错了。每年平均页面权重/资源数量都在增加，[今年看起来没有什么不同，因为页面权重增加了 16%](https://www.sitepoint.com/average-page-weight-increased-another-16-2015/) 。

随着所有花哨的动画、滚动元素和其他漂亮的东西，最终会出现一个较慢的网络，因为下载速度难以跟上页面大小。

如果你想加快你的浏览体验*和*减少你的下载量，也许值得看看 [Chrome 的数据保护选项](https://developer.chrome.com/multidevice/data-compression)。

## 数据保护程序简介

Data Saver 是 Chrome 的一项新功能，可以显著减少移动数据的使用。

早在 2014 年，Chrome 的开发人员就开始研究一种实验性的方法来自动减少页面的总重量——通过利用他们自己的服务器和技术。这实际上与 [Opera 一段时间以来提供的<q>涡轮模式</q>](http://www.opera.com/turbo) 非常相似。

通过在您的设置中激活数据保护程序，可以在您的手机(Android 和 iOS)上启用它。Data Saver 也可以在 Opera 的桌面浏览器上通过[安装 Data Saver 扩展](https://chrome.google.com/webstore/detail/data-saver/pfmgfdlgomnbgkofeojodiodmgpgmkac?hl=en)激活。

## 它是如何工作的？

本质上，Data Saver 将你的网页内容推送到谷歌的服务器上，而不是你的设备上。

当你发出一个请求来加载你最喜欢的网站，而不是你的浏览器下载你的内容，Data Saver 将连接到谷歌的数据中心的优化服务器之一，并为你提供优化的内容。这里有一个简单的例子:

![Chrome Data Saver overview](img/d24102c6c4a2e57b1e07e8f10e349877.png)

图片来自[谷歌的博客](https://developer.chrome.com/multidevice/data-compression)

## 数据保护程序实际上做什么？

Data Saver 的工作原理是让 Chrome 的网络流量通过谷歌的服务器。服务器执行自动压缩，缩小，图像和其他元素的转换，以节省您宝贵的带宽。

谷歌实施的优化过程有几个步骤来将这一切结合在一起。

### 通过 HTTP/2 的内容

在可能的情况下，优化服务器通过 HTTP/2 而不是 HTTP 请求内容。 [HTTP/2 是 HTTP](https://www.sitepoint.com/http2-the-pros-the-cons-and-what-you-need-to-know/) 的增压版。没有几十个连接，只有一个协调的 TCP 连接，减少了传统上与 HTTP 相关的来回服务器，从而帮助您更快地获取内容。此外，HTTP/2 可以缓存未来的资源，以便在需要时可以立即加载它们。

### 图像的自动转换

图像和富媒体是下载速度最慢的组件。有时，一张图片可能比几十个脚本和样式表更重要。

通过 Data Saver，优化服务器自动转换为新的 [WebP 格式](https://developers.google.com/speed/webp/)。WebP 是一种新的图像格式，同时支持**无损**(如 PNG)和**有损**(如 JPG)格式。自动转换为 WebP [节省了大量空间](https://developers.google.com/speed/webp/):平均来说，无损图像(如 PNGs)在转换后会缩小 25%，有损图像(如 JPGs)会缩小 34%。

你可能会认为质量反映了尺寸，但它们之间的差异是微不足道的。([你可以在谷歌开发者网站上看到一个比较](https://developers.google.com/speed/webp/gallery)。)

### 没有显示任何图像

对于较慢的连接，谷歌不会优化你的图片，而是选择不发送任何图片。页面加载后，它会提示您启用图像(然后它会获取、压缩并发送到浏览器)。

默认情况下没有办法强制这样做，所以如果你有一个不错的 3G 连接——甚至 4G——没有自动选择这样做的选项(这似乎取决于谷歌的判断)。

### 缩小和压缩

优化过程的另一部分是所有的资源将自动缩小。

谷歌的服务器将检查所有 CSS、JS 和 HTML 内容，并自动删除所有空白以缩小尺寸。它还确保所有内容都使用 gzip 压缩(进一步加快了过程)。

### 更好的 DNS 请求

当您的设备请求一个站点时，它通常需要进行 DNS 查找(将 URL 转换为 IP 地址)。作为压缩过程的一部分，谷歌的服务器将执行 DNS 请求，要么从缓存中获取所需信息，要么直接获取。

这看起来像是一个小小的改进，但是这确实有助于减少名称解析并加快整个过程。

### 隐私和安全流量

需要注意的一点是**数据保护程序将只适用于标准的 HTTP 流量**。一旦你想通过 HTTPS 加载一个安全的页面，或者如果你正在使用匿名模式，数据保护程序会自动关闭，页面会像往常一样处理。

## 启用数据保护程序

Data Saver 于 2015 年 12 月引入 Chrome 移动设备，所以你的移动版 Chrome(Android 和 iOS)很有可能已经准备好了 Data Saver。打开 Chrome，进入**设置**，然后进入**数据保护**。入门就是这么容易。

如果你使用的是桌面版 Chrome，你可以通过下载 [Data Saver Chrome 扩展](https://chrome.google.com/webstore/detail/data-saver/pfmgfdlgomnbgkofeojodiodmgpgmkac)来启用 Data Saver。这是谷歌的官方扩展，一旦你安装并运行它，你会得到一个漂亮的数据图表，显示你保存了多少数据。

![data graph showing how much data you've saved](img/21485c1b51f0b3d4b7cab398b27639ef.png)

您可以使用这个图表来快速查看您节省了多少带宽。此外，您可以点击**详细信息**页面，查看每个站点的数据使用明细:

![data usage breakdown](img/977bebafe7f18ab429712112e6d162ac.png)

## 现实世界中的数据保护程序

现在你已经掌握了 Data Saver 的功能，让我们一起比较一些启用了这个功能的网站。

我们感兴趣的是站点的总大小，它的响应时间，以及在这个过程中是否有任何问题(因为涉及到自动压缩)。

每个站点都被加载了几次，并选择了一个公平的平均值。(由于这些网站大多都有广告，每个页面的加载量都会有细微的不同。)我们感兴趣的是有和没有数据保护的站点的平均速度和权重(当缓存被禁用时)。

### SitePoint.com

没有比家更好的起点了。查看 SitePoint 网站，它主要由 JS 文件和小图片组成。

**禁用数据保护:**

![SitePoint page load with Data Saver disabled](img/d601b75257c6d9d63499509d8cb0234e.png)

该网站最终有 133 个请求，重约 1.2 兆字节。加载平均需要大约 3 秒钟。

**数据保存器启用:**

![SitePoint page load with Data Saver enabled](img/50a283c56e9c865919f647b70ae03559.png)

启用 Data Saver 后，页面权重下降到 700–780 kb 左右。请求的数量保持不变，尽管页面加载时间足足多了 1 秒钟。

这里的减少主要来自缩小 JS 和 HTML 资源。Data Saver 为我们节省了很大一部分带宽，只有轻微的延迟。

### 网络小鸟数码

在你自己的网站上比较潜在的节省总是好的。这个网站是更多的媒体，有几个组合图片，一个大滑块和较小的缩略图。

**禁用数据保护:**

![Web Bird page load with Data Saver disabled](img/163e287f0f85bfb0ad6e7d84d1de15c7.png)

在 63 次请求中，大约有 1.2MB 的数据被下载。加载时间大约在 1.8-2 秒内完成。

**数据保存器启用:**

![Web Bird page load with Data Saver enabled](img/e5f461ea000caffb81516fd07d1ab233.png)

启用 Data Saver 后，大小会急剧下降，降至 650–700 kb 左右。尺寸的减小几乎完全来自于图像到 WebP 格式的动态转换。这种数据节省的代价是下载速度，平均多花 0.5 秒下载。

### ebay.com

一个受欢迎的拍卖网站，易贝的主页(当你没有登录时)展示了几个当天的最新交易，以及一些随机收集的产品，按行动分类。这是另一个媒体密集的网站。

**禁用数据保护:**

![eBay page load with Data Saver disabled](img/031415ba15bbb14047add5be44c25ded.png)

该网站重约 2.4MB，有 200 个请求。所有这些都需要大约 4.5 秒才能完全加载。

**数据保存器启用:**

![eBay page load with Data Saver enabled](img/bfa8496a3cd92829d416f4c935bad459.png)

当 Data Saver 启用时，页面重量急剧下降，降至仅 1.4MB。通常，Data Saver 会使页面加载速度变慢，因为它必须通过谷歌的服务器推送所有内容。然而，对易贝来说，它似乎保持不变(有时甚至更快)。

这是一个很好的例子，我们几乎将下载量减半，并且没有任何损失。

## 把一切都包起来

总的来说，Data Saver 是一个非常有用的功能，可能会帮助每个人减少他们的移动数据使用。

我们已经通过几个示例站点了解了这是如何工作的。一般来说，我们最终会减少 30-40%的页面总重量，而速度的下降可以忽略不计。

和所有事情一样，它也有缺点。由于 Data Saver 正在压缩，您可能会牺牲质量和速度来换取较小的下载大小。同样值得一提的是，在使用 Data Saver 时，您依赖第三方公司(Google)来尊重您的隐私和数据。

所以，最后，你需要试试 Data Saver，看看它是否适合你。如果你已经试过了，请在评论中告诉我们你的想法。

## 分享这篇文章
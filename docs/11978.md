# Opera 10 的新特性(第 2 部分:性能)

> 原文：<https://www.sitepoint.com/opera-10-turbo-performance/>

![Opera 10](img/524eb89b3befc9dbe4a86356fe65f729.png "Opera 10")在[昨天的 Opera 10](https://www.sitepoint.com/opera-10-new-features/) 预览中，我们检查了界面、新功能、标准支持和开发者工具。今天，我们来看看浏览器的性能。

Opera 10 并不逊色。它立即启动，页面快速呈现，web 应用程序保持响应。它肯定是 Chrome 的对手。

内存使用似乎比其他浏览器更有效。在一个典型的浏览测试中，我估计 Opera 10 使用的内存大约是 Firefox 3 的一半(没有附加组件)，最小化浏览器会进一步减少它(无论 Firefox 是否设置了 config.trim_on_minimize)。

## JavaScript 基准测试

JavaScript 基准测试并不能说明全部情况；带宽、渲染速度和 DOM 操作都有助于提高 web 应用程序的效率。然而， [SunSpider JavaScript 基准测试](http://www2.webkit.org/perf/sunspider-0.9/sunspider.html)确实提供了一个让浏览器互相竞争的机会:

| **Safari 4 beta** |  | One thousand eight hundred and seventy-nine |  |
| **铬合金** |  | One thousand nine hundred and fifty-seven point four |  |
| **火狐 3.0.10** |  | Six thousand eight hundred and eleven |  |
| **Opera 10 beta** |  | Seven thousand eight hundred and sixty-five point four |  |
| **IE8** |  | Ten thousand eight hundred and forty-seven point two |  |
| **IE6** |  | *还在等待结果！* |  |

我要补充的是，这些测试几乎没有在严格的实验室条件下进行，但是，尽管 Opera 10 比 Opera 9.6 快 15%左右，但在赶上基于 webkit 的浏览器之前，它还有一段路要走。

## opera Turbo–*杀手级功能？*

Opera Turbo 有可能使您的互联网体验速度提高 4 倍以上——在 Opera Mobile 上甚至更好。这是通过在下载文件之前压缩文件来实现的，因此它在较慢的网络上可以证明是非常宝贵的。

单击状态栏左侧的图标可以启用或禁用加速模式。该数字表示大致的速度增量:

![Opera Turbo icon](img/9142ecfb3b00ccf7548421ec0bd16bb2.png "Opera Turbo icon")

Opera 可以配置为当带宽变得有限时自动切换到 Turbo 模式，并警告您这可能是优势还是劣势:

![Opera Turbo settings](img/507c0861714d678aa0a3d0be3ba20811.png "Opera Turbo settings")

下载量的减少是通过 Opera Turbo 代理服务器路由您的互联网流量来实现的。最明显的区别是 JPEG 图像的压缩率要高得多，而且会降低质量:

![Opera Turbo image comparison](img/8ad25020f9af3df4e8dc45f695e27d42.png "Opera Turbo image comparison")

GIF 和 PNG 图像不受影响，可能是因为它们不是有损格式。我怀疑 Opera 对 HTML、CSS 和 JavaScript 文件使用了其他压缩方法，比如 GZIP 或文件合并。但是，代码不会以任何方式更改，甚至会保留空白。

Opera Turbo 确实有效，下载速度也明显加快。JPEG 图片质量可能会在某些网站上引起问题，但这很少是个大问题。但是，有一些注意事项:

*   视频、Flash 和其他媒体没有经过压缩，所以大量使用 YouTube 的用户不太可能体验到速度的提升。
*   安全的 HTTPS 流量不会通过代理服务器。这确保了在线银行*等系统的安全性不会受到损害(并且防止验证码变得更加不可读！)*然而，在一些 HTTP 网站上，安全性可能是一个问题。
*   Opera Turbo 代理服务器可能会引发隐私问题。该公司对隐私有很强的承诺，但是 Opera T2 可以识别个人身份并收集使用统计数据。
*   服务是否可扩展？它现在工作得很好，但是一旦几百万用户开始使用 Opera 10 会发生什么？

总的来说，Opera Turbo 是一个很棒的特性，我怀疑其他浏览器厂商也会实现类似的系统。至于安全和隐私问题，这并不比使用公共网络更糟糕——公共网络是 Opera Turbo 用户受益最多的地方。

## 该不该转 Opera 10？

该浏览器的测试版有一些奇怪的地方，最好用于测试或出于好奇。然而，Opera 10 非常出色，我建议您尝试最终版本。

我会从默认浏览器切换吗？不——我仍然更喜欢 Firefox，并且已经开始依赖几个插件，如果我全职转向 Opera，我会错过这些插件。我会考虑让它只用于浏览，但是，一旦火狐加载完毕，我不认为我会去打扰它？

Opera 真正大放异彩的领域包括:

1.  附加功能，如电子邮件、新闻组、IRC 等。
2.  它对资源的要求很低，而且
3.  节省带宽的 Opera Turbo 模式。

这些使得浏览器非常适合移动设备。我肯定会把它安装在我的上网本上，在那里它可以取代 Firefox 和 Thunderbird(特别是如果 [Xmarks](http://www.xmarks.com/) 曾经发布过 Opera 书签同步插件)。

另请参见:

*   [Opera 10 测试版下载页面](http://www.opera.com/browser/next/)
*   [Opera 10 的新功能(第 1 部分——界面、特性、标准和工具)](https://www.sitepoint.com/opera-10-new-features/)
*   歌剧能流行起来吗？
*   [为什么 Opera 10 的用户代理气味难闻](https://www.sitepoint.com/opera-10-user-agent/)

你觉得 Opera 10 怎么样？你会使用 Opera Turbo 还是风险太大？

## 分享这篇文章
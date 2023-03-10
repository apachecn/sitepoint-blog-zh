# HTML 5 视频的阴暗面

> 原文：<https://www.sitepoint.com/the-dark-side-of-html-5-video/>

上周， [YouTube 宣布测试版 HTML 5 视频支持](http://youtube-global.blogspot.com/2010/01/introducing-youtube-html5-supported.html):一旦你激活了测试版，你将使用原生浏览器元素而不是 Flash 插件来观看视频。新播放器只能在最新版本的 Safari 或 Chrome(或 IE 中的 Chrome Frame)上运行，因为视频是用 H.264 编解码器编码的，Firefox 不支持这种编码。YouTube 发布声明的第二天， [Vimeo](http://vimeo.com) 为[做了一个类似的](https://vimeo.com/blog:268)。他们现在还通过一个新的 HTML 播放器提供了对 HTML 视频元素的初步支持。

从表面上看，这似乎是“开放”网络的胜利，对吗？一些主要的网站，代表了在线视频的很大一部分，开始从专有技术(Flash)转向开放标准(HTML 5)。但是当你稍微深入一点，就会发现事情并不那么简单。YouTube 和 Vimeo 都选择提供用 H.264 编解码器编码的 HTML 视频，这是受专利保护的。苹果在 H.264 中有很大的股份，所以 Safari 支持它，谷歌也支付了许可费，在 Chrome 中包含了一个 H.264 解码器。

另一方面，Mozilla Firefox 不支持 H.264:它只能播放用 Ogg Theora 编解码器编码的 HTML 视频。这部分是出于意识形态的原因，因为 Theora 编解码器是开源的，因此符合 Mozilla 的原则。但这不仅仅是意识形态。作为对 YouTube 声明的回应，Mozilla 的工程副总裁 Mike Shaver 发表了一篇[博客文章，解释了 Mozilla 坚持使用 Theora](http://shaver.off.net/diary/2010/01/23/html5-video-and-codecs/) 的原因。他指出，H.264 的许可费不仅是 Mozilla 决定不支持这种格式的理由，也是一个更可怕的威胁:“[……]如果 H.264 成为标准化网络的一个公认部分，这些费用将成为新浏览器开发者、将网络引入新设备或平台的开发者以及开发工具来帮助内容和应用程序开发的开发者的进入壁垒。”Mozilla 的开源倡导者 Christopher Blizzard 也对这个话题有很多话要对[说，他把这种情况比作几年前 GIF 格式(以及，在较小程度上，MP3)的情况。](http://www.0xdeadbeef.com/weblog/2010/01/html5-video-and-h-264-what-history-tells-us-and-why-were-standing-with-the-web/ "HTML5 video and H.264 – what history tells us and why we’re standing with the web")

重要的是要记住，目前浏览器对 web 标准的支持水平很大程度上来自 Firefox 与其他浏览器在公平竞争环境中的竞争能力，以及 Mozilla 团队对开放标准的专注。当像 YouTube 这样的大网站开始将专有格式定位为 HTML 视频的事实上的标准时，他们极大地阻碍了像 Firefox 这样的免费语音浏览器在功能上与竞争对手竞争的能力，这从整体上损害了网络的互操作性和创新。与此同时，尽管 Chrome 和 Safari 可能是优秀的浏览器，尽管它们对基于现代标准的 HTML 和 CSS 的支持应该受到欢迎，但在这方面，它们对专有视频格式的选择更像是 90 年代中期左右的 IE 浏览器

YouTube 和 Vimeo 将新的 HTML 5 视频支持宣传为开放标准的胜利，这一事实至少具有误导性。这确实会导致混乱:正如 Christopher Blizzard 所指出的，[Twitter 上有不少人](http://search.twitter.com/search?q=html5+firefox+doesn%27t)似乎认为 Firefox 不支持 YouTube 的 HTML 5 视频应该被理解为 Firefox 不支持 HTML 5！

YouTube 表示，它正在推出新功能，以回应一项用户调查，其中“支持开放格式的 HTML5 开放网络视频”是最受欢迎的功能。看起来 YouTube 可能只关注了前半句话:HTML 5 视频，是的；开放格式，呃，没那么多。

你觉得怎么样？YouTube 和其他类似网站的工作是引领以开放格式提供视频吗？还是 Chrome 和 Safari 应该率先支持这些格式？还是 Mozilla 是无可救药的理想主义者？

## 分享这篇文章
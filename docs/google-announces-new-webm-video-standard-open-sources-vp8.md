# 谷歌宣布新的 WebM 视频标准，开源 VP8

> 原文：<https://www.sitepoint.com/google-announces-new-webm-video-standard-open-sources-vp8/>

昨天，谷歌宣布了 WebM，这是一个新的网络媒体项目，将 VP8 编解码器(谷歌在收购 On2 时收购了它)与 Vorbis 音频编解码器和 Matroska 多媒体容器的部分内容结合在一起。作为这个项目的一部分，谷歌开源了 VP8 编解码器，这可能会对 HTML5 视频的未来产生深远的影响。

如果你对 HTML 视频游戏不太熟悉，下面是详细说明:即将到来的 HTML5 规范包括一个`<video>`标签，它旨在提供网页中的集成视频播放，而不需要第三方插件(浏览器将提供必要的视频解码和播放控制)。然而，HTML5 规范并没有指定用于视频内容的特定编解码器。目前，浏览器支持的状态是多种多样的:谷歌 Chrome 和 Safari 都支持专有的 H.264 编解码器，两种浏览器的母公司都需要支付许可费来包含该编解码器。Chrome、Opera 和 Firefox 都支持 Ogg Theora 编解码器，这是一个免费的开源替代品，许多人认为它的质量不够好。IE 目前两者都不支持，但已经确认即将发布的第 9 版将支持 H.264。

直到最近，这还是一个僵局:苹果和微软不会使用 Theora，因为他们说它缺乏 H.264 的性能，Opera 和 Mozilla 拒绝使用 H.264，因为它受专利保护，他们想避免支付许可费。

输入谷歌。2009 年 8 月，谷歌收购了 On2 公司，该公司拥有即将推出的 VP8 编解码器，据传其性能可与 H.264 媲美。当时有传言称，谷歌计划开源该编解码器，从而有可能一劳永逸地结束“视频战争”。这正是谷歌所做的。

VP8 编解码器的开源是谷歌推出新的开放网络媒体项目 WebM 的一部分。细节可以在[新项目的第一篇博客文章](http://webmproject.blogspot.com/2010/05/introducing-webm-open-web-media-project.html)中找到。

新的 WebM 项目已经得到了来自 [Mozilla](https://hacks.mozilla.org/2010/05/firefox-youtube-and-webm/) (他宣布将在 Firefox 4 中与 Theora 一起支持 HTML5 视频) [Opera](http://labs.opera.com/news/2010/05/19/) (他已经发布了支持 WebM 视频的浏览器“实验室”版本)和 [Adobe](http://blogs.adobe.com/flashplatform/2010/05/adobe_support_for_vp8.html) (他已经确认 VP8 编解码器将包含在即将发布的 Flash player 中)。微软采取了一种更微妙的方法，声明说，[“在其 HTML5 支持中，当用户在 Windows 上安装了 VP8 编解码器时，IE9 将支持 H.264 视频以及 VP8 视频的播放。”](http://blogs.msdn.com/ie/archive/2010/05/19/another-follow-up-on-html5-video-in-ie9.aspx)

当然，不言而喻，谷歌 Chrome 将在不久的将来支持新格式。Chromium 博客上的一篇[帖子称，浏览器的开发版本已经提供了对 WebM 的初步支持。](http://blog.chromium.org/2010/05/webm-and-vp8-land-in-chromium.html)

当然，这份名单上还缺少一个重要的玩家:苹果。 [*《卫报》*科技博客报道了](http://www.guardian.co.uk/technology/blog/2010/may/20/apple-steve-jobs-vp8-patent)史蒂夫·乔布斯回复一位读者的电子邮件，询问苹果对新视频编解码器的立场。乔布斯只是链接到 X.264 开发者论坛上的一个论坛帖子，指出编解码器作为一种规范的当前技术弱点。当然，目前的 WebM 被贴上了“开发者预览版”的标签，所以当然这些问题可能会在正式发布之前得到解决。

此外，如果谷歌继续将现有的 YouTube 剪辑库转换为 VP8，并在 HTML5 中提供这些剪辑，苹果的地位可能会被证明是站不住脚的，VP8 支持可能会进入 Safari。事实上，谷歌已经在 [WebM FAQ](http://www.webmproject.org/about/faq/) 上声明，“作为其 HTML5 实验的一部分，所有在 5 月 19 日之后上传到 YouTube 的 720p 或更大的视频都将在 WebM 中编码。”这似乎意味着，如果你正在使用 Firefox 或 Opera 的夜间版本查看实验性的 HTML5 YouTube 页面，你已经可以在野外查看 WebM 编码的视频了。

这无疑是一个令人兴奋的发展，它似乎让网络开放视频的梦想更接近现实。你对网络视频的未来有什么看法？

## 分享这篇文章
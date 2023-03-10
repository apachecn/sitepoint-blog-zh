# Chrome 不再支持 H.264 视频

> 原文：<https://www.sitepoint.com/chrome-drops-support-for-h-264-video/>

今天带来了 HTML5 视频世界的另一个大发展，我已经[在](https://www.sitepoint.com/the-dark-side-of-html-5-video/) [报道](https://www.sitepoint.com/google-announces-new-webm-video-standard-open-sources-vp8/)有一段时间了。通过 Chromium 博客，谷歌[宣布](http://blog.chromium.org/2011/01/html-video-codec-support-in-chrome.html)将放弃 Chrome 浏览器对 H.264 视频编解码器的支持，转而专注于谷歌的开源 WebM 格式，以及更老的 Ogg Theora 编解码器。这使得 Chrome 在编解码器支持方面与 Firefox 和 Opera 保持一致。

谷歌说:

> 尽管 H.264 在视频中发挥着重要作用，但由于我们的目标是实现开放式创新，因此将取消对编解码器的支持，我们的资源将转向完全开放的编解码器技术。

公告帖子上的评论总体基调似乎是负面的，这令人惊讶，至少对我来说是这样。我不禁认为这是一件好事，因为一段时间以来我一直是 HTML5 视频完全开放解决方案的支持者。当然，如果编解码器单独安装在 Windows 中，IE9 只支持 WebM，而苹果的 Safari 仍然是唯一一个只支持 H.264 的浏览器，我们可能还需要很长时间才能以单一格式提供 HTML5 视频，并让它在不同浏览器之间无缝工作。

但是即使是 Adobe 也承诺在 Flash 播放器中支持 VP8 视频(WebM 格式的视频编解码器)。Flash player 中的 WebM 支持将*几乎*把我们带到这样一个点，我们可以对我们的视频进行一次编码(在 WebM 中)，并用 Flash 包装它，作为不支持编解码器的浏览器(Safari 和 IE)的后备。然而，仍然会有一群挑剔的用户，他们的浏览器既不支持*WebM 也不支持 Flash: iOS。*

所以，这看起来又是苹果的举动。

你有什么想法？对于 Chrome 和整个网络来说，这是朝着正确方向迈出的一步吗？你认为苹果最终会屈服吗？

## 分享这篇文章
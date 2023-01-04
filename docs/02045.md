# 网络音频 API:给你的网页添加带宽友好的声音

> 原文：<https://www.sitepoint.com/web-audio-api-add-sound-to-web-page/>

这篇文章由[马克·布朗](https://www.sitepoint.com/author/mbrown)和[乔希·韦德金德](https://github.com/halfnibble)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

![Man in front of fireplace listening to sounds from a gramophone](img/2030c43294ffc5509ec4963012be3fa0.png)

[Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) 允许开发者使用 JavaScript 在浏览器中利用强大的音频处理技术，而不需要插件。除了实时定义和处理基于文件的音频源，它还可以基于各种波形合成声音；这对于经常通过低带宽网络使用的 web 应用程序非常有用。

在本教程中，我将通过展示一些更有用的方法向您介绍 Web 音频 API。我将演示如何使用它来加载和播放 mp3 文件，以及向用户界面添加通知声音( [demo](#demo) )。

如果你喜欢这篇文章，并想更深入地探讨这个话题，我正在为 SitePoint Premium 制作一个名为 [*的 5 集截屏系列，你还没有听到任何消息！*](https://www.sitepoint.com/premium/screencasts/an-introduction-to-the-web-audio-api)

## 我可以用网络音频 API 做什么？

API 在生产中的使用案例多种多样，但一些最常见的案例包括:

*   实时音频处理，例如为用户的声音添加混响
*   为游戏生成声音效果
*   向用户界面添加通知声音

在本文中，我们将最终编写一些代码来实现第三个用例。

## 浏览器支持的好吗？

Chrome、Edge、Firefox、Opera 和 Safari 支持网络音频。也就是说，在写这篇文章的时候，Safari 认为这个浏览器功能是实验性的，需要一个`webkit`前缀。

我可以使用 audio-api 吗？caniuse.com 主要浏览器对 audio-api 特性的支持数据。
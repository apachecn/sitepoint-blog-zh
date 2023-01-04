# 什么东西看起来像 IE11 但是闻起来像火狐？

> 原文：<https://www.sitepoint.com/ie11-smells-like-firefox/>

几天前，微软 Windows 8 的继任者 Windows Blue 的预发布版本被泄露。这可能是有意或无意的，但操作系统是通过网络[安装的](http://www.neowin.net/news/windows-blue-build-9364-leaked-to-the-internet)，[剖析的](http://www.youtube.com/watch?v=KAxXX0m-P_0)，[报告的](http://news.cnet.com/8301-10805_3-57576008-75/windows-blue-leak-shows-changes-large-and-small/)。

新操作系统包含早期版本的 Internet Explorer 11。非常早期的版本。可能更接近 IE10.01。没有什么新技术可以报道，但是一个更新在 [SlashDot](http://news.slashdot.org/story/13/03/25/0215212/testers-say-ie-11-can-impersonate-firefox-via-user-agent-string) 和[Neowin.net](http://www.neowin.net/news/ie11-to-appear-as-firefox-to-avoid-legacy-ie-css)上引起了一场争议风暴:IE11 的用户代理字符串似乎模仿了 Firefox…

**Mozilla/5.0(即 11.0；Windows NT 6.3 三叉戟/7.0；. NET4.0E. NET4.0Crv:11.0)像壁虎**

“MSIE”字符串消失了，出现了一个可疑的“like Gecko”语句。提示 1000 个阴谋论。

从历史上看，自从 web 出现以来，浏览器供应商和开发人员就一直在跳用户代理舞。当您试图创建一个跨浏览器兼容的网站时，问题就出现了:

1.  开发人员在浏览器 X 中创建一个网站，然后在浏览器 y 中测试它。
2.  浏览器 Y 失败，于是开发者编写浏览器 Y 检测例程，服务不同的代码。大家都很开心。
3.  浏览器 Y 随后被更新——但是网站现在失败了，因为它提供遗留代码。
4.  供应商更改用户代理字符串:检测失败，网站再次工作。每个人都很高兴，我们回到第一步。

看 IE11 的用户代理字符串开头: *Mozilla/5.0* 。IE 的早期版本不得不伪装成 Netscape，因为它是占主导地位的浏览器，许多网站拒绝将内容返回给其他任何网站。这种情况一直持续到今天——比如 Chrome、Safari 和 Firefox 都是从“Mozilla/5.0”开始的……

IE10:
**Mozilla/5.0(兼容；MSIE 10.0Windows NT 6.2WOW64【T2 三叉戟/6.0】**

chrome 25:
**Mozilla/5.0(Windows NT 6.2；WOW64) AppleWebKit/537.22 (KHTML，像壁虎一样)Chrome/25 . 0 . 1364 . 172 Safari/537.22**

iPad Safari 6:
**Mozilla/5.0(iPad；CPU OS 6_0 像 Mac OS X)apple WebKit/536.26(KHTML 像壁虎)版本/6.0 Mobile/10a 5355d Safari/8536.25**

火狐 19:
**Mozilla/5.0(Windows NT 6.2；WOW64rv:19.0)壁虎/20100101 火狐/19.0**

你还会注意到 Chrome 和 Safari 都使用了和 IE11 一样的“like Gecko”字符串。我不记得那个爆炸性新闻上过头条？

IE11 最大的变化是去掉了‘MSIE’。我怀疑这样做的原因只有一个:防止网站向现代版本的浏览器提供无效的 IE6/7/8 代码。IE 的 Trident 引擎的最新版本比其原有版本更接近 Gecko 或 WebKit。通过删除“MSIE”字符串，微软正在破解检测代码，并使许多网站正常工作。

重点是:*这些都不重要*。如果你在嗅浏览器，[你几乎肯定做错了](https://www.sitepoint.com/why-browser-sniffing-stinks/)！虽然这似乎是一个显而易见的解决方案，但浏览器嗅探是一种脆弱的技术，它带来的维护问题比它预防的问题更多。如果你知道有人会嗤之以鼻，请建议他们关闭 IDE，远离浏览器，开始另一项事业。

## 分享这篇文章
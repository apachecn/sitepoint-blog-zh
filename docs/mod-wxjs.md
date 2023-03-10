# mod_wxJS

> 原文：<https://www.sitepoint.com/mod-wxjs/>

我正在准备 PHP 和 Javascript 的对决，在得到消息后，我碰巧又看了一眼 T2 wxJS T3，这里是 T4 T5。

经过四年多的工作(Sourceforge 注册日期)，该项目现在处于 0.8.4 。而且不仅仅是做 [GUI](http://users.skynet.be/saw/wxJS/gui/index.html) 部分[wxWidgets](http://www.wxwidgets.org)(c++)；它也在做 [DBs](http://users.skynet.be/saw/wxJS/db/index.html) 和[文件和 zip](http://users.skynet.be/saw/wxJS/io/index.html)。wxWidgets 不仅仅意味着 GUI APIs 还有其他可用的东西[如](http://www.wxwindows.org/manuals/2.6.3/wx_librarieslist.html#librarieslist)[网络](http://www.wxwindows.org/manuals/2.6.3/wx_wxhttp.html#wxhttp)、[配置文件管理器](http://www.wxwindows.org/manuals/2.6.3/wx_wxconfigbase.html#wxconfigbase)和[日志](http://www.wxwindows.org/manuals/2.6.3/wx_wxlogoverview.html#wxlogoverview)——所有这些都可以帮助你用 C++编写跨平台的 GUI。

wxJS 还提供了 [XML](http://users.skynet.be/saw/wxJS/xml/index.html) 支持(OK Expat 不是 libxml2，但无论如何都是)并且 [Spidermonkey 的](http://www.mozilla.org/js/spidermonkey/) [E4X](http://en.wikipedia.org/wiki/E4X) 支持显然是启用的。

但是还有更多: [mod_wxJS](http://users.skynet.be/saw/wxJS/mod_wxjs/index.html) 是一个与 mod_php 一脉相承的 Apache 模块(实际上看起来更像 mod _ perl 看起来像是用 Apache 启动的应用程序)。当然，[服务器端 Javascript](http://en.wikipedia.org/wiki/Server-side_JavaScript) 有几个选择，但我敢说唯一真正的替代竞争者是 [haXe](http://haxe.org/) ，它让我觉得更像 Actionscript 而不是 Javascript。同时，从对这个的回复来看，对服务器端 Javascript 有需求，特别是如果它是你最喜欢的浏览器中可用的同一个。

不是说我还没有尝试过 wxJS，而是令人着迷的东西；)目前 Windows 只有二进制文件，但看起来可以在 Linux 上构建。

(愚蠢警告)所以忽略一两个次要的技术细节，这里有 Javascript " [all ur base](http://steve-yegge.blogspot.com/2006/09/bloggers-block-3-dreaming-in-browser.html) "的潜力——想象一下服务器端 AJAX！[你桌面 GUI 的原型](http://prototype.conio.net/)！Javascript 无处不在！微软走在了他们时代的前面。

## 分享这篇文章
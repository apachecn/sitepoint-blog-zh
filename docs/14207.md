# 使用谷歌地图的乐趣

> 原文：<https://www.sitepoint.com/fun-with-google-maps/>

几周前推出的谷歌地图是动态网络应用的又一次巨大胜利。如果你还没看过，那就去看看吧(目前只有 IE/Windows 或 Firefox/Mozilla，但 Safari 支持“即将推出”)。这是一件令人印象深刻的作品，主要的“惊喜”因素来自动态平移-只需拖放即可在地图上移动，新的地图切片在后台无缝加载。

[其他功能](http://google.blogspace.com/archives/001554)包括智能路线绘制(例如搜索[华盛顿特区到芝加哥](http://maps.google.com/maps?q=washington+dc+to+chicago))，键盘导航和来自[谷歌本地](http://local.google.com/)的综合搜索结果。它已经让一些知名的地图网站望尘莫及，唯一真正的弱点是完全没有覆盖北美以外的地区。

但是它是如何工作的呢？看看引擎盖下，你会发现一个令人惊讶的技术组合。使用 XMLHttpRequest 和不可见的 iframe 可以实现远程脚本(与服务器通信，无需重新加载页面),使用 iframe 是因为与 XMLHttpRequest 不同，iframe 与用户的 back 按钮很好地集成在一起。再加上 XSLT 和 VML(微软的 SVG 替代品，在 SVG 规范标准化之前推出)，你就拥有了我在公共互联网上见过的最先进的 web 应用程序之一。

jgwebber 对客户端进行了很好的剖析，T2 在我的另一个博客上讨论了一些内部工作。

弄清楚它是如何工作的只是它的一半；为服务收集的[黑客越来越多。乔恩·尤德尔](http://69.90.152.144/collab/GoogleMapsHacking)[指出](http://weblog.infoworld.com/udell/2005/02/09.html)一个隐藏的底层数据的 XML 接口，现在已经停止使用。更好的是，libgmail 项目已经发布了一些[的书签](http://libgmail.sourceforge.net/googlemaps.html)，它们可以直接操作谷歌地图，包括一些可以动画显示该服务建议的路线的书签(见 [Jon Udell 的截屏](http://weblog.infoworld.com/udell/gems/googleMaps.html))。使用它们非常有趣，并且揭示了关于 JavaScript API 的更多细节。谷歌地图磁贴抓取器[也很有趣。](http://brainoff.com/geocoder/gmaps.html)

这些都是令人兴奋的东西，但我不禁感到轻微的担忧。尽管有各种各样的功能，谷歌地图和 GMail 的相似之处在于，如果没有一个现代化的、支持 JavaScript 的桌面浏览器，它仍然是完全不可访问的。正如 [map.search.ch](http://map.search.ch) 聪明地[展示的](http://simon.incutio.com/archive/2005/01/05/swissMaps)一样，构建这样优雅降级的应用程序可能更难，但肯定不是不可能。我希望在令人兴奋的新一轮淘金热中，不要忘记在可访问性领域(对用户和平台而言)的最新进展。

## 分享这篇文章
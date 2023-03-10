# 让你的网站移动友好的 7 个技巧

> 原文：<https://www.sitepoint.com/7-tips-to-make-your-web-site-mobile-friendly/>

*这篇客座博文由 Igor Faletski 撰写， [Mobify](http://mobify.me) 的联合创始人，Mobify 是一家为移动网络优化网站的流行服务公司。SitePoint 在自己的移动网站上使用 Mobify 在[https://www.sitepoint.com/](https://www.sitepoint.com/)查看。*

## 1.手机风格

恭喜你——你已经决定拥抱移动，这是网络上增长最快的领域之一。您首先会注意到的是移动生态系统的多样性。浏览器、屏幕尺寸、连接速度——一切都不一样。网络社区享受了 iPhone 统治的短暂时期，当时解决一个浏览器/屏幕分辨率似乎就足够了。但是现在，Android 设备(有各种屏幕尺寸)正在迅速普及，再次考虑更大的画面是很重要的。

当设计你的移动网站时，**坚持使用相对单位**——百分比，ems 等等。没有办法预测您的用户今天在使用什么移动设备，或者明天可能会使用什么移动设备。相对单位可以走很长的路，当谈到一个持久的，高质量的移动设计。同样重要的是**保持与桌面网站**的视觉一致性——这将使你的品牌更强大，并让你的移动访问者放心，他们会得到很好的照顾。你还需要**最小化滚动和页面转换**来减少寻找内容的时间。

[![Snook.ca mobile site](img/4f507c6634e8037e7f077bf0645f749f.png)](http://mobify.me/gallery/snook/)

## 2.调整图像大小

小屏幕需要小图像。没有理由将桌面大小、笨重的图像资产推给不需要它们的移动用户。一个好的策略是在网络服务器上调整图片的大小——这样可以减少 90%的加载时间。基于 CSS 的大小调整也可以工作，尽管它会带来性能和带宽的损失。

如果可以的话，让你的优化图片链接到其他地方的完整的原始图片。如果你的移动访客想要放大以获得更多细节，他们会非常感激。

[![Matt Bango mobile site](img/669447b28c2826ab260612bd7cb2d3f6.png)](http://mobify.me/gallery/mattbangophotography/)

## 3.自动检测移动设备

即使在最好的移动设备上，打字也是一件痛苦的事情，许多用户在浏览时都会避免打字。再加上这样一个事实，那就是告诉你的用户你实际上提供了一个单独的移动版本的网站，让移动用户访问你的网站的移动版本实际上是相当困难的。

令人欣慰的是，最近出现了多种自动检测移动设备的方法。根据我们的经验，最可靠的方法是用户代理检测，服务器根据接收到的浏览器对其功能的描述来决定提供什么服务。其他实现的特点是对数据库的实时 JavaScript 请求，如 [DeviceAtlas](http://deviceatlas.com/) 或 [WURFL](http://wurfl.sourceforge.net/) ，这些数据库也提供设备可以做什么的信息。只有为每个移动用户自动触发，移动版本才是完整的！

[![Minlo mobile site](img/0ae8716b378f002e934735c89f614319.png)](http://mobify.me/gallery/minlotec/)

## 4.重新思考导航

你的网站可能有几十个页脚、页眉和侧边栏链接——但是它们对你的移动访问者重要吗？移动网上冲浪以其高跳出率而闻名(用户只看一个页面)，所以把注意力集中在网站的几个关键区域，承认你的访问者注意力持续时间短。不要犹豫是否要删除移动优先级不高的目的地的链接。

同时，考虑添加或优先考虑特定于移动设备的项目。隐藏在整个网站页脚的小小的“联系”链接？对于手机版来说，这可能是一个很好的顶行标题，因为许多用户会来寻找电话号码或你的办公室地址。手机标题中另一个很棒的项目是搜索。

[![metrolyrics mobile site](img/174fb74ca40ad21abdbfdd13be0f51ee.png)](http://mobify.me/gallery/metrolyrics/)

## 5.提供完整站点的清晰路径

作为一名网页设计师，你有责任为移动访问者做出内容决策。然而，有时用户正在寻找的一条信息不包括在移动版本中。一个非常简单的方法是在每一页上放一个页脚链接到你的“完整站点”。确保这个链接将用户带到他们当前正在浏览的页面的完整版本。

一旦向完整站点的转换完成，为该用户禁用移动重定向被认为是一种良好的做法。一些网站永久地这样做(例如 [Digg Mobile](http://m.digg.com) )，而其他网站( [CNN](http://m.cnn.com) )用特殊的 cookie 设置 1-2 小时的超时期限。你的电话！

## 6.不要断开链接

在移动网络的早期，常见的是独立的移动网站，它们的结构与传统的桌面网站完全不同。如今，移动流量在很大程度上是由谷歌搜索、Twitter 信息流中的链接和电子邮件驱动的。大多数链接指向桌面网站，这就是自动设备检测如此重要的原因。

然而，在移动用户被检测到并被发送到`m.yoursite.com`之后，向他们展示他们期望的**确切的**内容是很重要的。例如，如果一个`www.yoursite.com/category/article/`是原始链接，向访问者呈现移动优化的`m.yoursite.com/category/article/`，而不仅仅是`m.yoursite.com`主页。这听起来很容易，但是相当多的网络出版商没有这样做，最终让他们的观众感到惊讶和沮丧。

## 7.衡量移动受众

移动设备正迅速成为网络不可或缺的一部分，而不是一个孤立的利基市场。大量的移动分析和广告服务来来去去，但谷歌最近对 AdMob 的收购和 T2 的移动分析 API 将进一步帮助整合移动服务。

衡量移动受众很重要，原因有几个。首先，通过了解哪些设备受用户欢迎，就有可能做出**明智的设计决策**。例如，如果你的移动受众 99%是 iPhone，你就不应该太担心黑莓的兼容性。另一个方面是流量和线索生成。在查看移动统计数据后，你可能会发现**移动和桌面用户更喜欢不同的内容**。了解了所有这些，就有可能创造出杰出的移动业务并成功地将其货币化。

感谢您阅读这些移动提示，祝您的移动网站好运！

## 分享这篇文章
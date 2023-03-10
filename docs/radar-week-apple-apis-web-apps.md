# 本周我们关注的是:苹果、API 和网络应用

> 原文：<https://www.sitepoint.com/radar-week-apple-apis-web-apps/>

欢迎来到《在我们的雷达上》,这是一个来自 web 开发世界的新闻、趋势和其他酷东西的每周综述。

本周的大新闻是微软新操作系统的发布——巧妙命名为 [Windows 10](http://www.theverge.com/2014/9/30/6868695/microsoft-windows-10-announced-official) 。虽然这让人们问“[Windows 9](http://www.extremetech.com/computing/191279-why-is-it-called-windows-10-not-windows-9)怎么了”，但最初的反应是积极的，Windows 10 标志着[开始菜单](http://www.bbc.com/news/technology-29431412)的回归，以及对命令提示符的一些[受欢迎的改进。](http://www.winbeta.org/news/command-prompt-gets-welcome-improvements-windows-10)

与此同时，苹果公司遭受了一点打击，据透露，它在一项避税调查中面临数十亿欧元的费用。尽管 T4 公司拒绝承认其产品存在任何设计缺陷，但 T2 的 Bendgate 公司仍然牢牢占据着新闻头条。

安全研究人员在 bash shell 中发现了一个存在了 22 年的漏洞(随后打了补丁)。这里是如何[检测你是否易受这个恰当命名的 Shellshock Bug 的攻击](http://readwrite.com/2014/09/25/unix-bash-bug-shellshock-find-patch)，这里是[你可能想知道的关于它的一切](http://www.zdnet.com/the-shellshock-faq-heres-what-you-need-to-know-7000034219/)。

## ECMAScript 6

随着 2015 年 3 月的发布，JavaScript 的下一个版本越来越近了。harmony(ES6 的代号)已经冻结了特性，足够稳定，开发者可以开始了解它将带来的新方法和功能。

在这种情况下，SitePoint 作者奥雷利奥·德·罗萨已经开始撰写一系列文章，关注 ES6 的[新数组方法](hhttps://www.sitepoint.com/es6-array-methods-array-prototype/)、[新字符串方法](https://www.sitepoint.com/es6-string-methods-string-prototype/)，以及 [map 和 weakmap](https://www.sitepoint.com/preparing-ecmascript-6-map-weakmap/) (这些都是新的数据类型)。

这就是你如何使用`Object.is`在 ES6 中[实现更严格的平等，下面是 Axel Rauschmayer 博士关于](http://us6.campaign-archive1.com/?u=2cc20705b76fa66ab84a6634f&id=a47e584d69) [ES6 承诺和 JavaScript 异步编程](http://www.2ality.com/2014/09/es6-promises-foundations.html)的基础。

经常可以看到 Rauschmayer 博士在阐述 ES6 的优点。这是他最近一次演讲的视频，题目是“[ECMAScript 6:JavaScript 的下一步是什么？](http://www.youtube.com/watch?v=G21rdWfa_as)

最后，看看这篇颇有深度的文章，充满了 ES6 的优点，它研究了将 JavaScript 生态系统整合到 Rails 中的最佳方式。

## 移动网络

![Ionic Logo](img/af688cdfc38a92863ba2ded61b0c64ab.png)

移动网络代表了互联网有史以来最大的转变。但是作为一名开发人员，你应该制作 web 应用程序(为移动浏览而优化的网站)还是原生应用程序(用原生代码编写的软件应用程序)？

许多人认为网络应用是未来的发展方向，而其他许多人认为,[本地化是唯一的出路。当然，每种方法都有利弊。](http://yalantis.com/blog/native-vs-cross-platform-app-development-shouldnt-work-cross-platform/)

还有第三种方式——混合应用开发(用本地代码编写的应用，指向 web 应用内的页面)。

这里有一篇关于如何构建一个混合应用程序的文章，重点是一些可用的开发框架。

Ionic 是这些框架中比较流行的一个。爱奥尼亚的创造者[最近出现在 JavaScript Jabber 播客](http://javascriptjabber.com/126-jsj-the-ionic-framework-with-max-lynch-and-tyler-renelle/)上，解释爱奥尼亚是什么以及它解决什么问题。

本教程将带你完成[安装 Ionic 和创建你的第一个混合应用](http://developer.telerik.com/featured/supercharge-app-development-ionic-appbuilder/)，同时本教程解释了[如何使用 Ionic Box](https://www.sitepoint.com/ionic-box-vagrant-configuration-hybrid-mobile-apps/)——一个用 Ionic 创建混合移动应用的流浪配置。

无论你如何选择，这里都有一个构建移动应用的简单清单。

## 本周流行语:API

API(应用程序编程接口)是控制一个应用程序如何与另一个应用程序对话的一组要求。API 通过公开程序的一些内部函数来促进共享数据的共享。软件公司通常会向公众发布其 API，这样开发者就可以设计出基于其服务的产品(例如谷歌地图)。

继续关注谷歌，这就是如何[使用谷歌分析 API 通过 PHP 与谷歌分析数据](https://www.sitepoint.com/using-google-analytics-api-php-logging/)交互。或者，如果这不是你的事情，为什么不学习如何[用谷歌 API 和 Twilio SMS](https://www.twilio.com/blog/2014/09/gmail-api-oauth-rails.html) 授权你的 Rails 应用呢？

API 无处不在。有一种服务可以把你的电子表格转换成文档化的 API。此外， [MySQL 5.7 的原型与 REST API](http://www.infoq.com/news/2014/09/MySQL-REST) 一起发布，这有可能消除对中间层服务器或特定于数据库的驱动程序的需求。

不确定什么是 REST API？下面是如何[使用 Node 构建一个。JS 和 Express](http://www.codeproject.com/Articles/819070/How-to-build-a-scalable-REST-API-using-Node-JS-and) 。

WordPress 现在附带了一个 HTTP API。下面深入了解一下[是什么以及如何使用](https://www.sitepoint.com/the-wordpress-http-api/)。

最后，别忘了安全。在最近的 TestTalks 播客中，安全专家 Troy Hunt 讨论了 API 安全测试。你知道，在被黑之前先黑了自己，诸如此类的…

## 响应式网页设计

![responsive-web-designing-](img/a0c5d43965f94b86d1d757bb45374ac7.png)

本周还看到了一些关于 RWD 各个方面的有趣文章。

Chris Coyier 提醒我们，当处理响应式图像时，[我们应该使用`srcset`如果我们只是改变分辨率](http://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/)。一个警告:[请不要<图>所有的事情](http://blog.cloudfour.com/dont-use-picture-most-of-the-time/)！

我们一直都知道尺寸*对*很重要，尤其是当涉及到[平衡行长和字体尺寸](http://www.smashingmagazine.com/2014/09/29/balancing-line-length-font-size-responsive-web-design/)的时候。你认为[回复短信只是一种时尚吗？](https://www.sitepoint.com/thought-responsive-text-just-fad/)

了解《洛杉矶时报》如何改善其网站的用户体验，并为广告商提供更好的环境，作为其响应性重新设计的一部分。

就在我们认为自己已经接近最新水平的时候，RWD 的下一件大事来了。

为了使事情圆满，这里有一份为开发者准备的[响应式设计阅读清单](http://demosthenes.info/blog/925/Web-Developer-Reading-List-Responsive-Design)，以及[一份详细介绍 RWD 如何运作的信息图](http://blog.hubspot.com/marketing/responsive-web-design)。

这就是这周的全部内容。感谢加入我们。

我将留给你一个我最近在推特上看到的笑话:

一名测试人员走进一家酒吧。点了杯啤酒。订购 999999999 啤酒。订购一只蜥蜴。订单-1 瓶啤酒。订购 sfdeljknesv。

那么哪些链接引起了你的注意呢？你最喜欢的 API 有哪些？ES6 准备好黄金时间了吗？你是渴望得到一份 Windows 10，还是根本不在乎？不管怎样，我们都想听听你的想法。

此外，如果你在实现这里涉及的任何东西时有任何问题，或者只是想进一步讨论它， [SitePoint 的论坛](https://www.sitepoint.com/forums/)是一个很好的访问地点(你可以用你的 Google、FaceBook、Twitter、GitHub 或 Yahoo 帐户登录)。

## 分享这篇文章
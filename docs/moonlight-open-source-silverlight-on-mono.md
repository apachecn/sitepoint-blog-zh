# 月光 Mono 上的开源 Silverlight

> 原文：<https://www.sitepoint.com/moonlight-open-source-silverlight-on-mono/>

在 SitePoint 社区中，我们有许多开源、跨平台软件的支持者，我认为微软在推出了 [Silverlight](http://silverlight.net/) 和[动态语言运行时](http://blogs.msdn.com/hugunin/archive/2007/04/30/a-dynamic-language-runtime-dlr.aspx) (DLR)之后，让我们中的一些人挠头不知该站在哪里。

微软的 DLR 是他们的[公共语言运行时](http://en.wikipedia.org/wiki/Common_Language_Runtime) (CLR)之上的一层，它为动态类型语言提供支持，如 [Python](http://www.python.org/) 、 [Ruby](http://www.ruby-lang.org/) 和 JavaScript。好消息是，DLR 是在微软的许可许可下发布的，这是他们对开源的说法。微软的。Python 和 Ruby 的. NET/DLR 实现分别命名为 [IronPython](http://www.codeplex.com/Wiki/View.aspx?ProjectName=IronPython) 和 [IronRuby](http://www.iunknown.com/2007/04/introducing_iro.html) ，它们都包含在与 DLR 相同的许可协议中。

微软将 Silverlight 描述为“一个跨浏览器、跨平台的插件，用于交付下一代。基于. NET 的媒体体验和丰富的交互式网络应用。Silverlight 提供了一个灵活的编程模型，支持 AJAX、VB、C#、Python 和 Ruby，并与现有的 Web 应用程序集成。”这听起来很棒——跨平台、跨浏览器——但不幸的是，它缺少了“开源”这个神奇的词。

即便如此，人们很难不对无处不在的平台/插件的前景感到兴奋，这种平台/插件将 Ruby、Python 和 JavaScript 等动态语言整合到了 web 客户端。由于一些重要的组件已经开源，其他人可以更容易地完成剩下的部分…

**输入单声道**

Mono 是一个开源项目，已经存在了一段时间，它提供了开发和运行所需的软件。NET 客户端和服务器应用程序。是跑步常用的解决方案。Unix 服务器上的. NET web 应用程序。

似乎 Mono 已经有了一些很好的计划，并且已经有一些工作在进行中，为 Silverlight 的基于 Mono 的实现，在一个临时命名为“Moonlight”的项目下开发。与 Mono 现有的坚实支持。NET，结合微软的开源 DLR 代码，这听起来像是一个值得关注的项目。

## 分享这篇文章
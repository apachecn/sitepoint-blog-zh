# Web 开发人员常犯的 10 个错误

> 原文：<https://www.sitepoint.com/10-common-mistakes-web-developers-make/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

关于如何完成同样的任务——开发一个能在今天的现代网络中工作的网站，似乎有无穷无尽的选择。Web 开发人员必须选择 web 托管平台和底层数据存储，使用哪些工具编写 HTML、CSS 和 JavaScript，如何实现设计，以及要包含哪些潜在的 JavaScript 库/框架。一旦选择范围缩小，网上世界就充满了文章、论坛和示例，为创造更好的网络体验提供建议。然而，无论采取哪种方式，所有的开发人员都容易犯错误。尽管有些错误与特定的方法有关，但是所有的 web 开发人员都面临着同样的挑战。因此，通过研究、经验和最近的观察，我想我应该分享我所编制的一份清单，列出我看到的 web 开发人员犯的十个常见错误，以及如何避免它们。

以下列表排名不分先后。

## 1)编写老式 HTML

错误:互联网早期的标记选项比我们今天的少得多。然而,[旧习难改](http://stackoverflow.com/questions/14613030/do-i-need-a-doctype-declaration-in-a-php-file-with-html)，许多人仍然写着他们的 HTML，好像我们还在 20 世纪。这里的例子是使用`<table>`元素进行布局，使用`<span>`或`<div>`元素，而其他特定于语义的标签可能更合适，或者使用当前 HTML 标准不支持的标签，如``或`<font>`，或者在具有大量`&nbsp;`实体的页面上分隔项目。

**影响**:编写过去十年的 HTML 会导致过于复杂的标记，在多种浏览器中表现不一致。在更多的[现代浏览器如微软 Edge](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx/?WT.mc_id=13417-DEV-sitepoint-article41) 甚至是过去版本的 Internet Explorer (11，10，9)中，这是不必要的。

**如何避免**:停止使用`<table>`元素进行内容布局，并限制其用于显示表格数据。了解当前可用的标记选项，如在[whatwg.org](https://html.spec.whatwg.org/multipage/semantics.html#semantics)看到的。使用 HTML 来描述内容是什么，而不是它将如何显示。为了正确显示你的内容，使用 CSS([https://www.w3.org/Style/CSS/](https://www.w3.org/Style/CSS/))。

## 2)“它能在我的浏览器中工作……”

错误:开发人员可能喜欢某个特定的浏览器，或者真的不喜欢某个浏览器，并且可能主要测试带有偏见的网页。还有一种可能是，在编写在线代码示例时，没有考虑它在其他浏览器中的呈现方式。此外，一些浏览器对样式有不同的默认值。

影响:写一个以浏览器为中心的网站可能会导致在其他浏览器上显示时质量很差。

**如何避免**:在开发过程中测试每个浏览器&版本的网页是不切实际的。然而，定期检查您的站点在多个浏览器中的显示效果是一个好方法。如今，无论你偏好什么平台，都可以获得免费的工具来帮助你:[免费虚拟机](http://dev.modern.ie/tools/vms/?utm_source=SitePoint&utm_medium=article41&utm_campaign=SitePoint)，[站点扫描](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article41&utm_campaign=SitePoint)。像[http://browsershots.org/](http://browsershots.org/)或[https://www.browserstack.com/](https://www.browserstack.com/)这样的网站展示了给定页面在多种浏览器/版本/平台上如何呈现的快照。像 [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx/?WT.mc_id=13417-DEV-sitepoint-article41) 这样的工具也可以调用多个浏览器来显示你正在处理的单个页面。使用 CSS 设计时，考虑“重置”所有默认设置，如[meyerweb.com](http://meyerweb.com/eric/tools/css/reset/)所示。

如果你的网站正在使用任何专门为浏览器创建的 CSS 特性，要小心你将如何处理供应商前缀，如`-webkit-`、`-moz-`或`-ms-`。关于这方面的行业趋势，值得您花时间查看以下参考资料:

*   微软 Edge 开发博客:[告别过去，第 2 部分:告别 ActiveX、VBScript、attachEvent](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13417-DEV-sitepoint-article41)
*   被认为有害的 CSS 厂商前缀
*   布鲁斯·劳森:[在 Internet Explorer 上支持——WebKit——厂商前缀](http://www.brucelawson.co.uk/2014/on-internet-explorer-supporting-webkit-vendor-prefixes/)

虽然上述参考资料解释了远离供应商前缀的趋势及其原因，但本网站提供了如何解决这一问题的实用建议。

## 3)错误的形式

**错误**:提示用户提供任何信息(尤其是当输入到文本字段中时)，并假设数据将按预期接收。

**影响**:当用户输入被信任时，许多事情可能会出错。如果没有提供所需的数据，或者接收的数据与底层数据方案不兼容，页面可能会失败。更严重的是对网站数据库的故意侵犯，可能是通过注射攻击(见[OWASP:2013-A1-Injections 十大事件)。](https://www.owasp.org/index.php/Top_10_2013-A1-Injection)

**如何避免**:这里的第一点建议是确保用户清楚你在寻找什么类型的数据。如今，询问地址可能会得到一个公司、家庭甚至电子邮件地址！除了具体一点，利用今天的 HTML 中可用的数据验证技术，如在[这篇文章](http://devproconnections.com/html5/html5-form-input-enhancements-form-validation-css3-and-javascript)中看到的。无论在浏览器端如何验证数据，都要确保在服务器端验证数据。在未确认每个字段都是其应有的数据类型的情况下，绝不允许串联的 T-SQL 语句使用用户输入的数据。

## 4)膨胀的反应

**错误**:页面充满了许多高质量的图形和/或图片，使用`img`元素的高度和宽度属性缩小。CSS 和 JavaScript 等页面链接的文件很大。源 HTML 标记也可能是不必要的复杂和广泛。

**影响:**让一个页面完全呈现的时间变得足够长，以至于一些用户放弃甚至不耐烦地再次重新请求整个页面。在某些情况下，如果页面处理等待时间太长，就会出现错误。

**如何避免**:不要认为互联网接入速度越来越快，从而允许出现臃肿的情况。相反，把从浏览器到你的网站的所有东西都看作是一种成本。页面膨胀的罪魁祸首是图像。要将降低页面加载速度的图像成本降至最低，请尝试以下三个技巧:

1.  问问你自己:“我所有的图形都是绝对必要的吗？”如果没有，删除不需要的图像。你可以[点击这里](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article41&utm_campaign=SitePoint)浏览你的网站，寻找压缩图片的建议。
2.  使用[自动缩小](http://toki-woki.net/p/Shrink-O-Matic/)或[防暴](http://luci.criosweb.ro/riot/)等工具最小化图像的文件大小。
3.  预加载图像。这不会提高初始下载的成本，但会使网站上使用这些图片的其他页面加载得更快。有关这方面的提示，请参见本文。

另一种降低成本的方法是缩小链接的 CSS 和 JavaScript 文件。有很多工具可以在这方面提供帮助，比如 [Minify CSS](http://www.minifycss.com/) 和 [Minify JS](http://www.minifyjs.com/) 。

在我们结束这个话题之前，努力熟悉 HTML(参见错误 1 ),并在 HTML 中使用<`style>`或< `script>`标签时运用良好的判断力。

## 5)创建**应该**工作的代码

**错误**:无论是服务器上运行的 JavaScript 还是代码，开发人员都已经测试并确认它可以工作，从而得出结论，一旦部署，它应该仍然可以工作。代码在没有错误捕获的情况下执行，因为它在被开发人员测试时是有效的。

**影响**:没有适当错误检查的网站可能会以一种丑陋的方式向最终用户揭示错误。不仅用户体验会受到显著影响，错误消息内容的类型还会为黑客提供如何渗透网站的线索。

如何避免:人非圣贤，孰能无过，所以将这一理念引入编码。对于 JavaScript，一定要实现好的技术来防止错误以及捕捉错误。虽然本文讨论的是 Windows 应用程序的 JavaScript 编码，但是大多数主题也适用于 web 开发，并且其中充满了好的技巧！另一个帮助创建可靠代码的方法是单元测试(见[这篇维基百科文章](http://en.wikipedia.org/wiki/Unit_testing))。

应该在用户看不到任何讨厌的细节的情况下发现服务器端代码中的错误。只透露必要的内容，并确保为 HTTP 404s 之类的东西设置友好的错误页面(见[这篇维基百科文章](http://en.wikipedia.org/wiki/HTTP_404))。

## 6)编写分叉代码

**错误**:抱着支持所有浏览器和版本的崇高理念，开发人员编写代码来应对每一种可能的场景。代码变成了一堆 if 语句，向各个方向分叉。

**影响**:随着新版本浏览器的更新，代码文件的管理变得庞大而难以管理。正如在#1 中提到的，越来越多的[不必要的](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx/?WT.mc_id=13417-DEV-sitepoint-article41)。

**如何避免** : [在代码中实现特征检测](https://msdn.microsoft.com/en-us/library/hh273396%28v=vs.85%29.aspx/?WT.mc_id=13417-DEV-sitepoint-article41)与浏览器/版本检测。特征检测技术不仅大大减少了代码量，而且更易于阅读和管理。考虑使用像 [Modernizr](http://modernizr.com/) 这样的库，它不仅有助于特征检测，还能自动为那些不能适应 HTML5 或 CSS3 的旧浏览器提供后备支持。

## 7)设计不响应

**错误**:网站开发假设在与开发者/设计者相同大小的显示器上查看。

**影响**:当在移动设备或超大屏幕上浏览网站时，用户体验会受到影响，要么看不到页面的重要方面，要么甚至无法导航到其他页面。

**如何避免**:响应性思考。在你的网站中使用响应式设计([维基百科](http://en.wikipedia.org/wiki/Responsive_web_design))或者([一篇文章](http://alistapart.com/article/responsive-web-design))。这里有一些[实用](https://www.sitepoint.com/common-techniques-in-responsive-web-design/) [教程](https://msdn.microsoft.com/en-us/magazine/hh653584.aspx/?WT.mc_id=13417-DEV-sitepoint-article41)在上面包括[响应式图片](https://css-tricks.com/which-responsive-images-solution-should-you-use/)。一个非常受欢迎的准备在这个领域服务的库是 Bootstrap。

## 8)制作无意义的页面

**错误**:制作面向公众的页面，内容可能非常有用，但不向搜索引擎提供任何提示。未实现辅助功能。

**影响**:网页不容易被搜索引擎发现，因此很少或根本不会被访问。对于视力受损的用户来说，页面内容可能非常晦涩难懂。

**如何避免**:使用 SEO(搜索引擎优化)并在 HTML 中支持可访问性。关于搜索引擎优化，一定要添加标签，用关键词和描述为页面提供意义。在关于技术的的[中有一篇很好的文章。为了获得更好的可访问性体验，一定要在每个`img`或`area`标签中提供一个`alt="your image description"`属性。当然，还有更多的事情要做，更多的建议可以在 About Tech 上调查。你也可以在](http://webdesign.about.com/od/seo/a/keywords-html.htm)[测试一个公共网页，辛西娅说](http://www.cynthiasays.com)看看它是否符合[第 508 节](http://www.section508.gov/)。

## 9)制作过于清新的网站

错误:创建一个需要每次交互都刷新页面的网站。

**影响**:类似于页面膨胀(*见错误#4* )，页面加载时间性能受到影响。用户体验缺乏流动性，每次交互都会导致页面短暂(或长时间)的重置。

**如何避免**:避免这种情况的一个快速方法是确定是否确实需要回发到服务器。例如，当不依赖服务器端资源时，可以使用客户端脚本来提供即时结果。你也可以采用 AJAX 技术([维基百科](https://en.wikipedia.org/wiki/Ajax_(programming)))或者更进一步，使用单页面应用程序“SPA”([维基百科](https://en.wikipedia.org/wiki/Single-page_application))方法。流行的 JavaScript 库/框架可以使这些方法的采用更加容易，例如 [JQuery](http://jquery.com/) 、 [KnockoutJS](http://knockoutjs.com/) 和 [AngularJS](https://angularjs.org/) 。

## 10)工作过度

**错误**:一个开发者花了很长时间创建网页内容。时间可能会花在重复的任务上，或者只是简单地打了很多字。

**影响:**初始网站启动或后续更新时间过长。当其他开发人员用更少的时间和更少的努力做着类似的工作时，这个开发人员的价值就会降低。手工劳动很容易出错，并且排除错误需要更多的时间。

如何避免:探索你的选择。考虑在开发的每个阶段使用新的工具或新的过程技术。例如，您当前的代码编辑器与 [Sublime Text](http://www.sublimetext.com/) 或 [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx/?WT.mc_id=13417-DEV-sitepoint-article41) 相比如何？不管你用的是什么代码编辑器，你最近有没有研究过它的特性？也许你花一点时间仔细阅读文档就可以发现一种新的方法，可以节省几个小时的时间。例如，请注意 Visual Studio 的扩展如何提高 web 开发人员的工作效率，如本文所示。

不要错过网上可用的帮助工具！例如，在 [dev.modern.ie](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article41&utm_campaign=SitePoint) 查看工具，以简化测试(跨多个平台和设备)和故障排除。

您还可以通过自动化流程来帮助减少时间和错误。这方面的一个例子是使用像 [Grunt](http://gruntjs.com/) 这样的工具，它可以自动完成诸如缩小文件之类的事情(*参见错误 4* )。另一个例子是 [Bower](http://bower.io/) ，它可以帮助管理库/框架。

web 服务器本身怎么样？在微软 Azure Web Apps 等服务的帮助下，您可以快速创建一个适用于几乎任何开发场景的网站，并且可以轻松扩展您的业务！

## 摘要

通过识别常见错误，web 开发人员可以消除其他人已经忍受的许多挫折。不仅承认是重要的，而且当我们理解错误的影响并采取措施避免它时，我们可以创建一个迎合我们偏好的开发过程——并且自信地这样做！

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13417-DEV-sitepoint-article41) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13417-DEV-sitepoint-article41)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article41&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article41&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article41&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article41&utm_campaign=SitePoint)
*   GitHub 上的编码实验室:跨浏览器测试和最佳实践

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=13417-DEV-sitepoint-article41)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=13417-DEV-sitepoint-article41)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com) 和 [babylonJS](http://babylonjs.com) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=13417-DEV-sitepoint-article41)(来自律师奶爸和基里尔·赛克谢诺夫，包括[流形。JS](http://manifold.js.com) 项目)

## 为网络平台提供更多免费的跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13417-DEV-sitepoint-article41)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=13417-DEV-sitepoint-article41) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13417-DEV-sitepoint-article41)

## 分享这篇文章
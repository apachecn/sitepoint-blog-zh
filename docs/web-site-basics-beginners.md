# 网站基础:初学者需要知道的东西

> 原文：<https://www.sitepoint.com/web-site-basics-beginners/>

多亏了像微软 FrontPage 和 Adobe Dreamweaver 这样的应用程序,“建立一个网站很容易”的想法自从网络发明以来就一直存在。

然而，许多没有经验的网页设计师很快发现，创建一个向你的金丝雀鲍里斯致敬的网页(愿他的灵魂安息)和建立一个专业的网站是两个非常不同的任务。要建立一个能与受众有效沟通、能被客户更新的网站，*和*看起来赏心悦目需要一系列的技能——其中一些超出了大多数设计师的舒适区，尤其是如果他们有印刷背景的话。

例如，确定什么技术是最合适的，使用什么内容管理系统，在哪里托管网站，如何确保跨浏览器的兼容性以及如何确保网站在搜索引擎中的排名都很重要。对于门外汉来说，这些障碍会很快变得势不可挡。

那么什么有效，什么无效呢？嗯，正如你所料，*这取决于*。与任何项目一样，重要的决策是由项目的目标、预算、时间表、团队成员的经验以及参与者的个人偏好决定的。

虽然许多技术决策通常最好留给那些喋喋不休的极客，但有一些普遍真理适用于所有的 web 项目。至关重要的是，任何处理 web 项目的设计师都要理解这些基本原理，不管他们是削减代码、推动像素还是两者都涉足。

##### 客户端竞赛

许多网页设计师在着手一个项目之前，甚至没有考虑过网页标准三重奏: [HTML](https://www.sitepoint.com/html-css/) 、 [CSS](https://www.sitepoint.com/html-css/css/) 和 [JavaScript](https://www.sitepoint.com/javascript/) ，就已经决定用 Flash(或者微软的富媒体产品 Silverlight)来构建这个项目。作为真正的设计师，他们希望避免浏览器兼容性问题和一系列限制性的用户界面选项限制他们的创造力，所以他们选择了 Flash，相信他们做出了正确的决定。

我们设计师如此热爱 Flash 是可以理解的——为什么不呢？这是一个非常强大的媒体，我们可以用它来创造视觉享受，并推动互动和多媒体的边界。Flash 还有一个巨大的安装基础，所以它很方便地避开了困扰 CSS 网站多年的浏览器兼容性问题。最后，Flash 有一个明确无误的“惊喜”因素，这对于吸引客户非常有用。

不幸的是，Flash 有一些肮脏的小秘密。实际上，它们并不小——在你决定用 Flash 建立你的下一个站点之前，看看下面的缺点:

*   与用 HTML 创建的网站相比，Flash 网站在搜索引擎中表现不佳。虽然谷歌多年来在索引内容方面取得了进步，但谷歌的工程师们仍有很长的路要走，才能让抓取网络的机器人能够理解 Flash 文件，达到可以使用简单的 HTML 标签来定义的程度。
*   与基于 HTML 的网站相比，大多数 Flash 网站不太容易被残障用户访问。当然，每个 Flash 文件都内置了一些基本的键盘导航功能，但在许多浏览器中，在 Flash 对象和它的 HTML 容器之间切换是有问题的，用鼠标或键盘之外的输入设备导航 Flash 对象也是如此。
*   Flash 网站通常不如 HTML/CSS 网站好用，当一个网站建立在 Flash 中时，web 浏览器体验的关键功能就消失了。这些包括突出显示文本以便复制和粘贴，单击后退按钮返回页面，增加字体大小，以及允许用户在新窗口中打开链接。这是不管界面有多漂亮和直观。
*   大多数移动用户无法查看 Flash 站点。即使是 iPhone——苹果在澳大利亚非常成功的产品，也可以说是市场上最先进的手持设备——也不能浏览 Flash 文件。

当然，在很多情况下使用 Flash 确实是有意义的:动画报头、嵌入式电影以及脱离基于文档的 Web 模型的交互界面都是不错的选择。然而，这些情况很少涉及到完全在 Flash 中构建站点。

像[cssZenGarden.com](http://csszengarden.com)这样的网站已经证明，一个网站不需要为了好看而用 Flash 创建。许多网页设计者求助于 Flash，因为这是他们所知道的，结果是客户遭受损失。与此同时，当今的 web 浏览器对 CSS 的支持已经有了很大的改进，现在有大量优秀的 JavaScript 库，这使得编写 JavaScript 比以前简单多了。当优秀的旧 HTML 及其配套技术可以做同样好的工作并避免上面列出的陷阱时，没有理由求助于 Flash。

##### 服务器端摊牌

虽然设计师可能拥有 HTML、CSS 和 JavaScript 方面的一些技能是很常见的，但用服务器端语言编程通常是为这个世界的 it 书呆子保留的(除了那些罕见而令人讨厌的擅长左右脑活动的多面手)。

然而，越来越多的角色要求设计师既熟悉图形又熟悉编写代码——即使只是更新为网站提供动力的内容管理系统的模板。

这里有一个在 Web 上使用的最常见的服务器端语言的概要，以及它们是如何堆叠的。

*   编程语言 Perl 比万维网本身存在的时间还要长。该语言是一个正在进行的开源项目，你使用的任何主机提供商都保证会安装它。然而，除非你有学习 Perl 的好理由，否则我建议避开它——它以难学而闻名。
*   **[PHP:](https://www.sitepoint.com/php/)** 和 Perl 一样，PHP 也是一种开源语言。然而，与 Perl 不同，PHP 以吸引初学者而闻名，主要是因为它非常容易学习，并且在 web 托管提供商中无处不在。然而，PHP 的易用性有时被开发人员视为一种批评，因为它为新来者提供了“足够上吊的绳索”但是，PHP 的普及是不可否认的。它也是许多免费内容管理和博客系统的首选语言(我们将在本文后面探讨)。 [XAMPP 包](http://www.apachefriends.org/en/xampp.html)，一个点击安装 PHP 和其他与 PHP 集成的技术，是一个很棒的包，适用于 Windows、Mac 和 Linux 用户。
*   **[ASP。对于那些不太信任开源技术的客户来说，微软的 ASP.NET 可能是个不错的选择。Visual Studio 开发环境是构建 ASP.NET 站点的自然选择。Visual Studio 极大地简化了构建网站的过程，但这是有代价的——既昂贵又耗费资源(尽管有免费的“速成版”可用)。不幸的是，Visual Studio 生成的代码通常效率低下且不符合标准，一些 web 托管提供商缺乏托管 ASP.NET 页面的能力。](https://www.sitepoint.com/asp-net-getting-started/)**
*   Ruby 语言已经存在十多年了，但它在构建 web 应用程序方面的流行只是最近的事，主要是由一个名为 Ruby on Rails(或简称为 Rails)的 web 应用程序框架推动的。尽管名称有点奇怪，但 Rails 是一个经过深思熟虑的框架，它迫使开发人员在文件结构、文件命名约定、代码重用和单元测试方面遵循最佳实践。为了掌握 Rails，需要一个陡峭的学习曲线，但是那些坚持下来的人通常会成为直言不讳的传道者。
*   **[Flex:](https://www.sitepoint.com/web/flash-and-flex/)** Flex 指的是 Adobe 开发富互联网应用程序的一系列技术，富互联网应用程序是比标准的基于浏览器的 web 应用程序具有更高交互性的在线应用程序。在 Flex 中，用户界面是使用一种称为 MXML 的标记语言定义的，页面上的交互是用 ActionScript 描述的，最终产品作为 Flash 文件运行。Adobe 提供基本的 Flex 软件开发工具包供免费下载，但是如果您计划定期在 Flex 中构建站点，那么为 Flex Builder Pro editor 付费是值得的。

如您所见，web 项目的技术选择非常多。无论你是想弄脏你的手，还是只想了解你办公室地下室里的那些推进器是如何打发时间的，努力去欣赏上面列出的语言之间的根本差异将会使你成为一个更好的网页设计师——即使仅仅是因为你能够更好地与你的技术同事交流。

涉足服务器端 web 开发也可能提高你的客户端编码技能；也许如果更多的 web 开发项目是用软件工程的思维方式，而不是印刷思维方式来进行的，那么就会有更少的坏网站。

##### 内容管理系统:定制还是打包？

在上一节中，我认为设计师应该学习一门服务器端语言。现在我建议你在建立下一个网站时使用另一种语言。我知道这很令人困惑——请忍耐一下！

这就是为什么你应该使用尽可能少的定制代码来构建你的下一个网站:戴上设计师的帽子和程序员的帽子当然会让你在网络世界中获得竞争优势，但现实是大多数网站从根本上来说都是内容网站。对于这些类型的网站，内容管理系统(CMS)就足够了。

建立一个网站最困难的挑战之一是确保客户能够在项目完成很久以后自己更新网站。CMS 是实现这一目标的关键。虽然总会有比其他人学得更快的客户，但一些内容管理系统对于非技术人员来说比其他人更容易学习也是事实。

的确，有些情况下你正在设计的网站可能需要纯粹的定制代码，但是在这些情况下，开发(和设计)更有可能由内部管理，而不是由外部机构管理。如果你正在为你的客户重新发明轮子，你应该使用 CMS。

使用成熟的 CMS 比使用自己的 CMS 有很多优势。其中包括:

*   安全:使用不断发展的 CMS 意味着每次升级都会带来产品安全性的提高。这些更新可能会修复以前未发现的安全缺陷，适应浏览器操作方式的变化，或添加对升级数据库版本的支持。编写可通过 Web 访问的自定义代码，而不投入任何时间来测试和更新这些代码，会给客户网站的安全性带来极大的风险。
*   **熟悉度:**也许没有完美的 CMS，但市场上的 CMS 数量有限。如果碰巧你的客户在你为你的项目选择之前已经使用了相同的 CMS，那么更新他们的网站的学习曲线将会低得多。这对他们来说有很好的商业意义，也让你看起来不错。
*   **维护和支持:**如果为你的客户网站提供动力的代码是完全定制的，那么你的客户将会比你使用一个已建立的 CMS 更依赖你来做任何未来的调整或改变。虽然从表面上看，这似乎是一种锁定回头客的巧妙方法，但这种方法可能会损害客户与供应商的关系——尤其是当你无法及时满足他们的要求时。使用一个有大量开发人员的 CMS 更有意义，这样你的客户就可以在必要的时候找到其他的开发人员。

因此，如果从头开始建立一个网站是不允许的，那么问题就变成了“我应该使用哪个 CMS？”这个问题已经在 web 开发人员中引起了多年的热烈讨论，答案还是“视情况而定”

不幸的是，全面比较每一个可用的 CMS 超出了本文的范围。有大量的因素需要考虑。这里有一个例子(这个列表仅仅是冰山一角):

*   CMS 包含该网站所需的功能吗？
*   CMS 需要与任何遗留系统进行交互吗？
*   与 CMS 相关的开发社区有多大？
*   客户对使用开源解决方案感到满意吗？
*   未来有什么扩展网站的计划？

对于项目预算有限的客户来说，开源 CMS 通常是完美的选择。Drupal、Joomla、eZ Publish、WordPress 等工具都是开源的，用 PHP 编写。每个包都有其优点和缺点，但都值得您考虑。

开始研究开源 CMS 包的一个很好的地方是 opensourcecms.com。该网站提供了超过 100 个开源 CMS 的演示，您无需在自己的本地机器上安装任何东西就可以进行实验。

##### 搜索引擎中的排名

好的，你已经启动了你的网站，每天都在发布有趣而独特的内容。游客应该成群结队地来，对吗？那么，如果人们找不到你的网站，他们怎么知道它的存在呢？

如果你的日志文件告诉你每天只有两个人访问你的网站(你是其中之一)，那么你可能需要为搜索引擎优化你的网站。搜索引擎优化(SEO)是一个总括性的术语，它涵盖了你如何编码你的网站，你发布什么内容，你如何表达这些内容，你使用什么样的 URL 结构，以及你在搜索引擎中对各种短语的排名。

搜索引擎优化的世界继续被网络营销顾问的故事玷污，他们用非常规的技术与谷歌博弈；这些欺骗了谷歌算法，使其网站在结果页面中的位置高于其应有的位置。

然而，事实是，SEO 是任何商业网站的一个重要方面，有许多合法的技术，你可以，也应该利用，以确保人们能够通过搜索引擎找到你的网站。虽然在这些页面中描述如何开发一个全面的 SEO 策略是不可能的，但是如果你遵循我在本文开始时的建议，在适当的时候使用 HTML 而不是 Flash，你会做得很好。

一个常见的误解是，一个网站在搜索引擎中的排名与其 PageRank 相关。PageRank 是谷歌使用的一个商标术语，用来确定一个网站有多少权限。从根本上说，它的工作方式是这样的:一个网站的链接越多，谷歌认为这个网站越重要，因此，它的 PageRank 就越高。

然而实际上，决定一个网站的 PageRank 的算法是一个严格保密的秘密，算法本身也随着网络的发展而不断变化。此外，一个网站的 PageRank 只是影响它在 Google 搜索结果页面中位置的一个因素。最后，重要的是要记住谷歌只是众多搜索引擎中的一个。谷歌可能会主导当前的气候，但这可能会在未来发生变化。只关注谷歌是一种短视的策略。

最后，如果一个网站在谷歌和其他搜索引擎的搜索结果中排名很好，那么这个网站的 PageRank 值(或者使用其他指标的排名)就没有意义了。取得(并保持)成功的关键是使用像 Google Analytics 这样的分析工具持续监控你的网站流量。这些数据可以提供关于访问者如何使用你的网站以及他们键入什么关键词来找到它的洞察力，这对于塑造网站的 SEO 策略是非常宝贵的。

##### 网站是一个软件产品，而不是一张纸

对于那些有兴趣发布照片、与朋友聊天以及向世界展示随机想法的最终用户来说，访问 Web 从未如此容易，但是构建一个专业的网站却一如既往地困难。

因此，网页设计师主动了解“幕后”发生了什么是至关重要的，否则他们美丽的模型可能会因为实现它们的技术而无法发挥全部潜力。通过了解何时选择 Flash 或 HTML，各种服务器端语言的优缺点，如何选择 CMS，以及哪些因素会影响页面在搜索引擎中的排名，设计师将能够为他们的客户提供更明智的服务，以及更成功的网站。

## 分享这篇文章
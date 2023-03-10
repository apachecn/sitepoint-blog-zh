# 为 ColdFusion 辩护

> 原文：<https://www.sitepoint.com/making-case-coldfusion/>

经过十年的网络开发，我几乎接触过每一种语言。我涉足过 ASP，花了相当多的时间在 PHP 上，最近，我进入了 ColdFusion 的世界。

当我第一次接触 ColdFusion 时，我反对它。此时是 5.x 版本，我看不到任何我喜欢的地方。我在东海岸的同事已经使用它很多年了，他是一个经验丰富的 ColdFusion 程序员。我是一名经验丰富的 PHP 程序员，刚刚为我的部门完成了一个主要项目，现在被要求转移到 ColdFusion，以便与全球运营保持一致。我是一个开发 FreeBSD PHP Web 服务器的流氓开发者，我不适合我工作的微软世界。

我的公司派我去东海岸与这位开发人员交谈，了解更多关于 ColdFusion 的信息，以及为什么这对我们来说是一个好的举措。当然，我对此表示怀疑；ColdFusion 在我眼里是富人的语言。服务器价格为 1300 美元或更高，不包括微软视窗系统等的费用。我不知道我的小部门如何能负担得起这一飞跃。我在东海岸跟随这位 ColdFusion 程序员一周，当我离开回家时，我有了一堆 ColdFusion 书籍和对这种语言的新的尊重。

我敢肯定，你们中的许多人在某个时候都不得不寻找一种用于 Web 开发的替代语言。从一门你熟悉、热爱并花了数月或数年时间掌握的语言转向一门似乎完全陌生的语言并不容易。当我被要求从我快乐的 PHP 世界切换到 ColdFusion 的黑暗面时，这就是我所面临的。

仅仅过了两年，我就变成了 100% ColdFusion 商店。当然，我仍然谈论 PHP，我仍然到处推荐它，但是我已经认识到我可以做得更快、更干净，而且——信不信由你——比我用 PHP 更便宜。你现在可能会像我一样持怀疑态度，但我希望在这篇文章结束时，你会开始看到足够的光明，认识到 ColdFusion 毕竟不是黑暗面。

##### 背景

首先，为那些对 ColdFusion 语言一无所知的人提供一些背景知识。

ColdFusion 最初是由 Allaire 兄弟在 1995 年开发的。这开创了 Allaire 公司，后来被 Macromedia 收购。像大多数 Web 语言一样，它旨在为 Web 开发人员提供一种与数据库对话并在网页上显示数据的方式。与许多这样的语言一样，在早期，它不是最先进的技术，但它是实现、理解和使用起来较为简单的技术之一。这一愿景在今天的 cold fusion MX(cold fusion 的 6.x 版本)中仍然适用。事实上，ColdFusion 是微软发布 ASP 的原因，ASP 是由 Aspect Software 开发的，Aspect Software 是 Allaire 的竞争对手，后来被微软收购。所以，总的来说，你们所有的 ASP 程序员都应该感谢 ColdFusion 的开发者，感谢你们的编程语言！

现在，让我们回到 ColdFusion 语言的背景。除了收购和变化的历史之外，ColdFusion 一直是为 Web 构建的快速应用程序开发(RAD)语言。ColdFusion 通过提供基于标记的用法来实现这一 RAD 声明，这是任何 HTML 程序员都熟悉的。

许多其他语言都使用编程方法来编写服务器端脚本，这对于经验丰富的程序员来说是很好的，但是对于那些大部分时间都在使用 HTML 标记的人来说，这可能有点令人畏惧。ColdFusion 标记看起来总是类似于`<cfTagName>`，只是“标记名”被您正在使用的实际标记所替换。类似于 PHP 使用`<?php`和 ASP 使用`<%`来表示代码开始的方式，`<cf`告诉 ColdFusion 服务器，“嘿，这里有一些 ColdFusion 要处理”。

大多数 ColdFusion 标签都有一个构成标签对的结束标签。和 HTML 标签一样，CF 标签以`</cfTagName>`结尾。例如，您可以使用以下代码:

```
<cfloop index="i" from="1" to="10"> 

  <cfoutput>#i#<br /></cfoutput> 

</cfloop>
```

`<cfloop>`和`<cfoutput>`标签都是起始标签；`</cfloop>`和`</cfoutput>`是各自的结束标签。从上面的例子中可以看出，您可以在标记之间放置代码，如果您的应用程序需要的话，您甚至可以嵌套标记。

您刚刚看到了 ColdFusion 代码的一小段。我并不是想在这里教你 ColdFusion，而是想为你提供足够的知识，让你有好奇心去进一步检验它。要了解更多有关 ColdFusion 标签(又名 ColdFusion 标记语言(CFML))的信息，请访问 Macromedia 网站或浏览 SitePoint 上的许多精彩文章。

##### ColdFusion 神话被揭穿

现在，您已经对 ColdFusion 的历史及其基于标记的语言的工作原理有了一些非常基本的背景信息。但是，我敢肯定你还没有被 ColdFusion 说服——事实上，你现在可能和我想的一样。让我们深入了解一些关于 ColdFusion 的神话和误解，这样我们就可以消除一些误解。

***误区一:ColdFusion 是有钱人的语言***

假的！是的，如果你去 Macromedia 的网站查找 ColdFusion 服务器，你会看到标准版的标价是 1300 美元，如果你去 Zend 的网站，你可以花大约 1875 美元购买他们的 PHP 性能套件。我知道您不需要 Performance Suite 来运行 PHP 应用程序，但是 Performance Suite 为您提供了“动态内容缓存、代码加速和文件压缩技术，作为一个单一的解决方案”，这就是 Macromedia ColdFusion 服务器为您提供的现成解决方案。

这是指出 Macromedia 不对开发服务器收费的好时机，只对实际的生产服务器收费。那么，当你投入生产时会发生什么呢？嗯，如果您的团队有预算，您可以购买 Macromedia 标准版或企业版，或者选择免费的 ColdFusion 服务器版本。除了开发人员版之外，下载任何 Macromedia ColdFusion 服务器，30 天后，它就变成了全功能开发人员版。

一家名为 [New Atlanta](http://www.newatlanta.com) 的公司拥有自己版本的 ColdFusion 服务器。这种 ColdFusion 服务器被称为 Blue Dragon，它有 3 种风格，就像 Macromedia 的服务器一样。New Atlanta 有服务器版、服务器 JX 版和 J2EE 版，而 Macromedia 有标准版、企业版和企业 J2EE 版。新的亚特兰大服务器版本的伟大之处在于，它目前是免费的，而且没有任何迹象表明这种情况会很快改变。为什么免费？它没有新的亚特兰大 JX 版本或 Macromedia 标准版中的所有 CFML 标签。

我在新的亚特兰大免费服务器上工作过，我可以告诉你，它很棒。它确实对您可以使用的标签和可以连接的数据库类型有一些限制(目前仅限于 MySQL、PostGreSQL 或通过 ODBC / JDBC 桥的 ODBC)，但这些都不会真正影响您的开发—大多数缺少的标签都是 Macromedia 特有的。就性能而言，我真的很难区分，尽管它可能会因应用程序和服务器的不同而不同。

亚特兰大的新产品确实为初露头角的 ColdFusion 程序员提供了部署应用程序的能力，而最终用户只需支付很少的成本。您甚至可以将新的 Atlanta 服务器与您的应用程序捆绑在一起，并一次性部署它(要完成这一点，需要特殊的许可)。因此，ColdFusion 不再是富人的专利。新亚特兰大把它带给了大众。

***误区二:ColdFusion 速度慢***

假的！ColdFusion 的速度非常快，即使对于一个刚刚开始学习并且还没有学会所有技巧的人来说也是如此。

目前，ColdFusion MX 是使用 Java 构建的(过去是在 C++上开发的)，当你的页面第一次运行时，它们被编译成 Java 字节码。通俗地说，您的 ColdFusion 页面会在首次运行时进行编译，就像一个程序一样，然后以二进制文件的形式存储在 ColdFusion 服务器上。如果您的 ColdFusion 服务器配置为打开可信缓存，则您的文件将只编译一次。如果关闭了可信缓存，那么每次页面加载时或者页面过期时都会编译您的文件(同样，讨论 CFML 代码和服务器配置细节也超出了本文的范围)。

那么，被编译成字节码的页面有什么特别之处呢？

字节码是由程序处理的计算机目标代码，通常被称为虚拟机，而不是由“真正的”计算机，硬件处理器处理。

(定义来自[Whatis.com](http://whatis.techtarget.com/definition/0,,sid9_gci211722,00.html))

一旦 ColdFusion 代码被编译成字节码，您的服务器就可以更快地解释您的程序指令，因为它使用的是它已经理解的语言。许多当前的 Web 语言是在运行时解释的，这意味着它们在每次运行时都被处理和编译。通常，网页上的字节码和解释代码之间的差别是几毫秒到几秒。然而，在 Web 应用程序中，这些差异会很快累积起来，很容易就意味着满意的客户和不满意的客户之间的差异。

***误解 3: ColdFusion 不像< insert language >那样受支持！***

又错了！ColdFusion 拥有一个庞大的追随者社区，并得到最大的互联网公司之一的支持。你可以在世界上几乎每一个主要城市找到 ColdFusion 的忠实追随者。他们通常属于当地的 ColdFusion 用户组(CFUG)，并且总是愿意伸出援手或提供一些指导来帮助您。到处都有 ColdFusion 社区涌现。

你可以在 Macromedia 的 ColdFusion 开发中心找到帮助，在 SitePoint 也有一个活跃的 CF 社区。有些现在开始提供 ColdFusion 课程，当然，还有许多网站提供 ColdFusion 和 ColdFusion 认证的课件。

***误区四:ColdFusion 没有能力<插入另一种语言的酷功能>***

假的！我还没有发现 ASP、PHP 或 JSP 可以做 ColdFusion 不能做的事情。ColdFusion 具有上述语言的所有编程能力，并与 Flash 或新的 Flex 语言等其他 Macromedia 技术紧密集成。

与这些语言相比，ColdFusion 最大的特点之一是这些特殊功能大部分都内置在 ColdFusion 中。以图表为例。通过一个简单的数据库查询和 CFChart 标签，我可以创建 Flash、JPG 或 PNG 格式的条形图、线形图、金字塔图、面积图、圆锥图、曲线图、圆柱图、阶梯图、散点图或饼状图，图形可以是 3D 或 2D！这个工具是现成的，只需要很少的配置，甚至不需要配置。

ColdFusion 还挂钩到其他 Web 语言和对象，如 C/C++、Java、COM、CORBA、XML、SOAP 等等！由于所有这些挂钩和 API，您永远不会局限于仅在 ColdFusion 中编程。以我现在的雇主为例。他们的代码 99%是 Java，但由于 ColdFusion 与 Java 的挂钩，我可以将 Java 开发人员的类文件用于我的应用程序，从而提高我们的代码重用率。如果我自己这么说的话，那真是太时髦了。

误解 5: ColdFusion 鼓励糟糕的编程实践。

假的！ColdFusion 提供了开发高度可伸缩、模块化和可维护代码所需的所有构建模块。ColdFusion 不强制任何特定的开发方法，而是专注于完成工作。许多使用 ColdFusion 的程序员通常不会写出完美的代码，但是你可以在 ASP 或 PHP 中找到同样的问题。

现在有两种主要的开发方法:[保险丝盒](http://www.fusebox.org/)和[马赫 II](http://www.mach-ii.com/) 。随着时间的推移，使用这些方法之一的开发人员可以学习更好的编码实践，但是实际上是由程序员和项目来决定什么工作得最好。

这就是关于 ColdFusion 的五大神话。更多问答，请查看 [CFFAQ 网站](http://www.cffaq.com)。

##### ColdFusion 的优势

既然我们已经澄清了，让我们来谈谈 ColdFusion 的一些优点，以及为什么它可能非常适合您的公司，或者是您进行调查的好方法。

##### Java 基础

正如我之前提到的，ColdFusion 服务器是使用 Java 构建的，并且是经过 Sun J2EE 验证的应用程序。这意味着您可以在各种应用服务器上部署和运行 ColdFusion 和您的应用程序，例如 Sun ONE、BEA、IBM WebSphere 和许多其他服务器。Macromedia 甚至在 ColdFusion 的企业版中包含了 JRUN 4 的完全许可副本。

这对一般开发者意味着什么？好吧，假设你创建了一个非常酷的应用程序，并把它卖给了一家大公司，在接下来的几个月里，这家公司决定基于 Java 进行标准化。如果你有一个 PHP 或 ASP 应用程序，你可能会有大麻烦，失去你的合同，但是，有了 ColdFusion，你可以高枕无忧。您的应用程序将在他们的应用服务器上运行——它甚至将与他们现在和将来的 Java 代码进行交互。

##### 错误和错误修复

开发人员面临的另一个大问题是调试和接收来自应用程序的错误消息。使用 ColdFusion，您可以捕捉异常并以您喜欢的方式响应它们(就像在 Java 中一样)，或者您可以允许服务器向用户显示错误消息。从 ColdFusion 管理面板中，您可以设置希望错误包含的详细程度，确定哪些 IP 地址可以看到详细的错误，甚至可以设置自定义页面来处理错误或丢失的模板。使用 ColdFusion 调试，您会收到一条详细的错误消息，告诉您错误的确切位置、堆栈跟踪是什么(出错之前调用的文件的顺序)，以及关于问题可能是什么的建议。您很少会收到导致您思考更多、调试更少的错误消息。

除了大量的错误消息，您还会得到大量的调试信息。当我在开发环境中加载 ColdFusion 页面时，我会看到我的应用程序的名称和我当前正在查看的模板。我还可以看到完整的堆栈跟踪，包括页面执行时间和这些页面的完整路径。我看到了所有应用程序、CGI、Cookie、服务器、会话、表单和 URL 变量的完整列表。当我可以简单地在浏览器中滚动并得到一份完整的报告时，我几乎没有理由打印出一个变量来查看它的值。

##### ColdFusion 组件–CFC

随着 ColdFusion MX 的发布，Macromedia 引入了 ColdFusion 组件(也称为 CFC)的概念。这些是 ColdFusion 的一大飞跃，也是新系统最受欢迎的功能之一。更多详情请参见 [ColdFusion 组件简介](https://www.sitepoint.com/article/components-introduction)。

CFCs 最大的特性之一是能够记录你的代码，而不必加入一堆注释标签。通过在你的 CFC 文件和<cffunction>中使用“hint”属性，你可以告诉未来的用户你的 CFC 是做什么的，各个方法(也称为函数)是做什么的，以及它们接受(输入)和显示(输出)什么类型的数据。然后，您可以直接浏览到 CFC，输入您的管理员凭证，并收到该信息的非常好的输出。</cffunction>

如果你曾经不得不记录你的代码，你会知道这有多痛苦。我发现这个特性在我的日常操作中非常有用。由于文档随着代码的变化而变化，所以它也是非常动态的，所以您可以放心，您当前的文档将包含您刚刚添加的新特性。

我希望，到现在为止，你正以不同的眼光看待 ColdFusion，并把它视为需要进一步研究的东西。我强烈建议您下载 Macromedia ColdFusion Server 或 New Atlanta Server 的副本，并试用它。我敢肯定，只要有开放的心态和一点时间，你会将 ColdFusion 视为当前 Web 开发工作的可行替代方案。如果您有任何问题，请随时直接联系我，或者在 SitePoint 论坛上发帖跟进。

##### 资源

对于那些已经准备好开始工作的人，这里有一些很好的资源:

*   [Macromedia cold fusion](http://www.macromedia.com/coldfusion/)–从下载各种服务器版本开始您的旅程。一定要抢开发者版，1 个 IP 地址就好。
*   [新亚特兰大蓝龙](http://www.newatlanta.com/products/bluedragon/)-寻找免费的 ColdFusion 服务器以及 Macromedia 标准版和企业版的替代产品。
*   Forta.com 这家伙是 ColdFusion 的首席传播者。就当他是 ColdFusion 教父吧。Ben Forta 从事 ColdFusion 和其他相关编程已经很多很多年了。他写过许多关于各种 ColdFusion 主题和其他 Web 技术的书籍，被认为是该领域的专家。他的网站提供了他的书籍、技巧、诀窍、提示、托管链接的链接，以及一个[博客](http://www.forta.com/blog/)，在那里你可以跟上他和 ColdFusion 的进度。
*   [MXNA](http://www.markme.com/mxna/)Macromedia XML 新闻聚合器是互联网上许多 Macromedia 相关博客的中心点。在这里，您可以找到关于 ColdFusion、Dreamweaver、Flash 和更多 Macromedia 主题的博客。这是 Macromedia 知识的一个重要中心点。
*   [SitePoint ColdFusion 博客](https://www.sitepoint.com/blog/)和[论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=214)sitepoint.com 的任何资源都不会出错，这两个也不例外。

## 分享这篇文章
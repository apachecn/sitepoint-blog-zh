# 文件使世界运转

> 原文：<https://www.sitepoint.com/documentation-makes-the-world-go-round/>

你正在做一个软件项目，如果你读这篇文章的话，这可能不是一个新的情况，并且发生在你的项目需要的特定功能上。作为一个高效(和懒惰)的开发人员，您认识到这个功能非常普遍，可能以前有人需要它，并且已经为它编写了某种类型的库。为什么要重新发明轮子？

因此，您跳转到您喜欢的搜索引擎，找到一个可能的候选项，浏览登录页面，并得出结论，这个库符合您的要求。然后找到一个指向文档的链接并单击它。毕竟，您需要弄清楚如何安装、配置它，并实际开始在您的项目中使用它。只有一个问题:文档链接所链接的页面只包含生成的 API 文档。你立即想到:*我从哪里开始？*

您可以后退一步，搜索版本控制存储库，找到一个名为`README`、`INSTALL`或者甚至`EXAMPLE`的文件，它会为您提供一些关于下一步该做什么的信息。即使您设法安装并配置了这个库，接下来您要做什么呢？哪段特定的代码提供了库逻辑的入口点？您是否需要做一些特定的事情来加载或引导它？在使用它之前，除了将它放到项目的目录结构中并更改一些配置设置之外，您还需要做什么吗？

您可能会摸索一段时间，浏览 API 文档，寻找比类名、方法名、参数类型和返回值更重要的信息。您可能会做出有根据的猜测，并尝试一些事情，只是为了获得一个让库工作的立足点。如果你不成功，就会有一个时刻，你会把双手举到空中，继续下一个选择。

如果你写的代码是要和别人分享的，那就设身处地为他们想想。不要因为文档不足而让你的项目失去潜在的用户、社区成员和潜在的贡献者。

## 内容为王

“内容为王”是 SEO(搜索引擎优化)的主导原则。这同样适用于您的文档:它必须充满常见用例、示例代码、关于潜在陷阱的警告等信息。可悲的是，[橡皮鸭](http://en.wikipedia.org/wiki/Rubber_duck_debugging "Rubber duck debugging - Wikipedia, the free encyclopedia")并不像调试那样有效，因为鸭子不提供反馈，而且这种问题的解决方案不太可能在你的解释中显露出来。

首先尝试你认为是好的文档，然后做所有好的开发人员都做的事情:测试它。让几个人阅读，尝试使用图书馆，并提供反馈。询问他们哪些部分不清楚，或者可以用更好的方式陈述或组织，哪里缺少信息，哪些额外信息可能有用，等等。在项目启动之前迭代地应用改进，以便大多数潜在的问题在之前或之后很快得到解决。

## 打开它

制作高质量的内容是一项繁重的工作，尤其是在开始阶段，但这也是吸引早期用户的一个重要原因。一旦项目启动，如果人们没有时间或技能来开发源代码，他们可能会愿意为文档做贡献。但是，如果他们没有办法让你做出改变，那对你的贡献来说是非常令人不快的。你不想那样。

有两种打开文档的主要方法来帮助您的用户让它保持最新并满足他们的需求:使用 wiki 或在版本控制存储库中维护它。

像 [DokuWiki](http://www.dokuwiki.org/ "dokuwiki [DokuWiki]") 、 [MediaWiki](http://www.mediawiki.org/ "MediaWiki") 和(对于大公司) [Atlassian Confluence](http://www.atlassian.com/software/confluence/ "Content and Social Collaboration Software | Atlassian") 这样的 Wiki 通常很容易设置和定制，以满足您的需求。您可以允许任何人编辑它们，或者要求用户首先注册。大多数维基维护每个页面的版本历史，允许用户恢复恶意修改，并查看谁做了修改以及为什么要做修改。关于如何使用 wiki，可能会有一个轻微的学习曲线，但基于浏览器的界面对于潜在的贡献者来说是非常容易访问的。

使用版本控制库允许文档与源代码一起维护。在合并之前，添加或更改的实验分支可以很容易地被同行共享和审查。像 GitHub 提供的那些接口可以使跟踪提议的贡献变得容易，同时将更改限制到具有合并权限的选定数量的贡献者，以保持质量保证。根据您选择的格式和工具链(例如像 [Docbook](http://docbook.org/ "DocBook.org") 和 [PhD](http://doc.php.net/phd/docs/ "_blank") )，以多种格式(如 HTML、PDF 或 ePub)提供您的文档，并将文档集成到您的构建和持续集成过程中也可能更容易。

无论您提供什么样的文档，都应该尽可能地易于访问。不需要用户通过注册过程来访问它。托管公共文本或 HTML 副本，供用户在浏览器中访问，而不是要求他们下载完整的文本或 HTML 副本，以便在单独的程序中查看。这有额外的好处，使搜索引擎能够索引它。使用锚点使 HTML 副本中的节可链接，以允许用户轻松地单独引用它们。

## 文档，文档，文档

史蒂夫·鲍尔默因他的“开发者，开发者，开发者”口号而声名狼藉。毕竟，这代表了开始软件项目的人通常想要的:围绕该项目形成一个开发人员社区，为其做出贡献，并帮助其发展，使其比单独一个人做得更好。虽然这不是达到目的的唯一方法，但是如果文档是好的和经过深思熟虑的，它就不太可能阻碍你实现这个目标。

注意寻找技术写作资源来帮助你的文档写作技巧。这种资源以多种形式存在，比如你当地大学的一门课程，在 Kathy Sierra 现已解散的[创造热情用户](http://headrush.typepad.com/creating_passionate_users/2006/10/better_beginnin.html "Creating Passionate Users: Better Beginnings: how to start a presentation, book, article...")博客“交流！”书中的章节[务实的程序员](http://www.amazon.com/gp/product/020161622X "Amazon.com: The Pragmatic Programmer: From Journeyman to Master (9780201616224): Andrew Hunt, David Thomas: Books")、[缺失的手册作者指南](http://davidbarneswork.posterous.com/the-missing-manual-author-guidelines-top-rate "The Missing Manual author guidelines = top rate advice for ANY tutorial author - David Barnes at work")，以及 Rich Bowen 的演示文稿[写出更好的 FM](http://wooga.drbacchus.com/files/Write_A_Better_FM.pdf "Write_A_Better_FM.pdf") 。它们也可能以不太明显的形式出现，如 Garr Reynold 的书 [Presentation Zen](http://www.presentationzen.com/ "Presentation Zen") ，该书涵盖了创建挑衅性的视觉演示——无论最终媒体如何，这本书都教你如何以对消费者有趣和有益的方式呈现信息。

## 摘要

简而言之，尽最大努力记录你的项目，并让它为他人所用。你可能会惊讶，仅仅因为你投入了时间和精力，你在开发中就获得了如此多的支持。无论如何，这将是值得花费的时间和精力。

## 分享这篇文章
# 谷歌 10 分钟代码之夏:速成班

> 原文：<https://www.sitepoint.com/google-summer-code-10-minutes-crash-course/>

自 2005 年成立以来，谷歌每年都会组织一个名为[谷歌代码之夏](https://www.google-melange.com/gsoc/homepage/google/gsoc2015) (GSoC)的年度项目，学生们在这个夏天参与各种组织的开源项目。GSoC 是为 18 岁以上的学生设立的，它的对等物是为高中生设立的[谷歌代码输入](http://www.google-melange.com/gci/homepage/google/gci2014)。

在每年宣布该计划后，开源组织首先向谷歌提交申请，以被选为指导组织。一旦宣布了指导组织，学生们就要提交详细的提案，以便参与选定组织的项目。然后，被选中的学生在暑假期间编写代码，与他们各自的导师一起完成他们的项目。在项目的成功竞争中，谷歌向每个学生开发者支付 5500 美元的津贴，每个成功项目的 500 美元归指导组织所有。我刚才引起你的注意了吗？

## 了解 GSoC 2015 年时间表

在我们继续讨论选择项目的一般准则之前，让我们看看 GSoC 2015 项目的时间表。我将讨论与学生相关的事件，详细的时间表可以在 Melange 网站上找到。

*   3 月 2 日:在谷歌 2015 年代码之夏网站上公布了被认可的指导组织名单。
*   3 月 16 日:学生申请期开始。
*   3 月 27 日:学生申请截止日期。

3 月 2 日至 3 月 27 日这段时间对希望通过考试的学生来说至关重要。这是你决定在哪个组织工作的时候，与项目的导师联系，并准备你的建议供你的导师审阅。在 3 月 27 日之前，您必须在 Melange 网站上提交您的提案，之后您的提案将被锁定。然而，如果你发现你的建议中有错误，你可以要求你的指导组织修改它。

*   4 月 15 日:向指导组织公布时段分配。

虽然指导组织是提前宣布的，但是每个组织的项目数量是在以后完成的。到 4 月 15 日，你的指导机构将会知道这个夏天应该接收多少学生。这些信息是不公开宣布的，但你可以向你的导师询问，以了解你被选中的机会。

*   4 月 21 日:进行第一轮重复数据消除检查；各组织共同努力解决尽可能多的重复问题。

一个学生被允许在不同的组织下提交最多五个方案。因此，有可能选择两个或更多的建议。但是根据项目的规定，一个学生在夏天只能做一个项目。因此，这些组织试图首先在他们之间解决这个问题，或者询问学生他们想从事哪个项目。

*   4 月 27 日:在谷歌 2015 年代码之夏网站上公布了被接受的学生提案。
*   5 月 25 日:学生们开始为他们的谷歌代码项目之夏编写代码；谷歌开始发放首批学生款项，前提是税单存档，学生在社区中信誉良好。

从公布入选学生到编码期开始的这段时间被称为“社区结合期”。谷歌建议你花时间去了解在[邮件列表](http://en.wikipedia.org/wiki/Mailing_list)或 [IRC](http://en.wikipedia.org/wiki/Internet_Relay_Chat) 上的人，阅读文档，了解组织中正在发生的事情，开始你的编码工作。然而，如果你和你的导师认为这是最好的做法，你也可以在社区结合期开始编码。此时会向您发送一个欢迎包。

*   7 月 3 日:中期评估截止日期；谷歌开始发放学生中期付款，前提是学生调查合格。

编码开始六周后，你的导师会对你的工作进行评估。如果你已经达到了你在提案中设定的目标，你应该能够相当容易地通过。

*   8 月 21 日:确定的“落笔”日期。导师、学生和组织管理者可以开始向谷歌提交最终评估。
*   8 月 28 日:最终评估截止日期

你的最终工作由你的导师组织来评判，以决定你是否通过了这个项目。成功完成项目后，将会向您邮寄一份完成证书和一件谷歌代码之夏 t 恤。

## 怎样才能被选中？

在过去的两年里，我成功地完成了这个项目，人们经常问我被录取的秘诀。显然没有这样的秘密。然而，肯定有一些你可以遵循的指导方针，但它们并不适用于每一个人。

### 先决条件

我认为获得 GSoC 最重要的因素是在一项技术上有扎实的知识。无论是 PHP、Python、Ruby 还是 JavaScript——确保你对这种语言很熟悉，并根据你的熟悉程度确定项目的目标组织。

其次，所有开源组织都使用[版本控制软件](https://www.sitepoint.com/version-control-software-2014-what-options/)来管理来自全球分布式团队的代码变更。无论是 git、svn 还是 mercurial，你都应该了解版本控制的基础知识，并且能够自如地使用它。

在我看来，另一个重要的先决条件是使用单元测试。大多数组织需要你为你提交的代码编写单元测试，因此，你需要知道如何写好它们。

虽然以前对开源项目的贡献是一个优势，但只要你能证明你能通过提交补丁对代码库做出贡献，这就不是必须的。

### 早点开始

虽然现在考虑这个已经晚了，但是如果你打算申请 GSoC 2016，现在就开始吧。你开始得越早，你被选中的机会就越大。但是你到底是如何“开始”的呢？

你可以查看被选中的组织，浏览他们的“想法页面”,看看你是否对他们的项目感兴趣。你可以提出你自己的想法，但是他们各自页面上列出的想法已经被讨论过了，组织很可能会采纳这些想法——除非你的想法真的很有创意！

如果你对某个组织感兴趣，加入他们开发者的邮件列表，在 IRC 聊天室里闲逛。开源人员通常非常友好，如果你有任何问题，他们会帮你解决！然而，记住导师也有全职工作，开源只是志愿工作，所以你也应该考虑周全！

此外，当你在邮件列表或 IRC 中交流时，确保你是有礼貌的。除此之外，要有耐心(还要避免像这个一样[)。](https://github.com/atutor/ATutor/pull/64)

### 证明你可以为代码库做出贡献

在邮件列表或 IRC 上被介绍可能还不够。要入选 GSoC 这样的竞争项目，你需要证明你是最优秀的。最容易引起注意的方法是为代码库做贡献。

阅读文档并在本地机器上克隆代码。如果您在本地运行时遇到问题，您可以随时返回邮件列表或 IRC(您的问题一定会得到解决！)

每个组织还维护一个 bug 列表(或者有其他名字的列表，比如 bug 追踪器)。找一个简单的 bug 开始，或者在邮件列表上找一个。在这一点上，你必须知道一件重要的事情——许多其他人正在这个组织中工作，所以永远不要开始处理一个 bug，除非你已经被分配了这个 bug。如果你这样做了，而且有人已经在做了，那么组织就有义务接受由最先被指派的人提供的解决方案。

一旦你解决了一个 bug，提交一个补丁(或者一个 pull 请求，如果在 GitHub、GitLab 或者 BitBucket 上的话)。然后处理第二个 bug。然后是第三个、第四个等等。大多数组织要求您提交一个补丁程序，作为加入该计划的先决条件。

## 提出建议

建议书是一份详细的文件，在你被选中参加这个项目之前，你要提交给组织。它大致包含了你夏天打算做什么的信息，这需要对你正在做的项目有很好的理解。

建议大纲通常由指导组织提供给你。他们通常没有字数限制，所以最好尽可能写得长一些，解释每一个小细节。

这里有一些写好提案的指导方针。

## 最后的想法

“我是最后一年的学生，我将在项目结束前毕业。我有资格吗？”

"作为学生参加谷歌代码之夏需要多少时间？"

"谷歌代码之夏被认为是一份工作还是任何形式的雇佣？"

如果你有这类问题，请浏览 Melange 网站的 FAQ 部分。如果还有问题，欢迎在下面提问！

## 分享这篇文章
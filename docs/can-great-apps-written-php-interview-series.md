# 伟大的应用程序能用 PHP 编写吗——访谈系列

> 原文：<https://www.sitepoint.com/can-great-apps-written-php-interview-series/>

不久前，我在 MailChimp 博客上读到了一篇关于他们使用 PHP 的经历的旧文章。

这引起了我的共鸣，因为他们分享的观点我自己也感受到了，这些年来也听到了很多次。你可能会问，这些情绪是什么？

他们推断，尽管 PHP 取得了成功，但它并不是真正的编程语言。它们暗示，无论你有多优秀，无论你取得了什么成就，如果你是一名 PHP 程序员，那么你真的不是一名真正的开发人员。

他们建议，或者说是预先假定，你真的应该成为开发 **Ruby** 、 **Python** 或 **Go** 的酷孩子之一；基本上除了 PHP 以外的任何东西。毕竟，你真的能用 PHP 做什么，对吗？

尽管它取得了成功，或者人们也取得了成功(比如脸书*、*马奇姆*、*海洛*、*谷歌*等等)，但我们还是应该使用以纯粹语言为基础的语言；而不是随着时间的推移，PHP 演变成一种语言。*

 *以下是 MailChimp 帖子的一些摘录，突出了人们对他们使用 PHP 作为平台基础的反应:

> 尽管 PHP 很受欢迎，但几乎毫无例外地被编程精英认为是目前使用的最糟糕的语言之一
> 
> 术语“优秀的 PHP 程序员”被认为是一个矛盾的说法
> 
> 你可以想象当我们试图告诉一个优秀的开发人员我们使用 PHP 来解决很酷很有趣的问题时，我们看到的恐惧和惊讶

我既听到了这些观点的表达，有时也因为它们而感到不安全感。所以这个帖子真的引起了我的共鸣。我曾经用一套不起眼的脚本做了这么多，帮助了 Rasmus 的早期开发任务。

MailChimp 也是如此。

来自同一篇文章，以下是他们的一些成功案例:

> 我们使用这种架构来**处理每天数万用户发送的三千多万封电子邮件**，**产生数千万次的反弹、打开、点击和退订**，所有这些都需要近乎实时地处理。我们每天进一步处理数百万个 API 请求和数百万个订阅和确认。
> 
> 总的来说，我们一个月处理超过 5 亿的动态页面浏览量。我们的后端系统每天运行数百万个作业，计算统计数据，查询地理数据，扫描一切不良行为和滥用。
> 
> 随着**我们每年增长 500%或更多**，这个数字将会越来越大

但是尽管如此，PHP 通常有一段艰难的时间，从声誉角度来说。上周末在伦敦举行的 PHP 英国会议上，主题发言人之一格伦·坎贝尔对此进行了阐述，他在演讲中提到了一些链接。以下是其中的一些摘录:

其中最苛刻的一篇文章 [PHP:糟糕设计的分形](http://me.veekun.com/blog/2012/04/09/php-a-fractal-of-bad-design/)写道:

> PHP 是唯一的例外。事实上，PHP 的每一个特性都以某种方式被破坏了。语言、框架、生态系统都很糟糕。我甚至不能指出任何一个该死的东西，因为损害是如此系统。每次我试图编制一份 PHP 抱怨清单时，我都会陷入这种深度优先的搜索中，发现越来越多骇人听闻的琐事。(因此，分形。)
> 
> PHP 是一个尴尬，是我的手艺的一个污点。它是如此的破碎，却被每一个还没学会其他东西的有能力的业余爱好者如此称赞，以至于让人抓狂。它没有多少可取之处，我宁愿忘记它的存在。

《恐怖编码》的杰夫·阿特伍德说:

> 我们应该讨论的不是 PHP 有多可怕——尽管它持续的可怕是一个特别严重的指控——而是我们程序员如何在文化上用一个更好的工具取代一个有严重缺陷的工具。我们如何鼓励新程序员避免拿起双爪的锤子，而选择普通的锤子呢？

他还在推特上写道:

> PHP 是编程语言的核心。

这一论断中有一些微妙的前提，但我们将把它留到下一次。

或者听听弗雷德里克·霍尔姆斯特姆的这句话:

> 我见过的或者用 PHP 开发的每个解决方案都感觉笨重，没有优雅和风度。使用 PHP 有点像从十层楼高的建筑上扔一个 10 磅重的混凝土立方体:你会很快到达目的地，但不是很优雅。…我喜欢 PHP，它是一些工作的合适工具。它只是一个丑陋，笨重的工具，让我哭，做噩梦。是新款 VB6 穿 C 装。

## 这些论断正确吗？

我不会说 PHP 应用程序总是软件工程优秀的光辉典范。但是古老的谚语*一个贫穷的工人责怪他的工具*也可以在这里找到依据。

我开发了管理政治筹款活动的应用程序，简化 LDAP 接口的应用程序，定制电子商务商店的应用程序，等等。

我见过其他人*建立联盟网络*，*创建大型 CMS*和*财务包*。我见过各种各样的公司提供 PHP 绑定，比如 [Stripe](http://www.stripe.com) 、 [FreeAgent](http://www.freeagent.com) 、 [Twilio](http://www.twilio.com) 等等。

这让我开始思考。是不是只有我一个人觉得，尽管 PHP 有缺点，但它的能力远远超过它？

其他人是否也有类似的想法，就像 Glenn 在他的演讲中建议的那样，我们不必担心负面影响，只要继续用 PHP 做伟大的事情就可以了？我知道有！

## 来自实际开发人员的反馈

所以我决定去那里采访一些人，他们有坚实、实际的经验，用 PHP 开发不同规模的应用程序，并听取他们的意见。

这些开发人员来自不同的公司和背景；包括*自由开发者*、*专家开发团队*，比如 [Roave](http://www.roave.com) ，直通大公司，比如 MailChimp 自己。

未来几周，我将在 SitePoint 上与你分享这些采访。我相信他们会向你展示，尽管流传的一些批评是正确的，但基本上是无关紧要的。

我相信你会发现，如果你是一个专业人士，你可以用 PHP 开发严肃的企业应用程序，如果你选择这样做的话。接下来是对来自 Twist Digital 的 Bobby Deveaux 的第一次采访。

* * *

## 是什么让你对 PHP 产生了兴趣？

早在 2000 年，我在网上玩一个叫 Planetarion 的游戏。这是一个 MMORPG，我喜欢它！我当时才 15 岁，知识有限，就尝试用 wget 下载游戏。我真傻。

我在网上研究了如何创建一个 PHP 文件(游戏是用 PHP 编写的)。我最终从 SourceForge([http://sourceforge.net/projects/blacknova/](http://sourceforge.net/projects/blacknova/))下载了 Black Nova Traders 我贡献并学习了很多 PHP(不好，但仍然是 PHP)。它向我介绍了 ADODB & MySQL，以及我 PHP 技能的基础。这是我喜欢开源的主要原因。

## 关于 PHP，有哪些事情困扰着你？

很难说咬我的东西是否是因为 PHP。但是我确实发现一些令人沮丧的事情，即使是对医生来说，也是古老的`needle/haystack`或`haystack/needle`问题。

这么小，但是我记不住*每个*功能的顺序。也许是我选择 IDE 的错，或者我应该接受它…但是拜托，`array_walk`和`array_map`有不同的顺序吗？！

## 有哪些亮点或可取之处

这些年来，我见过很多混乱的代码。诚然，也许当时我觉得它看起来不错；但是 PHP 社区的伟大之处在于总会有改进和新的想法出现，看到事情是如何发展的真是太棒了。

## 对你来说有哪些引人注目的 PHP 特性？

是安逸。很容易就能拼凑出一些代码。我知道有些人认为这是敌人，是万恶之源，但是快速并不总是意味着肮脏。

## 你希望语言中增加什么？

我不是追求完美的人；我对添加任何东西都不太在意。PHP 开发人员的工作非常出色，他们所添加的任何东西都会受到极大的赞赏。我喜欢 PHP 的本来面目。它当然有它的怪癖，但这是我越来越喜欢它。

## 为什么 PHP 胜过 Ruby、Python 或 Go？

我是一名开发人员，不是 PHP 开发人员——但我可以通过开发主要是 PHP 的代码库获得报酬，这主要是因为我的经验。我已经用 Python 写了很多代码，我喜欢它。从前我用 C#。NET，但那是短暂的。不过对于个人项目，我已经开始使用围棋了。

## 你认为自己将来会转向另一种语言吗？

当然，我喜欢围棋的速度。唯一的问题是找时间学习如何有效地使用它，并尽可能快地用 PHP 创建应用程序。

## 你有定制的框架或设置吗？

我倾向于使用 Silex 作为 API 的基础。对于前端，我使用 [ZF2](http://framework.zend.com/) 、 [Symfony2](http://symfony.com/) 或[Kohana](http://kohanaframework.org/)——这取决于需求。

## 您是如何实施部署的？

我们目前有一个发布脚本，基本上是对所有服务器的 SSH，并检查一个特定的标签。即“git 结账标签/v1.2.1”。我开始新工作才几个月，我们正在做很多改变。我们正在努力建立一个持续集成服务器，但是首先我需要让团队编写单元测试。

我们在每个任务的特定分支上工作，并且总是部署到 QA 的测试/试运行环境中。我们的发布脚本通知团队修改之间的提交，并且有一个“确认”发布按钮，以确保只有在每个人都满意的情况下才发布。

## 你的标准工具链中有什么？

*，*崇高文字 3* ，*续作 Pro* ， *Dropbox* ，*牟* & *Github* ，当然还有 *Spotify* 。我们总是使用*代码嗅探器*来确保代码整洁，加上 [Sami](http://fabien.potencier.org/article/63/sami-yet-another-php-api-documentation-generator) 用于 PHP 文档。*

 *## 有哪些测试小技巧可以分享？

以单元测试的 100%覆盖率为目标。你可能达不到目标，但要确定你的目标。有些人会说这毫无意义，但这通常是因为他们编写了错误的测试。

当需要时，功能性的、非覆盖性的测试是好的，但是如果你的单元测试是正确的，那么你可能被覆盖了。如果你还没看过，Behat BDD 值得一看..哦，还有 [PHPSpec](http://www.phpspec.net/) ！

## 你使用的工具中有没有你离不开的？

没有 OSX 我会陷入困境的！我在苹果电脑之间跳来跳去，有时我只有宋承宪。我可以用 SSH 和我的 SSH 密钥做任何我想做的事情！

## 你的团队是如何工作的？

当我开始的时候，我设法实现了非常温和的敏捷实现。我们有一个 SCRUM 板，但它并不是真正的 SCRUM。董事会有我们的故事/任务，但他们没有努力/时间估计。

这违背了我的许多信念，然而我们有一个项目经理负责管理期望，公平地说，它工作得非常好。我们可以通过 git 标签一天发布多次，让业务保持愉快。

不幸的是，由于工作量大，我们倾向于成为一个被动的团队，而不是主动的团队。我想适当地实现 SCRUM，让项目经理扮演产品所有者的角色..一天:)

## 结尾部分

那么，鲍比的经历是否引起了你的共鸣？你能理解吗？他的经历有没有给你启发？请在评论中分享你的想法。

## 分享这篇文章**
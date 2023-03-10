# 我们与专家讨论了 Git 文字记录

> 原文：<https://www.sitepoint.com/we-talked-git-with-an-expert-the-transcript/>

今天早上与专家的交谈对于那些有幸在场的人来说是一次很棒的经历。拥有所有专业知识的人是 SitePoint 论坛团队的领导者 Thom Parkin，他肯定知道他的东西。与通常的一系列问答不同，Thom 进行了更多的小型讲座，甚至我们中一些更有经验的人也学到了一些东西。

不幸的是(有点讽刺意味的是), GitHub 在会议进行到一半时出现故障，这意味着任何试图进入的人都被拒绝——我们依靠 Gists 来控制对聊天室的访问。为此向所有未能加入的人道歉。

一些很好的资源来自于这次演讲，我在下面列出了这些，给那些不想浏览整篇文稿的人。

Git 综合初学者指南
[罗成和贝里隆德掌握 Git](http://shop.oreilly.com/product/0636920017462.do "McCullough and Berglund on Mastering Git")
[罗成和贝里隆德掌握高级 Git](http://shop.oreilly.com/product/0636920024774.do "McCullough and Berglund on Mastering Advanced Git")
[在 15 分钟内学会 Git](http://try.github.io/levels/1/challenges/1 "Git in 15 minutes")
Thom on[使用 Git 和 Dropbox](http://www.youtube.com/watch?v=2bxbzFQEMYM "Git and Dropbox")
如何[使用 Git 提交对新分支的更改](http://stackoverflow.com/questions/14655816/how-to-commit-changes-to-new-branch "Committing changes to a new branch")

如果你想知道我们在接下来的几周安排了什么，我们有一份即将到来的会议的时间表。如果你因为不知道而错过了今天的会议，你可以注册[电子邮件提醒未来的会议](https://www.facebook.com/sitepoint/app_115462065200508 "Email reminders")。

如果有人想知道今天早上发生了什么，这是完整的文字记录:

【21:53】<帕克特>热烈欢迎所有人——尤其是新来的人。

【21:53】

你们当中有人已经在日常工作中使用 Git 了吗？

与 subversion 相比，使用 git 的优势是什么？

【21:54】<帕金>伟大的问题，scruggs。

【21:55】

【21:55】<帕金>有许多人有偏好，而这些偏好通常是出于对某个特定工具的熟悉。

【21:56】<帕金>我用过很多最常用的工具，包括 SVN。事实上，在我的“日常工作”中，我不得不使用 SVN。

【21:56】<帕金特> Git 有几个明显的不同之处，我认为它们是优势。

【21:56】<park int>主要是，是本地的；这意味着您不需要集中式服务器。

【21:56】<Triopticon>在我尝试 Git 的短暂时间里，我不得不说我非常喜欢它！

【21:57】<帕金特>对 Git 的主要抱怨是[经常]迟钝的命令及其语法。

我并不反对这一点。

【21:57】

【21:57】<帕金特> Git 是{或多或少}独立的。

现在，这并不意味着你们不能合作！！

【21:58】<park int>和 Github 是最受欢迎的站点，代表了一个托管 Git 库的地方。

【21:58】<帕金特>我认为对 Git 最常见的误解(或误解)是 Github 和 Git 是一体的。

【21:59】<park int>bit bucket 是另一个托管 Git 仓库的流行站点。但是您可以在任何地方托管集中式 Git repo。

【21:59】<鹰派>是的，我听说过很多次了

【21:59】<Velochicdunord>他们有什么不同？

【21:59】<帕金特>几年前，我制作了一个视频，演示如何在 Dropbox 上托管 Git 回购；Dropbox 何时推出

【21:59】<Triopticon>Github 和 Bitbucket 我都喜欢。

【22:00】<Velochicdunord>有那个视频的链接吗？

【22:00】<HAWK>Velochicdunord Github 是一个托管 Git 库的站点。

【22:00】<帕金>回答维罗奇科诺的问题(并继续我的讨论)……

【22:00】<帕金>抱歉。我误解了这个问题。谢谢，霍克

【22:00】<帕金特>Git 与 Subversion 不同的另一点是，整个历史都保存在本地存储库中。

【22:01】<帕金特>当你在 Git 中工作时，所有的提交(文件的状态)都在你身边。

【22:02】<帕金特>事实上，一个项目(Git 回购中)的每一个合作者都拥有整个历史。因此，凭借这一点，任何时候都有大量冗余备份副本

【22:02】<帕金特>Git 存储信息的方式也是高效轻量的。

【22:02】<鹰派>欢迎刚刚加入的你们。

【22:02】<霍克>今天晚些时候我会在 SitePoint 上贴出完整的文字记录

【22:03】<鹰派>但同时你可能会发现这份文件很有用[https://s3.sitepoint.com/examples/git-project-changes.pdf](https://s3.sitepoint.com/examples/git-project-changes.pdf)

【22:03】<霍克>嗨莫洛娜

【22:03】<莫洛娜>嗨

【22:03】<莫洛娜>别告诉我我来晚了？

【22:03】<帕金>对不起，我不等你就开始了。

【22:03】<霍克>我们提前几分钟出发

【22:03】<帕金>我对这个话题太热情了，我无法控制自己！！

【22:04】<莫洛娜> Ok。这一次我会原谅你…仅此一次:-p

【22:04】<杰瑞>假设我开始着手一个项目，这个项目有一些历史记录和手工版本控制(或者是一个目录的快照，或者是文件名后附加了版本号的一系列文件)。我现在开始用一个干净的 git 库开发一个新版本。是否有可能创建第二个存储库作为主归档，并构建它

【22:04】<杰瑞>手工使用可用的文件，然后最终将当前存储库合并到主库中？

【22:04】<帕金>谢谢

【22:04】<霍克>我们一直在谈论 Git 相对于类似工具的优势

【22:04】<霍克>以及 Git 和 Github 的区别

【22:04】<杰瑞>有点复杂，但是我没有时间在开始当前工作之前创建主存储库

这是一个非常好的 Git 入门指南。谢谢鹰，我今天读了它。

【22:04】<莫洛娜>这是很重要的一个…谢谢霍克

【22:05】<帕金特>“优点”有点主观。但是我强调了差异*我*考虑了优势

【22:05】<卡洛琳>哦，我以为几分钟前开始有免费的 Git 课了。我迷路了吗？

【22:05】<帕金特>Git 的灵活性意味着你可以用多种方式处理这个问题。

【22:05】<斯克鲁格斯>帕金:你的结论很有见地

【22:05】<帕金>如果我面对这种情况，我会这样处理。

【22:06】<帕金特>将当前的工作文件放入 Git repo。将之前的作品(可能在 DVD 上)存档。

【22:06】<霍克>嗨，卡洛琳——不，你来对地方了。:)我们现在在聊 Git。随时欢迎提问。:)

我敢打赌，一旦这个项目完成，它看起来会比现在更不像那些旧文件。

【22:06】<霍克>今天晚些时候我会在 SitePoint 上贴出完整的文字记录

【22:06】<帕金>继续前进确保提交您的更改。

【22:07】<Triopticon>与 Mercurial 相比有什么区别或优缺点？

【22:07】<帕金特>Git 做得很好的另一件事，就是它允许你‘实验’或测试。

<帕金特>使用 Git 分支你可以做出巨大的改变，而不用担心你会失去你的位置(或者需要后退)。

【22:07】<帕金特> Mercurial 和 Git 非常*相似。

【22:08】<卡罗琳>哈。我不知道你们在说什么，这就是为什么我想上这门课来“学习 Git”！

我对 Mercurial 的经验非常非常少。如果你问我，我会简单地说，“我会拼写！!"LOL

【22:08】<霍克>卡洛琳——你可能会发现这份文件很有帮助[https://s3.sitepoint.com/examples/git-project-changes.pdf](https://s3.sitepoint.com/examples/git-project-changes.pdf)

谢谢，我会去的。

有时间就去逛逛。我们欢迎任何问题，即使是非常基本的问题。:)

【22:09】

【22:09】<帕金特>如果你不熟悉源代码管理的概念。或者您不是开发人员，但想知道这个工具如何帮助您。

【22:09】

【22:09】<帕金特>这是一本精心制作的安全指南。刚好够弄湿你的脚。

【22:10】<莫洛娜>那么帕金特，你在使用 GIT 之前有没有用过其他的源码控制软件？如果是这样，为什么让你改变？

【22:10】<park int>我用过 SourceSafe(别让我入门！！)而且，正如我前面提到的，我的“日常工作”迫使我使用 Subversion。

【22:11】<莫洛纳>我不得不说，对于那些使用 Linux 的人来说，这是一个很好的工具，当你习惯了 Windows :D 时，这就有点难了

【22:11】<Carolyn>我是一名作家，需要学习不止一个 CMS，Git 经常在开头就被提到。Sooo，显然我需要学习它，却对它一无所知。:)

对我来说，转向 Git 并不容易。我的观点需要一点调整。

【22:11】<杰瑞>感谢帕金。实际上，出于支持的目的，我们确实引用了很多旧文件，因为该产品的许多用户仍然使用旧版本。两个存储库肯定比我们现在所处的位置要好，但如果我能最终合并它们就更好了

<帕金特>但是，我必须说，a)阅读 ProGit 书籍和 b)了解更多关于 Git 的内部工作方式让我对它有了深刻的理解。

【22:11】<莫洛娜>卡洛琳，GIT 不是 CMS。它是一个版本控制软件

【22:12】<帕金特>啊，杰瑞，我这我就不清楚了。我明白你的困境。

【22:12】<霍克>我在上一个工作地点用过 SourceSafe，所以我听到了你的声音！

【22:12】<帕金特>我一直在向“各行各业”宣传 Git 的使用。

霍克，你现在用 GIT 吗？

【22:13】<卡洛琳>我意识到 GIT 不是 CMS。但是当我开始看 EE 或者 Chart 等的时候。等等。他们立即提到 Git。所以，我需要了解一下。

【22:13】<帕金特>我“日常工作”的一部分是技术培训，我需要在这方面开发课程。这些大多是 MSWord 文档。

【22:13】<帕金>我用 Git 来帮助我追踪(和追溯)我的工作。

【22:13】<莫洛娜>卡罗琳，好吧，道歉。只是误解了你所说的:D

【22:13】<霍克>这些天我没怎么做编码莫洛娜！我使用 GitHub，但只是为了 Gist 功能

卡罗琳提出了一个有趣的观点。

【22:14】

【22:14】<帕金特> Git(可能)是开源世界最受欢迎的 VCS

【22:14】

【22:14】

【22:14】<霍克>稍后我会在上发布的时候把它们添加到文字记录中

【22:15】<帕金>感谢 Triopticon。巨大的资源。

【22:15】<帕金特>代码学校也有“Try Git”课程(我想它有自己的域名‘Try Git . com’)。

【22:15】如果我想使用其他存储库呢？

【22:15】<帕金>但是它的目标受众是软件开发者

【22:16】<詹姆士>现在是[http://try.github.io/](http://try.github.io/)

【22:16】<帕金特>有很多地方可以托管一个 Git 存储库。

【22:16】<park int>这是我多年前创作的一个视频[http://www.youtube.com/watch?v=2bxbzFQEMYM](http://www.youtube.com/watch?v=2bxbzFQEMYM)演示了使用 Dropbox 作为你的 Git 库

【22:17】<Triopticon>好看！

【22:17】<帕金> Github 简直是最受欢迎的。

【22:17】<莫洛娜>酷，你现在是我的英雄帕金

【22:17】<ParkinT>bit bucket 是我知道的另一个支持 Git 的。

【22:17】*哈迪用一条大鳟鱼打了哈迪一下

【22:17】<Velochicdunord>谢谢！

【22:18】<霍克>啊，老鳟鱼，是不是很顽强？

【22:18】

【22:18】<哈代>呵呵——只是想看看我会造成多大的伤害；)

【22:18】<ParkinT>是它处理补丁的方式。通过这种方式，您可以以 Git 能够理解的打包形式通过电子邮件发送存储库的增量

【22:19】<帕金>哈代，你明天会有瘀伤！

【22:19】<Triopticon>我会说 Github 更适合社交方面和开源环境，即使你也可以为私人回购付费。

【22:19】<Scruggs>“一个仓库的三角洲”，什么意思？

【22:19】<莫洛娜>我还不知道 dropbox 可以和 GIT 一起工作……真好！

【22:19】<帕金>真实。Git 在“脸书开发者大会”上为自己买单

【22:19】<詹姆斯>有没有 noob 友好的推荐教程？

【22:19】<杰拉克>[http://gitlab.org/是](http://gitlab.org/ is)不错如果你有资源自己举办的话。

【22:20】<帕金特>我所说的‘德尔塔’就是一个和另一个之间的区别。

【22:20】<Scruggs>好了，谢谢澄清。

【22:20】<ParkinT>Github，对了，是开源的。有了网络主机，你可以创建自己的 Github.com 版本

【22:21】

【22:21】<jerrac>Github 是开源的？我从来没有看到任何地方可以下载他们的源代码。

我可能正在做一个项目；我们假设它是一个网站。

【22:22】但是我害怕弄糟已经“奏效”的东西。

我简单地在 git 中创建了一个分支。这给了我一个新的地方去从我目前的状态

【22:22】

【22:23】<帕金>如果我取得了惊人的进步，我可以把我的分支和主分支合并。

现在，最精彩的部分来了。

【22:23】<莫洛娜>合并是否意味着你将永远删除那个分支？

【22:23】<帕金特>我有一套新的密码。但是，在任何时候，我都可以通过结帐返回到之前的状态。

【22:24】<停车>否，分支未被删除。您可以显式删除分支。

【22:24】<帕金特>但是 Git 真的很难做到不让你搬起石头砸自己的脚。

【22:24】<LJ>如何从 ghithub 页面分支下载？可能吗？我只拿到主文件。

【22:24】<公园>如果一个分支背后没有任何历史，则不能删除该分支。

【22:24】<莫洛娜>别担心。我还是会设法搬起石头砸自己的脚:p

【22:24】<park int>在 Github 上，有一个下拉菜单显示所有的分支。

【22:25】<park int>许多存储库只有一个(主)分支。

【22:25】<LJ>我知道，但当我尝试从 githhub pages 分支下载所有文件时，我无法获得该分支上的所有文件。

【22:25】<帕金特>就像我之前说的，Git 的一个独特之处在于，知识库包含了整个历史。因此，如果有分支，当你获取回购时，它们将被包括在内。

【22:26】为了获得整个 Git 存储库，您需要使用 Git(传统上是在命令行上)。

【22:26】<LJ>我可以克隆到我自己的 github 上，但是我不能将分支“github pages”文件下载到我的机器上使用。

我怀疑你只是从网站上复制文件。

【22:26】<ParkinT>啊 Github pages 不是 Git 的一部分。

【22:27】<帕金>它是 Github 的一个‘特色’。像叉子和星星

是的，我试着压缩这些文件..

【22:27】<帕金特>Github 的团队是了不起的创新者。

我应该先编辑代码，然后再创建一个分支吗？还是创建分支/结帐并添加新代码？以便更改不会出现在主分支中？

【22:27】* Codebold 用一条大鳟鱼拍打 code bold

【22:27】<帕金>好问题，詹姆士。

【22:28】

【22:28】<帕克特>捕获所有这些文件的当前状态，并为提交(作为一个整体)分配一个全局唯一标识符。

【22:28】<帕金>如果您随后创建一个分支，它将包含所有相同的文件。

【22:29】

【22:29】<帕金>你现在基本上有两个不同版本的文件可以参考。

【22:29】<帕金特>把一棵大橡树的树干想象成主 Git 库。

你每做一个分支，它都是在某个特定的时间点从生长的树上长出的一根树枝。

【22:30】<帕金特>树的主体部分继续独立地生长。

【22:30】<帕金>主分支可以继续增长(变化)。如果你在做一个合作项目，它会的。

【22:30】<帕金特>因为别人正在对他们的副本库进行黑客攻击。

【22:30】

【22:31】<帕金特>与其他系统不同，同步过程(合并)是在每台单独的计算机(回购的副本)上执行的。

【22:31】

我认为最好的方法是总是签出你需要的文件，进行修改，然后提交这些修改。提交/更改历史信息在试图找出主存储库中某处发生更改时很有价值

【22:32】<霍克>仅供参考各位，由于 GitHub 停机，如果您离开本聊天室(通过刷新页面)，您将无法重新进入。

【22:32】<帕金>完全正确，斯克鲁格。

【22:32】<霍克>我希望他们能尽快解决这件事！我依靠 Gists 来控制访问。

【22:32】<park int>Git 中还有另一个我“最喜欢”的命令。【怪】

【22:33】<主战派>哦讽刺派

【22:33】<莫洛娜> lol 霍克

【22:33】

【22:33】<ParkinT>Gist 是 Github 聪明人的又一创新！

【22:33】<公园>和爱情之旅

【22:33】<LJ>可以用终端命令将一个回购目录拉入本地吗？就像你说的，“签出你需要的文件”

【22:34】<帕金>*即使我不能正确拼写！

【22:34】<帕金>是的，lj

【22:34】<杰瑞><——屏息等待命令的名字……:)

【22:34】

【22:34】<LJ>是的，气喘吁吁

【22:35】<帕金>对不起杰瑞。用-b 签出一个新的分支

【22:35】<Scruggs>git 管理员能控制住吗？

【22:35】<停车>然后根据需要添加并提交

【22:35】<停车>[http://stack overflow . com/questions/14655816/how-to-commit-changes-to-new-branch](http://stackoverflow.com/questions/14655816/how-to-commit-changes-to-new-branch)

【22:35】<帕金>那个饭桶管理员就是你。

【22:35】<帕金特>存储库在你的机器上，你控制水平，你控制垂直……

【22:35】<杰瑞>如果有未提交的修改，checkout -b 不会抱怨吗？

【22:36】<帕金特>就像我之前说的，git 是以“人类”为出发点设计的。

【22:36】<帕金>它帮助你避免犯愚蠢的错误和憎恨自己。

【22:36】<三光镜> :p

【22:36】<帕金>但是做任何事情都是有可能的

【22:36】<Scruggs>我想我的意思是…假设你有多个回购…比如开发、测试和生产。git 管理员可以设置安全规则来防止低级开发人员将事情提交给生产回购吗？

【22:36】<park int>很多命令都包含 a -f(意为‘力’)开关。

【22:37】<LJ>好的，那么$git checkout sass 这会把 sass 文件夹下载到我的本地机器上吗？抱歉，我对这个不太有经验。

为同一个项目设置不同的存储库是没有意义的。

【22:37】<帕金>你要鼓励协作。有了 Git，没有人会因为一段糟糕的代码而导致系统崩溃。

【22:37】<霍克>耶——我们又开张了！

【22:37】<帕金特>因为 a)你可以回到历史，b)每个合作者的办公桌上都有一份完整回购协议的副本。

【22:38】<Triopticon>我喜欢这种分支模式:[http://nvie.com/posts/a-successful-git-branching-model/](http://nvie.com/posts/a-successful-git-branching-model/)

【22:38】

【22:38】

【22:38】<LJ>“desk”你是指本地还是指我自己的 git 回购？因为拿不到整份回购

【22:39】<帕金特>不要把它们当成目录提交是一个包含一整套文件和文件夹的对象

【22:39】<LJ>萨斯不是一个分支而是一个目录

我喜欢你关于合作的评论。我看到一些网络商店建立了多个回购，以展示正在开发的东西，即将在下一个版本中发布的东西，以及当前“活跃”的东西。只是好奇你对此的看法。

【22:40】<帕金特> Scruggs，这正是树枝的用途。

【22:40】<莫洛娜>但我担心，在某个时候，我希望有人查看某个特定分支的一些文件，但我不想让他们做任何事情

例如，我想尝试一项新功能。我创建一个特性分支，进行我的更改，测试它们，然后将更改推送到测试服务器。

【22:40】<莫洛娜>虽然如果他们确实用他们的评论修改了文件，这可能是一个有用的修订过程，我想

【22:41】<帕金>如果它们看起来有效，我们可以“决定”将该分支合并到主服务器中并进行部署。如果有问题，我们可以再研究一下。

【22:41】<帕金特>说得好，莫洛娜。

【22:41】

【22:42】<海基会>嘿，伙计们。只有我还是你们所有人都断线了？抱歉打断

【22:42】<帕金特>然后，他们将提交内容作为整个历史的一部分，与他们在此期间所做的任何更改融合在一起。

【22:42】我正在和 WordPress 网站合作

【22:42】<鹰派>海基会你能看出这一点吗？

【22:42】<Scruggs>blog junkie:永远计划和测试！

【22:43】

【22:43】<海基会>霍克是的，我现在连接上了，只是半个小时连接不上

【22:43】

【22:43】<鹰派>是啊，不好意思。GitHub 出现了故障(讽刺的是),我使用 Gists 来控制访问。

【22:43】<主战派>所有的会议都为这件事发生在！

【22:44】<帕克特> LOL

【22:44】<帕金>讽刺的定义，嗯？

【22:44】<霍克>今天晚些时候我会在 SitePoint 上发布整个会议的文字记录，这样你就可以看到你错过的内容。同时，如果你有重要的问题，请随时提问

【22:44】<blog junkie>ok 酷。有我可以参考的需求列表吗？否则我想我可以编辑我自己的博客，把它变成一篇有用的博文……

根据我的经验，需求集是在任何工作完成之前(与客户合作)产生的。

【22:46】<帕金>虽然最终，要求应该是一个“当它这样做，我会认为它完成了，我会付给你”从客户端列表

【22:46】<Scruggs>还有，基本都是源于解题的基础。问题是什么，解决问题需要什么步骤

【22:46】<海基会>好的，我明白了:)其实我想问几个问题。首先，我想谈谈通过 git 的自动网站部署。我是否可以提交到生产分支，以便它将自动启动一些构建脚本/测试/无论什么，如果成功，把它都带到我的网站上？

【22:46】<莫洛娜>好问题 sef！

【22:46】<帕金>简答:绝对

【22:46】<博客瘾君子>感谢帕金特和斯克鲁格斯

【22:46】<帕金特>Git 的另一个很酷的特性是一组“钩子”

【22:46】<莫洛娜>好答案帕金特但现在展开，请

【22:47】<Sef>我知道有很多关于这方面和持续集成的教程，但也许你知道一些最好的方法和材料

【22:47】<帕金> Github 非常巧妙地利用了这些

使用钩子你可以触发任意数量的自动化操作。

我经常在铁轨上工作。我最喜欢的工具之一是 Capistrano

【22:48】

【22:49】<莫洛娜>你能做的自动化工作取决于你的库在哪里吗？我的意思是，如果我使用 GitHub，我可以做很多事情，但如果我使用其他库，我就不能做这些事情了？或者我能自己编码吗？

【22:49】<sewmeheadon>我们使用 Beanstalk 进行客户端工作，因为它内置了易于所有开发人员设置的部署。

【22:49】

【22:50】不管主持人。

【22:50】<鹰派>这一环节还剩 10 分钟。如果你正坐在一个问题上，确保你很快就能理解它。

如果需要的话，你能编写新的钩子吗？

【22:50】<sewmeyheadon>我也看到了 ftploy.com 礼部的部署

使用钩子做的另一件好事是在提交之前运行一些东西来根据编码标准检查你的源代码，比如 PHP_CodeSniffer。

【22:51】<帕金>说得好，克里斯

【22:52】<帕金特>据我所知，钩子是存在的，你需要对 Git 源代码(在公共库中)进行修改。

【22:52】

我想它们已经包含了你所需要的一切。

【22:52】<帕金特>认识到钩子是 Git 中事件的触发器。

【22:52】<莫洛娜>以我的情况来说，当然！我只是好奇

发生的事件数量是有限的。你可以灵活地以任何方式使用它们。

【22:53】<莫洛娜>是的，也许我应该说“行动”而不是钩子…我很好奇 Git 是否可以是自定义的，不知何故

【22:53】<帕金>感谢大家的积极参与。我希望没有人觉得他们的问题没有得到应有的关注。

【22:54】<帕金特>有很多工具可以通过多种方式使用 Git。

【22:54】<帕金>大部分都是“壳”脚本。

【22:54】<鹰派>是的，这是一场精彩的会议。更像是一场小型讲座。你做了一件了不起的工作，谢谢汤姆。

【22:54】<海基会>谢谢各位，这很有趣，期待完整的成绩单

【22:54】<克里斯>感谢主持！

我很喜欢这个游戏！:D

【22:54】<鹰派>无忧。:)

【22:54】<霍克>我每周跑一次。时间稍有变化，所以请务必查看时间表

【22:55】<blog junkie>感谢大家

【22:55】<帕金>谢谢霍克。:脸红:

那么，我有一个愚蠢的问题:如果我有一个 repo，并且创建了一个特性分支，其中包含了与原来相同的文件，只是状态不同，包含了不同的代码，那么当我切换到这个分支时，repo 中的所有文件都会有效地“改变”以模仿我正在使用的分支中文件的状态？

【22:55】<莫洛纳>当然还有霍克

【22:55】<莫洛娜>其余

【22:55】<帕金特>sewmeheadon，你说的完全正确！

【22:56】<【sewmyheadon】>谢谢！公园〔T3〕

【22:56】<帕金特>最好把每次提交看作是那些文件状态的快照

【22:56】<帕金>一瞬间的时间，像一张照片。

<停车场>再见/夜/日都

## 分享这篇文章
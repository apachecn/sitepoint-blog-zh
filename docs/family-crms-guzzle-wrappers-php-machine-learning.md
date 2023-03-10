# 家庭 CRM，Guzzle 包装器和 PHP 机器学习？Sourcehunt！

> 原文：<https://www.sitepoint.com/family-crms-guzzle-wrappers-php-machine-learning/>

是时候让我们的[每月寻找新的开源库来使用并贡献给](https://www.sitepoint.com/sourcehunt-open-source-week-edition/)了！

如果您是 Sourcehunt 的新手，这是我们的月度帖子，旨在推广那些有趣或有前途的开源项目，并在 Github stars 或 pull 请求方面提供帮助。

这是我们回馈社会的方式——推广我们使用(或可能使用)的项目，以便它们获得足够的曝光率来吸引更广泛的受众、强大的社区以及可能的新贡献者或赞助商。

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

* * *

### 莫尼卡/莫尼卡[2067★]

Monica 前几天在 Hacker News 和 ProductHunt 上大发雷霆，尽管她已经获得了 2000 多颗星星，但我们在这里提到它还是很合适的。

Monica 是一个基于 Laravel 的个人关系单用户 CRM。不是为了生意，而是为了你生活中的人——追踪你还没有买礼物、贷款、生日、会议等等的人。

![Monica promo screenshot](img/0c95d97117a546317b0651501b26ac5a.png)

我们认为这是令人耳目一新(因为这是你不常看到的)和令人沮丧(因为我们现在真的需要应用程序来跟踪我们生活中的人吗？)，但仍然很有趣。

由于来自各地的大量兴趣，Monica 现在有几十个问题和几个准备检查的 pr，更不用说需要修改的文档、计划的路线图等等。如果你想为一个受欢迎的项目做贡献，这里就是！

* * *

### [genkgo/mail](https://github.com/genkgo/mail) [3 ★]

Gengko/mail 声称是 PHP 邮件库的现代改造。用他们自己的话说:

> 在分析重构代码库时使用什么邮件库时，我们发现可用的邮件库大多是遗留库。有些不使用名称空间，我们遇到的每一个库都只是一个标量属性包的集合，而不是使用封装的对象。这不是对这些库的批评。我们都用过，而且用的很开心。然而，我们认为需要使用现代原则的新图书馆。

如果你想使用不可变的消息和流通过不同的传输和协议发送电子邮件，那么应该使用 Gengko 的邮件，如果在同一个句子中 streams 和 Gengko 听起来很熟悉，那是因为我们以前搜索过它们——只是那时它们是流压缩文件。用法非常简单直观:

```
$message = (new FormattedMessageFactory())
    ->withHtml('<html><body><p>Hello World</p></body></html>')
    ->withAttachment(new FileAttachment('/order1.pdf', new ContentType('application/pdf')))
    ->createMessage()
    ->withHeader(new From(new Address(new EmailAddress('from@example.com'), 'name')))
    ->withHeader(new Subject('Hello World'))
    ->withHeader(new To(new AddressList([new Address(new EmailAddress('to@example.com'), 'name')])))
    ->withHeader(new Cc(new AddressList([new Address(new EmailAddress('cc@example.com'), 'name')])));

$transport = new SmtpTransport(
    ClientFactory::fromString('smtp://user:pass@host/')->newClient(),
    EnvelopeFactory::useExtractedHeader()
);

$transport->send($message); 
```

这个回购迫切需要爱:它需要明星、贡献者，主要是测试人员，所以请投入帮助团队为 PHP 社区提供一个新的、现代的、命名空间和封装的库，为 PHP 7+时代做好准备。

* * *

### [kitdetail/zttp](https://github.com/kitetail/zttp)【526】

如果你不熟悉 Guzzle(我们在 [premium Exploring PHP 课程中探索的 PHP HTTP 客户端，现在可以购买](https://www.sitepoint.com/premium/courses/exploring-php-2951?aref=bskvorc))的发展，这个库基本上变得更加专业，每个新版本的可用性更低。新的一层又一层关于规范的抽象和规则使得 Guzzle 难以开始。再加上进一步的抽象，如[http plug](https://www.sitepoint.com/breaking-free-from-guzzle5-with-php-http-and-httplug/)旨在防止老油老虎和新油老虎的冲突，这种情况总的来说并不是一个令人愉快的发展。

进入 ZTTP——一个用一些易于使用和接近的助手方法包装 Guzzle 的包，以便它再次变得像以前一样可用。它是由 Laravel 的测试之神 Adam Wathan 创建的，并且知道他的代码没有太多贡献，但是 docs 可以使用帮助，并且总是需要使用示例和 PRs 以及更方便的帮助方法。

* * *

### [viacreative/sudo-su](https://github.com/viacreative/sudo-su)【324】

在许多应用程序中，能够以不同用户的身份登录是业务需求的一部分——这在支持、稿件跟踪、CRM 以及存在用户层次的类似应用程序中尤其流行。然而，有时该功能并没有在最终的计划中结束，但在开发过程中仍然很方便，只是为了测试各种权限、UI 选项等。

viacreative/sudo-su 软件包为任何 Laravel 应用程序添加了一个可点击的浮动小部件，让当前用户可以像系统中的任何其他用户一样登录。

![Package in use](img/6f99557f1719ea8436009e88cd055255.png)

默认情况下，该小工具在所有不以`dev`或`.local`结尾的域上都是禁用的——这两个标志表明一个网站没有投入使用，但这可以随意修改(例如，我们自己的[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)默认使用`.app`)。

有很多问题需要解决，所以如果您有兴趣了解更多关于 Laravel 的用户认证系统的信息，请继续前进！

* * *

### [icyapril/whatchenged](https://github.com/IcyApril/WhatsChanged)【22】

WhatsChanged 工具旨在通过许多测试来帮助测试大型代码库。由于测试可能需要一段时间来执行，特别是在处理数据库或者有数百个数据库的情况下，所以只需重新运行引用被更改文件的测试就很方便了。

这个工具使用 Git 来跟踪文件的变更，然后只重新运行相关的文件——这包括变更的测试。它有一些严格的假设:它假设您的类在`src`中，测试被映射到`tests`文件夹中的精确文件结构中。这听起来像是用 PR 升级的一件好事，不是吗？

贡献像 Windows 支持或支持非。php 文件，当然还有文档和例子！

* * *

### [PHP-ai/PHP-ml](https://github.com/php-ai/php-ml)[3497★]

最后但同样重要的是，流行的 PHP 机器学习库。不知道我们之前怎么没发现！这个移植到 PHP 的 ML 算法集合附带了一套全面的示例和解释，以一种中级到高级 PHP 开发人员可以理解的方式编写。

![PHP-ML logo](img/0eb78dca6cfeff427da7856b3b5b3b4b.png)

这个库需要现代的 PHP (7+)，并且有很好的文档记录。

有几个问题需要帮助，还需要添加更多的算法，所以如果机器学习是你觉得可以进入的东西，为什么不贡献出来，同时学习更多呢？

* * *

六月到此为止。找到你能咬进去的东西了吗？

和往常一样，请把你的链接和 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 标签一起扔给我们！如果您用我们提到的项目构建了一些东西，或者如果您提交了一个您想要谈论的详细的拉请求，请向我们大喊一声，我们将确保全世界都知道它！

就像上次的，(顺便说一句，这个挑战仍然无人问津——如果你完成了，就有 500 美元奖励给你！)，我们正在使用上面的包来获得灵感，以创建一两个潜在有用的应用程序:

**应用+每月指导创意:**

本月的挑战是升级 Monica，以支持带有 Genkgo/mail 的电子邮件提醒，以及多个用户(在开发期间使用 viacreative/sudo-su 包)，但谁都可以看到相同的联系人。但是，每个人都有自己的带有私人信息的仪表板。想象一下你的近亲家庭，以及你母亲的姐姐(也就是你的堂兄妹和姑姑)的家庭。你的母亲可以在她的仪表板上看到(除了她自己的笔记之外)这个家庭的生日和她与她姐姐的关系，而你可以看到你与你的堂兄妹的关系，并记下上次见面时你借给他们多少钱。这种以教程形式提供的升级价值 500 美元，所以赶快加入吧——先到先得！[保持联系！](mailto:bruno.skvorc@sitepoint.com)

编码快乐！

## 分享这篇文章
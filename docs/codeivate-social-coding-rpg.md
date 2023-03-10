# code ivate–社交编码角色扮演

> 原文：<https://www.sitepoint.com/codeivate-social-coding-rpg/>

构建 web 应用程序时，您可能会使用多种语言来完成工作。也许你想知道你实际上给了不同的语言多少关注？如今，你可以通过[协同衍生](http://www.codeivate.com/)来追踪。

Codeivate 是一个工具，你可以集成几个编辑器，如 PhpStorm 和 Sublime Text，它会跟踪你正在处理的文件类型，并为此给你加分。最后，您可以看到一个完整的概要文件，说明您一直在用什么语言编程，以及哪些语言是您使用得最多的。请看我在 Codeivate 上的个人资料。

![](img/0190ce23fcf5e1fc4d30680dd0581c3c.png)

### 设置

与几个编辑器的集成很容易。在本文中，我们将看看与 PhpStorm 和 Sublime Text 的集成。但是，请注意，更多的编辑器目前得到了[的支持，并且很快就会得到](http://codeivate.userecho.com/forum/17921-general/)的支持。

在我们开始之前，我们首先必须在 Codeivate 网站上注册。注册后，请确保登录并转到包含您的令牌的页面。这个网站很难理解，所以这里有一个直接链接。

#### 崇高的文本

在你可以配置 Sublime Text 之前，确保你已经安装了[包控制](https://sublime.wbond.net/)。打开包控制并选择安装一个包。搜索`Codeivate`并安装合适的软件包。

打开`preferences`->-`package settings`->-`codeivate`->-`settings - user`。在这个文件中，将`user_id`和`token_id`更改为您在 Codeivate 网站的令牌页面上找到的值。将`machine_name`字段更改为您喜欢的任何名称。如果您使用多台机器，这将是查看这些机器之间差异的标识符。

重启 Sublime Text，你就可以开始了。在状态栏中，你会不时看到来自 Codeivate 的反馈，表明你当前的 streak 是什么，以及你当前用哪种语言编程。请注意，Codeivate 会检查您正在处理的文件的语法，因此使用 Sublime Text 中的`set syntax`命令来确保文件定义正确。

#### PhpStorm

打开 PhpStorm，点击`file`->-`preferences`。在首选项中，转到插件部分并点击`browse repositories`。搜索`codeivate`并安装合适的插件。安装后，会出现一个名为`codeivate settings`的新设置页面。打开该设置页面，填写`user ID`和`user token`。如果愿意，您也可以定义一个机器名。通过定义一个机器名，您可以很容易地区分多台机器。如果您愿意，也可以在每个编辑器中使用不同的机器名。

在状态栏中，您将看到来自 Codeivate 的欢迎消息。每当你开始输入，它会显示你目前使用的语言和你目前的连胜纪录。

### 网站(全球资讯网的主机站)

如果您设置了编辑器并完成了第一次代码更改，您将能够在 Codeivate 网站上查看您的进度。例如，在摘要页面上，您可以看到您当前的排名。你可以看到你最活跃的语言，你在每台机器上写了多少程序，以及你最好的记录是什么。

在排行榜页面上，您可以看到顶级程序员的列表。如果你搜索你的名字，你可以找到你现在的位置。

Codeivate 网站有一些更好的功能。不幸的是，该网站是相当不清楚，你真的要通过挖掘一切来找到他们。例如，在这个页面上，您可以查看我过去 7 天的统计数据。你想看看你的吗？只需在 URL 中更改您的用户名。

还有一个[战斗页面](http://battle.codeivate.com/)，在那里你可以选择 2 个用户，然后在不同的方面进行战斗。

## 结论

Codeivate 是一个非常好的跟踪工具，尤其是当你喜欢成就的时候。我已经用了几个月了，很高兴看到我到目前为止都做了些什么。然而，目前大多数插件和页面已经测试了几个月。除此之外，该网站是非常不清楚的，因为有各种各样的新页面链接散布在各处。我认为这个工具真的有一些潜力，但是对于开发者来说，显然还有很多工作要做。你在用吗？你觉得这个工具怎么样？我很乐意在下面的评论中听到你的意见。

## 分享这篇文章